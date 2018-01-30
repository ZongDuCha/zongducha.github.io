---
title: webpack 打包后font-awesome路径错误
date: 2018-01-23 15:01:16
tags: 主页
---
类似与这种：
![1](1-23-1.png)

解决方法是：
在 `build/webpack.base.conf.js`文件内找到：

<!-- more -->

```javascript
{
    test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
    loader: 'url-loader',
    options: {
        limit: 10000,
        name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
    }
}
```
把limit尽量调大一点：例如 limit:80000

然后在次打包访问，应该就没问题了
