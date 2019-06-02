## Python与设计模式--策略模式

### 一、客户消息通知
假设某司维护着一些客户资料，需要在该司有新产品上市或者举行新活动时通知客户。现通知客户的方式有两种：短信通知、邮件通知。应如何设计该系统的客户通知部分？

为解决该问题，我们先构造客户类，包括客户常用的联系方式和基本信息，同时也包括要发送的内容。
```py
class customer:
    customer_name = ""
    snd_way = ""
    info = ""
    phone = ""
    email = ""
    def setPhone(self, phone):
        self.phone = phone
        
    def setEmail(self, mail):
        self.email = mail
        
    def getPhone(self):
        return self.phone
        
    def getEmail(self):
        return self.email
        
    def setInfo(self, info):
        self.info = info
        
    def setName(self, name):
        self.customer_name = name
        
    def setBrdWay(self, snd_way):
        self.snd_way = snd_way
        
    def sndMsg(self):
        self.snd_way.send(self.info)
        
# snd_way向客户发送信息的方式，该方式置为可设，即可根据业务来进行策略的选择。
# 发送方式构建如下：

class msgSender:
    dst_code = ""
    def setCode(self, code):
        self.dst_code = code
        
    def send(self,info):
        pass
        
class emailSender(msgSender):
    def send(self,info):
        print "EMAIL_ADDRESS:%s EMAIL:%s" % (self.dst_code,info)
        
class textSender(msgSender):
    def send(self,info):
        print "TEXT_CODE:%s EMAIL:%s" % (self.dst_code,info)
# 在业务场景中将发送方式作为策略，根据需求进行发送。

if  __name__ == "__main__":
    customer_x = customer()
    customer_x.setName("CUSTOMER_X")
    customer_x.setPhone("10023456789")
    customer_x.setEmail("customer_x@xmail.com")
    customer_x.setInfo("Welcome to our new party!")
    text_sender = textSender()
    text_sender.setCode(customer_x.getPhone())
    customer_x.setBrdWay(text_sender)
    customer_x.sndMsg()
    mail_sender = emailSender()
    mail_sender.setCode(customer_x.getEmail())
    customer_x.setBrdWay(mail_sender)
    customer_x.sndMsg()

```
结果打印如下：
```
PHONE_NUMBER:10023456789 TEXT:Welcome to our new party!
EMAIL_ADDRESS:customer_x@xmail.com EMAIL:Welcome to our new party!
```

### 二、策略模式
策略模式定义如下：定义一组算法，将每个算法都封装起来，并使他们之间可互换。以上述例子为例，customer类扮演的角色（Context）直接依赖抽象策略的接口，在具体策略实现类中即可定义个性化的策略方式，且可以方便替换。

上一节中我们介绍了桥接模式，仔细比较一下桥接模式和策略模式，如果把策略模式的Context设计成抽象类和实现类的方式，那么策略模式和桥接模式就可以划等号了。

从类图看上去，桥接模式比策略模式多了对一种角色（抽象角色）的抽象。二者结构的高度同构，也只能让我们从使用意图上去区分两种模式：桥接模式解决抽象角色和实现角色都可以扩展的问题；而策略模式解决算法切换和扩展的问题。

### 三、策略模式的优点和应用场景

优点：
- 1、各个策略可以自由切换：这也是依赖抽象类设计接口的好处之一；
- 2、减少代码冗余；
- 3、扩展性优秀，移植方便，使用灵活。

应用场景：
- 1、算法策略比较经常地需要被替换时，可以使用策略模式。如现在超市前台，会常遇到刷卡、某宝支付、某信支付等方式，就可以参考策略模式。

### 四、策略模式的缺点
- 1、项目比较庞大时，策略可能比较多，不便于维护；
- 2、策略的使用方必须知道有哪些策略，才能决定使用哪一个策略，这与迪米特法则是相违背的。


