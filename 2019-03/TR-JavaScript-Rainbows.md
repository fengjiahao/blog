
# 翻译 - Making annoying rainbows in javascript
## 标题：使用JavaScript制作令人困扰的彩虹🌈

> 译者序：文章是本人逛知乎，在`vue.js`作者尤大的[回答][0]中看到的一篇文章，很早就想翻译一下，但是无奈各种原因推迟至今
> 
> 附：[原文链接][1] [作者Twitter][2]
>
> 译者邮箱： <feng_jh2011@163.com>

2012/6:你会在[Jason Cohen's][3]的博客上，发现一个很棒且令人兴奋的文章-关于色觉如何工作。他观点来自于一个很长且深入的[文章][4],这篇文章值得你去花时间阅读。

2011/12:Charlie Loyd写了一个很好的[分析文章][5]针对本文所介绍的技术，并提出了一个很好的例子，将 `正弦-彩虹` （他称之为"Sinebow"）比作一个更常见的 `HSB-彩虹`
> 译者注：这里的`HSB-彩虹`原文为`HSB-space rainbows`，`HSB`是一种色彩空间命名，我们熟知的`RGB`也是其中一类。

2011/1:自从我编写本教程以来，`css3`引入了`hsla()`颜色规范，这使得本文中所阐述的各种技巧，在支持`css3`的浏览器上更加容易复现。因为我的目的是教一些简单的色彩理论和应用数学，我建议您在阅读本文时假装此功能不存在。之后，随意随意使用它！然而，如你所见，这里介绍的`Sinebow`比`HSL`色调在审美上更有进步。

---

这是一个关于如何生成离散颜色序列的教程，可以用于字体、图形或表格中。例如此处显示的色彩带，和本文的标题（注：标题的字体用了`css3`的渐变阴影）。

![rainbows-pic-01](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-01.png)

显然，如果你过度使用这项技术（我的意思是过度使用），你就会让网页看起来非常烦人，但是你可能会在学习这项技术时很开心，并且你会提高你的JS技巧，以及你对颜色如何工作的理解。

![rainbows-pic-02](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-02.png)

我将使用`JavaScript`作为我的代码示例，但是在任何语言中都可以使用相同的基本技术，事实上，我最初在`C`中开发了这些技巧，但现在我在`C++`、`Perl`、`Processing`、`Java`、`Flash ActionScript`和其他语言中使用了相同的老技巧。

![rainbows-pic-03](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-03.png)

为了充分了解本教程，您需要了解一点`JavaScript`和一点`HTML`知识。但是，你不需要知道太多。我是说，拜托。我甚至解释了十六进制在这个文档中是如何工作的，以及为什么`#ff0000`会变成红色等等…

![rainbows-pic-04](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-04.png)

Well, let's get to it, shall we?

## 将`RGB`值转换成`HTML`16进制表示法

在电脑显示器上，你可以通过结合红、绿和蓝来获得不同的颜色。红色、绿色和蓝色级别称为`颜色组件`。

为了生成彩虹一样的的颜色序列，我将操纵每个后续颜色的单个RGB组件。所以我的第一件事是需要一个功能函数，来把单个的`红`，`绿`，`蓝`转换为16进制的`HTML颜色规范`，看起来像这样：

> 例如 #AABBCC

这个代表（上面的例子）每一个`RGB`颜色组件都有一个`8位`(2^8=256)个值。在`8位`颜色系统中，颜色组件的值`R`，`G`，`B`可以从0到255。因为`HTML颜色规范`要对每个组件使用16进制表示法，所以每个组件占用两位数字。

在16进制中，每个颜色组件（<font style="color:red">红</font>、<font style="color:green">绿</font>、<font style="color:blue">蓝</font>）占用两个数字，例如：**#<font style="color:red">rr</font><font style="color:green">gg</font><font style="color:blue">bb</font>**。所以我们的颜色有以下十六进制值：

| Component | Value (16进制) |
| :-------- | :------------: |
| Red       |       AA       |
| Green     |       BB       |
| Blue      |       CC       |

16进制数以16个数字为基数。您可以从以下表格中将两位数的`16进制数`转换为其`10进制`:

<table border="1" cellpadding="8" cellspacing="0"><tbody><tr><td>Hexadecimal</td><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>A</td><td>B</td><td>C</td><td>D</td><td>E</td><td>F</td></tr>
<tr><td>Decimal</td><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td><td>14</td><td>15</td></tr>
</tbody></table>

如果你手头没有一个能进行16进制->10进制转换的计算器，你自己也可以做到。只需取第一个数字，将其转换为10进制（使用上表，这是很容易记住的），乘以16，然后添加第二个数字的十进制等价物。例如，数字`AB`对应于10*16+11=`171`。

使用此公式，您将看到颜色aabbcc对应于以下值：

| Component | Value (16进制) | Value (10进制) |
| --------- | :------------: | :------------: |
| Red       |       AA       |      170       |
| Green     |       BB       |      187       |
| Blue      |       CC       |      204       |

在教程中，我还需要的是一个执行反向转换的函数-将`170,187,204`转换成`aabbcc`。我会这样使用它：

> output = RGB2Color(170,187,204);

我们知道字符串`#AABBCC`作为HTML输出，可以使用`document.write()`方法输出值：

```javascript
function RGB2Color(r,g,b){
    return '#' + byte2Hex(r) + byte2Hex(g) + byte2Hex(b);
}
```

我的转换函数还依赖另一个功能函数`bytetohex()`，它将每个颜色分量从数值（如`170`）转换为十六进制字符串（如`aa`）。

```javascript
function byte2Hex(n){
    var nybHexString = "0123456789ABCDEF";
    return String(nybHexString.substr((n >> 4) & 0x0F,1)) + nybHexString.substr(n & 0x0F,1);
}
```

此函数依赖于二进制算术运算符 `>>`和 `&`，后者将数字视为二进制，与十六进制密切相关。二进制数中的每4位（或二进制数字）对应于一个十六进制数字0-f。因此，此函数取数字最左边的4位（由n>>4给出，它将数字向右移动4位）并将其转换为十六进制，然后取数字最右边的4位（由n和0x0f给出，它屏蔽最右边的4位）和转换器把它译成十六进制。

我应该指出，在提供`printf`，`10进制`->`16进制`转换（以及通常的数字格式）要容易得多——这是javascript中非常缺少的功能。

>  译者注：这里的`printf`似乎在已经有了，`numObj.toString([radix])`，这个方法可以把number类型转成想要的进制，radix(2~36)

```javascript
console.log((170).toString(16))
// output: 'aa'
```

在`Perl(一种CGI脚本语言)`中，您可以在一行中进行相同的转换，如下所示：

```perl
output = sprintf '#%02x%02x%02x', $r, $g, $b;
```

如果你在没有旧式的CSS颜色规范下依然可以使用，你也可以更简单地生成颜色而不使用 `byte2hex`，例如:

```javascript
function RGB2Color(r,g,b){
    return 'rgb(' + Math.round(r) + ',' + Math.round(g) + ',' + Math.round(b) + ')';
}
```
## 正弦波周期
我将要用来制作颜色循环的基本工具是正弦波。在`javascript`中有一个函数`math.sin()`，它将生成一个平滑的波动值，从0到1到0到-1，再回到0。以下是一些示例：

```javascript
function makeSineTable(rows, f)
{
  var v = 0;

  document.write("<table><tr><td width=100>function</td><td width=60 align='right'>result</td><td width=40></td><td width=300>graph of result (with zero in the middle)</td></tr>");
  var graphWidth = 300;
  var lEdge, rEdge;
  for (var y = 0; y < rows; ++y, v += f)
  {
    var str = 'Math.sin(' + roundDecimal(v,2) + ');';
    var r = Math.sin(v);
    if (r < 0) {
      lEdge = graphWidth/2 + r*(graphWidth/2);
      rEdge = graphWidth/2;
    }
    else {
      lEdge = graphWidth/2;
      rEdge = graphWidth/2 + r*graphWidth/2;
    }
  var subTable = '<table cellpadding=0 cellspacing=0 width=300><tr>' +
                 '<td width=' + lEdge + '></td>' +
                 '<td width=' + (rEdge-lEdge) + ' bgcolor=\'black\'>&nbsp;</td>' +
                 '<td width=' + (300-rEdge) + '></td>' +
                 '</tr></table>';


  document.write('<tr>');
  document.write('<td>' + str + '</td>');
  document.write('<td align=right>' + roundDecimal(r,2) + '</td>');
  document.write('<td></td>');
  document.write('<td>' + subTable + '</td>');
  document.write('</tr>');
  }
  document.write('</table>');
}
// 作者制作的table
makeSineTable(40,.2);
```

<table><tbody><tr><td width="100">function</td><td width="60" align="right">result</td><td width="40"></td><td width="300">graph of result (with zero in the middle)</td></tr><tr><td>Math.sin(0.00);</td><td align="right">0.00</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="0" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(0.20);</td><td align="right">0.19</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="29.800399619259167" style="background-color:#333;">&nbsp;</td><td width="120.19960038074083"></td></tr></tbody></table></td></tr><tr><td>Math.sin(0.40);</td><td align="right">0.38</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="58.41275134629757" style="background-color:#333;">&nbsp;</td><td width="91.58724865370243"></td></tr></tbody></table></td></tr><tr><td>Math.sin(0.60);</td><td align="right">0.56</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="84.6963710092553" style="background-color:#333;">&nbsp;</td><td width="65.3036289907447"></td></tr></tbody></table></td></tr><tr><td>Math.sin(0.80);</td><td align="right">0.71</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="107.60341363492842" style="background-color:#333;">&nbsp;</td><td width="42.396586365071585"></td></tr></tbody></table></td></tr><tr><td>Math.sin(1.00);</td><td align="right">0.84</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="126.22064772118449" style="background-color:#333;">&nbsp;</td><td width="23.77935227881551"></td></tr></tbody></table></td></tr><tr><td>Math.sin(1.20);</td><td align="right">0.93</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="139.80586289508392" style="background-color:#333;">&nbsp;</td><td width="10.19413710491608"></td></tr></tbody></table></td></tr><tr><td>Math.sin(1.40);</td><td align="right">0.98</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="147.81745949826905" style="background-color:#333;">&nbsp;</td><td width="2.182540501730955"></td></tr></tbody></table></td></tr><tr><td>Math.sin(1.59);</td><td align="right">0.99</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="149.93604045622578" style="background-color:#333;">&nbsp;</td><td width="0.06395954377421731"></td></tr></tbody></table></td></tr><tr><td>Math.sin(1.79);</td><td align="right">0.97</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="146.0771446317293" style="background-color:#333;">&nbsp;</td><td width="3.922855368270689"></td></tr></tbody></table></td></tr><tr><td>Math.sin(1.00);</td><td align="right">0.90</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="136.39461402385228" style="background-color:#333;">&nbsp;</td><td width="13.605385976147716"></td></tr></tbody></table></td></tr><tr><td>Math.sin(2.19);</td><td align="right">0.80</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="121.27446057293855" style="background-color:#333;">&nbsp;</td><td width="28.72553942706145"></td></tr></tbody></table></td></tr><tr><td>Math.sin(2.40);</td><td align="right">0.67</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="101.31947708267265" style="background-color:#333;">&nbsp;</td><td width="48.68052291732735"></td></tr></tbody></table></td></tr><tr><td>Math.sin(2.60);</td><td align="right">0.51</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="77.32520577321964" style="background-color:#333;">&nbsp;</td><td width="72.67479422678036"></td></tr></tbody></table></td></tr><tr><td>Math.sin(2.80);</td><td align="right">0.33</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="50.24822252338569" style="background-color:#333;">&nbsp;</td><td width="99.75177747661431"></td></tr></tbody></table></td></tr><tr><td>Math.sin(3.00);</td><td align="right">0.14</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="21.16800120898" style="background-color:#333;">&nbsp;</td><td width="128.83199879102"></td></tr></tbody></table></td></tr><tr><td>Math.sin(3.20);</td><td align="right">-0.05</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="141.24387848586292"></td><td width="8.756121514137078" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(3.40);</td><td align="right">-0.25</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="111.66883469597519"></td><td width="38.33116530402481" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(3.60);</td><td align="right">-0.44</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="83.62193350577202"></td><td width="66.37806649422798" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(3.80);</td><td align="right">-0.61</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="58.22131635859199"></td><td width="91.77868364140801" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(4.00);</td><td align="right">-0.75</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="36.47962570381067"></td><td width="113.52037429618933" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(4.20);</td><td align="right">-0.87</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="19.263634137961702"></td><td width="130.7363658620383" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(4.40);</td><td align="right">-0.95</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="7.259688916572543"></td><td width="142.74031108342746" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(4.60);</td><td align="right">-0.99</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="0.9463494549802931"></td><td width="149.0536505450197" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(4.80);</td><td align="right">-0.99</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="0.5753086746239262"></td><td width="149.42469132537607" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(5.00);</td><td align="right">-0.95</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="6.161358800529285"></td><td width="143.83864119947071" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(5.20);</td><td align="right">-0.88</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="17.481801641977142"></td><td width="132.51819835802286" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(5.40);</td><td align="right">-0.77</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="34.08532686660209"></td><td width="115.91467313339791" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(5.60);</td><td align="right">-0.63</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="55.31000431915207"></td><td width="94.68999568084793" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(5.80);</td><td align="right">-0.46</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="80.30967308793674"></td><td width="69.69032691206326" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(6.00);</td><td align="right">-0.27</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="108.0876752701615"></td><td width="41.9123247298385" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(6.20);</td><td align="right">-0.08</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="137.53658957737593"></td><td width="12.463410422624065" style="background-color:#333;">&nbsp;</td><td width="150"></td></tr></tbody></table></td></tr><tr><td>Math.sin(6.40);</td><td align="right">0.11</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="17.48238072757445" style="background-color:#333;">&nbsp;</td><td width="132.51761927242555"></td></tr></tbody></table></td></tr><tr><td>Math.sin(6.60);</td><td align="right">0.31</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="46.73120452700718" style="background-color:#333;">&nbsp;</td><td width="103.26879547299282"></td></tr></tbody></table></td></tr><tr><td>Math.sin(6.80);</td><td align="right">0.49</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="74.11700267079169" style="background-color:#333;">&nbsp;</td><td width="75.88299732920831"></td></tr></tbody></table></td></tr><tr><td>Math.sin(7.00);</td><td align="right">0.65</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="98.54798980781877" style="background-color:#333;">&nbsp;</td><td width="51.452010192181234"></td></tr></tbody></table></td></tr><tr><td>Math.sin(7.20);</td><td align="right">0.79</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="119.05017957737329" style="background-color:#333;">&nbsp;</td><td width="30.949820422626715"></td></tr></tbody></table></td></tr><tr><td>Math.sin(7.40);</td><td align="right">0.89</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="134.80621437174426" style="background-color:#333;">&nbsp;</td><td width="15.193785628255739"></td></tr></tbody></table></td></tr><tr><td>Math.sin(7.60);</td><td align="right">0.96</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="145.18795080472307" style="background-color:#333;">&nbsp;</td><td width="4.812049195276927"></td></tr></tbody></table></td></tr><tr><td>Math.sin(7.80);</td><td align="right">0.99</td><td></td><td><table cellpadding="0" cellspacing="0" width="300"><tbody><tr><td width="150"></td><td width="149.78150180619082" style="background-color:#333;">&nbsp;</td><td width="0.21849819380918234"></td></tr></tbody></table></td></tr></tbody></table>


正弦波在**生活中很有用**，与你在课堂上学的不同，它们对所有与图形和音乐相关的地方都特别有用。

为了得到这个模式，我在`math.sin()`函数中输入了一个稳定增长的值。您可以使用循环生成一系列正弦波值，如下所示：
```javascript
var frequency = .3;
for (var i = 0; i < 32; ++i) {
//    Document.write( Math.sin(frequency * i) );
    console.log(Math.sin(frequency * i))
}
```
你可能已经注意到正弦波模式在`frequency * i`等于6.2时开始重复。它重复的精确值实际上是2π（2乘以π）或6.28318，恰好对应于半径为1的圆的圆周。正如你所知道的，正弦波和圆是密切相关的。正弦波的中点正好是π，或者3.14159…

现在，我要为π（pi）字符在80%的计算机中使用的san serif字体中的糟糕外观道歉。如果我使用<font face='Roman'>可爱的罗马字体，π字符会更容易识别</font>。遗憾的是，我没有，因为我发现san-serif字体的可读性更高(跑题咯~)

与`math.sin()`函数（称为弧度）一起使用的π单位和从0到360度（度数是一个古老的巴比伦单位，它们之间存在一种关系，巴比伦人非常喜欢60和360度这样的数字，因为所有的数字都是均匀的。尽管此时，巴比伦人还没有发明PI）

| Degrees | Radians |
| :-----: | :-----: |
| 0       | 0       |
| 90      | π/2     |
| 180     | π       |
| 270     | 3*π/2   |
| 360     | 2*π     |

You can produce a value for π in Javascript by saying:

```javascript
document.write(Math.PI);
```
像这样：

> 3.141592653589793

我个人已经把π记住了8个数字。真的有点浪费了灰质(应该是浪费脑细胞的意思)。

说到灰质…

## 使用正弦波制作灰色阴影
我可以使用`math.sin()`的返回值来创建波动的颜色值。但是，我需要做一些转换。如上所示，`math.sin（）`的返回值从-1变为1。我用来使RGB颜色从0到255的颜色分量。因此，我需要将范围（-1->1）中的数字转换为（0->255）。

这并不难，因为我们知道我们希望正弦值从某个中心值开始，然后上下移动一定的量。由于正弦波上升到1，下降到-1，它已经被规范化了（这意味着它的最大值是1，一个非常有用的数字）。我们可以把它乘以其他的数，它会上升到那个数，然后下降到这个数的负数。

对于我们的颜色值，如果我们使用255/2作为中心值，并使正弦值上下波动，振幅为255/2（这将使我们达到255，下波动为0），它将很好地工作。
	
```js
color_component = Math.sin(frequency*i)*255/2 + 255/2;
```

我通常用整数表示，如下：

```js
color_component = Math.sin(frequency*i)*128 + 127;
```
不完全一样，但足够接近

一般来说，当我们想用正弦波来摆动某个物体时，我们将用这个公式

```js
value = Math.sin(frequency*increment)*amplitude + center;
```

> 频率是一个控制波振荡速度的常数，增量是一个向上计数的变量，通常由环路振幅控制波的中心位置。(问题翻译)

现在我可以修改我原来的正弦波循环，并生成一系列测试颜色。

```js
var frequency = .3;
var amplitude = 127;
var center = 128;

for (var i = 0; i < 32; ++i)
{
   v = Math.sin(frequency*i) * amplitude + center;

   // &#9608; 是一个特殊字符编码
   document.write( '<font style="color:' + RGB2Color(v,v,v) + '">&#9608;</font>');
}

```
`&#9608;` <font>&#9608;</font>

在`document.write`中，我通过调用`rgb2color（v，v，v）`生成一种颜色。因为我在3次中传递相同的值，对于红色、绿色和蓝色，我将得到灰色。在灰色中，红色、绿色和蓝色具有相同的值。要查看颜色，我使用Unicode值&9608；打印出一个块字符。

这就是代码产生的——一种彩色正弦波：

![rainbows-pic-05](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-05.png)

为了好玩，我可以尝试使用一系列不同的频率值。这就是我将得到的，如果我添加一个额外的外循环来改变频率值。

```js
for (frequency = .01; frequency < 1; frequency += .05)
{
  for (var i = 0; i < 32; ++i)
  {
     lev = Math.sin(frequency*i) * 127 + 128;
     document.write( '<font style="color:' + RGB2Color(lev,lev,lev) + '">&#9608;</font>');
  }
  document.write(' frequency = ' + roundDecimal(frequency,3) + '<br>');
}
```

![rainbows-pic-06](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-06.png)

## 用异相正弦波制作彩虹

![rainbows-pic-07](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-07.png)

现在，正如我所说的，上面的渐变看起来是灰色的原因是我对红色、绿色和蓝色使用相同的值。我们可以尝试获取颜色的一种方法是在我们用于绿色和蓝色参数的v值上添加一个常量。

```js
RGB2Color(v,v,v)

RGB2Color(v,v+30,v+60)
```

这将产生以下颜色序列：

![rainbows-pic-08](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-08.png)

这很有意思，但这不是我想要的。一种更好的技术是通过这样做来产生3个异相正弦波：

```js
var frequency = .3;
for (var i = 0; i < 32; ++i)
{
   red   = Math.sin(frequency*i + 0) * 127 + 128;
   green = Math.sin(frequency*i + 2) * 127 + 128;
   blue  = Math.sin(frequency*i + 4) * 127 + 128;

   document.write( '<font color="' + RGB2Color(red,green,blue) + '">&#9608;</font>');
}
```

![rainbows-pic-09](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-09.png)

如您所见，我使用2和4来更改绿色和蓝色正弦波的对齐（或相位）。我选择2和4是因为它们几乎把正弦波（2π或6.2）的范围分成三个相等的部分，每个正弦波大约是一个周期的1/3，或120°，异相，就像这样：

```js
// red
makeColorGradient(.3,.3,.3,0,0,0);
// green
makeColorGradient(.3,.3,.3,2,2,2);
// blue
makeColorGradient(.3,.3,.3,4,4,4);
// rgb
makeColorGradient(.3,.3,.3,0,2,4);

```

![rainbows-pic-10](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-10.png)

如果我正好想要120度，我可以用 `2*math.pi/3`代替`2`，用`4*math.pi/3`代替`4`，这样就产生了几乎相同（但完美）的颜色循环：

![rainbows-pic-11](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-11.png)

然而，结果是如此接近，我认为2和4是很好的替代品。据说古埃及人认为π等于三。在本教程中，我们将像埃及人一样编写代码。

## 关于色调循环的更多信息

这一基本现象——三个120°的异相正弦波产生彩虹效应——实际上是从20世纪80年代中期开始，我就知道这一点。我最初只是通过使用正弦波来制造色彩（这是一种代替生活的频繁活动）来发现它。一旦我学会了这个技巧，我就开始使用它，每当我想要一个明亮的颜色分类。例如，我的惠特尼音乐盒中的圆点是用这个系统着色的。

后来，当我学习了更多关于颜色理论的知识时，我发现我所做的基本上是使颜色的色调在色轮的圆上移动。如果你把我在色相饱和圆上画出的路径画出来，你会发现它不是一个完美的圆，而是一个三叶图案，就像这样：

![rainbows-pic-12](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-12.png)

Note：我使用[处理语言](https://www.processing.org/)生成了这个示例。在javascript中实现这一点有点麻烦。如果你喜欢javascript中的图形编程，你会喜欢processing.js中的图形编程，你会喜欢我的处理博客（和未完成的书）：[The Joy of Processing](http://joyofprocessing.com/blog/)。

尽管它不能形成一个完美的色调圆，但正弦波三叶草可以形成一个很好的颜色渐变，而且只需要几行代码就可以很容易地获得彩虹效果。我实际上更喜欢它的“正确”的hsb/hsl色调周期，因为它有更一致的亮度。它最小化了辐条的黄色，青色和洋红，你可以在图片中看到。

现在，我将讨论如何修改函数，以生成更大的颜色分类和不同的阴影，例如粉彩。

## 概括

在接下来的几节中，我将使用一个通用函数来绘制这些颜色渐变。它允许我为每个颜色分量指定单独的频率和相位参数。代码如下：

``` js
function makeColorGradient(frequency1, frequency2, frequency3,
                             phase1, phase2, phase3,
                             center, width, len)
  {
    if (center == undefined)   center = 128;
    if (width == undefined)    width = 127;
    if (len == undefined)      len = 50;

    for (var i = 0; i < len; ++i)
    {
       var red = Math.sin(frequency1*i + phase1) * width + center;
       var grn = Math.sin(frequency2*i + phase2) * width + center;
       var blu = Math.sin(frequency3*i + phase3) * width + center;
       document.write( '<font color="' + RGB2Color(red,grn,blu) + '">&#9608;</font>');
    }
  }
```

这是绘制基本彩虹梯度的代码。

``` js
makeColorGradient(.3,.3,.3,0,2,4);
```

看起来是这样的：

![rainbows-pic-13](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-13.png)

## 制作粉彩

获取粉彩的基本技巧是更改颜色组件的范围，以便对颜色组件使用较浅的颜色值。因此，当我们转换正弦值，而不是转换为全范围（0->255）时，我们将转换为类似（205-255）的值。我们将最后两个参数（中心和宽度）更改如下。之前：

```js
// center = 128, width = 127
   makeColorGradient(.3,.3,.3,0,2,4, 128,127);
```

after:

```js
// center = 230, width = 25
   makeColorGradient(.3,.3,.3,0,2,4, 230,25);
```

在修改后的代码中，230是正弦波的中心，25是与该中心值的最大偏差。所以正弦波从230开始，上升到（230+25），下降到（230-25）。换句话说，它的范围是（205-255）。

这就形成了一条柔和的条纹：

![rainbows-pic-14](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-14.png)

我可以通过使用更宽的范围（中心=200，宽度=55）获得更暗的颜色，如：

![rainbows-pic-15](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-15.png)

## 通过增加频率获得更多条纹

当你改变频率值时，你可以让颜色变化得更快或更慢。到目前为止，我主要使用的频率值是.3，但这里还有一些其他值：

![rainbows-pic-16](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-16.png)

## 阶段性试验

对于基本的彩虹效应，我将每个正弦波的相位分离120度。如果我把相位放得更近会发生什么？让我们看看：

![rainbows-pic-17](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-17.png)

## 通过对每个组件使用单独的频率来获得更多的变化

我使用的另一个技巧可以产生更大的颜色变化，那就是将每个颜色分量放在不同的频率上。代码如下：

```js
redFrequency = .1;
grnFrequency = .2;
bluFrequency = .3;
center = 128;
width = 127;
makeColorGradient(redFrequency,grnFrequency,bluFrequency,0,2,4,center,width,50);
```

…下面是它的样子：

![rainbows-pic-18](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-18.png)

如果我把各个频道分开，你可以看到发生了什么：

![rainbows-pic-19](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-19.png)

使用三种不同的频率可能会产生饱和度值较低的颜色，例如上面例子中看到的黑色，或灰色或白色。当三个正弦波同时穿过同一个值时，就会发生这种情况。

## 让循环重复

假设您希望颜色循环每6步重复一次。你是怎么做到的？我的方法是使用一个频率值，它对应于2π的1/6。记住，正弦波每2π重复一次，所以这将使颜色每6个增量重复一次。这是密码……

```js
center = 128;
width = 127;
steps = 6;
frequency = 2*Math.PI/steps;
makeColorGradient(frequency,frequency,frequency,0,2,4,center,width,50);
```

它看起来是这样的：

![rainbows-pic-20](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-20.png)

## 使循环不重复

如果你不想让颜色重复，那就用频率值，它不等于2π。事实证明，2.4这个数字在这方面很有效。2.4在弧度上，非常接近黄金角（137.51°），这是许多植物生长新的嫩枝的角度，以最大限度地利用树叶接收的阳光。在这里，我使用的基本上是相同的技术——我正在最大化颜色重复之间的距离（就像在茎周围重叠的叶子）。如果要为饼图或图形选择颜色，并且不确定需要绘制多少数据值，则使用2.4是一个很好的频率。这是密码。

```js
center = 128;
width = 127;
frequency = 2.4;
makeColorGradient(frequency,frequency,frequency,0,2,4,center,width,50);
```

And here's what it looks like with a frequency of 2.4:

![rainbows-pic-21](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-21.png)

如果你将这一点与对每种颜色使用不同频率的技巧结合起来，并确保每个频率都不是彼此的倍数，你就可以得到更多的变化。这里我使用的频率是1.666、2.666和4.666。

```js
center = 128;
width = 127;
redFrequency = 1.666;
grnFrequency = 2.666;
bluFrequency = 3.666;
makeColorGradient(redFrequency,grnFrequency,bluFrequency,0,0,0,center,width,50);
```

![rainbows-pic-22](https://github.com/fengjiahao/blog/blob/master/2019-03/images/rainbows-pic-22.png)

最后，这里是一个函数，它对文本行产生彩虹效果。

```js
function colorText(str,phase)
{
    if (phase == undefined) phase = 0;
  center = 128;
  width = 127;
  frequency = Math.PI*2/str.length;
  for (var i = 0; i < str.length; ++i)
  {
     red   = Math.sin(frequency*i+2+phase) * width + center;
     green = Math.sin(frequency*i+0+phase) * width + center;
     blue  = Math.sin(frequency*i+4+phase) * width + center;
     document.write( '<font style="color:' + RGB2Color(red,green,blue) + '">' + str.substr(i,1) + '</font>');
  }
}
```

## 附录(出现的js方法)
```js

function RGB2Color(r,g,b)
{
  return '#' + byte2Hex(r) + byte2Hex(g) + byte2Hex(b);
}

function byte2Hex(n)
{
  var nybHexString = "0123456789ABCDEF";
  return String(nybHexString.substr((n >> 4) & 0x0F,1)) + nybHexString.substr(n & 0x0F,1);
}


function roundDecimal(v,n)
{
  var isNeg = v < 0;
  v = Math.abs(v);

  return (isNeg? '-' : '') + String(Math.floor(v)) + '.' + String((1 + Math.abs(v) - Math.floor(Math.abs(v)))*Math.pow(10,n)).substr(1,n);
}

function colorText(str,phase)
{
  if (phase == undefined)
    phase = 0;
  center = 128;
  width = 127;
  frequency = Math.PI*2/str.length;
  for (var i = 0; i < str.length; ++i)
  {
     red   = Math.sin(frequency*i+2+phase) * width + center;
     green = Math.sin(frequency*i+0+phase) * width + center;
     blue  = Math.sin(frequency*i+4+phase) * width + center;
     document.write( '<font style="color:' + RGB2Color(red,green,blue) + '">' + str.substr(i,1) + '</font>');
  }
}

function makeColorGradient(frequency1, frequency2, frequency3, phase1,phase2,phase3,center,width,len)
{
  if (len == undefined)
    len = 50;
  if (center == undefined)
    center = 128;
  if (width == undefined)
    width = 127;
  for (var i = 0; i < len; ++i)
  {
     var red = Math.sin(frequency1*i + phase1) * width + center;
     var grn = Math.sin(frequency2*i + phase2) * width + center;
     var blu = Math.sin(frequency3*i + phase3) * width + center;
     document.write( '<font style="color:' + RGB2Color(red,grn,blu) + '">&#9608;</font>');
  }
}

```


[0]: https://www.zhihu.com/question/40385011/answer/86314996
[1]: https://krazydad.com/tutorials/makecolors.php
[2]: https://twitter.com/jbum
[3]: https://blog.asmartbear.com/color-wheels.html
[4]: http://www.handprint.com/HP/WCL/color1.html
[5]: http://basecase.org/env/on-rainbows
