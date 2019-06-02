## Python与设计模式--访问者模式

### 一、药房业务系统
假设一个药房，有一些大夫，一个药品划价员和一个药房管理员，它们通过一个药房管理系统组织工作流程。大夫开出药方后，药品划价员确定药品是否正常，价格是否正确；通过后药房管理员进行开药处理。该系统可以如何实现？最简单的想法，是分别用一个一个if…else…把划价员处理流程和药房管理流程实现，这样做的问题在于，扩展性不强，而且单一性不强，一旦有新药的加入或者划价流程、开药流程有些变动，会牵扯比较多的改动。今天介绍一种解决这类问题的模式：访问者模式。
首先，构造药品类和工作人员类：
```py
class Medicine:
    name=""
    price=0.0
    def __init__(self,name,price):
        self.name=name
        self.price=price
    def getName(self):
        return self.name
    def setName(self,name):
        self.name=name
    def getPrice(self):
        return self.price
    def setPrice(self,price):
        self.price=price
    def accept(self,visitor):
        pass
class Antibiotic(Medicine):
    def accept(self,visitor):
        visitor.visit(self)
class Coldrex(Medicine):
    def accept(self,visitor):
        visitor.visit(self)
# 药品类中有两个子类，抗生素和感冒药；

class Visitor:
    name=""
    def setName(self,name):
        self.name=name
    def visit(self,medicine):
        pass
class Charger(Visitor):
    def visit(self,medicine):
        print "CHARGE: %s lists the Medicine %s. Price:%s " % (self.name,medicine.getName(),medicine.getPrice())
class Pharmacy(Visitor):
    def visit(self,medicine):
        print "PHARMACY:%s offers the Medicine %s. Price:%s" % (self.name,medicine.getName(),medicine.getPrice())
# 工作人员分为划价员和药房管理员。
# 在药品类中，有一个accept方法，其参数是个visitor；而工作人员就是从Visitor类中继承而来的，也就是说，他们就是Visitor，都包含一个visit方法，其参数又恰是medicine。药品作为处理元素，依次允许（Accept）Visitor对其进行操作，这就好比是一条流水线上的一个个工人，对产品进行一次次的加工。整个业务流程还差一步，即药方类的构建（流水线大机器）。

class ObjectStructure:
    pass
class Prescription(ObjectStructure):
    medicines=[]
    def addMedicine(self,medicine):
        self.medicines.append(medicine)
    def rmvMedicine(self,medicine):
        self.medicines.append(medicine)
    def visit(self,visitor):
        for medc in self.medicines:
            medc.accept(visitor)

# 药方类将待处理药品进行整理，并组织Visitor依次处理。
# 业务代码如下：

if __name__=="__main__":
    yinqiao_pill=Coldrex("Yinqiao Pill",2.0)
    penicillin=Antibiotic("Penicillin",3.0)
    doctor_prsrp=Prescription()
    doctor_prsrp.addMedicine(yinqiao_pill)
    doctor_prsrp.addMedicine(penicillin)
    charger=Charger()
    charger.setName("Doctor Strange")
    pharmacy=Pharmacy()
    pharmacy.setName("Doctor Wei")
    doctor_prsrp.visit(charger)
    doctor_prsrp.visit(pharmacy)

```
打印如下：
```
CHARGE: Doctor Strange lists the Medicine Yinqiao Pill. Price:2.0
CHARGE: Doctor Strange lists the Medicine Penicillin. Price:3.0
PHARMACY:Doctor Wei offers the Medicine Yinqiao Pill. Price:2.0
PHARMACY:Doctor Wei offers the Medicine Penicillin. Price:3.0
```
### 二、访问者模式
访问者模式的定义如下：封装一些作用于某种数据结构中的各元素的操作，它可以在不改变数据结构的前提下定义于作用于这些元素的新操作。

提到访问者模式，就不得不提一下双分派。分派分为静态分派和动态分派。首先解释下静态分派，静态分派即根据请求者的名称和接收到的参数，决定多态时处理的操作。比如在Java或者C++中，定义名称相同但参数不同的函数时，会根据最终输入的参数来决定调用哪个函数。双分派顾名思义，即最终的操作决定于两个接收者的类型，在本例中，药品和工作人员互相调用了对方（药品的accept和工作人员的visit中，对方都是参数），就是双分派的一种应用。
那么Python支持静态分派么？先看下面的一个例子。
```py
def max_num(x,y,z):
    return max(max(x,y),z)
def max_num(x,y):
    return max(x,y)
if __name__=="__main__":
    print max_num(1,2,4)
```
打印如下：
```
Traceback (most recent call last):
File "D:/WorkSpace/Project/PyDesignMode/example.py", line 786, in

print max_num(1,2,4)
TypeError: max_num() takes exactly 2 arguments (3 given)
```
可见，Python原生是不支持静态分派的，因而也不直接支持更高层次的分派。访问者模式实现的分派，是一种动态双分派。但这并不妨碍Python通过访问者模式实现一种基于类的“双分派效果”。Python多分派可以参考David Mertz 博士的一篇文章：可爱的Python：多分派—用多元法泛化多样性。

### 三、访问者模式的优点和应用场景
优点：
1、将不同的职责非常明确地分离开来，符合单一职责原则；
2、职责的分开也直接导致扩展非常优良，灵活性非常高，加减元素和访问者都非常容易。
应用场景：
1、要遍历不同的对象，根据对象进行不同的操作的场景；或者一个对象被多个不同对象顺次处理的情况，可以考虑使用访问者模式。除本例外，报表生成器也可以使用访问者模式实现，报表的数据源由多个不同的对象提供，每个对象都是Visitor，报表这个Element顺次Accept各访问者完善并生成对象。

### 四、访问者模式的缺点
1、访问者得知了元素细节，与最小隔离原则相悖；
2、元素变更依旧可能引起Visitor的修改。


