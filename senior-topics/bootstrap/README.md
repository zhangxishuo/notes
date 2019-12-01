# 问题描述

在软件开发越来越工程化的时代，雨后春笋般的开发工具使得开发越来越自动化，越来越简单。

使用`Angular CLI`，我们可以通过如下命令快速初始化一个简单的`Angular`应用并启动。

```shell
➜ ng new boot
➜ ng serve
```

打开浏览器，访问`http://127.0.0.1:4200`，即访问到初始化的`Angular`应用。

![访问效果](images/0.jpg)

用`WebStorm`打开项目，`src`文件夹下的文件众多，让人眼花缭乱，那究竟，一个`Angular`应用是怎么启动的呢？

![文件目录](images/1.jpg)

# 启动

`main.ts`文件负责整个`Angular`应用的启动。

![main.ts](images/2.jpg)

打开`main.ts`，找到如下代码：

```typescript
platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.error(err));
```

调用从`@angular/platform-browser-dynamic`包中导入的`platformBrowserDynamic`方法，该方法声明`Angular`应用在浏览器`(Browser)`环境中启动。因为`Angular`能被应用在`JavaScript`环境中，而不仅仅是浏览器。

```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
```

查看该方法的声明，其返回值类型为`PlatformRef`，即平台引用。

```typescript
export declare const platformBrowserDynamic: (extraProviders?: StaticProvider[] | undefined) => PlatformRef;
```

初步猜想，调用`platformBrowserDynamic`方法，返回了适用于浏览器平台的`PlatformRef`对象，可能调用其他方法，返回的`PlatformRef`对象适用于其他平台。

```typescript
.bootstrapModule(AppModule)
```
