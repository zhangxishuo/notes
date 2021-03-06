# 计算机的字符与编码集

## 字符编码集的历史

### ASCII码

使用`7`个`bits`就可以完全表示`ASCII`码，共计`2^7 = 128`个字符，很多应用或者国家中的符号都无法表示。

### Extended ASCII码

第一次对`ASCII`码进行扩充，`7bits => 8bits`，共计`256`个字符。

## 编码集

### GB 2312

`1980`年发明，是我国最早最完备的编码集，共收录了`7445`个字符，但不符合国际标准。

### GBK

`1995`年推出，向下兼容`GB 2312`，向上支持国际`ISO`标准，收录了`21003`个汉字，支持全部中日韩字符。

### Unicode

兼容全球的字符集，`Unicode`定义了世界通用的符号集，`UTF-*`实现了该编码，`UTF-8`以字节为单位对`Unicode`进行编码。

> `Windows`系统默认使用`GBK`编码。