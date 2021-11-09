* webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个bundle。![](./Webpack/1.png)

   webpack的基本功能

  * 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等等
  * ⽂件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图⽚等
  * 代码分割：提取多个⻚⾯的公共代码、提取⾸屏不需要执⾏部分的代码让其异步加载
  * 模块合并：在采⽤模块化的项⽬有很多模块和⽂件，需要构建功能把模块分类合并成⼀个⽂件
  * ⾃动刷新：监听本地源代码的变化，⾃动构建，刷新浏览器
  * 代码校验：在代码被提交到仓库前需要检测代码是否符合规范，以及单元测试是否通过
  * ⾃动发布：更新完代码后，⾃动构建出线上发布代码并传输给发布系统

* webpack构建过程

  * 从entry⾥配置的module开始递归解析entry依赖的所有module
  * 每找到⼀个module，就会根据配置的loader去找对应的转换规则
  * 对module进⾏转换后，再解析出当前module依赖的module
  * 这些模块会以entry为单位分组，⼀个entry和其所有依赖的module被分到⼀个组Chunk
  * 最后webpack会把所有Chunk转换成⽂件输出
  * 在整个流程中webpack会在恰当的时机执⾏plugin⾥定义的逻辑

* webpack打包原理：将所有依赖打包成⼀个bundle.js，通过代码分割成单元⽚段按需加载；bundle是webpack打包出来的⽂件，chunk是webpack在进⾏模块的依赖分析的时候，代码分割出来的代码块。module是开发中的单个模块

* webpack 五个核心概念

  * Entry：入口(Entry)指示 webpack 以哪个文件为入口起点开始打包，分析构建内部依赖图。

  * Output：输出(Output)指示 webpack 打包后的资源 bundles 输出到哪里去，以及如何命名。

  * Loader：让 webpack 能够去处理那些非 JS 的文件，比如样式文件、图片文件(webpack 自身只理解JS)

  * Plugins：可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量等。

  * Mode：指示 webpack 使用相应模式的配置。

    | 选项        | 描述                                                         | 特点                       |
    | ----------- | :----------------------------------------------------------- | :------------------------- |
    | development | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。 | 能让代码本地调试运行的环境 |
    | production  | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 TerserPlugin。 | 能让代码优化上线运行的环境 |

* Webpack 初体验

  * 初始化配置

    初始化package.json文件，输入指令：npm init

    下载并安装 webpack，输入指令:npm install webpack webpack-cli -g（全局安装）（即使以前安装过，现在也是可以安装的，会对以前的进行更新）npm install webpack webpack-cli -D（本地安装）

  * 编译打包应用

    创建文件，运行指令；开发环境指令：webpack src/js/index.js -o build/js/built.js --mode=development，功能：webpack 能够编译打包 js 和 json 文件，并且能将 es6 的模块化语法转换成 浏览器能识别的语法。生产环境指令：webpack src/js/index.js -o build/js/built.js --mode=production，功能：在开发配置功能上多一个功能，压缩代码。

    结论：webpack 能够编译打包 js 和 json 文件；能将 es6 的模块化语法转换成浏览器能识别的语法；能压缩代码。

    问题：不能编译打包 css、img 等文件；不能将 js 的 es6 基本语法转化为 es5 以下语法

* webpack 开发环境的基本配置

  * 创建文件 webpack.config.js，配置内容如下，运行指令: webpack，结论: 此时功能与上面一致

    ```javascript
    const { resolve } = require('path'); // node 内置核心模块，用来处理路径问题。
    
    module.exports = {
      entry: './src/js/index.js',
      // 入口文件
      output: {
        // 输出配置
        filename: './built.js',
        // 输出文件名
        path: resolve(__dirname, 'build/js')// 输出文件路径配置
      },
      mode: 'development'//开发环境
    };
    ```

  * 打包样式资源

    创建文件，下载安装包npm i css-loader style-loader less-loader less -D，修改配置文件webpack.config.js，运行指令：webpack

    ```javascript
    /*
      webpack.config.js  webpack的配置文件（src里面写项目代码，webpack写配置代码）
        作用: 指示 webpack 干哪些活（当你运行 webpack 指令时，会加载里面的配置）
    
        所有构建工具都是基于nodejs平台运行的~模块化默认采用commonjs。
    */
    
    // resolve用来拼接绝对路径的方法
    const {
      resolve
    } = require('path');
    
    module.exports = {
      // webpack配置
      // 入口起点
      entry: './src/index.js',
      // 输出（输出到build文件夹下面的built.js文件中）
      output: {
        // 输出文件名
        filename: 'built.js',
        // 输出路径
        // __dirname nodejs的变量，代表当前文件的目录绝对路径（表示的是与当前webpack.config.js平级的build文件夹）
        path: resolve(__dirname, 'build')
      },
      // loader的配置
      module: {
        rules: [
          // 详细loader配置
          // 不同文件必须配置不同loader处理
          {
            // 匹配哪些文件
            test: /\.css$/,
            // 使用哪些loader进行处理
            use: [
              // use数组中loader执行顺序：从右到左，从下到上 依次执行
              // 创建style标签，将js中的样式资源插入进行，添加到head中生效
              'style-loader',
              // 将css文件变成commonjs模块加载js中，里面内容是样式字符串
              'css-loader'
            ]
          }, {
            test: /\.less$/,
            use: [
              'style-loader',
              'css-loader',
              // 将less文件编译成css文件
              // 需要下载 less-loader和less
              'less-loader'
            ]
          }
        ]
      },
      // plugins的配置
      plugins: [
        // 详细plugins的配置
      ],
      // 模式（表示使用什么模式）
      mode: 'development', // 开发模式
      // mode: 'production'
    }
    
    /*
      执行的步骤：只分析less（安装loader:style-loader css-loader less-loader less）(css只需要安装前面两个loader)
        1. 根据entry找到入口文件'./src/index.js'
        2. 发现入口文件中引入了.less文件，而.less文件不是js或者json文件，去找module的rules里面寻找less的loader
        3. 找到test: /\.less$/，执行该对象中的rules数组中的代码（从右到左，从下到上 依次执行）
        4. 通过less-loader将less文件编译成css文件
        5. 通过css-loader将css文件变成commonjs模块加载js中，里面内容是样式字符串
        6. 通过style-loader创建style标签，将js中的样式资源插入进行，添加到head中生效
        7. 将代码添加到出口文件与当前webpack.config.js平级的build文件夹下面的built.js文件中
    */
    ```

  * 打包html资源

    创建文件，下载安装包npm install --save-dev html-webpack-plugin，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/index.js',
      output: {
        filename: 'built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
          test: /\.less$/,
          // 要使用多个loader处理用use
          use: ['style-loader', 'css-loader', 'less-loader']
        }, {
          // 问题：默认处理不了html中img图片
          // 处理图片资源
          test: /\.(jpg|png|gif)$/,
          // 使用一个loader
          // 下载 url-loader file-loader(url-loader依赖于file-loader)
          loader: 'url-loader',
          options: {
            // 图片大小小于8kb，就会被base64处理（通常小图片(8-12kb)使用limit进行这种处理，如果有9kb的图片，我们可以将limit写成10 * 1024）
            // 优点: 减少请求数量（减轻服务器压力）
            // 缺点：图片体积会更大（文件请求速度更慢）
            limit: 8 * 1024,
            // 问题：因为url-loader默认使用es6模块化解析，而html-loader引入图片是commonjs
            // 解析时会出问题：[object Module]
            // 解决：关闭url-loader的es6模块化，使用commonjs解析
            esModule: false,
            // 给图片进行重命名
            // [hash:10]取图片的hash的前10位
            // [ext]取文件原来扩展名
            name: '[hash:10].[ext]'
          }
        }, {
          test: /\.html$/,
          // 处理html文件的img图片,而不是处理html文件的，html文件是用HtmlWebpackPlugin处理的（负责引入img，从而能被url-loader进行处理）
          loader: 'html-loader'
        }]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development'
    };
    /*
      执行的步骤：打包图片资源（安装loader:url-loader file-loader html-loader）
        1. 根据entry找到入口文件'./src/index.js'
        2. 发现入口文件中引入了jpg|png|gif文件，而jpg|png|gif文件不是js或者json文件，去找module的rules里面寻找jpg|png|gif的loader
        3. 找到test: /\jpg|png|gifs$/，执行该对象中的rules数组中的代码
        4. 通过options-->limit对体积进行限制
        5. 这种方式只能处理样式中引入的图片，不能处理html中引入的图片，如果要处理htnl中引入的资源，需要使用html-loader
        6. 处理html中引入的图片需要找到test: /\jpg|png|gifs$/，执行里面的html-loader
        7. 但是解析时图片的名称会出现[object Module]问题，这是因为url-loader默认使用es6模块化解析，而html-loader引入图片是commonjs，使用esModule: false关闭url-loader的es6模块化，使用commonjs解析即可
        8. 使用name: '[hash:10].[ext]'给图片进行重命名
    */
    ```

  * 打包其他资源

    创建文件，下载安装包npm install --save-dev file-loader，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const { resolve } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/index.js',
      output: {
        filename: 'built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          {
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
          },
          // 打包其他资源(除了html/js/css资源以外的资源)
          {
            // 排除css/js/html资源
            exclude: /\.(css|js|html|less)$/,
            loader: 'file-loader',
            options: {
              name: '[hash:10].[ext]'
            }
          }
        ]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development'
    };
    ```

  * devserver

    创建文件，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/index.js',
      output: {
        filename: 'built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
          },
          // 打包其他资源(除了html/js/css资源以外的资源)
          {
            // 排除css/js/html资源
            exclude: /\.(css|js|html|less)$/,
            loader: 'file-loader',
            options: {
              name: '[hash:10].[ext]'
            }
          }
        ]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development',
    
      // 要安装 webpack-dev-server（因为是本地安装，所以需要使用npx webpack-dev-server启动，webpack我们使用的全局安装，所以启动的时候不需要使用npx）
      // 开发服务器 devServer：用来自动化（自动编译，自动打开浏览器，自动刷新浏览器~~）
      // 特点：只会在内存中编译打包，不会有任何输出
      // 启动devServer指令为：npx webpack-dev-server
      devServer: {
        // 项目构建后路径
        contentBase: resolve(__dirname, 'build'),
        // 启动gzip压缩(使得打包后的代码体积更小)
        compress: true,
        // 端口号
        port: 3000,
        // 自动打开浏览器
        open: true
      }
    };
    ```

  *  开发环境配置（基本模板）

    创建文件，下载安装包，上面下载的loader/plugin文件，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    /*
      开发环境配置：能让代码运行
        运行项目指令：
          webpack 会将打包结果输出出去
          npx webpack-dev-server 只会在内存中编译打包，没有输出
    
      所有的代码打包输出到js/built.js文件中
      随着我们的资源越来越多，我们还需要将代码分类打包，便是在每一个loader中使用outputPath: build文件夹下面的需要放置打包文件的文件夹名
      css、less等文件不需要设置专门的outputPath，因为它们是直接打包成字符串放进js文件中的
    */
    
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          // loader的配置
          {
            // 处理less资源
            test: /\.less$/,
            use: ['style-loader', 'css-loader', 'less-loader']
          }, {
            // 处理css资源
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
          }, {
            // 处理图片资源
            test: /\.(jpg|png|gif)$/,
            loader: 'url-loader',
            options: {
              limit: 8 * 1024,
              name: '[hash:10].[ext]',
              // 关闭es6模块化
              esModule: false,
              outputPath: 'imgs'
            }
          }, {
            // 处理html中img资源
            test: /\.html$/,
            loader: 'html-loader'
          }, {
            // 处理其他资源
            exclude: /\.(html|js|css|less|jpg|png|gif)/,
            loader: 'file-loader',
            options: {
              name: '[hash:10].[ext]',
              outputPath: 'media'
            }
          }
        ]
      },
      plugins: [
        // plugins的配置
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development',
      devServer: {
        contentBase: resolve(__dirname, 'build'),
        compress: true,
        port: 3000,
        open: true
      }
    };
    ```

