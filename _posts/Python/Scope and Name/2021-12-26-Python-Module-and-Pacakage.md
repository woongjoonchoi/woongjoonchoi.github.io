---
title : "Module and Package"



excerpt: "Module and Package"

categories:
  - Python
tags:
  - [Python,Module,Package]
# classes : wide
toc: true
toc_sticky: true
---
## Python Module and Pacakage

BoostCampAi Tech에서 최종프로젝트를 진행하였습니다. 제 개인적인 생각으로는 중형규모의 프로젝트였습니다. user flow , system design, prototype , serving의 단계를 거친 프로젝트 였기에 , 여러  module, 이를 넘어선 package 단위로 프로젝트를 관리하게 되었습니다. 초기에는  , 모듈을 import 할 때 absolute path로 관리를 했었습니다. module 및 package를 관리하기 위해서 hardcoding으로 세부적인 경로를 지정해서 import했습니다. 이의 문제점을 깨닫고 , 제가 다시 relative import 로 바꾸었지만, package, module에 관한 공부를 저만 했기에 팀원들이 이해하지 못하여 다시 absolute path로 돌아가게 되었습니다. 제가 이 때 팀원들에게 잘 알려주었다면 이라는 생각이 들었습니다.이 외에도 ,  ML Model을 학습할 때는 단순히 여러 module들을 한 directory안에서 관리해도 괜찮았지만, 여러 domain의 engineer들과 협업을 하게 되면 package  단위로 프로젝트를 관리하게 된다고 생각이 들어서 이 내용을 제대로 정리하는 기회를 가져보고자 합니다.(되게, 양이 많은 내용이여서 제가 프로젝트를 하면서 공부했던 내용들을 정리하고 추가적으로 정리해서 update 하도록 하겠습니다.)(이 책은 Python-cookbook 을 보고 공부하고 제가 closed book으로 저의 word로 적은 글입니다.)(적고 다시 책을 보고 부족한 내용을 채워넣었습니다.)

## hierachical package and module

```python
graphics 
	__init.py
	primitive/
		__init__.py
		line.py
		fill.py
		text.py
	pic/
		__init__.py
		png.py
		jpg.py
```

graphics라는 package가 있다고 하겠습니다.`fill.py `, `text.py` , `png.py`를 import할려면 어떻게 해야할까요?

```python
import graphics.primitive.line
from graphics.primitive import fill
from graphics.pic import png
```

## `__init__.py`  

python에서 `__init__.py` 를 각 pacakage에 포함시킬 수 있습니다. 이 packagae를 import하게 되면 `__init__.py`를 실행하게 됩니다. 

`graphics.primitive.line`를  import하게 되면 먼저  `graphics.__init__.py` , `graphics.primitive.__init__.py`를 import 하게 됩니다.  `__init__.py`는 아무내용이 적혀있지 않아도 괜찮습니다. 

```python
from . import png
from . import jpg
```

만약에 , `pic.__init__.py`에 위와 같이 적혀있다고 하겠습니다. 

```python
import graphics.primitive.line
from graphics.primitive import fill
import pic
```

pic을 import 하게 되면 png ,jpg를 import 하게 될 것입니다. 

`__init__.py`는 Python 3.3 버전 이전에는 반드시 필수적으로 사용을 해야만 했지만, 지금은 사용하지 않아도 괜찮습니다.

##  `__all__`

import * 를 사용하게 되면 매번 module안의 모든 attribute 및 method를 import하게 됩니다. import *를 사용하지만 좀 더 정교하게 module을 import 할 수 있는  방법이 있을까요? `__all__ = []` 를 사용하면 됩니다.

```python
def spam():
	pass
	
def mac():
	pass

blah = 42

__all__=['spam' , 'mac']
```

이 module을 import를 하게 되면, namespace에 `spam` , `mac`  만이 포함 되어있음을 확인할 수 있습니다. 만약에 , empty list가 `__all__` 에 assign 된다면 아무것도 import 하지 않을 것입니다.

## realtive import

매번 , 하드 코딩을 통해 module을 import 할 수 있지만 realtive path를 이용하면 코딩의 양을 줄일 수 있습니다. 

```python

    A
        __init__.py
        grok.py
        Gabi.py
    B
        __init__.py
        mba.py
        ccd.py
```

이러한 package들이 있습니다.

```python
from . import Gabi
```

`grok` 에서 Gabi를 import를 하게 된다면 `.` 를 이용하게 됩니다. `.` 은 현재 directory를 의미하는 relative path입니다. 

```python
from ..B import mba
```

`grok`에서 mba를 import를 하게 된다면, `..` 를 이용하게 됩니다. `..` 은 자신이 속해 있는 package와 같은 level 을 의미합니다.

`.`  , `..`  이 둘은 굉장히 유용하지만 만능은 아닙니다.  

```python
from . import Gabi  # OK
import .Gabi   # Error
```

이 syntax는 오직 from 을 사용하는 import 문에서만 허용이 됩니다. `.Gabi` 를 import 할 수는 없습니다.

```python
E
	__init__.py
    c.py
my_package
	__init__.py
    A
    	__init__.py
        grok.py
        Gabi.py
    B
    	__init__.py
        mba.py
        ccd.py


from ...E import c  # Error
```

dotted name patterns들은 자신이 속해 있는 package가 정의되어 있는 곳을 벗어날 수 없습니다.  A package의 level보다 더 상위 레벨에 있는 package D에서 c라는 module을 import 한다고 하겠습니다.뭔가 될거 같지만,  syntax를 error를 마주하게 됩니다.

그리고, 이 relative import는 module,package들이 규칙을 지키면서 구성이 되어있어야 합니다.

relative import는 package가 아닌 단순 top-level(시작점) module script에서는 동작하지 않습니다. 또한 , package안의 module이 단순 script로 써 실행이 된다면 동작하지 않습니다.



```python
python my_package/A/grok.py  # relative import error

python -m my_package.A.grok  # OK
```



