# 枚举类的正确学习方式

【参考】枚举类名带上`Enum`后缀，枚举成员名称需要全大写，单词间用下划线隔开。

说明: 枚举其实就是特殊的类，域成员均为常量，且构造方法被默认强制是私有。

【推荐】如果变量值仅在一个固定范围内变化用`enum`类型来定义。

【强制】二方库里可以定义枚举类型，参数可以使用枚举类型，但是接口返回值不允许使用枚举类型或者包含枚举类型的`POJO`对象。

## 使用场景

枚举一般用来表示一组相同类型的常量，比如月份、星期、颜色等。

枚举的主要使用场景是，当需要一组固定的常量，并且编译时成员就已能确定时就应该使用枚举。

因此枚举类型没必要多例，如果能够保证单例，则可以减少内存开销。

另外枚举为数值提供了命名，更容易理解，而且枚举更加安全，功能更加强大。

## 官方文档

如果枚举类如果被`abstract`或`final`修饰，枚举如果常量重复，如果尝试实例化枚举类型都会有编译错误。

枚举类除声明的枚举常量没有其他实例。

枚举类型的`E`是`Enum`的直接子类。

- `Enum`的`clone`方法被`final`修饰，保证`enum`常量不会被克隆。

- 禁止对枚举类型的反射。

- 序列化机制保证反序列化时枚举类型不允许构造多个相同实例。

## 反汇编

```java
public enum CoinEnum {
    PENNY(1), NICKEL(5), DIME(10), QUARTER(25);

    CoinEnum(int value) {
        this.value = value;
    }

    private final int value;
    public int value() { return value; }
}
```

先编译：`javac CoinEnum.java`

然后再反汇编：`javap -c CoinEnum`

反汇编结果：

```java
public final class com.imooc.basic.learn_enum.CoinEnum extends java.lang.Enum<com.imooc.basic.learn_enum.CoinEnum> {
  public static final com.imooc.basic.learn_enum.CoinEnum PENNY;

  public static final com.imooc.basic.learn_enum.CoinEnum NICKEL;

  public static final com.imooc.basic.learn_enum.CoinEnum DIME;

  public static final com.imooc.basic.learn_enum.CoinEnum QUARTER;

  // 第 1 处代码
  public static com.imooc.basic.learn_enum.CoinEnum[] values();
    Code:
       0: getstatic     #1                  // Field $VALUES:[Lcom/imooc/basic/learn_enum/CoinEnum;
       3: invokevirtual #2                  // Method "[Lcom/imooc/basic/learn_enum/CoinEnum;".clone:()Ljava/lang/Object;
       6: checkcast     #3                  // class "[Lcom/imooc/basic/learn_enum/CoinEnum;"
       9: areturn

   // 第 2 处代码
  public static com.imooc.basic.learn_enum.CoinEnum valueOf(java.lang.String);
    Code:
       0: ldc           #4                  // class com/imooc/basic/learn_enum/CoinEnum
       2: aload_0
       3: invokestatic  #5                  // Method java/lang/Enum.valueOf:(Ljava/lang/Class;Ljava/lang/String;)Ljava/lang/Enum;
       6: checkcast     #4                  // class com/imooc/basic/learn_enum/CoinEnum
       9: areturn

  public int value();
    Code:
       0: aload_0
       1: getfield      #7                  // Field value:I
       4: ireturn

  static {};
    Code:
       0: new           #4                  // class com/imooc/basic/learn_enum/CoinEnum
       3: dup
       4: ldc           #8                  // String PENNY
       6: iconst_0
       7: iconst_1
       8: invokespecial #9                  // Method "<init>":(Ljava/lang/String;II)V
      11: putstatic     #10                 // Field PENNY:Lcom/imooc/basic/learn_enum/CoinEnum;
      14: new           #4                  // class com/imooc/basic/learn_enum/CoinEnum
      17: dup
      18: ldc           #11                 // String NICKEL
      20: iconst_1
      21: iconst_5
      22: invokespecial #9                  // Method "<init>":(Ljava/lang/String;II)V
      25: putstatic     #12                 // Field NICKEL:Lcom/imooc/basic/learn_enum/CoinEnum;
      28: new           #4                  // class com/imooc/basic/learn_enum/CoinEnum
      31: dup
      32: ldc           #13                 // String DIME
      34: iconst_2
      35: bipush        10
      37: invokespecial #9                  // Method "<init>":(Ljava/lang/String;II)V
      40: putstatic     #14                 // Field DIME:Lcom/imooc/basic/learn_enum/CoinEnum;
      43: new           #4                  // class com/imooc/basic/learn_enum/CoinEnum
      46: dup
      47: ldc           #15                 // String QUARTER
      49: iconst_3
      50: bipush        25
      52: invokespecial #9                  // Method "<init>":(Ljava/lang/String;II)V
      55: putstatic     #16                 // Field QUARTER:Lcom/imooc/basic/learn_enum/CoinEnum;
      58: iconst_4
      59: anewarray     #4                  // class com/imooc/basic/learn_enum/CoinEnum
      62: dup
      63: iconst_0
      64: getstatic     #10                 // Field PENNY:Lcom/imooc/basic/learn_enum/CoinEnum;
      67: aastore
      68: dup
      69: iconst_1
      70: getstatic     #12                 // Field NICKEL:Lcom/imooc/basic/learn_enum/CoinEnum;
      73: aastore
      74: dup
      75: iconst_2
      76: getstatic     #14                 // Field DIME:Lcom/imooc/basic/learn_enum/CoinEnum;
      79: aastore
      80: dup
      81: iconst_3
      82: getstatic     #16                 // Field QUARTER:Lcom/imooc/basic/learn_enum/CoinEnum;
      85: aastore
      86: putstatic     #1                  // Field $VALUES:[Lcom/imooc/basic/learn_enum/CoinEnum;
      89: return
}
```

