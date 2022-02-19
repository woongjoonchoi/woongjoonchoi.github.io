---

title : Special Stalck

# layout: "post"

excerpt: "Special Stack "

categories:
  - CodingInterview
tags:
  - [Algorithm,CodingTest,CodingInterview]
# classes : wide
toc: true
toc_sticky: true
---
Stack이라는 data structure는 FIFO(First In First Out)의 원칙을 가지는 data structure입니다. 대표적으로 사용되는 곳이 computer memory 입니다. Stack에 원소들을 저장하면서 data set의 max, min 같은 property를 같이 보관하고 싶을 수도 있습니다 . 기존의 Stack은 max,min 같은 property를 보관해주지 않습니다.예를 들어 , DataStructure에서 최솟값을 data의 size에 관계없이 O(1) 의 time complexity로 return 해주는 Heap이라는 자료구조가 있습니다 . 하지만, Heap과는 다르지만 Stack에서도 최솟값을 data의 Size에 관계없이 O(1)의 time complexity로 return 해주도록 할 수 있습니다. 

# Problem Statement

**Question:** Design a Data Structure SpecialStack that supports all the stack operations like push(), pop(), isEmpty(), isFull() and an additional operation getMin() which should return minimum element from the SpecialStack. All these operations of SpecialStack must be O(1). To implement SpecialStack, you should only use standard Stack data structure and no other data structure like arrays, list, . etc. 

link : [[Special Stack Practice  GeeksforGeeks]](https://www.geeksforgeeks.org/design-and-implement-special-stack-data-structure/)

Design a data-structure **SpecialStack** that supports all the stack operations like **push()**, **pop()**, **isEmpty()**, **isFull()** and an additional operation **getMin()** which should return **minimum** element from the SpecialStack. Your task is to **complete all the functions**, using stack data-Structure.


**Example 1:**

```
Input:
Stack: 18 19 29 15 16
Output: 15
Explanation:
The minimum element of the stack is 15.
```

 


**Your Task:**
Since this is a function problem, you don't need to take inputs. You just have to complete 5 functions, **push()** which takes the stack and an integer x as input and pushes it into the stack; **pop()** which takes the stack as input and pops out the topmost element from the stack; **isEmpty()** which takes the stack as input and returns true/false depending upon whether the stack is empty or not; **isFull()** which takes the stack and the size of the stack as input and returns true/false depending upon whether the stack is full or not (depending upon the
given size); **getMin()** which takes the stack as input and returns the minimum element of the stack. 
**Note:** The output of the code will be the value returned by **getMin()** function.


**Expected Time Complexity:** O(N) for getMin, O(1) for remaining all 4 functions.
**Expected Auxiliary Space:** O(1) for all the 5 functions.


**Constraints:**
1 ≤ N ≤ 104

# Simple no efficient sol

min value가 stack의 element에서의 min value라는 사실을 잘 기억해야 합니다. 단순하게 생각하면 , stack에 매번 operation을 할 때 마다 , min_value를 update 하면 괜찮지 않나라고 생각할 수 있습니다 .하지만 , 처음에 min_value가 설정되고 push ,pop operation을 반복하다 보면 min_value가 매번 바뀌게 됩니다. pop을 하면서 이전의 min_value로 복원을 해줄지 말지 결정할 수 가 없습니다. 이전의 min_value를 기억할 방법이 없습니다.  따라서, 이 방법은 사용할 수 없습니다.



# Sol1

위에서 얻은 힌트를 보면 이전의 min_value 문제만 해결하면 됨을 알 수 있습니다. push , pop operation을 하면서 stack의 element들이 바뀌는데  , 이전의 value 를 기억하지 못한다는 것은 orignal stack의 element와 min_vaule간의 연결고리 , 즉 mapping 관계가 없음을 의미합니다  . orginal stack 과 min_value 간의 mapping 관계가 유지가 된다면, min_value 는 여러 개의 value가 담겨있는 sequence가 될 것입니다. 그리고, mapping 관계를 유지하기에 stack operation이 적용이 될 것입니다. 





