[TOC]
## 优化策略

### gzip 
使用compression-webpack-plugin实现gzip。配置如下：
```javascript
const CompressionWebpackPlugin = require("compression-webpack-plugin")
module.exports = {
    plugins: [
        new CompressionWebpackPlugin({
            test: /\.(js|css)$/,         //匹配要压缩的文件
            algorithm: "gzip"
        })
    ]
}

```
### webpackbar
在打包时实时显示打包进度。配置也很简单，在plugins数组中加入即可。
```javascript
const WebpackBar = require('webpackbar')
module.exports = {
    plugins: [
        new WebpackBar()
    ]
}
```
### JS文件处理
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
// 提取css文件
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
// 压缩css
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');

const {resolve} = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'js/build.js',
    output: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      // 兼容性处理babel-loader @babel/core
      // 1.基本兼容性处理(如promise无法转换) --> @babel/preset-env
      // 2.全部js兼容性处理 -->@babel/polyfill(安装后直接import '@babel/polyfill',缺点是体积太大)
      // 3.按需加载 --> core-js
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          // 预设环境
          presets: [
            "@babel/preset-env",
            {
              // 按需加载
              useBuiltIns: 'usage',
              // 指定core-js版本
              corejs: {
                version: 3
              },
              // 指定兼容具体到那个版本浏览器
              targets: {
                chrome: '60',
                firefox: '60',
                ie: '9',
                safari: '10'
              }

            }
          ]
        }
      },
      /*
        语法检查:
        1.安装包: eslint-loader eslint eslint-plugin-import eslint-config-airbnb-base
        2.在package.json中eslintConfig配置
        "eslintConfig": {
          "extends": "airbnb-base"
        }
      * */
      {
        test: /\.js$/,
        exclude: /node_modules/,
        enforce: 'pre',
        loader: 'eslint-loader',
        options: {
          // 自动修复eslint的错误
          fix: true,
        }
      }
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development'
};
```
### CSS文件处理
使用mini-css-extract-plugin 插件,将CSS文件单独提取处理。
使用optimize-css-assets-webpack-plugin插件，对CSS文件进行压缩处理。
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
// 提取css文件
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
// 压缩css
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');

const {resolve} = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'js/build.js',
    output: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // 创建style标签,将样式放入
          // 'style-loader',
          // 取代 style-loader,提取js中的css成单独文件
          MiniCssExtractPlugin.loader,
          // 将css文件整合到js文件中
          'css-loader',
          // css兼容性处理: postcss--> 需要安装包 postcss-loader, postcss-preset-env
          /*
          需要在package.json中新增 browserslist
          {
            "version": "",
            "browserslist": {
              "development": [
                "last 1 chrome version"
              ],
              "production": [
                ">0.2%",
                "not-dead",
                "not op_mini all"
              ]
            }
          }
          * */
          {
            loader: 'postcss-loader',
            options: {
              plugins: () => {
                // postcss的插件,
                require('postcss-preset-env')()
              }
            }
          }
        ]
      }
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new MiniCssExtractPlugin({
      // 对输出的css文件进行重命名
      filename: 'css/main.css'
    }),
    new OptimizeCssAssetsWebpackPlugin(),
  ],
  mode: 'development'
};
```
### 优化命令行日志显示
使用插件friendly-errors-webpack-plugin

```javascript
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');

module.exports = {
  plugins: [
    new FriendlyErrorsWebpackPlugin(),
  ],
  stats: "errors-only" // 开发环境需放在devServer对象内
}
```
### 基础库分离 CDN加载
`方法一`：使用html-webpack-externals-plugin，将react,react-dom等基础包通过cdn方式引入，不打如bundle中。

```javascript
// CDN 
https://reactjs.org/docs/cdn-links.html
```
```javascript
const HtmlWebpackExternalsPlugin = require('html-webpack-externals-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackExternalsPlugin({
      externals: [
        {
          module: 'react',
          entry: 'https://unpkg.com/react@16/umd/react.production.min.js',
          global: 'React'
        },
        {
          module: 'react-dom',
          entry: 'https://unpkg.com/react-dom@16/umd/react-dom.production.min.js',
          global: 'ReactDOM'
        }
      ]
    })
  ]
};
```
`方法二`：使用SplitChunksPlugin分离基础包
```javascript
// 利用SplitChunksPlugin分离基础包
module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: { // 缓存组
        commons: {
          test: /(react|react-dom)/,
          name:'vendors', // 需要将名称vendors添加到HtmlWebpackPlugins中chunks中
          chunks: 'all'
        }
      }
    }
  }
}
```
### 文件指纹

