## Python与设计模式--适配器模式

### 一、外包人员系统兼容
假设某公司A与某公司B需要合作，公司A需要访问公司B的人员信息，但公司A与公司B协议接口不同，该如何处理？先将公司A和公司B针对各自的人员信息访问系统封装了对象接口。

```py
class ACpnStaff:
    name=""
    id=""
    phone=""
    def __init__(self,id):
        self.id=id
    def getName(self):
        print "A protocol getName method...id:%s"%self.id
        return self.name
    def setName(self,name):
        print "A protocol setName method...id:%s"%self.id
        self.name=name
    def getPhone(self):
        print "A protocol getPhone method...id:%s"%self.id
        return self.phone
    def setPhone(self,phone):
        print "A protocol setPhone method...id:%s"%self.id
        self.phone=phone

class BCpnStaff:
    name=""
    id=""
    telephone=""
    def __init__(self,id):
        self.id=id
    def get_name(self):
        print "B protocol get_name method...id:%s"%self.id
        return self.name
    def set_name(self,name):
        print "B protocol set_name method...id:%s"%self.id
        self.name=name
    def get_telephone(self):
        print "B protocol get_telephone method...id:%s"%self.id
        return self.telephone
    def set_telephone(self,telephone):
        print "B protocol get_name method...id:%s"%self.id
        self.telephone=telephone
# 为在A公司平台复用B公司接口，直接调用B公司人员接口是个办法，但会对现在业务流程造成不确定的风险。为减少耦合，规避风险，我们需要一个帮手，就像是转换电器电压的适配器一样，这个帮手就是协议和接口转换的适配器。适配器构造如下：

class CpnStaffAdapter:
    b_cpn=""
    def __init__(self,id):
        self.b_cpn=BCpnStaff(id)
    def getName(self):
        return self.b_cpn.get_name()
    def getPhone(self):
        return self.b_cpn.get_telephone()
    def setName(self,name):
        self.b_cpn.set_name(name)
    def setPhone(self,phone):
        self.b_cpn.set_telephone(phone)
# 适配器将B公司人员接口封装，而对外接口形式与A公司人员接口一致，达到用A公司人员接口访问B公司人员信息的效果。
# 业务示例如下：

if __name__=="__main__":
    acpn_staff=ACpnStaff("123")
    acpn_staff.setName("X-A")
    acpn_staff.setPhone("10012345678")
    print "A Staff Name:%s"%acpn_staff.getName()
    print "A Staff Phone:%s"%acpn_staff.getPhone()
    bcpn_staff=CpnStaffAdapter("456")
    bcpn_staff.setName("Y-B")
    bcpn_staff.setPhone("99987654321")
    print "B Staff Name:%s"%bcpn_staff.getName()
    print "B Staff Phone:%s"%bcpn_staff.getPhone()
```
打印如下：
```
A protocol setName method...id:123
A protocol setPhone method...id:123
A protocol getName method...id:123
A Staff Name:X-A
A protocol getPhone method...id:123
A Staff Phone:10012345678
B protocol set_name method...id:456
B protocol get_name method...id:456
B protocol get_name method...id:456
B Staff Name:Y-B
B protocol get_telephone method...id:456
B Staff Phone:99987654321
```
### 二、适配器模式
适配器模式定义如下：将一个类的接口变换成客户端期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。适配器模式和装饰模式有一定的相似性，都起包装的作用，但二者本质上又是不同的，装饰模式的结果，是给一个对象增加了一些额外的职责，而适配器模式，则是将另一个对象进行了“伪装”。
f1.png
适配器可以认为是对现在业务的补偿式应用，所以，尽量不要在设计阶段使用适配器模式，在两个系统需要兼容时可以考虑使用适配器模式。

### 三、适配器模式的优点和使用场景
优点：
1、适配器模式可以让两个接口不同，甚至关系不大的两个类一起运行；
2、提高了类的复用度，经过“伪装”的类，可以充当新的角色；
3、适配器可以灵活“拆卸”。
应用场景：
1、不修改现有接口，同时也要使该接口适用或兼容新场景业务中，适合使用适配器模式。例如，在一个嵌入式系统中，原本要将数据从Flash读入，现在需要将数据从磁盘读入，这种情况可以使用适配器模式，将从磁盘读入数据的接口进行“伪装”，以从Flash中读数据的接口形式，从磁盘读入数据。

### 四、适配器模式的缺点
1、适配器模式与原配接口相比，毕竟增加了一层调用关系，所以，在设计系统时，不要使用适配器模式。