```python
# Python3 program for the
# above approach
# A simple stack class with 
# basic stack funtionalities
class stack:
 
  def __init__(self):
 
    self.array = []
    self.top = -1
    self.max = 100 
 
  # Stack's member method to check
  # if the stack is empty
  def isEmpty(self):
 
    if self.top == -1:
      return True
    else:
      return False 
 
  # Stack's member method to check
  # if the stack is full 
  def isFull(self): 
     
    if self.top == self.max - 1:
      return True
    else:
      return False  
 
  # Stack's member method to
  # insert an element to it  
 
  def push(self, data):
 
    if self.isFull():
      print('Stack OverFlow')
      return
    else:
      self.top += 1
      self.array.append(data)    
 
  # Stack's member method to
  # remove an element from it
  def pop(self):
 
    if self.isEmpty():
      print('Stack UnderFlow')
      return
    else:
      self.top -= 1
      return self.array.pop()
 
# A class that supports all the stack 
# operations and one additional
# operation getMin() that returns the
# minimum element from stack at
# any time.  This class inherits from
# the stack class and uses an
# auxiliary stack that holds
# minimum elements 
class SpecialStack(stack):
 
  def __init__(self):
    super().__init__()
    self.Min = stack() 
 
  # SpecialStack's member method to
  # insert an element to it. This method
  # makes sure that the min stack is also
  # updated with appropriate minimum
  # values
  def push(self, x):
 
    if self.isEmpty():
      super().push(x)
      self.Min.push(x)
    else:
      super().push(x)
      y = self.Min.pop()
      self.Min.push(y)
      if x <= y:
        self.Min.push(x)
      else:
        self.Min.push(y) 
 
  # SpecialStack's member method to 
  # remove an element from it. This
  # method removes top element from
  # min stack also.
  def pop(self):
 
    x = super().pop()
    self.Min.pop()
    return x 
 
  # SpecialStack's member method
  # to get minimum element from it.
  def getmin(self):
 
    x = self.Min.pop()
    self.Min.push(x)
    return x
 
# Driver code
if __name__ == '__main__':
   
  s = SpecialStack()
  s.push(10)
  s.push(20)
  s.push(30)
  print(s.getmin())
  s.push(5)
  print(s.getmin())
 
# This code is contributed by rachitkatiyar99
```



1. Original Stack, MinStack 을 생성합니다. 
2.  When Push to original stack
   1. If stack empty : 
      1. Push(element) to minstack
   2. if stack not emtpy :
      1. if top of minstack <= element : 
         1. Push minstack top to minstack
      2. else :
         1. Push element to minstack
3. When Pop from minstack 
   1. Pop from minstack



Pop  Operation 의 경우 minstack 과 original stack이 항상 대응관계이기에 , Original Stack이 Pop을 할 때 , 같이 Pop을 해주면 됩니다. 







# Sol2

So1의 문제점으로 minstack의 공간이 O(n)의 Space Complexity가 발생한다는 점입니다. 반드시 , 모든 stack의 element에 최솟값을  mapping을 할 필요가 있을까요? Sol1에서 Minstack의 element는 min value가 update 되는 순간에 element의 값이 변함을 알 수 있습니다. 따라서, original stack에서 minvalue가 update 될 때 , minstack에서 어떤 min_value에 mapping 되는지를 알 수 있다면 언제나 min_value를 얻을 수 있을 것입니다. 

```python
class MinStack:
    
    def __init__(self):
        self.stack = []
        self.min_stack = []
    def push(self, val: int) -> None:
        self.stack.append(val)
        if len(self.min_stack)==0 or val <= self.getMin() :
            self.min_stack.append(val)
    def pop(self) -> None:
        if len(self.stack) == 0 or self.stack.pop() == self.getMin() :
            self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]


# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(val)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```

1. Push
   1. if stack size == 0:
      1. push to minstack
      2. push to original stack
   2. original stack size > 0 :
      1. push to org stack
      2. if getmin() <= ele :
         1. push to min stack
