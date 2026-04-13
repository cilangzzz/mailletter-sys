# M06 模板管理模块 - API设计

## 文档信息

**产品名称：** gaxx-pro 信件处理系统
**模块编号：** M06
**文档版本：** v1.0
**创建日期：** 2026-04-13
**状态：** 草稿

---

## 1. API接口列表

### 1.1 回复模板API

| 序号 | 接口名称 | 接口路径 | HTTP方法 | 权限标识 |
|------|----------|----------|----------|----------|
| 1 | 创建回复模板 | /mailbox/template/reply/create | POST | mailbox:template:reply:create |
| 2 | 更新回复模板 | /mailbox/template/reply/update | PUT | mailbox:template:reply:update |
| 3 | 删除回复模板 | /mailbox/template/reply/delete | DELETE | mailbox:template:reply:delete |
| 4 | 批量删除回复模板 | /mailbox/template/reply/delete-list | DELETE | mailbox:template:reply:delete |
| 5 | 获取回复模板详情 | /mailbox/template/reply/get | GET | mailbox:template:reply:query |
| 6 | 获取回复模板分页列表 | /mailbox/template/reply/page | GET | mailbox:template:reply:query |
| 7 | 获取回复模板简单列表 | /mailbox/template/reply/list | GET | mailbox:template:reply:query |
| 8 | 更新回复模板状态 | /mailbox/template/reply/update-status | PUT | mailbox:template:reply:update |
| 9 | 导出回复模板Excel | /mailbox/template/reply/export-excel | GET | mailbox:template:reply:export |

### 1.2 AI模板API

| 序号 | 接口名称 | 接口路径 | HTTP方法 | 权限标识 |
|------|----------|----------|----------|----------|
| 1 | 创建AI模板 | /mailbox/template/ai/create | POST | mailbox:template:ai:create |
| 2 | 更新AI模板 | /mailbox/template/ai/update | PUT | mailbox:template:ai:update |
| 3 | 删除AI模板 | /mailbox/template/ai/delete | DELETE | mailbox:template:ai:delete |
| 4 | 批量删除AI模板 | /mailbox/template/ai/delete-list | DELETE | mailbox:template:ai:delete |
| 5 | 获取AI模板详情 | /mailbox/template/ai/get | GET | mailbox:template:ai:query |
| 6 | 获取AI模板分页列表 | /mailbox/template/ai/page | GET | mailbox:template:ai:query |
| 7 | 获取AI模板简单列表 | /mailbox/template/ai/list | GET | mailbox:template:ai:query |
| 8 | 更新AI模板状态 | /mailbox/template/ai/update-status | PUT | mailbox:template:ai:update |
| 9 | AI模板预览效果 | /mailbox/template/ai/preview | POST | mailbox:template:ai:query |

### 1.3 快捷填入模板API

| 序号 | 接口名称 | 接口路径 | HTTP方法 | 权限标识 |
|------|----------|----------|----------|----------|
| 1 | 创建快捷填入模板 | /mailbox/template/quick/create | POST | mailbox:template:quick:create |
| 2 | 更新快捷填入模板 | /mailbox/template/quick/update | PUT | mailbox:template:quick:update |
| 3 | 删除快捷填入模板 | /mailbox/template/quick/delete | DELETE | mailbox:template:quick:delete |
| 4 | 批量删除快捷填入模板 | /mailbox/template/quick/delete-list | DELETE | mailbox:template:quick:delete |
| 5 | 获取快捷填入模板详情 | /mailbox/template/quick/get | GET | mailbox:template:quick:query |
| 6 | 获取快捷填入模板分页列表 | /mailbox/template/quick/page | GET | mailbox:template:quick:query |
| 7 | 获取快捷填入模板简单列表 | /mailbox/template/quick/list | GET | mailbox:template:quick:query |
| 8 | 更新快捷填入模板状态 | /mailbox/template/quick/update-status | PUT | mailbox:template:quick:update |
| 9 | 按字段获取快捷填入模板 | /mailbox/template/quick/list-by-field | GET | mailbox:template:quick:query |

