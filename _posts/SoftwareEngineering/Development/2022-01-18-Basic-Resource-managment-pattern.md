---
title : "Resource ManageMent"

excerpt: "Pattern in Resource Mangement"

categories:
  - Development
tags:
  - [SoftwareEngineering , Resource Management]
# classes : wide
toc: true
toc_sticky: true
---

저희가 프로그램을 만들게 되면 수많은 리소스들을 관리하게 됩니다. 이 리소스에는 memory , transaction ,thread , network connecting , file등이 포함됩니다 . 이것들은 전부 limit가 있습니다. 하지만, resource의 usage는 대부분 예측가능한 pattern을 따르게 됩니다. 그럼에도 불구하고 , 대부분의 개발자들은 resource를 관리할 때 일관적으로 pattern을 처리하지 못합니다. 오늘은 , 대중적인 pattern에 대해 알아보고자 합니다 .



## Basic Resource managment pattern

resource 를 관리할 때 대부분의 상황에서 적용되는 패턴이 있습니다. 바로 , allocator가 deallocation에 책임을 갖도록 하는 것입니다 . 즉, allocator가 resource를 할당해서 사용하게 되면, 사용중인 resource를 반환하는 것은 항상 allocator가 되야 합니다. allocator가 deallocation을 책임지는 일관적인 pattern을 사용하지 않고 , 제 3자가 resource 할당에 대한 책임을 가지게 되면, 어느 순간 resource를 사용할 수 없게 될 것입니다. 이에 대해, 예제 프로그램으로 구체적으로 알아보도록 하겠습니다. 



### Common resource management mistake

```ruby
def read_customer
 @customer_file = File.open(@name + ".rec", "r+")
 @balance = BigDecimal(@customer_file.gets)
end
def write_customer
 @customer_file.rewind
 @customer_file.puts @balance.to_s
 @customer_file.close
end
def update_customer(transaction_amount)
 read_customer
 @balance = @balance.add(transaction_amount,2)
 write_customer
end
```

이러한 Ruby 프로그램이 있다고 가정하겠습니다. 은행 업무에서 거래가 발생하면 사용자의 계좌에 있는 잔고를 업데이트 하는 프로그램입니다. 프로그램을 좀 더 자세히 보면, `read_customer` 가 file을 open하고 , `write customer` 가 file을 close 합니다. 하지만, 이 프로그램에는 치명적인 문제점이 있습니다. 이들이 너무 서로 연관되어 있다는 것입니다. `customer file` 이라는 자원을 read, write가 공유하고 있습니다. 그리고  , `customer file` 에 대한 요청을 하는 것은 update 인데, update 프로그램에서는 `customer file` 이 나타나질 않습니다 . 이렇게 공유되는 변수가 있기에 문제점이 발생하게 됩니다. 아래의 , 예시를 보겠습니다.

```ruby
def update_customer(transaction_amount)
 	read_customer
    if (transaction_amount >= 0.00)
     @balance = @balance.add(transaction_amount,2)
     write_customer
    end
end
```

프로그램의 제약사항이 바뀌어 `transaction amount`가 0이상일 경우에만 `customer file` 에있는 잔고를 변경시켜 주도록 프로그램을 수정하였습니다. 이렇게 되면 , Test시에는 의도한 기능이 잘 작동해서, 문제없다고 판단할 수 있습니다. 하지만, 실제로 Production 단계에 가게되면, 이 프로그램은 붕괴하게 될 것입니다 . 아마도 , `too many open file` 라는 오류를 보게 될 것입니다. 

이 프로그램은 `customer file` 을 open 하고 `transaction amount` 가 0 이상일때만 잔고를 변경하고 file을 닫습니다. 즉 , `transaction amount `가 0 이상이 아니더라도 , file이 open 되고 , close되는 것이 조건부 즉, `update customer `가 책임지지 않기에 file의 상태는 계속 열려있는 상태가 되는 것입니다. 



### Responsible for deallocation

