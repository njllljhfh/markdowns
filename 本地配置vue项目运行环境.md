# Windows10配置vue项目运行环境

[下载node](http://nodejs.cn/download/)

安装node

修改 npm 源

```shell
npm config set registry http://registry.npm.taobao.org
```

安装yarn

```shell
npm install -g yarn
```

修改 yarn 源

```shell
yarn config set registry https://registry.npm.taobao.org/
```

在 vue 项目目录下执行

```shell
yarn install
```

在 vue 项目目录下执行，启动项目的 dev 环境

```shell
yarn dev
```