---

## 2. 回复模板API详细设计

### 2.1 创建回复模板

**接口路径：** `POST /mailbox/template/reply/create`
**权限标识：** `mailbox:template:reply:create`

#### 请求参数

```json
{
  "name": "标准咨询回复模板",
  "type": 1,
  "content": "尊敬的来信人：您好！您反映的问题已收悉...",
  "orgIds": "1,2,3",
  "businessTypeIds": "1,2",
  "status": 0,
  "sort": 0,
  "remark": "用于咨询类信件的通用回复"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 是 | 模板名称，长度1-50字符 |
| type | Integer | 是 | 模板类型：1-公共模板 2-业务模板 3-单位模板 |
| content | String | 是 | 模板内容，长度1-10000字符 |
| orgIds | String | 否 | 适用单位编号列表，多个用逗号分隔 |
| businessTypeIds | String | 否 | 适用业务类型编号列表，多个用逗号分隔 |
| status | Integer | 否 | 状态：0-启用 1-禁用，默认0 |
| sort | Integer | 否 | 排序值，默认0 |
| remark | String | 否 | 备注，长度最大200字符 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": 1
}
```

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 响应码，0表示成功 |
| msg | String | 错误提示信息 |
| data | Long | 新创建的模板编号 |

---

### 2.2 更新回复模板

**接口路径：** `PUT /mailbox/template/reply/update`
**权限标识：** `mailbox:template:reply:update`

#### 请求参数

