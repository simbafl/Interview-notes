## Python与设计模式--享元模式

### 一、网上咖啡选购平台
假设有一个网上咖啡选购平台，客户可以在该平台上下订单订购咖啡，平台会根据用户位置进行线下配送。假设其咖啡对象构造如下：
```py
class Coffee:
    name = ''
    price = 0
    def __init__(self, name):
        self.name = name
        self.price = len(name)  # 在实际业务中，咖啡价格应该是由配置表进行配置，或者调用接口获取等方式得到，此处为说明享元模式，将咖啡价格定为名称长度，只是一种简化
    def show(self):
        print "Coffee Name:%s Price:%s" % (self.name, self.price)
        
# 其对应的顾客类如下：
class Customer:
    name = ""
    def __init__(self, name):
        self.name=name
        
    def order(self, coffee_name):
        print "%s ordered a cup of coffee:%s" % (self.name, coffee_name)
        return Coffee(coffee_name)

```
按照一般的处理流程，用户在网上预订咖啡，其代表用户的Customer类中生成一个Coffee类，直到交易流程结束。整个流程是没有问题的。

如果，随着网站用户越来越多，单位时间内购买咖啡的用户也越来越多，并发量越来越大，对系统资源的消耗也会越来越大，极端情况下，会造成宕机等严重后果。此时，高效利用资源，就显得非常重要了。

简单分析下业务流程，高并发下用户数量增加，而该模型下，每个用户点一杯咖啡，就会产生一个咖啡实例，如果一种咖啡在该时间内被很多用户点过，那么就会产生很多同样咖啡的实例。避免重复实例的出现，是节约系统资源的一个突破口。类似于单例模式，我们这里在咖啡实例化前，增加一个控制实例化的类：咖啡工厂。

```py
class CoffeeFactory():
    coffee_dict = {}
    
    def getCoffee(self, name):
        if self.coffee_dict.has_key(name) == False:
            self.coffee_dict[name] = Coffee(name)
        return self.coffee_dict[name]
        
    def getCoffeeCount(self):
        return len(self.coffee_dict)
        
# 咖啡工厂中，getCoffeeCount直接返回当前实例个数。Customer类可以重写下，如下：

class Customer:
    coffee_factory = ""
    name = ""
    
    def __init__(self, name, coffee_factory):
        self.name = name
        self.coffee_factory = coffee_factory
        
    def order(self, coffee_name):
        print "%s ordered a cup of coffee:%s" % (self.name, coffee_name)
        return self.coffee_factory.getCoffee(coffee_name)
        
# 假设业务中短时间内有多人订了咖啡，业务模拟如下：

if __name__ == "__main__":
    coffee_factory = CoffeeFactory()
    customer_1 = Customer("A Client", coffee_factory)
    customer_2 = Customer("B Client", coffee_factory)
    customer_3 = Customer("C Client", coffee_factory)
    c1_capp = customer_1.order("cappuccino")
    c1_capp.show()
    c2_mocha = customer_2.order("mocha")
    c2_mocha.show()
    c3_capp = customer_3.order("cappuccino")
    c3_capp.show()
    print "Num of Coffee Instance:%s" % coffee_factory.getCoffeeCount()

```
打印如下：
```
A Client ordered a cup of coffee:cappuccino
Coffee Name:cappuccino Price:10
B Client ordered a cup of coffee:mocha
Coffee Name:mocha Price:5
C Client ordered a cup of coffee:cappuccino
Coffee Name:cappuccino Price:10
Num of Coffee Instance:2
根据结果可以得知，该模式下三个用户点了两种咖啡，最终的咖啡实例为2，而不是3。
```

### 二、享元模式
享元模式定义如下：使用共享对象支持大量细粒度对象。大量细粒度的对象的支持共享，可能会涉及这些对象的两类信息：内部状态信息和外部状态信息。

- 内部状态信息就是可共享出来的信息，它们存储在享元对象内部，不会随着特定环境的改变而改变；
- 外部状态信息就不可共享的信息了。享元模式中只包含内部状态信息，而不应该包含外部状态信息。这点在设计业务架构时，应该有所考虑。

### 三、享元模式的优点和使用场景
优点：
- 1、减少重复对象，大大节约了系统资源。

使用场景：
- 1、系统中存在大量的相似对象时，可以选择享元模式提高资源利用率。咖啡订购平台比较小，若假设一个电商平台，每个买家和卖家建立起买卖关系后，买家对象和卖家对象都是占用资源的。如果一个卖家同时与多个买家建立起买卖关系呢？此时享元模式的优势就体现出来了；
- 2、需要缓冲池的场景中，可以使用享元模式。如进程池，线程池等技术，就可以使用享元模式（事实上，很多的池技术中已经使得了享元模式）。

### 四、享元模式的缺点
- 1、享元模式虽然节约了系统资源，但同时也提高了系统的复杂性，尤其当遇到外部状态和内部状态混在一起时，需要先将其进行分离，才可以使用享元模式。否则，会引起逻辑混乱或业务风险；
- 2、享元模式中需要额外注意线程安全问题。

