# 关于window下的btoa、atob和Base64.js

Base64是网络上最常见的用于传输8Bit字节码的编码方式之一，Base64就是一种基于64个可打印字符来表示二进制数据的方法。

在Javascript中提供了操作base64的方法，挂载在window全局对象下面，分别是：

* window.btoa （binary to ascii)  对binary进行base64编码

* window.atob （ascii to binary）对ascii 字符串进行解码

### btoa、atob使用方法

```js
let encodedData = window.btoa("Hello, world"); // SGVsbG8sIHdvcmxk
let decodedData = window.atob(encodedData); // Hello, world
```

这么一看Javascript对base64操作也挺简单的，那么为什么还需要Base64.js npm包呢，带着疑问我们来使用一下Base64js，了解这个包有何不同。

### Base64.js

安装

```bash
npm install --save js-base64
```

base64js 提供了encode方法进行编码，decode方法进行解码

```javascript
import { Base64 } from 'js-base64';
Base64.encode('Hello, world');  // SGVsbG8sIHdvcmxk
Base64.decode('SGVsbG8sIHdvcmxk');  // Hello, world
window.atob('SGVsbG8sIHdvcmxk'); // Hello, world
```

使用encode对英文字符进行base63编码，发现和btoa的结果是一致的， atob也可以对base64js encode的值进行解码。上面我们只对英文字符进行了测试，接下来对中文字符进行测试。

```javascript
Base64.encode('你好世界') // 5L2g5aW95LiW55WM
Base64.decode('5L2g5aW95LiW55WM') // 你好世界
window.atob('5L2g5aW95LiW55WM'); // ä½ å¥½ä¸ç
```

#### btoa不支持中文

测试下来发现atob对Base64js 编码后的值进行解码后结果出现了乱码，而Base64js自有的decode却能成解析。所以初步判断原生的btoa、atob对中文不支持。接下来再单独对原生方法进行中文编解码测试。

```javascript
window.btoa('你好世界')
// 错误
VM1615:1 Uncaught DOMException: Failed to execute 'btoa' on 'Window': The string to be encoded contains characters outside of the Latin1 range.
```

#### btoa为什么不支持中文

错误指出我们要进行编码的字符串没有在Latin1的范围里面，那Latin1的范围是指的什么呢，我在MDNz查看btoa的解释。

> **Note**: 由于这个函数将每个字符视为二进制数据的字节，而不管实际组成字符的字节数是多少，所以如果任何字符的码位超出 0x00 ~ 0xFF 范围，则会引发 `InvalidCharacterError` 异常。请参阅 [Unicode_字符串](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowBase64/btoa#Unicode_字符串) ，该示例演示如何编码字符数超出 0x00 ~ 0xFF 范围的字符串

而Latin1的范围指的是ISO-8859-1标准，兼容ASCII其编码，范围就是0x00-0xFF（0-255）。因为中文是unicode字符，并没有在0x00-0xFF范围内所以抛出异常。

### base64js是如何支持unicode字符的？

带着疑问我对base64js的源码进行了阅读，发现encode方法及将传入所有字符先用字符串的replace方法进行正则替换为二进制字符

![base64js](https://github.com/lemontree2000/blog-note-image/blob/master/images/base64js-tou.png?raw=true)

#### 如何使btoa、atob支持unicode字符

上面说了base64js 作者只是先让unicode字符手动转为二进制字符

```javascript
btoa(unescape(encodeURIComponent('你好世界'))) // 5L2g5aW95LiW55WM
decodeURIComponent(escape(atob('5L2g5aW95LiW55WM'))) // 你好世界
Base64.decode('5L2g5aW95LiW55WM') // 你好世界
```

我们先用encodeURIComponent方法对中文字符串其中的某些字符将被十六进制的转义序列进行替换。再对得出的结果再进行unescape反编码， 反编码后的乱码的字符的二进制是在0x00 ~ 0xFF 范围内，这时候我们就可以使用btoa方法了。反之亦然，所以我们可以将其封装为方法。

```javascript
// base64编码
function base64_encode(string) {
	return btoa(unescape(encodeURIComponent(string)));
}

// base64解码
function base64_decode(string) {
	return decodeURIComponent(escape(atob(string)))；
}
```

#### 为什么base64js不使用unescape和escape

base64js为什么不使用unescape和escape进行二进制字符的转换，而去手动进行位运算的转换。原因是unescape方法在ECMAScript v3 已从标准中删除了 unescape() 函数，并反对使用它。现在浏览器虽然对unescape进行了兼容，但是未来可能有方法移除的风险。所以建议还是使用base64进行编解码。



## 总结

* window.btoa 只支持 0x00 - 0xFF二进制范围内的字符，不支持unicode字符
* window.btoa 可以使用unescape和escape解决unicode字符支持的问题
* unescape已被标准废弃，未来可能被浏览器移除，建议使用base64js来代替