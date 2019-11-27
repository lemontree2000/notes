# 多媒体与嵌入



## 目录

* HTML中的图片-img

  * 属性

    * src
    * alt
    * width&height
    * title
    
  *  figure
  
  * CSS 背景图片
  
* 视频和音频内容
  
  * 安装
  
* 从`<object>`到`<iframe>` -其他嵌入技术

* 在页面中添加矢量图像

* 响应式图片



## HTML中的图片-img

最常用的多媒体标签

### 属性-`src`

`src`（一般读作其全称 *source*），`src` 属性包含了指向我们想要引入的图片的路径，可以是相对路径或绝对URL

> ```html
> <img src="images/dinosaur.jpg">
> ```

片存储在和 HTML 页面同路径的 `images` 文件夹下（利于SEO/索引），图片名称需有一个描述性的名称（搜索引擎也读取图像的文件名并把它们计入SEO）

```html
<img src="https://www.example.com/images/dinosaur.jpg">
```

网站内图片使用绝对路径，浏览器会重新通过 DNS 再去寻找 IP 地址，会多花费一些时间，使用网站外的图片可以采用cdn存储。

cdn存储可以通过dns-prefetch进行DNS预解析，加快域名访问速度。[更多内容](<https://www.cnblogs.com/xiaohuochai/p/9185622.html>)

```
<link rel="dns-prefetch" href="//img.alicdn.com">
```

### 属性-`alt`

`alt` 称为备用文本是对图片的文字描述，用于在图片无法显示或不能被看到的情况。

#### 为什么要用`alt`

* 提供无障碍使用
* 图片的路径或文件名拼错
* 提供一些文字描述来给搜索引擎使用` seo`
* 用户关闭的图片显示以减少数据的传输

#### 什么时候不需要用`alt`

* 图片只用做装饰-最佳实践应该用css来做

* 文本内容已经描述了图片的信息

* 图片嵌入在a标签里面，a标签里面已经有了图片相对于的描述信息


### 属性-`width`和`height`

图片的宽高应该在应该在视频前就得知，设置图片宽高时需要按照图片的宽高比来设置，不要然图片会变形。

（最佳实践：不应该通过属性去设置宽高，而是用CSS设置）

### 属性-`title`

`title`属性是用来提供需要更进一步的支持信息，图片标题并不必须要包含有意义的信息。

不推荐使用title，因为屏幕阅读器的支持是不可预测的（[更多不推荐的原因](https://www.24a11y.com/2017/the-trials-and-tribulations-of-the-title-attribute/)）

### figure标签

> 通过为图片搭配说明文字的方式来解说图片

无语义布局，这会给使用屏幕阅读的人造成问题，比如当你有 50 张图片和其搭配的 50 段说明文字，那么一段说明文字是和哪张图片有关联的呢？

```html
<div class="figure">
  <img src="/images/dinosaur_small.jpg"
     alt="一只恐龙头部和躯干的骨架，它有一个巨大的头，长着锋利的牙齿。"
     width="400"
     height="341">
  <p>曼彻斯特大学博物馆展出的一只霸王龙的化石</p>
</div>
```

使用HMTL5 ` <figure>` 和 `<figcaption> `元素，存在的意义：为图片提供一个语义容器，在标题和图片之间建立清晰的关联

```html
<figure>
  <img src="https://raw.githubusercontent.com/mdn/learning-area/master/html/multimedia-and-embedding/images-in-html/dinosaur_small.jpg"
     alt="一只恐龙头部和躯干的骨架，它有一个巨大的头，长着锋利的牙齿。"
     width="400"
     height="341">
  <figcaption>曼彻斯特大学博物馆展出的一只霸王龙的化石</figcaption>
</figure>
```

### CSS 背景图片

可用通过CSS 属性 `background-image`和另其他 `background-*` 属性是用来放置背景图片

#### 为什么还要用html插入图片

> CSS 背景图片只是为了装饰 — 如果你只是想要在你的页面上添加一些漂亮的东西，来提升视觉效果，那 CSS 的做法是可以的。但是这样插入的图片完全没有语义上的意义，它们不能有任何备选文本，也不能被屏幕阅读器识别。这就是 HTML 图片有用的地方了。
>
> 总而言之，如果图像对您的内容里有意义，则应使用HTML图像。 如果图像纯粹是装饰，则应使用CSS背景图片。

## WEB 中的音频和视频

HTML5标准之前是用flash和Silverlight展示音频和视频，缺点无法很好的支持 HTML/CSS 特性、安全问题，以及可行性问题

###  video 标签

```html
<video src="rabbit320.webm" controls>
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

* src -视频资源, 与img的src使用相同
* controls- 控制视频和音频的回放功能
* video标签内的段落可用作备用文本，当浏览器不支持video标签则会显示



#### 视频格式的支持

不同的浏览器对视频格式的支持不同。

MP3、MP4、WebM这些术语叫做**容器格式**。

- WebM 容器通常包括了 Ogg Vorbis 音频和 VP8/VP9 视频。主要在 FireFox 和 Chrome 当中支持。
- MP4 容器通常包括 AAC 以及 MP3 音频和 H.264 视频。主要在 Internet Explorer 和 Safari 当中支持。
- 老式的 Ogg 容器往往支持 Ogg Vorbis  音频和 Ogg Theora 视频。主要在 Firefox 和 Chrome 当中支持，不过这个容器已经被更强大的 WebM 容器所取代。

> **MP3** (音频格式) 和 **MP4/H.264** (视频格式) 是被广泛支持的两种格式,但由于专利的限制，导致浏览器厂商需要付高额的专利费，所以无法支持。在2017 MP3专利 已经解除了，但H.264 会持续到2027年早期。

#####  srouce 标签-处理多个格式的支持

```html
<video controls>
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

每个 `<source>` 标签页含有一个 `type` 属性，这个属性是可选的，建议加上。



#### video其他特性

```html
<video controls width="400" height="400"
       autoplay loop muted
       poster="poster.png">
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

* `width` 和 `height` 控制视频展示的区域大小，设置的时候注意控制**纵横比**
* autoplay 视频自动播放
* loop 视频循环播放
* muted 关闭视频声音
* poster 指向一个图像的URL，在播放前显示
* preload 用来缓冲较大的文件，有3个值可选：
  * `"none"` ：不缓冲
  * `"auto"` ：页面加载后缓存媒体文件
  * `"metadata"` ：仅缓冲文件的元数据(比如文件的大小，时长等)

### audio标签

`<audio>` 标签与 `<video>` 标签的使用方式几乎完全相同

与 HTML5 `<video>` 的差异如下：

- `<video>`标签不支持 `width`/`height` 属性 — 由于其并没有视觉部件，也就没有可以设置 `width`/`height` 的内容。
- 同时也不支持 `poster` 属性 — 同样，没有视觉部件。

### 显示音轨文本 （字幕）

> 提供给不能听到声音或者不想听到的人使用，更容易理解媒体内容。

HTML5推出了`<track>`标签，支持[WebVTT](https://developer.mozilla.org/en-US/docs/Web/API/Web_Video_Text_Tracks_Format) 格式的音轨文本，这个文本文件包含了众多的字符串，这些字符串会带有一些元数据，它们可以用来描述这个字符串将会在视频中显示的时间。可以文本里来修改字幕的样式及定位信息,也可以用css 代替。

```
WEBVTT

STYLE
::cue {
  background-image: linear-gradient(to bottom, dimgray, lightgray);
  color: papayawhip;
}
/* Style blocks cannot use blank lines nor "dash dash greater than" */

NOTE comment blocks can be used between style blocks.

STYLE
::cue(b) {
  color: peachpuff;
}

00:00:00.000 --> 00:00:10.000
- Hello <b>world</b>.

NOTE style blocks cannot appear after the first cue.
```



#### track标签

`<track>` 标签需放在 `<audio>` 或 `<video> 标签当中`，同时需要放在所有 <source> 标签之后。

* src 用来连接.vtt 文件
* kind属性来指明是哪一种类型
  * subtitles -通过添加翻译字幕，来帮助那些听不懂外国语言的人们理解音频当中的内容。
  * captions -同步翻译对白，或是描述一些有重要信息的声音，来帮助那些不能听音频的人们理解音频中的内容。
  * descriptions - 视频内容的文本描述,适用于失明用户或者当视频不可见的场景。
  *  srclang- 告诉浏览器你是用什么语言来编写的 subtitles
  *  label 显示在字幕选择列表里面

```html
<video controls>
    <source src="example.mp4" type="video/mp4">
    <source src="example.webm" type="video/webm">
    <track label="English" kind="subtitles" src="subtitles_en.vtt" srclang="en">
</video>
```

## 从对象到iframe - 其他嵌入技术

90年代中期至90年代后期，使用**框架**创建网站 — 网站的一小部分存储于单独的HTML页面中，这样有利于下载速度 —尤其是在那时网络连接速度太慢的情况下更为明显。然而，这些技术有很多问题，随着网络速度越来越快，这些技术带来的问题远超过它们带来的积极因素。

#### Iframe详解

iframe元素旨在允许您将其他Web文档嵌入到当前文档中，常用于评论系统，在线地图提供商，广告横幅、视频等。

```html
<iframe src="https://developer.mozilla.org/en-US/docs/Glossary"
        width="100%" height="500" frameborder="0"
        allowfullscreen sandbox>
  <p> <a href="https://developer.mozilla.org/en-US/docs/Glossary">
    Fallback link for browsers that don't support iframes
  </a> </p>
</iframe>
```

##### 属性

* allowfullscreen-iframe全屏模式
* frameborder-iframe绘制边框(0删除边框,不推荐这样设置)
* src -URL路径
*   width 和 height-指定iframe的宽度和高度。
*   sandbox-该属性可以提高安全性设置,更现代的浏览器上才能工作（例如IE 10及更高版本）
*   备选内容-与video等其他类似元素相同

##### 安全隐患

> [单击劫持](https://en.wikipedia.org/wiki/Clickjacking)是一种常见的iframe攻击，黑客将隐藏的iframe嵌入到您的文档中（或将您的文档嵌入到他们自己的恶意网站），并使用它来捕获用户的交互。这是误导用户或窃取敏感数据的常见方式。

如何再使用iframe时更安全

* 只有在必要时嵌入
* 使用 HTTPS

  * HTTPS减少了远程内容在传输过程中被篡改的机会，
  * HTTPS防止嵌入式内容访问您的父文档中的内容，反之亦然。
* 始终使用`sandbox`属性

  * 没有使用sandbox属性，iframe里面的内容可执行JavaScript，提交表单，弹出窗口等
  * 没有参数的`sandbox`属性来强制执行所有可用的限制
  * *永远不*应该同时添加`allow-scripts`和`allow-same-origin`到你的`sandbox`属性中-在这种情况下，嵌入式内容可以绕过阻止站点执行脚本的同源安全策略，并使用JavaScript完全关闭沙盒
* 配置CSP指令
  * [CSP](https://developer.mozilla.org/en-US/docs/Glossary/CSP)代表**内容安全策略**，它提供一组HTTP标头
  * *将服务器配置为发送适当的[X-Frame-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options)  标题。*
  * deny 不允许任何iframe加载、sameorigin允许相同域加载iframe




#### embed和object元素

embed和object元素的功能不同于`<iframe>`—— 这些元素是用来嵌入多种类型的外部内容的通用嵌入工具，其中包括像Java小程序和Flash，PDF（可在浏览器中显示为一个PDF插件）这样的插件技术，甚至像视频，SVG和图像的内容！

> **注意**：**插件**是一种对浏览器原生无法读取的内容提供访问权限的软件。

|                                                              | embed                        | object                                                       |
| ------------------------------------------------------------ | ---------------------------- | ------------------------------------------------------------ |
| 嵌入内容的[网址](https://developer.mozilla.org/en-US/docs/Glossary/URL) | `src`                        | `data`                                                       |
| 嵌入内容的*准确*[媒体类型](https://developer.mozilla.org/en-US/docs/Glossary/MIME_type) | `type`                       | `type`                                                       |
| 由插件控制的框的高度和宽度（以CSS像素为单位）                | `height`      `width`        | `height`      `width`                                        |
| 名称和值，将插件作为参数提供                                 | 具有这些名称和值的ad hoc属性 | 单标签[``](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/param)元素，包含在内`<object>` |
| 独立的HTML内容作为不可用资源的回退                           | 不支持（`<noembed>`已过时）  | 包含在元素`<object>`之后`<param>`                            |

使用embed元素嵌入Flash影片

```html
<embed src="whoosh.swf" quality="medium"
       bgcolor="#ffffff" width="550" height="400"
       name="whoosh" align="middle" allowScriptAccess="sameDomain"
       allowFullScreen="false" type="application/x-shockwave-flash"
       pluginspage="http://www.macromedia.com/go/getflashplayer">
```

使用object元素嵌入PDF,嵌入PDF难以在小屏幕上阅读，最好是用链接指向它们

```html
<object data="mypdf.pdf" type="application/pdf"
        width="800" height="1200" typemustmatch>
  <p>You don't have a PDF plugin, but you can <a href="myfile.pdf">download the PDF file.</a></p>
</object>
```

现有标准HTML5已经可以轻松完成多媒体需求了。

## 在网页中添加矢量图形



## 自适应图片

