---
layout: post
title: 如何给torchvision中的faster_rcnn_resnet50_fpn()换backbone网络
category: 技术
tags: 检测模型, 替换基准模型
keywords:  faster rcnn, backbone
---

#  目录

+ torchvision是怎么做的？
+ 如何替换backbone？
+ 训练（待完成）

# torchvision是如何实现faster_rcnn_resnet_fpn的？

torchvision中的代码实现如下

```PYTHON
def fasterrcnn_resnet50_fpn(pretrained=False, progress=True,
                            num_classes=91, pretrained_backbone=True, trainable_backbone_layers=None, **kwargs):
    """
    Constructs a Faster R-CNN model with a ResNet-50-FPN backbone.

    The input to the model is expected to be a list of tensors, each of shape ``[C, H, W]``, one for each
    image, and should be in ``0-1`` range. Different images can have different sizes.

    The behavior of the model changes depending if it is in training or evaluation mode.

    During training, the model expects both the input tensors, as well as a targets (list of dictionary),
    containing:

        - boxes (``FloatTensor[N, 4]``): the ground-truth boxes in ``[x1, y1, x2, y2]`` format, with
          ``0 <= x1 < x2 <= W`` and ``0 <= y1 < y2 <= H``.
        - labels (``Int64Tensor[N]``): the class label for each ground-truth box

    The model returns a ``Dict[Tensor]`` during training, containing the classification and regression
    losses for both the RPN and the R-CNN.

    During inference, the model requires only the input tensors, and returns the post-processed
    predictions as a ``List[Dict[Tensor]]``, one for each input image. The fields of the ``Dict`` are as
    follows:

        - boxes (``FloatTensor[N, 4]``): the predicted boxes in ``[x1, y1, x2, y2]`` format, with
          ``0 <= x1 < x2 <= W`` and ``0 <= y1 < y2 <= H``.
        - labels (``Int64Tensor[N]``): the predicted labels for each image
        - scores (``Tensor[N]``): the scores or each prediction

    Faster R-CNN is exportable to ONNX for a fixed batch size with inputs images of fixed size.
    Args:
        pretrained (bool): If True, returns a model pre-trained on COCO train2017
        progress (bool): If True, displays a progress bar of the download to stderr
        num_classes (int): number of output classes of the model (including the background)
        pretrained_backbone (bool): If True, returns a model with backbone pre-trained on Imagenet
        trainable_backbone_layers (int): number of trainable (not frozen) resnet layers starting from final block.
            Valid values are between 0 and 5, with 5 meaning all backbone layers are trainable.
    """
    # 这里是验证backbone中可训练层数是否符合要求，如果trainable_backbone_layers是默认值None的话，那么trainable_backbone_layers=3
    trainable_backbone_layers = _validate_trainable_layers(
        pretrained or pretrained_backbone, trainable_backbone_layers, 5, 3)
	# 如果使用官方训练好的检测模型，那就不需要加载resnet预训练模型
    if pretrained:
        # no need to download the backbone if pretrained is set
        pretrained_backbone = False
        # 
    backbone = resnet_fpn_backbone('resnet50', pretrained_backbone, trainable_layers=trainable_backbone_layers)
    model = FasterRCNN(backbone, num_classes, **kwargs)
    if pretrained:
        state_dict = load_state_dict_from_url(model_urls['fasterrcnn_resnet50_fpn_coco'],
                                              progress=progress)
        model.load_state_dict(state_dict)
        overwrite_eps(model, 0.0)
    return model
```

