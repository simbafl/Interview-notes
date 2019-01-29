python已经实现了int方法，面试时让实现一下int函数遇到了尴尬， int函数看似简单，实际上自己来实现还是有一些坑的


#### 1.判断正负

这点很容易忘记

#### 2.python不能字符串减法

python 不能像 c++ 一样直接使用`s - '0'`直接实现个位数的字符串转整型，而是需要转换 ascii 码，`ord(s) - ord('0')`来实现转换

#### 3.判断是否超限

这也是手写int函数最容易忽略的问题，返回结果不能出 int 的限制， python 中 int 类型的最大值使用`sys.maxint`查看。但是 python 语言很神奇，实际上 python 内置的 int 方法并没有结果必须小于 maxint 的限制。

代码：
```python
#!/use/bin/env python
# _*_ coding:utf-8 _*_
import sys
max_int = sys.maxsize
num_tuple = ('0', '1', '2', '3', '4', '5', '6', '7', '8', '9')

def _int(input_string):
    total_num = 0
    is_minus = False
    string = input_string.strip()
    if string.startswith('-'):
        is_minus = True
        string = string[1:]
    for s in string:
        if s not in num_tuple:
            print("input error")
            return 0
            
        num = ord(s) - ord('0')
        total_num = total_num * 10 + num
        if total_num > max_int:
            total_num = max_int
            break
            
    return total_num * -1 if is_minus else total_num
```