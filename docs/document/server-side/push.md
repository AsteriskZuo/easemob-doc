# 离线推送设置 REST API

<Toc />

本文展示如何调用环信即时通讯 RESTful API 实现离线推送，包括设置离线推送通知显示的昵称、推送通知方式及免打扰模式。调用以下方法前，请先参考 [限制条件](limitationapi.html) 了解即时通讯 RESTful API 的调用频率限制。

## 公共参数

以下表格列举了即时通讯 RESTful API 的公共请求参数和响应参数：

### 请求参数

| 参数            | 类型   | 描述  | 是否必填                                                         |
| :-------------- | :----- | :------- | ------------------------------------------------------------ |
| `host`   | String | 你在环信即时通讯云控制台注册项目时所在的集群服务器地址。    | 是  |
| `org_name` | String | 即时通讯服务分配给每个企业（组织）的唯一标识。你可以通过控制台获取该字段。 | 是 |
| `app_name` | String | 你在环信即时通讯云控制台注册项目时填入的应用名称。  | 是 |
| `username` | String | 环信用户 ID。 | 是 |

### 响应参数

| 参数     | 类型     | 描述                                                        |
| :--------| :----- | :----------------------------------------------------------- |
| `action` | String | 请求方式。                                 |
| `organization`| String  | 即时通讯服务分配给每个企业（组织）的唯一标识。                               |
| `application` | String |  环信即时通讯服务分配给每个 app 的唯一内部标识，开发者无需关注。                                |
| `uri` | String | 请求 URL。                                 |
| `timestamp`| Long  | HTTP 响应的 Unix 时间戳，单位为毫秒。                              |
| `applicationName`| String  | 你在环信即时通讯云控制台注册项目时填入的应用名称，相当于路径参数 app_name。                            |
| `duration` | Int | 请求耗时，单位为毫秒。                              |
| `data` | String | 返回数据详情。                              |
| `username` | String | 环信用户 ID。                                 |
| `uuid`   | String   | 用户的 UUID，即系统为用户生成唯一标识字段，开发者无需关注。      |

## 认证方式

环信即时通讯 REST API 要求 Bearer HTTP 认证。每次发送 HTTP 请求时，必须在请求 header 部填入如下 Authorization 字段：

`Authorization: Bearer ${YourAppToken}`

为提高项目的安全性，环信使用 Token（动态密钥）对即将登录即时通讯系统的用户进行鉴权。即时通讯 REST API 仅支持使用 App Token 的鉴权方式，详见 [使用 Token 鉴权](easemob_app_token.html)。

## 设置离线推送时显示的昵称

设置离线推送时显示的昵称。

### HTTP 请求

```http
PUT https://{host}/{org_name}/{app_name}/users/{username}
```

#### 路径参数

参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

| 参数            | 类型   | 描述  | 是否必填                          |
| :-------------- | :----- | :------- | ---------------- |
| `Content-Type`   | String | 内容类型：`application/json`    | 是  |
| `Accept`   | String | 内容类型：`application/json`    | 是  |
| `Authorization` | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。  | 是 |

#### 请求 body

请求包体为 JSON Object 类型，包含以下字段：