```javascript
1.hash：和整个项目的构建有关，只要项目文件有修改，整个项目构建的hash值就会更改。
2.chunkhash：和webpack打包的chunk有关，不同的entry会生成不同的chunkhash值。
3.contenthash：根据文件内容来定义hash，文件内容不变，则contenthash不变。
// JS的文件指纹设置
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name][chunkhash:8].js',
    path: __dirname + '/dist'
  }
};

// CSS的文件指纹设置
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name]_[chunkhash:8].js',
    path: __dirname + '/dist'
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name]_[contenthash:8].css'
    })
  ]
};

//图片或文件的指纹设置
module.exports = {
  entry: {
    app: './src/app.js'
  },
  output: {
    filename: '[name][chunkhash:8].js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.(png|svg|gif)$/,
        use: [
          {
            loader: 'file-loader', 
            options: {
              name: 'img/[name][hash:8].[ext]'
            }
          }]
      }
    ]
  }
};
```
### 拆包 splitChunks
webpack4.x中使用splitChunks来进行拆包,抽离第三方依赖库。

```javascript
默认情况下，webpack将会基于以下条件自动分割代码块:
1.新的代码块被共享或者来自node_modules文件夹
2.新的代码块大于30kb(在min+giz之前)
3.按需加载代码块的请求数量应该<=5
4.页面初始化时加载代码块的请求数量应该<=3
```
默认配置
```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: "async", // 默认是async(异步引入的库进行分离) | init(同步引入的库进行分离) | all(所有的库进行分离) 推荐
      minSize: 30000, // 模块的最小体积
      maxSize: 0,
      minChunks: 1, // 模块的最小被引用次数
      maxAsyncRequests: 5, // 按需加载的最大并行请求数
      maxInitialRequests: 3, // 一个入口最大并行请求数
      automaticNameDelimiter: '~', // 文件名的连接符
      name: true,
      cacheGroups: { // 缓存组
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    }
  }
}
```
分离页面公共文件
```javascript
// 利用SplitChunksPlugin分离页面公告文件
module.exports = {
  optimization: {
    splitChunks: {
      minSize: 0, // 分离的包体积大小
      cacheGroups: {
        commons: {
          name:'commons',
          chunks: 'all',
          minChunks: 2 // 设置最新引用次数为2次
        }
      }
    }
  }
}
```
可以通过自定义配置项修改配置，如像这样抽离第三方依赖库.
```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        lib: {
          name: 'chunks-libs',
          test: /[\\/]node_modules[\\/]/,
          priority: 10,
          chunks: 'initial' // 只打包初始时依赖的第三方
        },
        elementUI: {
          name: 'chunk-elementUI', // 单独将 elementUI 拆包
          priority: 20, // 权重要大于 libs 和 app 不然会被打包进 libs 或者 app
          test: /[\\/]node_modules[\\/]element-ui[\\/]/,
          chunks: 'all'
        }
      }
    }
  },
}
```
### 多线程打包 happyPack

```
// 地址
https://github.com/amireh/happypack
```
由于 JavaScript 是单线程模型,在webpack构建过程中，我们需要使用Loader对js，css，图片，字体等文件做转换操作，并且转换的文件数据量也是非常大的，且这些转换操作不能并发处理文件，而是需要一个个文件进行处理，HappyPack的基本原理是将这部分任务分解到多个子进程中去并行处理，子进程处理完成后把结果发送到主进程中，从而减少总的构建时间。(ps:对file-loader和url-loader支持不好，所以这两个loader就不需要换成HappyPack)。
使用：

