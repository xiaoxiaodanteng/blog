# webpack优化

### 1. 提取CSS

1. 将c s s提取到独立文件中

   `mini-css-extract-plugin`是用于将CSS提取到独立的文件的插件，对每个包含css的js文件都会创建一个css文件，支持按需加载css和sourceMap

   只能在webpack4中，有如下优势：

   * 异步加载
   * 不重复变异，性能更好
   * 更容易使用
   * 只针对css

   使用方法：

   1. 安装

      `npm i -D mini-css-extract-plugin`

   2. 在webpack配置文件中引入插件

      ```js
      const MiniCssExtractPlugin = require('mini-css-extract-plugin')
      ```

   3. 创建插件对象，配置抽离的css文件名，支持placeholder语法

      ```js
      new MiniCssExtractPlugin({
      	filename: '[name].css'
      })
      ```

   4. 将原来配置的所有`style-loader`替换为`MiniCssExtractPlugin.loader`
      ```js
      { 
        test: /\.css$/,
        // use: ['style-loader', 'css-loader']
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      },
      {
        test: /\.less$/,
        // use: ['style-loader', 'css-loader', 'less-loader']
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'less-loader']
      },
      {
        test: /\.s(a|c)ss$/,
        // use: ['style-loader', 'css-loader', 'sass-loader']
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
      }
      ```

### 2. 自动添加css前缀

​	使用`postcss`，需要用到`postcss-loader`和`autoprefixer`插件

  1. 安装

     `npm i -D postcss-loader autoprefixer`

  2. 修改webpack配置文件中的loader，将`postcss-loader`放置在`css-loader`的右边		

     ```js
     {
       test: /\.less$/,
       // use: ['style-loader', 'css-loader', 'less-loader']
       use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader', 'less-loader']
     },
     {
       test: /\.(sa|sc|c)ss$/,
       // use: ['style-loader', 'css-loader', 'sass-loader']
       use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader', 'sass-loader']
     }
     ```

  3. 在项目根目录下添加postcss的配置文件：`postcss.config.js`

  4. 在`postcss`的配置文件中使用插件

         module.exports = {
           plugins: [require('autoprefixer')]
         }

### 3. 开启css压缩

​	需要使用`optimize-css-assets-webpack-plugin`插件

​	由于配置css压缩时会覆盖掉webpack默认的优化配置，导致js代码无法压缩，所以还需要手动把js代码压缩

​	插件导入：`terser-webpack-plugin`

  1. 安装

     `npm i -D optimize-css-assets-webpack-plugin terser-webpack-plugin`

		2. 导入插件

     ```js
     const TerserJSPlugin = require('terser-webpack-plugin')
     const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
     ```

		3. 在webpack配置文件中添加配置

     ```js
     optimization: {
       minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})]
     }
     ```

     Tips: webpack4默认采用的js压缩插件为：`uglifyjs-webpack-plugin`，在`mini-css-extract-plugin`上一个版本中还推荐使用该插件，但最新的v0.6中建议使用`terser-webpack-plugin`来完成js代码压缩



### 4. 抽取公共代码

​	tips：webpack4以上使用的插件为：`SplitChunksPlugin`，以前使用的`CommonsChunkPlugin`已经被移除

​	最新版的webpack只需要在配置文件中的`optimization`节点下添加一个`splitChunks`属性即可进行相关配置

  1. 修改webpack配置文件

     ```js
     optimization: {
       splitChunks: {
         chunks: 'all'
       }
     }
     ```

     