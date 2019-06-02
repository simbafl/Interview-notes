## Python与设计模式--观察者模式

### 一、火警报警器
在门面模式中，我们提到过火警报警器。在当时，我们关注的是通过封装减少代码重复。而今天，我们将从业务流程的实现角度，来再次实现该火警报警器。
```py
class AlarmSensor:
    def run(self):
        print "Alarm Ring..."
class WaterSprinker:
    def run(self):
        print "Spray Water..."
class EmergencyDialer:
    def run(self):
        print "Dial 119..."
"""        
 以上是门面模式中的三个传感器类的结构。仔细分析业务，报警器、洒水器、拨号器都是“观察”烟雾传感器的情况来做反应的。
 因而，他们三个都是观察者，而烟雾传感器则是被观察对象了。根据分析，将三个类提取共性，泛化出“观察者”类，并构造被观察者。
"""
# 观察者如下：

class Observer:
    def update(self):
        pass
        
class AlarmSensor(Observer):
    def update(self,action):
        print "Alarm Got: %s" % action
        self.runAlarm()
    def runAlarm(self):
        print "Alarm Ring..."
        
class WaterSprinker(Observer):
    def update(self,action):
        print "Sprinker Got: %s" % action
        self.runSprinker()
    def runSprinker(self):
        print "Spray Water..."
        
class EmergencyDialer(Observer):
    def update(self,action):
        print "Dialer Got: %s"%action
        self.runDialer()
    def runDialer(self):
        print "Dial 119..."

"""        
 观察者中定义了update接口，如果被观察者状态比较多，或者每个具体的观察者方法比较多，可以通过update传参数进行更丰富的控制。
"""
# 下面构造被观察者。

class Observed:
    observers = []
    action = ""
    def addObserver(self,observer):
        self.observers.append(observer)
        
    def notifyAll(self):
        for obs in self.observers:
            obs.update(self.action)
            
class smokeSensor(Observed):
    def setAction(self,action):
        self.action = action
    def isFire(self):
        return True

# 被观察者中首先将观察对象加入到观察者数组中，若发生情况，则通过notifyAll通知各观察者。
# 业务代码如下：

if __name__ == "__main__":
    alarm = AlarmSensor()
    sprinker = WaterSprinker()
    dialer = EmergencyDialer()

    smoke_sensor = smokeSensor()
    smoke_sensor.addObserver(alarm)
    smoke_sensor.addObserver(sprinker)
    smoke_sensor.addObserver(dialer)


    if smoke_sensor.isFire():
        smoke_sensor.setAction("On Fire!")
        smoke_sensor.notifyAll()
```
打印如下：
```
Alarm Got: On Fire!
Alarm Ring...
Sprinker Got: On Fire!
Spray Water...
Dialer Got: On Fire!
Dial 119...
```
### 二、观察者模式
观察者模式也叫发布-订阅模式，其定义如下：定义对象间一种一对多的依赖关系，使得当该对象状态改变时，所有依赖于它的对象都会得到通知，并被自动更新。

观察者模式的通知方式可以通过直接调用等同步方式实现（如函数调用，HTTP接口调用等），也可以通过消息队列异步调用（同步调用指被观察者发布消息后，必须等所有观察者响应结束后才可以进行接下来的操作；异步调用指被观察者发布消息后，即可进行接下来的操作。）。事实上，许多开源的消息队列就直接支持发布-订阅模式，如Zero MQ等。

### 三、观察者模式的优点和应用场景
优点：
- 1、观察者与被观察者之间是抽象耦合的；
- 2、可以将许多符合单一职责原则的模块进行触发，也可以很方便地实现广播。

应用场景：
- 1、消息交换场景。如上述说到的消息队列等；
- 2、多级触发场景。比如支持中断模式的场景中，一个中断即会引发一连串反应，就可以使用观察者模式。

### 四、观察者模式的缺点
- 1、观察者模式可能会带来整体系统效率的浪费；
- 2、如果被观察者之间有依赖关系，其逻辑关系的梳理需要费些心思。

