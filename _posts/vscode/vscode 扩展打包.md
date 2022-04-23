## 简介

VSCode 扩展打包官方推荐使用 webpack，使用 webpack打包后可以把多个源文件变成一个源文件，同时进行压缩。 对于较大的扩展来说，打包之后可以减少安装和扩展启动的时间。

一般的扩展只是用webpack即可满足要求，对很复杂的扩展一般采用 webpack 结合 gulp的方式处理。实际进行打包，压缩操作的仍然是 webpack，不过由构建工具 gulp 来控制 webpack 打包的过程。

在扩展中添加 webpack 打包的过程很简单，只需要两步即可。

1. 在 `package.json` 文件中添加 webpack 开发依赖.
2. 在扩展根目录下面添加一个 webpack 配置文件

## 添加开发依赖

webpack 是一个打包工具，只需要在开发的时候使用即可，因此需要添加到开发依赖 `devDependencies`，一般需要添加下面几个 npm 包：

```json
		"ts-loader": "^7.0.5",
		"webpack": "^4.43.0",
		"webpack-cli": "^3.3.11"
```

具体根据你的项目中使用的npm包的情况，可能还需要添加其他的 loader 作为依赖，例如： `less-loader`, `css-loader` 等等


## webpack 配置文件

每一个 webpack 项目都需要一个 `webpack.config.js` 文件来控制打包。 直接在扩展的 `package.json` 同级目录下面创建该文件。下面给出一个样例文件，大家可以将该文件复制到你的项目中，然后修改即可：

```js
'use strict';

const path = require('path');

/**@type {import('webpack').Configuration}*/
const config = {
    target: 'node', // vscode extensions run in a Node.js-context 📖 -> https://webpack.js.org/configuration/node/

    entry: './src/extension.ts', // the entry point of this extension, 📖 -> https://webpack.js.org/configuration/entry-context/
    output: { // the bundle is stored in the 'dist' folder (check package.json), 📖 -> https://webpack.js.org/configuration/output/
        path: path.resolve(__dirname, 'dist'),
        filename: 'extension.js',
        libraryTarget: "commonjs2",
        devtoolModuleFilenameTemplate: "../[resource-path]",
    },
    devtool: 'none',
    externals: {
        vscode: "commonjs vscode" // the vscode-module is created on-the-fly and must be excluded. Add other modules that cannot be webpack'ed, 📖 -> https://webpack.js.org/configuration/externals/
    },
    resolve: { // support reading TypeScript and JavaScript files, 📖 -> https://github.com/TypeStrong/ts-loader
        extensions: ['.ts', '.js']
    },
    module: {
        rules: [{
            test: /\.ts$/,
            exclude: /node_modules/,
            use: [{
                loader: 'ts-loader',
                options: {
                    compilerOptions: {
                        "module": "es6" // override `tsconfig.json` so that TypeScript emits native JavaScript modules.
                    }
                }
            }]
        }]
    },
}

module.exports = config;
```

该文件有以下几个**注意点**：

1. `entry`: entry 是你的扩展源代码的入口点，请注意与 `package.json` 文件中 `main` 的区别
2. `output`: 在output中通过对 path 和 file 的设置，来指定源代码经过打包之后的输出文件。 此处和 package.json 的 main 一直
3. `devtool`: 指定为 `none` 的话不生成 source-map 文件，指定为 `source-map` 同时会生产 source-map 文件，该文件对调试有用