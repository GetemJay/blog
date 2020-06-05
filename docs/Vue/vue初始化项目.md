# 使用脚手架初始化项目

## 使用vue-cli3的初始化方法

1. `vue create vue-name`第一项选择`Manually select features`之后就是按照自己的需求是填写

如果全局没有vue-vli3的话，请先`npm install -g @vue/cli`

2. 添加`vue.config.js`,用户配置的覆盖，和vue-cli2中的道理是一样的,附上一个我常做的一种覆盖

```js
const path = require('path')
const config = require('./package.json')
const autoprefixer = require('autoprefixer')
const pxtoviewport = require('postcss-px-to-viewport')

module.exports = {
  publicPath: './',
  indexPath: path.resolve(__dirname, './dist/' + config.version + '/index.html'),
  outputDir: path.resolve(__dirname, './dist/' + config.version),
  assetsDir: 'static',
  chainWebpack: config => {
    config.module
      .rule('images')
      .use('url-loader')
      .loader('url-loader')
      .tap(options => Object.assign(options, {
        limit: 10000
      }))
    config.plugin('version')
      .use(require.resolve('copy-webpack-plugin'), [
        [{
          from: './version',
          to: '../'
        }]
      ])
    // 移除 prefetch 插件
    config.plugins.delete('prefetch')
    // 移除 preload 插件
    config.plugins.delete('preload')
  },
  css: {
    loaderOptions: {
      postcss: {
        plugins: [
          autoprefixer(),
          pxtoviewport({
            viewportWidth: 375,
            selectorBlackList: ['van-circle__layer']
          })
        ]
      }
    }
  },
  devServer: {
    open: true,
    port: 3000,
    https: false,
    hotOnly: false,
    proxy: require('./proxy')
  },
  // devtool: 'eval-source-map',
  transpileDependencies: [ 'vue-echarts', 'resize-detector' ]
}

```

<back-to-top />

