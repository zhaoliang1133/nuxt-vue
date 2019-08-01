# test-one

> My fabulous Nuxt.js project

## Build Setup

``` bash
# install dependencies
$ npm run install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project
$ npm run generate
```

For detailed explanation on how things work, check out [Nuxt.js docs](https://nuxtjs.org).

```
nuxt.js部署vue应用到服务端过程
 vue.js  nuxt.js  17k 次阅读  ·  读完需要 5 分钟
由于seo的需要，最近将项目移植道nuxt.js下采用ssr渲染

移植完成后，一路顺畅，但是到了要部署到服务器端上时候，还是个头疼的问题，但最终还是顺利完成。
现在记录一下部署中的过程。

注：部署时候过程中，参考了下：https://segmentfault.com/a/11...

一：搭建nginx+node+npm+pm2环境

我们的nginx版本是 1.12
node版本是v8.11.1
npm版本是5.6.0
pm2版本是2.10.2

具体环境安装，网上都有教程，就不在叙述，直接进入正文。

一：配置nginx代理监听3002端口，package打包时端口3002

在nuxt.js的项目下package.json里设置
图片描述

在nginx的 vhost里新建一个主机绑定

upstream nodenuxt {
    server 127.0.0.1:3002; #nuxt项目 监听端口
    keepalive 64;
}

server {
    listen 80;
    server_name mysite.com;
    location / {
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;  
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Nginx-Proxy true;
        proxy_cache_bypass $http_upgrade;
        proxy_pass http://nodenuxt; #反向代理
    }
}
二：项目在本地完成后，npm run build 打包应用
打包完成后，我们将

.nuxt
static
nuxt.config.js
package.json
传到服务器空间里, 需要上传文件如下
图片描述

三：在服务器上部署运行

运行npm install 安装package里的依赖
运行npm start 就可以运行起来nuxt的服务渲染了
此时我们就可以在浏览器上输入 mysite.com 访问了。服务端渲染瞬间出来了，但这并不理想，进程稳定性能否常驻后台？

四：pm2开启进程守护

进入对应的应用目录，执行以下命令

pm2 start npm --name "my-nuxt" -- run start
其中 my-nuxt的名称是 我们在package中的项目名称。
执行完pm2的启动命令后，我们用 pm2 list 查看一下进程列表，我截一下我个人服务器的pm2列表：
图片描述

大功完成，今晚吃鸡去

```
