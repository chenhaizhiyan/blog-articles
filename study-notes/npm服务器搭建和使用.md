# npm 私有服务器搭建和使用

## 作用

- 可以通过权限管理发布私有包，保障发布包的私密性
- 可以同步官方仓库，提高包安装速度，方便同时管理公有库和私有库

## 可选方案

### sinopia (verdaccio)

#### 优点
- 配置简单
- 对环境依赖少（仅 node 就够了）
- 支持 windows 系统下运行

#### 缺点
- 权限管理比较弱，对用户权限，发布权限，下载权限控制不是很得心应手
- 缓存优化不足，经常会在安装共有包的时候处于挂起状态
- 不能做官方仓库的镜像

`sinopia` 比较适合个人搭建在本地作为 npm 缓存，这样，安装过的包会直接从缓存中获取，加快安装速度。对于企业级的应用，`cnpm` + `cnpmjs.org` 的方案更适合。

### cnpm + cnpmjs.org

#### 优点
- 权限管理等功能完善，更适合企业级使用

#### 缺点
- 配置较复杂

> 选型确定方案为cnpm + cnpmjs.org

## 环境要求

- mac或者linux（服务器基本都是linux的）
- nodejs:4.2.3 以上版本

## 基本安装

### nodejs
略
### git
略

### 克隆cnpmjs.org

通过github下载项目源代码

    git clone https://github.com/cnpm/cnpmjs.org.git

在我们开始安装依赖包之前，先要升级NPM的版本，不然会出现“No compatible version found”的错误

    sudo npm install npm -g
    /usr/local/bin/npm -> /usr/local/lib/node_modules/npm/bin/npm-cli.js
    npm@2.0.0-beta.0 /usr/local/lib/node_modules/npm

安装项目依赖

    sudo npm install 

### mysql

    rpm -qa | grep mariadb  
    rpm -e --nodeps mariadb/mysql
    sudo yum -y install mariadb-server
    systemctl start mariadb.service
    mysqladmin -u root password 'root'

创建数据库    
    
    Mysql -u root -p
    create database cnpmjs;
    use cnpmjs;

初始化数据表（也可以用图形化工具执行导入）

    source docs/db.sql【db.sql位于cnpmjs.org/docs/db.sql】

### 配置cnpmjs.org

在cnpmjs.org目录下

    vi config/config.js

> 也可以直接编辑config/index.js中的这部分内容，但建议另建config.js进行修改，index.js文件会自动合并config.js中的配置

输入以下内容

    'use strict';

    var path = require('path');
    var root = path.dirname(__dirname);
    var dataDir = path.join(process.env.HOME || root, '.cnpmjs.org');
     
    module.exports = { 
      database: {
        db: 'private_npm',
        username: 'root',
        password: 'root',
    
        // the sql dialect of the database
        // - currently supported: 'mysql', 'sqlite', 'postgres', 'mariadb'
        dialect: 'mysql',
    
        // custom host; default: 127.0.0.1
        host: '127.0.0.1',
    
        // custom port; default: 3306
        port: 3306,
    
        // use pooling in order to reduce db connection overload and to increase speed
        // currently only for mysql and postgresql (since v1.5.0)
        pool: {
          maxConnections: 10,
          minConnections: 0,
          maxIdleTime: 30000
        },
    
        dialectOptions: {
          // if your server run on full cpu load, please set trace to false
          trace: true,
        },
  
        // the storage engine for 'sqlite'
        // default store into ~/.cnpmjs.org/data.sqlite
        storage: path.join(dataDir, 'data.sqlite'),
    
        logging: !!process.env.SQL_DEBUG,
      }
    };

> 注意编辑完后`:wq`保存并退出

在cnpmjs.org目录下

    vi config/index.js

开放非本机访问，注释bindingHost一行。

    //bindingHost: '127.0.0.1', // only binding on 127.0.0.1 for local access

- 启动服务

        npm run start：启动 CNPM

> 附：`npm run stop`：停止 CNPM。`start`, `stop`, `test`是npm 内置命令，可以省略run直接运行`npm stop`

- 测试是否启动成功

在浏览器中输入：http://192.168.1.225:7002/，页面正常加载显示cnpm.org的页面

## 发布私有包

### 环境配置

- 更改默认的registry，指向私有的registry

        cnpm set registry http://192.168.1.225:7001

> 建议安装`nrm`进行管理

- 用npm账号登录你的私有registry

        $ cnpm login
        Username: admin
        Password: *** // 任意
        Email: (this IS public) admin@cnpmjs.org
> admin账户信息在`index.js`文件中配置

- 如果过程中遇到不能同步或者下载自己的模块 ，可以修改/config/index.js 文件

        enableAbbreviatedMetadata: true

### 创建私有测试包

- 生成测试包

选择一个测试包存放的目录位置

    $ mkdir helloworld && cd helloworld
    $ cnpm init
    name: @hd/helloworld
    version: 1.0.0

> 上述配置name的`@hd`要跟index.js配置中的一致，如下

    // registry scopes, if don't set, means do not support scopes
    scopes: [ '@hd' ],
> scopes配置值是数组，可以配置多个，可以用于私有包的分组管理，如公司级用`@hd`，部门级用`@hdit`等

- 生成package.json

        {
          "name": "@hd/helloworld",
          "version": "1.0.0",
          "description": "my first scoped package",
          "main": "index.js",
          "scripts": {
            "test": "echo \"Error: no test specified\" && exit 1"
          },
          "author": "",
          "license": "ISC"
        }

- 发布与同步

        npm publish

