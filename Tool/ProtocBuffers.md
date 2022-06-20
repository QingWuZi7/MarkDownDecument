Protocol Buffers

支持较复杂结构。用于数据交互。由google开发，语言中立平台中立（跨平台）

二进制数据保存方式，数据小，解析效率高，开发简单，





> 前端需要protobuf.js这个库来处理proto文件。
>
> .proto文件由后端定义和维护，可直接做前后端沟通的文档。



protobufjs提供了pbjs工具，根据参数不同可打包成xx.json或xx.js文件.

`pbjs -t json xx.proto > xx.json`

`pbjs xxx.proto > xxx.json`将proto编译成json

> 其实使用json格式与使用proto格式没什么大的差别。读过源码的话知道，protobufjs库加载proto文件的顺序大致如下：
>
> 1加载proto文件 2将获取的proto字符串，解析为json对象 3build操作将json对象转换为proto对象
>
> 使用预编译json加载相当于省略了第二步，直接加载json文件转换proto对象。
>
> 当proto文件比较多的时候，使用json加载可以提高一些效率。

`pbjs -t commonjs xxx.proto > xxx.js` 用pbjs -t的参数将proto文件编译成目标格式。(这里转成了js文件)

js中会有

`require ('protobufjs')`导入protobuf模块

```js
import protoRoot from '@/proto/proto'
import protobuf from 'protobufjs'
//请求体message,取决于.proto文件定义
const a = protoRoot.lookup('PackageName.MessageName');
//响应体
const b = protoRoot.lookup('PackageName.MessageName');
```







---

`%ERRORLEVEL%`查看上个命令的返回值

0-成功执行`>=1`命令失败

