---
title: 网易蜂巢 OpenAPI

language_tabs:
  - http
  - shell


toc_footers:
  - <a href='https://c.163.com/dashboard' target='_blank'>控制台</a>

includes:
  - errors

search: true
---


# 1. OpenAPI 简介

<aside class="success">网易蜂巢 OpenAPI Beta 版，未来可能会有所调整。若有任何问题，请提交工单联系我们。</aside> 

蜂巢 API 提供更灵活的资源控制方式，满足你的定制化需求，如自动化部署、持续集成等功能。蜂巢 API 采用 Restful API 风格，支持内省，支持 API 访问频率限制。

蜂巢 API 需要使用 API Token 来发起 API 请求。

API 访问地址： https://open.c.163.com。


## 1.1. 功能特点

* 支持内省
  * 为每一个请求响应包含一个 Request-Id 头，并使用 UUID 作为该值。通过在客户端、服务器或任何支持服务上记录该值，它能为我们提供一种机制来跟踪、诊断和调试请求。
* 安全
  * 所有 API 均采用 Https。
* 频率限制
  * 针对授权接口，支持基于用户的频率限制，频率值根据用户的等级级别设置；
  * 针对非授权接口，支持基于 IP 的频率限制。

## 1.2. Access Key

注册后，蜂巢会颁发 Access Key 和 Access Secret 给客户 。
没有颁发认证授权的将无法调用任何接口
说明：
* Access Key：用于标识客户身份，在网络请求中会以某种形式传输；
* Access Secret ：作为私钥形式存储于客户方本地， 不在网络传递，它的作用是对客户方发起的请求进行数字签名，保证该请求是来自指定客户的请求，并且是合法的有效的。

