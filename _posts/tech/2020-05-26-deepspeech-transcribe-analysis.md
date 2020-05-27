---
layout: post
title: DeepSpeech中transcribe文件分析
category: 技术
tags: React
keywords: React
---

## 直觉上DeepSpeech中的transcribe功能中，有关于logits的定义，于是主要分析transcribe.py文件
#!/usr/bin/env python

```
# -*- coding: utf-8 -*-
from __future__ import absolute_import, division, print_function

import os
import sys
import json
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '3'
import tensorflow as tf
import tensorflow.compat.v1.logging as tflogging
import tensorflow.compat.v1 as tfv1
tflogging.set_verbosity(tflogging.ERROR)
import logging
logging.getLogger('sox').setLevel(logging.ERROR)

from multiprocessing import Process, cpu_count
from ds_ctcdecoder import ctc_beam_search_decoder_batch, Scorer
from util.config import Config, initialize_globals
from util.audio import AudioFile
from util.feeding import split_audio_file
from util.flags import create_flags, FLAGS
from util.logging import log_error, log_info, log_progress, create_progressbar


def fail(message, code=1):
    log_error(message)
    sys.exit(code)


# 除了要输入音频路径外，还需要输入 目标文本信息
# 可以使用如下的.csv文件进行训练

def transcribe_file(audio_path, tlog_path):
    # 导入DeepSpeech 模型
	from DeepSpeech import create_model, try_loading  # pylint: disable=cyclic-import,import-outside-toplevel  
    # 初始化参数
	initialize_globals()
	# 用于CTC decoder的参数信息
    scorer = Scorer(FLAGS.lm_alpha, FLAGS.lm_beta, FLAGS.lm_binary_path, FLAGS.lm_trie_path, Config.alphabet)
    # 获得CPU的个数
	try:
        num_processes = cpu_count()
    except NotImplementedError:
        num_processes = 1
	# 使用AudioFile读取音频数据，输入音频文件的路径等
    with AudioFile(audio_path, as_path=True) as wav_path:
		# 将采样值数据转化成MFCC特征的直接可输入模型的信息
		# data_set = split_audio_file(wav_path,
        #                            batch_size=FLAGS.batch_size,  
        #                            aggressiveness=FLAGS.vad_aggressiveness,    # FLAGE中似乎没有该参数， 若报错可删除。因为在该函数中已经有默认值为3
        #                            outlier_duration_ms=FLAGS.outlier_duration_ms,   # FLAGE中没有该参数，默认值为10000
        #                            outlier_batch_size=FLAGS.outlier_batch_size)     # FLGAE中没有该参数，默认值为1
        # 构造该dataset的迭代器
		iterator = tf.data.Iterator.from_structure(data_set.output_types, data_set.output_shapes,
                                                   output_classes=data_set.output_classes)
		# 使用get_next()获取batch_size大小的数据
        batch_time_start, batch_time_end, batch_x, batch_x_len = iterator.get_next()
        no_dropout = [None] * 6
		# ----------------------TODO--------------------
		# 得到模型输出的logits信息, 在tf_logit.py中，通过输入audio,以及batch_size，输出该logits 最后一层由（[n_steps*batch_size, n_hidden_6]）->（[n_steps, batch_size, n_hidden_6]）.
		# 若要使用集成模型，则需要对该部分进行改造
        logits, _ = create_model(batch_x=batch_x, seq_length=batch_x_len, dropout=no_dropout)  （[n_steps*batch_size, n_hidden_6]）->（[n_steps, batch_size, n_hidden_6]）.
        # ----------------------------------------------
		# 进行转置操作 Transpose to batch major for decoder。 其实在deepSpeech v0.4.1的版本中也是用了该操作（注：在使用logit进行ctcloss时，并没有使用转置操作，而是直接使用model输出的logits）
		transposed = tf.nn.softmax(tf.transpose(logits, [1, 0, 2]))   # 该操作仅用于Decode
        # 若是restore模型，则使用该操作获取global_step； 若不是，则创建一个global_step
		tf.train.get_or_create_global_step()
        saver = tf.train.Saver()
        with tf.Session(config=Config.session_config) as session:
            loaded = try_loading(session, saver, 'best_dev_checkpoint', 'best validation', log_success=False)
            if not loaded:
                loaded = try_loading(session, saver, 'checkpoint', 'most recent', log_success=False)
            if not loaded:
                fail('Checkpoint directory ({}) does not contain a valid checkpoint state.'
                     .format(FLAGS.checkpoint_dir))
			# 数据迭代器初始化
            session.run(iterator.make_initializer(data_set))
            transcripts = []
            while True:
                try:
				# 得到解码文字的一些参数
                    starts, ends, batch_logits, batch_lengths = \
                        session.run([batch_time_start, batch_time_end, transposed, batch_x_len])
                except tf.errors.OutOfRangeError:
                    break
				# 解码
                decoded = ctc_beam_search_decoder_batch(batch_logits, batch_lengths, Config.alphabet, FLAGS.beam_width,
                                                        num_processes=num_processes,
                                                        scorer=scorer)
                decoded = list(d[0][1] for d in decoded)
                transcripts.extend(zip(starts, ends, decoded))
            transcripts.sort(key=lambda t: t[0])
            transcripts = [{'start': int(start),
                            'end': int(end),
                            'transcript': transcript} for start, end, transcript in transcripts]
            with open(tlog_path, 'w') as tlog_file:
                json.dump(transcripts, tlog_file, default=float)


def transcribe_many(path_pairs):
    pbar = create_progressbar(prefix='Transcribing files | ', max_value=len(path_pairs)).start()
    for i, (src_path, dst_path) in enumerate(path_pairs):
        p = Process(target=transcribe_file, args=(src_path, dst_path))
        p.start()
        p.join()
        log_progress('Transcribed file {} of {} from "{}" to "{}"'.format(i + 1, len(path_pairs), src_path, dst_path))
        pbar.update(i)
    pbar.finish()


def transcribe_one(src_path, dst_path):
    transcribe_file(src_path, dst_path)
    log_info('Transcribed file "{}" to "{}"'.format(src_path, dst_path))


def resolve(base_path, spec_path):
    if spec_path is None:
        return None
    if not os.path.isabs(spec_path):
        spec_path = os.path.join(base_path, spec_path)
    return spec_path


def main(_):
    if not FLAGS.src:
        fail('You have to specify which file or catalog to transcribe via the --src flag.')
    src_path = os.path.abspath(FLAGS.src)
    if not os.path.isfile(src_path):
        fail('Path in --src not existing')
    if src_path.endswith('.catalog'):
        if FLAGS.dst:
            fail('Parameter --dst not supported if --src points to a catalog')
        catalog_dir = os.path.dirname(src_path)
        with open(src_path, 'r') as catalog_file:
            catalog_entries = json.load(catalog_file)
        catalog_entries = [(resolve(catalog_dir, e['audio']), resolve(catalog_dir, e['tlog'])) for e in catalog_entries]
        if any(map(lambda e: not os.path.isfile(e[0]), catalog_entries)):
            fail('Missing source file(s) in catalog')
        if not FLAGS.force and any(map(lambda e: os.path.isfile(e[1]), catalog_entries)):
            fail('Destination file(s) from catalog already existing, use --force for overwriting')
        if any(map(lambda e: not os.path.isdir(os.path.dirname(e[1])), catalog_entries)):
            fail('Missing destination directory for at least one catalog entry')
        transcribe_many(catalog_entries)
    else:
        dst_path = os.path.abspath(FLAGS.dst) if FLAGS.dst else os.path.splitext(src_path)[0] + '.tlog'
        if os.path.isfile(dst_path):
            if FLAGS.force:
                transcribe_one(src_path, dst_path)
            else:
                fail('Destination file "{}" already existing - use --force for overwriting'.format(dst_path), code=0)
        elif os.path.isdir(os.path.dirname(dst_path)):
            transcribe_one(src_path, dst_path)
        else:
            fail('Missing destination directory')


if __name__ == '__main__':
    create_flags()
    tf.app.flags.DEFINE_string('src', '', 'source path to an audio file or directory to recursively scan '
                                          'for audio files. If --dst not set, transcription logs (.tlog) will be '
                                          'written in-place using the source filenames with '
                                          'suffix ".tlog" instead of ".wav".')
    tf.app.flags.DEFINE_string('dst', '', 'path for writing the transcription log or logs (.tlog). '
                                          'If --src is a directory, this one also has to be a directory '
                                          'and the required sub-dir tree of --src will get replicated.')
    tf.app.flags.DEFINE_boolean('force', False, 'Forces re-transcribing and overwriting of already existing '
                                                'transcription logs (.tlog)')
    tf.app.flags.DEFINE_integer('vad_aggressiveness', 3, 'How aggressive (0=lowest, 3=highest) the VAD should '
                                                         'split audio')
    tf.app.flags.DEFINE_integer('batch_size', 40, 'Default batch size')
    tf.app.flags.DEFINE_float('outlier_duration_ms', 10000, 'Duration in ms after which samples are considered outliers')
    tf.app.flags.DEFINE_integer('outlier_batch_size', 1, 'Batch size for duration outliers (defaults to 1)')
    tf.app.run(main)
```


