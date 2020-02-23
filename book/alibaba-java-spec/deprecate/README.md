# 过期类、属性、接口的正确处理姿势

> 接口过时必须加`@Deprecated`注解，并清晰地说明采用的新接口或者新服务是什么。

> 接口提供方既然明确是过时接口，那么有义务同时提供新的接口；作为调用方来说，有义务去考证过时方法的新实现是什么。

我们以`JDK`中的`URLEncoder`和`URLDecoder`为例介绍如何写过期函数的注释和如何替换该过期函数：

```java
String url = "xxx";
String encode = URLEncoder.encode(url);
log.debug("URL编码结果：" + encode);
String decode = URLDecoder.decode(encode);
log.debug("URL解码结果：" + decode);
```

在`IDEA`中编写如上代码时候，`java.net.URLEncoder#encode(java.lang.String)`和`java.net.URLDecoder#decode(java.lang.String)`会有删除的标志，便表示该函数已经过期。

```java
/**
 * Decodes a {@code x-www-form-urlencoded} string.
 * The platform's default encoding is used to determine what characters
 * are represented by any consecutive sequences of the form
 * "<i>{@code %xy}</i>".
 * @param s the {@code String} to decode
 * @deprecated The resulting string may vary depending on the platform's
 *          default encoding. Instead, use the decode(String,String) method
 *          to specify the encoding.
 * @return the newly decoded {@code String}
 */
@Deprecated
public static String decode(String s) {
    String str = null;
    try {
        str = decode(s, dfltEncName);
    } catch (UnsupportedEncodingException e) {
        // The system should always have the platform default
    }
    return str;
}
```

在`@deprecated`的注释里我们找到了答案：“`The resulting string may vary depending on the platform’s default encoding.`（解析结果的字符串和系统的默认字符编码强关联）”，并给出了替代函数的说明“`Instead, use the decode(String,String) method to specify the encoding.`（使用`decode(String,String)`函数来指定字符串编码）”。

因此我们提供新的接口，就得接口要废弃时也可以参考这里写上废弃的原因以及替代的新接口。

通过[codota](https://www.codota.com/code/query)搜索，即可得到`github`优秀的开源框架或`stackoverflow`中相关优秀范例。

写工具类时如果能再注释上添加一些范例和结果，则会极大方便使用者。

作为接口或对象的提供者，废弃的类、属性、函数加上废弃的原因和替代方案。

如`RPC`订单常见接口的`OrderCreateParam`参数类的`JSON`类型参数：`orderItemDetail`要替换成列表`orderItemParams`下面的属性类型进行替换：

```java
public class OrderCreateParam {

    /**
     * 对象详情
     * 参考示例：'[{"count":22,"name":"商品1"},{"count":33,"name":"商品2"}]'
     * <p>
     * 废弃原因：订单详情由JSON传参，改为对象传参。
     * 替代方案： {@link com.imooc.basic.deprecated.OrderCreateParam#orderItemParams}
     */
    @Deprecated
    private String orderItemDetail;

    private List<OrderItemParam> orderItemParams;

    // 其他属性
}
```