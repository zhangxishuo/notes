# 当 Switch 遇到空指针

> 【强制】当`switch`括号内的变量类型为`String`并且此变量为外部参数时，必须先进行`null`判断。

`switch`的表达式必须是`char, byte, short, int, Character, Byte, Short, Integer, String`，或者`enum`类型，否则会发生编译错误。

`switch`语句必须满足以下条件，否则会出现编译错误：

- 与`switch`语句关联的每个`case`都必须和`switch`的表达式的类型一致。
- 如果`switch`表达式是枚举类型，`case`常量也必须是枚举类型。
- 不允许同一个`switch`的两个`case`常量的值相同。
- 和`switch`语句关联的常量不能为`null`。
- 一个`switch`语句最多有一个`default`标签。

![switch](assets/switch.png)

> `switch`语句执行的时候，首先将执行`switch`的表达式。如果表达式为`null`，则会抛出`NullPointerException`，整个`switch`语句的执行将被中断。

因此空指针出现的根源在于：虚拟机为了实现`switch`的语法，将参数表达式转换成`int`。而这里的参数为`null`，从而造成了空指针异常。

先计算字符串参数的哈希值，判断哈希值的范围，然后哈希值相等再判断对象是否相等，然后执行对应的代码块。

![流程图](assets/process.png)

这种先判断`hash`值是否相等(有可能是同一个对象/两个对象有可能相等)再通过`equals`比较“对象是否相等”的做法，在`Java`的很多`JDK`源码中和其他框架中非常常见。