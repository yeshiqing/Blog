# JS 代码重构经验

## 重构是什么？

对软件内部结构的一种调整，目的是在不改变软件系统外部行为的前提下，提高代码可读性，降低维护成本。

| 对比       | Code Refactoring                                             | Code Optimization                                            |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 概念       | 代码重构：在内部结构上修改代码，且不影响软件外部行为。       | 代码（性能）优化：在性能方面修改代码，且不影响软件外部行为。 |
| 什么时候做 | ①代码不易理解<br />②代码不易维护。很难去修改，或者低效率的重复改动很多。 | 出现性能问题时                                               |
| 效益       | 代码易读性提高，易于维护。                                   | 优化算法，降低时间复杂度。程序运行得更快，性能更好           |
| 编程思想   | While code refactoring, *design principles* are more useful like *SOLID, KISS, DRY, YAGNI*. | Use advance/generic concepts like design patterns, Async Programming etc.<br />使用先进的/通用的概念，如设计模式、异步编程等。 |



## 重构角度

在写完一个代码文件后，可以从以下角度进行重构。

- 简化代码，删除「注释掉的代码」（[commented out code](https://codeql.github.com/codeql-query-help/cpp/cpp-commented-out-code/#commented-out-code)），尽量删除“无用”的代码。说它“无用”是需要主观判断的，比如这个工具函数当前已经不用了，并且未来也不太可能用，就要删掉。

- 逻辑优化，属性归谁管就挂载在哪个对象上，归属的逻辑要正确。比如`$hijack`上的`websiteScreen` 、`pauseVideo`等方法应该挂载在`$eventsHandler`上。

- 逻辑优化，减少没有必要的重复执行的部分，本质是解耦模块间的依赖。比如执行函数 a 过程中调用了 b 函数，b 函数里面执行一个 c 函数，但是接下来 a 过程又要调用 c 函数。给A和C解耦，可以让 c 函数只调用一次，提高代码性能。

    | 解耦之前                                                     | 解耦之后                                                     |
    | ------------------------------------------------------------ | ------------------------------------------------------------ |
    | ![截屏2022-11-21 20.39.29](https://upload-images.jianshu.io/upload_images/1231311-01195b02fa8ab826.png) | ![截屏2022-11-21 20.39.16](https://upload-images.jianshu.io/upload_images/1231311-e7b6ab5ce0a8187a.png) |


- 统一变量、函数的命名规则。Follow the similar naming conventions throughout the project.

- 有些函数很臃肿，需要将大段的代码拆分成一个个小巧的功能函数。抽成函数并不是为了应对重复，而是为了拆分大段代码，使其便于阅读。

- 预测/预警重复，提前做「事不过三」的抽象，抽象出函数、类，以及使用「继承」。

- 意大利面条式代码作为属性，挂载在一个对象上，实际上是对象的思想（「模块化」思想）。与其说这个对象有这样的属性，我更愿意理解为这些属性归于这个对象来管理。
    另外，有很多全局变量，作为配置项，也可以挂载在一个叫 config 的对象上，都归 config 对象管。而且编辑器中还方便折叠代码，把一长串配置项折叠起来。

    

## 抽象思维

### 1）事不过三

- 同样的代码写三遍，就应该抽成一个函数
- 同样的属性写三遍，就应该做成**共用属性（抽出原型或类）**
- 同样的原型写三遍，就应该用**继承**；
    不同类有同样的方法，就应该让不同类**继承**自同一个父类

代价：
有时候会造成继承层级太深，无法一下看懂代码，可以通过写文档、画类图解决。

示例：

**1.1) 用哈希表或数组替换 if 条件语句**

```js
var video_status = 'loadedmetadata'

function foo(a) {
    if (a === 'loadedmetadata') {
        video_status = 'loadedmetadata'
    } else if (a === 'play') {
        video_status = 'play'
    } else if (a === 'playing') {
        video_status = 'pause'
    } else if (a === 'ended') {
        video_status = 'pause'
    }
}

function foo2(a) {
    const VIDEO_EVENTNAME = ['playing', 'play', 'waiting', 'pause', 'ended', 'loadedmetadata']
    if (VIDEO_EVENTNAME.includes(a)) {
        video_status = a
    }
}
```

**1.2) 用哈希表替换 switch 语句**

以随机选择东南西北为例，看看`switch`语句和`hashMap`实现的差异。

switch 语句

```javascript
let foo = Math.floor(Math.random()*4); // 随机产生一个 1 到 4 的整数
let output = 'Output: ';
switch (foo) {
  case 0:
    output += 'east';
  case 1:
    output += 'south';
  case 2:
    output += 'west';
  case 3:
    output += 'north';
  default:
    console.log('Please pick a number from 0 to 3!');
}
```

更换为 hashMap

```javascript
let foo = Math.floor(Math.random() * 4);
let output = 'Output: ';
// 对象的键名如果是数值，会被自动转为字符串
let hashMap = {
    0: 'east',
    1: 'south',
    2: 'west',
    3: 'north',
    default: 'Please pick a number from 0 to 3!'
}
// 虽然传入键名 foo 是数字，但会被自动转换为字符串
output += hashMap[foo || default]
```

总结：用 hashMap 代替 switch 语句

- 优点：少写了很多`case`和`output`
- 缺点：

  - `default:`需要用短路逻辑实现，~~代码不是很好读，对新手来说不是很好理解~~。

### 2）表驱动编程

「表」指的是数据结构，如哈希表、数组等。

表驱动法就是一种编程模式(scheme)——从表里面查找信息而不使用逻辑语句(if 和 case)。对简单的情况而言，使用逻辑语句更为容易和直白。但**随着逻辑链的越来越复杂，查表法会更具吸引力**。

```js
// 表驱动编程示例
function translate(number) {
  let numberList = {
  '1': '一',
  '2': '二',
  '3': '三'
  }
  return numberList[number];
}
```

表驱动编程可以减少重复代码，只将重要的信息放在表里，然后利用表来编程。当你看到大批类似但不重复的代码，看看哪些是不同的数据，把这些重要数据做成哈希表，代码就简单了。

示例：在`autobindEvents`函数中根据`events`哈希表自动绑定事件。

![示例](https://cdn.nlark.com/yuque/0/2022/png/29373291/1667298874991-a70373a4-1503-4b9a-80bc-afada89d65fb.png)
