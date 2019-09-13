# nvm控制node版本：

由于node的版本过一段事件就会更新，所以直接在官网下载安装包不是一个好办法，对于之后所要维护的项目也会造成影响。网上推荐一款nvm的工具来管理node，在同一个电脑上可以有多个不同的版本nvm，非常好用，下面是一部分的指令。

```
nvm --help：能够看到一些常用的版本。
nvm install  [version]，安装制定版本的node版本，并自动切换到该版本。如nvm install v11.0.0。
nvm version，查看当前的版本
nvm use [version]，切换制定的node版本。
nvm ls：查看本地安装了哪些版本。
nvm ls available：远程所有的版本。
nvm on，打开nodejs版本控制
nvm off，关闭nodejs版本控制
```

相关链接：[https://segmentfault.com/a/1190000007612011](https://segmentfault.com/a/1190000007612011)

### 下载安装

windows上较为复杂一些，上网查询即可。

### 配置 npm 全局安装路径

执行下面的命令：

npm config set prefix "E:\nodejs\npm-global"
C:\\Users\\你的用户名\\ 会生成个 .npmrc 文件，内容如下：

prefix=E:\nodejs\npm-global
去装些全局的东西 npm install vue -g.
全局的所有包都在这：E:\nodejs\npm-global\node_modules

设置——系统——关于——系统信息——高级程序设置——环境变量——xxx用户的变量
修改环境变量，将 C:\\Users\\你的用户名\\AppData\\Roaming\\npm 修改为 PATH E:\\nodejs\\npm-global.



### 解决 nvm 下载慢的问题

在程序安装目录下找到 settings.txt，添加下面两行。
root: C:\Program Files\nvm
path: C:\Program Files\nodejs

- node_mirror: https://npm.taobao.org/mirrors/node/
- npm_mirror: https://npm.taobao.org/mirrors/npm/

当然，如果之前存在node版本，需要重新卸载，并清除相关目录和文件，避免nvm安装失败。