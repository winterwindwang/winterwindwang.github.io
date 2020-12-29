---

layout: post

title : 神经网络解释---saliency map

category: 技术

tag: 神经网络解释, 网络权重/特征可视化

keywork: React
---

## Saliency map可视化像素值对梯度的重要性程度

实现代码如下：

```
def compute_saliency_maps(X, y, model):
    """
    X表示图片, y表示分类结果, model表示使用的分类模型

    Input :
    - X : Input images : Tensor of shape (N, 3, H, W)
    - y : Label for X : LongTensor of shape (N,)
    - model : A pretrained CNN that will be used to computer the saliency map

    Return :
    - saliency : A Tensor of shape (N, H, W) giving the saliency maps for the input images
    """
    # 确保model是test模式
    model.eval()

    # 确保X是需要gradient
    X.requires_grad_()

    saliency = None

    logits, aux , _= model(X)
    # batch_x = batch_x.to(device)
    # batch_y = batch_y.to(device)
    # batch_out, aux,batch_out_p = model(batch_x)
    # batch_loss = criterion(batch_out, batch_y)
    # _, batch_pred = batch_out.max(dim=1)
    logits = logits.gather(1, y.view(-1, 1)).squeeze()  # 得到正确分类
    logits.backward(torch.FloatTensor([1.0] * len(logits)).cuda())  # 只计算正确分类部分的loss

    saliency = abs(X.grad.data)  # 返回X的梯度绝对值大小
    # saliency, _ = torch.max(saliency, dim=1)

    return saliency.squeeze()
    
for i, (batch_x, batch_y, batch_p) in enumerate(test_loader):
            # batch_x = batch_x.permute(0, 2, 1)
            batch_size = batch_x.size(0)
            batch_x = batch_x.to(device)
            batch_y = batch_y.to(device)
            batch_out, aux, batch_out_p = model(batch_x)
            # batch_loss = criterion(batch_out, batch_y)
            # _, batch_pred = batch_out.max(dim=1)
            # Compute saliency maps for images in X
            saliency = compute_saliency_maps(batch_x, batch_y, model)

       # Convert the saliency map from Torch Tensor to numpy array and show images
            # and saliency maps together.
            saliency = saliency.cpu().numpy()
            batch_x = batch_x.cpu().data.numpy()
            # batch_x = np.expand_dims(batch_x)
            # saliency = np.expand_dims(saliency)
            N = batch_x.shape[0]

            for i in range(N):
                plt.subplot(2, N, i + 1)
                plt.imshow(batch_x[i], figure_size=(5,10))
                plt.axis('off')
                # plt.title(class_names[y[i]])
                plt.title(batch_y[i].cpu().data.numpy())
                plt.subplot(2, N, N + i + 1)
                plt.imshow(saliency[i], cmap=plt.cm.hot, figure_size=(5,10))
                plt.axis('off')
                plt.gcf().set_size_inches(12, 5)
            plt.show()
```

参考文献：[reference][1].

[1]:Simonyan, K., Vedaldi, A., & Zisserman, A. (2014). Deep Inside Convolutional Networks: Visualising Image Classification Models and Saliency Maps. *CoRR, abs/1312.6034*.

