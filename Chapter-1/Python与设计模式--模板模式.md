## Python与设计模式--模板模式

### 一、股票查询客户端
投资股票是种常见的理财方式，我国股民越来越多，实时查询股票的需求也越来越大。今天，我们通过一个简单的股票查询客户端来认识一种简单的设计模式：模板模式。

根据股票代码来查询股价分为如下几个步骤：登录、设置股票代码、查询、展示。构造如下的虚拟股票查询器：
```py
class StockQueryDevice():
    stock_code = "0"
    stock_price = 0.0
    def login(self,usr,pwd):
        pass
        
    def setCode(self,code):
        self.stock_code = code
        
    def queryPrice(self):
        pass
        
    def showPrice(self):
        pass

"""        
 现在查询机构很多，我们可以根据不同的查询机构和查询方式，来通过继承的方式实现其对应的股票查询器类。
 例如，WebA和WebB的查询器类可以构造如下：
"""

class WebAStockQueryDevice(StockQueryDevice):
    def login(self,usr,pwd):
        if usr == "myStockA" and pwd == "myPwdA":
            print "Web A:Login OK... user:%s pwd:%s" % (usr,pwd)
            return True
        else:
            print "Web A:Login ERROR... user:%s pwd:%s" % (usr,pwd)
            return False
            
    def queryPrice(self):
        print "Web A Querying...code:%s " % self.stock_code
        self.stock_price = 20.00
        
    def showPrice(self):
        print "Web A Stock Price...code:%s price:%s" % (self.stock_code,self.stock_price)
        
class WebBStockQueryDevice(StockQueryDevice):
    def login(self,usr,pwd):
        if usr == "myStockB" and pwd == "myPwdB":
            print "Web B:Login OK... user:%s pwd:%s" % (usr, pwd)
            return True
        else:
            print "Web B:Login ERROR... user:%s pwd:%s"%(usr, pwd)
            return False
    def queryPrice(self):
        print "Web B Querying...code:%s "%self.stock_code
        self.stock_price = 30.00
        
    def showPrice(self):
        print "Web B Stock Price...code:%s price:%s"%(self.stock_code,self.stock_price)
        
# 在场景中，想要在网站A上查询股票，需要进行如下操作：

if  __name__=="__main__":
    web_a_query_dev = WebAStockQueryDevice()
    web_a_query_dev.login("myStockA","myPwdA")
    web_a_query_dev.setCode("12345")
    web_a_query_dev.queryPrice()
    web_a_query_dev.showPrice()

```
打印结果如下：
```
Web A:Login OK... user:myStockA pwd:myPwdA
Web A Querying...code:12345 
Web A Stock Price...code:12345 price:20.0
```
每次操作，都会调用登录，设置代码，查询，展示这几步，是不是有些繁琐？既然有些繁琐，何不将这几步过程封装成一个接口。

由于各个子类中的操作过程基本满足这个流程，所以这个方法可以写在父类中：
```py
class StockQueryDevice():
    stock_code = "0"
    stock_price = 0.0
    def login(self,usr,pwd):
        pass
        
    def setCode(self,code):
        self.stock_code = code
        
    def queryPrice(self):
        pass
        
    def showPrice(self):
        pass
        
    def operateQuery(self, usr, pwd, code):
        self.login(usr, pwd)
        self.setCode(code)
        self.queryPrice()
        self.showPrice()
        return True

# 这样，在业务场景中，就可以通过operateQuery一气呵成了。

if  __name__ == "__main__":
    web_a_query_dev = WebAStockQueryDevice()
    web_a_query_dev.operateQuery("myStockA", "myPwdA", "12345")
```
这种基本每个程序员都会想到的解决方案，就是模板模式。很简单吧。

但也许你会问，登录并不一定每次都会成功呀？是的，所以在operateQuery接口中需要做一重判断，写成：
```py
def operateQuery(self, usr, pwd, code):
    if not self.login(usr, pwd):
        return False
        
    self.setCode(code)
    self.queryPrice()
    self.showPrice()
    return True

```
在模板模式中，像这样类似于login等根据特定情况，定制某些特定动作的函数，被称作钩子函数。此例中，如果登录失败（user：myStock B，pwd:myPwdA），会打印如下结果：
```
Web A:Login ERROR... user:myStockB pwd:myPwdA
```
### 二、模板模式
模板模式定义如下：定义一个操作中的算法的框架，而将一些步骤延迟到子类中，使得子类可以不改变一个算法的结构即可重新定义该算法的某些特定的步骤。

子类实现的具体方法叫作基本方法，实现对基本方法高度的框架方法，叫作模板方法。

### 三、模板模式的优点和应用
优点：
- 1、可变的部分可以充分扩展，不变的步骤可以充分封装；
- 2、提取公共代码，减少冗余代码，便于维护；
- 3、具体过程可以定制，总体流程方便掌控。

使用场景：
- 1、某超类的子类中有公有的方法，并且逻辑基本相同，可以使用模板模式。必要时可以使用钩子方法约束其行为。具体如本节例子；
- 2、比较复杂的算法，可以把核心算法提取出来，周边功能在子类中实现。例如，机器学习中的监督学习算法有很多，如决策树、KNN、SVM等，但机器学习的流程大致相同，都包含输入样本、拟合（fit）、预测等过程，这样就可以把这些过程提取出来，构造模板方法，并通过钩子方法控制流程。

### 四、模板模式的缺点
- 1、模板模式在抽象类中定义了子类的方法，即子类对父类产生了影响，部分影响了代码的可读性。


