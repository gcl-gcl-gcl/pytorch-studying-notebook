# 扩展pytorch

主要包括扩展torch.nn和torch.autograd

应该不需要太深究，因为标准层与激活函数基本可以满足所有需求



## 扩展torch.autograd

### 继承

首先我们所需要训练的operation有autograd，那我们的operation需要继承Function。

### 每个operation需要三个方法

#### __ init __操作

一方面，init可以传入不需要计算梯度的参数，方便后续计算

另一方面可以初始化

#### forward（）操作

记住：`forward()`的参数只能是`Variable`。函数的返回值既可以是 `Variable`也可以是`Variables`的`tuple`。

#### backward()操作

参数的个数和`forward`返回值的个数一样，每个参数代表传回到此`operation`的梯度.



## 扩展torch.nn

这个基本都需要自己去写。不管是拼盘还是设计一个独创的神经网络都需要自己根据画出来的草稿图去进行编写。

### 两个方法需要实现

#### __ init __

`__init__ (optional)` - 输入参数，例如`kernel sizes`, `numbers of features`, 等等。同时初始化 `parameters`和`buffers`。

#### forward()

`forward()` - 实例化一个执行`operation`的`Function`，使用它执行`operation`。和`functional wrapper(上面实现的那个简单的wrapper)`十分类似。

### CNN举例：

```python
import torch.nn as nn
import torch.nn.functional as F

class Net(nn.Module):
    def __init__(self):
        super(Net,self).__init__()
        self.conv1=nn.Conv2d(3,6,5)
        self.conv2=nn.Conv2d(6,16,5)
        self.fc1=nn.Linear(16*5*5,120)
        self.fc2=nn.Linear(120,84)
        self.fc3=nn.Linear(84,10)
    def forward(self,x):
        x=self.conv1(x)#进行一次卷积
        x=F.relu(x)
        x=F.max_pool2d(x,2)
        x=F.max_pool2d(F.relu(self.conv2(x)),2)    
        x=x.view(-1,x.size()[1:].numel())
        x=F.relu(self.fc1(x))
        x=F.relu(self.fc2(x))
        x=self.fc3(x)
        return x
    
net=Net()
net.to(device)  # 将模型加载到设备
print(net)
```