1. 首先，看看`_validate_trainable_layers`里面实现了什么逻辑，这里主要注意：`_validate_trainable_layers`函数是在`torchvision/modes/detection/backbone_utils.py`中定义的，后续会用到这个

   ```python
   def _validate_trainable_layers(pretrained, trainable_backbone_layers, max_value, default_value):
       # dont freeze any layers if pretrained model or backbone is not used
       if not pretrained:
           if trainable_backbone_layers is not None:
               warnings.warn(
                   "Changing trainable_backbone_layers has not effect if "
                   "neither pretrained nor pretrained_backbone have been set to True, "
                   "falling back to trainable_backbone_layers={} so that all layers are trainable".format(max_value))
           trainable_backbone_layers = max_value
   
       # by default freeze first blocks
       if trainable_backbone_layers is None:
           trainable_backbone_layers = default_value
       assert 0 <= trainable_backbone_layers <= max_value
       return trainable_backbone_layers
   ```

   

2.  接着，看看`backbone`函数的处理

   ```python
   def resnet_fpn_backbone(
       backbone_name,
       pretrained,
       norm_layer=misc_nn_ops.FrozenBatchNorm2d,
       trainable_layers=3,
       returned_layers=None,
       extra_blocks=None
   ):
       """
       Constructs a specified ResNet backbone with FPN on top. Freezes the specified number of layers in the backbone.
       Args:
           backbone_name (string): resnet architecture. Possible values are 'ResNet', 'resnet18', 'resnet34', 'resnet50',
                'resnet101', 'resnet152', 'resnext50_32x4d', 'resnext101_32x8d', 'wide_resnet50_2', 'wide_resnet101_2'
           pretrained (bool): If True, returns a model with backbone pre-trained on Imagenet
           norm_layer (torchvision.ops): it is recommended to use the default value. For details visit:
               (https://github.com/facebookresearch/maskrcnn-benchmark/issues/267)
           trainable_layers (int): number of trainable (not frozen) resnet layers starting from final block.
               Valid values are between 0 and 5, with 5 meaning all backbone layers are trainable.
           returned_layers (list of int): The layers of the network to return. Each entry must be in ``[1, 4]``.
               By default all layers are returned.
           extra_blocks (ExtraFPNBlock or None): if provided, extra operations will
               be performed. It is expected to take the fpn features, the original
               features and the names of the original features as input, and returns
               a new list of feature maps and their corresponding names. By
               default a ``LastLevelMaxPool`` is used.
       """
       # 获得resnet50模型，如果使用的是预训练的faster rcnn， 那么pretrained就是false，如果是从头训练（但是使用resnet50作为预训练模型），那就是True
       backbone = resnet.__dict__[backbone_name](
           pretrained=pretrained,
           norm_layer=norm_layer)
   
       # select layers that wont be frozen
       # trainable_layers默认是3
       assert 0 <= trainable_layers <= 5
       # resnet50模型，只有最后3层网络的参数要在训练faster rcnn时一同训练，前两层不需要训练，参数固定。
       layers_to_train = ['layer4', 'layer3', 'layer2', 'layer1', 'conv1'][:trainable_layers]
       if trainable_layers == 5:
           layers_to_train.append('bn1')
       for name, parameter in backbone.named_parameters():
           if all([not name.startswith(layer) for layer in layers_to_train]):
               parameter.requires_grad_(False)
   
       if extra_blocks is None:
           extra_blocks = LastLevelMaxPool()
   	# 返回的层，若没指定的话使用默认的层
       if returned_layers is None:
           returned_layers = [1, 2, 3, 4]
       assert min(returned_layers) > 0 and max(returned_layers) < 5
       # layer1：0，layer2：1，layer3：2，layer4：3
       return_layers = {f'layer{k}': str(v) for v, k in enumerate(returned_layers)}
   	# 计算特征层的尺寸，inplanes=2048, in_channels_stage2=256
       in_channels_stage2 = backbone.inplanes // 8
       # 分别计算了returned_layers对应的输入层：256，512，1024，2028，恰好对应resnet50中四个layer的输出特征尺寸。
       in_channels_list = [in_channels_stage2 * 2 ** (i - 1) for i in returned_layers]
   	# 输出通道的大小
       out_channels = 256
       return BackboneWithFPN(backbone, return_layers, in_channels_list, out_channels, extra_blocks=extra_blocks)
   ```

   

