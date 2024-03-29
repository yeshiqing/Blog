## 一、缩略词的全名

| abbreviation |                        full form                         |                      note                      |
| :----------: | :------------------------------------------------------: | :--------------------------------------------: |
|     JSON     |                JavaScript Object Notation                |              轻量级的数据交换格式              |
|     OSS      |                   Open-source software                   |                    开源软件                    |
|     OSS      |                  Object Storage Service                  |         对象存储服务，一种云存储服务。         |
|     RSS      | RDF Site Summary<br />or<br /> Really Simple Syndication | 资源描述框架站点摘要<br />或<br />简易咨询聚合 |
|     SEO      |                Search Engine Optimization                |                  搜索引擎优化                  |
|     XML      |                Extensible Markup Language                |                 一种标记式语言                 |
|     Zsh      |                         Z shell                          |             Zsh 开源框架 Oh My Zsh             |

## 二、名称中的大小写与空格

| 网站英文名      | 备注                                                         |
| --------------- | ------------------------------------------------------------ |
| Stack Overflow  | 一个程序设计领域的问答网站，隶属 Stack Exchange Network。    |
| GitHub          | 在线软件源代码托管服务平台                                   |
| VS Code         | 全称 Visual Studio Code，由微软开发的源代码编辑器            |
| WebStorm        | JavaScript IDE                                               |
| Chrome DevTools | Chrome 开发者工具                                            |
| Markdown        | 轻量级标记语言                                               |
| macOS           | 以后要写作“macOS”，而不要写作“Mac 操作系统”，Mac 指苹果电脑而非系统。 |

## 三、计算机科学术语

### Call Stack

```js
function bar() {
    debugger // Paused in debugger
    console.log(this); // window
}
function foo() {
    bar() // 等价于 window.bar() 所以 bar 中的 this 是 window
}
foo()
```

调用栈信息：
![image.png](https://cdn.jsdelivr.net/gh/yeshiqing/imgHosting/blog-pictures/20221229174815.png)

- 函数 foo 叫做 bar 的「调用者」（caller），**即 foo 中调用了 bar**。
- 函数 bar 叫做 foo 的「被调用者」（callee），**即 bar 在 foo 中被调用**。
- 【注意】bar 的 this 指向 window 并不指向 foo。

### HashSet 和 HashMap

| HashSet                                           | **HashMap**                        |
| ------------------------------------------------- | ---------------------------------- |
| **不允许重复元素**，无法在 HashSet 中存储重复值。 | **不允许重复键**，但它允许重复值。 |
