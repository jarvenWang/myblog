---
author: "wangjinbao"
title: "kratos框架项目实践"
date: 2022-12-05 00:00:01
description: "一套轻量级 Go 微服务框架，包含大量微服务相关框架及工具"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags: 
- golang
- categories:




---

## 1.在项目根目录下新建models目录：

## 2.下载gorm包
```shell
$ go get -u gorm.io/gorm
go: downloading gorm.io/gorm v1.25.5
go: downloading github.com/jinzhu/now v1.1.5
go: added github.com/jinzhu/inflection v1.0.0
go: added github.com/jinzhu/now v1.1.5
go: added gorm.io/gorm v1.25.5

$  go get -u gorm.io/driver/mysql 
go: downloading gorm.io/driver/mysql v1.5.2
go: downloading github.com/go-sql-driver/mysql v1.7.0
go: added github.com/go-sql-driver/mysql v1.7.1
go: added gorm.io/driver/mysql v1.5.2

```
## 3.新增数据表结构体
models/repo_basic.go 内容如下：
```golang
 package models

import "gorm.io/gorm"

type RepoBasic struct {
	gorm.Model
	Identity string `gorm:"column:identity;type:varchar(36);" json:"identity"` //唯一标识
	Name     string `gorm:"column:name;type:varchar(255);" json:"name"`        //Name
	Desc     string `gorm:"column:desc;type:varchar(255);" json:"desc"`        //desc
	Star     int    `gorm:"column:star;type:int(11);default:0;" json:"star"`   //star
}
```

models/user_basic.go 内容如下：
```golang
package models

import "gorm.io/gorm"

type UserBasic struct {
	gorm.Model
	Identity string `gorm:"column:identity;type:varchar(36);" json:"identity"`  //唯一标识
	Username string `gorm:"column:username;type:varchar(255);" json:"username"` // 用户名
	Password string `gorm:"column:password;type:varchar(36);" json:"password"`  //密码
}

func (table *UserBasic) TableName() string {
	return "user_basic"
}
```

models/repo_user.go 内容如下：
```golang
package models

import "gorm.io/gorm"

type RepoUser struct {
	gorm.Model
	Rid  int `gorm:"column:rid;type:int(11);" json:"rid"`      //仓库ID
	Uid  int `gorm:"column:uid;type:int(11);" json:"uid"`      //用户ID
	Type int `gorm:"column:type;type:tinyint(1);" json:"type"` //类型{1所有者2被授权者}
}

func (table *RepoUser) TableName() string {
	return "repo_user"
}

```

models/repo_star.go 内容如下：
```golang
package models

import "gorm.io/gorm"

type RepoStar struct {
	gorm.Model
	Rid int `gorm:"column:rid;type:int(11);" json:"rid"` //仓库ID
	Uid int `gorm:"column:uid;type:int(11);" json:"uid"` //用户ID
}

func (table *RepoStar) TableName() string {
	return "repo_star"
}

```
## 4创建 user.proto 并修改
根目录下执行：
```shell
kratos proto add api/git/user.proto
```
user.proto 内容如下：
```proto
syntax = "proto3";

package api.git;

import "google/api/annotations.proto";

option go_package = "customer/api/git;git";
option java_multiple_files = true;
option java_package = "api.git";

service User {
	rpc Login (LoginRequest) returns (LoginReply){
		option (google.api.http) = {
			get: "/login"
		};
	};

}

message LoginRequest {
	string  username = 1; //用户名
	string  password = 2;//密码
}
message LoginReply {
	string  token = 1; //token
}
```

## 5 创建 PB
```shell
kratos proto client api/git/user.proto
```

## 6 创建 Service
```shell
kratos proto server api/git/user.proto -t internal/service
```
在cmd -> main.go -> wireApp -> NewHTTPServer 中注册服务

customer/internal/server/http.go 修改内容：
```golang
git.RegisterUserHTTPServer(srv, service.NewUserService())
```

## 7 初始化DB
#### 新增 models/init.go文件
内容：
```golang
package models

import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
)

var DB *gorm.DB

func NewDB(dsn string) error {
	db, err := gorm.Open(mysql.Open(dsn))
	if err != nil {
		return err
	}
	err = db.AutoMigrate(&UserBasic{})
	if err != nil {
		return err
	}
	err = db.AutoMigrate(&RepoBasic{})
	if err != nil {
		return err
	}
	err = db.AutoMigrate(&RepoUser{})
	if err != nil {
		return err
	}
	err = db.AutoMigrate(&RepoStar{})
	if err != nil {
		return err
	}
	DB = db
	return nil
}
```

#### 加载 init.go
cmd/main.go文件加载 init.go文件：
```golang
# wireApp 关键字前加
	//init db
	err := models.NewDB(bc.Data.Database.Source)
	if err != nil {
		panic(err)
	}
```

#### 修改config参数
修改config.yaml 数据库配置参数：
```yaml
data:
  database:
    driver: mysql
    source: root:654321@tcp(127.0.0.1:3306)/up-git?charset=utf8mb4&parseTime=True&loc=Local
```

> PS: proto 文件只要修改就重新执行：
> `kratos proto client api/git/user.proto`

## 8用迁移文件生数据表：
```golang
db.AutoMigrate(&RepoUser{})
```

## 9修改service/user.go文件：
```golang
func (s *UserService) Login(ctx context.Context, req *pb.LoginRequest) (*pb.LoginReply, error) {
	ub := new(models.UserBasic)
	err := models.DB.Where("username = ? AND password = ?", req.Username, req.Password).First(ub).Error
	if err != nil {
		return nil, err
	}

	return &pb.LoginReply{
		Token: "token",
	}, nil
}
```
### 10验证接口
POST http://127.0.0.1:8600/login
{
"username":"admin",
"password":"123321"
}
{
"token": "token"
}