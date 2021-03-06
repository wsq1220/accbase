<p align="center">
  <a href="https://winyh.github.io/accbase/">
    <img width="300" src="https://github.com/winyh/accbase/blob/master/public/gopher.svg">
  </a>
</p>

<h1 align="center">Accbase</h1>

<p align="center">基于微服务的用户认证和权限的管理平台  代号：土拨鼠</p>

<div align="center">

![](https://img.shields.io/github/issues/winyh/accbase) ![](https://img.shields.io/github/languages/code-size/winyh/accbase) ![](https://img.shields.io/github/watch/winyh/accbase) ![](https://img.shields.io/github/stars/winyh/accbase) ![](https://img.shields.io/github/last-commit/winyh/accbase)

</div> 
这是一个基于Go micro + Casbin + Gin + Gorm + JWT 的用户认证和权限的微服务


### 基本原理图(重点理解聚合层)

![](https://github.com/winyh/accbase/blob/master/public/tech.png)

## 开始

- [目录说明](#目录说明)
- [依赖](#依赖)
- [使用](#使用)

## 生成 proto 文件 (GOPATH/src 目录里执行)

protoc --proto_path=.:$GOPATH/src --go_out=. --micro_out=. accbase/srv/auth/proto/auth.proto

## 目录说明

```
├── admin                                      // 后台管理界面
├── api                                        // Restful api 接口实现
├── cli                                        // cli 服务调用
├── cmd                                        // main函数文件目录
├── configs                                    // 项目配置
|   |—— model.conf                             // Casbin的模型配置（当前模式是RBAC基于角色的访问控制） 
|   |—— policy.csv                             // Casbin的策略配置
|—— database                                   // 数据库配置(gorm)
├── public                                     // 静态资源目录
|   |—— gopher.svg                             // logo 
|—— srv                                        // 数据库配置(gorm)
|   |—— auth                                   // auth srv 服务 
|—— go.mod                                     // 项目依赖管理
|—— Dockerfile                                 // Docker 文件
|—— README.md                                  // 说明文档
```

## 依赖
etcd 服务发现

```
# install etcd
go get -v go.etcd.io/etcd

go get -v go.etcd.io/etcd/etcdctl
```
etcd 启动

```
etcd
``` 

## 使用

进入到项目根目录(默认使用 mdns 服务发现)

运行srv服务
```
go run srv/auth/main.go
```

运行cli测试返回
```
go run cli/main.go
```

运行api服务
```
go run api/user/main.go
```

运行micro api 网关服务
```
micro api --handler=api --enable_rpc
```

运行micro web 控制台面板
```
micro web
```

## 管理界面

请查看 [管理界面](https://github.com/winyh/accbase/tree/master/admin/README.md)

## 调用示例
默认账号密码：

```
userName:"winyh"

password:"123456"
```

/auth/auth/register 注册
```
Action:POST
URL: http://localhost:8080/auth/auth/register
Content-Type:x-www-form-urlencoded

Params:
userName:"winyh",
password:"123456",
verifyPassword:"123456"

Response:
{
    "message": "注册成功！",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYmYiOjE1NzA3MDg4MDAsInBhc3N3b3JkIjoiMTIzNDU2IiwidXNlcm5hbWUiOiJ3aW55aCJ9.1UKdHGY_f6eXIxOJbvU3rW-nHkO_ZAMB9O8nXuCQka8"
}

```

/auth/auth/login 登录
```
Action:POST
URL: http://localhost:8080/auth/auth/login
Content-Type:x-www-form-urlencoded

Params:
userName:"winyh",
password:"123456"

Response:
{
    "message": "登录成功！",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYmYiOjE1NzA3MDg4MDAsInBhc3N3b3JkIjoiMTIzNDU2IiwidXNlcm5hbWUiOiJ3aW55aCJ9.1UKdHGY_f6eXIxOJbvU3rW-nHkO_ZAMB9O8nXuCQka8"
}

```

/rpc 直接调用
   ```
   Action:POST
   URL: http://localhost:8080/rpc 
   
   Params:
   {
   	"service":"go.micro.srv.auth",
   	"method":"Auth.Login",
   	"request":{
   		"userName":"winyh"
   	}
   }
   
   Response:
   {
       "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYmYiOjE1NzA3MDg4MDAsInBhc3N3b3JkIjoiIiwidXNlcm5hbWUiOiJ3aW55aCJ9.kDEBDzrP1yXbzFZ52q-BK7PZYEK_KBphnthOO9zFK9c"
   }
   
   ```



Todo List
1. 用户注册，获取token ✅
2. 用户登录，获取token ✅
3. 根据token，获取用户信息 ✅
4. 根据token，获取用户角色集合 ❌
5. 用户新增角色 ❌
6. 角色新增权限 ❌
7. 角色的增删改查 ❌
8. 权限的增删改查 ❌
9. 系统所有的角色列表 ❌
10. 完成 docker 自动部署 ❌
11. 项目开发思路详细说明 ❌

## 注意事项
1. 请注意保持 micro@1.14.0 和 go-micro@1.14.0 版本一致
2. 直接 `/rpc` 调用服务时，开启 `micro api --handle=api --enable_rpc` 一定要加上 --enable_rpc 参数，否则返回 500 错误 
___________________________
3.版本升级到了 micro@1.16.0 / go-micro@1.16.0