2. Pop
   1. if stack.size < 0 :
      1. not Operation

   2. if stack.size > 0 :
      1. if getmin() == orignal stack.pop() 
         1. minstack.pop()


## Q1 . Why duplicated min value ? 

알고리즘을 보면 min value를 같은 값을 중복해서 min stack에 저장함을 알 수 있습니다.  min value를 1개만 저장하는 것이 space complexity 측면에서 효율적이다 라고 볼 수 있습니다  . 아까 위에서 min value와 stack의 element를 mapping 해서 이전의 min value를 복원할 수 있다는 것을 설명했습니다 . 만약에 , min value를 중복해서 저장하지 않는다면 min stack에서 언제 pop을 해야할 지 알 수 없습니다 . 

구체적으로 경우를 나눠 보면 , 

- unique한 min_value인 경우 :
  - 위에서 설명한 알고리즘을 따라가면 됩니다.
- multiple min_value인 경우 : 
  - 어떤 element가 최초의 min_value인지 표시를 해야 합니다.

즉, 2가지 mapping을 구현을 해야되는 상황이 발생합니다.즉 , minstack에 multiple min_value인지 아닌지를 표시해주는 flag가 변수를 매번 붙여줘야 함으로 , space complexity에서 O(n) 의 overhead가 추가적으로 발생하게 됩니다. 따라서, 비효율적이라 볼 수 있습니다



# Sol3

위의 2가지 솔루션을 요약하면, min_value와 stack의 element 사이에 1:1 mapping을 만들어 주어야 한다. 1 : 1 mapping을 만들기 위해서 반드시 추가저장 공간이 필요할 까요?

map 이라는 것은 function을 뜻합니다 . function이란 key값이 있으면 value 값에 대응시키는 relation 입니다 . 어떤 key값이 주어지면 value를 assign 해주는 관계를 수학적으로 정의가 가능하다면 O(n) 의 auxiliary space complexity가 발생하지 않을 것입니다.  수학적으로 정의한 값을 stack에 넣어준다면 auxiliary space complexity 없이 2개의 값을 보존하는 것이 가능할 것입니다. 

## ver 1

```
class stack:
    def __init__(self):
        self.s=[]
        self.minEle=None
        
    def push(self,x):
        if self.minEle == None :
            self.minEle = x
            self.s.append(x)
            
        elif self.minEle > x :  
            self.s.append(2*x - self.minEle)
            self.minEle = x
        else :
            self.s.append(x)
        #CODE HERE
        
    def pop(self):
        if len(self.s) == 0:
            return -1
        if len(self.s) == 1 :
            self.minEle = None
            return self.s.pop()
        if self.minEle > self.s[-1] :
            x = self.minEle
            self.minEle = 2 * self.minEle - self.s.pop()
            return x
        else :
            return self.s.pop()
        #CODE HERE
        

    def getMin(self):
        #CODE HERE
        if len(self.s) == 0 :
            return -1
        return self.minEle

#{ 
#  Driver Code Starts
#contributed by RavinderSinghPB
if __name__ == '__main__':
    t = int(input())
    for _ in range(t):
        q = int(input())

        arr = [int(x) for x in input().split()]

        stk=stack()  

        qi = 0
        qn=1
        while qn <= q:
            qt = arr[qi]

            if qt == 1:
                stk.push(arr[qi+1])
                qi+=2
            elif qt==2:
                print(stk.pop(),end=' ')
                qi+=1
            else:
                print(stk.getMin(),end=' ')
                qi+=1
            qn+=1
        print()

# } Driver Code Ends
```





min_value를 O(1) 의 complexity로 계산해서 결과값으로 도출해야 하기 때문에 , min_value를 key로 가지는 function을 만들어야 할거 같습니다. 

stack에  element가 들어오는 경우는 min_value > element  , min_value <= elment의 relation 중 하나입니다. 

알고리즘은 아래와 같습니다.