```ruby
def read_customer
 @customer_file = File.open(@name + ".rec", "r+")
 @balance = BigDecimal(@customer_file.gets)
end
def write_customer
 @customer_file.rewind
 @customer_file.puts @balance.to_s
 @customer_file.close
end
def update_customer(transaction_amount)
 read_customer
if (transaction_amount >= 0.00)
 @balance += BigDecimal(transaction_amount, 2)
 write_customer
else
 @customer_file.close # Bad idea!
end
end
```

위의 코드는 문제점을 해결한 코드입니다. `transaction amount`가 0이상든 아니든 언제나 file을 close 합니다. 이제 , `update customer` 가 allocation을 요청하고 deallocation 을 요청하고 있습니다. 하지만, 이 정도로 충분하지 않습니다. 왜냐하면, file이 read, write에 공유되고 있는 variable이기 때문입니다.이렇게 되면, 누가 file을 열고 , 닫는지를 추적할 때 여러 단계를 거치므로 복잡해질 것입니다. 그리고, 이는 이상적인 deallocation에 대한 책임을 지는 것이 아닙니다.

### Not shared with multiple routine

```ruby
def read_customer(file)
 @balance=BigDecimal(file.gets)
end
def write_customer(file)
 file.rewind
 file.puts @balance.to_s
end
def update_customer(transaction_amount)
 file=File.open(@name + ".rec", "r+") # >--
 read_customer(file) # |
 @balance = @balance.add(transaction_amount,2) # |
 write_customer(file) # |
 file.close # <--
end
```

 `update customer`가 resource를 할당하고 해제하는 주체가 되어야 합니다.  `udpate customer ` 가 resource를 할당하고 해제하도록 code를 변경했습니다. 이제 reosource에 대한 모든 책임은 `udpate customer` 가지게 됩니다. 파일은 `udpate customer` 에 open이 되고 잔고를 변경하고 `update customer`에서 file이 닫히게 됩니다 .



## Need one more resource 

위에서 , resource allocation을 요청한 자가 deallocation까지 책임을 지는 basic pattern을 보았습니다. 이전까지는, resource를 allocation 받으면 deallocation 하는 pattenr이었습니다. 이제부터는 , 이 basic pattern은 한 resource를 사용중에 다른 resource를 요청하는 pattern으로 확장될 수 있습니다. 그렇다고 해도 , 위에서 다루었던 pattern이 바뀌질 않습니다. 약간의 변형이 있지만, 2가지 방법이 있습니다.

- 나중에 할당되었던   리소스부터 Deallocation하도록 합니다. 
  - 이렇게 되면, 다른 리소스에 대한 reference가 있어도 리소스가 고아가 되는 상황이 발생하지 않습니다.
- 다른 공간에 같은 resource를 할당한다 해도 , rule은 지키도록 합니다.
  - process A, B가 있을때 A가 B의 resource를 요청하고 , B가 A의 resource를 요청하면, process는 영원히 기다리는 상태가 됩니다. 이를 deadlock이라 부릅니다.  A는 갖고 있는  resource를 반드시 할당하도록 하고, B도 마찬가지로 갖고있는 resource를 할당하도록 합니다. 이렇게 되면, deadlock의 가능성이 좀 더 줄어들게 됩니다.

누군가 allocation을 하면 반드시 deallocation하는 기본적인 rule은 변하지 않습니다. 이를 프로그래밍 언어적 관점에서 보아도 항상 적용되고 있음을 알 수 있습니다.



### Object Resource management

Objected-Oriented Programming 에서 보면 class가 variable를 encapstulate 해서  보관하는 것이 유용함을 알 수 있습니다. class instance  하나 만들게 되면, class안에 있는 variable에 resource가 할당이 됩니다. 그리고 ,instance가 scope에서 벗어나게 되면, descontructor가 이를 descontruc하고 , garbage collector가 resource를 회수하게 됩니다.

object가 constructor하고 scope를 벗어나면 deconstructor를 호출하는 것은 allocator가 deallocation을  책임지는 것은과 동일하다고 볼 수 있습니다 .



### Exception Resource management

