# webpack HtmlWebpackPlugin

> webpack plugin 会在output 的path 选项指定的文件夹中生成 一个模板(html, jade.ejs...), 并且在模板中会自动引用webpack 处理后的静态资源

### 安装依赖

```bash
yarn add html-webpack-plugin --save-dev
```



### option 选项

* title 

  > 用于生成HTML文档的title

* filename

  > 生成的HTML文件，默认为index.html, 也可以指定一个子目录(assets/index.html)

* template

  > Webpack 需要的模板路径
  >
  > 详情查看：[doc](https://github.com/jantimon/html-webpack-plugin/blob/master/docs/template-option.md)

* inject

  > 可选值有： true|'head'|'body'|false。
  >
  > 将静态资源注入到指定的模板，当用`true` 或则和`body` ，`javascript`资源将被插入到`body`元素的底部，当用`head` `javascript`资源将会插入到`head` 元素里面。当用`false`就是不插入资源。

* favicon

  > 添加favicon路径

* minify

  > 可选值： {...} | false
  >
  > 使用{...} 指定生成的`html`的格式(删除空格，删除注释...)
  >
  > 配置对象详情查看[minify选项](https://github.com/kangax/html-minifier#options-quick-reference)

* hash

  > 可选值：true|false
  >
  > 如果是`true` 则为插入的`javascript` 和 `css` 文件添加一个唯一的`webpack`编译的`hash`
  >
  > 用于清除缓存

* cache

  > 可选值： true|false
  >
  > 默认值：true
  >
  > 如果为true  ，就会只有在内容发生变化的时候才会生成一个新文件

* showErrors

  > 可选值： true|false
  >
  > 默认值： true
  >
  > 如果为true，错误信息会被写入`html`页面中

* chunks

  > 指定`html`中需要引入`entry`中指定的`chunk`

* chunksSortMode

  > 可选值： ‘none’ | 'auto'|'dependency'|{function}
  >
  > 默认值： 'auto'
  >
  > 指定如何处理引入的chunks 在`html`中的引入顺序

* excludeChunks

  > 排除`entry`中的`chunk`, 与  `chunks`选项相反

* xhtml

  > 可选值：true|false
  >
  > 默认值： false
  >
  > 设置生成的`html`是否符合`xhtml`规范。

### 基础使用

```javascript
var HtmlWebpackPlugin = require("html-webpack-plugin")
var webpackConfig = {
  entry: "index.js",
  output: {
    path: __dirname + "/dist",
    filename:"index_bundle.js"
  },
  
  plugins:[new HtmlWebpackPlugin()]
}
```

执行`webpack` 命令后 会在`dist` 文件夹生成一个`index.html` 和 `index_bundle.js` 文件， 而且`index.html` 中自动引入了`index_bundle.js`	



> 更多案例使用[查看](https://github.com/jantimon/html-webpack-plugin/blob/master/README.md)