* webpack 生产环境的基本配置

  * 提取 css 成单独文件

    创建文件，下载插件安装包npm install --save-dev mini-css-extract-plugin，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    //使用这个插件的作用：style样式不是放在style标签中的，而是通过link的方式使用
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
          test: /\.css$/,
          use: [
            // 'style-loader'作用：创建style标签，将样式放入, 
            // 这个loader取代style-loader。作用：提取js中的css成单独文件
            MiniCssExtractPlugin.loader,
            // 将css文件整合到js文件中
            'css-loader'
          ]
        }]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        }),
        new MiniCssExtractPlugin({
          // 对输出的css文件进行重命名
          filename: 'css/built.css'
        })
      ],
      mode: 'development'
    };
    ```

  * css 兼容性处理

    创建文件，下载loader安装包npm install --save-dev postcss-loader postcss-preset-env，修改配置文件webpack.config.js

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    
    // 设置nodejs环境变量
    // process.env.NODE_ENV = 'development';
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
          test: /\.css$/,
          use: [
            MiniCssExtractPlugin.loader,
            'css-loader',
            /*
              css兼容性处理：postcss --> postcss-loader postcss-preset-env（作用：postcss-preset-env帮助postcss识别环境从而加载对应的配置，从而使得代码兼容每一个浏览器的版本）
    
             postcss-preset-env： 帮postcss找到package.json中browserslist里面的配置，通过配置加载指定的css兼容性样式
    
              //browserslist要写在package.json中
              "browserslist": {
                // 开发环境 --> 设置node环境变量：process.env.NODE_ENV = development
                "development": [
                  "last 1 chrome version",//兼容最近的版本
                  "last 1 firefox version",
                  "last 1 safari version"
                ],
                // 生产环境：默认是看生产环境
                "production": [
                  ">0.2%",
                  "not dead",//不用已经死的浏览器
                  "not op_mini all"//不用op_mini版本的浏览器
                ]
              }
            */
            // 使用loader的默认配置
            // 'postcss-loader',
            // 修改loader的配置，写成下面的对象的形式
            {
              loader: 'postcss-loader',
              options: {
                ident: 'postcss',
                plugins: () => [
                  // postcss的插件
                  require('postcss-preset-env')()
                ]
              }
            }
          ]
        }]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        }),
        new MiniCssExtractPlugin({
          filename: 'css/built.css'
        })
      ],
      mode: 'development'
    };
    ```

    修改package.json，运行指令：webpack

    ```js
     "browserslist": {
                // 开发环境 --> 设置node环境变量：process.env.NODE_ENV = development
                "development": [
                  "last 1 chrome version",//兼容最近的版本
                  "last 1 firefox version",
                  "last 1 safari version"
                ],
                // 生产环境：默认是看生产环境
                "production": [
                  ">0.2%",
                  "not dead",//不用已经死的浏览器
                  "not op_mini all"//不用op_mini版本的浏览器
                ]
              }
    ```

  * 压缩css

    创建文件，下载插件安装包npm install --save-dev optimize-css-assets-webpack-plugin，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const { resolve } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
    
    // 设置nodejs环境变量
    // process.env.NODE_ENV = 'development';
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          {
            test: /\.css$/,
            use: [
              MiniCssExtractPlugin.loader,
              'css-loader',
              {
                loader: 'postcss-loader',
                options: {
                  ident: 'postcss',
                  plugins: () => [
                    // postcss的插件
                    require('postcss-preset-env')()
                  ]
                }
              }
            ]
          }
        ]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        }),
        new MiniCssExtractPlugin({
          filename: 'css/built.css'
        }),
        // 压缩css
        new OptimizeCssAssetsWebpackPlugin()
      ],
      mode: 'development'
    };
    ```

  * js语法检查

    创建文件，下载插件安装包npm install --save-dev eslint-loader eslint eslint-config-airbnb-base eslint-plugin-import，修改配置文件webpack.config.js

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          /*
            语法检查： eslint-loader  eslint
              注意：只检查自己写的源代码，第三方的库是不用检查的
              设置检查规则：
                package.json中eslintConfig中设置~
                  "eslintConfig": {
                    "extends": "airbnb-base"
                  }
                airbnb --> eslint-config-airbnb-base  eslint-plugin-import eslint
          */
          {
            test: /\.js$/,
            exclude: /node_modules/, //排除第三方的代码，只检查自己的代码
            loader: 'eslint-loader',
            options: {
              // 自动修复eslint的错误
              fix: true
            }
          }
        ]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development'
    };
    /*
      1. 语法检查我们需要使用eslint-loader  eslint，所以先下载eslint-loader  eslint
      2. 使用exclude: /node_modules/排除第三方的代码，只检查自己的代码
      3. 语法检查通常采用airbnb（可以在网站https://github.com/topics/javascript中找出进行详细的看里面的介绍，我们想要将airbnb库与eslint结合在一起的话，去npmjs网站中搜索elsint找到eslint-config-airbnb-base，点进去根据需求下载安装即可，eslint-config-airbnb是可以检测react代码的，用到react编写代码的时候可以使用这个库），因此需要下载eslint-config-airbnb-base  eslint-plugin-import eslint
      4. 在package.json中配置语法检查采用的标准
      5. 检查出现语法问题的代码使用fix: true实现自动修复eslint出现的语法错误
      6. 如果在代码中使用console.log()等语法，eslint会出现提示警告，此时我们可以在console.log()上一行加上一行注释// eslint-disable-next-line表示下一行eslint所有规则都失效（下一行不进行eslint检查）
    */
    ```

    配置package.json文件，运行指令：webpack

    ```js
    "eslintConfig": {
        "extends": "airbnb-base",
        "env": {
          "browser": true
        }
      }
    ```

  * js兼容性处理

    创建文件，下载插件安装包npm install --save-dev babel-loader @babel/core @babel/preset-env @babel/polyfill core-js，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          /*
            js兼容性处理：安装babel-loader @babel/core 两个库
              1. 基本js兼容性处理 --> 安装@babel/preset-env库
                问题：只能转换基本语法，如promise高级语法不能转换
              2. 需要做兼容性处理的就做：按需加载  --> 安装core-js库并在presets里面作如下配置
              
              前两步骤是我们常用的兼容性处理的方式
    
              3. 全部js兼容性处理 --> 安装babel-loader @babel/core @babel/preset-env @babel/polyfill  
              不需要进行配置，只需要在主入口文件中引入 @babel/polyfill即可（import '@babel/polyfill'）
                问题：我只要解决部分兼容性问题，但是将所有兼容性代码全部引入，体积太大了，所以我们不使用这种方式~
          */
          {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              // 预设：指示babel做怎么样的兼容性处理
              presets: [
                [
                  '@babel/preset-env', {
                    // 按需加载
                    useBuiltIns: 'usage',
                    // 指定core-js版本
                    corejs: {
                      version: 3
                    },
                    // 指定兼容性做到哪个版本浏览器
                    targets: {
                      chrome: '60',
                      firefox: '60',
                      ie: '9',
                      safari: '10',
                      edge: '17'
                    }
                  }
                ]
              ]
            }
          }
        ]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development'
    };
    ```

  * js压缩

    创建文件，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      // 生产环境下会自动压缩js代码//内部会自动加载一些插件
      mode: 'production'
    };
    ```

  * html压缩

    创建文件，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const { resolve } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html',
          // 压缩html代码
          minify: {
            // 移除空格
            collapseWhitespace: true,
            // 移除注释
            removeComments: true
          }
        })
      ],
      mode: 'production'
    };
    ```

  * 生产环境配置

    创建文件，下载插件安装包（前面的所有的安装包），修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    // 定义nodejs环境变量：决定使用browserslist的哪个环境
    process.env.NODE_ENV = 'production';
    
    // 复用loader
    const commonCssLoader = [
      MiniCssExtractPlugin.loader,
      'css-loader', {
        // 还需要在package.json中定义browserslist
        loader: 'postcss-loader',
        options: {
          ident: 'postcss',
          plugins: () => [require('postcss-preset-env')()]
        }
      }
    ];
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
            test: /\.css$/,
            use: [...commonCssLoader]
          }, {
            test: /\.less$/,
            use: [...commonCssLoader, 'less-loader']
          },
          /*
            正常来讲，一个文件只能被一个loader处理。
            当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序：
              先执行eslint 在执行babel
          */
          {
            // 在package.json中eslintConfig --> airbnb
            test: /\.js$/,
            exclude: /node_modules/,
            // 优先执行
            enforce: 'pre',
            loader: 'eslint-loader',
            options: {
              fix: true
            }
          }, {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              presets: [
                [
                  '@babel/preset-env', {
                    useBuiltIns: 'usage',
                    corejs: {
                      version: 3
                    },
                    targets: {
                      chrome: '60',
                      firefox: '60',
                      ie: '9',
                      safari: '10',
                      edge: '17'
                    }
                  }
                ]
              ]
            }
          }, {
            test: /\.(jpg|png|gif)/,
            loader: 'url-loader',
            options: {
              limit: 8 * 1024,
              name: '[hash:10].[ext]',
              outputPath: 'imgs',
              esModule: false
            }
          }, {
            test: /\.html$/,
            loader: 'html-loader'
          }, {
            exclude: /\.(js|css|less|html|jpg|png|gif)/,
            loader: 'file-loader',
            options: {
              outputPath: 'media'
            }
          }
        ]
      },
      plugins: [
        new MiniCssExtractPlugin({
          filename: 'css/built.css'
        }),
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
          template: './src/index.html',
          minify: {
            collapseWhitespace: true,
            removeComments: true
          }
        })
      ],
      mode: 'production'
    };
    ```

