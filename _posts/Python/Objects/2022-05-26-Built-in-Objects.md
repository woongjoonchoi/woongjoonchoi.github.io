---
title : "What is Objects and Why use Built-in objects"



excerpt: "Python Objects"

categories:
  - Python
tags:
  - [Python,Object,Builtin, LearnigPython]
# classes : wide
toc: true
toc_sticky: true
---
# What is Objects?

Python을 이용하여 Program을 만들려면 Object를 이용하여 Program을 만들게 됩니다. Object를 이용해서 Program을 만드는 것은 당연하다고 볼 수 있지만, 왜 굳이 Built-in Type을 사용해야 될까요? 

## Python's Conceptual Hierachy

Python의 Object-Type을 이해할려면 Python Syntax가 어떤 계층으로 이루어져 있는지를 알아야 합니다. 

1. Program은 여러개의 Modules로 이루어져 있습니다.
2. Modules는 여러개의 Statements로 이루어져 있습니다.
3. Statement는 여러개의 Expression으로 이루어져 있습니다.
4. Expression은 Object-type을 process하거나 create 합니다.

즉, 연역법에 의해서 , Python의 Program은 Object-type을 통해서만 작동됨을 알 수 있습니다. 

다시 말하면 , Python의 Program은 Object가 어떻게 작동하는지 알아야 , Python Program을 의도하는대로 작동시킬 수 있습니다. 



## Why Use Built-in types?

Object를 잘 알아야하는 이유는 알겠는데 , 왜 굳이 Built-in Object Type을 사용할까요?  내가 필요한 Operation을 지원해주는 Object-Type을 사용하면 안될까요?

### C++,C  vs Python

 C++,C 에서하는 일을 생각해보면 Built-in type을 사용하는 이유를 알 수 있습니다. C,C++ 에서 주로 Object를 Implementation 하는데 초점이 맞추어져 있습니다. 이 object를 위해서 memory structure를 배치하고 ,  memory allocation을 관리하고  , 또한 search 와 access 루틴을 구현해야 합니다. 

하지만, Python에서는 Built-in Object를 사용한다면, C++,C에서 해야만 하는 작업들을 할 필요가 없습니다.

### Other Reasons

위 이유 이외에도 다양한 여러가지 이유가 있습니다.

1. 간단한 task의 경우 Built-in Object type이 내가 지금 풀고있는 problem domain에 필요로하는 data structure를 제공해줄 수 있습니다. 
2. 복잡한 task의 경우 , custom object나 c언어 interface를 사용해야 할 수 있습니다 .Built-in type objects는 custom object을 구성하는 요소입니다.
3. Built-in object는 custom data structure보다 효율적입니다. Python의 Built-in Object는 speed를 최적화하기 위해 C로 구현된 data structure를 사용합니다.

## Python's Core Data Type

Python의 Core Data Type은 아래와 같습니다.

- Object
- Numbers
- Strings
- Lists
- Dictionaries
- Files
- Sets
- Other Core Types
- Program Unit Types
- Implementation-related data types

# References



Learning Python(by Mark Lutz)  - chp 4