3. 接下来，看看`BackboneWithFPN`中是什么

   ```python
   class BackboneWithFPN(nn.Module):
       """
       Adds a FPN on top of a model.
       Internally, it uses torchvision.models._utils.IntermediateLayerGetter to
       extract a submodel that returns the feature maps specified in return_layers.
       The same limitations of IntermediatLayerGetter apply here.
       Args:
           backbone (nn.Module)
           return_layers (Dict[name, new_name]): a dict containing the names
               of the modules for which the activations will be returned as
               the key of the dict, and the value of the dict is the name
               of the returned activation (which the user can specify).
           in_channels_list (List[int]): number of channels for each feature map
               that is returned, in the order they are present in the OrderedDict
           out_channels (int): number of channels in the FPN.
       Attributes:
           out_channels (int): the number of channels in the FPN
       """
       def __init__(self, backbone, return_layers, in_channels_list, out_channels, extra_blocks=None):
           super(BackboneWithFPN, self).__init__()
   
           if extra_blocks is None:
               extra_blocks = LastLevelMaxPool()
   		# 根据returned_layers，从最原始的backbone模型中抽取return_layers指定的网络层，注意网络会从头取到return_layer中的最后一层，丢弃后面的层。如resnet50中最后一层是layer4,那么layer4后面的层将会被丢弃。
           self.body = IntermediateLayerGetter(backbone, return_layers=return_layers)
   		# 特定金字塔，如上所述，分别取256，512，1024，2048四个层级的特征
           self.fpn = FeaturePyramidNetwork(
               in_channels_list=in_channels_list,
               out_channels=out_channels,
               extra_blocks=extra_blocks,
           )
           # 最终的output的通道是256
           self.out_channels = out_channels
   
       def forward(self, x):
           x = self.body(x)
           x = self.fpn(x)
           return x
   ```

   最后一步是将所得的backbone赋值给`faster rcnn`中。

   # 如何以torchvision的方式实现faster_rcnn_vgg16_fpn？

   根据上述分析，我们依葫芦画瓢就行了。但是观察到`backbone_utils`是torchvision内置的文件，没法直接修改该文件，所以要创建一个自定义的`backbone_utils`，为了方便，我们将`faster_rcnn_vgg16_fpn`和`backbone_utils`中用到的函数都放在一块，下面直接贴代码讲解

   ```python
   from torchvision import transforms
   from torchvision.models.detection import FasterRCNN
   from torchvision.models.detection import backbone_utils
   from torchvision.ops import misc as misc_nn_ops
   from torchvision.ops.feature_pyramid_network import LastLevelMaxPool
   import torch
   import torch.nn as nn
   from torchvision import models
   from torchvision import datasets
   from torch.utils.data import Sampler, BatchSampler
   
   def fasterrcnn_vgg16_fpn(pretrained=False, progress=True,
                               num_classes=91, pretrained_backbone=True, trainable_backbone_layers=None, **kwargs):
       # 这里主要注意的是不同模型，设置也不同，比如vgg16的模型构造与resnet不一样，所以_validate_trainable_layers中max_value的值应该是30，而default_value是10
       not_trainable_backbone_layers = backbone_utils._validate_trainable_layers(
           pretrained or pretrained_backbone, trainable_backbone_layers, 30, 10)
   
       if pretrained:
           # no need to download the backbone if pretrained is set
           pretrained_backbone = False
           
       backbone = vgg16_fpn_backbone('vgg16', pretrained_backbone, fpn=True, not_trainable_layers=not_trainable_backbone_layers)
       model = FasterRCNN(backbone, num_classes, **kwargs)
       if pretrained:
           state_dict = torch.hub.load_state_dict_from_url(model_urls['fasterrcnn_resnet50_fpn_coco'],
                                                 progress=progress)
           model.load_state_dict(state_dict)
           overwrite_eps(model, 0.0)
       return model
   ```

   1. backbone的构造

      ```python
      def vgg16_fpn_backbone(
          backbone_name,
          pretrained,
          fpn,
          norm_layer=misc_nn_ops.FrozenBatchNorm2d,
          not_trainable_layers=10,
          returned_layers=None,
          extra_blocks=None
      ):
          """
          Constructs a specified ResNet backbone with FPN on top. Freezes the specified number of layers in the backbone.
          Args:
              backbone_name (string): resnet architecture. Possible values are 'ResNet', 'resnet18', 'resnet34', 'resnet50',
                   'resnet101', 'resnet152', 'resnext50_32x4d', 'resnext101_32x8d', 'wide_resnet50_2', 'wide_resnet101_2'
              pretrained (bool): If True, returns a model with backbone pre-trained on Imagenet
              norm_layer (torchvision.ops): it is recommended to use the default value. For details visit:
                  (https://github.com/facebookresearch/maskrcnn-benchmark/issues/267)
              trainable_layers (int): number of trainable (not frozen) resnet layers starting from final block.
                  Valid values are between 0 and 5, with 5 meaning all backbone layers are trainable.
              returned_layers (list of int): The layers of the network to return. Each entry must be in ``[1, 4]``.
                  By default all layers are returned.
              extra_blocks (ExtraFPNBlock or None): if provided, extra operations will
                  be performed. It is expected to take the fpn features, the original
                  features and the names of the original features as input, and returns
                  a new list of feature maps and their corresponding names. By
                  default a ``LastLevelMaxPool`` is used.
          """
          # 直接取vgg16的backbone的feature特征
          backbone = models.vgg16(pretrained=pretrained).features
          # select layers that wont be frozen
          assert 0 <= not_trainable_layers <= 30
      
          # 固定VGG16的前10层，只更新后面的网络层参数
          for layer in backbone[:not_trainable_layers]:
              for p in layer.parameters():
                  p.requires_grad = False
      
      	# 保持out_channels = 256
          out_channels = 256
          if fpn: # 参考了mobile_net作为基准网络的实现方式
              if extra_blocks is None:
                  extra_blocks = LastLevelMaxPool()
      
              if returned_layers is None:
                  # 根据不同的VGG，设置不同的returned_layers, VGG16中features特征层一共30层
                  # FPN特征金字塔层，选择VGG中不同层级的特征尺寸的输出的索引，以下几个索引对应的卷积层输出的特征分别是 64，128，256， 512
                  # returned_layers的关键是找到特征金字塔的各个特征层所在的位置
                  returned_layers = [2, 7, 14, 28]
              assert min(returned_layers) > 0 and max(returned_layers) < 31
              return_layers = {f'{k}': str(v) for v, k in enumerate(returned_layers)}
      		# 这里同理
              in_channels_list = [backbone[i].out_channels for i in returned_layers]
      
              return backbone_utils.BackboneWithFPN(backbone, return_layers, in_channels_list, out_channels,
                                                    extra_blocks=extra_blocks)
          else:
              m = nn.Sequential(
                  backbone,
                  # depthwise linear combination of channels to reduce their size
                  nn.Conv2d(backbone[-1].out_channels, out_channels, 1),
              )
              m.out_channels = out_channels
              return m
      ```

   2. 测试，根据官方给的测试代码，训练代码待续。

      ```python
      model = fasterrcnn_vgg16_fpn()
      model.eval()
      images = [torch.rand(3, 300, 400), torch.rand(3, 500, 400)]
      output = model(images)
      print(output)
      ```

      

   # 参考资料

   [1]:https://github.com/pytorch/vision/tree/main/torchvision/models/detection
   [2]: https://github.com/chenyuntc/simple-faster-rcnn-pytorch
   [3]: https://github.com/bubbliiiing/faster-rcnn-pytorch

   

   

   

