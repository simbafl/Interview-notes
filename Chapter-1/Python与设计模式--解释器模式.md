## Python与设计模式--解释器模式

### 一、模拟吉他
要开发一个自动识别谱子的吉他模拟器，达到录入谱即可按照谱发声的效果。除了发声设备外（假设已完成），最重要的就是读谱和译谱能力了。

分析其需求，整个过程大致上分可以分为两部分：根据规则翻译谱的内容；根据翻译的内容演奏。我们用一个解释器模型来完成这个功能。
```py
class PlayContext():
    play_text = None

class Expression():
    def interpret(self, context):
        if len(context.play_text) == 0:
            return
        else:
            play_segs = context.play_text.split(" ")
            for play_seg in play_segs:
                pos = 0
                for ele in play_seg:
                    if ele.isalpha():
                        pos += 1
                        continue
                    break
                play_chord = play_seg[0:pos]
                play_value = play_seg[pos:]
                self.execute(play_chord,play_value)
    def execute(self,play_key,play_value):
        pass

class NormGuitar(Expression):
    def execute(self, key, value):
        print "Normal Guitar Playing--Chord:%s Play Tune:%s" % (key,value)

"""
 PlayContext类为谱的内容，这里仅含一个字段，没有方法。Expression即表达式，里面仅含两个方法，interpret负责转译谱，execute则负责演奏；NormGuitar类覆写execute，以吉他 的方式演奏。
"""
# 业务场景如下：

if __name__=="__main__":
    context = PlayContext()
    context.play_text = "C53231323 Em43231323 F43231323 G63231323"
    guitar=NormGuitar()
    guitar.interpret(context)

```
打印如下：
```
Normal Guitar Playing--Chord:C Play Tune:53231323
Normal Guitar Playing--Chord:Em Play Tune:43231323
Normal Guitar Playing--Chord:F Play Tune:43231323
Normal Guitar Playing--Chord:G Play Tune:63231323
```

### 二、解释器模式
解释器模式定义如下：给定一种语言，定义它的文法表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。典型的解释器模式中会有终结符和非终结符之说，语法也根据两种终结符，决定语句最终含义。上例中，非终结符就是空格，终结符就是整个句尾。

### 三、解释器模式的优点和应用场景
优点：
- 1、在语法分析的场景中，具有比较好的扩展性。规则修改和制订比较灵活。

应用场景：
- 1、若一个问题重复发生，可以考虑使用解释器模式。这点在数据处理和日志处理过程中使用较多，当数据的需求方需要将数据纳为己用时，必须将数据“翻译”成本系统的数据规格；同样的道理，日志分析平台也需要根据不同的日志格式翻译成统一的“语言”。
- 2、特定语法解释器。如各种解释型语言的解释器，再比如自然语言中基于语法的文本分析等。

### 四、解释器模式的缺点
- 1、解释规则多样化会导致解释器的爆炸；
- 2、解释器目标比较单一，行为模式比较固定，因而重要的模块中尽量不要使用解释器模式。


