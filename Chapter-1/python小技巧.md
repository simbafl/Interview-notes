### python 常用小技巧
```py
1. python排序
lst.sort()
lsts = sorted(lst)
# 第二个会创建新的list，成本高。第一个是in-place，改变原list，效率高。
```
```py
2. xrange和range
# python2中range会产生新的list存于memory中，xrange更像生成器。
# python3中的range取代了xrange的功能。
```
```py
3. python处理矩阵
row = len(matrix)
col = len(matrix[0]) if row else 0
# 这样写的原因是，当matrix=[], row=0, col=0。
```
```py
4. python列表生成式
lst = [0 for i in range(3)] # [0, 0, 0]
lst = [[0 for i in range(3)] for j in range(2)] # [[0,0,0], [0,0,0]]
# 下面写法很危险
lst1 = [0, 0, 0]
lst2 = [lst1] * 2
lst2[0][0] = 1  # lst2=[[1, 0, 0], [1, 0, 0]]
# 因为此时lst2是object，改一个相当于全改
```
```py
5. 字典取值
D = {}
if i in D:
    val = D[i]
else:
    val = 0

# 等同于
val = D.get(i, 0) 
```
```py
6. 字符串反转和列表反转
# 字符串没有reverse，只能str[::-1]
# 对于列表反转，可以有三种写法
tmp=[i for i in range(100000)]
import datetime
start = datetime.datetime.now()
for i in range(100000):
    tmp = tmp[::-1]
end = datetime.datetime.now()
s1 = end-start

start = datetime.datetime.now()
for i in range(100000):
    tmp = list(reverse(tmp))
end = datetime.datetime.now()
s2 = end-start

start = datetime.datetime.now()
for i in range(100000):
    tmp = tmp.reverse()
end = datetime.datetime.now()
s3 = end-start
print(s1, s2, s3)

>>>
0:00:32.784672
0:01:44.324500
0:00:04.982143

# 说明in-place反映最快，但是这样改变了原list，不改变原list情况下，用分片最好。
```
```py
7. 快速统计
import collections
lst = [1,1,3,2,4,2,1]
collections.Counter(lst)  # Counter({1:3, 2:2, 3:1})
[(k, v) for k,v in l.items()]  # [(1, 3), (2, 2), (3, 1)]
```
```py
8. python自带堆heapq，默认min heapq

heapq.heappush(heap, item)  # 把item加入堆中
heapq.heappop(heap)  # 把堆顶元素弹出
heapq.heappushpop(heap, item)  # 比先heappush再heapqpop要快
heapq.heapreplace(heap, item)  # 比先heappop再heappush要快
heapq.heapify(x)  # 将列表堆调整
heapq.merge(*iterables)  # 将多个列表合并，并进行堆调整，返回的是合并后的迭代器
heapq.nlargest(n, iterable, key=None)  # Top-K问题
heapq.nsmallest(n, iterable, key=None) 
```
```py
9. 双端队列deque
# 方便实现popleft(), popright, appendleft(), appendright(), 复杂度都是O(1), 用list模拟队列时间复杂度是O(n)
# 看到网上有人弄了个挺有意思的跑马灯程序
import sys
import time
from collections improt deque

fancy_loading = deque('>----------')
while True:
    print('\r%s' % ''.join(fancy_loading))
    fancy_loading.rotate(1)
    sys.stdout.flush()
    time.sleep(1)
```
```py 
10. 位运算判断奇偶
# 判断奇偶除了用除法，还可以用二进制位运算
# 偶数在二进制里面，最后一位是0，奇数是1
if num & 1 == 0:
    print('偶')
else:
    print('奇')
```
```py
11. 双层for循环的比较
# 正常情况下，次数多的循环放在内层，减少cache的更新数据的次数。
a = [[0] * 10000 for i in range(10000)]
for i in range(10000):
    for j in range(10000):
        a[i][j] = 1
        
for i in range(10000):
    for j in range(10000):
        a[j][i] = 1
        
事实证明第一种更快。
因为cpu从内存中获取数据放到cache时，是一块一块获取，如果chche效率高，那么chche利用率就高。因为第一个是对数组顺序访问，换页次数少，时间开销更小。
```