```json
{
  "id": 1,
  "name": "标准咨询回复模板",
  "type": 1,
  "content": "尊敬的来信人：您好！您反映的问题已收悉...",
  "orgIds": "1,2,3",
  "businessTypeIds": "1,2",
  "status": 0,
  "sort": 0,
  "remark": "用于咨询类信件的通用回复"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板编号 |
| name | String | 是 | 模板名称 |
| type | Integer | 是 | 模板类型 |
| content | String | 是 | 模板内容 |
| orgIds | String | 否 | 适用单位编号列表 |
| businessTypeIds | String | 否 | 适用业务类型编号列表 |
| status | Integer | 否 | 状态 |
| sort | Integer | 否 | 排序值 |
| remark | String | 否 | 备注 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 2.3 删除回复模板

**接口路径：** `DELETE /mailbox/template/reply/delete`
**权限标识：** `mailbox:template:reply:delete`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 2.4 批量删除回复模板

**接口路径：** `DELETE /mailbox/template/reply/delete-list`
**权限标识：** `mailbox:template:reply:delete`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| ids | List<Long> | 是 | 模板编号列表 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 2.5 获取回复模板详情

**接口路径：** `GET /mailbox/template/reply/get`
**权限标识：** `mailbox:template:reply:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "id": 1,
    "name": "标准咨询回复模板",
    "type": 1,
    "content": "尊敬的来信人：您好！您反映的问题已收悉...",
    "orgIds": "1,2,3",
    "orgNames": "市局办公室,区县分局,交警支队",
    "businessTypeIds": "1,2",
    "businessTypeNames": "交通管理,户籍管理",
    "status": 0,
    "useCount": 200,
    "sort": 0,
    "remark": "用于咨询类信件的通用回复",
    "createTime": "2026-04-01 10:00:00"
  }
}
```

---

### 2.6 获取回复模板分页列表

**接口路径：** `GET /mailbox/template/reply/page`
**权限标识：** `mailbox:template:reply:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| name | String | 否 | 模板名称，模糊匹配 |
| type | Integer | 否 | 模板类型 |
| status | Integer | 否 | 状态 |
| orgId | Long | 否 | 适用单位编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 25,
    "list": [
      {
        "id": 1,
        "name": "标准咨询回复模板",
        "type": 1,
        "content": "尊敬的来信人...",
        "orgIds": "1,2,3",
        "orgNames": "市局办公室,区县分局",
        "status": 0,
        "useCount": 200,
        "sort": 0,
        "createTime": "2026-04-01 10:00:00"
      }
    ]
  }
}
```

---

### 2.7 获取回复模板简单列表

**接口路径：** `GET /mailbox/template/reply/list`
**权限标识：** `mailbox:template:reply:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| orgId | Long | 否 | 当前用户所属单位编号（用于筛选可用模板） |
| letterId | Long | 否 | 信件编号（用于获取信件信息填充占位符） |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "id": 1,
      "name": "标准咨询回复模板",
      "contentPreview": "尊敬的来信人：您好！您反映的问题..."
    }
  ]
}
```

---

### 2.8 更新回复模板状态

**接口路径：** `PUT /mailbox/template/reply/update-status`
**权限标识：** `mailbox:template:reply:update`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板编号 |
| status | Integer | 是 | 状态：0-启用 1-禁用 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 2.9 导出回复模板Excel

**接口路径：** `GET /mailbox/template/reply/export-excel`
**权限标识：** `mailbox:template:reply:export`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 否 | 模板名称，模糊匹配 |
| type | Integer | 否 | 模板类型 |
| status | Integer | 否 | 状态 |

#### 响应结果

直接输出Excel文件流。

---

## 3. AI模板API详细设计

### 3.1 创建AI模板

**接口路径：** `POST /mailbox/template/ai/create`
**权限标识：** `mailbox:template:ai:create`

#### 请求参数

```json
{
  "name": "信访类AI回复模板",
  "letterTypeIds": "1,2",
  "orgIds": "1,2",
  "replyStyle": "formal",
  "contentPoints": "事实核查+处理结果+后续跟进",
  "wordCountMin": 300,
  "wordCountMax": 500,
  "maxTokens": 2000,
  "temperature": 0.7,
  "referenceHistory": true,
  "promptTemplate": "请根据信件内容生成回复...",
  "status": 0,
  "sort": 0,
  "remark": "用于信访类信件的AI回复生成"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 是 | AI模板名称，长度1-50字符 |
| letterTypeIds | String | 否 | 适用信件类型编号列表 |
| orgIds | String | 否 | 适用单位编号列表 |
| replyStyle | String | 是 | 回复风格：formal/casual/standard |
| contentPoints | String | 否 | 内容要点配置 |
| wordCountMin | Integer | 否 | 字数最小值，默认300 |
| wordCountMax | Integer | 否 | 字数最大值，默认500 |
| maxTokens | Integer | 否 | 最大token数，默认2000 |
| temperature | BigDecimal | 否 | 温度参数，默认0.7 |
| referenceHistory | Boolean | 否 | 是否参考历史回复，默认true |
| promptTemplate | String | 否 | AI提示词模板 |
| status | Integer | 否 | 状态，默认0 |
| sort | Integer | 否 | 排序值，默认0 |
| remark | String | 否 | 备注 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": 1
}
```

---

### 3.2 更新AI模板

**接口路径：** `PUT /mailbox/template/ai/update`
**权限标识：** `mailbox:template:ai:update`

#### 请求参数

同创建接口，需额外传入id参数。

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 3.3 删除AI模板

**接口路径：** `DELETE /mailbox/template/ai/delete`
**权限标识：** `mailbox:template:ai:delete`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | AI模板编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 3.4 批量删除AI模板

**接口路径：** `DELETE /mailbox/template/ai/delete-list`
**权限标识：** `mailbox:template:ai:delete`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| ids | List<Long> | 是 | AI模板编号列表 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 3.5 获取AI模板详情

**接口路径：** `GET /mailbox/template/ai/get`
**权限标识：** `mailbox:template:ai:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | AI模板编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "id": 1,
    "name": "信访类AI回复模板",
    "letterTypeIds": "1,2",
    "letterTypeNames": "信访,咨询",
    "orgIds": "1,2",
    "orgNames": "市局办公室,区县分局",
    "replyStyle": "formal",
    "contentPoints": "事实核查+处理结果+后续跟进",
    "wordCountMin": 300,
    "wordCountMax": 500,
    "maxTokens": 2000,
    "temperature": 0.7,
    "referenceHistory": true,
    "promptTemplate": "请根据信件内容生成回复...",
    "status": 0,
    "useCount": 50,
    "sort": 0,
    "remark": "用于信访类信件的AI回复生成",
    "createTime": "2026-04-01 10:00:00"
  }
}
```

---

### 3.6 获取AI模板分页列表

**接口路径：** `GET /mailbox/template/ai/page`
**权限标识：** `mailbox:template:ai:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| name | String | 否 | 模板名称，模糊匹配 |
| replyStyle | String | 否 | 回复风格 |
| status | Integer | 否 | 状态 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 10,
    "list": [
      {
        "id": 1,
        "name": "信访类AI回复模板",
        "letterTypeIds": "1,2",
        "letterTypeNames": "信访,咨询",
        "replyStyle": "formal",
        "status": 0,
        "useCount": 50,
        "createTime": "2026-04-01 10:00:00"
      }
    ]
  }
}
```

---

### 3.7 获取AI模板简单列表

**接口路径：** `GET /mailbox/template/ai/list`
**权限标识：** `mailbox:template:ai:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| letterTypeId | Long | 否 | 信件类型编号 |
| orgId | Long | 否 | 单位编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "id": 1,
      "name": "信访类AI回复模板",
      "replyStyle": "formal"
    }
  ]
}
```

---

### 3.8 更新AI模板状态

**接口路径：** `PUT /mailbox/template/ai/update-status`
**权限标识：** `mailbox:template:ai:update`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | AI模板编号 |
| status | Integer | 是 | 状态：0-启用 1-禁用 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 3.9 AI模板预览效果

**接口路径：** `POST /mailbox/template/ai/preview`
**权限标识：** `mailbox:template:ai:query`

#### 请求参数

```json
{
  "id": 1,
  "sampleLetterContent": "您好，我想咨询一下户籍迁移的相关政策..."
}
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | AI模板编号 |
| sampleLetterContent | String | 是 | 示例信件内容 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "previewContent": "尊敬的来信人：您好！关于您咨询的户籍迁移政策问题..."
  }
}
```

---

## 4. 快捷填入模板API详细设计

### 4.1 创建快捷填入模板

**接口路径：** `POST /mailbox/template/quick/create`
**权限标识：** `mailbox:template:quick:create`

#### 请求参数

```json
{
  "name": "来信类型默认",
  "targetField": "letter_type",
  "fillValue": "投诉",
  "orgIds": "1,2",
  "status": 0,
  "sort": 0,
  "remark": "用于默认填入来信类型"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 是 | 模板名称，长度1-50字符 |
| targetField | String | 是 | 适用字段：letter_type/business_type/case_direction/address/custom_tag/remark |
| fillValue | String | 是 | 填入内容，根据targetField存储对应的值 |
| orgIds | String | 否 | 适用单位编号列表 |
| status | Integer | 否 | 状态，默认0 |
| sort | Integer | 否 | 排序值，默认0 |
| remark | String | 否 | 备注 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": 1
}
```

---

### 4.2 更新快捷填入模板

**接口路径：** `PUT /mailbox/template/quick/update`
**权限标识：** `mailbox:template:quick:update`

#### 请求参数

同创建接口，需额外传入id参数。

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 4.3 删除快捷填入模板

**接口路径：** `DELETE /mailbox/template/quick/delete`
**权限标识：** `mailbox:template:quick:delete`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 快捷填入模板编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 4.4 批量删除快捷填入模板

**接口路径：** `DELETE /mailbox/template/quick/delete-list`
**权限标识：** `mailbox:template:quick:delete`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| ids | List<Long> | 是 | 快捷填入模板编号列表 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 4.5 获取快捷填入模板详情

**接口路径：** `GET /mailbox/template/quick/get`
**权限标识：** `mailbox:template:quick:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 快捷填入模板编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "id": 1,
    "name": "来信类型默认",
    "targetField": "letter_type",
    "targetFieldName": "来信类型",
    "fillValue": "投诉",
    "orgIds": "1,2",
    "orgNames": "市局办公室,区县分局",
    "status": 0,
    "useCount": 100,
    "sort": 0,
    "remark": "用于默认填入来信类型",
    "createTime": "2026-04-01 10:00:00"
  }
}
```

---

### 4.6 获取快捷填入模板分页列表

**接口路径：** `GET /mailbox/template/quick/page`
**权限标识：** `mailbox:template:quick:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| name | String | 否 | 模板名称，模糊匹配 |
| targetField | String | 否 | 适用字段 |
| status | Integer | 否 | 状态 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 15,
    "list": [
      {
        "id": 1,
        "name": "来信类型默认",
        "targetField": "letter_type",
        "targetFieldName": "来信类型",
        "fillValue": "投诉",
        "status": 0,
        "useCount": 100,
        "createTime": "2026-04-01 10:00:00"
      }
    ]
  }
}
```

---

### 4.7 获取快捷填入模板简单列表

**接口路径：** `GET /mailbox/template/quick/list`
**权限标识：** `mailbox:template:quick:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| orgId | Long | 否 | 当前用户所属单位编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "id": 1,
      "name": "来信类型默认",
      "targetField": "letter_type",
      "fillValue": "投诉"
    }
  ]
}
```

---

### 4.8 更新快捷填入模板状态

**接口路径：** `PUT /mailbox/template/quick/update-status`
**权限标识：** `mailbox:template:quick:update`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 快捷填入模板编号 |
| status | Integer | 是 | 状态：0-启用 1-禁用 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 4.9 按字段获取快捷填入模板

**接口路径：** `GET /mailbox/template/quick/list-by-field`
**权限标识：** `mailbox:template:quick:query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| targetField | String | 是 | 适用字段类型 |
| orgId | Long | 否 | 当前用户所属单位编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "id": 1,
      "name": "来信类型默认",
      "fillValue": "投诉"
    },
    {
      "id": 2,
      "name": "来信类型咨询",
      "fillValue": "咨询"
    }
  ]
}
```

---

## 5. 权限SQL配置

### 5.1 回复模板权限

```sql
-- 回复模板管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('回复模板管理', '', 2, 11, {后台管理父菜单ID}, 'reply-template', 'ep:document', 'backend/reply-template/index', 0);
SET @replyTemplateMenuId = LAST_INSERT_ID();