* webpack 优化配置

  * 开发环境性能优化
    * 优化打包构建速度
      * HMR
    * 优化代码调试
      * source-map

  * 生产环境性能优化
    * 优化打包构建速度
      * oneOf
      * babel缓存
      * 多进程打包
      * externals
      * dll
    * 优化代码运行的性能
      * 缓存(hash-chunkhash-contenthash)
      * tree shaking
      * code split
      * 懒加载/预加载
      * pwa

  * HMR

    创建文件，修改配置文件webpack.config.js

    ```js
    /*
      1.为什么要启用热更新？
      当我们改变页面中的css文件的时候会触发整个文件包括js文件都会重新打包进行更新，这样会造成慢的打包速度，这是我们不想要的结果，所以要进行热更新，只对修改的那个文件进行重新打包，进行更新。
    
      2.HMR: hot module replacement 热模块替换 / 模块热替换（在devServer中将hot设置为true即为开启）
        作用：一个模块发生变化，只会重新打包这一个模块（而不是打包所有模块） 
          极大提升构建速度
          
          样式文件：可以使用HMR功能：因为style-loader内部实现了~
    
          js文件：默认不能使用HMR功能 --> 需要修改js代码，添加支持HMR功能的代码
            注意：HMR功能对js的处理，只能处理非入口js文件的其他文件。
    
          html文件: 默认不能使用HMR功能.同时会导致问题：html文件不能热更新了~ （不用做HMR功能，因为只有一个html文件，只要里面的内容发生变化，文件是一定要更新的）
            解决：修改entry入口，将html文件引入,这样才可以生效
    */
    
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: ['./src/js/index.js', './src/index.html'],
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          // loader的配置
          {
            // 处理less资源
            test: /\.less$/,
            use: ['style-loader', 'css-loader', 'less-loader']
          }, {
            // 处理css资源
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
          }, {
            // 处理图片资源
            test: /\.(jpg|png|gif)$/,
            loader: 'url-loader',
            options: {
              limit: 8 * 1024,
              name: '[hash:10].[ext]',
              // 关闭es6模块化
              esModule: false,
              outputPath: 'imgs'
            }
          }, {
            // 处理html中img资源
            test: /\.html$/,
            loader: 'html-loader'
          }, {
            // 处理其他资源
            exclude: /\.(html|js|css|less|jpg|png|gif)/,
            loader: 'file-loader',
            options: {
              name: '[hash:10].[ext]',
              outputPath: 'media'
            }
          }
        ]
      },
      plugins: [
        // plugins的配置
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development',
      devServer: {
        contentBase: resolve(__dirname, 'build'),
        compress: true,
        port: 3000,
        open: true,
        // 开启HMR功能
        // 当修改了webpack配置，新配置要想生效，必须重启webpack服务
        hot: true
      }
    };
    ```

    js代码需要在代码中添加，运行指令：webpack

    ```js
    if (module.hot) {
    	// 一旦 module.hot 为true，说明开启了HMR功能。 --> 让HMR功能代码生效
    	module.hot.accept('./print.js', function() {
    		// 方法会监听 print.js 文件的变化，一旦发生变化，其他模块不会重新打包构建。
    		// 会执行后面的回调函数
    		//此处相当于是监听到print.js发生了变化，执行print()函数
    		print();
    	});
    }
    ```

  * source-map

    创建文件，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: ['./src/js/index.js', './src/index.html'],
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          // loader的配置
          {
            // 处理less资源
            test: /\.less$/,
            use: ['style-loader', 'css-loader', 'less-loader']
          }, {
            // 处理css资源
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
          }, {
            // 处理图片资源
            test: /\.(jpg|png|gif)$/,
            loader: 'url-loader',
            options: {
              limit: 8 * 1024,
              name: '[hash:10].[ext]',
              // 关闭es6模块化
              esModule: false,
              outputPath: 'imgs'
            }
          }, {
            // 处理html中img资源
            test: /\.html$/,
            loader: 'html-loader'
          }, {
            // 处理其他资源
            exclude: /\.(html|js|css|less|jpg|png|gif)/,
            loader: 'file-loader',
            options: {
              name: '[hash:10].[ext]',
              outputPath: 'media'
            }
          }
        ]
      },
      plugins: [
        // plugins的配置
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'development',
      devServer: {
        contentBase: resolve(__dirname, 'build'),
        compress: true,
        port: 3000,
        open: true,
        hot: true
      },
      devtool: 'eval-source-map' //使用：直接加上这句话即可
    };
    
    /*
      source-map: 一种 提供源代码到构建后代码映射 技术 （如果构建后代码出错了，通过映射可以追踪源代码错误）
    
        [inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map
    
        source-map：外部
          错误代码准确信息 和 源代码的错误位置
        inline-source-map：内联
          只生成一个内联source-map
          错误代码准确信息 和 源代码的错误位置
        hidden-source-map：外部
          错误代码错误原因，但是没有错误位置
          不能追踪源代码错误，只能提示到构建后代码的错误位置
        eval-source-map：内联
          每一个文件都生成对应的source-map，都在eval
          错误代码准确信息 和 源代码的错误位置
        nosources-source-map：外部
          错误代码准确信息, 但是没有任何源代码信息
        cheap-source-map：外部
          错误代码准确信息 和 源代码的错误位置 
          只能精确的行
        cheap-module-source-map：外部
          错误代码准确信息 和 源代码的错误位置 
          module会将loader的source map加入
    
        内联 和 外部的区别：1. 外部生成了文件，内联没有 2. 内联构建速度更快
    
        开发环境：速度快，调试更友好
          速度快(eval>inline>cheap>...)
            eval-cheap-souce-map（速度最快）
            eval-source-map
          调试更友好  
            souce-map（调试最好）
            cheap-module-souce-map
            cheap-souce-map
    
          --> eval-source-map  > ：eval-cheap-module-souce-map
    
        生产环境：源代码要不要隐藏? 调试要不要更友好
          内联会让代码体积变大，所以在生产环境不用内联
          nosources-source-map 全部隐藏
          hidden-source-map 只隐藏源代码，会提示构建后代码错误信息
    
          --> source-map（） / cheap-module-souce-map
          最终总结：
            开发环境使用：eval-source-map
            生产环境使用：source-map（）
    */
    ```

  * oneOf

    创建文件，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    process.env.NODE_ENV = 'production';
    
    // 复用loader
    const commonCssLoader = [
      MiniCssExtractPlugin.loader,
      'css-loader', {
        loader: 'postcss-loader',
        options: {
          ident: 'postcss',
          plugins: () => [require('postcss-preset-env')()]
        }
      }
    ];
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
          test: /\.js$/,
          exclude: /node_modules/,
          // 优先执行
          enforce: 'pre',
          loader: 'eslint-loader',
          options: {
            fix: true
          }
        }, {
          // 以下loader只会匹配一个（这样子便不会只需要一个loader的时候将所有的loader遍历一遍了，可以提高速度）
          // 注意：不能有两个配置处理同一种类型文件（比如我们的bable-loader和eslint-loader都要使用，那么我们将eslint-loader提取出来放到oneOf前面，这样前面的eslint-loader执行完毕之后再从oneOf里面找到bable-loader执行）
          oneOf: [{
              test: /\.css$/,
              use: [...commonCssLoader]
            }, {
              test: /\.less$/,
              use: [...commonCssLoader, 'less-loader']
            },
            /*
              正常来讲，一个文件只能被一个loader处理。
              当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序：
                先执行eslint 在执行babel
            */
            {
              test: /\.js$/,
              exclude: /node_modules/,
              loader: 'babel-loader',
              options: {
                presets: [
                  [
                    '@babel/preset-env', {
                      useBuiltIns: 'usage',
                      corejs: {
                        version: 3
                      },
                      targets: {
                        chrome: '60',
                        firefox: '50'
                      }
                    }
                  ]
                ]
              }
            }, {
              test: /\.(jpg|png|gif)/,
              loader: 'url-loader',
              options: {
                limit: 8 * 1024,
                name: '[hash:10].[ext]',
                outputPath: 'imgs',
                esModule: false
              }
            }, {
              test: /\.html$/,
              loader: 'html-loader'
            }, {
              exclude: /\.(js|css|less|html|jpg|png|gif)/,
              loader: 'file-loader',
              options: {
                outputPath: 'media'
              }
            }
          ]
        }]
      },
      plugins: [
        new MiniCssExtractPlugin({
          filename: 'css/built.css'
        }),
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
          template: './src/index.html',
          minify: {
            collapseWhitespace: true,
            removeComments: true
          }
        })
      ],
      mode: 'production'
    };
    ```

  * 缓存

    创建文件，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    /*
      缓存：
        1. babel缓存
          cacheDirectory: true
          --> 让第二次打包构建速度更快
        2. 文件资源缓存
          hash: 每次wepack构建时会生成一个唯一的hash值。
            问题: 因为js和css同时使用一个hash值。
              如果重新打包，会导致所有缓存失效。（可能我却只改动一个文件）
          chunkhash：根据chunk生成的hash值。如果打包来源于同一个chunk，那么hash值就一样
            问题: js和css的hash值还是一样的
              因为css是在js中被引入的，所以同属于一个chunk
          contenthash: 根据文件的内容生成hash值。不同文件hash值一定不一样    
          --> 让代码上线运行缓存更好使用（上线代码的性能优化的）
    
        综上所述：开启缓存需要经历两个步骤：
          1. 设置cacheDirectory: true
          2. 在输出的数组中加上contenthash
    */
    
    // 定义nodejs环境变量：决定使用browserslist的哪个环境
    process.env.NODE_ENV = 'production';
    
    // 复用loader
    const commonCssLoader = [
      MiniCssExtractPlugin.loader,
      'css-loader', {
        // 还需要在package.json中定义browserslist
        loader: 'postcss-loader',
        options: {
          ident: 'postcss',
          plugins: () => [require('postcss-preset-env')()]
        }
      }
    ];
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.[contenthash:10].js', //本节修改的地方
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
          // 在package.json中eslintConfig --> airbnb
          test: /\.js$/,
          exclude: /node_modules/,
          // 优先执行
          enforce: 'pre',
          loader: 'eslint-loader',
          options: {
            fix: true
          }
        }, {
          oneOf: [{
              test: /\.css$/,
              use: [...commonCssLoader]
            }, {
              test: /\.less$/,
              use: [...commonCssLoader, 'less-loader']
            },
    
            {
              test: /\.js$/,
              exclude: /node_modules/,
              loader: 'babel-loader',
              options: {
                presets: [
                  [
                    '@babel/preset-env', {
                      useBuiltIns: 'usage',
                      corejs: {
                        version: 3
                      },
                      targets: {
                        chrome: '60',
                        firefox: '50'
                      }
                    }
                  ]
                ],
                // 开启babel缓存
                // 第二次构建时，会读取之前的缓存
                cacheDirectory: true // 本节修改的位置
              }
            }, {
              test: /\.(jpg|png|gif)/,
              loader: 'url-loader',
              options: {
                limit: 8 * 1024,
                name: '[hash:10].[ext]',
                outputPath: 'imgs',
                esModule: false
              }
            }, {
              test: /\.html$/,
              loader: 'html-loader'
            }, {
              exclude: /\.(js|css|less|html|jpg|png|gif)/,
              loader: 'file-loader',
              options: {
                outputPath: 'media'
              }
            }
          ]
        }]
      },
      plugins: [
        new MiniCssExtractPlugin({
          filename: 'css/built.[contenthash:10].css' //本节修改的地方
        }),
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
          template: './src/index.html',
          minify: {
            collapseWhitespace: true,
            removeComments: true
          }
        })
      ],
      mode: 'production',
      devtool: 'source-map'
    };
    ```

  * tree shaking

    修改配置文件，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    /*
      tree shaking：去除无用代码，使得体积更小
        前提：1. 必须使用ES6模块化  2. 开启production环境
        作用: 减少代码体积
    
        在package.json中配置 
          "sideEffects": false 所有代码都没有副作用（都可以进行tree shaking）
            问题：可能会把css / @babel/polyfill （副作用）文件干掉，所以采用下面的一行形式
          "sideEffects": ["*.css", "*.less"]（加上这句话表示不移除css和less文件）
    */
    
    process.env.NODE_ENV = 'production';
    
    const commonCssLoader = [
      MiniCssExtractPlugin.loader,
      'css-loader', {
        loader: 'postcss-loader',
        options: {
          ident: 'postcss',
          plugins: () => [require('postcss-preset-env')()]
        }
      }
    ];
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.[contenthash:10].js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
          test: /\.js$/,
          exclude: /node_modules/,
          enforce: 'pre',
          loader: 'eslint-loader',
          options: {
            fix: true
          }
        }, {
          oneOf: [{
            test: /\.css$/,
            use: [...commonCssLoader]
          }, {
            test: /\.less$/,
            use: [...commonCssLoader, 'less-loader']
          }, {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              presets: [
                [
                  '@babel/preset-env', {
                    useBuiltIns: 'usage',
                    corejs: {
                      version: 3
                    },
                    targets: {
                      chrome: '60',
                      firefox: '50'
                    }
                  }
                ]
              ],
              cacheDirectory: true
            }
          }, {
            test: /\.(jpg|png|gif)/,
            loader: 'url-loader',
            options: {
              limit: 8 * 1024,
              name: '[hash:10].[ext]',
              outputPath: 'imgs',
              esModule: false
            }
          }, {
            test: /\.html$/,
            loader: 'html-loader'
          }, {
            exclude: /\.(js|css|less|html|jpg|png|gif)/,
            loader: 'file-loader',
            options: {
              outputPath: 'media'
            }
          }]
        }]
      },
      plugins: [
        new MiniCssExtractPlugin({
          filename: 'css/built.[contenthash:10].css'
        }),
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
          template: './src/index.html',
          minify: {
            collapseWhitespace: true,
            removeComments: true
          }
        })
      ],
      mode: 'production',
      devtool: 'source-map'
    };
    ```

  * code split

    * 多入口文件

      修改配置文件，运行指令：webpack

      ```js
      const {
        resolve
      } = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      
      module.exports = {
        // 单入口打包输出一个文件，多入口打包输出多个文件，有几个入口便打包输出多少个文件
        // 单入口（单页面应用程序使用单入口）（开发时单入口应用使用的多）
        // entry: './src/js/index.js',
        entry: {
          // 多入口：有一个入口，最终输出就有一个bundle（多页面应用程序使用多入口）
          index: './src/js/index.js',
          test: './src/js/test.js'
        },
        output: {
          // [name]：取文件名（比如上面的entry中名称为index,那么输出的文件名首部会有index名称）
          filename: 'js/[name].[contenthash:10].js',
          path: resolve(__dirname, 'build')
        },
        plugins: [
          new HtmlWebpackPlugin({
            template: './src/index.html',
            minify: {
              collapseWhitespace: true,
              removeComments: true
            }
          })
        ],
        mode: 'production'
      };
      ```

    * 单入口文件分割多个文件-1

      修改配置文件，运行指令：webpack

      ```js
      const {
        resolve
      } = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      
      module.exports = {
        // 单入口
        // entry: './src/js/index.js',
        entry: {
          index: './src/js/index.js',
          test: './src/js/test.js'
        },
        output: {
          // [name]：取文件名
          filename: 'js/[name].[contenthash:10].js',
          path: resolve(__dirname, 'build')
        },
        plugins: [
          new HtmlWebpackPlugin({
            template: './src/index.html',
            minify: {
              collapseWhitespace: true,
              removeComments: true
            }
          })
        ],
        /*
          1. 可以将node_modules中代码单独打包一个chunk最终输出（将别人的第三方的东西单独打包，将自己写的东西单独打包）
          2. 自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独一个chunk
          3. 这种单入口的形式不常使用
        */
        optimization: {
          splitChunks: {
            chunks: 'all'
          }
        },
        mode: 'production'
      };
      ```

    * 单入口文件输出多个出口文件-2（常用）

      修改配置文件

      ```js
      const {
        resolve
      } = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      
      module.exports = {
        // 单入口
        entry: './src/js/index.js',
        output: {
          // [name]：取文件名
          filename: 'js/[name].[contenthash:10].js',
          path: resolve(__dirname, 'build')
        },
        plugins: [
          new HtmlWebpackPlugin({
            template: './src/index.html',
            minify: {
              collapseWhitespace: true,
              removeComments: true
            }
          })
        ],
        /*
          1. 单入口的这种形式经常使用，实现功能：单入口打包输出多个出口文件，从而使得多个文件并行运行，增加运行速度
          2. 这种方式可以将node_modules中代码单独打包一个chunk最终输出，将入口文件打包输出一个出口文件，如果想要将某个单独的文件也打包输出为一个文件，则需要进行以下配置：
            1. optimization配置
            2.在打包的出口文件中对需要单独打包的文件输入相关代码
        */
        optimization: {
          splitChunks: {
            chunks: 'all'
          }
        },
        mode: 'production'
      };
      ```

      入口文件输入代码，运行指令：webpack

      ```js
      function sum(...args) {
        return args.reduce((p, c) => p + c, 0);
      }
      
      /*
        通过js代码，让某个文件被单独打包成一个chunk
        import动态导入语法：能将某个文件单独打包
        webpackChunkName: 'test'的作用是命名输出的打包名称，否则打包名称会根据每次打包输出的id进行命名，每次打包输出的id不一样，名称也不一样
      */
      import ( /* webpackChunkName: 'test' */ './test')
      .then(({
          mul,
          count
        }) => {
          // 文件加载成功~
          // eslint-disable-next-line
          console.log(mul(2, 5));
        })
        .catch(() => {
          // eslint-disable-next-line
          console.log('文件加载失败~');
        });
      
      // eslint-disable-next-line
      console.log(sum(1, 2, 3, 4));
      ```

  * 懒加载

    修改配置文件

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      // 单入口
      entry: './src/js/index.js',
      output: {
        // [name]：取文件名
        filename: 'js/[name].[contenthash:10].js',
        path: resolve(__dirname, 'build')
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html',
          minify: {
            collapseWhitespace: true,
            removeComments: true
          }
        })
      ],
      /*
        1. 单入口的这种形式经常使用，实现功能：单入口打包输出多个出口文件，从而使得多个文件并行运行，增加运行速度
        2. 这种方式可以将node_modules中代码单独打包一个chunk最终输出，将入口文件打包输出一个出口文件，如果想要将某个单独的文件也打包输出为一个文件，则需要进行以下配置：
          1. optimization配置
          2.在打包的出口文件中对需要单独打包的文件输入相关代码
      */
      optimization: {
        splitChunks: {
          chunks: 'all'
        }
      },
      mode: 'production'
    };
    ```

    入口文件中输入，运行指令：webpack

    ```js
    console.log('index.js文件被加载了~');
    
    // import { mul } from './test';  属于正常加载
    
    document.getElementById('btn').onclick = function() {
    	// 懒加载~：当文件需要使用时才加载~
    	// 预加载 webpackPrefetch: true：会在使用之前，提前加载js文件 
    	// 正常加载可以认为是并行加载（同一时间加载多个文件）  
    	// 预加载 prefetch：等其他资源加载完毕，浏览器空闲了，再偷偷加载资源（兼容性比较差的）（检测是否是预加载了可以通过打开控制台，查看网络看出是否是提前加载了）
    	/*
    	1. 通常情况下懒加载的第一次加载如果加载的文件比较大的话会加载的时间比较长，给用户造成阻塞的现象
    	2. 一般不使用预加载，因为兼容性差了一些，一般使用懒加载
    	3. 懒加载的实现是基于前面的代码分割的基础上的，要进行了前面的代码分割的配置才可以使用懒加载，这个案例实现的功能是点击按钮之后再加载test文件中的js代码
    	4. 懒加载第一次可能会慢一点，第二次加载便不会慢了，第一次加载会存入缓存中，第二次加载会直接从缓存中加载
      */
    	import ( /* webpackChunkName: 'test', webpackPrefetch: true */ './test').then(({
    		mul
    	}) => {
    		console.log(mul(4, 5));
    	});
    };
    ```

  * pwa

    下载安装包npm install --save-dev workbox-webpack-plugin，修改配置文件

    ```js
    const {
      resolve
    } = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    
    const WorkboxWebpackPlugin = require('workbox-webpack-plugin');
    
    /*
      PWA: 渐进式网络开发应用程序(离线可访问)
        workbox --> workbox-webpack-plugin
    */
    
    process.env.NODE_ENV = 'production';
    
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.[contenthash:10].js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
    
        ]
      },
      plugins: [
    
        new WorkboxWebpackPlugin.GenerateSW({
          /*
            1. 帮助serviceworker快速启动
            2. 删除旧的 serviceworker
    
            生成一个 serviceworker 配置文件~
          */
          clientsClaim: true,
          skipWaiting: true
        })
      ],
      mode: 'production',
      devtool: 'source-map'
    };
    ```

    入口文件中输入，运行指令：webpack

    ```js
    import { mul } from './test';
    import '../css/index.css';
    
    function sum(...args) {
      return args.reduce((p, c) => p + c, 0);
    }
    
    // eslint-disable-next-line
    console.log(mul(2, 3));
    // eslint-disable-next-line
    console.log(sum(1, 2, 3, 4));
    
    /*
      1. eslint不认识 window、navigator全局变量
        解决：需要修改package.json中eslintConfig配置
          "env": {
            "browser": true // 支持浏览器端全局变量，如果要支持node的全局变量，则写"node":true
          }
       2. sw代码必须运行在服务器上
          --> nodejs
          -->
            npm i serve -g
            serve -s build 启动服务器，将build目录下所有资源作为静态资源暴露出去
    */
    // 注册serviceWorker
    // 处理兼容性问题
    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker
          .register('/service-worker.js')
          .then(() => {
            console.log('sw注册成功了~');
          })
          .catch(() => {
            console.log('sw注册失败了~');
          });
      });
    }
    ```

  * 多进程打包

    下载安装包npm install --save-dev thread-loader，修改配置文件，运行指令：webpack

    ```js
    const { resolve } = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    
    const WorkboxWebpackPlugin = require('workbox-webpack-plugin');//
    
    
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.[contenthash:10].js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
        
          {
            oneOf: [
              {
                test: /\.css$/,
                use: [...commonCssLoader]
              },
              {
                test: /\.less$/,
                use: [...commonCssLoader, 'less-loader']
              },
              {
                test: /\.js$/,
                exclude: /node_modules/,
                use: [
                  /* 
                    开启多进程打包。 
                    进程启动大概为600ms，进程通信也有开销。
                    只有工作消耗时间比较长，才需要多进程打包
                  */
                  {
                    loader: 'thread-loader',
                    options: {
                      workers: 2 // 进程2个
                    }
                  },
                  {
                    loader: 'babel-loader',
                    options: {
                      presets: [
                        [
                          '@babel/preset-env',
                          {
                            useBuiltIns: 'usage',
                            corejs: { version: 3 },
                            targets: {
                              chrome: '60',
                              firefox: '50'
                            }
                          }
                        ]
                      ],
                      cacheDirectory: true
                    }
                  }
                ]
              },
              {
                test: /\.(jpg|png|gif)/,
                loader: 'url-loader',
                options: {
                  limit: 8 * 1024,
                  name: '[hash:10].[ext]',
                  outputPath: 'imgs',
                  esModule: false
                }
              },
              {
                test: /\.html$/,
                loader: 'html-loader'
              },
              {
                exclude: /\.(js|css|less|html|jpg|png|gif)/,
                loader: 'file-loader',
                options: {
                  outputPath: 'media'
                }
              }
            ]
          }
        ]
      },
      plugins: [
        new MiniCssExtractPlugin({
          filename: 'css/built.[contenthash:10].css'
        }),
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
          template: './src/index.html',
          minify: {
            collapseWhitespace: true,
            removeComments: true
          }
        }),
        new WorkboxWebpackPlugin.GenerateSW({
          clientsClaim: true,
          skipWaiting: true
        })
      ],
      mode: 'production',
      devtool: 'source-map'
    };
    ```

  * externals

    修改配置文件，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        })
      ],
      mode: 'production',
      externals: {
        // 拒绝jQuery被打包进来，作用： 假如说我们的jequery使用的是CDN链接，那么打包的时候jquery不会被打包进来，直接使用CDN链接
        jquery: 'jQuery'
      }
    };
    ```

  * dll

    创建webpack.dll.js文件并输入

    ```js
    /*
      使用dll技术，对某些库（第三方库：jquery、react、vue...）进行单独打包
        当你运行 webpack 时，默认查找 webpack.config.js 配置文件
        需求：需要运行 webpack.dll.js 文件
          --> webpack --config webpack.dll.js
    */
    
    const {
      resolve
    } = require('path');
    const webpack = require('webpack');
    
    module.exports = {
      entry: {
        // 最终打包生成的[name] --> jquery
        // ['jquery'] --> 要打包的库是jquery
        jquery: ['jquery'],
      },
      output: {
        filename: '[name].js',
        path: resolve(__dirname, 'dll'),
        library: '[name]_[hash]' // 打包的库里面向外暴露出去的内容叫什么名字
      },
    
      plugins: [
        // 打包生成一个 manifest.json --> 提供和jquery映射
        new webpack.DllPlugin({
          name: '[name]_[hash]', // 映射库的暴露的内容名称
          path: resolve(__dirname, 'dll/manifest.json') // 输出文件路径
        })
      ],
      mode: 'production'
    };
    ```

    运行指令：webpack --config webpack.dll.js，修改配置文件webpack.config.js，运行指令：webpack

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const webpack = require('webpack');
    const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');
    
    module.exports = {
      entry: './src/index.js',
      output: {
        filename: 'built.js',
        path: resolve(__dirname, 'build')
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: './src/index.html'
        }),
        // 告诉webpack哪些库不参与打包，同时使用时的名称也得变~
        new webpack.DllReferencePlugin({
          manifest: resolve(__dirname, 'dll/manifest.json')
        }),
        // 将某个文件打包输出去，并在html中自动引入该资源
        new AddAssetHtmlWebpackPlugin({
          filepath: resolve(__dirname, 'dll/jquery.js')
        })
      ],
      mode: 'production'
    };
    
    /*
      首先要在webpack.dll.js与webpack.config.js中引入webpack插件
      1. 在webpack.dll.js文件中的写入我们需要打包的库以及打包的库输出的名字为什么（实现功能：第一次打包之后只要jquery库名称不变，下一次不需要在重新打包了，直接使用，提高构建速度）(不仅仅是jquery库，各种库都要引入)
      2. plugin中生成的manifest.json文件表示了jquery的映射关系
      3. webpack.config.js中使用DllReferencePlugin告诉webpack哪些文件不需要再重新打包
      4. webpack.config.js中使用AddAssetHtmlWebpackPlugin将ebpack.dll.js中打包的资源在html中自动引入
    */
    ```

* webpack配置详情

  * entry

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    /*
      entry: 入口起点
        1. string --> './src/index.js'（用的多）
          单入口
          打包形成一个chunk。 输出一个bundle文件。
          此时chunk的名称默认是 main
        2. array  --> ['./src/index.js', './src/add.js']（一般不用这种形式）
          多入口
          所有入口文件最终只会形成一个chunk, 输出出去只有一个bundle文件。
            --> 只有在HMR功能中让html热更新生效~
        3. object（用的多）
          多入口
          有几个入口文件就形成几个chunk，输出几个bundle文件
          此时chunk的名称是 key
    
          --> 特殊用法
            {
              // 所有入口文件最终只会形成一个chunk, 输出出去只有一个bundle文件。
              index: ['./src/index.js', './src/count.js'], 
              // 形成一个chunk，输出一个bundle文件。
              add: './src/add.js'
            }
    */
    
    module.exports = {
      entry: {
        index: ['./src/index.js', './src/count.js'],
        add: './src/add.js'
      },
      output: {
        filename: '[name].js',
        path: resolve(__dirname, 'build')
      },
      plugins: [new HtmlWebpackPlugin()],
      mode: 'development'
    };
    ```

  * output

    ```js
    const { resolve } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/index.js',
      output: {
        // 文件名称（指定名称+目录）
        filename: 'js/[name].js',
        // 输出文件目录（将来所有资源输出的公共目录）
        path: resolve(__dirname, 'build'),
        // 所有资源引入公共路径前缀 --> 'imgs/a.jpg' --> '/imgs/a.jpg'
        publicPath: '/',
        chunkFilename: 'js/[name]_chunk.js', // 非入口chunk的名称
        // library: '[name]', // 整个库向外暴露的变量名
        // libraryTarget: 'window' // 变量名添加到哪个上 browser
        // libraryTarget: 'global' // 变量名添加到哪个上 node
        // libraryTarget: 'commonjs'
      },
      plugins: [new HtmlWebpackPlugin()],
      mode: 'development'
    };
    ```

  * module

    ```js
    const { resolve } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/index.js',
      output: {
        filename: 'js/[name].js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          // loader的配置
          {
            test: /\.css$/,
            // 多个loader用use
            use: ['style-loader', 'css-loader']
          },
          {
            test: /\.js$/,
            // 排除node_modules下的js文件
            exclude: /node_modules/,
            // 只检查 src 下的js文件
            include: resolve(__dirname, 'src'),
            // 优先执行
            enforce: 'pre',
            // 延后执行
            // enforce: 'post',
            // 单个loader用loader
            loader: 'eslint-loader',
            options: {}
          },
          {
            // 以下配置只会生效一个
            oneOf: []
          }
        ]
      },
      plugins: [new HtmlWebpackPlugin()],
      mode: 'development'
    };
    ```

  * resolve

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/[name].js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [{
          test: /\.css$/,
          use: ['style-loader', 'css-loader']
        }]
      },
      plugins: [new HtmlWebpackPlugin()],
      mode: 'development',
      // 解析模块的规则
      resolve: {
        // 配置解析模块路径别名: 优点简写路径 缺点路径没有提示
        alias: {
          $css: resolve(__dirname, 'src/css')
        },
        // 配置省略文件路径的后缀名
        extensions: ['.js', '.json', '.jsx', '.css'],
        // 告诉 webpack 解析模块是去找哪个目录（不写这个的话，他会一层一层的往上面找，直到找到位置）
        modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
      }
    };
    ```

  * devserver

    ```js
    const { resolve } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/[name].js',
        path: resolve(__dirname, 'build')
      },
      module: {
        rules: [
          {
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
          }
        ]
      },
      plugins: [new HtmlWebpackPlugin()],
      mode: 'development',
      resolve: {
        alias: {
          $css: resolve(__dirname, 'src/css')
        },
        extensions: ['.js', '.json', '.jsx', '.css'],
        modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
      },
      devServer: {
        // 运行代码的目录
        contentBase: resolve(__dirname, 'build'),
        // 监视 contentBase 目录下的所有文件，一旦文件变化就会 reload
        watchContentBase: true,
        watchOptions: {
          // 监视的时候忽略文件
          ignored: /node_modules/
        },
        // 启动gzip压缩，体积小
        compress: true,
        // 端口号
        port: 5000,
        // 域名
        host: 'localhost',
        // 自动打开浏览器
        open: true,
        // 开启HMR功能
        hot: true,
        // 不要显示启动服务器日志信息
        clientLogLevel: 'none',
        // 除了一些基本启动信息以外，其他内容都不要显示
        quiet: true,
        // 如果出错了，不要全屏提示~，只需要在日志中打印即可
        overlay: false,
        // 服务器代理 --> 解决开发环境跨域问题
        proxy: {
          // 一旦devServer(5000)服务器接受到 /api/xxx 的请求，就会把请求转发到另外一个服务器(3000)
          '/api': {
            target: 'http://localhost:3000',
            // 发送请求时，请求路径重写：将 /api/xxx --> /xxx （去掉/api）
            pathRewrite: {
              '^/api': ''
            }
          }
        }
      }
    };
    ```

  * optimization

    ```js
    const {
      resolve
    } = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const TerserWebpackPlugin = require('terser-webpack-plugin')
    
    module.exports = {
      entry: './src/js/index.js',
      output: {
        filename: 'js/[name].[contenthash:10].js',
        path: resolve(__dirname, 'build'),
        chunkFilename: 'js/[name].[contenthash:10]_chunk.js'
      },
      module: {
        rules: [{
          test: /\.css$/,
          use: ['style-loader', 'css-loader']
        }]
      },
      plugins: [new HtmlWebpackPlugin()],
      mode: 'production',
      resolve: {
        alias: {
          $css: resolve(__dirname, 'src/css')
        },
        extensions: ['.js', '.json', '.jsx', '.css'],
        modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
      },
      optimization: {
        splitChunks: {
          chunks: 'all'
            // 默认值，可以不写，基本上不修改~
            /* minSize: 30 * 1024, // 分割的chunk最小为30kb，小于30kb的不分割，大于30kb才分割
            maxSiza: 0, // 最大没有限制
            minChunks: 1, // 要提取的chunk最少被引用1次
            maxAsyncRequests: 5, // 按需加载时并行加载的文件的最大数量
            maxInitialRequests: 3, // 入口js文件最大并行请求数量
            automaticNameDelimiter: '~', // 名称连接符
            name: true, // 可以使用命名规则
            cacheGroups: {
              // 分割chunk的组
              // node_modules文件会被打包到 vendors 组的chunk中。--> vendors~xxx.js
              // 满足上面写的公共规则，如：大小超过30kb，至少被引用一次。
              vendors: {
                test: /[\\/]node_modules[\\/]/,
                // 优先级
                priority: -10
              },
              default: {
                // 要提取的chunk最少被引用2次
                minChunks: 2,
                // 优先级
                priority: -20,
                // 如果当前要打包的模块，和之前已经被提取的模块是同一个，就会复用，而不是重新打包模块
                reuseExistingChunk: true
              } 
            }*/
        },
        // 将当前模块的记录其他模块的hash单独打包为一个文件 runtime
        // 解决：修改a文件导致b文件的contenthash变化
        runtimeChunk: {
          name: entrypoint => `runtime-${entrypoint.name}`
        },
        minimizer: [
          // 配置生产环境的压缩方案：js和css
          new TerserWebpackPlugin({
            // 开启缓存
            cache: true,
            // 开启多进程打包
            parallel: true,
            // 启动source-map
            sourceMap: true
          })
        ]
      }
    };
    ```

* webpack5

  此版本重点关注以下内容:

  * 通过持久缓存提高构建性能.
  * 使用更好的算法和默认值来改善长期缓存.
  * 通过更好的树摇和代码生成来改善捆绑包大小.
  * 清除处于怪异状态的内部结构，同时在 v4 中实现功能而不引入任何重大更改.
  * 通过引入重大更改来为将来的功能做准备，以使我们能够尽可能长时间地使用 v5.

  以下是使用细节：

  * 自动删除 Node.js Polyfills

    早期，webpack 的目标是允许在浏览器中运行大多数 node.js 模块，但是模块格局发生了变化，许多模块用途现在主要是为前端目的而编写的。webpack <= 4 附带了许多 node.js 核心模块的 polyfill，一旦模块使用任何核心模块（即 crypto 模块），这些模块就会自动应用。尽管这使使用为 node.js 编写的模块变得容易，但它会将这些巨大的 polyfill 添加到包中。在许多情况下，这些 polyfill 是不必要的。

    webpack 5 会自动停止填充这些核心模块，并专注于与前端兼容的模块。迁移：尽可能尝试使用与前端兼容的模块；可以为 node.js 核心模块手动添加一个 polyfill。错误消息将提示如何实现该目标。

  * Chunk 和模块 ID

    添加了用于长期缓存的新算法。在生产模式下默认情况下启用这些功能。

    chunkIds: "deterministic", moduleIds: "deterministic"

  * Chunk ID

    你可以不用使用 import(/* webpackChunkName: "name" */ "module") 在开发环境来为 chunk 命名，生产环境还是有必要的

    webpack 内部有 chunk 命名规则，不再是以 id(0, 1, 2)命名了

  * Tree Shaking

    webpack 现在能够处理对嵌套模块的 tree shaking，在生产环境中, inner 模块暴露的 b 会被删除

    ```js
    // inner.js
    export const a = 1;
    export const b = 2;
    
    // module.js
    import * as inner from './inner';
    export { inner };
    
    // user.js
    import * as module from './module';
    console.log(module.inner.a);
    
    ```

    webpack 现在能够多个模块之前的关系，当设置了"sideEffects": false时，一旦发现test方法没有使用，不但删除test，还会删除"./something"

    ```js
    import { something } from './something';
    
    function usingSomething() {
      return something;
    }
    
    export function test() {
      return usingSomething();
    }
    ```

    webpack 现在能处理对 Commonjs 的 tree shaking

  * Output

    webpack 4 默认只能输出 ES5 代码

    webpack 5 开始新增一个属性 output.ecmaVersion, 可以生成 ES5 和 ES6 / ES2015 代码，如：output.ecmaVersion: 2015

  * SplitChunk

    ```js
    // webpack4
    minSize: 30000;
    // webpack5
    minSize: {
      javascript: 30000,
      style: 50000,
    }
    ```

  * Caching

    缓存将存储到 node_modules/.cache/webpack

    ```js
    // 配置缓存
    cache: {
      // 磁盘存储
      type: "filesystem",
      buildDependencies: {
        // 当配置修改时，缓存失效
        config: [__filename]
      }
    }
    ```

  * 监视输出文件

    之前 webpack 总是在第一次构建时输出全部文件，但是监视重新构建时会只更新修改的文件。此次更新在第一次构建时会找到输出文件看是否有变化，从而决定要不要输出全部文件。

  * 默认值

    * entry: "./src/index.js
    * output.path: path.resolve(__dirname, "dist")
    * output.filename: "[name].js"

* create-react-app react-project

  本部分只讲述通过脚手架创建的项目的分析路线及步骤，具体每个文件夹里面讲述了什么内容分别在源码中进行注释讲解。

  通过 npm run eject将配置文件暴露出来

  * config-->paths.js（向外暴露出路径）
  * scripts-->start.js（开发环境对应的文件）
  * webpack.config.js（主要内容为对loader和plugin的配置，将来自己修改的时候可以直接在这个文件夹里面进行loader和plugin的修改）（核心）
  * scripts-->build.js（生产环境对应的文件，与开发环境对应的文件差不多）

* vue create vue-project

  这里只讲述通过脚手架创建的项目的分析路线及步骤，具体每个文件夹里面讲述了什么内容分别在源码中进行注释。

  通过 vue inspect --mode=development > webpack.dev.js将vue开发环境配置打包一起放在webpack.dev.js文件下面，开发环境代码只需要研究webpack.dev.js文件即可

  通过 vue inspect --mode=production > webpack.prod.js将vue生产环境配置打包一起放在webpack.prod.js文件下面，生产环境代码只需要研究webpack.prod.js文件即可

  开发环境文件webpack.dev.js，生产环境文件webpack.prod.js（除了在css上面以及多线程打包上面进行了一些修改，其余和开发环境是一样的）

