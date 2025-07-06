## 资源猎手规则

在了解资源猎手的规则之前建议学习基本的规则：

[jsoup](https://blog.csdn.net/hou_angela/article/details/80519718)

[XPath](https://www.w3cschool.cn/xpath/xpath-syntax.html)

[jsonPatn](https://cloud.tencent.com/developer/article/2423548)

[regex (正则表达式)](https://www.runoob.com/regexp/regexp-tutorial.html)



软件支持四种规则类型：jsoup + XPath + jsonPath + 正则表达式(Regex，*软件内建议用于补充使用*)

简单的说软件内部仅需两个规则：列表规则 + 单个结果规则（`从每一个结果提取需要的属性或标签`）



#### 第一部分，**如何匹配出一个列表？**

##### 网站HTML源码示例

```html
<div class="tbox">
    <div class="ssbox">
        <div class="title">
            <h3><a href="/hash/92d1b2697d14a607867390f34b9747dfcbced709.html" target="_blank"> <span class="red">钢铁侠</span>_铁人_钢铁人_铁甲奇侠 Iron.Man.PROPER.TS.XviD-iLG </a></h3>
        </div>
        <div class="sbar">
            <span><a href="magnet:?xt=urn:btih:92D1B2697D14A607867390F34B9747DFCBCED709"
                    target="_blank">[磁力链接]</a></span>
            <span>添加时间:<b>2017-02-26</b></span>
            <span>大小:<b class="cpill yellow-pill">1.5 GB</b></span>
            <span>最近下载:<b>2025-07-04</b></span>
            <span>热度:<b>1253</b></span>
        </div>
    </div>

    <div class="ssbox">
        <div class="title">
            <h3>
                <a href="/hash/b85d3bcafde437967ffc3dff6f28c8518526b71c.html" target="_blank"> <span class="red">钢铁侠</span>4-铁甲奇侠4-IRON MAN 4-1080P.rmvb</a>
            </h3>
        </div>

        <div class="sbar">
            <span><a href="magnet:?xt=urn:btih:B85D3BCAFDE437967FFC3DFF6F28C8518526B71C"
                    target="_blank">[磁力链接]</a></span>
            <span>添加时间:<b>2017-07-03</b></span>
            <span>大小:<b class="cpill yellow-pill">1.3 GB</b></span>
            <span>最近下载:<b>2024-08-23</b></span>
            <span>热度:<b>12</b></span>
        </div>
    </div>

    <div class="ssbox">
        <div class="title">
            <h3>
                <a href="/hash/3f950f8cfc7f674ee4f1557d3cbf9b2b67084c0f.html" target="_blank"> 【<span class="red">钢铁侠</span>3(铁甲奇侠3)】【高清1280版BD-RMVB.国语】【2013最新美国科幻动作大片】 </a>
            </h3>
        </div>
        
        <div class="sbar">
            <span><a href="magnet:?xt=urn:btih:3F950F8CFC7F674EE4F1557D3CBF9B2B67084C0F"
                    target="_blank">[磁力链接]</a></span>
            <span>添加时间:<b>2024-05-14</b></span>
            <span>大小:<b class="cpill yellow-pill">1.4 GB</b></span>
            <span>最近下载:<b>2025-06-30</b></span>
            <span>热度:<b>592</b></span>
        </div>
    </div>

</div>
```

软件的需求是从当前的Html中匹配出所有div ssbox列表，我们有三个方案（JsonPath仅支持Json）：



##### 规则示例：

- [ ] Jsoup示例：`div.ssbox`
- [ ] XPath示例：`//div[@class="ssbox"]`
- [ ] Regex(正则)示例：`@Regex:<div class="ssbox">[\s\S]*?<div class="sbar">[\s\S]*?/div>`



##### **匹配结果示例：**

>  此处仅展示单个结果

```html
<div class="ssbox">
        <div class="title">
            <h3>
                <a href="/hash/3f950f8cfc7f674ee4f1557d3cbf9b2b67084c0f.html" target="_blank"> 【<span class="red">钢铁侠</span>3(铁甲奇侠3)】【高清1280版BD-RMVB.国语】【2013最新美国科幻动作大片】 </a>
            </h3>
        </div>
        
        <div class="sbar">
            <span><a href="magnet:?xt=urn:btih:3F950F8CFC7F674EE4F1557D3CBF9B2B67084C0F"
                    target="_blank">[磁力链接]</a></span>
            <span>添加时间:<b>2024-05-14</b></span>
            <span>大小:<b class="cpill yellow-pill">1.4 GB</b></span>
            <span>最近下载:<b>2025-06-30</b></span>
            <span>热度:<b>592</b></span>
        </div>
    </div>
```



##### 列表规则扩展：

软件内部针对列表匹配额外添加了部分规则：

###### *获取列表区间：@range*

规则后紧接`@range(index..index)`可以从结果列表中提取从指定索引开始到指定索引的结果。

例如：

`div.ssbox > @range(0..2)` 

`//div[@class="ssbox"] > @range(0..2)`

`@Regex:<div class="ssbox">[\s\S]*?<div class="sbar">[\s\S]*?/div>##@range(0..2)`

> 注：正则表达式需要使用`##`进行规则分割



即可从结果列表中提取到从第`1`个结果到第`3`个结果区间（索引从0开始）。

`@range(1..4)` 即从第`2`个结果到第`5`个结果

如果`结束索引`大于列表总长度则会返回从`开始索引`开始到列表结尾的整个列表内容。



#### 第二部分，如何从结果列表中获取我们需要的属性或参数？

结果列表示例见上方（`匹配结果示例`）

结果规则通常为 `路径规则` + `属性规则`

##### 1.jsoup属性规则：

`路径规则 > @id` 获取此路径元素的id属性。

`路径规则 > @text` 获取此路径元素及其所有子元素的规范化组合文本。

`路径规则 > @html` 检索元素的内部HTML。例如在一个 `<div>` 与一个空 `<p>`，将返回 `<p>`。(而`outerHtml` 将返回 `<div><p><div>`。)

`路径规则 > @outerhtml` 获取此节点的外部HTML。例如，在`p`元素上，可能返回 <p>Para<p>。

`路径规则 > @attr(属性名称)` 获取此节点元素的属性值，例如：`@attr(src) , @attr(href) , @attr(abs:src) , @attr(abs:href) 此处abs指获得绝对URL，即完整的网址。`。



> *其他jsoup属性同上，以 `@` 开头带上属性规则即可 ，如：*

```java
[@className,@tagName,@data,@wholeText,@ownText,@normalName....]
```



##### 2.XPath属性规则：

`路径规则/text()` 获取此路径元素及其所有子元素的规范化组合文本。

`路径规则/@src` 获取此路径元素中的src属性。

`路径规则/@href` 获取此路径元素中的href属性。



##### 3.正则属性规则：

 `暂无`



##### 4.属性规则扩展：

> 仅适用于属性规则，对列表规则无效。

`原规则 + 扩展规则` 

`原规则 > @trim`  去除属性结果的前后空白，*正则表达式使用 `原规则##trim()`*。

`原规则 > @uppercase`  将属性结果转换为大写，*正则表达式使用 `原规则##uppercase()`*。

`原规则 > @lowercase`  将属性结果转换为小写，*正则表达式使用 `原规则##lowercase()`*。

`原规则 > @clearText(文本|文本|文本)`  从属性结果中清除文本内容，多个文本使用`|`分隔。

`原规则 > @replaceText(Windows,Macos|Android,iphone)`  从属性结果中替换文本内容，多个替换规则使用`|`分隔，互相替换的文本之间使用小写 `,` 分隔，*正则表达式使用 `原规则##replaceText(Windows>>Macos|Android>>iphone)`*。

***属性规则可叠加使用，如：` 原规则 > @trim > @clearText(...)。`



> **仅适用于正则
>
> **正则规则规范 `规则##索引##属性##属性···`  属性可叠加使用
>
> **示例： `正则表达式##1##@htmlToString()##@clearText(...)`
>
> **解释：从正则表达式结果中提取第一个子匹配内容，并将内容从Html转换为文本，然后从文本中清除指定文本。

`原规则 > @htmlToString()`  将正则匹配的结果从Html转换为规范的文本内。



##### 属性结果提取示例：

1.如何获取标题(此处为a标签文本)？

规则示例：

> jsoup: `div.title > h3 > a > @text`
>
> XPath: `//h3/a/text()`
>
> regex: `@Regex:<a href.*?target="_blank">(.*?)</a>##htmlToString()`



2.如何提取Href属性(此处为a标签)？

规则示例：

> jsoup: `div.title > h3 > a > @attr(href)`
>
> XPath: `//h3/a/@href`
>
> Regex(正则): `@Regex:<a href=\"(.*?)\"##1`   `此处##1表示获取第一个子匹配文本，即(.*?)中的内容，如果为0则是整个匹配结果。`



#### **第三部分，搜索源示例：**

> 部分字段解释，具体请在软件内`创建搜索源`页面查看。
>
> ** `id` 通常使用host生成MD5，每个搜索源的Id必须是唯一的。
>
> ** `method ` 请求方式，0为GET, 1为Post
>
> ** `charset` 网站编码，也用于搜索关键词编码
>
> ** `{keywords}`  搜索关键词，软件会自动将其替换为URL编码后的关键词。
>
> ** `{keywords-base64}` 搜索关键词，软件会自动将其替换为Base64编码后的关键词。
>
> ** `{keywords-un-encoded}`  搜索关键词，软件会自动将其替换为`不使用`URL编码后的`原始`关键词。
>
> ** `allowProxy` 是否允许搜索源通过设备代理进行网络请求。
>
> ** `creationDate` 搜索源创建日期【13位时间戳，毫秒】，[当前时间戳](http://daokeyou.top/) 。
>
> ** `itemHrefPrefix` 软件内会自动将 `@MagnetV1` 替换为 `magnet:?xt=urn:btih:` `@MagnetV2` 替换为 `magnet:?xt=urn:btmh:1220`，当然，你也可以使用普通的字符串代替。
>
> ** `pageOffset` 大部分网站的页标通常从`1`开始，但极少部分网站可能是从`0`开始，那么程序内部默认页标`1`则无法满足需求，那么只需要修改`pageOffset`即可，软件内将使用 `默认页标（1|当前页索引）` `+` `pageOffset`，我们只需要将 `pageOffset` 修改为 `-1 `即可。具体根据网站页标需求决定。



> BT磁力天堂

```json
{
  "id": "b09b75f63ac19985e4a35be9a20c0398", 
  "name": "BT磁力天堂",
  "host": "https://www.691061.xyz/",
  "method": 0,                              
  "charset": "UTF-8",                       
  "favicon": "/static/favicon.ico",
  "searchApi": "/main-search-kw-{keywords}-{page}.html", 
  "pageOffset": 0,
  "allowProxy": true,
  "creationDate": 1751567801463,
  "description": "",
    
  "itemListRule": "div.search-item.detail-width",
  "itemNameRule": "div.item-title > h3 > a > @text",
  "itemSizeRule": "div.item-bar > span:nth-child(3) > b > @text",
  "itemDateRule": "div.item-bar > span:nth-child(2) > b > @text",
  "itemHrefRule": "div.item-title > h3 > a > @attr(href) > @clearText(/hash/|.html)",
  "itemHrefPrefix": "@MagnetV1",
  "itemHrefSuffix": "",
  "headers": {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36"
  }
}
```



> 种子搜

```json
{
  "id": "edca11e3ddab12942dd3d10dc64c0415",
  "name": "种子搜",
  "host": "https://m.zhongziso.com/",
  "method": 0,
  "charset": "UTF-8",
  "favicon": "",
  "searchApi": "/list/{keywords}/{page}",
  "pageOffset": 0,
  "allowProxy": true,
  "creationDate": 1750323550240,
  "description": "",
    
  "itemListRule": "ul.list-group",
  "itemNameRule": "li:nth-child(1) > a > @text",
  "itemSizeRule": "dd.text-size > @text",
  "itemDateRule": "dd.text-time > @text",
  "itemHrefRule": "@Regex:href=\"(magnet:\\?xt=urn:btih:[A-Za-z0-9]{32,40}+)\"##1",
  "itemHrefPrefix": "",
  "itemHrefSuffix": "",
  "headers": {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36"
  }
}
```



> 磁力先锋

```json
{
  "id": "26fb95f2296f3528419b790045c58774",
  "name": "磁力先锋",
  "host": "https://clxf.best/",
  "method": 0,
  "charset": "UTF-8",
  "favicon": "https://ciliku.net/favicon.ico",
  "searchApi": "/search.php?name={keywords}&page={page}",
  "pageOffset": 0,
  "allowProxy": true,
  "creationDate": 1750683900224,
  "description": "",
  "itemListRule": "div.card.border-dashed.border-2.mb-2",
  "itemNameRule": "h5 > @clearText(🧲)",
  "itemSizeRule": "p.card-text.mb-1 > span:nth-child(1) > @text",
  "itemDateRule": "@Regex:更新时间：(\\d{4}-\\d{2}-\\d{2})##1",
  "itemHrefRule": "@Regex:xiangqing(\\('\\d','.*?'\\))##1##replaceText(('>>https://clxf.best/detail.php?sjk=|','>>&md5hash=)##clearText('))",
  "itemHrefPrefix": "",
  "itemHrefSuffix": "",
  "requireDetailPage": true,
  "detailNameRule": "",
  "detailSizeRule": "",
  "detailDateRule": "",
  "detailHrefRule": "@Regex:资源哈希：([a-zA-Z0-9]{32,40})##1",
  "detailHrefPrefix": "@MagnetV1",
  "detailHrefSuffix": "",
  "headers": {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36"
  }
}
```

> 磁力天堂

```json
{
  "id": "1d2c8892ab3ee7a0152a151ec3d2414a",
  "name": "磁力天堂",
  "host": "https://janrzbvl.770670.xyz",
  "method": 0,
  "charset": "UTF-8",
  "favicon": "",
  "searchApi": "/search-{keywords}-0-0-{page}.html",
  "pageOffset": 0,
  "allowProxy": true,
  "creationDate": 1750323472465,
  "description": "",
  "itemListRule": "//div[@class='search_result']/dl",
  "itemNameRule": "dt > a > @text",
  "itemSizeRule": "@Regex:<span>文档大小:(.*?)</span>##1",
  "itemDateRule": "@Regex:收录时间:<b>(.*?)</b>##1",
  "itemHrefRule": "@Regex:<span><a href=\"(.*?)\"##1",
  "itemHrefPrefix": "",
  "itemHrefSuffix": "",
  "headers": {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36"
  }
}
```



#### **第四部分，订阅源示例：**

> 此处订阅源Id必须是唯一的。

```json
{
  "id": "A0000001",
  "name": "订阅源名称",
  "searchers": [
    {
      "id": "b09b75f63ac19985e4a35be9a20c0398",
      "name": "BT磁力天堂",
      "host": "https://www.691061.xyz/",
      "method": 0,
      "charset": "UTF-8",
      "favicon": "/static/favicon.ico",
      "searchApi": "/main-search-kw-{keywords}-{page}.html",
      "pageOffset": 0,
      "allowProxy": true,
      "creationDate": 1751567801463,
      "description": "",
      "itemListRule": "div.search-item.detail-width",
      "itemNameRule": "div.item-title > h3 > a > @text",
      "itemSizeRule": "div.item-bar > span:nth-child(3) > b > @text",
      "itemDateRule": "div.item-bar > span:nth-child(2) > b > @text",
      "itemHrefRule": "div.item-title > h3 > a > @attr(href) > @clearText(/hash/|.html)",
      "itemHrefPrefix": "@MagnetV1",
      "itemHrefSuffix": "",
      "headers": {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36"
      }
    },
    {
      "id": "edca11e3ddab12942dd3d10dc64c0415",
      "name": "种子搜",
      "host": "https://m.zhongziso.com/",
      "method": 0,
      "charset": "UTF-8",
      "favicon": "",
      "searchApi": "/list/{keywords}/{page}",
      "pageOffset": 0,
      "allowProxy": true,
      "creationDate": 1750323550240,
      "description": "",
      "itemListRule": "ul.list-group",
      "itemNameRule": "li:nth-child(1) > a > @text",
      "itemSizeRule": "dd.text-size > @text",
      "itemDateRule": "dd.text-time > @text",
      "itemHrefRule": "@Regex:href=\"(magnet:\\?xt=urn:btih:[A-Za-z0-9]{32,40}+)\"##1",
      "itemHrefPrefix": "",
      "itemHrefSuffix": "",
      "headers": {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36"
      }
    },
    {
      "id": "26fb95f2296f3528419b790045c58774",
      "name": "磁力先锋",
      "host": "https://clxf.best/",
      "method": 0,
      "charset": "UTF-8",
      "favicon": "https://ciliku.net/favicon.ico",
      "searchApi": "/search.php?name={keywords}&page={page}",
      "pageOffset": 0,
      "allowProxy": true,
      "creationDate": 1750683900224,
      "description": "",
      "itemListRule": "div.card.border-dashed.border-2.mb-2",
      "itemNameRule": "h5 > @clearText(🧲)",
      "itemSizeRule": "p.card-text.mb-1 > span:nth-child(1) > @text",
      "itemDateRule": "@Regex:更新时间：(\\d{4}-\\d{2}-\\d{2})##1",
      "itemHrefRule": "@Regex:xiangqing(\\('\\d','.*?'\\))##1##replaceText(('>>https://clxf.best/detail.php?sjk=|','>>&md5hash=)##clearText('))",
      "itemHrefPrefix": "",
      "itemHrefSuffix": "",
      "requireDetailPage": true,
      "detailNameRule": "",
      "detailSizeRule": "",
      "detailDateRule": "",
      "detailHrefRule": "@Regex:资源哈希：([a-zA-Z0-9]{32,40})##1",
      "detailHrefPrefix": "@MagnetV1",
      "detailHrefSuffix": "",
      "headers": {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36"
      }
    }
  ]
}
```

