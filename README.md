# 1. owlready2支持中文：
* CLASS支持中文
reasoning.py 修改

```
def _decode(s):
  if not s: return ""
  try:
    return s.decode("gbk")
  except UnicodeDecodeError:
    return s.decode("latin")
```
* SWRL规则支持中文
rule.py 修改

```
def _create_rule_parser():
  global _RULE_PARSER
  import owlready2.rply as rply
  
  lg = rply.LexerGenerator()
  lg.add("(", r"\(")
  lg.add(")", r"\)")
  lg.add(",", r",")
  lg.add(",", r"\^")
  lg.add("IMP", r"->")
  lg.add("FLOAT", r"-[0-9]*\.[0-9]+")
  lg.add("FLOAT", r"[0-9]*\.[0-9]+")
  lg.add("INT", r"-[0-9]+")
  lg.add("INT", r"[0-9]+")
  lg.add("STR", r'".*?"')
  lg.add("STR", r"'.*?'")
  lg.add("BOOL", r"true")
  lg.add("BOOL", r"false")
  lg.add("VAR", r"\?[a-zA-Z0-9_\u4e00-\u9fa5]+")
  lg.add("NAME", r'[a-zA-Z\u4e00-\u9fa5][a-zA-Z0-9\u4e00-\u9fa5_:/.#]*')
  
  lg.ignore(r"\s+")

```
# 2. 静态定义本体结构Class 与 Relation

```
#-*- coding: utf-8 -*-
from owlready2 import *
import types
PATH = "."#本地路径
IRI  = "https://x.owl"#iri
onto_path.append(PATH)
onto = get_ontology(IRI)
with onto:
    class 人(Thing):
        pass
    class 运动员(人):
        pass
    class 足球运动员(运动员):
        pass
    class 篮球运动员(运动员):
        pass
    class 羽毛球运动员(运动员):
        pass
    class 裁判(人):
        pass
    class 教练(人):
        pass
    class 球(Thing):
        pass
    class 羽毛球(球):
        pass
    class 足球(球):
        pass
    class 篮球(球):
        pass
    class 球队(Thing):
        pass
    class 足球队(球队):
        pass
    class 篮球队(球队):
        pass
    class 效力于(ObjectProperty):
        domain    = [运动员]
        range     = [球队]
    class 执教(ObjectProperty):
        domain    = [教练]
        range     = [球队]   
    class 战力强于(ObjectProperty,TransitiveProperty):
        domain    = [球队]
        range     = [球队]  
    class 水平高于(ObjectProperty,TransitiveProperty):
        domain    = [教练]
        range      = [教练] 
    onto.save()
```
# 3. 动态创建Class与SWRL规则
```
# -*- coding: utf-8 -*-
from owlready2 import *
import types
PATH = "."#本地路径
IRI  = "https://x.owl"#iri
onto_path.append(PATH)
onto = get_ontology(IRI).load()

with onto:
        types.new_class('橄榄球队', (onto.球队,))
        '''
        #可添加等于
        eq='[onto.球队 & ... ]]'
        glqd.equivalent_to=eval(eq)
        '''        
        swrl='球队(?x),教练(?a),执教(?a,?x),球队(?y),教练(?b),执教(?b,?y),战力强于(?x,?y) -> 水平高于(?a,?yb)'
        rule =  Imp()
        rule.set_as_rule(swrl)        
        onto.save()
```
# 4. 特殊符号owlready2不支持
个别特殊符号定义成类名、实例名会报错，需要去除特殊符号，如： ‘-’‘/’等
```
from zhon.hanzi import punctuation
def conv(source):
    if source == None:
        return None
    source = source.replace(' ','')
    source = source.replace('Ⅲ','3')
    source = source.replace('Ⅱ','2')
    source = source.replace('Ⅰ','1')
    
    _pun = punctuation + '\-\&\,\.\?\/\%\+'
    target = re.sub('[{}]+'.format(_pun), "", source)
    return target
```


