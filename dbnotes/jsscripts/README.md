# 使用 gitbook-plugin-custom-html 注入自定义 JS 脚本

## 概述

`gitbook-plugin-custom-html` 是一个 HonKit/GitBook 插件，可以通过自定义 JS 文件来修改生成的 HTML 内容，非常适合注入自定义脚本（如百度统计、Google Analytics 等）。

## 优势

✅ **无需创建自定义插件** - 直接使用现有插件
✅ **灵活性强** - 可以使用 cheerio 修改 HTML 的任何部分
✅ **简单易用** - 只需配置一个 JS 文件

## 安装

插件已经在 `devDependencies` 中，无需额外安装。

## 配置步骤

### 1. 创建自定义 JS 文件

在项目根目录创建 `js/custom-html.js` 文件：

```javascript
module.exports = function($) {
  var baiduScript = `
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?你的统计代码";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>
  `;
  
  $('body').append(baiduScript);
  return $.html();
}
```

### 2. 配置 book.json

```json
{
  "plugins": [
    "custom-html"
  ],
  "pluginsConfig": {
    "customHTML": {
      "js": "js/custom-html.js",
      "toURL": "_book"
    }
  }
}
```

### 3. 构建项目

```bash
npm run build
```

## 高级用法

### 在 head 中注入脚本

```javascript
module.exports = function($) {
  var script = '<script src="https://example.com/script.js"></script>';
  $('head').append(script);
  return $.html();
}
```

### 修改特定元素

```javascript
module.exports = function($) {
  // 修改标题
  $('h1').css('color', 'red');
  
  // 添加自定义样式
  $('body').append('<style>.custom { color: blue; }</style>');
  
  return $.html();
}
```

### 注入多个脚本

```javascript
module.exports = function($) {
  var scripts = `
<script src="https://example.com/script1.js"></script>
<script src="https://example.com/script2.js"></script>
  `;
  
  $('body').append(scripts);
  return $.html();
}
```

## Cheerio API

`$` 参数是 cheerio 对象，支持类似 jQuery 的语法：

```javascript
// 选择元素
$('body')
$('h1')
$('.class-name')
$('#id-name')

// 修改元素
$('h1').text('New Title')
$('p').addClass('highlight')

// 插入内容
$('body').append('<div>Content</div>')
$('head').prepend('<script>...</script>')

// 返回修改后的 HTML
return $.html();
```

## 验证

构建后，在生成的 HTML 文件中应该能看到注入的脚本：

```html
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?7a76c4f4450d5c392378331568daee66";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>
</body></html>
```

## 配置参数说明

- `js`: 自定义 JS 文件的路径（相对于项目根目录）
- `toURL`: 输出目录（默认为 `_book`）

## 注意事项

- 插件只在 website 模式下生效
- 修改会在所有 HTML 页面上生效
- 使用 cheerio 语法进行 HTML 操作
- 确保自定义 JS 文件导出一个函数