-- 回复模板按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('回复模板查询', 'mailbox:template:reply:query', 3, 1, @replyTemplateMenuId, 0),
('回复模板新增', 'mailbox:template:reply:create', 3, 2, @replyTemplateMenuId, 0),
('回复模板修改', 'mailbox:template:reply:update', 3, 3, @replyTemplateMenuId, 0),
('回复模板删除', 'mailbox:template:reply:delete', 3, 4, @replyTemplateMenuId, 0),
('回复模板导出', 'mailbox:template:reply:export', 3, 5, @replyTemplateMenuId, 0);
```

### 5.2 AI模板权限

```sql
-- AI模板管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('AI模板管理', '', 2, 12, {后台管理父菜单ID}, 'ai-template', 'ep:magic-stick', 'backend/ai-template/index', 0);
SET @aiTemplateMenuId = LAST_INSERT_ID();

-- AI模板按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('AI模板查询', 'mailbox:template:ai:query', 3, 1, @aiTemplateMenuId, 0),
('AI模板新增', 'mailbox:template:ai:create', 3, 2, @aiTemplateMenuId, 0),
('AI模板修改', 'mailbox:template:ai:update', 3, 3, @aiTemplateMenuId, 0),
('AI模板删除', 'mailbox:template:ai:delete', 3, 4, @aiTemplateMenuId, 0);
```

### 5.3 快捷填入模板权限

```sql
-- 快捷填入模板管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('快捷填入模板管理', '', 2, 13, {后台管理父菜单ID}, 'quick-fill-template', 'ep:edit', 'backend/quick-fill-template/index', 0);
SET @quickFillTemplateMenuId = LAST_INSERT_ID();

