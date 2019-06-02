## Python与设计模式--原型模式

### 一、图层
大家如果用过类似于Photoshop的平面设计软件，一定都知道图层的概念。图层概念的提出，使得设计、图形修改等操作更加便利。设计师既可以修改和绘制当前图像对象，又可以保留其它图像对象，逻辑清晰，且可以及时得到反馈。本节内容，将以图层为主角，介绍原型模式。

首先，设计一个图层对象。
```py
class simpleLayer:
    background = [0,0,0,0]
    content = "blank"
    def getContent(self):
        return self.content

    def getBackgroud(self):
        return self.background

    def paint(self,painting):
        self.content = painting

    def setParent(self,p):
        self.background[3] = p

    def fillBackground(self,back):
        self.background = back

# 在实际的实现中，图层实现会很复杂，这里仅介绍相关的设计模式，做了比较大的抽象，用background表示背景的RGBA，简单用content表示内容，除了直接绘画，还可以设置透明度。
# 新建图层，填充蓝底并画一只狗，可以简单表示如下：

if  __name__ == "__main__":
    dog_layer = simpleLayer()
    dog_layer.paint("Dog")
    dog_layer.fillBackground([0,0,255,0])
    print "Background:",dog_layer.getBackgroud()
    print "Painting:",dog_layer.getContent()

```
打印如下：
```
Background: [0, 0, 255, 0]
Painting: Dog
```
接下来，如果需要再生成一个同样的图层，再填充同样的颜色，再画一只同样狗，该如何做呢？

还是按照新建图层、填充背景、画的顺序么？或许你已经发现了，这里可以用复制的方法来实现，而复制（clone）这个动作，就是原型模式的精髓了。

按照此思路，在图层类中新加入两个方法：clone和deep_clone
```py
from copy import copy, deepcopy
class simpleLayer:
    background = [0,0,0,0]
    content = "blank"
    
    def getContent(self):
        return self.content

    def getBackgroud(self):
        return self.background

    def paint(self,painting):
        self.content = painting

    def setParent(self,p):
        self.background[3] = p

    def fillBackground(self,back):
        self.background = back

    def clone(self):
        return copy(self)

    def deep_clone(self):
        return deepcopy(self)

if  __name__ == "__main__":
    dog_layer = simpleLayer()
    dog_layer.paint("Dog")
    dog_layer.fillBackground([0,0,255,0])
    print "Background:",dog_layer.getBackgroud()
    print "Painting:",dog_layer.getContent()
    another_dog_layer = dog_layer.clone()
    print "Background:", another_dog_layer.getBackgroud()
    print "Painting:", another_dog_layer.getContent()

```
打印结果如下：
```
Background: [0, 0, 255, 0]
Painting: Dog
Background: [0, 0, 255, 0]
Painting: Dog
```
clone和deep_clone有什么区别呢？大多数编程语言中，都会涉及到深拷贝和浅拷贝的问题。

一般来说，浅拷贝会拷贝对象内容及其内容的引用或者子对象的引用，但不会拷贝引用的内容和子对象本身；而深拷贝不仅拷贝了对象和内容的引用，也会拷贝引用的内容。

所以，一般深拷贝比浅拷贝复制得更加完全，但也更占资源（包括时间和空间资源）。

举个例子，下面的场景，可以说明深拷贝和浅拷贝的区别。
```py
if  __name__=="__main__":
    dog_layer = simpleLayer()
    dog_layer.paint("Dog")
    dog_layer.fillBackground([0,0,255,0])
    print "Original Background:",dog_layer.getBackgroud()
    print "Original Painting:",dog_layer.getContent()
    another_dog_layer = dog_layer.clone()
    another_dog_layer.setParent(128)
    another_dog_layer.paint("Puppy")
    print "Original Background:", dog_layer.getBackgroud()
    print "Original Painting:", dog_layer.getContent()
    print "Copy Background:", another_dog_layer.getBackgroud()
    print "Copy Painting:", another_dog_layer.getContent()
```
打印如下：
```
Original Background: [0, 0, 255, 0]
Original Painting: Dog
Original Background: [0, 0, 255, 128]
Original Painting: Dog
Copy Background: [0, 0, 255, 128]
Copy Painting: Puppy
```
浅拷贝后，直接对拷贝后引用（这里的数组）进行操作，原始对象中该引用的内容也会变动。

如果将`another_dog_layer = dog_layer.clone()`换成`another_dog_layer = dog_layer.deep_clone()`，即把浅拷贝换成深拷贝，其如果如下：
```
Original Background: [0, 0, 255, 0]
Original Painting: Dog
Original Background: [0, 0, 255, 0]
Original Painting: Dog
Copy Background: [0, 0, 255, 128]
Copy Painting: Puppy
```
深拷贝后，其对象内的引用内容也被进行了复制。

### 二、原型模式
原型模式定义如下：用原型实例指定创建对象的种类，并且通过复制这些原型创建新的对象。

需要注意一点的是，进行clone操作后，新对象的构造函数没有被二次执行，新对象的内容是从内存里直接拷贝的。


### 三、原型模式的优点和使用场景
优点：
- 1、性能极佳，直接拷贝比在内存里直接新建实例节省不少的资源；
- 2、简化对象创建，同时避免了构造函数的约束，不受构造函数的限制直接复制对象，是优点，也有隐患，这一点还是需要多留意一些。

使用场景：
- 1、对象在修改过后，需要复制多份的场景。如本例和其它一些涉及到复制、粘贴的场景；
- 2、需要优化资源的情况。如，需要在内存中创建非常多的实例，可以通过原型模式来减少资源消耗。此时，原型模式与工厂模式配合起来，不管在逻辑上还是结构上，都会达到不错的效果；
- 3、某些重复性的复杂工作不需要多次进行。如对于一个设备的访问权限，多个对象不用各申请一遍权限，由一个设备申请后，通过原型模式将权限交给可信赖的对象，既可以提升效率，又可以节约资源。

### 四、原型模式的缺点
- 1、深拷贝和浅拷贝的使用需要事先考虑周到；
- 2、某些编程语言中，拷贝会影响到静态变量和静态函数的使用。
