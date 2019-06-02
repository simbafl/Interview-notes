## Python与设计模式--工厂类相关模式

### 一、快餐点餐系统
想必大家一定见过类似于麦当劳自助点餐台一类的点餐系统吧。在一个大的触摸显示屏上，有三类可以选择的上餐品：汉堡等主餐、小食、饮料。当我们选择好自己需要的食物，支付完成后，订单就生成了。

下面，我们用今天的主角--工厂模式--来生成这些食物的逻辑主体。
首先，来看主餐的生成（仅以两种汉堡为例）。
```py
class Burger():
    name = ""
    price = 0.0
    
    def getPrice(self):
        return self.price
        
    def setPrice(self,price):
        self.price=price
        
    def getName(self):
        return self.name
        
class cheeseBurger(Burger):
    def __init__(self):
        self.name = "cheese burger"
        self.price = 10.0
        
class spicyChickenBurger(Burger):
    def __init__(self):
        self.name = "spicy chicken burger"
        self.price = 15.0

# 其次，是小食。（内容基本一致）
class Snack():
    name = ""
    price = 0.0
    type = "SNACK"
    
    def getPrice(self):
        return self.price
        
    def setPrice(self, price):
        self.price = price
        
    def getName(self):
        return self.name

class chips(Snack):
    def __init__(self):
        self.name = "chips"
        self.price = 6.0

class chickenWings(Snack):
    def __init__(self):
        self.name = "chicken wings"
        self.price = 12.0

# 最后，是饮料。
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
以上的Burger，Snack，Beverage，都可以认为是该快餐店的产品，由于只提供了抽象方法，
我们把它们叫抽象产品类，而cheese burger等6个由抽象产品类衍生出的子类，叫作具体产品类。
"""

# 接下来，“工厂”就要出现了。
class foodFactory():
    type = ""
    
    def createFood(self,foodClass):
        print self.type," factory produce a instance."
        foodIns = foodClass()
        return foodIns
        
class burgerFactory(foodFactory):
    def __init__(self):
        self.type = "BURGER"
        
class snackFactory(foodFactory):
    def __init__(self):
        self.type = "SNACK"
        
class beverageFactory(foodFactory):
    def __init__(self):
        self.type = "BEVERAGE"

# 同样，foodFactory为抽象的工厂类，而burgerFactory，snackFactory，beverageFactory为具体的工厂类。

# 在业务场景中，工厂模式是如何“生产”产品的呢？

if  __name__=="__main__":
    burger_factory = burgerFactory()
    snack_factorry = snackFactory()
    beverage_factory = beverageFactory()
    cheese_burger = burger_factory.createFood(cheeseBurger)
    print cheese_burger.getName(),cheese_burger.getPrice()
    chicken_wings = snack_factorry.createFood(chickenWings)
    print chicken_wings.getName(),chicken_wings.getPrice()
    coke_drink = beverage_factory.createFood(coke)
    print coke_drink.getName(),coke_drink.getPrice()
    
```
可见，业务中先生成了工厂，然后用工厂中的createFood方法和对应的参数直接生成产品实例。

打印结果如下：
```
BURGER factory produce a instance.
cheese burger 10.0
SNACK factory produce a instance.
chicken wings 12.0
BEVERAGE factory produce a instance.
coke 4.0
```
### 二、工厂模式、简单工厂模式、抽象工厂模式
**工厂模式**的定义如下：定义一个用于创建对象的接口，让子类决定实例化哪个类。工厂方法使一个类的实例化延迟到其子类。其通用类图如下。其产品类定义产品的公共属性和接口，工厂类定义产品实例化的“方式”。

在上述例子中，工厂在使用前必须实例化。如果，把工厂加个类方法，写成如下形式：
```py
class simpleFoodFactory():
    @classmethod
    def createFood(cls,foodClass):
        print "Simple factory produce a instance."
        foodIns = foodClass()
        return foodIns

```
在场景中写成如下形式：
```py
spicy_chicken_burger = simpleFoodFactory.createFood(spicyChickenBurger)

```
这样，省去了将工厂实例化的过程。这种模式就叫做**简单工厂模式**。

还是在上述例子中，`createFood`方法中必须传入`foodClass`才可以指定生成的food实例种类，如果，将每一个细致的产品都建立对应的工厂（如`cheeseBurger`建立对应一个`cheeseBurgerFactory`），这样，生成食物时，`foodClass`也不必指定。事实上，此时，`burgerFactory`就是具体食物工厂的一层抽象。这种模式，就是**抽象工厂模式**。

### 三、工厂模式的优点和应用
工厂模式、抽象工厂模式的优点：
- 1、工厂模式巨有非常好的封装性，代码结构清晰；在抽象工厂模式中，其结构还可以随着需要进行更深或者更浅的抽象层级调整，非常灵活；
- 2、屏蔽产品类，使产品的被使用业务场景和产品的功能细节可以分而开发进行，是比较典型的解耦框架。

工厂模式、抽象工厂模式的使用场景：
- 1、当系统实例要求比较灵活和可扩展时，可以考虑工厂模式或者抽象工厂模式实现。比如，
在通信系统中，高层通信协议会很多样化，同时，上层协议依赖于下层协议，那么就可以对应建立对应层级的抽象工厂，根据不同的“产品需求”去生产定制的实例。

### 四、工厂类模式的不足
- 1、工厂模式相对于直接生成实例过程要复杂一些，所以，在小项目中，可以不使用工厂模式；
- 2、抽象工厂模式中，产品类的扩展比较麻烦。毕竟，每一个工厂对应每一类产品，产品扩展，就意味着相应的抽象工厂也要扩展。

