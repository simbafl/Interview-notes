## Python与设计模式--装饰器模式

### 一、快餐点餐系统
又提到了那个快餐点餐系统，不过今天我们只以其中的一个类作为主角：饮料类。首先，回忆下饮料类：

```py
class Beverage():
    name = ""
    price = 0.0
    type = "BEVERAGE"
    def getPrice(self):
        return self.price
    def setPrice(self, price):
        self.price = price
    def getName(self):
        return self.name


class coke(Beverage):
    def __init__(self):
        self.name = "coke"
        self.price = 4.0


class milk(Beverage):
    def __init__(self):
        self.name = "milk"
        self.price = 5.0

"""        
除了基本配置，快餐店卖可乐时，可以选择加冰，如果加冰的话，要在原价上加0.3元；卖牛奶时，可以选择加糖，
如果加糖的话，要原价上加0.5元。怎么解决这样的问题？可以选择装饰器模式来解决这一类的问题。

首先，定义装饰器类：
"""

class drinkDecorator():
    def getName(self):
        pass
    def getPrice(self):
        pass

class iceDecorator(drinkDecorator):
    def __init__(self,beverage):
        self.beverage = beverage
        
    def getName(self):
        return self.beverage.getName()+" + ice"
        
    def getPrice(self):
        return self.beverage.getPrice() + 0.3
    
class sugarDecorator(drinkDecorator):
    def __init__(self,beverage):
        self.beverage = beverage
    def getName(self):
        return self.beverage.getName()+" + sugar"
    def getPrice(self):
        return self.beverage.getPrice() + 0.5
        
# 构建好装饰器后，在具体的业务场景中，就可以与饮料类进行关联。以可乐+冰为例，示例业务场景如下：

if  __name__ == "__main__":
    coke_cola = coke()
    print "Name:%s" % coke_cola.getName()
    print "Price:%s" % coke_cola.getPrice()
    ice_coke = iceDecorator(coke_cola)
    print "Name:%s" % ice_coke.getName()
    print "Price:%s" % ice_coke.getPrice()

```
打印结果如下：
```
Name:coke
Price:4.0
Name:coke +ice
Price:4.3
```

### 二、装饰器模式
装饰器模式定义如下：动态地给一个对象添加一些额外的职责。在增加功能方面，装饰器模式比生成子类更为灵活。

装饰器模式和上一节说到的代理模式非常相似，可以认为，装饰器模式就是代理模式的一个特殊应用，两者的共同点是都具有相同的接口，不同点是侧重对主题类的过程的控制，而装饰模式则侧重对类功能的加强或减弱。

上一次说到，JAVA中的动态代理模式，是实现AOP的重要手段。而在Python中，AOP通过装饰器模式实现更为简洁和方便。
先来解释一下什么是AOP。AOP即Aspect Oriented Programming，中文翻译为面向切面的编程，它的含义可以解释为：如果几个或更多个逻辑过程中（这类逻辑过程可能位于不同的对象，不同的接口当中），有重复的操作行为，就可以将这些行为提取出来（即形成切面），进行统一管理和维护。举例子说，系统中需要在各个地方打印日志，就可以将打印日志这一操作提取出来，作为切面进行统一维护。

从编程思想的关系来看，可以认为AOP和OOP（面向对象的编程）是并列关系，二者是可以替换的，也可以结合起来用。实际上，在Python语言中，是天然支持装饰器的，如下例：
```py
def log(func):
    def wrapper(*args, **kw):
        print 'call %s():' % func.__name__
        return func(*args, **kw)
    return wrapper

@log
def now():
    print '2016-12-04'
if  __name__ == "__main__":
    now()
```    
打印如下：
```
call now():
2016-12-04
```
log接口就是装饰器的定义，而Python的@语法部分则直接支持装饰器的使用。
如果要在快餐点餐系统中打印日志，该如何进行AOP改造呢？可以借助类的静态方法或者类方法来实现：
```py
class LogManager:
    @staticmethod
    def log(func):
        def wrapper(*args):
            print "Visit Func %s"%func.__name__
            return func(*args)
        return wrapper

```
在需要打印日志的地方直接加上`@LogManager.log`，即可打印出访问的日志信息。如，在beverage类的函数前加上`@LogManager.log`，场景类保持不变，则打印结果如下：
```
Visit Func getName
Name:coke
Visit Func getPrice
Price:4.0
Visit Func getName
Name:coke +ice
Visit Func getPrice
Price:4.3
```

### 三、装饰器模式的优点和应用场景
优点：
- 1、装饰器模式是继承方式的一个替代方案，可以轻量级的扩展被装饰对象的功能；
- 2、Python的装饰器模式是实现AOP的一种方式，便于相同操作位于不同调用位置的统一管理。

应用场景：
- 1、需要扩展、增强或者减弱一个类的功能，如本例。

### 四、装饰器模式的缺点
- 1、多层装饰器的调试和维护有比较大的困难。


