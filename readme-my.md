## 个人理解关于mock数据

- 获取 `mock.json` 的流程
就此项目本身而言，`mock.json` 本身是写好的, 项目启动，读取 src 目录下所有已 `mock.json`结尾的文件，
map的形式放在内存中。
初始化 io 的时候，闭包的形式 保存携带 `global` 或 `login`参数 和 plainObj 中key 做为以后接口请求参数。

- 代理请求的原理
// https://github.com/chimurai/http-proxy-middleware
按照他这个意思 `webpack配置`中 
```
{
  devServer: {
    proxy: {
      '/api/*': {
        target: 'http://192.168.90.160:8888',
        changeOrigin: true,
        secure: true,
        onProxyReq: [AsyncFunction: onProxyReq],
        onProxyRes: [AsyncFunction: onProxyRes],
        onError: [Function: onError]
      }
    }
  }
}
```
使用的是这个包 `http-proxy-middleware`。
并且回调函数 `onProxyReq` 在请求的时候将读取的 `mock 文件`，返回。

- 读取 `mock文件`的方式
```
const mockFiles = syncWalkDir(path.join(__dirname, '../src'), (file) => /-mock.json$/.test(file))
mockFiles.forEach((filePath) => {
    const p = path.parse(filePath)
    const mockKey = p.name.substr(0, p.name.length - 5)
    if (mockJson[mockKey]) {
      console.error(`有相同的mock文件名称${p.name} 存在`, filePath)
    }
    delete require.cache[require.resolve(filePath)]
    mockJson[mockKey] = require(filePath)
})
```

- require语法
```
require('./index.js') === require('.')
```

## TODO
- 编写接口，返回服务端数据，添加 真实的登陆判断
- 写完上传到github，新建个仓库，专门放可运行项目
- 博客 react-starter 和 render-server 怎么实现，关键代码。描述码云。 

## 参考链接：
https://mp.weixin.qq.com/s?__biz=Mzg5NDEyMzA2NQ==&mid=2247487284&idx=2&sn=79e363101e11fe8be6e21ee28951c4ed&chksm=c0252262f752ab740539dccff842ad37237d952bc02008f720cce60383e8e6b2aa60fce56d03&mpshare=1&scene=1&srcid=0605Vxh4FGkIVBb3G7nMlunz&sharer_sharetime=1623205658462&sharer_shareid=addda33c4eab5e9d285d29057d1ba689#rd