通过浏览器访问：http://192.168.1.225:7002/ 可查看和操作私有CNPM服务器
在搜索中查找helloworld，有搜索结果说明发布成功

- 配置文件设置参考

        enableCluster：是否启用 cluster-worker 模式启动服务，默认 false，生产环节推荐为 true; 利用多核
        registryPort：API 专用的 registry 服务端口，默认 7001；
        webPort：Web 服务端口，默认 7002；
        bindingHost：监听绑定的 Host，默认为 127.0.0.1，如果外面架了一层本地的 Nginx 反向代理或者 Apache 反向代理的话推荐不用改；
        sessionSecret：session 用的盐；
        logdir：日志目录；
        uploadDir：临时上传文件目录；
        viewCache：视图模板缓存是否开启，默认为 false；
        enableCompress：是否开启 gzip 压缩，默认为 false；
        admins：管理员们，这是一个 JSON Object，对应各键名为各管理员的用户名，键值为其邮箱，默认为 { fengmk2: 'fengmk2@gmail.com', admin: 'admin@cnpmjs.org', dead_horse: 'dead_horse@qq.com' }；
        logoURL：Logo 地址，不过对于我这个已经把 CNPM 前端改得面目全非的人来说已经忽略了这个配置了；
        adBanner：广告 Banner 的地址；
        customReadmeFile：实际上我们看到的 cnpmjs.org 首页中间一大堆冗长的介绍是一个 Markdown 文件转化而成的，你可以设置该项来自行替换这个文件；
        customFooter：自定义页脚模板；
        npmClientName：默认为 cnpm，如果你有自己开发或者 fork 的 npm 客户端的话请改成自己的 CLI 命令，这个应该会在一些页面的说明处替换成你所写的；
        backupFilePrefix：备份目录；
        database：数据库相关配置，为一个对象，默认如果不配置将会是一个 ~/.cnpmjs.org/data.sqlite 的 SQLite；
          odb：数据的库名；
          ousername：数据库用户名；
          opassword：数据库密码；
          odialect：数据库适配器，可选 "mysql"、"sqlite"、"postgres"、"mariadb"，默认为 "sqlite"；
          ohost：数据库地址；
          oport：数据库端口；
          opool：数据库连接池相关配置，为一个对象；
          omaxConnections：最大连接数，默认为 10；
          ominConnections：最小连接数，默认为 0；
          omaxIdleTime：单条链接最大空闲时间，默认为 30000 毫秒；
          ostorege：仅对 SQLite 配置有效，数据库地址，默认为 ~/.cnpmjs/data.sqlite；
        nfs：包文件系统处理对象，为一个 Node.js 对象，默认是 fs-cnpm 这个包，并且配置在 ~/.cnpmjs/nfs 目录下，也就是说默认所有同步的包都会被放在这个目录下；开发者可以使用别的一些文件系统插件（如上传到又拍云等）,又或者自己去按接口开发一个逻辑层，这些都是后话了；
        registryHost：默认为 r.cnpmjs.org；
        enablePrivate：是否开启私有模式，默认为 false；
          o如果是私有模式则只有管理员能发布包，其它人只能从源站同步包；
          o如果是非私有模式则所有登录用户都能发布包；
        scopes：非管理员发布包的时候只能用以 scopes 里面列举的命名空间为前缀来发布，如果没设置则无法发布，也就是说这是一个必填项，默认为 [ '@cnpm', '@cnpmtest', '@cnpm-test' ]，据苏千大大解释是为了便于管理以及让公司的员工自觉按需发布；更多关于 NPM scope 的说明请参见 npm-scope；
        privatePackages：就如该配置项的注释所述，出于历史包袱的原因，有些已经存在的私有包（可能之前是用 Git 的方式安装的）并没有以命名空间的形式来命名，而这种包本来是无法上传到 CNPM 的，这个配置项数组就是用来加这些例外白名单的，默认为一个空数组；
        sourceNpmRegistry：更新源 NPM 的 registry 地址，默认为 https://registry.npm.taobao.org；
        sourceNpmRegistryIsCNpm：源 registry 是否为 CNPM，默认为 true，如果你使用的源是官方 NPM 源，请将其设为 false；
        syncByInstall：如果安装包的时候发现包不存在，则尝试从更新源同步，默认为 true；
        syncModel：更新模式（不过我觉得是个 typo），有下面几种模式可以选择，默认为 "none";
          o"none"：永不同步，只管理私有用户上传的包，其它源包会直接从源站获取；
          o"exist"：定时同步已经存在于数据库的包；
          o"all"：定时同步所有源站的包；
        syncInterval：同步间隔，默认为 "10m" 即十分钟；
        syncDevDependencies：是否同步每个包里面的 devDependencies 包们，默认为 false；
        badgeSubject：包的 badge 显示的名字，默认为 cnpm；
        userService：用户验证接口，默认为 null，即无用户相关功能也就是无法有用户去上传包，该部分需要自己实现接口功能并配置，如与公司的 Gitlab 相对接，这也是后话了；
        alwaysAuth：是否始终需要用户验证，即便是 $ cnpm install 等命令；
        httpProxy：代理地址设置，用于你在墙内源站在墙外的情况。


> 参与教程：

> https://cnpmjs.org/

> CNPM搭建私有的NPM服务：http://blog.fens.me/nodejs-cnpm-npm/

> 卓健前端工程白皮书：https://www.kancloud.cn/eagles/fepro/326466

> 前端工程化指南：https://www.kancloud.cn/csnikey/fepro-guide/334551