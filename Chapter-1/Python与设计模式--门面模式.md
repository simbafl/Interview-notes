## Python与设计模式--门面模式

### 一、火警报警器
假设有一组火警报警系统，由三个子元件构成：一个警报器，一个喷水器，一个自动拨打电话的装置。其抽象如下：
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
# 在业务中如果需要将三个部件启动，例如，如果有一个烟雾传感器，检测到了烟雾。在业务环境中需要做如下操作：

if __name__=="__main__":
    alarm_sensor=AlarmSensor()
    water_sprinker=WaterSprinker()
    emergency_dialer=EmergencyDialer()
    alarm_sensor.run()
    water_sprinker.run()
    emergency_dialer.run()

```
但如果在多个业务场景中需要启动三个部件，怎么办？Ctrl+C加上Ctrl+V么？当然可以这样，但作为码农的基本修养之一，减少重复代码是应该会被很轻易想到的方法。这样，需要将其进行封装，在设计模式中，被封装成的新对象，叫做门面。门面构建如下：

```py
class EmergencyFacade:
    def __init__(self):
        self.alarm_sensor=AlarmSensor()
        self.water_sprinker=WaterSprinker()
        self.emergency_dialer=EmergencyDialer()
    def runAll(self):
        self.alarm_sensor.run()
        self.water_sprinker.run()
        self.emergency_dialer.run()
# 这样，业务场景中这样写就可以了：

if __name__=="__main__":
    emergency_facade=EmergencyFacade()
    emergency_facade.runAll()

```
打印如下：
```
Alarm Ring...
Spray Water...
Dial 119...
```

### 二、门面模式
门面模式也叫外观模式，定义如下：要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。门面模式提供一个高层次的接口，使得子系统更易于使用。门面模式注重“统一的对象”，也就是提供一个访问子系统的接口。门面模式与之前说过的模板模式有类似的地方，都是对一些需要重复方法的封装。但从本质上来说，是不同的。模板模式是对类本身的方法的封装，其被封装的方法也可以单独使用；而门面模式，是对子系统的封装，其被封装的接口理论上是不会被单独提出来用的。
f1.png

### 三、门面模式的优点和使用场景
优点：
1、减少了系统之间的相互依赖，提高了系统的灵活；
2、提高了整体系统的安全性：封装起的系统对外的接口才可以用，隐藏了很多内部接口细节，若方法不允许使用，则在门面中可以进行灵活控制。
使用场景：
1、为一个复杂的子系统提供一个外界访问的接口。这类例子是生活还是蛮常见的，例如电视遥控器的抽象模型，电信运营商的用户交互设备等；
2、需要简化操作界面时。例如常见的扁平化系统操作界面等，在生活中和工业中都很常见。

### 四、门面模式的缺点
1、门面模式的缺点在于，不符合开闭原则，一旦系统成形后需要修改，几乎只能重写门面代码，这比继承或者覆写等方式，或者其它一些符合开闭原则的模式风险都会大一些。

