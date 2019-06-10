# NodeJs npm安装包管理工具，常用命令

## package.json中的部分条目

### dependencies

指的是在`production`环境下需要的安装包

```javascript
// 添加到您package.json的 dependencies：

`npm install <package_name> --save`
```

### devDependencies

仅仅在`development`和`testing`环境时需要的安装包

```javascript
// 添加到您package.json的 devDependencies：

`npm install <package_name> --save-dev`
```

### optionalDependencies

如果某些`package`无法安装，但是你仍然想让npm install继续下去，你可以把这些不确定是否可以安装的`package`放到此项下面

但是确保程序还是要安装这些`package`
```javascript
try {
  var foo = require('foo')
  var fooVersion = require('foo/package.json').version
} catch (er) {
  foo = null
}
if ( notGoodFooVersion(fooVersion) ) {
  foo = null
}

// .. then later in your program ..

if (foo) {
  foo.doFooThings()
}
```
注：`optionalDependencies`中的配置会覆盖`devDependencies`,通常最好的选择是写在一个地方

---

## npm install

将安装程序包及其依赖的任何程序包

> `npm install (无参数)`

将安装列为依赖项的所有模块 `package.json`

> `-D, --save-dev`

安装包将出现在 `devDependencies`

> `-O, --save-optional`

安装包将出现在 `optionalDependencies`

> `--no-save`

防止保存到 `dependencies`

> `-P, --save-prod`

安装包将出现在 `dependencies`，这个是默认的，除非使用 `-D` or `-O`

> `-g, --global`

安装包将全局安装

## npm run 

从 `package.json` 的 `scripts` 对象运行任意命令

```javascript
// vue cli3 中的 package.json
// 执行 npm run serve
// 实际上是 npm run node_modules/.bin/vue-cli-service serve
"scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
}
```

