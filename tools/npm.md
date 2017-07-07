# npm

## 简介

NPM（node package manager），通常被称为node包管理器，它的主要功能就是管理node包，即安装、卸载、更新、查看、搜索、发布等。

npm的背后，是基于couchdb的一个数据库，详细记录了每个包的信息，包括作者、版本、依赖、授权信息等。它的一个很重要的作用就是：将开发者从繁琐的包管理工作（版本、依赖等）中解放出来，更加专注于功能的开发。

### 1. package.json

package.json文件在项目的根目录下，是给npm使用的一个配置文件，里面的字段都用来描述当前的项目，有很多的字段，主要的有

1. name: package的名字，由于他会成为url的一部分，所以non-url-safe的字符是不会通过的的，例如一些特殊的字符`_ . 、`，如果要发布的话，最好先[搜索](http://registry.npmjs.org/上搜下你取的名字是否已经存在)一下包名是否已经存在。

2. version: package的版本号，当package发生变化时，version也应该跟着变化，同时，你声明的版本需要通过semver的校验

3. dependencies: package的应用依赖的模块，即别人要使用这个package，需要安装哪些包。

4. devDependencies: 在开发package时，所需要依赖的模块。 

5. scripts: 配置常用的命令，然后可以使用npm来快速地执行，举个例子，我们使用mocha来进行测试，默认我们使用tdd的接口来进行测试，可以在package.json中配置

    ```javascript
    "script":{
        "test" : "mocha -u tdd"
    }
    ```

    之后，我们只需要运行`npm test`，就可以执行这个任务了，注意后面跟的文件路径是以package.json为根目录
    
### 2. 安装模式

1. 全局模式下安装，Node包会被安装到Node的安装目录下的node_modules下

    ```shell
    npm install -g moduleName
    ```

2. 本地模式下安装（默认），包会被安装到本目录下的node_modules目录下。

    ```shell
    npm install moduleName
    ```

### 3. 配置代理

npm的配置工作主要是通过`npm config`，比较常见就是配置代理，可以避免网络原因而无法安装包

```shell
npm config set proxy http://proxy.example.com:8080 # 设置代理
npm config get proxy # 查看当前代理
npm config delete proxy # 删除代理
npm config list # 查看所有配置
npm config edit # 直接修改配置文件
```

## 常用命令

### 1.  初始化

会一步步引导创建一个package.json文件，其中package.json描述了这个文件夹的结构

```shell
npm init
```

### 2. 安装

1. 安装特定的包

    ```shell
    npm install grunt-cli # 安装最新版本的
    npm install grunt-cli@"0.1.9" # 安装指定版本
    ```

2. 根据package.json安装

    ```shell
    npm install # 安装package.json里面的依赖的包
    ```

package.json并不会主动记录我们所安装的包，因此需要我们在安装的时候，加一个选项，来记录我们安装过的包，达到依赖包的同步。 

```shell
npm install moduleName --save # 正式发布的依赖包
npm install moduleName --save-dev  # 开发环境时依赖的包
```

### 3. 卸载

```shell
npm uninstall moduleName
```

### 4. 查看

```shell
npm ls # 查看全部已安装的包
npm ls pkg # 查看特定package的信息
```

如果需要查看一个包更加详细的信息

```shell
npm info pkg
```

### 5. 更新

```shell
npm update pkg # 更新一个包
```

### 6. 搜索

```shell
npm search pkg # 搜索一个包
```

### 6. 发布

```shell
npm publish <tarball>
npm publish <folder>
```

## 参考

1. [npm官网](https://npmjs.org/)
2. [npm官方文档](https://npmjs.org/doc/README.html)