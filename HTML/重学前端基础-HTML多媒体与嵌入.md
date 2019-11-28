# 多媒体与嵌入



## 目录

* HTML中的图片-img

* 视频和音频内容
  
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

### 什么是矢量图形？

- 位图使用像素网格来定义 — 一个位图文件精确得包含了每个像素的位置和它的色彩信息。流行的位图格式包括 Bitmap (`.bmp`), PNG (`.png`), JPEG (`.jpg`), and GIF (`.gif`.)
- 矢量图使用算法来定义 — 一个矢量图文件包含了图形和路径的定义，电脑可以根据这些定义计算出当它们在屏幕上渲染时应该呈现的样子。 [SVG](https://developer.mozilla.org/en-US/docs/Glossary/SVG) 格式可以让我们创造用于 Web 的精彩的矢量图形。

### SVG是什么？

[SVG](https://developer.mozilla.org/zh-CN/docs/Web/SVG) 是用于描述矢量图像的[XML](https://developer.mozilla.org/en-US/docs/Glossary/XML)语言

```html
<svg version="1.1"
     baseProfile="full"
     width="300" height="200"
     xmlns="http://www.w3.org/2000/svg">
  <rect width="100%" height="100%" fill="black" />
  <circle cx="150" cy="100" r="90" fill="blue" />
</svg>
```

### SVG的优缺点

优点

* 矢量图像中的文本仍然可访问（这也有利于 [SEO](https://developer.mozilla.org/en-US/docs/Glossary/SEO))）。
* SVG 可以很好地适应样式/脚本，因为图像的每个组件都是可以通过CSS或通过JavaScript编写的样式的元素。

缺点

* SVG非常容易变得复杂，这意味着文件大小会增加; 复杂的SVG也会在浏览器中占用很长的处理时间。
* SVG可能比栅格图像更难创建，具体取决于您尝试创建哪种图像。
* 旧版浏览器不支持SVG，因此如果您需要在网站上支持旧版本的 IE，则可能不适合（SVG从IE9开始得到支持）。

### 将SVG添加到页面

#### 通过 img 元素嵌入SVG

```html
<img 
    src="equilateral.svg" 
    alt="triangle with all three sides equal"
    height="87px"
    width="100px" />
```

优点

* 快速，熟悉的图像语法与`alt`属性中提供的内置文本等效。
* 可以通过在a元素嵌套`<img>`，使图像轻松地成为超链接。

缺点

* 无法使用JavaScript操作图像。
* 如果要使用CSS控制SVG内容，则必须在SVG代码中包含内联CSS样式。 （从SVG文件调用的外部样式表不起作用）
* 不能用CSS伪类来重设图像样式（如`:focus`）。

#### 跨浏览器支持

​	对于不支持SVG（IE 8及更低版本，Android 2.3及更低版本）的浏览器，您可以从`src`属性引用PNG或JPG，并使用`srcset`属性 只有最近的浏览器才能识别）来引用SVG。

```html
<img src="equilateral.png" alt="triangle with equal sides" srcset="equilateral.svg">
```

#### 通过css嵌入SVG

```css
background: url("fallback.png") no-repeat center;
background-image: url("image.svg");
background-size: contain;
```

#### 在HTML中引入SVG代码

```html
<svg width="300" height="200">
    <rect width="100%" height="100%" fill="green" />
</svg>
```

优点

- 将 SVG 内联减少 HTTP 请求，可以减少加载时间。
- 您可以为 SVG 元素分配`class`和`id`，并使用 CSS 修改样式，无论是在SVG中，还是 HTML 文档中的 CSS 样式规则。 实际上，您可以使用任何 [SVG外观属性](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Attribute#Presentation_attributes) 作为CSS属性。
- 内联SVG是唯一可以让您在SVG图像上使用CSS交互（如`:focus`）和CSS动画的方法（即使在常规样式表中）。
- 您可以通过将 SVG 标记包在[``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a)元素中，使其成为超链接。

缺点

- 这种方法只适用于在一个地方使用的SVG。多次使用会导致资源密集型维护（resource-intensive maintenance）。
- 额外的 SVG 代码会增加HTML文件的大小。
- 浏览器不能像缓存普通图片一样缓存内联SVG。
- 您可能会在[`<foreignObject>`](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/foreignObject) 元素中包含回退，但支持 SVG 的浏览器仍然会下载任何后备图像。你需要考虑仅仅为支持过时的浏览器，而增加额外开销是否真的值得。

#### 使用 [`iframe`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe) 嵌入SVG

```html
<iframe src="triangle.svg" width="500" height="500" sandbox>
    <img src="triangle.png" alt="Triangle with three unequal sides" />
</iframe>
```

这是不推荐的。

缺点：

- 如你所知， `iframe`有一个回退机制，如果浏览器不支持`iframe`，则只会显示回退。
- 此外，除非 SVG 和您当前的网页具有相同的 [origin](https://developer.mozilla.org/en-US/docs/Glossary/origin)，否则你不能在主页面上使用 JavaScript 来操纵 SVG。

## 自适应图片

#### 为什么要用自适应的图片？

图片在不同屏幕尺寸的设备上，表现不一致，导致无法暂时一些图片的重要信息，在不同的屏幕下使用了相同分辨率的图片，造成带宽的浪费。

#### 创建自适应的图片

使用`srcset`和`sizes`

```html
<img srcset="elva-fairy-320w.jpg 320w,
             elva-fairy-480w.jpg 480w,
             elva-fairy-800w.jpg 800w"
     sizes="(max-width: 320px) 280px,
            (max-width: 480px) 440px,
            800px"
     src="elva-fairy-800w.jpg" alt="Elva dressed as a fairy">
```

**srcset**定义了我们允许浏览器选择的图像集，以及每个图像的大小

* 一个**文件名** (`elva-fairy-480w.jpg`.)
* 一个空格
* **图像的固有宽度**（以像素为单位）这是图像的真实大小,而不是你预计的`px`

**sizes**定义了一组媒体条件（例如屏幕宽度）并且指明当某些媒体条件为真时，什么样的图片尺寸是最佳选择

* 一个**媒体条件**（`(max-width:480px)`）
* 一个空格
* 当媒体条件为真时，图像将填充的**槽的宽度**（`440px`）

浏览器执行过程

1. 查看设备宽度
2. 检查`sizes`列表中哪个媒体条件是第一个为真
3. 查看给予该媒体查询的槽大小
4. 加载`srcset`列表中引用的最接近所选的槽大小的图像

老旧的浏览器不支持这些特性，它会忽略这些特征。并继续正常加载 `src`属性引用的图像文件。

#### 开发工具

firefox(*Tools > Web Developer > Responsive Design View）*

> **注意**: 在 Chrome 中测试时，通过如下方式禁用缓存：打开 DevTools 
> ，并选中 Settings > Preferences > Network下Disable cache的选择框。否则，Chrome 
> 会优先选择缓存图片而不是恰好适配的那个。

#### 分辨率切换: 相同的尺寸, 不同的分辨率

你可以让浏览器通过`srcset`和x语法结合来选择适当分辨率的图片。

```html
<img srcset="elva-fairy-320w.jpg,
             elva-fairy-480w.jpg 1.5x,
             elva-fairy-640w.jpg 2x"
     src="elva-fairy-640w.jpg" alt="Elva dressed as a fairy">
```



#### 使用picture代替img

```html
<picture>
  <source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg">
  <source media="(min-width: 800px)" srcset="elva-800w.jpg">
  <img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva">
</picture>
```

这样的代码允许我们在宽屏和窄屏上都能显示合适的图片。

-  `<source>`元素包含一个`media`属性，这一属性包含一个媒体条件——就像第一个`srcset`例子，这些条件来决定哪张图片会显示——第一个条件返回真，那么就会显示这张图片。在这种情况下，如果视窗的宽度为799px或更少，第一个`<source>`元素的图片就会显示。如果视窗的宽度是800px或更大，就显示第二张图片。
- `srcset`属性包含要显示图片的路径。请注意，正如我们在`<img>`上面看到的那样，`<source>`可以使用引用多个图像的`srcset`属性，还有`sizes`属性。所以你可以通过一个 `<picture>`元素提供多个图片，不过也可以给每个图片提供多分辨率的图片。实际上，你可能不想经常做这样的事情。
- 在任何情况下，你都必须在 `</picture>`之前正确提供一个`<img>`元素以及它的`src`和`alt`属性，否则不会有图片显示。当媒体条件都不返回真的时候（你可以在这个例子中删除第二个`<source>` 元素），它会提供图片；如果浏览器不支持 `<picture>`元素时，它可以作为后备方案。

> **注意**: 你应该仅仅当在美术设计场景下使用media属性；当你使用media时，不要在sizes属性中也提供媒体条件。

##### 为什么我们不能使用 CSS 或 JavaScript 

当浏览器开始加载一个页面, 它会在主解析器开始加载和解析页面的 CSS 和 JavaScript 之前先下载 (预加载) 任意的图片。

`<picture>`让我们能继续满足老式浏览器的需要。你可以在`type`属性中提供MIME类型，这样浏览器就能立即拒绝其不支持的文件类型：

```html
<picture>
  <source type="image/svg+xml" srcset="pyramid.svg">
  <source type="image/webp" srcset="pyramid.webp"> 
  <img src="pyramid.png" alt="regular pyramid built from four equilateral triangles">
</picture>
```

- 不要使用`media`属性，除非你也需要美术设计。
- 在`<source>` 元素中，你只可以引用在`type`中声明的文件类型。
- 像之前一样，如果必要，你可以在`srcset`和`sizes`中使用逗号分割的列表。