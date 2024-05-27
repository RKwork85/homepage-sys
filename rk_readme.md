# 哎，关于那些坑


关于复现该项目

1 以下两个文件夹都好说，进入根目录下npm i 即可

>./ADMIN

>./CLIENT 

2 ./Server文件夹下 先 npm i

然后修改 package.json文件 script 三行 中的  && 为 &         //ubuntu下遇到的

就能启动起来了

3 还有一个是 关于mongodb数据库的"bug"如下:

>MongoServerError[AuthenticationFailed]: Authentication failed.

我用的是docker,思路是在navicat中导入数据库以后，进入到容器内，使用数据表ZHOUYI_DB 为该数据库添加一个超级权限

具体命令逻辑如下
```
docker exec -it mongo bash      
mongosh
use admin
db.auth("username","password")        //需替换为真实账号密码
use ZHOUYI_DB

db.createUser({
  user: "rkwork",
  pwd: "rkwork",
  roles: [
    { role: "userAdminAnyDatabase", db: "admin" },
    { role: "dbAdminAnyDatabase", db: "admin" },
    { role: "readWriteAnyDatabase", db: "admin" }
  ]
})

//解释： mongodb数据库会有权限限制，在其他数据库中登录用户是不可以的，现在admin中登录root, 然后再进入其他数据库中创建用户，赋予权限，这样在项目代码中就可以用账号密码进行登录数据库操作数据库

```

4 数据库配置文件修改
./SERVER/.env.development 注释掉 16行 增加   DB_USER = username DB_PASS = password        //需替换为 真实账号密码
./SERVER/.env.production 增加 DB_USER = username DB_PASS = password
./SERVER/config/db.config.js 第九行 改为如下

    url: `mongodb://${process.env.DB_USER}:${process.env.DB_PASS}@${process.env.DB_URL}:${process.env.DB_PORT}/${process.env.DB_NAME}`,

即可运行全服务








