## Python与设计模式--建造者模式

### 一、快餐点餐系统
今天的例子，还是上一次谈到的快餐点餐系统。只不过，今天我们从订单的角度来构造这个系统。最先还是有请上次的主角们：

主餐：
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
        
# 小食：
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
# 饮料：
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
        
# 最终，我们是要建造一个订单，因而，需要一个订单类。
假设，一个订单，包括一份主食，一份小食，一种饮料。（省去一些异常判断）

class order():
    burger = ""
    snack = ""
    beverage = ""
    def __init__(self,orderBuilder):
        self.burger=orderBuilder.bBurger
        self.snack=orderBuilder.bSnack
        self.beverage=orderBuilder.bBeverage
        
    def show(self):
        print "Burger:%s"%self.burger.getName()
        print "Snack:%s"%self.snack.getName()
        print "Beverage:%s"%self.beverage.getName()
        
# 代码中的orderBuilder是什么鬼？这个orderBuilder就是建造者模式中所谓的“建造者”了，先不要问为什么不在order类中把所有内容都填上，而非要用builder去创建。接着往下看。

# orderBuilder的实现如下：

class orderBuilder():
    bBurger = ""
    bSnack = ""
    bBeverage = ""
    def addBurger(self,xBurger):
        self.bBurger = xBurger
        
    def addSnack(self,xSnack):
        self.bSnack = xSnack
        
    def addBeverage(self,xBeverage):
        self.bBeverage = xBeverage
        
    def build(self):
        return order(self)
        
# 在场景中如下去实现订单的生成：
if  __name__=="__main__":
    order_builder = orderBuilder()
    order_builder.addBurger(spicyChickenBurger())
    order_builder.addSnack(chips())
    order_builder.addBeverage(milk())
    order_1 = order_builder.build()
    order_1.show()

```
打印结果如下：
```
Burger:spicy chicken burger
Snack:chips
Beverage:milk
```

### 二、建造者模式
建造者模式的定义如下：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

建造者模式的作用，就是将“构建”和“表示”分离，以达到解耦的作用。在上面订单的构建过程中，如果将order直接通过参数定义好（其构建与表示没有分离），同时在多处进行订单生成，此时需要修改订单内容，则需要一处处去修改，业务风险也就提高了不少。

在建造者模式中，还可以加一个Director类，用以安排已有模块的构造步骤。对于在建造者中有比较严格的顺序要求时，该类会有比较大的用处。在上述例子中，Director实现如下：
```py
class orderDirector():
    order_builder = ""
    
    def __init__(self,order_builder):
        self.order_builder=order_builder
        
    def createOrder(self,burger,snack,beverage):
        self.order_builder.addBurger(burger)
        self.order_builder.addSnack(snack)
        self.order_builder.addBeverage(beverage)
        return self.order_builder.build()

```
通过createOrder直接代入参数，即可直接生成订单。


### 三、建造者模式的优点和使用场景
优点：
- 1、封装性好，用户可以不知道对象的内部构造和细节，就可以直接建造对象；
- 2、系统扩展容易；
- 3、建造者模式易于使用，非常灵活。在构造性的场景中很容易实现“流水线”；
- 4、便于控制细节。
使用场景：
- 1、目标对象由组件构成的场景中，很适合建造者模式。例如，在一款赛车游戏中，车辆生成时，需要根据级别、环境等，选择轮胎、悬挂、骨架等部件，构造一辆“赛车”；
- 2、在具体的场景中，对象内部接口需要根据不同的参数而调用顺序有所不同时，可以使用建造者模式。例如：一个植物养殖器系统，对于某些不同的植物，浇水、施加肥料的顺序要求可能会不同，因而可以在Director中维护一个类似于队列的结构，在实例化时作为参数代入到具体建造者中。

### 四、建造者模式的缺点
1、“加工工艺”对用户不透明。（封装的两面性）

