### __radd_＿处理右侧加法
从严格意义上来讲，前边例子中出现的__add__方法并不支持＋运算符右侧使用实例对象。要实现这类表达式，而支持可互换的运算符，可以一并编写__radd__方法。＋右侧的对象是类实例，而左边对象不是类实例时，Python才会调用__radd__。所有情况下，则由左侧对象调用add方法。

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
 
class Commuter:
    def __init__(self, val):
        self.val = val
    def __add__(self, other):
        print('add', self.val, other)
    def __radd__(self, other):
        print("radd", self.val, other)
 
x = Commuter(88)
y = Commuter(99)
x + 1
1 + y
x + y
```
运行结果
```md
add 88 1
radd 99 1
add 88 <__main__.Commuter object at 0x00000000027F3E80>
```

注意：__rodd_＿中的顺序与之相反：self是每的右侧，other是在左侧。每个二元运算符都有类似的右侧重载方法（例如__mul__和__rmul__）。一般而言，像__radd_＿这类右侧方法只在需要时才进行转换，而且会返回＋来触发__add__（主逻辑写在这里）。此外，注意x和y是同一个类的实例。当不同类的实例混合出现在表达式时，Python优先选择左侧的那个类。

右侧方法是高级话题，在实际中很少使用。当你需要运算符具有互换性，而且只有当需要支持这类运算符时，才需要写这种方法。例如，v.ector类会需要这些工具，但Employee或Button类可能就不需要。