```javascript
// 多进程并发执行loader 默认为单线程
const HappyPack = require('happypack');
const os = require('os');
const happyThreadPool = HappyPack.ThreadPool({size: os.cpus().length});

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        //把对.js 的文件处理交给id为happyBabel 的HappyPack 的实例执行
        loader: 'happypack/loader?id=happyBabel',
        //排除node_modules 目录下的文件
        exclude: /node_modules/
      },
    ]
  },
  plugins: [
    new HappyPack({
      //用id来标识 happypack处理那里类文件
      id: 'happyBabel',
      //如何处理  用法和loader 的配置一样
      loaders: [{
        loader: 'babel-loader?cacheDirectory=true',
      }],
      //共享进程池
      threadPool: happyThreadPool,
      //允许 HappyPack 输出日志
      verbose: true,
    })
  ]
}
```
happyPack参数说明
```javascript
id: String 用唯一的标识符 id 来代表当前的 HappyPack 是用来处理一类特定的文件.
loaders: Array 用法和 webpack Loader 配置中一样.
threads: Number 代表开启几个子进程去处理这一类型的文件，默认是3个，类型必须是整数。
verbose: Boolean 是否允许 HappyPack 输出日志，默认是 true。
threadPool: HappyThreadPool 代表共享进程池，即多个 HappyPack 实例都使用同一个共享进程池中的子进程去处理任务，以防止资源占用过多。
verboseWhenProfiling: Boolean 开启webpack --profile ,仍然希望HappyPack产生输出。
debug: Boolean 启用debug 用于故障排查。默认 false。
```
`注意：`HappyPack 的作者现在基本不维护此插件，推荐使用 `Webpack 官方 thread-loader`。
```javascript
const commonConfig = {
  // ...
  module: {
    rules: [{
      test: /\.jsx?$/,
      // exclude: /node_modules/,
      // include: path.resolve(__dirname, '../src'), 
      use: [
        {
          loader: 'thread-loader',
          options: {
            workers: 3, // 开启几个 worker 进程来处理打包，默认是 os.cpus().length - 1
          }
        },
        'babel-loader'
      ]
    }]
  },
  // ...
}
```
### 缓存与增量缓存
缓存构建:webpack构建中,一般需要使用许多loader来预处理文件,以babel-loader为例。可以通过设置cacheDirectory或cacheDirectory=true来达到缓存的目的。
例如：

```javascript
{  
    test: /\.js$/,  
    loader: 'babel-loader?cacheDirectory',  
    exclude: /node_modules/, // 排除不处理的目录  
}
```
cacheDirectory对loader转译后的结果进行缓存,之后的webpack进行构建时，都会去尝试读取缓存来避免高耗能的babel重新转译过程。
增量构建:使用增量构建而不是全量构建有利于构建速度的提升。
需要注意的是：全量构建即每次重新构建都需要重新编译一次(包括未修改部分),而增量构建对于未修改的部分不会再重新编译,对于rebuild能够大大提高编译速度。对于开发阶段,可以使用webpack-dev-server来达到增量编译的目的,对于生产阶段,可以通过给生成的文件添加hash(或chunkhash 或contenthash )来实现增量构建。
```javascript
output: {  
    path: config.build.assetsRoot,  
    filename: utils.assetsPath('js/[name].[chunkhash:8].js'),  
    chunkFilename: utils.assetsPath('js/[name].[chunkhash:8].js')
}
```
### 使用动态链接库DllPlugin
dll全称为动态链接库,先通过dllPlugin生成清单文件(这个文件包含了从 require 和 import 的request到模块 id 的映射),然后通过DllReferencePlugin引用该清单文件,将依赖的名称映射到模块的 id 上。这样每次打包时.先去查找清单里中是否已经存在这个依赖，如果已经存在，则不打包，如果还没存在，则需要打包。
与通过externals 的方式引入第三方库类似,dll主要用于那些没有可以在<script>标签中引入的资源的模块（纯npm包)。
使用方法是，新建一个webpack.dll.conf.js文件,并执行webpack --config ./build/webpack.dll.conf.js会在static/js文件夹下生成dll.vendor.js以及在根目录下生成mainfest.json

