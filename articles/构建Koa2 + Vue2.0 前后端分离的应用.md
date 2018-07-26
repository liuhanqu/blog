换了家公司，技术栈由之前的 React 变成了现在的 Vue。之前也没怎么接触过 Vue，但现在需要搭建一个基于 Vue 的多页面应用，于是就网上搜了一番，顺便记录一下过程。

## client 配置

Vue 官方的 [vue-cli](https://github.com/vuejs/vue-cli) 已经推出 3.0 版本了，可以使用这个创建项目。
首先，安装 vue-cli

```
npm install -g @vue/cli
# or
yard global add @vue/cli
```

[官方文档](https://cli.vuejs.org/guide/installation.html) 都有详细的说明，可以参考。接下来，还需要先安装 `cli-init`

```
npm install -g @vue/cli-init
```

现在，我们使用 webpack 模板来创建项目了

```
vue init webpack my-project
```

修改 `config/index.js`，给 webpack-dev-server 添加 proxyTable:

```js
  proxyTable: {
    '/api/': {
      // target 为你后端的地址
      target: 'http://localhost:3000/api',
      changeOrigin: true,
      pathRewrite: {
        '/^api': '/'
      }
    }
  }
```

至此，已经能达到了单页应用的前后端分离。但我们的目的是为了实现多页应用，那就还需要做一些改动。

调整项目目录，创建 `src/pages/index`, 将 `src/assets`、`src/router`、`src/App.vue`、`src/main.js` 和 `index.html` 移动到该目录下，并将 `main.js` 改成 `index.js`。

修改构建脚本`build/utils`，添加：

```js
// glob是webpack安装时依赖的一个第三方模块，还模块允许你使用 *等符号, 例如lib/*.js就是获取lib文件夹下的所有js后缀名的文件
const glob = require('glob');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const merge = require('webpack-merge');

// 取得相应的页面路径，因为之前的配置，所以是src文件夹下的pages文件夹
const PAGE_PATH = path.resolve(__dirname, '../src/pages');

//多入口配置
// 通过glob模块读取pages文件夹下的所有对应文件夹下的js后缀文件，如果该文件存在
// 那么就作为入口处理
exports.entries = function() {
  const entryFiles = glob.sync(PAGE_PATH + '/*/*.js');
  const map = {};
  entryFiles.forEach(filePath => {
    const filename = filePath.substring(filePath.lastIndexOf('/') + 1, filePath.lastIndexOf('.'));
    map[filename] = filePath;
  });
  return map;
};

//多页面输出配置
// 与上面的多页面入口配置相同，读取pages文件夹下的对应的html后缀文件，然后放入数组中
exports.htmlPlugin = function() {
  let entryHtml = glob.sync(PAGE_PATH + '/*/*.html');
  return entryHtml.map(filePath => {
    let filename = filePath.substring(filePath.lastIndexOf('/') + 1, filePath.lastIndexOf('.'));
    let conf = {
      // 模板来源
      template: filePath,
      // 文件名称
      filename: filename + '.html',
      // 页面模板需要加对应的js脚本，如果不加这行则每个页面都会引入所有的js脚本
      chunks: ['vendor', filename],
      inject: true,
    };
    if (process.env.NODE_ENV === 'production') {
      conf = merge(conf, {
        minify: {
          removeComments: true,
          collapseWhitespace: true,
          removeAttributeQuotes: true,
        },
        chunksSortMode: 'dependency',
      });
    }
    return new HtmlWebpackPlugin(conf);
  });
};
```

修改 `build/webpack.base.conf.js` 的入口配置

```js
module.exports = {
  context: path.resolve(__diranme, '../'),
  entry: utils.entries()

  ...
  ...
}
```

修改 `build/webpack.dev.conf.js` 和 `build/webpack.prod.conf.js` 的插件配置：

```js
  plugins: [
    ...

    ...utils.htmlPlugin(),

    ...
  ]
```

到这里配置就算是完成了。

接下来，你可以测试一下你的配置是否 ok。

创建`src/pages/abc`目录，添加`abc.html`文件。

 通过`npm start`启动，然后在浏览器中打开`http://localhost:8080/abc`，看看是否能看到内容。

vue-cli 中的 webpack 模板使用的是 3.x 版本的 webpack，但现在都已经 4.x 了，所以我又折腾了一下，升级了一下 webpack，具体的修改就不多阐述，可以在这里看到最终的配置 [vue-multipage-app](https://github.com/liuhanqu/vue-multipage-app)

## server 配置

对于前端来说，server 端使用 node 是最简单的。这里就采用了 koa2，没有采用任何项目生成命令，就自己搭了一下。最主要就是指定 view 的目录。

```js
app.use(views(path.resolve(__dirname, '../../var/dist'), { extension: 'html' }));
```

因为我们在 client 的打包输出的 html、js、css 等，最后都会在 var 目录下。
又或者，你可以在`npm run build`之后，把生成的 html 复制到 server 目录的 view 目录下，看个人喜欢。

## 目录结构

markdown 要贴目录树结构有点麻烦，这里直接就贴图了，又或者你可以直接在这里看 [vue-multipage-app](https://github.com/liuhanqu/vue-multipage-app)
![](/images/vue-multipage/tree.jpg)
