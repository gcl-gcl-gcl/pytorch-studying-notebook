# CUDA

## 意义

CUDA存在的意义是把张量分配到GPU上从而提升速度

他会首先记录当前选择的GPU，然后把所有的张量创建在选择的GPU上

不支持跨GPU操作

##  代码

定义：啥也不说的情况下，默认是分配到GPU 0上面

```python
x = torch.cuda.FloatTensor(1)
# x.get_device() == 0

#类比下面的做法，效果是一样的

y = torch.FloatTensor(1).cuda()
# y.get_device() == 0
```

上下文控制器

```python
with torch.cuda.device(1):
```

此时在这里声明变量的话那都是存在GPU1下面的

我个人感觉因为不同GPU变量不能交叉计算的话，那就全默认（分配到GPU0下面）就完事了。