```javascript
//webpack.dll.conf.js
const webpack = require('webpack');
const {resolve} = require('path');

module.exports = {
  entry: {
    // vendor 表示最终生成的[name] --> vendor
    // ['react', 'react-router', 'redux', 'axios'] --> 表示要打包的库
    vendor: ['react', 'react-router', 'redux', 'axios']
  },
  output: {
    filename: '[name].js',
    path: resolve(__dirname, 'dll'),
    library: '[name]_[hash]' // [name]_[hash] 打包的库里面向外暴露出去的内容叫什么 
  },
  plugins: [
    // 打包生成一个mainfest.json --> 提供映射关系
    new webpack.DllPlugin({
      name: '[name]_[hash]', // 映射库的暴露内容名称
      path: resolve(__dirname, 'dll/manifest.json')
    })
  ]
};
```
并修改webpack.config.js
````javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');
const webpack = require('webpack');
const {resolve} = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'js/build.js',
    output: resolve(__dirname, 'build')
  },
  module: {
    rules: []
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    // 告诉webpack那些库不参与打包,同时使用时的名称也要变
    new webpack.DllReferencePlugin({
      mainfest: resolve(__dirname, 'dll/mainfest.json')
    }),
    // 将某个文件在html中自动引入资源
    new AddAssetHtmlWebpackPlugin({
      filepath: resolve(__dirname, 'dll/vendor.js')
    })
  ],
  mode: 'production'
};
```
### 使用HardSourceWebpackPlugin，比dll更优秀的插件
比dll更优秀，使用更简单
```javascript
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');

module.exports = {
  // ......
  plugins: [
    new HardSourceWebpackPlugin() // <- 直接加入这行代码就行
  ]
}
```
### 分析工具 webpack-bundle-analyzer

```
// 地址
https://github.com/webpack-contrib/webpack-bundle-analyzer
stat：文件未经过任何转换的原始大小
parsed：文件经过转换后的输出大小（比如babel-loader转换ES6->ES5、UglifyJsPlugin压缩等等）
gzip：parsed后的文件，经过Gzip压缩的大小
```
查看 webpack 打包后所有组件与组件间的依赖关系,可以通过打包分析工具`webpack-bundle-analyzer`来实现。
使用：
```javascript
plugins:[
  new BundleAnalyzerPlugin({
    // analyzerPort: 8080,  
    // openAnalyzer: false,  
    // generateStatsFile: false
  })
]
```
在项目配置文件(如webpack.base.conf.js)中通过DllReferencePlugin引入
```javascript
// build/webpack.base.conf.js
const manifest = require('../manifest.json')

// 插件中引入
plugins: [
  new webpack.DllReferencePlugin({
    mainfest
  })
]
```
然后在index.html手动引入该文件
```
<script type="text/javascript" src="/static/js/dll.vendor.js"></script>
```

### JS代码压缩 
插件1：uglifyjs-webpack-plugin
使用uglifyjs-webpack-plugin来压缩js,重点属性parallel,用来开启多进程并行执行js压缩,提高构建速度，但是它对ES6的压缩不是很好，webpack4默认是使用terser-webpack-plugin插件进行压缩，所以也推荐使用terser-webpack-plugin插件。
```javascript
//压缩js
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')

