## 资源猎手规则文档
[资源猎手下载地址](https://xswl.lanzouu.com/b0ko85cuf)


## 概述

资源猎手是一款支持多种解析规则（jsoup、XPath、jsonPath、正则表达式）的网络资源抓取工具。本文档详细说明了如何配置和使用这些规则来高效提取网页数据。

## 基础规则学习

在开始前，建议先学习以下基础知识：
- [jsoup语法](https://blog.csdn.net/hou_angela/article/details/80519718)
- [XPath语法](https://www.w3cschool.cn/xpath/xpath-syntax.html)
- [jsonPath语法](https://cloud.tencent.com/developer/article/2423548)
- [正则表达式教程](https://www.runoob.com/regexp/regexp-tutorial.html)

## 规则类型

软件支持四种规则类型：
1. **jsoup** - HTML解析
2. **XPath** - XML/HTML路径查询
3. **jsonPath** - JSON数据提取
4. **正则表达式(Regex)** - 文本模式匹配（建议作为补充使用）

## 核心概念

### 1. 列表规则
用于匹配包含多个结果的容器元素。

### 2. 结果规则
从每个列表项中提取所需属性或内容。

## 列表匹配指南

### 1.HTML示例

```html
<div class="tbox">
    <div class="ssbox">...</div>
    <div class="ssbox">...</div>
    <div class="ssbox">...</div>
</div>
```

### 2.匹配规则示例

- **jsoup**: `div.ssbox`
- **XPath**: `//div[@class="ssbox"]`
- **正则**: `@Regex:<div class="ssbox">[\s\S]*?<div class="sbar">[\s\S]*?/div>`

### 3.列表范围控制

使用`@range`限定结果范围：
```
div.ssbox > @range(0..2)  // 获取第1-3项
//div[@class="ssbox"] > @range(1..4)  // 获取第2-5项
@Regex:...##@range(0..2)  // 正则需用##分隔
```

## 属性提取指南

### 1. jsoup属性规则
- `路径规则 > @id` 获取此节点元素的id属性。
- `路径规则 > @text`  获取此节点元素及其所有子元素的规范化组合文本。
- `路径规则 > @html` - 检索此节点元素的内部HTML。节点为 `<div>` ，内部为一个空 `<p>`，将返回 `<p>`。
- `路径规则 > @outerhtml` - 获取此节点元素的外部HTML。假如节点为 `<p>`，可能返回 <p>Para<p>。
- `路径规则 > @attr(特定属性名称)` 获取此节点元素的属性值，例如：`@attr(src)` , `@attr(href)` , `@attr(abs:src)` , `@attr(abs:href)` 此处`abs`指获得绝对URL，即完整的网址。
- 其他属性：[`@className`,`@tagName`,`@data`,`@wholeText`,`@ownText`,`@normalName`....]

### 2. XPath属性规则

> 在XPath中，获取节点的属性通常使用`@`符号。当你想要获取某个元素的属性值时，可以使用 `@属性名`

- `路径规则/text()` - 获取此节点元素及其所有子元素的规范化组合文本。
- `路径规则/@id` - 获取此节点元素的`id`属性。
- `路径规则/@src` - 获取此节点元素的`src`属性。
- `路径规则/@href` - 获取此节点元素的`href`属性。
- ....依此类推...

### 3. 正则属性规则
- `@Regex:pattern##1` - 提取第一个捕获组

### 4.属性规则扩展
> 仅适用于属性规则，对列表规则无效。

- `原规则 + 扩展规则` 
- `原规则 > @trim`  去除属性结果的前后空白，*正则表达式使用 `原规则##trim()`*。
- `原规则 > @uppercase`  将属性结果转换为大写，*正则表达式使用 `原规则##uppercase()`*。
- `原规则 > @lowercase`  将属性结果转换为小写，*正则表达式使用 `原规则##lowercase()`*。
- `原规则 > @clearText(文本|文本|文本)`  从属性结果中清除文本内容，多个文本使用`|`分隔。
- `原规则 > @replaceText(Windows,Macos|Android,iphone)`  从属性结果中替换文本内容，多个替换规则使用`|`分隔，互相替换的文本之间使用小写 `,` 分隔，*正则表达式使用 `原规则##replaceText(Windows>>Macos|Android>>iphone)`*。
- 属性规则可叠加使用，如：` 原规则 > @trim > @clearText(...)。`

> ** 仅适用于正则
>
> ** `原规则 > @htmlToString()`  将正则匹配的结果从Html转换为规范的文本内。
>
> ** 正则规则规范 `规则##索引##属性##属性···`  属性可叠加使用
>
> ** 示例： `正则表达式##1##@htmlToString()##@clearText(...)`
>
> ** 解释：从正则表达式结果中提取第一个子匹配内容，并将内容从Html转换为文本，然后从文本中清除指定文本。

## 搜索源配置

> ##### 关键字段说明具体信息请在软件内`创建搜索源`页面查看

- `id` 通常使用`host地址`生成MD5，每个搜索源的Id必须是唯一的。
- `method ` 请求方式，`0`为GET, `1`为Post
- `charset` 网站编码，也用于搜索关键词编码
- `{keywords}`  搜索关键词，软件会自动将其替换为`URL编码后`的关键词。
- `{keywords-base64}` 搜索关键词，软件会自动将其替换为`Base64编码后`的关键词。
- `{keywords-un-encoded}`  搜索关键词，软件会自动将其替换为`不使用`URL编码后的`原始`关键词。
- `allowProxy` 是否允许搜索源通过设备代理进行网络请求。
- `creationDate` 搜索源创建日期【`13位`时间戳，毫秒】，[当前时间戳](http://daokeyou.top/) 。
- `itemHrefPrefix` 软件内会自动将 `@MagnetV1` 替换为 `magnet:?xt=urn:btih:` `@MagnetV2` 替换为 `magnet:?xt=urn:btmh:1220`，当然，你也可以使用普通的字符串代替。
- `pageOffset` 大部分网站的页标通常从`1`开始，但极少部分网站可能是从`0`开始，那么程序内部默认页标`1`则无法满足需求，那么只需要修改`pageOffset`即可，软件内将使用 `默认页标（1|当前页索引）` `+` `pageOffset`，我们只需要将 `pageOffset` 修改为 `-1 `即可。具体根据网站页标需求决定。

### 搜索源示例
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

## 订阅源配置

> 订阅源可包含多个搜索源。
>
> 订阅源ID必须是唯一不重复的。

```json
{
  "id": "A0000001",
  "name": "综合搜索",
  "searchers": [
    {搜索源1},
    {搜索源2},
      ...
  ]
}
```

## 最佳实践

1. 优先使用jsoup/XPath规则
2. 正则表达式作为最后手段
3. 测试规则时从简单开始逐步完善
4. 注意编码设置与网站一致
5. 合理使用属性处理链简化后续处理

## 常见问题

Q: 如何提取磁力链接？
A: 使用 `itemHrefPrefix` 拼接匹配结果或直接匹配完整磁力链接

Q: 页码不从1开始怎么办？
A: 调整 `pageOffset`（如从0开始设为-1）

Q: 如何获取绝对URL？
A: 使用 `@attr(abs:href)`



