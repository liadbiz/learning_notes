这篇笔记是关于Python的日志系统

首先是从文档中总结的关于日志的基本知识

日志是通过打印出某些描述信息来确保程序执行过程中某个事件的执行状态。

主要分为五个级别：

+ logging.debug()
+ logging.info()
+ logging.warning()
+ logging.error()
+ logging.critical()

级别从低到高。

默认条件下是将消息打印到终端，我们可以配置将消息输出不同的地方。

下面是将日志信息输出到文件的基本用法：

```python
import logging

logging.basicConfig(filename='test_logging.log', level=logging.DEBUG)

logging.debug("this message should be put into the logging file")
logging.info("so dese this")
logging.warning('and this too')

```

由于我们设置了日志的级别是DEBUG，所以三条日志信息都会输出到文件。

之前说过，日志的主要作用是确保时间的执行。

下面是一个简单的例子：

```python
# myapp.py

import logging
import mylib

def main():
    logging.basicConfig(format='%(asctime)s %(message)s', level=logging.INFO)
    logging.info('func start')
    mylib.do_something()
    logging.info('func end')
if __name__ == '__main__':
    main()

# mulib.py

import logging

def do_something():
    logging.debug('doing something')
```

logging实际上有四个模块组成，而消息要在这四个模块间传递。分别是：

+ loggers 暴露用户接口
+ handlers  把日志信息送到对应的地方
+ filters 决定哪些信息可以输出
+ formatters 决定最后输出的形式 

当我们在使用logging模块时，实际上是在logge的实例上调用相应的方法，每个实例都有一个名字，而名字采用的是命名空间的的形式，也就是foo.bar这种。

一个约定俗称的规定是我们一般就使用模块的层级关系来命名logger实例。即：

```python
logger = logging.getLogger(__name__)
```

在这个层级关系的最顶级的实例的名字就是'root'，这也是我们为何在之前的例子的输出中会看到root。实际上最终输出的消息的形式是`severity:logger name:message`。

默认情况下，我们的输出是`sys.err`，模块中支持多个输出目标，比如文件，邮件等，如果提供的输出目标不能满足你的需求，你甚至还能自己写一个`destination class`。

下面分别介绍这四个模块。

首先我们来看下描述信息在logger和handler中的流动路径图：

![logging flow](url)



