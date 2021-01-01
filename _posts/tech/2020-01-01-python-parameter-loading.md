---
layout: post
title: Python中加载参数的一些方法
category: 技术
tag: React
keywords: argparse
---

## python中使用argparse加载参数后，再往里面添加参数

```
import argparse
class Args:
	@staticmethod
	def parse():
		parser = argparse.ArgumentParser()
		return parser
	
	@staticmethod
	def initialize(parser: argparse.ArgumentParser):
        parser.add_argument("--train_dir", type=str, default='./data', help="train data path")
        parser.add_argument("--batch_size", type=int, default=64, help="train data batch size")
        return parser
	
	def get_parser(self):
		parser = self.parses()
		parser = self.initialize(parser)
		return parser.parse_args()

if __name__ == "__main__":
	args = Arg().get_parser()
	args.batch_size = 128
	# 后续就可以使用了
```

未完待续，还有个yaml的方法。具体是yaml文件中指定参数，然后加载到argparse中，再解析成argparser格式的文件。

假设在python文件中定义了部分parser参数，有一部分参数需要从`yaml`参数文件中获得，然后统一构建一个args文件作为后续使用，可以参考以下代码

`config.ymal`文件内容如下

```
batch_size: 64,
epochs: 100,
lr: 1e-4
...
```

`train.py`文件中代码如下

```
import yaml
import argparse

parser = argparse.ArgumentParser(description="example for config loading")
parser.add_argument("--config", default='config.yaml', metavar="FILE",help="config file")
parser.add_argument("--train_dir", type=str, default='./data', help="")
parser.add_argument("--save_dir", type=str, default='./checkpoints', help="")
args = parser.parse_args()

# load config.yaml file
with open(args.config) as f:
	config = yaml.load(f)
# config output： {'batch_size': '64,', 'epochs': '100,', 'lr': '1e-4'}

# merge config into args
for key, value in config.items():
	setattr(args,k,av)
	# more custom setting can be done,
	# e.g. train and test setting in list, only assign the default None value 	   # in original args
```

