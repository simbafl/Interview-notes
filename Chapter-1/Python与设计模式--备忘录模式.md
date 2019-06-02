## Python与设计模式--备忘录模式

### 一、游戏进度保存
打过游戏的朋友一定知道，大多数游戏都有保存进度的功能，如果一局游戏下来，忘保存了进度，那么下次只能从上次进度点开始重新打了。一般情况下，保存进度是要存在可持久化存储器上，本例中先以保存在内存中来模拟实现该场景的情形。

以模拟一个战斗角色为例。首先，创建游戏角色。
```py
class GameCharacter():
    vitality = 0
    attack = 0
    defense = 0
    def displayState(self):
        print 'Current Values:'
        print 'Life:%d' % self.vitality
        print 'Attack:%d' % self.attack
        print 'Defence:%d' % self.defense
        
    def initState(self,vitality,attack,defense):
        self.vitality = vitality
        self.attack = attack
        self.defense = defense
        
    def saveState(self):
        return Memento(self.vitality, self.attack, self.defense)
        
    def recoverState(self, memento):
        self.vitality = memento.vitality
        self.attack = memento.attack
        self.defense = memento.defense
        
class FightCharactor(GameCharacter):
    def fight(self):
        self.vitality -= random.randint(1, 10)

"""        
GameCharacter定义了基本的生命值、攻击值、防御值以及实现角色状态控制的方法，FightCharactor实现具体的“战斗”接口。为实现保存进度的细节，还需要一个备忘录，来保存进度。
"""

class Memento:
    vitality = 0
    attack = 0
    defense = 0
    def __init__(self, vitality, attack, defense):
        self.vitality = vitality
        self.attack = attack
        self.defense = defense
        
#万事俱备，在业务逻辑中可以进行类的调度了。
if __name__ == "__main__":
    game_chrctr = FightCharactor()
    game_chrctr.initState(100,79,60)
    game_chrctr.displayState()
    memento = game_chrctr.saveState()
    game_chrctr.fight()
    game_chrctr.displayState()
    game_chrctr.recoverState(memento)
    game_chrctr.displayState()

```
打印如下：
```
Current Values:
Life:100
Attack:79
Defence:60
Current Values:
Life:91
Attack:79
Defence:60
Current Values:
Life:100
Attack:79
Defence:60
```
由生命值变化可知，先保存状态值，经过一轮打斗后，生命值由100变为91，而后恢复状态值，生命值又恢复成100。

### 二、备忘录模式
备忘录模式定义如下：在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可以将该对象恢复到原来保存的状态。在备忘录模式中，如果要保存的状态多，可以创造一个备忘录管理者角色来管理备忘录。

### 三、备忘录模式应用场景
- 1、需要保存和恢复数据的相关状态场景。如保存游戏状态的场景；撤销场景，如Ctrl-Z操作；事务回滚的应用。一般情况下事务回滚有两种方式： 一是把从恢复点开始的操作都反向执行一遍； 二是直接恢复到恢复点的各种状态。两种方式各有优缺点，要结合业务场景，决定使用哪种模式；
- 2、副本监控场景。备忘录可以当作一个临时的副本监控，实现非实时和准实时的监控。

