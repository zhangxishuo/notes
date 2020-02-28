# Integer 缓存问题分析

> 【强制】所有整型包装类对象之间值的比较，全部使用`equals`方法比较。

> 说明：对于`Integer var = ?`在`- 128`至`127`范围内的赋值，`Integer`对象是在`IntegerCache.cache`产生，会复用已有对象，这个区间内的`Integer`值可以直接使用`==`进行判断，但是这个区间之外的所有数据，都会在堆上产生，并不会复用已有对象，这是一个大坑，推荐使用`equals`方法进行判断。

**示例**

```java
public class IntTest {
    public static void main(String[] args) {
        Integer a = 100, b = 100, c = 150, d = 150;
        /** true */
        System.out.println(a == b);
        /** false */
        System.out.println(c == d);
    }
}
```

因为缓存了`-128`到`127`之间的数值。

**源码**

```java
/**
 * Returns an {@code Integer} instance representing the specified
 * {@code int} value.  If a new {@code Integer} instance is not
 * required, this method should generally be used in preference to
 * the constructor {@link #Integer(int)}, as this method is likely
 * to yield significantly better space and time performance by
 * caching frequently requested values.
 *
 * This method will always cache values in the range -128 to 127,
 * inclusive, and may cache other values outside of this range.
 *
 * @param  i an {@code int} value.
 * @return an {@code Integer} instance representing {@code i}.
 * @since  1.5
 */
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

如果用`Ineger.valueOf(int)`来创建整数对象，参数大于等于整数缓存的最小值`(IntegerCache.low)`并小于等于整数缓存的最大值`(IntegerCache.high)`，会直接从缓存数组`(java.lang.Integer.IntegerCache#cache)`中提取整数对象；否则会`new`一个整数对象。

**那么这里的缓存最大和最小值分别是多少呢？**

从上述注释中我们可以看出，最小值是`-128`，最大值是`127`。

**那么为什么会缓存这一段区间的整数对象呢？**

通过注释我们可以得知：如果不要求必须新建一个整型对象，缓存最常用的值(提前构造缓存范围内的整型对象)，会更省空间，速度也更快。

**启示：**

> 如果想减少内存占用，提高程序运行的效率，可以将常用的对象提前缓存起来，需要时直接从缓存中提取。

**Integer 缓存的区间可以修改吗？**

```java
/**
 * Cache to support the object identity semantics of autoboxing for values between
 * -128 and 127 (inclusive) as required by JLS.
 *
 * The cache is initialized on first usage.  The size of the cache
 * may be controlled by the {@code -XX:AutoBoxCacheMax=<size>} option.
 * During VM initialization, java.lang.Integer.IntegerCache.high property
 * may be set and saved in the private system properties in the
 * sun.misc.VM class.
 */
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];
    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue = sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        // 省略其它代码
    }
    // 省略其它代码
}
```

通过`IntegerCache`代码和注释我们可以看到，最小值是固定值`-128`，最大值并不是固定值，缓存的最大值是可以通过虚拟机参数`-XX:AutoBoxCacheMax=<size>`或`-Djava.lang.Integer.IntegerCache.high=<value>`来设置的，未指定则为`127`。

**为什么要缓存这个范围的数据？**

> 是为了自动装箱时可以复用这些对象，这也是`JLS2`的要求。

> 在`-128`到`127`(含)之间的`int`类型的值，或者`boolean`类型的`true`或`false`，以及范围在`'\u0000'`和`'\u007f'`(含)之间的`char`类型的数值`p`，自动包装成`a`和`b`两个对象时，可以使用`a == b`判断`a`和`b`的值是否相等。

**反汇编**

首先编译源代码：`javac IntTest.java`。

然后需要对代码进行反汇编，执行：`javap -c IntTest`。

```text
Compiled from "IntTest.java"
public class com.chujianyun.common.int_test.IntTest {
  public com.chujianyun.common.int_test.IntTest();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: bipush        100
       2: invokestatic  #2                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
       5: astore_1
       6: bipush        100
       8: invokestatic  #2                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
      11: astore_2
      12: sipush        150
      15: invokestatic  #2                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
      18: astore_3
      19: sipush        150
      22: invokestatic  #2                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
      25: astore        4
      27: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
      30: aload_1
      31: aload_2
      32: if_acmpne     39
      35: iconst_1
      36: goto          40
      39: iconst_0
      40: invokevirtual #4                  // Method java/io/PrintStream.println:(Z)V
      43: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
      46: aload_3
      47: aload         4
      49: if_acmpne     56
      52: iconst_1
      53: goto          57
      56: iconst_0
      57: invokevirtual #4                  // Method java/io/PrintStream.println:(Z)V
      60: return
}
```

可以明确得“看到”这四个`Integer var = ?`形式声明的变量的确是通过`java.lang.Integer#valueOf(int)`来构造`Integer`对象的。

**Long缓存问题源码**

```java
public static Long valueOf(long l) {
    final int offset = 128;
    if (l >= -128 && l <= 127) { // will cache
        return LongCache.cache[(int)l + offset];
    }
    return new Long(l);
}

private static class LongCache {
    private LongCache(){}

    static final Long cache[] = new Long[-(-128) + 127 + 1];

    static {
        for(int i = 0; i < cache.length; i++)
            cache[i] = new Long(i - 128);
    }
}
```

**Short缓存问题源码**

```java
public static Short valueOf(short s) {
    final int offset = 128;
    int sAsInt = s;
    if (sAsInt >= -128 && sAsInt <= 127) { // must cache
        return ShortCache.cache[sAsInt + offset];
    }
    return new Short(s);
}

private static class ShortCache {
    private ShortCache(){}

    static final Short cache[] = new Short[-(-128) + 127 + 1];

    static {
        for(int i = 0; i < cache.length; i++)
            cache[i] = new Short((short)(i - 128));
    }
}
```

**Byte缓存问题源码**

```java
public static Byte valueOf(byte b) {
    final int offset = 128;
    return ByteCache.cache[(int)b + offset];
}

private static class ByteCache {
    private ByteCache(){}

    static final Byte cache[] = new Byte[-(-128) + 127 + 1];

    static {
        for(int i = 0; i < cache.length; i++)
            cache[i] = new Byte((byte)(i - 128));
    }
}
```

**Boolean缓存问题源码**

```java
public static Boolean valueOf(boolean b) {
    return (b ? TRUE : FALSE);
}

public static final Boolean TRUE = new Boolean(true);

public static final Boolean FALSE = new Boolean(false);
```

**Character缓存问题源码**

```java
public static Character valueOf(char c) {
    if (c <= 127) { // must cache
        return CharacterCache.cache[(int)c];
    }
    return new Character(c);
}

private static class CharacterCache {
    private CharacterCache(){}

    static final Character cache[] = new Character[127 + 1];

    static {
        for (int i = 0; i < cache.length; i++)
            cache[i] = new Character((char)i);
    }
}
```

**Float与Double无缓存**

```java
public static Float valueOf(float f) {
    return new Float(f);
}

public static Double valueOf(double d) {
    return new Double(d);
}
```