| 参数            | 类型   | 描述  | 是否必填                                                         |
| :-------------- | :----- | :------- | ------------------------------------------------------------ |
| `nickname`   | String | 收到离线推送通知时显示的昵称，仅用在客户端推送通知栏显示。你可自定义该昵称，长度不能超过 100 个字符。<br/>支持以下字符集：<br/> - 26 个小写英文字母 a-z；<br/> - 26 个大写英文字母 A-Z；<br/> - 10 个数字 0-9；<br/> - 中文；<br/> - 特殊字符。<br/> 该昵称可与用户信息中的昵称设置不同，不过我们建议这两种昵称的设置保持一致。因此，修改其中一个昵称时，也需调用相应方法对另一个进行更新，确保设置一致。更新用户信息中的昵称的方法，详见 [设置用户属性](userprofile.html#设置用户属性)。 | 否  |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数    | 类型  | 描述                                                        |
| :-------- | :-----| :----------------------------------------------------------- |
| `type`    | String  | 用户类型，即 “user”。              |
| `created`   | Long   | 用户创建时间戳。                       |
| `modified`   | Long   | 用户信息修改时戳。                    |
| `activated`   | Bool    | 用户是否为活跃状态：<br/> - `true`：用户为活跃状态。<br/> - `false`：用户为封禁状态。如要使用已被封禁的用户账户，你需要调用 [解禁接口](account_system.html#账号解禁) 解除封禁，才能正常登录。 |
|  `nickname`     | String  | 收到离线推送通知时显示的昵称。     |

其他参数及说明详见 [公共参数](#公共参数)。

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -X PUT -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' -d '{    "nickname": "testuser"   }' 'http://XXXX/XXXX/XXXX/users/user1'
```

#### 响应示例

```json
{
  "action": "put",
  "application": "8be024f0-XXXX-XXXX-XXXX-5d598d5f8402",
  "path": "/users",
  "uri": "https://XXXX/XXXX/XXXX/users",
  "entities": [
        {
            "uuid": "4759aa70-XXXX-XXXX-XXXX-6fa0510823ba",
            "type": "user",
            "created": 1542595573399,
            "modified": 1542596083687,
            "username": "user1",
            "activated": true,
            "nickname": "testuser"
        }
    ],
    "timestamp": 1542596083685,
    "duration": 6,
    "organization": "agora-demo",
    "applicationName": "testapp"
}
```

## 设置离线推送通知的展示方式

设置离线推送通知在客户端的展示方式，设置即时生效。服务端据此向用户推送离线消息。

### HTTP 请求

```http
PUT https://{host}/{org_name}/{app_name}/users/{username}
```

#### 路径参数

参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

| 参数            | 类型   | 描述  | 是否必填                                                         |
| :-------------- | :----- | :------- | ------------------------------------------------------------ |
| `Content-Type`   | String | 内容类型：`application/json`    | 是  |
| `Authorization` | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。| 是 |

#### 请求 body

请求包体为 JSON Object 类型，包含以下字段：

| 参数      | 类型   | 描述  | 是否必填          |
| :-------------- | :----- | :------- | ------------------- |
| `notification_display_style`   | Int | 离线推送通知的展示方式：<br/> - （默认） 0：推送标题为“您有一条新消息”，推送内容为“请点击查看”；<br/> - `1`：推送标题为“您有一条新消息”，推送内容为发送人昵称和离线消息的内容。   | 是  |

### HTTP 响应

#### 响应 body

| 参数   | 类型   | 描述                 |
| :--------| :-------- | :-------------------- |
| `type`   | String  | 用户类型，即 “user”。                                              |
| `created`   | Long  | 用户创建时间戳。                       |
| `modified`   | Long  | 用户信息修改时间戳。              |
| `activated`   | Bool   | 用户是否为活跃状态：<br/> - `true`：用户为活跃状态。<br/> - `false`：用户为封禁状态。如要使用已被封禁的用户账户，你需要调用 [解禁接口](account_system.html#账号解禁) 解除封禁，才能正常登录。 |
| `notification_no_disturbing` | Bool   | 是否设置为免打扰模式：<br/> - `true`：是；<br/> - `false`：否。    |
| `notification_no_disturbing_start`  | Int    | 免打扰时间段的开始时间。            |
| `notification_no_disturbing_end`  | Int    | 免打扰时间段的结束时间。         |
| `notification_display_style`  | Int    | 离线推送通知的展示方式。 |
| `nickname`   | String    | 离线推送通知收到时显示的昵称。 |
| `notifier_name` | String    | 推送证书名称。 |

其他参数及说明详见 [公共参数](#公共参数)。

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -X PUT -H "Authorization: Bearer <YourAppToken>" -i  https://XXXX/XXXX/XXXX/users/a -d '{"notification_display_style": "1"}'
```

#### 响应示例

```json
{
    "action" : "put",
    "application" : "17d59e50-XXXX-XXXX-XXXX-0dc80c0f5e99",
    "path" : "/users",
    "uri" : "https://XXXX/XXXX/XXXX/users",
    "entities" : [
        {
            "uuid" : "3b8c9890-XXXX-XXXX-XXXX-f50bf55cafad",
            "type" : "user",
            "created" : 1530276298905,
            "modified" : 1534407146060,
            "username" : "user1",
            "activated" : true,
            "notification_no_disturbing" : false,
            "notification_no_disturbing_start" : 1,
            "notification_no_disturbing_end" : 3,
            "notification_display_style" : 1,
            "nickname" : "testuser",
            "notifier_name" : "2882303761517426801"
        }
    ],
    "timestamp" : 1534407146058,
    "duration" : 3,
    "organization" : "1112171214115068",
    "applicationName" : "testapp"
}
```

## 设置免打扰模式

设置离线推送免打扰模式，在免打扰期间，用户将不会收到离线消息推送。

### HTTP 请求

```http
PUT https://{host}/{org_name}/{app_name}/users/{username}
```

#### 路径参数

参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

| 参数            | 类型   | 描述 | 是否必填                                                   |
| :-------------- | :----- | :------- | ------------------------------------------------------------ |
| `Content-Type`  | String | 内容类型：`application/json`     | 是                                 |
| `Authorization` | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。| 是 |

#### 请求 body

请求包体为 JSON Object 类型，包含以下字段：

| 参数                           | 类型 | 描述   | 是否必填<div style="width: 80px;"></div>                                         |
| :-------- | :------------ | :------------ |:------------ |
| `notification_no_disturbing`      | Bool  |  是否设置为免打扰模式：<br/> - `true`：是；<br/> - `false`：否。 | 否 |
| `notification_no_disturbing_start` |  String | 免打扰时间段的开始时间，精确到小时，例如 “8” 表示每日 8:00 开启免打扰模式。该参数的取值范围为 [0,23]。 | 否|
| `notification_no_disturbing_end`  | String | 免打扰时间段的结束时间，精确到小时，例如 “18” 表示每日 18:00 关闭免打扰模式。该参数的取值范围为 [0,23]。 | 否                   |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数       | 类型           | 描述           |
| :------------| :------------- | :------------------- |
| `type`     | String | 用户类型，即 “user”。            |
| `created`   | Long   | 创建用户的时间戳。          |
| `modified`    | Long    | 用户信息修改时间戳。             |
| `activated`   | Bool   | 用户是否为活跃状态：<br/> - `true`：用户为活跃状态。<br/> - `false`：用户为封禁状态。如要使用已被封禁的用户账户，你需要调用 [解禁接口](account_system.html#账号解禁) 解除封禁，才能正常登录。 |
| `notification_no_disturbing`   | Bool     | 是否设置为免打扰模式：<br/> - `true`：是；<br/> - `false`：否。      |
| `notification_no_disturbing_start`  | String    | 免打扰时间段的开始时间。                           |
| `notification_no_disturbing_end`   | String   | 免打扰时间段的结束时间。  |
| `notification_display_style`  | Int    | 离线推送通知的展示方式。 |
| `nickname`    | String  | 离线推送通知收到时显示的昵称。 |
| `notifier_name` | String    | 推送证书名称。 |][========================]

其他参数及说明详见 [公共参数](#公共参数)。

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

**设置免打扰时间段**

```bash
curl -X PUT -H "Authorization: Bearer <YourAppToken>" -i  "https://XXXX/XXXX/XXXX/users/a " -d '{"notification_no_disturbing": true,"notification_no_disturbing_start": "1","notification_no_disturbing_end": "3"}'
```

**关闭免打扰模式**

```bash
curl -X PUT -H "Authorization: Bearer <YourAppToken>" -i  "https://XXXX/XXXX/XXXX/users/a " -d '{"notification_no_disturbing": false}'
```

#### 响应示例

```json
{
    "action" : "put",
    "application" : "17d59e50-XXXX-XXXX-XXXX-0dc80c0f5e99",
    "path" : "/users",
    "uri" : "https://XXXX/XXXX/XXXX/users",
    "entities" : [
        {
            "uuid" : "3b8c9890-XXXX-XXXX-XXXX-f50bf55cafad",
            "type" : "user",
            "created" : 1530276298905,
            "modified" : 1534405429835,
            "username" : "User1",
            "activated" : true,
            "notification_no_disturbing" : true,
            "notification_no_disturbing_start" : 1,
            "notification_no_disturbing_end" : 3,
            "notification_display_style" : 0,
            "nickname" : "testuser",
            "notifier_name" : "2882303761517426801"
        }
    ],
    "timestamp" : 1534405429833,
    "duration" : 4,
    "organization" : "1112171214115068",
    "applicationName" : "testapp"
}
```

## 设置离线推送设置

你可以设置全局离线推送的通知方式和免打扰模式以及单个单聊或群聊会话的离线推送设置。

### HTTP 请求

```http
PUT https://{host}/{org}/{app}/users/{username}/notification/{type}/{key}
```

#### 路径参数

| 参数           | 类型  | 描述     |  是否必填                                                       |
| :------------ | :-------| :------ | :----------------------------------------------------------- |
| `type` | String | 对象类型，即会话类型：<br/> - `user`：用户，表示单聊；<br/> - `chatgroup`：群组，表示群聊。  | 是 |
| `key`         | String     | 对象名称：<br/> - 单聊时为对端用户的用户 ID；<br/> - 群聊时为群组 ID。 | 是  |

其他参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

|      参数      | 类型   | 描述 |        是否必填       |
| :------------ | :----- | :------ | :---------------- |
| `Content-Type` | String | 内容类型：`application/json`   | 是  |
| `Authorization`  | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。 | 是 |

#### 请求 body

| 参数             | 类型  | 描述   | 是否必填<div style="width: 80px;"></div>                                     |
| :--------------- | :------- | :----- | :--------------------------------------- |
| `type`           | String | 离线推送通知方式：<br/> - `DEFAULT`：默认值，采用全局配置；<br/> - `ALL`：接收全部离线消息的推送通知；<br/> - `AT`：只接收提及当前用户的离线消息的推送通知；<br/> - `NONE`：不接收离线消息的推送通知。 | 否 |
| `ignoreInterval` | Int  | 离线推送免打扰时间段，精确到分钟，格式为 HH:MM-HH:MM，例如 08:30-10:00。该时间为 24 小时制，免打扰时间段的开始时间和结束时间中的小时数和分钟数的取值范围分别为 [00,23] 和 [00,59]。免打扰时段的设置仅针对 app 生效，对单聊或群聊不生效。如需设置 app 的免打扰时段，`type` 指定为 `user`，`key` 指定为当前用户 ID。| 否  |
| `ignoreDuration` | Long   | 离线推送免打扰时长，单位为毫秒。该参数的取值范围为 [0,604800000]，`0` 表示该参数无效，`604800000` 表示免打扰模式持续 7 天。   | 否  |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数      | 类型     | 描述                  |
| :---------| :---------- | :------------------- |
| `type`          | String        | 离线推送通知方式。|
| `ignoreInterval`    | Int   | 离线推送免打扰时间段。 |
| `ignoreDuration` | Long | 离线推送免打扰时长。 |

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -L -X PUT '{url}/{org_name}/{app_name}/users/{username}/notification/user/{key}' \
-H 'Authorization: Bearer <YourAppToken>' \
-H 'Content-Type: application/json' \
--data-raw '{
    "type":"NONE",
    "ignoreInterval":"21:30-08:00",
    "ignoreDuration":86400000
}'
```

#### 响应示例

```json
{
    "path": "/users",
    "uri": "https://XXXX/XXXX/XXXX/users/notification/user/hxtest",
    "timestamp": 1647503749918,
    "organization": "hx",
    "application": "17fe201b-XXXX-XXXX-XXXX-1ed1ebd7b227",
    "action": "put",
    "data": {
        "type": "NONE",
        "ignoreDuration": 1647590149924,
        "ignoreInterval": "21:30-08:00"
    },
    "duration": 20,
    "applicationName": "hxdemo"
}
```

## 查询离线推送设置

查询指定单聊、指定群聊或全局的离线推送设置。

### HTTP 请求

```http
GET https://{host}/{org}/{app}/users/{username}/notification/{type}/{key}
```

#### 路径参数

| 参数           | 类型  | 描述     |  是否必填                                                       |
| :------------ | :-------| :------ | :----------------------------------------------------------- |
| `type` | String | 对象类型，即会话类型：<br/> - `user`：用户，表示单聊；<br/> - `chatgroup`：群组，表示群聊。  | 是 |
| `key`         | String     | 对象名称：<br/> - 单聊时为对端用户的用户 ID；<br/> - 群聊时为群组 ID。 | 是  |

其他参数及说明详见 [公共参数](#公共参数)。


#### 请求 header

|      参数      | 类型   | 描述 | 是否必填               |
| :------------ | :----- | :------: | :----------------: |
| `Authorization`  | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。 | 是 |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数   | 类型     | 描述             |
| :--------| :---------- | :-------- |
| `type`            | String          | 离线推送通知方式。|
| `ignoreInterval`   | Int    | 离线推送免打扰时间段。 |
| `ignoreDuration` | Long  | 离线推送免打扰时长。 |

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -L -X GET '{url}/{org}/{app}/users/{username}/notification/chatgroup/{key}' \
-H 'Authorization: Bearer <YourAppToken>'
```

#### 响应示例

```json
{
    "path": "/users",
    "uri": "https://XXXX/XXXX/XXXX/users/notification/chatgroup/12312312321",
    "timestamp": 1647503749918,
    "organization": "hx",
    "application": "17fe201b-XXXX-XXXX-XXXX-1ed1ebd7b227",
    "action": "get",
    "data": {
        "type": "NONE",
        "ignoreDuration": 1647590149924,
        "ignoreInterval": "21:30-08:00"
    },
    "duration": 20,
    "applicationName": "hxdemo"
}
```

## 设置推送翻译语言

设置离线推送消息的翻译语言。

### HTTP 请求

```http
PUT https://{host}/{org}/{app}/users/{username}/notification/language
```

#### 路径参数

参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

| 参数      | 类型   | 描述   | 是否必填       |
| :------------ | :----- | :------: | :----------------: |
| `Content-Type` | String | 内容类型：`application/json` | 是  |
| `Authorization`  | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。 | 是  |

#### 请求 body

| 参数                  | 类型  | 描述   | 是否必填       |
| :-------------------- | :------- | :----- | :----------- |
| `translationLanguage` | String     | 目标语言代码。若设置为空字符串，表示无需翻译，仅发送消息原文。 | 是 |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数      | 类型       | 描述                       |
| :----------| :-------- | :------------- |
| `language`     | String | 目标语言代码。|

### 示例

#### 请求示例

```bash
curl -L -X PUT '{url}/{org}/{app}/users/{username}/notification/language' \
-H 'Authorization: Bearer <YourAppToken>' \
-H 'Content-Type: application/json' \
--data-raw '{
    "translationLanguage":"EU"
}'
```

#### 响应示例

```json
{
    "path": "/users",
    "uri": "https://XXXX/XXXX/XXXX/users/notification/language",
    "timestamp": 1648089630244,
    "organization": "hx",
    "application": "17fe201b-XXXX-XXXX-XXXX-1ed1ebd7b227",
    "action": "put",
    "data": {
        "language": "EU"
    },
    "duration": 66,
    "applicationName": "hxdemo"
}
```

## 查询推送翻译

查询离线推送消息的翻译语言。

### HTTP 请求

```http
GET https://{host}/{org}/{app}/users/{username}/notification/language
```

#### 路径参数

参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

|      参数      | 类型   | 描述 |  是否必填              |
| :------------ | :----- | :------: | :----------------: |
| `Content-Type` | String | 内容类型：`application/json`  | 是 |
| `Authorization`  | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。 | 是 |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数      | 类型      | 描述          |
| :-----------| :-------- | :-------- |
| `language`    | String          | 目标语言代码。|

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -L -X GET '{url}/{org}/{app}/users/{username}/notification/language' \
-H 'Authorization: Bearer <YourAppToken>'
```

#### 响应示例

```json
{
    "path": "/users",
    "uri": "https://XXXX/XXXX/XXXX/users/notification/language",
    "timestamp": 1648089630244,
    "organization": "hx",
    "application": "17fe201b-XXXX-XXXX-XXXX-1ed1ebd7b227",
    "action": "put",
    "data": {
        "language": "EU"
    },
    "duration": 66,
    "applicationName": "hxdemo"
}
```

## 创建离线推送模板

创建离线推送消息模板。

### HTTP 请求

```http
POST https://{host}/{org}/{app}/notification/template
```

#### 路径参数

参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

| 参数      | 类型   | 描述 | 是否必填       |
| :------------ | :----- | :------: | :----------------: |
| `Content-Type` | String | 内容类型：`application/json`   | 是  |
| `Authorization`  | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。| 是   |

#### 请求 body

| 参数                 |  类型  | 描述    | 是否必填    |
| :-------------------- | :------- | :----- | :----------- |
| `name` | String | 要添加的推送模板的名称。 | 是  |
| `title_pattern` | String  | 自定义推送标题，例如： 标题 {0}。 | 是 |
| `content_pattern` | String     | 自定义推送内容，例如：内容 {0}, {1}。 | 是 |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数   |  类型    | 描述           |
| :-----------| :------- | :------------ |
| `name`             | String            | 推送模板名称。|
| `createAt`         | Long            | 推送模板的创建时间。|
| `updateAt`          | Long          | 推送模板的更新时间。|
| `title_pattern`      | String          | 自定义推送标题。|
| `content_pattern`   | String           | 自定义推送内容。|

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -X POST '{url}/{org}/{app}/notification/template' \
-H 'Authorization: Bearer <YourAppToken>' \
-H 'Content-Type: application/json' \
--data-raw '{
    "name": "test7",
    "title_pattern": "你好,{0}",
    "content_pattern": "推送测试,{0}"
}'
```

#### 响应示例

```json
{
    "uri": "https://XXXX/XXXX/XXXX/notification/template",
    "timestamp": 1646989584108,
    "organization": "hx",
    "application": "17fe201b-XXXX-XXXX-XXXX-1ed1ebd7b227",
    "action": "post",
    "data": {
        "name": "test7",
        "createAt": 1646989584124,
        "updateAt": 1646989584124,
        "title_pattern": "你好,{0}",
        "content_pattern": "推送测试,{0}"
    },
    "duration": 26,
    "applicationName": "hxdemo"
}
```

## 查询离线推送模板

查询离线推送消息使用的模板。

### HTTP 请求

```http
GET https://{host}/{org}/{app}/notification/template/{name}
```

#### 路径参数

|      参数      | 类型   |  描述  | 是否必填           |
| :------------ | :----- | :------: | :----------------: |
| `name` | String | 要查询的推送模板的名称。   | 是 |

其他参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

|      参数      | 类型   |  描述  | 是否必填           |
| :------------ | :----- | :------: | :----------------: |
| `Content-Type` | String | 内容类型：`application/json`    | 是 |
| `Authorization`  | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。 | 是   |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数  | 类型   | 描述             |
| :-----------| :----- | :------------- |
| `name`           | String | 推送模板名称。|
| `createAt`       | Long   | 推送模板的创建时间。|
| `updateAt`       | Long   | 推送模板的更新时间。|
| `title_pattern`  | String | 自定义推送标题。|
| `content_pattern`| String | 自定义推送内容。|

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -X GET '{url}/{org}/{app}/notification/template/{name}' \
-H 'Authorization: Bearer <YourAppToken>'
```

#### 响应示例

```json
{
    "uri": "https://XXXX/XXXX/XXXX/notification/template/test7",
    "timestamp": 1646989686393,
    "organization": "hx",
    "application": "17fe201b-XXXX-XXXX-XXXX-1ed1ebd7b227",
    "action": "get",
    "data": {
        "name": "test7",
        "createAt": 1646989584124,
        "updateAt": 1646989584124,
        "title_pattern": "你好,{0}",
        "content_pattern": "推送测试,{0}"
    },
    "duration": 11,
    "applicationName": "hxdemo"
}
```

## 删除离线推送模板

删除离线消息推送模板。

### HTTP 请求

```
DELETE https://{host}/{org}/{app}/notification/template/{name}
```

#### 路径参数

|      参数      | 类型   |  描述  | 是否必填           |
| :------------ | :----- | :------: | :----------------: |
| `name` | String | 要删除的推送模板的名称。   | 是 |

其他参数及说明详见 [公共参数](#公共参数)。

#### 请求 header

|      参数      | 类型   |  描述    |  是否必填          |
| :------------ | :----- | :------: | :----------------: |
| `Content-Type` | String | 内容类型：`application/json`    | 是 |
| `Authorization`  | String | `Bearer ${YourAppToken}` Bearer 是固定字符，后面加英文空格，再加上获取到的 app token 的值。 | 是   |

### HTTP 响应

#### 响应 body

如果返回的 HTTP 状态码为 200，表示请求成功，响应包体中包含以下字段：

| 参数     | 类型   | 描述    |
| :-------| :------- | :---------- |
| `name`           | String        | 推送模板名称。|
| `createAt`       | Long          | 推送模板的创建时间。|
| `updateAt`        | Long         | 推送模板的更新时间。|
| `title_pattern`    | String      | 自定义推送标题。|
| `content_pattern`   | String     | 自定义推送内容。|

如果返回的 HTTP 状态码非 200，表示请求失败。你可以参考 [错误码](error.html) 了解可能的原因。

### 示例

#### 请求示例

```bash
curl -X DELETE '{url}/{org}/{app}/notification/template' \
-H 'Authorization: Bearer {YourAppToken}'
```

#### 响应示例

```json
{
    "uri": "https://XXXX/XXXX/XXXX/notification/template",
    "timestamp": 1646989686393,
    "organization": "hx",
    "application": "17fe201b-XXXX-XXXX-XXXX-1ed1ebd7b227",
    "action": "delete",
    "data": {
        "name": "test7",
        "createAt": 1646989584124,
        "updateAt": 1646989584124,
        "title_pattern": "你好,{0}",
        "content_pattern": "推送测试,{0}"
    },
    "duration": 11,
    "applicationName": "hxdemo"
}
```