使用 Access Key 进行身份识别，加上 Access Secret 进行数字签名，即可完成应用接入与认证授权。
你可以在 [Access Key](https://c.163.com/dashboard#/m/account/accesskey/) 中创建、下载、删除、禁用你的 Access Key。
蜂巢支持多 Access Key、Access Secret，保障你的应用安全。


## 1.3. 接口设计风格

OpenAPI 的设计采用了 Restful API 风格。下面将简要介绍 OpenAPI 接口设计风格。

### 1.3.1. 协议

客户端访问 OpenAPI 接口服务，必须采用 HTTPS 协议，即所有的访问接口行为，都需要用 TLS ，通过安全连接来访问。

### 1.3.2. 版本号

OpenAPI 将 API 版本号放入了 URL 中。举个例子，当前使用 OpenAPI 创建镜像仓库的 URL 为 https://open.c.163.com/API/v1/repositories，其中 v1 指的是当前 API 版本为 v1。

### 1.3.3. 请求

如果请求的 body 需要携带参数，需要将参数 JSON 格式化，否则会得到错误响应。同时，请求中需要规范地使用HTTP 请求方法动词，即：

* GET：从服务器获取资源（一项或多项）；
* POST：在服务器上新建资源；
* PUT：在服务器上更新资源（客户端提供改变后的完整资源）；
* DELETE：在服务器上删除资源。

### 1.3.4. 响应

OpenAPI 的接口服务会为每次响应，按照 HTTP 规范返回合适的响应状态码。并且，在错误响应中，除了上述的 Request-ID，还会生成结构化的错误信息，即机器可读错误码（code）和肉眼可读的错误信息（message）。其中错误码的构造规则为：code = HttpStatus.code (长度3) + OpenAPI 服务编码(长度2) + 细分错误码(长度2)。对于正确的响应，针对不同的操作，OpenAPI 接口向用户返回的结果符合以下规范：

* GET 请求：返回资源对象的列表或者单个资源对象；
* POST 请求：返回新建的资源的 ID；
* PUT 请求：返回处理结果状态码，比如状态码 200；
* DELETE 请求：返回处理结果状态码，比如状态码 200。


# 2. 开始使用

### 2.1. 获取 Access Key 和 App Secret

登录 [网易蜂巢控制台](https://c.163.com/dashboard#/m/account/accesskey/)，点击右上角你的头像，进入「用户中心」选择「Access Key」标签，即可管理你的 Access Key。

### 2.2. 获取 Token

使用 Access Key 和 App Secret，调用 OpenAPI 中的 [Token 生成接口](../?http#3-1-api-token)，获取授权 Token。

### 2.3. 开始使用

使用 Token 便可以访问 OpenAPI 中的全部接口服务。如果请求没有 Token，OpenAPI 的安全机制将会视其为对服务（除了获取 Token 服务和 WebHook 服务）的未授权访问操作，进而会拦截。

<aside class="notice">OpenAPI 中内置了流控机制，对于恶意攻击 OpenAPI 服务的行为，会采取限制措施。</aside>


# 3. 用户

## 3.1. 生成 API Token
> 请求示例

```http
POST /api/v1/token HTTP/1.1
Host: open.c.163.com
Content-Type: application/json

{
  "app_key": "19275e8b113f48e1b0c5ce0d6f1647a8", 
  "app_secret": "e9846f12c8e44edc8b0d4df065e5cd89"
}
```

```shell
curl -X POST -H "Content-Type: application/json" -d '{  "app_key": "19275e8b113f48e1b0c5ce0d6f1647a8",  "app_secret": "e9846f12c8e44edc8b0d4df065e5cd89"}' "https://open.c.163.com/api/v1/token"
```

> 响应示例

```json
{
  "token": "48e6b1bdb5fb4a28a680a977dffb3c30",
  "expires_in": "86399"
}
```


通过 Access Key 和 Access Secret 获取 Token。[点我查看 APP Key 及 APP Secret](https://c.163.com/dashboard#/m/account/accesskey/)

<aside class="warning">Token 有效期为 24 小时。</aside>


### HTTP Request

`POST https://open.c.163.com/api/v1/token`

### URL Parameters

|    参数    |  类型  | 是否必填 |        描述        |              示例值              |
|------------|--------|----------|--------------------|----------------------------------|
| app_key    | String | 是       | 用户 Access Key    | 19275e8b113f48e1b0c5ce0d6f1647a8 |
| app_secret | String | 是       | 用户 Access Secret | e9846f12c8e44edc8b0d4df065e5cd89 |

### Query Parameters

|    参数    |  类型  |             描述             |              示例值              |
|------------|--------|------------------------------|----------------------------------|
| token      | String | 访问 API 的授权码            | 48e6b1bdb5fb4a28a680a977dffb3c30 |
| expires_in | String | API 授权码的失效时间，单位秒 | 86399                            |



# 4. 密钥

## 4.1. 创建密钥

> 请求示例

```http
POST /api/v1/secret-keys HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a23a680a977dffb3c30
Content-Type: application/json

{
  "key_name": "mykey"
}
```

```shell
curl -X POST -H "Authorization: Token 48e6b1bdb5fb4a23a680a977dffb3c30" -H "Content-Type: application/json" -d '{
  "key_name": "keyname"
}' "https://open.c.163.com/api/v1/secret-keys"
```

> 响应示例

```json
{
  "id": 119766,
  "name": "mykey",
  "fingerprint": "e7:a9:dd:85:2a:a8:fe:bd:34:35:e8:74:65:ac:e7:7e",
  "created_at": "2016-12-22T13:03:00Z"
}
```
### HTTP Request

`POST https://open.c.163.com/api/v1/secret-keys`

### URL Parameters

|   参数   |  类型  | 是否必填 |   描述   | 示例值 |
|----------|--------|----------|----------|--------|
| key_name | String | 否       | 密钥名称 | mykey  |



### Query Parameters

|     参数    |  类型  |                              描述                             |                     示例值                      |
|-------------|--------|---------------------------------------------------------------|-------------------------------------------------|
| id          | long   | 密钥 id                                                       | 119766                                          |
| name        | String | 密钥名称                                                      | mykey                                           |
| fingerprint | String | 密钥指纹                                                      | e7:a9:dd:85:2a:a8:fe:bd:34:35:e8:74:65:ac:e7:7e |
| created_at  | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-22T13:03:00Z                            |




## 4.2. 获取密钥列表

> 请求示例

```http
GET /api/v1/secret-keys HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/secret-keys"
```

> 响应示例

```json
[
  {
    "id": 119766,
    "name": "mykey",
    "fingerprint": "e7:a9:dd:85:2a:a8:fe:bd:34:35:e8:74:65:ac:e7:7e",
    "created_at": "2016-12-22T13:03:00Z"
  },
  {
    "id": 119646,
    "name": "mykey2",
    "fingerprint": "39:11:74:90:6c:bc:12:56:03:8b:a4:46:df:d7:c1:2c",
    "created_at": "2016-12-21T02:53:21Z"
  }
]
```
### HTTP Request

`GET https://open.c.163.com/api/v1/secret-keys`

### Query Parameters

|     参数    |  类型  |                              描述                             |                     示例值                      |
|-------------|--------|---------------------------------------------------------------|-------------------------------------------------|
| id          | long   | 密钥 id                                                       | 119766                                          |
| name        | String | 密钥名称                                                      | mykey                                           |
| fingerprint | String | 密钥指纹                                                      | e7:a9:dd:85:2a:a8:fe:bd:34:35:e8:74:65:ac:e7:7e |
| created_at  | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-22T13:03:00Z                            |


## 4.3. 查看私钥文件

通过 [获取密钥列表](../?http#4-2) 获取的密钥 id 查看私钥文件内容。

<aside class="notice">此接口每用户每秒访问次数为 300 次。</aside>

> 请求示例

```http
GET /api/v1/secret-keys/119766 HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/secret-keys/119766"
```


> 响应示例

```
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAqARla6u0o+hDkoZ6xXRq1IiuAEwL4q1UpAXzMMthXIfllu38
wR871yAOR5Y`````私钥文件内容`````oB+b8GTFS9bipE4zgI6Ttxxu0du/B6Q
vkLu1Imf0etmoa+J9icqdEO2tBSCl67R1gBmM1BjY2tVEBOa8wNRYQ==
-----END RSA PRIVATE KEY-----
```
### HTTP Request

`GET https://open.c.163.com/api/v1/secret-keys/{id}`

### URL Parameters

| 参数 | 类型 |              是否必填              |   描述  |
|------|------|------------------------------------|---------|
| id   | long | 是（[获取密钥列表](../?http#4-2)） | 密钥 id |


## 4.4. 删除密钥

通过 [获取密钥列表](../?http#4-2) 获取的密钥 id 删除密钥。

> 请求示例

```http
DELETE /api/v1/secret-keys/119766 HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type: application/json
```

```shell
curl -X DELETE -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/secret-keys/119766"
```


### HTTP Request

`DELETE https://open.c.163.com/api/v1/secret-keys/{id}`

### URL Parameters

| 参数 | 类型 |              是否必填              |   描述  |
|------|------|------------------------------------|---------|
| id   | long | 是（[获取密钥列表](../?http#4-2)） | 密钥 id |



# 5. 镜像

## 5.1. 构建镜像

使用 Dockerfile 构建镜像。我们也建议你 [推送本地镜像](http://support.c.163.com/md.html#!容器服务/镜像仓库/使用指南/推送本地镜像.md)。

> 请求示例

```http
POST /api/v1/repositories/myrepo/tags/v1/actions/build/?docker_file=xxxx HTTP/1.1
Host: open.c.163.com
Authorization:Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type:multipart/form-data
```

### HTTP Request

`POST https://open.c.163.com/api/v1/repositories/{repo_name}/tags/{tag}/actions/build/?docker_file={docker_file}`

### URL Parameters
|   参数说明  |               描述              |  类型  | 是否必填 |
|-------------|---------------------------------|--------|----------|
| repo_name   | 镜像仓库名称                    | String | 必填     |
| tag         | 镜像 tag                        | String | 必填     |
| docker_file | docker xml 文件，大小限制在 1 M | file   | 必填     |

<aside class="notice">其他构建方式待更新。</aside> 


## 5.2. 获取镜像仓库列表

> 请求示例

```http
GET /api/v1/repositories?limit=20&offset=0 HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/repositories?limit=20&offset=0"
```

> 响应示例

```json
{
      "total": 2,
      "repositories": [
        {
          "repo_id": 45433,
          "user_name": "admin",
          "repo_name": "mysql",
          "open_level": 0,
          "base_desc": "基本描述",
          "detail_desc": "详细描述",
          "tag_count": 2,
          "created_at": "2016-12-21T02:27:12Z",
          "updated_at": "2016-12-21T02:27:12Z"
        },
        {
          "repo_id": 44627,
          "user_name": "admin",
          "repo_name": "axure",
          "open_level": 0,
          "base_desc": "basedesc",
          "detail_desc": "detaildesc",
          "tag_count": 5,
          "created_at": "2016-12-15T06:50:20Z",
          "updated_at": "2016-12-15T08:28:44Z"
        }
      ]
}
```



### HTTP Request

`GET https://open.c.163.com/api/v1/repositories?limit={limit}&offset={offset}`

### URL Parameters

|  参数  | 类型 | 是否必填 |                        描述                       | 示例值 |
| ------ | ---- | -------- | ------------------------------------------------- | ------ |
| limit  | int  | 否       | 每次获取的镜像仓库数量，取值范围 (0,30]，默认 20  |     20 |
| offset | int  | 否       | 获取镜像仓库列表起始位置，取值范围 [0,+∞)，默认 0 |      0 |


### Query Parameters

|     参数    |  类型  |                              描述                             |        示例值        |
|-------------|--------|---------------------------------------------------------------|----------------------|
| repo_id     | long   | 镜像仓库 id                                                   | 45433                |
| user_name   | String | 用户名                                                        | admin                |
| repo_name   | String | 镜像仓库库名                                                  | mysql                |
| open_level  | int    | 开放级别，0 （私有）/1（公有）                                | 0                    |
| base_desc   | String | 基本描述                                                      | 基本描述             |
| detail_desc | String | 详细描述                                                      | 详细描述             |
| tag_count   | int    | 版本数量                                                      | 2                    |
| created_at  | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-21T02:27:12Z |
| updated_at  | String | 修改时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-21T02:27:12Z |


## 5.3. 查看镜像仓库

通过 [获取镜像仓库列表](../?http#5-2) 获取的镜像仓库 id 查看某个镜像仓库的具体信息。

> 请求示例

```http
GET /api/v1/repositories/45433 HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
```

```shell
curl -X GET -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/repositories/45433"
```

> 响应示例

```json
{
  "tags": [
    {
      "name": "master20161215042831791",
      "size": 190425418,
      "status": 2
    },
    {
      "name": "master20161215034013640",
      "size": 0,
      "status": 2
    }
  ],
  "repo_id": 45433,
  "user_name": "admin",
  "repo_name": "mysql",
  "open_level": 0,
  "base_desc": "基本描述",
  "detail_desc": "详细描述",
  "tag_count": 2,
  "download_url": "hub.c.163.com/admin/mysql:master20161215042831791",
  "created_at": "2016-12-15T06:50:20Z",
  "updated_at": "2016-12-15T08:28:44Z"
}
```



### HTTP Request

`GET https://open.c.163.com/api/v1/repositories/{id}`

### URL Parameters

| 参数 | 类型 |                是否必填                |     描述    |
|------|------|----------------------------------------|-------------|
| id   | long | 是（[获取镜像仓库列表](../?http#5-2)） | 镜像仓库 id |


### Query Parameters

|   参数说明   |  类型  |                               描述                              |                      示例值                       |
|--------------|--------|-----------------------------------------------------------------|---------------------------------------------------|
| name         | String | 镜像 tag                                                        | master20161215042831791                           |
| size         | long   | 镜像大小，单位 B                                                | 190425418                                         |
| status       | int    | 镜像状态，0 (初始化) / 1 (构建中) / 2 (构建成功) / 3 (构建失败) | 2                                                 |
| repo_id      | long   | 镜像仓库 id                                                     | 45433                                             |
| user_name    | String | 用户名                                                          | admin                                             |
| repo_name    | String | 镜像仓库名                                                      | mysql                                             |
| open_level   | int    | 开放级别，0（私有）/1（公有）                                   | 0                                                 |
| base_desc    | String | 基本描述                                                        | 基本描述                                          |
| detail_desc  | String | 详细描述                                                        | 详细描述                                          |
| tag_count    | int    | 版本数量                                                        | 2                                                 |
| download_url | String | docker pull 地址                                                | hub.c.163.com/admin/mysql:master20161215042831791 |
| created_at   | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化   | 2016-12-15T06:50:20Z                              |
| updated_at   | String | 修改时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化   | 2016-12-15T08:28:44Z                              |


## 5.4. 删除镜像

删除某个镜像仓库内的某个镜像。

> 请求示例

```http
DELETE /api/v1/repositories/mysql/tags/v1 HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type:application/json
```

```shell
curl -X DELETE -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/repositories/mysql/tags/v1"
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/repositories/{mysql}/tags/{tag}`

### URL Parameters

|    参数   |  类型  | 是否必填 |     描述     |
|-----------|--------|----------|--------------|
| repo_name | String | 是       | 镜像仓库名称 |
| tag       | String | 是       | 镜像 tag     |


# 6. 负载均衡

负载均衡实例和监听器相关接口为异步接口，证书相关接口为同步接口。

<aside class="warning">负载均衡 API 参数中的 namespace 为服务的空间名称（以用户名结尾），不是空间显示名称。</aside>

## 6.1. 创建负载均衡


> 请求示例

```http
POST /api/v1/nlb/loadbalancers HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
namespace: my-ev-admin

{
    "name": "mylb",
    "bandwidthLimit": 1, 
    "chargeMode": "bandwidth", 
    "description": "mylb description",
    "address": "59.111.163.163"
}
```

```shell
curl -X POST -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" -H "namespace: my-ev-admin" -d '{
    "name": "mylb"
    "chargeMode": "bandwidth", 
    "bandwidthLimit": 1, 
    "description": "mylb description", 
    "address": "59.111.163.163"
}
' "https://open.c.163.com/api/v1/nlb/loadbalancers"
```

> 响应示例

```json
{
  "name": "mylb",
  "description": "mylb description",
  "address": "59.111.163.163",
  "status": "CREATING",
  "chargeMode": "bandwidth",
  "bandwidthLimit": 1,
  "createtime": "2016-12-29T07:20:26.000Z",
  "updatetime": "2016-12-29T07:20:26.000Z"
}
```

### HTTP Request

`POST https://open.c.163.com/api/v1/nlb/loadbalancers`

### URL Parameters

|      参数      |  类型  | 是否必填 |                              描述                              |      示例值      |
| -------------- | ------ | -------- | -------------------------------------------------------------- | ---------------- |
| namespace      | String | 是       | 空间名称（[获取空间列表](../?http#9-2)）                       | my-ev-admin      |
| name           | String | 是       | 负载均衡名称                                                   | mylb             |
| chargeMode     | String | 否       | 计费方式，netflow（流量）/bandwidth（带宽）<br>默认按流量计费  | bandwidth        |
| bandwidthLimit | int    | 否       | 带宽限制，单位 Mbps，取值范围 [1,100]<br>默认为最大值 100 Mbps | 1                |
| description    | String | 否       | 自定义描述信息                                                 | mylb description |
| address        | String | 否       | 通过指定 IP 创建实例（[获取 IP 列表](../?http#8-2-ip)）        | 59.111.163.163   |

### Query Parameters

|      参数      |  类型  |                              描述                             |          示例值          |
| -------------- | ------ | ------------------------------------------------------------- | ------------------------ |
| name           | String | 负载均衡名称                                                  | mylb                     |
| description    | String | 自定义描述信息                                                | mylb description         |
| address        | String | IP 地址（指定 IP 创建时显示）                                 | 59.111.163.163           |
| status         | String | 负载均衡状态                                                  | CREATING                 |
| chargeMode     | String | 计费方式                                                      | bandwidth                |
| bandwidthLimit | int    | 带宽限制，单位 Mbps                                           | 1                        |
| createtime     | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-29T07:20:26.000Z |
| updatetime     | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-29T07:20:26.000Z |



## 6.2. 获取负载均衡列表

> 请求示例

```http
GET /api/v1/nlb/loadbalancers?offset=0&limit=50 HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/nlb/loadbalancers?offset=0&limit=50"
```

> 响应示例

```json
[
  {
    "name": "mylb",
    "description": "mylb description",
    "address": "59.111.163.163",
    "status": "WORKING",
    "instanceStatus": "UP",
    "chargeMode": "bandwidth",
    "bandwidthLimit": 1,
    "namespace": "my-ev-admin",
    "createtime": "2016-12-29T07:46:05.455Z",
    "updatetime": "2016-12-29T07:46:24.967Z"
  },
  {
    "name": "mysql",
    "description": "",
    "address": "59.111.126.126",
    "status": "WORKING",
    "instanceStatus": "UP",
    "chargeMode": "netflow",
    "bandwidthLimit": 100,
    "namespace": "pro-ev-admin",
    "createtime": "2016-12-15T09:33:53.605Z",
    "updatetime": "2016-12-15T09:35:33.703Z"
  }
]
```

### HTTP Request

`GET https://open.c.163.com/api/v1/nlb/loadbalancers?offset={offset}&limit={limit}`

### URL Parameters

|  参数  | 类型 | 是否必填 |                        描述                       | 示例值 |
| ------ | ---- | -------- | ------------------------------------------------- | ------ |
| offset | int  | 否       | 获取负载均衡列表起始位置，取值范围 [0,+∞)，默认 0 |      0 |
| limit  | int  | 否       | 每次获取负载均衡的数量，取值范围 [0,200)，默认 50 |     50 |

### Query Parameters

|      参数      |  类型  |                                描述                               |          示例值          |
| -------------- | ------ | ----------------------------------------------------------------- | ------------------------ |
| name           | String | 负载均衡名称                                                      | mylb                     |
| description    | String | 自定义描述信息                                                    | mylb description         |
| address        | String | IP 地址                                                           | 59.111.163.163           |
| status         | String | 负载均衡状态                                                      | WORKING                  |
| instanceStatus | String | 后端服务器综合状态，只要一个端口为 DOWN，则为 DOWN，其余情况为 UP | UP                       |
| chargeMode     | String | 计费方式                                                          | bandwidth                |
| bandwidthLimit | int    | 带宽限制，单位 Mbps                                               | 1                        |
| namespace      | String | 空间名称                                                          | my-ev-admin              |
| createtime     | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化     | 2016-12-29T07:46:05.455Z |
| updatetime     | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化     | 2016-12-29T07:46:24.967Z |


## 6.3. 查看负载均衡详情

查看某个负载均衡实例的详情信息。

> 请求示例

```http
GET /api/v1/nlb/loadbalancers/mylb HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
namespace: my-ev-admin
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" -H "namespace: my-ev-admin" "https://open.c.163.com/api/v1/nlb/loadbalancers/mylb"
```

> 响应示例

```json
{
  "name": "mylb",
  "description": "mylb description",
  "address": "59.111.163.163",
  "status": "WORKING",
  "instanceStatus": "UP",
  "chargeMode": "netflow",
  "bandwidthLimit": 100,
  "namespace": "my-ev-admin",
  "createtime": "2016-12-15T09:33:53.605Z",
  "updatetime": "2016-12-15T09:35:33.703Z",
  "listeners": [
    {
      "name": "http-listeners",
      "listenPort": 80,
      "protocol": "http",
      "createtime": "2016-12-15T09:35:33.367Z",
      "updatetime": "2016-12-15T09:35:33.367Z",
      "clusters": [
        {
          "name": "groupName_3a407e12-add5-4ad7-8a92-5425eee88fc7",
          "serverName": "*",
          "path": "/",
          "serviceName": "myservice",
          "servicePort": 80,
          "policy": {
            "mode": "insert",
            "expire": 60
          }
        }
      ]
    }
  ]
}
```

### HTTP Request

`GET https://open.c.163.com/api/v1/nlb/loadbalancers/{lb_name}`

### URL Parameters

|    参数   |  类型  | 是否必填 |     描述     |   示例值    |
| --------- | ------ | -------- | ------------ | ----------- |
| lb_name   | String | 是       | 负载均衡名称 | mylb        |
| namespace | String | 是       | 空间名称     | my-ev-admin |

### Query Parameters

|      参数      |  类型  |                                描述                               |          示例值          |
| -------------- | ------ | ----------------------------------------------------------------- | ------------------------ |
| name           | String | 负载均衡名称（[获取负载均衡列表](../?http#6-2)）                    | mylb                     |
| description    | String | 自定义描述信息                                                    | mylb description         |
| address        | String | IP 地址                                                           | 59.111.163.163           |
| status         | String | 负载均衡状态                                                      | WORKING                  |
| instanceStatus | String | 后端服务器综合状态，只要一个端口为 DOWN，则为 DOWN，其余情况为 UP | UP                       |
| chargeMode     | String | 计费方式                                                          | netflow                  |
| bandwidthLimit | int    | 带宽限制，单位 Mbps                                               | 100                      |
| namespace      | String | 空间名称                                                          | my-ev-admin              |
| createtime     | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化     | 2016-12-29T07:46:05.455Z |
| updatetime     | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化     | 2016-12-29T07:46:24.967Z |

| listeners 参数 |  类型  |                              描述                             |          示例值          |
| -------------- | ------ | ------------------------------------------------------------- | ------------------------ |
| name           | String | 监听名称                                                      | http-listeners           |
| listenPort     | int    | 监听端口                                                      | 80                       |
| protocol       | String | 监听协议                                                      | http                     |
| createtime     | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-15T09:35:33.367Z |
| updatetime     | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-15T09:35:33.367Z |

| clusters 参数 |    类型    |                          描述                          |                     示例值                     |
| ------------- | ---------- | ------------------------------------------------------ | ---------------------------------------------- |
| name          | String     | 规则名称                                               | groupName_3a407e12-add5-4ad7-8a92-5425eee88fc7 |
| serverName    | String     | 域名                                                   | *                                              |
| path          | String     | URL 配置                                               | /                                              |
| serviceName   | String     | 服务名称                                               | myservice                                      |
| servicePort   | int        | 服务端口                                               | 80                                             |
| policy        | JSONObject | 会话保持策略，启用会话保持才会返回                     | 详见示例                                       |
| mode          | String     | 模式，rewrite（改写 cookie）/ insert（植入 cookie）    | insert                                         |
| expire        | int        | cookie 超时时间,1-86400 内的数字，单位为秒, 默认为 30s | 60                                             |
| cookieName    | String     | cookie 名称，改写 cookie 时返回                        |                                                |


## 6.4. 设置负载均衡

> 请求示例

```http
PUT /api/v1/nlb/loadbalancers/mylb HTTP/1.1
Host: open.c.163.com
Authorization: Token 15ddd3f91a45462a8c73026488b02f10
Content-Type: application/json
Namespace: my-ev-admin

{
    "bandwidthLimit": 10, 
    "chargeMode": "bandwidth", 
    "description": "update mylb description"
}

```

```shell
curl -X PUT -H "Authorization: Token 15ddd3f91a45462a8c73026488b02f10" -H "Content-Type: application/json" -H "Namespace: my-ev-admin" -d '{
    "bandwidthLimit": 10, 
    "chargeMode": "bandwidth", 
    "description": "update mylb description"
}
' "https://open.c.163.com/api/v1/nlb/loadbalancers/mylb"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/nlb/loadbalancers/{lb_name}`

### URL Parameters

|      参数      |  类型  | 是否必填 |                              描述                              |         示例值          |
| -------------- | ------ | -------- | -------------------------------------------------------------- | ----------------------- |
| lb_name        | String | 是       | 负载均衡名称（[获取负载均衡列表](../?http#6-2)）               | mylb                    |
| namespace      | String | 是       | 空间名称（[获取空间列表](../?http#9-2)）                       | my-ev-admin             |
| chargeMode     | String | 否       | 计费方式，netflow（流量）/bandwidth（带宽）<br>默认按流量计费  | bandwidth               |
| bandwidthLimit | int    | 否       | 带宽限制，单位 Mbps，取值范围 [1,100]<br>默认为最大值 100 Mbps | 10                      |
| description    | String | 否       | 自定义描述信息                                                 | update mylb description |



## 6.5. 删除负载均衡

删除负载均衡实例不会同时释放 IP，请调用 [删除 IP](../?http#8-6-ip) 接口。

> 请求示例

```http
DELETE /api/v1/nlb/loadbalancers/mylb HTTP/1.1
Host: open.c.163.com
Authorization: Token 15ddd3f91a45462a8c73026488b02f10
Content-Type: application/json
Namespace: my-ev-admin
```

```shell
curl -X DELETE -H "Authorization: Token 15ddd3f91a45462a8c73026488b02f10" -H "Content-Type: application/json" -H "Namespace: my-ev-admin" "https://open.c.163.com/api/v1/nlb/loadbalancers/mylb"
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/nlb/loadbalancers/{lb_name}`

### URL Parameters

|    参数   |  类型  | 是否必填 |                       描述                       |   示例值    |
| --------- | ------ | -------- | ------------------------------------------------ | ----------- |
| lb_name   | String | 是       | 负载均衡名称（[获取负载均衡列表](../?http#6-2)） | mylb        |
| namespace | String | 是       | 空间名称（[获取空间列表](../?http#9-2)）         | my-ev-admin |



## 6.6. 创建监听
为指定负载均衡实例创建一个监听。

<aside class="notice">一次只能创建一个监听</aside>


> 请求示例

```http
POST /api/v1/nlb/loadbalancers/mylb/listeners HTTP/1.1
Host: open.c.163.com
Authorization: Token 15ddd3f91a45462a8c73026488b02f10
Content-Type: application/json
namespace: my-ev-admin

{
    "name": "my-listeners",
    "listenPort": 80,
    "protocol": "http",
    "clusters": [
      {
        "serverName": "163.com",
        "certId":"f271a811-d277-4d23-822e-9b9e71bf05a1"
        "path": "/cloud",
        "serviceName": "test",
        "servicePort": 8080,
        "policy": {
          "mode": "insert",
          "expire": 30000
        }
      }
    ]
}
```

```shell
curl -X POST -H "Authorization: Token 15ddd3f91a45462a8c73026488b02f10" -H "Content-Type: application/json" -H "namespace: my-ev-admin" -d '{
    "name": "my-listeners",
    "listenPort": 80,
    "protocol": "https",
    "clusters": [
      {
        "serverName": "163.com",
        "certId":"f271a811-d277-4d23-822e-9b9e71bf05a1"
        "path": "/cloud",
        "serviceName": "test",
        "servicePort": 8080,
        "policy": {
          "mode": "insert",
          "expire": 30000
        }
      }
    ]
}' "https://open.c.163.com/api/v1/nlb/loadbalancers/mylb/listeners"
```

### HTTP Request

`POST https://open.c.163.com/api/v1/nlb/loadbalancers/{lb_name}/listeners`

### URL Parameters

|    参数    |  类型  | 是否必填 |                                      描述                                      |    示例值    |
| ---------- | ------ | -------- | ------------------------------------------------------------------------------ | ------------ |
| lb_name    | String | 是       | 负载均衡名称（[获取负载均衡列表](../?http#6-2)）                               | mylb         |
| namespace  | String | 是       | 空间名称（[获取空间列表](../?http#9-2)）                                       | my-ev-admin  |
| name       | String | 是       | 监听名称（1-24 位小写字母、数字、或中划线组成，以字母开头，字母或数字结尾）    | my-listeners |
| listenPort | int    | 是       | 监听端口（http：80 或 1025-65535 内的数字；https：443 或 1025-65535 内的数字） | 80           |
| protocol   | String | 是       | 监听协议，http / https                                                         | https        |

| clusters 参数 |    类型    | 是否必填 |                          描述                          |                示例值                |
| ------------- | ---------- | -------- | ------------------------------------------------------ | ------------------------------------ |
| serverName    | String     | 否       | 域名，默认匹配所有域名                                 | 163.com                              |
| certId        | String     | 条件必填 | 证书 id，当选择 protocol:"https" 时必填                | f271a811-d277-4d23-822e-9b9e71bf05a1 |
| path          | String     | 否       | 路径分流功能 URL 配置; 默认为'/',即全部路径转发        | /cloud                               |
| serviceName   | String     | 是       | 服务名                                                 | test                                 |
| servicePort   | int        | 是       | 服务端口                                               | 8080                                 |
| policy        | JSONObject | 条件必填 | 会话保持策略，启用会话保持时必填                       | 详见示例                             |
| mode          | String     | 条件必填 | 模式，rewrite（改写 cookie）/ insert（植入 cookie）    | insert                               |
| expire        | int        | 条件必填 | cookie 超时时间,1-86400 内的数字，单位为秒, 默认为 30s | 30000                                |
| cookieName    | String     | 条件必填 | cookie 名称，当选择改写 cookie 时必填                  |                                      |

具体参数规则详见 [创建负载均衡](http://support.c.163.com/md.html#!容器服务/负载均衡/使用指南/创建负载均衡.md)。



## 6.7. 查看监听
查看指定负载均衡实例的监听信息。

> 请求示例

```http
GET /api/v1/nlb/loadbalancers/mylb/listeners HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
namespace: my-ev-admin
```

```shell
curl -X GET -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -H "namespace: my-ev-admin" "https://open.c.163.com/api/v1/nlb/loadbalancers/mylb/listeners"
```

> 响应示例

```json
[
  {
    "name": "my-listeners",
    "listenPort": 80,
    "protocol": "http",
    "createtime": "2016-12-29T17:06:20.343Z",
    "updatetime": "2016-12-29T17:06:20.343Z",
    "clusters": [
      {
        "serverName": "163.com",
        "path": "/cloud",
        "serviceName": "test",
        "servicePort": 8080,
        "policy": {
          "mode": "insert",
          "expire": 30000
        }
      }
    ]
  },
  {
    "name": "my-listeners-https",
    "listenPort": 443,
    "protocol": "https",
    "createtime": "2017-01-03T10:54:14.421Z",
    "updatetime": "2017-01-03T10:54:14.421Z",
    "clusters": [
      {
        "serverName": "*",
        "certId": "7b0c8ccf-1987-4082-a7a8-f1c18f345f63",
        "path": "/",
        "serviceName": "ubuntu",
        "servicePort": 80
      }
    ]
  }
]
```

### HTTP Request

`GET https://open.c.163.com/api/v1/nlb/loadbalancers/{lb_name}/listeners`

### URL Parameters

|    参数    |  类型  | 是否必填 |                                      描述                                      |    示例值    |
|------------|--------|----------|--------------------------------------------------------------------------------|--------------|
| lb_name    | String | 是       | 负载均衡名称（[获取负载均衡列表](../?http#6-2)）                               | mylb         |
| namespace  | String | 是       | 空间名称（[获取空间列表](../?http#9-2)）                                       | my-ev-admin  |

### Query Parameters

|    参数    |  类型  |                              描述                             |          示例值          |
|------------|--------|---------------------------------------------------------------|--------------------------|
| name       | String | 监听名称                                                      | my-listeners             |
| listenPort | int    | 监听端口                                                      | 80                       |
| protocol   | String | 监听协议                                                      | http                     |
| createtime | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-29T17:06:20.343Z |
| updatetime | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-29T17:06:20.343Z |

| clusters 参数 |    类型    |                           描述                          |                示例值                |
|---------------|------------|---------------------------------------------------------|--------------------------------------|
| serverName    | String     | 域名                                                    | 163.com                              |
| certId        | String     | 证书 id                                                 | 7b0c8ccf-1987-4082-a7a8-f1c18f345f63 |
| path          | String     | URL 配置                                                | /cloud                               |
| serviceName   | String     | 服务名称                                                | test                                 |
| servicePort   | int        | 服务端口                                                | 8080                                 |
| policy        | JSONObject | 会话保持策略，启用会话保持才会返回                      | 详见示例                             |
| mode          | String     | 模式，rewrite（改写 cookie）/ insert（植入 cookie）     | insert                               |
| expire        | int        | cookie 超时时间,1-86400 内的数字，单位为秒, 默认为 30 s | 30000                                |
| cookieName    | String     | cookie 名称，改写 cookie 时返回                         |                                      |


## 6.8. 设置监听

<aside class="notice">该接口为全量接口，只能对单个监听器进行修改，且必须在 body 中指定该监听下的所有 cluster</aside>

> 请求示例

```http
PUT /api/v1/nlb/loadbalancers/mylb/listeners/my-listeners HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
Namespace: my-ev-admin

{
    "clusters": [
      {
        "serverName": "1.com",
        "path": "/1",
        "serviceName": "test",
        "servicePort": 8081,
        "policy": {
          "mode": "insert",
          "expire": 30000
        }
      },
      {
        "serverName": "2.com",
        "path": "/2",
        "serviceName": "test",
        "servicePort": 80,
        "policy": {
          "mode": "rewrite",
          "cookieName": "hello"
        }
      }
    ]
}
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -H "Namespace: my-ev-admin" -d '{
    "clusters": [
      {
        "serverName": "1.com",
        "path": "/1",
        "serviceName": "test",
        "servicePort": 8081,
        "policy": {
          "mode": "insert",
          "expire": 30000
        }
      },
      {
        "serverName": "2.com",
        "path": "/2",
        "serviceName": "test",
        "servicePort": 80,
        "policy": {
          "mode": "rewrite",
          "cookieName": "hello"
        }
      }
    ]
}' "https://open.c.163.com/api/v1/nlb/loadbalancers/mylb/listeners/my-listeners"
```


### HTTP Request

`PUT https://open.c.163.com/api/v1/nlb/loadbalancers/${lb_name}/listeners/${listener_name}`

### URL Parameters

|      参数     |  类型  | 是否必填 |                       描述                       |    示例值    |
|---------------|--------|----------|--------------------------------------------------|--------------|
| lb_name       | String | 是       | 负载均衡名称（[获取负载均衡列表](../?http#6-2)） | mylb         |
| listener_name | String | 是       | 监听名称 （[查看监听](../?http#6-7)）            | my-listeners |
| namespace     | String | 是       | 空间名称（[获取空间列表](../?http#9-2)）         | my-ev-admin  |

| clusters 参数 |    类型    | 是否必填 |                          描述                          |  示例值  |
|---------------|------------|----------|--------------------------------------------------------|----------|
| serverName    | String     | 否       | 域名，默认匹配所有域名                                 | 1.com    |
| certId        | String     | 条件必填 | 证书 id，当选择 protocol:"https" 时必填                |          |
| path          | String     | 否       | 路径分流功能 URL 配置; 默认为'/',即全部路径转发        | /1       |
| serviceName   | String     | 是       | 服务名                                                 | test     |
| servicePort   | int        | 是       | 服务端口                                               | 8081     |
| policy        | JSONObject | 条件必填 | 会话保持策略，启用会话保持时必填                       | 详见示例 |
| mode          | String     | 条件必填 | 模式，rewrite（改写 cookie）/ insert（植入 cookie）    | insert   |
| expire        | int        | 条件必填 | cookie 超时时间,1-86400 内的数字，单位为秒, 默认为 30s | 30000    |
| cookieName    | String     | 条件必填 | cookie 名称，当选择改写 cookie 时必填                  | hello    |



## 6.9. 删除监听


> 请求示例

```http
DELETE /api/v1/nlb/loadbalancers/mylb/listeners/my-listeners HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
Namespace: my-ev-admin
```

```shell
curl -X DELETE -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -H "Namespace: my-ev-admin" "https://open.c.163.com/api/v1/nlb/loadbalancers/mylb/listeners/my-listeners"
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/nlb/loadbalancers/${lb_name}/listeners/${listener_name}`

### URL Parameters

|      参数     |  类型  | 是否必填 |                       描述                       |    示例值    |
|---------------|--------|----------|--------------------------------------------------|--------------|
| lb_name       | String | 是       | 负载均衡名称（[获取负载均衡列表](../?http#6-2)） | mylb         |
| listener_name | String | 是       | 监听名称 （[查看监听](../?http#6-7)）            | my-listeners |
| namespace     | String | 是       | 空间名称（[获取空间列表](../?http#9-2)）         | my-ev-admin  |


## 6.10. 上传证书


> 请求示例

```http
POST /api/v1/nlb/certs HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json

{
  "name" : "my-cer",
  "pubCert":"-----BEGIN CERTIFICATE-----\nMIIDBjCCA```公钥内容```sQaRSmg==\n-----END CERTIFICATE-----",
  "priKey":"-----BEGIN RSA PRIVATE KEY-----\nMIIEpQIBAA```私钥内容```lCybGco=\n-----END RSA PRIVATE KEY-----"
}
```

```shell
curl -X POST -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -H "Cache-Control: no-cache" -H "Postman-Token: c6e8dd0a-56a0-e7a0-5245-1297ec755bff" -d '{
  "name" : "my-cer",
  "pubCert":"-----BEGIN CERTIFICATE-----\nMIIDBjCCA```公钥内容```sQaRSmg==\n-----END CERTIFICATE-----",
  "priKey":"-----BEGIN RSA PRIVATE KEY-----\nMIIEpQIBAA```私钥内容```lCybGco=\n-----END RSA PRIVATE KEY-----"
}' "https://open.c.163.com/api/v1/nlb/certs"
```

> 响应示例

```json
{
  "id": "7b0c8ccf-1987-4082-a7a8-f1c18f345f63",
  "name": "my-cer"
}
```

### HTTP Request

`POST https://open.c.163.com/api/v1/nlb/certs`

### URL Parameters

|   参数  |  类型  | 是否必填 |   描述   |  示例值  |
|---------|--------|----------|----------|----------|
| name    | String | 是       | 证书名称 | my-cer   |
| pubCert | String | 是       | 公钥内容 | 详见示例 |
| priKey  | String | 是       | 私钥内容 | 详见示例 |

### Query Parameters

| 参数 |  类型  |   描述   |                示例值                |
|------|--------|----------|--------------------------------------|
| id   | long   | 证书 id  | 7b0c8ccf-1987-4082-a7a8-f1c18f345f63 |
| name | String | 证书名称 | my-cer                               |


## 6.11. 获取证书列表

> 请求示例

```http
GET /api/v1/nlb/certs HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/nlb/certs"
```

> 响应示例

```json
[
  {
    "id": "7b0c8ccf-1987-4082-a7a8-f1c18f345f63",
    "name": "my-cer",
    "fingerPrint": "E8:68:DA:BA:E4:2D:F0:DD:89:2D:B0:E2:32:73:02:D6:56:7F:0A:C1",
    "createtime": "2017-01-03T09:29:23.658Z",
    "updatetime": "2017-01-03T09:29:23.658Z"
  }
]
```

### HTTP Request

`GET https://open.c.163.com/api/v1/nlb/certs`


### Query Parameters

|     参数    |  类型  |                              描述                             |                           示例值                            |
|-------------|--------|---------------------------------------------------------------|-------------------------------------------------------------|
| id          | long   | 证书 id                                                       | 7b0c8ccf-1987-4082-a7a8-f1c18f345f63                        |
| name        | String | 证书名称                                                      | my-cer                                                      |
| fingerPrint | String | 证书指纹                                                      | E8:68:DA:BA:E4:2D:F0:DD:89:2D:B0:E2:32:73:02:D6:56:7F:0A:C1 |
| createtime  | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2017-01-03T09:29:23.658Z                                    |
| updatetime  | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2017-01-03T09:29:23.658Z                                    |



## 6.12. 修改证书

修改指定证书 id 的名称、私钥和公钥内容。

> 请求示例

```http
PUT /api/v1/nlb/certs/e83d63f1-bf0e-4073-8a11-b54985185f99 HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json

{
  "name" : "my-cer2",
  "pubCert":"-----BEGIN CERTIFICATE-----\nMIIDBjCCA```公钥内容```sQaRSmg==\n-----END CERTIFICATE-----",
  "priKey":"-----BEGIN RSA PRIVATE KEY-----\nMIIEpQIBAA```私钥内容```lCybGco=\n-----END RSA PRIVATE KEY-----"
}
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -d '{
  "name" : "my-cer2",
  "pubCert":"-----BEGIN CERTIFICATE-----\nMIIDBjCCA```公钥内容```sQaRSmg==\n-----END CERTIFICATE-----",
  "priKey":"-----BEGIN RSA PRIVATE KEY-----\nMIIEpQIBAA```私钥内容```lCybGco=\n-----END RSA PRIVATE KEY-----"
}' "https://open.c.163.com/api/v1/nlb/certs/e83d63f1-bf0e-4073-8a11-b54985185f99"
```


### HTTP Request

`PUT https://open.c.163.com/api/v1/nlb/certs/{cert_id}`

### URL Parameters

|   参数  |  类型  | 是否必填 |                   描述                   |                示例值                |
|---------|--------|----------|------------------------------------------|--------------------------------------|
| cert_id | long   | 是       | 证书 id（[获取证书列表](../?http#6-11)） | e83d63f1-bf0e-4073-8a11-b54985185f99 |
| name    | String | 是       | 修改后的证书名称                         | my-cer2                              |
| pubCert | String | 是       | 修改后的公钥内容                         | 详见示例                             |
| priKey  | String | 是       | 修改后的私钥内容                         | 详见示例                             |



## 6.13. 删除证书


> 请求示例

```http
DELETE /api/v1/nlb/certs/e83d63f1-bf0e-4073-8a11-b54985185f99 HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X DELETE -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/nlb/certs/e83d63f1-bf0e-4073-8a11-b54985185f99"
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/nlb/certs/{cert_id}`

### URL Parameters

|   参数  | 类型 | 是否必填 |                   描述                  |                示例值                |
|---------|------|----------|-----------------------------------------|--------------------------------------|
| cert_id | long | 是       | 证书 id [获取证书列表](../?http#6-11)） | e83d63f1-bf0e-4073-8a11-b54985185f99 |




# 7. 云硬盘

## 7.1. 创建云硬盘

> 请求示例

```http
POST /api/v1/cloud-volumes HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type: application/json

{
  "volume_name": "myvolume",
  "size": 10
}
```

```shell
curl -X POST -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" -d '{
    "size": 10, 
    "volume_name": "myvolume"
}' "https://open.c.163.com/api/v1/cloud-volumes"
```


> 响应示例

```json
{
  "id": 19893
}
```



### HTTP Request

`POST https://open.c.163.com/api/v1/cloud-volumes`

### URL Parameters

|     参数    |  类型  | 是否必填 |                                           描述                                          |  示例值  |
|-------------|--------|----------|-----------------------------------------------------------------------------------------|----------|
| volume_name | String | 是       |  云硬盘名称（1~32 位小写字母、数字或中划线组成，并且以字母开头，以数字或字母结尾） | myvolume |
| size        | int    | 是       | 云硬盘大小，单位为 G（大于等于 10 小于 1000，且必需是 10 的整数倍）             | 10       |



### Query Parameters

| 参数 | 类型 |    描述   | 示例值 |
|------|------|-----------|--------|
| id   | long | 云硬盘 id |  19893 |


## 7.2. 获取云硬盘列表

> 请求示例

```http
GET /api/v1/cloud-volumes?limit=10&offset=0 HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/cloud-volumes?limit=10&offset=0"
```


> 响应示例

```json
{
    "total": 2, 
    "volumes": [
        {
            "created_at": "2016-12-23T08:05:22Z", 
            "file_system_type": "Ext4", 
            "from_snapshot_id": 0, 
            "id": 19894, 
            "name": "myvolume2", 
            "service_name": "myspace/myservice", 
            "size": 10, 
            "status": "mount_succ", 
            "upated_at": "2016-12-23T08:17:21Z", 
            "volume_mappings": [
                {
                    "mount_path": "/data/", 
                    "service_container_id": 49927
                }
            ], 
            "volume_uuid": "3df611c0-cfda-4c0c-aeda-304ef3a49d8f"
        }, 
        {
            "created_at": "2016-12-23T07:52:08Z", 
            "file_system_type": "Ext4", 
            "from_snapshot_id": 0, 
            "id": 19893, 
            "name": "myvolume", 
            "service_name": "", 
            "size": 10, 
            "status": "create_succ", 
            "upated_at": "2016-12-23T07:52:08Z", 
            "volume_mappings": [], 
            "volume_uuid": "2cf3face-adc8-41af-992c-78415d2fcd96"
        }
    ]
}
```



### HTTP Request

`GET https://open.c.163.com/api/v1/cloud-volumes?limit={limit}&offset={offset}`

### URL Parameters


|  参数  | 类型 | 是否必填 |                  描述                  | 示例值 |
| ------ | ---- | -------- | -------------------------------------- | ------ |
| limit  | int  | 否       | 每次获取的云硬盘数量，取值范围 (0,20]  |     20 |
| offset | int  | 否       | 获取云硬盘列表起始位置，[0,+∞)，默认 0 |      0 |


### Query Parameters

|         参数         |  类型  |                              描述                             |                示例值                |
|----------------------|--------|---------------------------------------------------------------|--------------------------------------|
| id                   | long   | 云硬盘 id                                                     | 19894                                |
| name                 | String | 云硬盘名称                                                    | myvolume2                            |
| size                 | int    | 云硬盘大小，单位为 G                                          | 10                                   |
| status               | String | 云硬盘当前状态                                                | mount_succ                           |
| volume_uuid          | String | 云硬盘 uuid                                                   | 3df611c0-cfda-4c0c-aeda-304ef3a49d8f |
| service_name         | String | 云硬盘所属空间和服务名称                                      | myspace/myservice                    |
| file_system_type     | String | 云硬盘格式类型                                                | Ext4                                 |
| created_at           | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-23T08:05:22Z                 |
| updated_at           | String | 修改时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-23T08:05:22Z                 |
| from_snapshot_id     | long   | 云硬盘来源的快照 id，0 表示不是由快照创建                     | 0                                    |
| volume_mappings      | List   | 云硬盘的挂载信息列表                                          | 详见示例                             |
| service_container_id | long   | 云硬盘所属容器 id                                             | 49927                                |
| mount_path           | String | 云硬盘挂载路径                                                | /data/                               |


## 7.3. 扩容云硬盘

通过 [获取云硬盘列表](../?http#7-2) 获取的云硬盘 id 扩容该云硬盘。

> 请求示例

```http
PUT /api/v1/cloud-volumes/19893/actions/resize?size=1000 HTTP/1.1
Host: open.c.163.com
Authorization: Token c98dfae9c6cd405f95da15219e908643
Content-Type: application/json
```


```shell
curl -X PUT -H "Authorization: Token c98dfae9c6cd405f95da15219e908643" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/cloud-volumes/19893/actions/resize?size=1000"
```


### HTTP Request

`PUT https://open.c.163.com/api/v1/cloud-volumes/{id}/actions/resize?size={size}`

### URL Parameters

| 参数 | 类型 |               是否必填               |                               描述                              |
|------|------|--------------------------------------|-----------------------------------------------------------------|
| id   | long | 是（[获取云硬盘列表](../?http#7-2)） | 云硬盘 id                                                       |
| size | int  | 是                                   | 扩容大小，单位为 G（大于原容量小于 1000，且必需是 10 的整数倍） |


## 7.4. 删除云硬盘

> 请求示例

```http
DELETE /api/v1/cloud-volumes/19893 HTTP/1.1
Host: open.c.163.com
Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30
Content-Type: application/json
```

```shell
curl -X DELETE -H "Authorization: Token 48e6b1bdb5fb4a28a680a977dffb3c30" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/cloud-volumes/19893"
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/cloud-volumes/{id}`

### URL Parameters

| 参数 | 类型 |               是否必填               |    描述   | 示例值 |
|------|------|--------------------------------------|-----------|--------|
| id   | long | 是（[获取云硬盘列表](../?http#7-2)） | 云硬盘 id |  19893 |


# 8. IP 管理

## 8.0. IP 状态及类型

|  IP 状态  |  描述  |        备注        |
|-----------|--------|--------------------|
| available | 可用   | 在该状态下可被释放 |
| disabled  | 禁用   | 网络服务不可用     |
| binding   | 绑定中 |                    |
| binded    | 已绑定 |                    |

| IP 类型 |   描述   |
|---------|----------|
| nce     | 服务     |
| nlb     | 负载均衡 |


## 8.1. 创建 IP

> 请求示例

```http
POST /api/v1/ips HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json

{
    "nce": 1, 
    "nlb": 1
}
```

```shell
curl -X POST -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" -d '{
    "nce": 1, 
    "nlb": 1
}' "https://open.c.163.com/api/v1/ips"
```

> 响应示例

```json
{
  "total": 2,
  "ips": [
    {
      "id": "896",
      "ip": "59.111.109.202",
      "status": "available",
      "type": "nlb",
      "service_id": "",
      "service_name": "",
      "create_at": "2016-12-28T13:13:11Z",
      "update_at": "2016-12-28T13:13:11Z"
    },
    {
      "id": "7f7a9f12-035f-401b-ace0-7926b4a518b4",
      "ip": "59.111.120.200",
      "status": "available",
      "type": "nce",
      "service_id": "",
      "service_name": "",
      "create_at": "2016-12-28T13:13:12Z",
      "update_at": "2016-12-28T13:13:12Z"
    }
  ]
}
```


### HTTP Request

`POST https://open.c.163.com/api/v1/ips`

### URL Parameters

| 参数 |  类型  | 是否必填 | 默认值 |              描述              | 示例值 |
|------|--------|----------|--------|--------------------------------|--------|
| nlb  | Number | 否       |      0 | 创建指定数量的负载均衡 IP 实例 |      1 |
| nce  | Number | 否       |      0 | 创建指定数量的服务 IP 实例     |      1 |

### Query Parameters  


|     参数     |    类型   |                              描述                             |        示例值        |
|--------------|-----------|---------------------------------------------------------------|----------------------|
| total        | Number    | 创建成功的 IP 实例数                                          | 2                    |
| ips          | JSONArray | 返回的 IP 列表                                                | 详见示例             |
| id           | String    | IP 的唯一标识符                                               | 896                  |
| ip           | String    | IP                                                            | 59.111.109.202       |
| status       | String    | IP 状态（[IP 状态及类型](../?http#8-0-ip)）                     | available            |
| type         | String    | IP 类型（[IP 状态及类型](../?http#8-0-ip)）                     | nlb                  |
| service_id   | String    | 绑定的实例 id                                                 |                      |
| service_name | String    | 绑定的实例名称                                                |                      |
| create_at    | String    | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:12Z |
| update_at    | String    | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:12Z |

## 8.2. 获取 IP 列表

获取 IP 列表，并查看所有 IP 实例的信息。

> 请求示例

```http
GET /api/v1/ips?status=available&type=nlb&offset=0&limit=20 HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/ips?status=available&type=nlb&offset=0&limit=20"
```

> 响应示例

```json
{
  "total": 2,
  "ips": [
    {
      "id": "896",
      "ip": "59.111.109.202",
      "status": "available",
      "type": "nlb",
      "service_id": "",
      "service_name": "",
      "create_at": "2016-12-28T13:13:11Z",
      "update_at": "2016-12-28T13:13:11Z"
    },
    {
      "id": "926",
      "ip": "59.111.109.232",
      "status": "available",
      "type": "nlb",
      "service_id": "",
      "service_name": "",
      "create_at": "2016-12-28T13:13:03Z",
      "update_at": "2016-12-28T13:13:03Z"
    }
  ]
}
```

### HTTP Request

`GET https://open.c.163.com/api/v1/ips?status={status}&type={type}}&offset={offset}&limit={limit}`

### URL Parameters

|  参数  |  类型  | 是否必填 |  默认值  |                    描述                    |  示例值   |
|--------|--------|----------|----------|--------------------------------------------|-----------|
| status | String | 否       | 所有状态 | IP 状态（[IP 状态及类型](/?http#8-0-ip)）  | available |
| type   | String | 否       | 所有类型 | IP 类型 （[IP 状态及类型](/?http#8-0-ip)） | nlb       |
| offset | Number | 否       | 0        | 获取列表的起始位置                         | 0         |
| limit  | Number | 否       | 20       | 每次获取的 IP 数量                         | 20        |


### Query Parameters

|     参数     |    类型   |                              描述                             |        示例值        |
|--------------|-----------|---------------------------------------------------------------|----------------------|
| total        | Number    | IP 总数                                                       | 详见示例             |
| ips          | JSONArray | 返回的 IP 列表                                                | 详见示例             |
| id           | String    | IP 的唯一标识符                                               | 896                  |
| ip           | String    | IP                                                            | 59.111.109.202       |
| status       | String    | IP 状态                                                       | available            |
| type         | String    | IP 类型                                                       | nlb                  |
| service_id   | String    | 绑定的实例 id                                                 |                      |
| service_name | String    | 绑定的实例名称                                                |                      |
| create_at    | String    | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:11Z |
| update_at    | String    | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:11Z |


## 8.3. 查看 IP 详情

查看某个 IP 实例的详情信息。

> 请求示例

```http
GET /api/v1/ips/163 HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/ips/163"
```

> 响应示例

```json
{
  "id": "163",
  "ip": "59.111.163.163",
  "status": "binded",
  "type": "nlb",
  "service_id": "e2cc21da-9bb9-4db8-ae54-10b358a3a7aa",
  "service_name": "mysql",
  "create_at": "2016-12-15T09:33:53Z",
  "update_at": "2016-12-15T09:33:53Z"
}
```
### HTTP Request

`GET https://open.c.163.com/api/v1/ips/{id}`

### URL Parameters

| 参数 |  类型  | 是否必填 |                       描述                       | 示例值 |
|------|--------|----------|--------------------------------------------------|--------|
| id   | String | 是       | IP 的唯一标识符（[获取 IP 列表](/?http#8-2-ip)） |    163 |

### Query Parameters

|     参数     |  类型  |                              描述                             |        示例值        |
|--------------|--------|---------------------------------------------------------------|----------------------|
| id           | String | IP 的唯一标识符                                               | 163                  |
| ip           | String | IP                                                            | 59.111.163.163       |
| status       | String | IP 状态                                                       | binded               |
| type         | String | IP 类型                                                       | nlb                  |
| service_id   | String | 绑定的实例 id                                                 | binded               |
| service_name | String | 绑定的实例名称                                                | mysql                |
| create_at    | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:11Z |
| update_at    | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:11Z |


## 8.4. 查看 IP 配额

> 请求示例

```http
GET /api/v1/ips/quota HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/ips/quota"
```

> 响应示例

```json
{
  "total": 20,
  "used": 5,
  "quotas": [
    {
      "type": "nce",
      "quota": 10,
      "used": 2
    },
    {
      "type": "nlb",
      "quota": 10,
      "used": 3
    }
  ]
}
```

### HTTP Request

`GET https://open.c.163.com/api/v1/ips/quota`

### Query Parameters

|  参数  |    类型   |        描述        |  示例值  |
|--------|-----------|--------------------|----------|
| total  | Number    | 总配额             | 20       |
| used   | Number    | 已使用配额         | 5        |
| quotas | JSONArray | 不同类型的配额详情 | 详见示例 |
| type   | String    | IP 类型            | nce      |
| quota  | String    | 对应类型的配额     | 10       |

## 8.5. 查看 IP 费用
查看某 IP 实例创建至今产生的所有费用。

> 请求示例

```http
GET /api/v1/ips/163/cost HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/ips/163/cost"
```

> 响应示例

```json
{
  "id": "163",
  "cost": 1.26
}
```

### HTTP Request

`GET https://open.c.163.com/api/v1/ips/{id}/cost`

### URL Parameters

| 参数 |  类型  | 是否必填 |                       描述                       | 示例值 |
|------|--------|----------|--------------------------------------------------|--------|
| id   | String | 是       | IP 的唯一标识符（[获取 IP 列表](/?http#8-2-ip)） |    163 |

### Query Parameters

| 参数 |  类型  |       描述      | 示例值 |
|------|--------|-----------------|--------|
| id   | String | IP 的唯一标识符 |    163 |
| cost | Number | 费用，单位：元  |   1.26 |

## 8.6. 删除 IP

删除某个 IP 实例。

> 请求示例

```http
DELETE /api/v1/ips/163 HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X DELETE -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/ips/163"
```

> 响应示例

```json
{
  "id": "163",
  "ip": "59.111.163.163",
  "status": "deleted",
  "type": "nlb",
  "service_id": "",
  "service_name": "",
  "create_at": "2016-12-28T13:13:12Z",
  "update_at": "2016-12-28T13:13:12Z"
}
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/ips/{id}`

### URL Parameters

| 参数 |  类型  | 是否必填 |                       描述                       | 示例值 |
|------|--------|----------|--------------------------------------------------|--------|
| id   | String | 是       | IP 的唯一标识符（[获取 IP 列表](/?http#8-2-ip)） |    163 |

### Query Parameters

|     参数     |  类型  |                              描述                             |        示例值        |
|--------------|--------|---------------------------------------------------------------|----------------------|
| id           | String | IP 的唯一标识符                                               | 163                  |
| ip           | String | IP                                                            | 59.111.163.163       |
| status       | String | IP 状态                                                       | deleted              |
| type         | String | IP 类型                                                       | nlb                  |
| service_id   | String | 绑定的实例 id                                                 |                      |
| service_name | String | 绑定的实例名称                                                |                      |
| create_at    | String | 创建时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:12Z |
| update_at    | String | 更新时间，使用 UTC（世界标准时间）时间，用 ISO8601 进行格式化 | 2016-12-28T13:13:12Z |





# 9. 服务管理

## 9.0. 规格参数

| id |    名称    | 单价（元/小时） | CPU（核） |  内存  | 硬盘（GB） | 硬盘类型 |
|----|------------|-----------------|-----------|--------|------------|----------|
|  1 | 微小型     |           0.049 |         1 | 64 0MB |         20 | SSD      |
|  2 | 小型       |            0.06 |         1 | 1 GB   |         20 | SSD      |
|  3 | 中型       |            0.19 |         2 | 2 GB   |         20 | SSD      |
|  4 | 大型       |            0.25 |         2 | 4 GB   |         20 | SSD      |
|  5 | 豪华型     |            0.52 |         4 | 8 GB   |         20 | SSD      |
|  6 | 旗舰型     |            1.02 |         8 | 16 GB  |         20 | SSD      |
|  7 | 超级旗舰型 |            3.32 |        16 | 64 GB  |         20 | SSD      |



## 9.1. 创建空间

> 请求示例

```http
POST /api/v1/namespaces HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json

{
  "name": "my-ev"
}
```

```shell
curl -X POST -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" -d '{
  "name": "my-ev"
}' "https://open.c.163.com/api/v1/namespaces"
```

> 响应示例

```json
{
  "namespace_Id": 51616
}
```

### HTTP Request

`POST https://open.c.163.com/api/v1/namespaces`

### URL Parameters

| 参数 |  类型  | 是否必填 |   描述   | 示例值 |
|------|--------|----------|----------|--------|
| name | String | 是       | 空间名称 | my-ev  |

### Query Parameters

|     参数     | 类型 |   描述  | 示例值 |
|--------------|------|---------|--------|
| namespace_id | long | 空间 id |  51616 |


## 9.2. 获取空间列表

> 请求示例

```http
GET /api/v1/namespaces HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/namespaces"
```

> 响应示例

```json
{
    "namespaces": [
        {
            "deletable": true,
            "id": 51616,
            "display_name": "my-ev",
            "namespace": "my-ev-admin"
        },
        {
            "deletable": false,
            "id": 10685,
            "display_name": "default",
            "namespace": "default-sgx52"
        }
    ]
}
```

### HTTP Request

`GET https://open.c.163.com/api/v1/namespaces`

### Query Parameters

|     参数     |   类型  |                描述                |   示例值    |
|--------------|---------|------------------------------------|-------------|
| deletable    | boolean | 是否可删除（有服务的空间无法删除） | true        |
| id           | long    | 空间 id                            | 51616       |
| display_name | String  | 空间显示名称                       | my-ev       |
| namespace    | String  | 空间名称                           | my-ev-admin |

## 9.3. 删除空间

> 请求示例

```http
DELETE /api/v1/namespaces/51614 HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X DELETE -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/namespaces/51614"
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/namespaces/{id}`

### URL Parameters

| 参数 | 类型 | 是否必填 |                   描述                  | 示例值 |
|------|------|----------|-----------------------------------------|--------|
| id   | long | 是       | 空间 id（[获取空间列表](../?http#9-2)） |  51614 |

## 9.4. 获取镜像列表

获取可用的镜像列表，用于创建服务。

> 请求示例

```http
GET /api/v1/microservices/images HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/microservices/images"
```

> 响应示例

```json
{
    "custom_images": [
        {
            "tag": "v1",
            "repo_id": 44627,
            "repo_name": "mysql",
            "image_path": "hub.c.163.com/admin/mysql:v1"
        },
        {
            "tag": "master20161215041423615",
            "repo_id": 44627,
            "repo_name": "mysql",
            "image_path": "hub.c.163.com/admin/mysql:master20161215041423615"
        },
        ......
    ],
    "public_images": [
        {
            "tag": "6.5",
            "repo_id": 1055,
            "repo_name": "centos",
            "image_path": "hub.c.163.com/public/centos:6.5"
        },
        {
            "tag": "7.2.1511",
            "repo_id": 1055,
            "repo_name": "centos",
            "image_path": "hub.c.163.com/public/centos:7.2.1511"
        },
        ......
    ]
}
```

### HTTP Request

`GET https://open.c.163.com/api/v1/microservices/images`

### Query Parameters

|      参数     |  类型  |         描述         |            示例值            |
|---------------|--------|----------------------|------------------------------|
| custom_images | List   | 私有镜像和收藏的镜像 | 详见示例                     |
| tag           | String | 镜像标签             | v1                           |
| repo_id       | long   | 镜像仓库 id          | 44627                        |
| repo_name     | String | 镜像仓库名称         | mysql                        |
| image_path    | String | 镜像地址             | hub.c.163.com/admin/mysql:v1 |

## 9.5. 创建服务

<aside class="warning">请注意参数之间的逻辑关系，如无状态服务不需要绑定公网。</aside>

> 请求示例

```http
POST /api/v1/microservices HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json

{
    "bill_info":"default",
        "service_info": {
        "namespace_id": "51616",
        "stateful": "1",
        "replicas": 1,
        "service_name": "ubuntu",
        "port_maps": [
            {
                "dest_port": "80",
                "source_port": "8080",
                "protocol": "TCP"
            }
        ],
        "spec_id": 1,
        "state_public_net": {
            "used": true,
            "type": "flow",
            "bandwidth": 20
        },
        "disk_type": 0,
        "ip_id": "cef9069c-d97d-42b1-a86c-f815f748820c"
    },
    "service_container_infos": [
        {
            "image_path": "hub.c.163.com/public/ubuntu:14.04",
            "container_name": "container002",
            "command": "",
            "envs": [
                {
                    "key": "password",
                    "value": "password"
                },
                {
                    "key": "user",
                    "value": "user"
                }
            ],
            "log_dirs": [
                "/var/log/"
            ],
            "cpu_weight": 100,
            "memory_weight": 100,
            "ssh_keys": [
                "sshkey1","sshkey2"
            ],
            "local_disk_info": [],
            "volume_info": {
                "163": "/mnt/"
            }
        }
    ]
}
```

```shell
curl -X POST -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -d '{
    "bill_info":"default",
        "service_info": {
        "namespace_id": "51616",
        "stateful": "1",
        "replicas": 1,
        "service_name": "ubuntu",
        "port_maps": [
            {
                "dest_port": "80",
                "source_port": "8080",
                "protocol": "TCP"
            }
        ],
        "spec_id": 1,
        "state_public_net": {
            "used": true,
            "type": "flow",
            "bandwidth": 20
        },
        "disk_type": 0,
        "ip_id": "cef9069c-d97d-42b1-a86c-f815f748820c"
    },
    "service_container_infos": [
        {
            "image_path": "hub.c.163.com/public/ubuntu:14.04",
            "container_name": "container002",
            "command": "",
            "envs": [
                {
                    "key": "password",
                    "value": "password"
                },
                {
                    "key": "user",
                    "value": "user"
                }
            ],
            "log_dirs": [
                "/var/log/"
            ],
            "cpu_weight": 100,
            "memory_weight": 100,
            "ssh_keys": [
                "sshkey1","sshkey2"
            ],
            "local_disk_info": [],
            "volume_info": {
                "163": "/mnt/"
            }
        }
    ]
}' "https://open.c.163.com/api/v1/microservices"
```

> 响应示例

```json
{
  "service_id": "51572"
}
```

### HTTP Request

`POST https://open.c.163.com/api/v1/microservices`

### URL Parameters

|             参数            |       类型       | 是否必填 |                                                                描述                                                                |                示例值                |
|-----------------------------|------------------|----------|------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| bill_info                   | String           | 是       | 默认 default，未来接收订单号                                                                                                       | default                              |
| **service_info**            | jsonObject       | 是       | 服务相关元信息                                                                                                                     | 详见示例                             |
| namespace_id                | long             | 是       | 空间 id（[获取空间列表](../?http#9-2)）                                                                                            | 51616                                |
| stateful                    | int              | 是       | 服务状态，0（无状态）/1（有状态）                                                                                                  | 1                                    |
| replicas                    | int              | 是       | 副本数（有状态服务默认为 1）                                                                                                       | 1                                    |
| service_name                | String           | 是       | 服务名称                                                                                                                           | ubuntu                               |
| port_maps                   | List             | 否       | 端口映射信息                                                                                                                       | 详见示例                             |
| - dest_port                 | int              | 否       | 服务端口                                                                                                                           | 80                                   |
| - source_port               | int              | 否       | 容器端口                                                                                                                           | 8080                                 |
| - protocol                  | String           | 否       | 协议                                                                                                                               | TCP                                  |
| spec_id                     | long             | 是       | 规格 id（[规格参数](../?http#9-0)）                                                                                                | 1                                    |
| state_public_net            |                  | 否       | 公网信息（无状态容器无需填写）                                                                                                     | 详见示例                             |
| - used                      | boolean          | 否       | 是否使用公网                                                                                                                       | true                                 |
| - type                      | String           | 否       | 类型，flow（流量）/ bandwidth（带宽）                                                                                              | flow                                 |
| - bandwidth                 | int              | 否       | 带宽（单位 Mbps）                                                                                                                  | 20                                   |
| disk_type                   | int              | 是       | 数据盘类型，0（云硬盘）/1（本地盘）/2（不挂盘）/3（混合使用）                                                                      | 0                                    |
| ip_id                       | Stirng           | 否       | IP 的 uuid（[获取 IP 列表](../?http#8-2-ip)）                                                                                      | cef9069c-d97d-42b1-a86c-f815f748820c |
| **service_container_infos** | jsonArray        | 是       | 容器相关元信息                                                                                                                     | 详见示例                             |
| image_path                  | String           | 是       | 镜像地址（[获取镜像列表](../?http#9-5)）                                                                                           | hub.c.163.com/public/ubuntu:14.04    |
| container_name              | String           | 是       | 容器名称                                                                                                                           | container002                         |
| command                     | String           | 否       | 自定义启动命令（[如何自定义服务启动命令](http://support.c.163.com/md.html#!容器服务/服务管理/使用指南/如何自定义服务启动命令.md)） |                                      |
| envs                        | List             | 否       | 环境变量                                                                                                                           |                                      |
| - key                       | String           | 否       | 环境变量 key                                                                                                                       | password                             |
| - value                     | String           | 否       | 环境变量 value                                                                                                                     | password                             |
| log_dirs                    | List             | 否       | 日志服务采集日志的目录                                                                                                             | ["/var/log/"]                        |
| cpu_weight                  | int              | 是       | cpu 占比（1-99，总和为100，单容器下必须为 100）                                                                                    | 100                                  |
| memory_weight               | int              | 是       | 内存占比（1-99，总和为100，单容器下必须为 100）                                                                                    | 100                                  |
| ssh_keys                    | List             | 否       | 需要注入的 ssh 密钥名称（[获取密钥列表](../?http#4-2)）                                                                            | ["sshkey1","sshkey2"]                |
| volume_info                 | Map<Long,String> | 是       | 云硬盘挂载信息{云盘id:挂载路径}（有状态容器必填，无状态容器为空）（[获取云硬盘列表](../?http#7-2)）                                | {"163": "/mnt/"}                     |
| local_disk_info             | jsonArray        | 否       | 本地盘挂载信息（有状态容器使用本地盘必填；无状态容器为空）                                                                         |                                      |
| - name                      | String           | 否       | 本地盘名称                                                                                                                         |                                      |
| - path                      | String           | 否       | 本地盘挂载路径                                                                                                                     |                                      |
| - size                      | int              | 否       | 本地盘大小（单位 GB）                                                                                                              |                                      |


### Query Parameters

|    参数    | 类型 |   描述  | 示例值 |
|------------|------|---------|--------|
| service_id | long | 服务 id |  51572 |



## 9.6. 获取服务列表

获取某个空间内的服务列表。


> 请求示例

```http
GET /api/v1/namespaces/51614/microservices?offset=0&limit=10 HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/namespaces/51190/microservices?offset=0&limit=10"
```

> 响应示例

```json
{
    "total": 1,
    "microservice_infos": [
        {
            "id": 50511,
            "status": "restart_succ",
            "replicas": 2,
            "service_name": "mysql",
            "stateful": 1,
            "port_maps": [
                {
                    "protocol": "TCP",
                    "source_port": 80,
                    "dest_port": 80
                }
            ],
            "spec_id": 3,
            "stop_service": 0
        }
    ]
}
```

### HTTP Request

`GET https://open.c.163.com/api/v1/namespaces/{id}/microservices?offset={offset}&limit={limit}`

### URL Parameters

|  参数  | 类型 | 是否必填 |                   描述                  | 示例值 |
| ------ | ---- | -------- | --------------------------------------- | ------ |
| id     | long | 是       | 空间 id（[获取空间列表](../?http#9-2)） |  51614 |
| offset | int  | 否       | 获取服务列表起始位置，取值范围 [0,+∞)   |      0 |
| limit  | int  | 否       | 每次获取的服务数量，取值范围 (0,+∞)     |     10 |


### Query Parameters

|     参数     |   类型   |                描述               |  示例值  |
|--------------|----------|-----------------------------------|----------|
| total        | 服务总数 | 该空间内服务总数                  | 1        |
| id           | long     | 服务 id                           | 50511    |
| status       | String   | 服务名称                          | mysql    |
| replicas     | int      | 副本数                            | 2        |
| stateful     | int      | 服务状态，0（无状态）/1（有状态） | 1        |
| port_maps    | List     | 端口映射信息                      | 详见示例 |
| protocol     | String   | 协议                              | TCP      |
| source_port  | int      | 容器端口                          | 80       |
| dest_port    | int      | 服务端口                          | 80       |
| spec_id      | long     | 规格 id                           | 3        |
| stop_service | int      | 停服标识                          | 0        |



## 9.7. 查看服务详情

> 请求示例

```http
GET /api/v1/microservices/51572 HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X GET -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/microservices/51572"
```

> 响应示例

```json
{
  "service_info": {
    "id": 51572,
    "namespace": "my-ev-admin",
    "replicas": 1,
    "status": "create_succ",
    "service_name": "ubuntu",
    "spec_info": "{\"cpu\":1000,\"diskType\":\"SSD\",\"id\":1,\"memory\":671088640,\"name\":\"微小型\",\"price\":0.049,\"storage\":20}",
    "port_maps": "[{\"destPort\":80,\"protocol\":\"TCP\",\"sourcePort\":8080}]",
    "stateful": 1,
    "state_public_net": "{\"bandwidth\":20,\"type\":\"flow\",\"used\":true}",
    "extends_disk_size": 0,
    "pub_ip": "59.111.163.163",
    "stop_service": 0
  },
  "service_container_infos": [
    {
      "envs": [
        {
          "key": "password",
          "value": "password"
        },
        {
          "value": "user",
          "key": "user"
        }
      ],
      "command": null,
      "volumes": [
        {
          "name": "volume",
          "mount_path": "/mnt/"
        }
      ],
      "container_id": 51577,
      "container_name": "container002",
      "ssh_keys": [
        "sshkey1",
        "sshkey2"
      ],
      "log_dirs": [
        "/var/log/"
      ],
      "image_path": "hub.c.163.com/public/ubuntu:14.04",
      "repo_name": "ubuntu",
      "image_tag": "14.04",
      "local_disk_info": [],
      "cpu_weight": 100,
      "memory_weight": 100,
      "docker_container_ids": [
        "79e666ac71b70e446eb32a7eecc92830d937a4518ebb871456eae5fd8fcf5627"
      ]
    }
  ]
}
```


### HTTP Request

`GET https://open.c.163.com/api/v1/microservices/{id}`



### URL Parameters

| 参数 | 类型 | 是否必填 |                                  描述                                 | 示例值 |
|------|------|----------|-----------------------------------------------------------------------|--------|
| {id} | long | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） |  51572 |

### Query Parameters

|             参数            |    类型    |                描述               |                                                        示例值                                                        |
|-----------------------------|------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **service_info**            | jsonObject | 服务相关元信息                    | 详见示例                                                                                                             |
| id                          | long       | 空间 id                           | 51616                                                                                                                |
| namespace                   | String     | 空间名称                          | my-ev-admin                                                                                                          |
| replicas                    | int        | 副本数（有状态服务默认为 1）      | 1                                                                                                                    |
| status                      | String     | 服务当前状态                      | create_succ                                                                                                          |
| service_name                | String     | 服务名称                          | ubuntu                                                                                                               |
| spec_info                   | Map        | 规格信息                          | {\"cpu\":1000,\"diskType\":\"SSD\",\"id\":1,\"memory\":671088640,\"name\":\"微小型\",\"price\":0.049,\"storage\":20} |
| port_maps                   | List       | 端口映射信息                      | [{\"destPort\":80,\"protocol\":\"TCP\",\"sourcePort\":8080}]                                                         |
| stateful                    | int        | 服务状态，0（无状态）/1（有状态） | 1                                                                                                                    |
| state_public_net            | Map        | 公网信息                          | {\"bandwidth\":20,\"type\":\"flow\",\"used\":true}                                                                   |
| extends_disk_size           | long       | 扩展盘大小                        | 0                                                                                                                    |
| pub_ip                      | String     | 公网 IP                           | 59.111.163.163                                                                                                       |
| stop_service                | int        | 停服状态，0（停服）/1（未停服）   | 0                                                                                                                    |
| **service_container_infos** | jsonArray  | 容器相关元信息                    | 详见示例                                                                                                             |
| envs                        | List       | 环境变量，key-value 的形式         | 详见示例                                                                                                             |
| command                     | String     | 自定义启动命令                    | null                                                                                                                 |
| volumes                     | List       | 云硬盘信息                        | { "name": "volume","mount_path": "/mnt/"}                                                                            |
| container_id                | long       | 容器 id                           | 51577                                                                                                                |
| container_name              | String     | 容器名称                          | container002                                                                                                         |
| ssh_keys                    | List       | ssh 密钥名称                      | ["sshkey1","sshkey2"]                                                                                                |
| log_dirs                    | List       | 日志服务采集日志的目录            | ["/var/log/"]                                                                                                        |
| image_path                  | String     | 镜像地址                          | hub.c.163.com/public/ubuntu:14.04                                                                                    |
| repo_name                   | String     | 镜像仓库名称                      | ubuntu                                                                                                               |
| image_tag                   | String     | 镜像 tag                          | 14.04                                                                                                                |
| local_disk_info             | List       | 本地盘挂载信息                    |                                                                                                                      |
| cpu_weight                  | int        | cpu 占比                          | 100                                                                                                                  |
| memory_weight               | int        | 内存占比                          | 100                                                                                                                  |
| docker_container_ids        | List       | 容器运行时 id 列表                | ["79e666ac71b70e446eb32a7eecc92830d937a4518ebb871456eae5fd8fcf5627"]                                                 |
|

## 9.8. 设置有状态服务

<aside class="notice">目前在设置中支持修改公网信息，但无法选择关闭或打开公网。</aside> 

> 请求示例

```http
PUT /api/v1/microservices/51572/actions/update-stateful HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json

{
    "state_public_net": {
        "bandwidth": 50, 
        "type": "flow", 
        "used": true
    }
}
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -d '{
    "state_public_net": {
        "bandwidth": 50, 
        "type": "flow", 
        "used": true
    }
}' "https://open.c.163.com/api/v1/microservices/51572/actions/update-stateful"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/microservices/{id}/actions/update-stateful`

### URL Parameters

|       参数       |   类型  | 是否必填 |                                  描述                                 |  示例值  |
|------------------|---------|----------|-----------------------------------------------------------------------|----------|
| id               | long    | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） | 51572    |
| state_public_net | json    | 是       | 公网信息                                                              | 详见示例 |
| bandwidth        | String  | 是       | 带宽（单位 Mbps）                                                     | 50       |
| type             | int     | 是       | 类型，flow（流量）/ bandwidth（带宽）                                 | flow     |
| used             | boolean | 是       | 是否使用（保持原状态，无法修改）                                      | true     |



## 9.9. 设置无状态服务

<aside class="notice">目前在设置中支持修改环境变量和日志目录。</aside> 

> 请求示例

```http
PUT /api/v1/microservices/50883/actions/update-stateless HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json

{
    "container_infos": [
        {
            "container_id": 50883, 
             "envs": [
                {
                    "key": "password",
                    "value": "password"
                },
                {
                    "key": "user",
                    "value": "user"
                }
            ],
            "log_dirs": [
                "/var/log/", 
                "/var/logs/"
            ], 
            "cpu_weight": 100, 
            "memory_weight": 100
        }
    ]
}
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -d '{
    "container_infos": [
        {
            "container_id": 50883, 
             "envs": [
                {
                    "key": "password",
                    "value": "password"
                },
                {
                    "key": "user",
                    "value": "user"
                }
            ],
            "log_dirs": [
                "/var/log/", 
                "/var/logs/"
            ], 
            "cpu_weight": 100, 
            "memory_weight": 100
        }
    ]
}' "https://open.c.163.com/api/v1/microservices/50883/actions/update-stateless"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/microservices/{id}/actions/update-stateless`

### URL Parameters

|       参数      | 类型 | 是否必填 |                                  描述                                 |           示例值           |
|-----------------|------|----------|-----------------------------------------------------------------------|----------------------------|
| id              | long | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） | 50883                      |
| container_infos | List | 是       | 容器相关元信息                                                        | 详见示例                   |
| container_id    | long | 是       | 容器 id（[查看服务详情](../?http#9-7)）                               | 50883                      |
| envs            | List | 否       | 环境变量，key-value 的形式                                            | 详见示例                   |
| log_dirs        | List | 否       | 日志服务采集日志的目录                                                | ["/var/log/","/var/logs/"] |
| cpu_weight      | int  | 是       | cpu 占比（1-99，总和为100，单容器下必须为 100）                       | 100                        |
| memory_weight   | int  | 是       | 内存占比（1-99，总和为100，单容器下必须为 100）                       | 100                        |



## 9.10. 更改端口配置


> 请求示例

```http
PUT /api/v1/microservices/51572/actions/update-port HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json

{
    "port_maps": [
        {
            "src_port": 80,
            "dst_port": 8080, 
            "protocol": "TCP"
        }
    ]
}
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -d '{
    "port_maps": [
        {
            "src_port": 80,
            "dst_port": 8080, 
            "protocol": "TCP"
        }
    ]
}' "https://open.c.163.com/api/v1/microservices/51572/actions/update-port"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/microservices/{id}/actions/update-port`

### URL Parameters

|      参数     |  类型  | 是否必填 |                                  描述                                 |  示例值  |
|---------------|--------|----------|-----------------------------------------------------------------------|----------|
| id            | long   | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） | 51572    |
| port_maps     | List   | 否       | 端口映射信息                                                          | 详见示例 |
| - dest_port   | int    | 否       | 服务端口                                                              | 80       |
| - source_port | int    | 否       | 容器端口                                                              | 8080     |
| - protocol    | String | 否       | 协议，TCP/UDP                                                         | TCP      |


## 9.11. 弹性伸缩

<aside class="notice">目前仅支持无状态服务。</aside>


> 请求示例

```http
PUT /api/v1/microservices/50883/actions/elastic-scale?new_replicas=2 HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/microservices/50883/actions/elastic-scale?new_replicas=2"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/microservices/{id}/actions/elastic-scale?new_replicas={new_replicas}`

### URL Parameters

|     参数     | 类型 | 是否必填 |                                  描述                                 | 示例值 |
|--------------|------|----------|-----------------------------------------------------------------------|--------|
| id           | long | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） |  50883 |
| new_replicas | int  | 是       | 需要扩缩容的副本数，取值范围：[1,5]                                   |      2 |


## 9.12. 更改镜像版本

<aside class="notice">目前仅支持无状态服务。仅限于更换同一个镜像仓库内的镜像。</aside>


> 请求示例

```http
PUT /api/v1/microservices/50883/actions/update-image HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json

{
    "container_images": [
        {
            "container_id": 50883, 
            "image_path": "hub.c.163.com/public/ubuntu:14.04"
        }
    ], 
    "min_ready_seconds": 10
}
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" -d '{
    "container_images": [
        {
            "container_id": 50883, 
            "image_path": "hub.c.163.com/public/ubuntu:14.04"
        }
    ], 
    "min_ready_seconds": 10
}' "https://open.c.163.com/api/v1/microservice/50883/actions/update-image"
```

### HTTP Request

`POST https://open.c.163.com/api/v1/microservices/{id}/actions/update-image`

### URL Parameters

|        参数       |  类型  | 是否必填 |                                  描述                                 |              示例值               |
|-------------------|--------|----------|-----------------------------------------------------------------------|-----------------------------------|
| id                | long   | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） | 50883                             |
| container_id      | long   | 是       | 容器 id（[查看服务详情](../?http#9-7)）                               | 50883                             |
| image_path        | String | 是       | 待更新的镜像地址                                                      | hub.c.163.com/public/ubuntu:14.04 |
| min_ready_seconds | int    | 是       | 副本更新时间间隔，单位秒，取值范围：[1,600]                           | 10                                |


## 9.13. 更改规格

<aside class="notice">目前仅支持无状态服务。</aside>

> 请求示例

```http
PUT /api/v1/microservices/50883/specification?spec_id=3 HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/microservices/50883/specification?spec_id=3"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/microservices/{id}/specification?spec_id={spec_id}`

### URL Parameters

|   参数  | 类型 | 是否必填 |                                  描述                                 | 示例值 |
|---------|------|----------|-----------------------------------------------------------------------|--------|
| id      | long | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） |  50883 |
| spec_id | long | 是       | 新的规格 id（[规格参数列表](../?http#9-0)）                           |      3 |


## 9.14. 重启和冷重启服务

<aside class="notice">目前仅支持重启和冷重启有状态服务。</aside>

> 请求示例

```http
PUT /api/v1/microservices/51572/actions/restart?option=restart HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/microservices/51572/actions/restart?option=restart"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/microservices/{id}/actions/restart?option={option}`

### URL Parameters

|  参数  |  类型  | 是否必填 |                                  描述                                 | 示例值  |
|--------|--------|----------|-----------------------------------------------------------------------|---------|
| id     | long   | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） | 51572   |
| option | String | 是       | restart（重启）/ hard_reboot（冷重启）                                | restart |




## 9.15. 重启单容器

<aside class="notice">目前仅支持重启有状态服务。</aside>

> 请求示例

```http
PUT /api/v1/microservices/51572/containers/79e666ac71b70e446eb32a7eecc92830d937a4518ebb871456eae5fd8fcf5627/actions/restart HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X PUT -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/microservices/51572/containers/79e666ac71b70e446eb32a7eecc92830d937a4518ebb871456eae5fd8fcf5627/actions/restart"
```

### HTTP Request

`PUT https://open.c.163.com/api/v1/microservices/{id}/containers/{uuid}/actions/restart`

### URL Parameters

| 参数 |  类型  | 是否必填 |                                  描述                                 |                              示例值                              |
|------|--------|----------|-----------------------------------------------------------------------|------------------------------------------------------------------|
| id   | long   | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） | 51572                                                            |
| uuid | String | 是       | 容器 uuid（[查看服务详情](../?http#9-7) 中的 docker_container_ids）   | 79e666ac71b70e446eb32a7eecc92830d937a4518ebb871456eae5fd8fcf5627 |


## 9.16. 删除服务


> 请求示例

```http
DELETE /api/v1/microservices/51572?free_ip=true HTTP/1.1
Host: open.c.163.com
Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f
Content-Type: application/json
```

```shell
curl -X DELETE -H "Authorization: Token 5b17a473c90c443ca1f37ddcdd59ad0f" -H "Content-Type: application/json" "https://open.c.163.com/api/v1/microservices/51572?free_ip=true"
```

### HTTP Request

`DELETE https://open.c.163.com/api/v1/microservices/{id}?free_ip={free_ip}`

### URL Parameters

|   参数  |   类型  | 是否必填 |                                  描述                                 | 示例值 |
|---------|---------|----------|-----------------------------------------------------------------------|--------|
| id      | long    | 是       | 服务 id（[获取空间列表](../?http#9-2)，[获取服务列表](../?http#9-6)） | 51572  |
| free_ip | boolean | 是       | 是否释放 IP，true（释放）/false（不释放）                             | true   |