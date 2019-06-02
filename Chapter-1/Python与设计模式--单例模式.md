## Python与设计模式--单例模式

### 一、总线
总线是计算机各种功能部件或者设备之间传送数据、控制信号等信息的公共通信解决方案之一。

现假设有如下场景：某中央处理器（CPU）通过某种协议总线与一个信号灯相连，信号灯有64种颜色可以设置，中央处理器上运行着三个线程，都可以对这个信号灯进行控制，并且可以独立设置该信号灯的颜色。

抽象掉协议细节（用打印表示），如何实现线程对信号等的控制逻辑。
加线程锁进行控制，无疑是最先想到的方法，但各个线程对锁的控制，无疑加大了模块之间的耦合。下面，我们就用设计模式中的单例模式，来解决这个问题。

什么是单例模式？单例模式是指：保证一个类仅有一个实例，并提供一个访问它的全局访问点。具体到此例中，总线对象，就是一个单例，它仅有一个实例，各个线程对总线的访问只有一个全局访问点，即惟一的实例。

Python代码如下：
```py
# encoding=utf8
import threading
import time

# 这里使用方法__new__来实现单例模式
class Singleton(object): # 抽象单例
    def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
            orig = super(Singleton, cls)
            cls._instance = orig.__new__(cls, *args, **kw)
        return cls._instance
        
#总线
class Bus(Singleton):
    lock = threading.RLock()
    def sendData(self,data):
        self.lock.acquire()
        time.sleep(3)
        print "Sending Signal Data...", data
        self.lock.release()
        
#线程对象，为更加说明单例的含义，这里将Bus对象实例化写在了run里
class VisitEntity(threading.Thread):
    my_bus = ""
    name = ""
    def getName(self):
        return self.name
        
    def setName(self, name):
        self.name = name
        
    def run(self):
        self.my_bus = Bus()
        self.my_bus.sendData(self.name)

if  __name__=="__main__":
    for i in range(3):
        print "Entity %d begin to run..." % i
        my_entity = VisitEntity()
        my_entity.setName("Entity_"+str(i))
        my_entity.start()

```
运行结果如下：
```
Entity 0 begin to run...
Entity 1 begin to run...
Entity 2 begin to run...
Sending Signal Data... Entity_0
Sending Signal Data... Entity_1
Sending Signal Data... Entity_2
```
在程序运行过程中，三个线程同时运行（运行结果的前三行先很快打印出来），而后分别占用总线资源（后三行每隔3秒打印一行）。虽然看上去总线Bus被实例化了三次，但实际上在内存里只有一个实例。

### 二、单例模式
单例模式是所有设计模式中比较简单的一类，其定义如下：`Ensure a class has only one instance, and provide a global point of access to it.`（保证某一个类只有一个实例，而且在全局只有一个访问点）

### 三、单例模式的优点和应用
单例模式的优点：

- 1、由于单例模式要求在全局内只有一个实例，因而可以节省比较多的内存空间；
- 2、全局只有一个接入点，可以更好地进行数据同步控制，避免多重占用；
- 3、单例可长驻内存，减少系统开销。

单例模式的应用举例：
- 1、生成全局惟一的序列号；
- 2、访问全局复用的惟一资源，如磁盘、总线等；
- 3、单个对象占用的资源过多，如数据库等；
- 4、系统全局统一管理，如Windows下的Task Manager；
- 5、网站计数器。

### 四、单例模式的缺点
- 1、单例模式的扩展是比较困难的；
- 2、赋于了单例以太多的职责，某种程度上违反单一职责原则（六大原则后面会讲到）;
- 3、单例模式是并发协作软件模块中需要最先完成的，因而其不利于测试；
- 4、单例模式在某种情况下会导致“资源瓶颈”.