1. ​	Push	 
   1. if min_value == None :
      1. min_value = element
      2. stack.push(element)
   2. min_value <= element :
      1. push element stack
   3. min_value > element :
      1. push 2*element - min_value
      2. min_value = element
2.  Pop
   1.  if stack size == 0 :
      1. return -1
   2. if stack size == 1 :
      1. min_value = None
      2. stack.pop()
   3. min_value > stack.top() :
      1.  min_value = 2 * min_value -  stack.top() 
      2. stack.pop()
   4. min_value < stack.top() 
      1. stack.pop()



아까와는 다르게 min_value의 duplicate를 허용하지 않습니다. 왜냐하면, 처음 나온 min_value에 대해서 mathmatical function 값을 계산하여 stack에 push하기 때문에 따로 flag를 안달아도 위치를 찾을 수 있기 때문입니다.

전체적인 알고리즘에 대하여 설명을 하면 ,  min_value <= element 인 경우는 기존의 stack 처럼 push를 합니다. 만약에 , min_value > element인 경우가 발생하면 2 * element - min_value를 계산하여 stack에 push합니다. stack의 element가 min_value와의 function 관계를 나타내는 것임을 알기 위해서는 기준점이 필요합니다. min_value<=element인 기준과  다른 기준을 사용할려면 min_value > element인 기준이 필요합니다. 이 시점에서 min_value는 update된 min_value입니다. 따라서, element를 min_value로 update 하기전에 element < f(element,min_value) 인 f를 만들어야 합니다. element < min_value 이므로 element-min_value < min_value를 만족하게 됩니다. 하지만, 둘의 차이가 어느정도인지 몰라서 overflow가 발생할 수도 있습니다. 따라서, element- min_value <0 인 것을 이용하여 element -min_value + element < min_value 인 관계를 도출할 수 있습니다. 



## ver2

key 1개로 1개의 value가 아닌 value의 set을 return할 수는 없을까요?  Discrete math에서 자주사용되는 modular 연산이라는 것을 이용하면 됩니다.  Modular 연산이란 어떤 key값을 통해서 remainder를 구하는 mapping관계 입니다. remainder를 구할 수 있다는 것은 key값에 대한  quotient를 구할 수 있다는 것을 의미합니다. 따라서, 1: 2 mapping 관계를 만들 수 있게 됩니다. 

```python
class stack:
    def __init__(self):
        self.s=[]
        self.minEle=None
        self.key = 9999
        
    def push(self,x):
        if self.minEle == None :
            self.minEle = x
            self.s.append(key * x + min_Ele)
           
        else :
            self.s.append(key * x + min_Ele)
        #CODE HERE
        
    def pop(self):
        if len(self.s) == 0:
            return -1
        if len(self.s) == 1 :
            self.minEle = None
            return self.s.pop() // key
        else :
            return self.s.pop() // key
        #CODE HERE
        

    def getMin(self):
        #CODE HERE
        if len(self.s) == 0 :
            return -1
        return self.s.top() % key

#{ 
#  Driver Code Starts
#contributed by RavinderSinghPB
if __name__ == '__main__':
    t = int(input())
    for _ in range(t):
        q = int(input())

        arr = [int(x) for x in input().split()]

        stk=stack()  

        qi = 0
        qn=1
        while qn <= q:
            qt = arr[qi]

            if qt == 1:
                stk.push(arr[qi+1])
                qi+=2
            elif qt==2:
                print(stk.pop(),end=' ')
                qi+=1
            else:
                print(stk.getMin(),end=' ')
                qi+=1
            qn+=1
        print()

# } Driver Code Ends
```

1. Push
   1. if min_ele == None :
      1. min_ele = element
      2. push key * element + min_ele
   2. else :
      1. push key * element + min_ele 
2. Pop
   1. if len(stack) == 0 :
      1. return -1
   2. if len(stack ) == 1 :
      1. return stack.pop()//key
      2. min_ele = None
   3. else :
      1. return stack.pop()//key
3. getmin
   1. return stack.top() % key