### 关于global_step的坑
global_step 往往会影响learning rate的计算，所以要正确确定该参数。
fine-tuning时，global_step肯定要从0开始记；继续训练时要从上次的断点开始计。
tf中restore model的方法：

```
作者：leaf
链接：https://www.zhihu.com/question/269968195/answer/351000240
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

# method 1
var_list=variables_to_restore = []
saver = tf_saver.Saver(var_list）
# 可指定var_list, 适用于fine-tuning
# 若不指定，则回复所有参数，适用于继续训练
saver.restore(session, model_path)
# 该方法不会对global_step 产生作用，即使是在继续训练时，也要额外操作
# 使用正则获得step，assign给gloal_step
global_step = tf.Variable(0, name="global_step", trainable=False,dtype=tf.int32)
# ckpt.model_checkpoint_path like 'model.ckpt-175409'
step = int(os.path.basename(ckpt.model_checkpoint_path).split('-')[1])
tf.assign(global_step, step+1)
# method 2, 使用slim
global_step = tf.train.get_or_create_global_step()
# or global_step = slim.create_global_step()
# 这两种定义方法，都会吧global_step 加入到GraphKeys.GLOBAL_STEP中，方便取出
slim.learning.train(
            train_tensor,
            logdir=FLAGS.train_dir,
            master='',
            is_chief=True,
            init_fn=tf_utils.get_init_fn()
            )
# 从logdir中存在ckpt，默认恢复global_step
# 若指定init_fn=tf_utils.get_init_fn，可自定义需要恢复的参数，用来fine-tuning
```