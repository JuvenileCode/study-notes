## Node Npm Use

> 此文仅记录前端某些工具或者一些前端框架的基本使用配置

### NodeJs 之 NPM

**`NPM`（node package manager），通常称为node包管理器**

> npm的背后，是基于couchdb的一个数据库，详细记录了每个包的信息，包括作者、版本、依赖、授权信息等。它的一个很重要的作用就是：将开发者从繁琐的包管理工作（版本、依赖等）中解放出来，更加专注于功能的开发。
>
> `因网络问题得换源`

**解决开发中问题点**

- 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
- 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

#### NPM 依赖包安装

npm包安装分`本地安装` 和` 全局安装`

- 本地安装：package会被下载到当前所在目录，也只能在当前目录下使用。
- 全局安装：package会被下载到到特定的系统目录下，安装的package能够在所有目录下使用、`-g`表示。

#### NPM换源

1. npm镜像

   - 淘宝npm镜像
     - 搜索地址：[http://npm.taobao.org/](https://link.jianshu.com/?t=http://npm.taobao.org/)
     - registry地址：[http://registry.npm.taobao.org/](https://link.jianshu.com/?t=http://registry.npm.taobao.org/)
   - cnpmjs镜像
     - 搜索地址：[http://cnpmjs.org/](https://link.jianshu.com/?t=http://cnpmjs.org/)
     - registry地址：[http://r.cnpmjs.org/](https://link.jianshu.com/?t=http://r.cnpmjs.org/)

2. 使用方式

   - 临时使用

     `npm --registry https://registry.npm.taobao.org install express`

   - 持久使用

     ```bash
     npm config set registry https://registry.npm.taobao.org
     // 配置后可通过下面方式来验证是否成功
     npm config get registry
     // 或npm info express
     ```

   - 通过cnpm使用
     ```bash
     npm install -g cnpm --registry=https://registry.npm.taobao.org
     // 使用cnpm install expresstall express
     ```

#### [NPM 常用命令](http://www.cnblogs.com/PeunZhang/p/5553574.html#npm-install)

#####  1.  `init` 初始化

   ```bash
    //一个基于node的项目，会创建一个配置文件package.json
    //1.一般情况下 一路enter
    $ npm init
    //2.全部使用默认配置
    $npm init --yes
   ```

#####  2. `npm install  <module>` 安装模块

   ```bash
   //全局安装
   $ npm install 模块名 -g
   //本地安装
   $ npm install 模块名
   //一次性安装多个
   $ npm install 模块1 模块2 模块n --save
   //安装运行时依赖包
   $ npm install 模块名 --save
   //安装开发时依赖包
   $ npm install 模块名 --save-dev
   ```

#####  3. `npm uninstall  <module>` 卸载模块

   ```bash
   /卸载本地模块
   $ npm uninstall 模块名
   //卸载全局模块
   $ npm uninstall -g 模块名
   ```

#####  4. `npm update <moudle>` 更新模块

   ```bash
   $ npm update 模块名
   $ npm update 模块名 -g
   ```

#####  5. `npm root` 查看安装目录

   ```bash
   //查看本地安装的目录
   $ npm root
   //查看全局安装的目录
   $ npm root -g
   ```

##### 6. `npm ls`查看当前安装模块

```bash
	$ npm ls
	$ npm ls -g
```

##### 7. `npm info`查看模块信息

```bash
	$ npm info 模块名
```

##### 8.`npm search`搜索

```bash
	npm search grunt-cli
```

#### [npm 脚本](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)

> `npm run XXX`是执行配置在package.json中的脚本

#####  1. 什么是npm脚本？ 

npm 允许在`package.json`文件里面，使用`scripts`字段定义脚本命令。

```javascript
{
  // ...
  "scripts": {
    "build": "node build.js"
  }
}
```

上面代码是`package.json`文件的一个片段，里面的`scripts`字段是一个对象。它的每一个属性，对应一段脚本。比如，`build`命令对应的脚本是`node build.js`。

命令行下使用`npm run`命令，就可以执行这段脚本。

 ```bash
 $ npm run build
 # 等同于执行
 $ node build.js
 ```

这些定义在`package.json`里面的脚本，就称为 npm 脚本。

> 查看当前项目的所有 npm 脚本命令，可以使用不带参数`npm run` 命令。