-- 快捷填入模板按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('快捷填入模板查询', 'mailbox:template:quick:query', 3, 1, @quickFillTemplateMenuId, 0),
('快捷填入模板新增', 'mailbox:template:quick:create', 3, 2, @quickFillTemplateMenuId, 0),
('快捷填入模板修改', 'mailbox:template:quick:update', 3, 3, @quickFillTemplateMenuId, 0),
('快捷填入模板删除', 'mailbox:template:quick:delete', 3, 4, @quickFillTemplateMenuId, 0);
```

---

## 6. 错误码定义

```java
// ========== 回复模板相关错误码 1_006_001_XXX ==========
ErrorCode REPLY_TEMPLATE_NOT_EXISTS = new ErrorCode(1_006_001_000, "回复模板不存在");
ErrorCode REPLY_TEMPLATE_NAME_DUPLICATE = new ErrorCode(1_006_001_001, "已存在该名称的回复模板");
ErrorCode REPLY_TEMPLATE_CONTENT_EMPTY = new ErrorCode(1_006_001_002, "模板内容不能为空");
ErrorCode REPLY_TEMPLATE_CONTENT_TOO_LONG = new ErrorCode(1_006_001_003, "模板内容超过最大长度限制10000字符");
ErrorCode REPLY_TEMPLATE_CAN_NOT_DELETE = new ErrorCode(1_006_001_004, "回复模板不能删除，原因：{}");
ErrorCode REPLY_TEMPLATE_STATUS_ERROR = new ErrorCode(1_006_001_005, "回复模板状态不正确");

