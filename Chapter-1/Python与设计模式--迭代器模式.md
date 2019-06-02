## Python与设计模式--迭代器模式

一、迭代器与生成器
今天的主角是迭代器模式。在python中，迭代器并不用举太多的例子，因为python中的迭代器应用实在太多了（不管是python还是其它很多的编程语言中，实际上迭代器都已经纳入到了常用的库或者包中）。而且在当前，也几乎没有人专门去开发一个迭代器，而是直接去使用list、string、set、dict等python可迭代对象，或者直接使用__iter__和next函数来实现迭代器。如下例：
```py
if __name__=="__main__":
    lst=["hello Alice","hello Bob","hello Eve"]
    lst_iter=iter(lst)
    print lst_iter
    print lst_iter.next()
    print lst_iter.next()
    print lst_iter.next()
    print lst_iter.next()

```
打印如下：
```
hello Alice
hello Bob
hello Eve
Traceback (most recent call last):
File "D:/WorkSpace/Project/PyDesignMode/example.py", line 719, in 
print lst_iter.next()
StopIteration
```
在这种迭代器的使用过程中，如果next超过了迭代范围，会抛出异常。
在python对象的方法中，也可以轻易使用迭代器模式构造可迭代对象，如下例：
```py
class MyIter(object):
    def __init__(self, n):
        self.index = 0
        self.n = n
    def __iter__(self):
        return self
    def next(self):
        if self.index < self.n:
            value = self.index**2
            self.index += 1
            return value
        else:
            raise StopIteration()
# __iter__和next实现了迭代器最基本的方法。如下方式进行调用：

if __name__=="__main__":
    x_square=MyIter(10)
    for x in x_square:
        print x

```
打印如下：
```
0
1
4
9
16
25
36
49
64
81
```
注意__iter__方法中的返回值，由于直接返回了self，因而该迭代器是无法重复迭代的，如以下业务场景：
```py
if __name__=="__main__":
    x_square=MyIter(10)
    for x in x_square:
        print x
    for x in x_square:
        print x
# 只能打印一遍平方值。解决办法是，在__iter__中不返回实例，而再返回一个对象，写成：

def __iter__(self):
    return MyIter(self.n)
```
这样，在每次迭代时都可以将迭代器“初始化”，就可以多次迭代了。
另外，在python中，使用生成器可以很方便的支持迭代器协议。生成器通过生成器函数产生，生成器函数可以通过常规的def语句来定义，但是不用return返回，而是用yield一次返回一个结果，在每个结果之间挂起和继续它们的状态，来自动实现迭代协议。
如下例：
```py
def MyGenerater(n):
    index=0
    while index<n:
        yield index**2
        index+=1

# 注意，这是个函数。在每次调用生成器，得到返回结果后，现场得以保留，下次再调用该生 成器时，返回保留的现场从yield后继续执行程序。

if __name__=="__main__":
    x_square=MyGenerater(10)
    for x in x_square:
        print x
```
打印结果与上面一致。

### 二、迭代器模式
迭代器模式的定义如下：它提供一种方法，访问一个容器对象中各个元素，而又不需要暴露对象的内部细节。

