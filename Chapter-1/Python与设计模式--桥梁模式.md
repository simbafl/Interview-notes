## Python与设计模式--桥梁模式

### 一、画笔与形状
在介绍原型模式的一节中，我们举了个图层的例子，这一小节内容，我们同样以类似画图的例子，说明一种结构类设计模式：桥梁模式。

在一个画图程序中，常会见到这样的情况：有一些预设的图形，如矩形、圆形等，还有一个对象-画笔，调节画笔的类型（如画笔还是画刷，还是毛笔效果等）并设定参数（如颜色、线宽等），选定图形，就可以在画布上画出想要的图形了。要实现以上需求，先从最抽象的元素开始设计，即形状和画笔（暂时忽略画布，同时忽略画笔参数，只考虑画笔类型）。
```py
class Shape:
    name = ""
    param = ""
    def __init__(self,*param):
        pass
        
    def getName(self):
        return self.name
        
    def getParam(self):
        return self.name, self.param

class Pen:
    shape = ""
    type = ""
    def __init__(self, shape):
        self.shape = shape
        
    def draw(self):
        pass
        
# 形状对象和画笔对象是最为抽象的形式。接下来，构造多个形状，如矩形和圆形：

class Rectangle(Shape):
    def __init__(self, long, width):
        self.name = "Rectangle"
        self.param = "Long:%s Width:%s" % (long, width)
        print "Create a rectangle:%s" % self.param
        
class Circle(Shape):
    def __init__(self,radius):
        self.name = "Circle"
        self.param = "Radius:%s" % radius
        print "Create a circle:%s" % self.param

# 紧接着是构造多种画笔，如普通画笔和画刷：

class NormalPen(Pen):
    def __init__(self, shape):
        Pen.__init__(self, shape)
        self.type = "Normal Line"
        
    def draw(self):
        print "DRAWING %s:%s----PARAMS:%s" % (self.type,self.shape.getName(), self.shape.getParam())
        
class BrushPen(Pen):
    def __init__(self,shape):
        Pen.__init__(self,shape)
        self.type="Brush Line"
    def draw(self):
        print "DRAWING %s:%s----PARAMS:%s" % (self.type,self.shape.getName(), self.shape.getParam())

# 业务中的逻辑如下：

if __name__=="__main__":
    normal_pen = NormalPen(Rectangle("20cm","10cm"))
    brush_pen = BrushPen(Circle("15cm"))
    normal_pen.draw()
    brush_pen.draw()

```
打印如下：
```
Create a rectangle:Long:20cm Width:10cm
Create a circle:Radius:15cm
DRAWING Normal Line:Rectangle----PARAMS:('Rectangle', 'Long:20cm Width10cm')
DRAWING Brush Line:Circle----PARAMS:('Circle', 'Radius:15cm')
```

### 二、桥梁模式
桥梁模式又叫桥接模式，定义如下：将抽象与实现解耦（注意此处的抽象和实现，并非抽象类和实现类的那种关系，而是一种角色的关系，这里需要好好区分一下），可以使其独立变化。

在形如上例中，Pen只负责画，但没有形状，它终究是不知道要画什么的，所以我们把它叫做抽象化角色；而Shape是具体的形状，我们把它叫做实现化角色。抽象化角色和实现化角色是解耦的，这也就意味着，所谓的桥，就是抽象化角色的抽象类和实现化角色的抽象类之间的引用关系。

### 三、桥梁模式的优点和应用场景
优点：
- 1、抽象角色与实现角色相分离，二者可以独立设计，不受约束；
- 2、扩展性强：抽象角色和实现角色可以非常灵活地扩展。

应用场景：
- 1、不适用继承或者原继承关系中抽象类可能频繁变动的情况，可以将原类进行拆分，拆成实现化角色和抽象化角色。例如本例中，若将形状、粗细、绘画样式等属于汇集在一个类中，一旦抽象类中有所变动，将造成巨大的风险；
- 2、重用性比较大的场景。比如开关控制逻辑的程序，开关就是抽象化角色，开关的形式有很多种，操作的实现化角色也有很多种，采用桥梁模式，（如当前例子）开关即可进行复用，整体会将设计的粒度减小。

### 四、桥梁模式的缺点
- 1、增加对系统理解的难度。

