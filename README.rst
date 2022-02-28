# 1. owlready2支持中文：
CLASS支持中文
reasoning.py line:148 ==>output = output.decode("gbk").replace("\r","")

SWRL规则支持中文
rule.py

lg.add("VAR", r"\?[a-zA-Z0-9_\u4e00-\u9fa5]+")

lg.add("NAME", r'[a-zA-Z\u4e00-\u9fa5][a-zA-Z0-9\u4e00-\u9fa5_:/.#]*')

# 2. 静态定义本体结构Class 与 Relation

```python
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