枚举类型的`E`是`Enum`的直接子类。枚举类编译后被自动加上`final`关键字。枚举常量也会被加上`public static final`修饰。

- 所有的枚举类型有一个隐式的函数`public static T valueOf(String)`用来根据枚举名称来获取枚举常量。

- 如果想获取当前枚举的所有枚举常量可以通过调用隐式的`public static T[] values()`函数来实现。

## 源码

其中`Enum`类有两个属性：

`name`表示枚举的名称。

`ordinal`表示枚举的顺序，其主要用在`java.util.EnumSet`和`java.util.EnumMap`这两种基于枚举的数据结构中。

### clone

我们查看`Enum`类的`clone`函数：

```java
/**
 * Throws CloneNotSupportedException.  This guarantees that enums
 * are never cloned, which is necessary to preserve their "singleton"
 * status.
 *
 * @return (never returns)
 */
protected final Object clone() throws CloneNotSupportedException {
    throw new CloneNotSupportedException();
}
```

通过注释和源码我们可以明确地学习到，枚举类不支持`clone`，如果调用会报`CloneNotSupportedException`异常。

目的是为了保证枚举不能被克隆，维持单例的状态。

### newInstance

我们知道即使将构造方法设置为私有，也可以通过反射机制`setAccessible`为`true`后调用。普通的类可以通过`java.lang.reflect.Constructor#newInstance`来构造实例，这样就破坏了单例。

然而在该函数源码中对枚举类型会作判断并报`IllegalArgumentException`。

```java
public T newInstance(Object ... initargs) throws InstantiationException, IllegalAccessException, IllegalArgumentException, InvocationTargetException {
    // 省略..
    if ((clazz.getModifiers() & Modifier.ENUM) != 0)
        throw new IllegalArgumentException("Cannot reflectively create enum objects");
 
     // 省略..
    return inst;
}
```

这样就防止了通过反射来构造枚举实例的可能性。

### compareTo

```java
/**
 * Compares this enum with the specified object for order.  Returns a
 * negative integer, zero, or a positive integer as this object is less
 * than, equal to, or greater than the specified object.
 *
 * Enum constants are only comparable to other enum constants of the
 * same enum type.  The natural order implemented by this
 * method is the order in which the constants are declared.
 */
public final int compareTo(E o) {
    Enum<?> other = (Enum<?>)o;
    Enum<E> self = this;
    if (self.getClass() != other.getClass() && // optimization
        self.getDeclaringClass() != other.getDeclaringClass())
        throw new ClassCastException();
    return self.ordinal - other.ordinal;
}
```

根据注释和源码，我们可以看到：其排序的依据是 枚举常量在枚举类的声明顺序。

## 实现状态机

假设业务开发中需要实现状态流转的功能。

活动有：申报-> 批准-> 报名 -> 开始 -> 结束几种状态，依次流转。

我们可以通过下面的代码实现：

```java
public enum ActivityStatesEnum {
    /**
     * 活动状态
     * 申报-> 批准-> 报名 -> 开始 -> 结束
     */
    DEACLARE(1) {
        @Override
        ActivityStatesEnum nextState() {
            return APPROVE;
        }
    },
    APPROVE(2) {
        @Override
        ActivityStatesEnum nextState() {
            return ENROLL;
        }
    },
    ENROLL(3) {
        @Override
        ActivityStatesEnum nextState() {
            return START;
        }
    },
    START(4) {
        @Override
        ActivityStatesEnum nextState() {
            return END;
        }
    },
    END(5) {
        @Override
        ActivityStatesEnum nextState() {
            return this;
        }
    };

    private int status;

    abstract ActivityStatesEnum nextState();

    ActivityStatesEnum(int status) {
        this.status = status;
    }
  
    public ActivityStatesEnum getEnum(int status) {
        for (ActivityStatesEnum statesEnum : ActivityStatesEnum.values()) {
            if (statesEnum.status == status) {
                return statesEnum;
            }
        }
        return null;
    }
}
```