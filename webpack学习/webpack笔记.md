# 官网：

https://webpack.docschina.org/

# 认识

webpack是专门的打包工具，可以将前端语言，vue,ts等打包成浏览器能够直接运行的html代码，js代码等。

# 不足：

webpack目前可以直接打包es语法，但是将es语法进行降级处理，兼容浏览器的时候，对新特性promise,箭头函数无法直接处理，所以需要结合配置babel，进行降级处理，如果浏览器版本要求比较新，支持es6及以上的语法，就不用进行babel降级处理。

# 可以处理多种前端语言：

- vue

博客连接：

https://blog.csdn.net/m0_46692959/article/details/106349631

**1、需要下载vue的loader来加载到vue文件:**

```javascript
# vue-loader 负责加载vue文件
# vue-template 负责 vue-模板-编译

1、npm install vue-loader vue-template-compiler --save-dev
2、npm install vue-loader vue-template-compiler -D
```

**2、配置webpack.config.js文件中vue的loader配置**

```javascript
# 在rules数组中加入：
{
    test：/\.vue$/,//匹配以vue结尾的文件
    use:['vue-loader']
}
```

```javascript
# 如果运行报错，则是因为因为 vue-loader 14版本以后需要配置插件才可以加载vue文件，
# 所以这里可以选择去package.json中把vue-loader的版本改成13.0.0这样它就只会下载13-14之间的版本
# 改完之后直接打入 npm install 来安装一下改的依赖
```



- 