몇몇 programming language들은 exception을 지원하게 됩니다. 하지만, exception은 resource를 deallocation 하는 과정을 더 복잡하게 만듭니다. 예를 들어 , exception이전에 resource가 deallocation 됬음을 어떻게 알 수 있을까요? 이러한 경우에는 2가지 option 이있습니다.

1. variable scope
2. try .. finally.. catch

C++ 이나 Rust같은 언어들의 scoping rule에서는 , scope를 벗어나서 return이 되면 varialbe의 hook function으로 desconstructor가 호출되고 , resource를 deallocation 하게 됩니다. 

```rust
{
let mut accounts = File::open("mydata.txt")?; // >--
// use 'accounts' // |
 ... // |
} // <--
// 'accounts' is now out of scope, and the file is
// automatically close
```

여기서 account file은 자동적으로 scope를 벗어나면 할당이 해제되게 됩니다.

다른 option은 try, catch 구문입니다.

```
try
// some dodgy stuff
catch
// exception was raised
finally
// clean up in either case
```

하지만, try, catch를 사용할 떄 주의할 점이 있습니다. 

```
begin
 thing = allocate_resource()
 process(thing)
finally
 deallocate(thing)
end
```

allocation이 실패해서 deallocate를 하게 되면 어떻게 될까요? resource가 할당이 되지 않았음에도 deallocation을 요청하게 될 것입니다. 

```
thing = allocate_resource()
begin
 process(thing)
finally
 deallocate(thing)
end
```

allocation , deallocation을 try,finally에서 분리해주는 것이 올바른 pattern이라 할 수 있습니다. 

## Basic pattern not applied

여태까지 resource를 manage하는 basic한 pattern들을 알아봤습니다. 하지만, 이러한 pattern들이 적절하지 않을때 가 있습니다. 바로 , dynamic datastructure를 사용할 때입니다. dynamic datastructure에서는 어떤 routine이 memory를 할당하게 되면, 이를 large datastructure가 가리키게 될 것입니다. 여기서의 해결책은 어떠한 semantic invaraint를 정해서, 누가 deallocation을 책임질지 결정해야합니다. Top-level datastructure를 해제할 때 다음과 같은 option들중 하나를 선택해서 따르게 됩니다.

1. top-level structure는 substucture가 갖고있는 resource의 deallocation에 대한 책임이 있다. 
   1. substurcture의 data를 recursive하게 deallocation 한다.
2. top-level structure를 그냥 해제한다.
   1. top-level strucuture가 reference하고 있는 resource들은 고아가 된다.
3. substructure가 resource를 allocation하고 있으면 top-level structure가 deallocation을 거절한다.

이 선택에는 절대적으로 좋은 것이 있는게 아니고 , 어떤 dats structure 를 사용하는지에 따라 달라지게 됩니다. 하지만, 이러한 option을 명확하게 일관되게 작동하도록 구현해야 합니다.

 

C같은 절처지향 언어에서는 이러한 optio들을 구현하는것이 문제가 될 수 있습니다. 왜냐하면, dats structure가 inactvie하기 때문입니다. C는 datastructure가 내부 method가 아닌 외부 method나 thread나 process로 제어됩니다.

해당 구조에 대한 표준 할당 및 할당 해제 기능을 제공하는 각 주요 구조에 대한 모듈을 작성하는 것입니다

## Checking the Resource

이러한 전략을 세워도 Resource가 적절하게 freed 되는지는 확신할 수 없습니다. 따라서, resource의 사용량이 어떻게 되는지 track하는 code를 만드는 것이 좋습니다.

### resource checking wrapper method

대부분의 application은 각 type의 resource에 대해서 wrappers를 만듭니다. wrapper가 allocation ,deallocation을 track하도록 만듭니다. 이러한 코도들이 어디를 track할지 정할 때 유용한 point가 있습니다. main program은 loop의 실행이 끝난후 어떤 지점의 code로 돌아오게 되었습니다.  이 때 , loop가 끝난후 memory usage가 증가했는지 확인하는 것은 좋은 option입니다. 