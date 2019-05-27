#正文 Console API
- console.assert
- console.clear
- console.count
- console.debug
- console.dir
- console.dirxml
- console.error
- console.group
- consoel.groupCollapsed
- consoel.groupEnd
- consoel.info
- consoel.log
- consoel.profile
- consoel.profileEnd
- consoel.time
- consoel.timeEnd
- consoel.timeStamp
- consoel.trace
- consoel.warn
## console.log()

对于程序员，我们最常用 `console.log()` `console.info()` `console.debug()`等等，来记录我们代码中的操作

举例
```javascript
for(let i = 0; i < 10; i++) {
    console.log('i:', i)
}
// 控制台打印出 `i: 0` 一直到 `i: 9`
```
当然，我们也可以用来做别的，就像百度一样，在首页种植一个招聘菜单
```javascript
console.log(0)
```