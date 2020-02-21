# Java 序列化引发的血案

> 【强制】当序列化类新增属性时，请不要修改`serialVersionUID`字段，以避免反序列失败；如果完全不兼容升级，避免反序列化混乱，那么请修改`serialVersionUID`值。

> 说明：注意`serialVersionUID`值不一致会抛出序列化运行时异常。

**序列化与反序列化**

序列化是将内存中的对象信息转化成可以存储或者传输的数据到临时或永久存储的过程。而反序列化正好相反，是从临时或永久存储中读取序列化的数据并转化成内存对象的过程。

![序列化与反序列化](assets/serialization-and-deserialization.png)

如果要实现`Java`远程方法调用，就需要将调用结果通过网路传输给调用方，如果调用方和服务提供方不在一台机器上就很难共享内存，就需要将`Java`对象进行传输。而想要将`Java`中的对象进行网络传输或存储到文件中，就需要将对象转化为二进制字节流，这就是所谓的序列化。存储或传输之后必然就需要将二进制流读取并解析成`Java`对象，这就是所谓的反序列化。

序列化的主要目的是：方便存储到文件系统、数据库系统或网络传输等。

实际开发中常用到序列化和反序列化的场景有：

- 远程方法调用`(RPC)`的框架里会用到序列化。
- 将对象存储到文件中时，需要用到序列化。
- 将对象存储到缓存数据库`(Redis)`时需要用到序列化。
- 通过序列化和反序列化的方式实现对象的深拷贝。

**Java原生序列化**

```java
public interface Serializable {
}
```

`Java`原生序列化需要实现`Serializable`接口。序列化接口不包含任何方法和属性等，它只起到序列化标识作用。

一个类实现序列化接口则其子类型也会继承序列化能力，但是实现序列化接口的类中有其他对象的引用，则其他对象也要实现序列化接口。序列化时如果抛出`NotSerializableException`异常，说明该对象没有实现`Serializable`接口。

每个序列化类都有一个叫`serialVersionUID`的版本号，反序列化时会校验待反射的类的序列化版本号和加载的序列化字节流中的版本号是否一致，如果序列化号不一致则会抛出`InvalidClassException`异常。

强烈推荐每个序列化类都手动指定其`serialVersionUID`，如果不手动指定，那么编译器会动态生成默认的序列化号，因为这个默认的序列化号和类的特征以及编译器的实现都有关系，很容易在反序列化时抛出`InvalidClassException`异常。建议将这个序列化版本号声明为私有，以避免运行时被修改。

实现序列化接口的类可以提供自定义的函数修改默认的序列化和反序列化行为。

自定义序列化方法：

```java
private void writeObject(ObjectOutputStream out) throws IOException;
```

自定义反序列化方法：

```java
private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException;
```

通过自定义这两个函数，可以实现序列化和反序列化不可序列化的属性，也可以对序列化的数据进行数据的加密和解密处理。

`Java`序列化的优点是：对对象的结构描述清晰，反序列化更安全。主要缺点是：效率低，序列化后的二进制流较大。

**Hessian序列化**

`Hessian`是一个动态类型，二进制序列化，也是一个基于对象传输的网络协议。`Hessian`是一种跨语言的序列化方案，序列化后的字节数更少，效率更高。`Hessian`序列化会把复杂对象的属性映射到`Map`中再进行序列化。

`Hession`序列化二进制流较`Java`序列化更小，且序列化和反序列化耗时更短。但是父类和子类有相同类型属性时，由于先序列化子类再序列化父类，因此反序列化时子类的同名属性会被父类的值覆盖掉，开发时要特别注意这种情况。

> `Hession2.0`序列化二进制流大小是`Java`序列化的`50%`，序列化耗时是`Java`序列化的`30%`，反序列化的耗时是`Java`序列化的`20%`。

编写待测试的类：

```java
@Data
public class PersonHessian implements Serializable {
    private Long id;
    private String name;
    private Boolean male;
}

@Data
public class Male extends PersonHessian {
    private Long id;
}
```

编写单测来模拟序列化继承覆盖问题：

```java
/**
 * 验证Hessian序列化继承覆盖问题
 */
@Test
public void testHessianSerial() throws IOException {
    HessianSerialUtil.writeObject(file, male);
    Male maleGet = HessianSerialUtil.readObject(file);
    // 相等
    Assert.assertEquals(male.getName(), maleGet.getName());
    // male.getId()结果是1，maleGet.getId()结果是null
    Assert.assertNull(maleGet.getId());
    Assert.assertNotEquals(male.getId(), maleGet);
}
```

上述单测示例验证了：反序列化时子类的同名属性会被父类的值覆盖掉的问题。

**Kryo序列化**

`Kryo`是一个快速高效的`Java`序列化和克隆工具。`Kryo`的目标是快速、字节少和易用。`Kryo`还可以自动进行深拷贝或者浅拷贝。`Kryo`的拷贝是对象到对象的拷贝而不是对象到字节，再从字节到对象的恢复。`Kryo`为了保证序列化的高效率，会提前加载需要的类，这会带一些消耗，但是这是序列化后文件较小且反序列化非常快的重要原因。

`Kryo`优点是：速度快、序列化后二进制流体积小、反序列化超快。但是缺点是：跨语言支持复杂。注册模式序列化更快，但是编程更加复杂。

**JSON序列化**

`JSON (JavaScript Object Notation)`是一种轻量级的数据交换方式。`JSON`序列化是基于`JSON`这种结构来实现的。`JSON`序列化将对象转化成`JSON`字符串，`JSON`反序列化则是将`JSON`字符串转回对象的过程。常用的`JSON`序列化和反序列化的库有`Jackson`、`GSON`、`Fastjson`等。

`JSON`序列化的优势在于可读性更强。主要缺点是：没有携带类型信息，只有提供了准确的类型信息才能准确地进行反序列化，这点也特别容易引发线上问题。

下面给出使用`Gson`框架模拟`JSON`序列化时遇到的反序列化问题的示例代码：

```java
/**
 * 验证GSON序列化类型错误
 */
@Test
public void testGSON() {
    Map<String, Object> map = new HashMap<>();
    final String name = "name";
    final String id = "id";
    map.put(name, "张三");
    map.put(id, 20L);

    String jsonString = GSONSerialUtil.getJsonString(map);
    Map<String, Object> mapGSON = GSONSerialUtil.parseJson(jsonString, Map.class);
    // 正确
    Assert.assertEquals(map.get(name), mapGSON.get(name));
    // 不等  map.get(id)为Long类型 mapGSON.get(id)为Double类型
    Assert.assertNotEquals(map.get(id).getClass(), mapGSON.get(id).getClass());
    Assert.assertNotEquals(map.get(id), mapGSON.get(id));
}
```

下面给出使用 fastjson 模拟 JSON 反序列化问题的示例代码：

```java
/**
 * 验证FatJson序列化类型错误
 */
@Test
public void testFastJson() {
    Map<String, Object> map = new HashMap<>();
    final String name = "name";
    final String id = "id";
    map.put(name, "张三");
    map.put(id, 20L);

    String fastJsonString = FastJsonUtil.getJsonString(map);
    Map<String, Object> mapFastJson = FastJsonUtil.parseJson(fastJsonString, Map.class);

    // 正确
    Assert.assertEquals(map.get(name), mapFastJson.get(name));
    // 错误  map.get(id)为Long类型 mapFastJson.get(id)为Integer类型
    Assert.assertNotEquals(map.get(id).getClass(), mapFastJson.get(id).getClass());
    Assert.assertNotEquals(map.get(id), mapFastJson.get(id));
}
```