//在optimization.minimizer中引用
new UglifyJsPlugin({
  uglifyOptions: {
    warnings: false,
    mangle: {
      safari10: true
    },
    compress: {
      drop_debugger: false,
      drop_console: true, //console      
      pure_funcs: ['console.log'] // 移除console    
    },
    output: {
      // 去掉注释内容      
      comments: false
    }
  },
  sourceMap: false,
  cache: true,
  parallel: true
})
```
使用terser-webpack-plugin插件`推荐`
```javascript
const TerserPlugin = require('terser-webpack-plugin');
// ...
const commonConfig = {
  // ...
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        parallel: 4, // 开启几个进程来处理压缩，默认是 os.cpus().length - 1
      }),
    ],
  },
  // ...
}
```
### css 压缩
使用optimize-css-assets-webpack-plugin插件。默认的压缩引擎是cssnano。压缩css另外一个插件是mini-css-extract-plugin。
```javascript
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
// ...
const prodConfig = {
  // ...
  optimization: {
    minimizer: [
      new OptimizeCSSAssetsPlugin({
        assetNameRegExp: /\.optimize\.css$/g,
        cssProcessor: require('cssnano'),
        cssProcessorPluginOptions: {
          preset: ['default', { discardComments: { removeAll: true } }],
        },
        canPrint: true,
      })
    ]
  },
}
```
### 图片压缩
使用插件image-webpack-loader。使用是在file-loader之后加入image-webpack-loader即可。
```javascript
module: {
  rules: [
    {
      test: /\.(png|jpg|gif)$/,
      use: [
        {
          loader: 'file-loader',
          options: {
            name: '[name]_[hash].[ext]',
            outputPath: 'images/',
          }
        },
        {
          loader: 'image-webpack-loader',
          options: {
            // 压缩 jpeg 的配置
            mozjpeg: {
              progressive: true,
              quality: 65
            },
            // 使用 imagemin**-optipng 压缩 png，enable: false 为关闭
            optipng: {
              enabled: false,
            },
            // 使用 imagemin-pngquant 压缩 png
            pngquant: {
              quality: '65-90',
              speed: 4
            },
            // 压缩 gif 的配置
            gifsicle: {
              interlaced: false,
            },
            // 开启 webp，会把 jpg 和 png 图片压缩为 webp 格式
            webp: {
              quality: 75
            }
          }
        }
      ]
    },
  ]
}
```
### 压缩html html-webpack-plugin
压缩html代码,并实现自动化注入脚本以及样式文件,对于文件名中包含哈希的Webpack捆绑包尤其有用。

```javascript
// 压缩html
const HtmlWebpackPlugin = require('html-webpack-plugin')

// 在plugins中引入
module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      filename: path.resolve(__dirname, '../dist/index.html'),
      template: 'index.html',
      chunks: ['vendors','index'],
      favicon: resolve('favicon.ico'),
      title: '标题',
      inject: true,
      minify: {
        html5:true,
        collapseWhitespace: true,
        removeComments: true,
        removeAttributeQuotes: true,
        minifyJS: true,
        minifyCSS: true
      }
    })
  ]
}
```
### 压缩lodash-webpack-plugin

```
$ npm i --D lodash-webpack-plugin babel-plugin-lodash 
```
使用
```javascript
var LodashModuleReplacementPlugin = require('lodash-webpack-plugin');
var webpack = require('webpack');

module.exports = {
  module: {
    rules: [{
      use: 'babel-loader',
      test: /\.js$/,
      exclude: /node_modules/,
      options: {
        plugins: ['lodash'],
        presets: [['env', { 'modules': false, 'targets': { 'node': 4 } }]]
      }
    }]
  },
  plugins: [
    new LodashModuleReplacementPlugin(),
    new webpack.optimize.UglifyJsPlugin
  ]
};
```
### 速度分析
使用插件speed-measure-webpack-plugin
```javascript
const SpeedMeasurePlugin = require('speed-measure-webpack-plugin');
const smp = new SpeedMeasurePlugin();

module.exports = smp.wrap({
  plugins: [
    new MyPlugin(),
    new MyOtherPlugin()
  ]
});
```
### 懒加载
```javascript
import React from 'react';
import { render } from 'react-dom';

// 写个辅助的类
class Music extends React.Component {
  constructor() {
    super();
    this.state = { lrc: [] };
  }
  showLrc() {
    // 通过ES6的import()方法实现了懒加载功能，实际上是利用了jsonp去动态导入了
    import('./lrc').then(data => {
      let lrc = data.default.split('\n').filter(item => item !== '');
      this.setState({ lrc });
    });
  }
  render() {
    return (
      <div>
        <button className="btn" onClick={() => this.showLrc()}>显示歌词</button>
        <div className="lrc-box">
          {this.state.lrc && this.state.lrc.map((item, index) => (
            <p className="lrc" key={index}>{item}</p>
          ))}
        </div>
      </div>
    )
  }
}
```
```
使用 import() 动态加载模块
使用 React.lazy() 动态加载组件
使用 lodable-component 动态加载路由，组件或者模块
```
### 使用progress-bar-webpack-plugin 插件
```javascript
const ProgressBarPlugin = require("progress-bar-webpack-plugin");
const chalk = require("chalk");

module.exports = {
  plugins: [
    new ProgressBarPlugin({
      format: " build [:bar] " + chalk.green.bold(":percent") + " (:elapsed seconds)",
      clear: false
    })
  ]
};
```