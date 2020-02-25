# 架构

## MVP

![MVP](assets/mvp.png)

`Presenter`是`MVP`的核心，`Presenter`职责太重，并且其中很大的一部分代码都是在操作`DOM`，属于面向`DOM`编程。

## MVVM

![MVP](assets/mvvm.png)

`Vue`框架提供`ViewModel`层，不需要关注`VM`，只需要关注`View`与`Model`。