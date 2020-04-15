# 总结常用的JavaScript小方法

- [随机ID](#随机ID)
- [RGB色值生成16进制色值](#RGB色值生成16进制色值)
- [颜色混合](#颜色混合)
- [颜色混合](#颜色混合)

## 随机ID

代码如下

```javascript

const genUid = () => {
  var length = 20
  var soupLength = genUid.soup_.length
  var id = []
  for (var i = 0; i < length; i++) {
    id[i] = genUid.soup_.charAt(Math.random() * soupLength)
  }
  return id.join('')
}
genUid.soup_ = '!#$%()*+,-./:;=?@[]^_`{|}~ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
genUid()

```

## RGB色值生成16进制色值

代码如下


```javascript

const rgb2Hex = rgb => {
    let rgbList = rgb.toString().match(/\d+/g)
    let hex = '#'
    for (let i = 0, len = rgbList.length; i < len; ++i) {
      hex += ('0' + Number(rgbList[i]).toString(16)).slice(-2)
    }
    return hex
};
rgb2Hex('100, 50, 0')

```

## 颜色混合

代码如下

```javascript

const colourBlend = (c1, c2, ratio) => {
  ratio = Math.max(Math.min(Number(ratio), 1), 0)
  let r1 = parseInt(c1.substring(1, 3), 16)
  let g1 = parseInt(c1.substring(3, 5), 16)
  let b1 = parseInt(c1.substring(5, 7), 16)
  let r2 = parseInt(c2.substring(1, 3), 16)
  let g2 = parseInt(c2.substring(3, 5), 16)
  let b2 = parseInt(c2.substring(5, 7), 16)
  let r = Math.round(r1 * (1 - ratio) + r2 * ratio)
  let g = Math.round(g1 * (1 - ratio) + g2 * ratio)
  let b = Math.round(b1 * (1 - ratio) + b2 * ratio)
  r = ('0' + (r || 0).toString(16)).slice(-2)
  g = ('0' + (g || 0).toString(16)).slice(-2)
  b = ('0' + (b || 0).toString(16)).slice(-2)
  return '#' + r + g + b
}
colourBlend('#ff0000', '#3333ff', 0.5) // "#991a80"

```


































## title02