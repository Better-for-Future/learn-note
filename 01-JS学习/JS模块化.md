# 规范类型

1. commonjs
2. AMD
3. ES6
4. CMD(次要)

- 一个<script>标签，会发起一次http请求，一个js文件就是一个js模块
- 模块的引入方式
  - 【modules.export】:使用的是commonjs标准，暴漏到windows对象上赋值，静态编译阶段
  - 【require】:运行时
- 【IIFE(即时diaoy函数表达式)】：插件工具里面经常用，模块化

- Teminal终端打开文件夹可以直接拖拽

## 一、CommonJS规范

- 服务器端用commonjs，浏览器端用Browerserify将后端node代码以及依赖打包成前端可以用的js文件
- 模块的加载是同步（阻塞）的
- node项目结构
  - modules
  - app.js 汇聚其他模块的主模块
  - 还有一个package.json，可以用命令行，在根目录：npm init生成
    - 生成的package name是包名：小写，要求非大写，非中文，最新版本会自动转包名小写
    - 最主要的是name,version
- 暴露模块
  - module.exports=value  暴露一个对象，写多个，最后一个exports会覆盖前边的对象
  - exports.xxx=value
- 引入模块require(xxx)
  - 自定义模块，require('模块路径')，一个js文件就是一个模块
  - 第三方模块，xxx为模块名，即包名
  - 注意：引入第三方模块的时候，需要放在自定义的模块上方
  - - 
  - https://www.npmjs.com 寻找模块的网站（npm官网）
  - 下载第三方模块
    - npm install 模块名 --save(npm5开始，--save是一个默认属性)
  - 运行app.js文件：node app.js

## 二、AMD(RequireJS)规范

### 说明：

- 专门用于浏览器端，模块的加载是异步的，早于CommonJS的Broserify

### 基本语法：

- 定义暴露模块

  - ```javascript
    //定义无依赖的模块
    define(function(){
        return 模块
    })
    ```

  - ```javascript
    //定义有依赖的模块
    define(['module1','module2'],function(m1,m2){
        return 模块
    })
    ```

- 引入使用模块

  - ```javascript
    require(['module1','module2'],function(m1,m2){
        使用m1/m2
    })
    ```

### 实现（浏览器端）

- 需要依赖库Require.js
- http://www.requirejs.cn
- 

## 三、CMD（不推荐）

### 说明：

commonjs和requirejs语法都可以使用，目前已经被国外收购

## 四、ES6

### 基本语法

- 定义暴露模块: export

  - 常规暴露

    - 分别暴露：js文件定义多个，每个export一个具体对象

    - ```javascript
      //module1.js
      export function foo(){
          console.log('foo() module1');
      }
      export function bar(){
          console.log('bar() module1');
      }
      export let arr = [1,2,3,4,5];
      ```

    - 统一暴露：js文件定义多个函数，变量，js文件最后统一export暴露

    - ```javascript
      //module2.js
      function fun(){
          console.log('fun() module2');
      }
      function fun2(){
          console.log('fun2() module2');
      }
      export {fun,fun2};
      ```

    - 

  - 默认暴露：

    - 默认暴露在export后多加了一个default，import引入时可以不用使用对象解构引入，可以直接引入

    - ```javascript
      语法：export default value;
      例子：
      	export default () => {
              console.log('我是默认暴露的箭头函数');
          }
      //默认暴露只写一次，多个数据可以暴露到一个对象中
      ```

    - 

- 引入模块：

  - ```javascript
    //import引入自定义模块
    //使用对象解构的方法引入：
    //import {xxx,yyy} from '路径'；
    import {foo,bar} from './module1';
    import {fun,fun2} from './module2';
    import module3 from './module3';
    
    //import module1 from 'module1';
    //console(module1); //unfefine,说明没有引入
    
    
    foo();
    bar();
    fun();
    fun2();
    module3();
    //能正常打印出来
    
    
    ```

  - ```javascript
    //引入第三方模块
    
    ```

  - 

  - 如果前端使用，需要转ES5,browserify编译,然后前端引用

```javascript
//cli：command line interface:用于执行命令行交互
需要引入：babel-core,babel-cli,babel-preset-es2015,browserify
//.babelrc文件用于定义：runtime control,转编译es5用
{
    "presets":[
        "@babel/preset-env"
    ]
}
```

```javascript
//新版用法
// ES6语法 转为ES5 babel配置
// 命令： npm init -y =>创建package.json文件

// 命令：npm i @babel/core @babel/cli @babel/preset-env -D
// 命令：npm i @babel/polyfill -S
// 进入到项目package.json 文件中： 添加快速命令，npm run build ,并在根目录下新建一个src文件
babel src -d dest
//会在dest目录生成ES5语法的js文件
browserify es5.js -o js/build/bundle.js//编译生成浏览器能用的文件
```

```javascript
// ES6自动编译ES5,需要配合webpack
```