// ========== AI模板相关错误码 1_006_002_XXX ==========
ErrorCode AI_TEMPLATE_NOT_EXISTS = new ErrorCode(1_006_002_000, "AI模板不存在");
ErrorCode AI_TEMPLATE_NAME_DUPLICATE = new ErrorCode(1_006_002_001, "已存在该名称的AI模板");
ErrorCode AI_TEMPLATE_CONFIG_INCOMPLETE = new ErrorCode(1_006_002_002, "AI模板配置参数不完整");
ErrorCode AI_TEMPLATE_PREVIEW_FAILED = new ErrorCode(1_006_002_003, "AI模板预览生成失败");
ErrorCode AI_TEMPLATE_STATUS_ERROR = new ErrorCode(1_006_002_004, "AI模板状态不正确");

// ========== 快捷填入模板相关错误码 1_006_003_XXX ==========
ErrorCode QUICK_FILL_TEMPLATE_NOT_EXISTS = new ErrorCode(1_006_003_000, "快捷填入模板不存在");
ErrorCode QUICK_FILL_TEMPLATE_NAME_DUPLICATE = new ErrorCode(1_006_003_001, "已存在该名称的快捷填入模板");
ErrorCode QUICK_FILL_TEMPLATE_FIELD_INVALID = new ErrorCode(1_006_003_002, "快捷填入模板适用字段无效");
ErrorCode QUICK_FILL_TEMPLATE_STATUS_ERROR = new ErrorCode(1_006_003_003, "快捷填入模板状态不正确");
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，包含回复模板、AI模板、快捷填入模板的完整API设计 | Claude Agent |