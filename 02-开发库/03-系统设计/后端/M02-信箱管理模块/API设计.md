# M02 信箱管理模块 - API设计

## 文档信息

**产品名称：** gaxx-pro 信件处理系统
**模块名称：** M02 信箱管理模块
**文档版本：** v1.0
**创建日期：** 2026-04-13
**技术栈：** Java/Spring Boot + MyBatis Plus
**状态：** 草稿

---

## 1. API接口概览

### 1.1 接口模块划分

| 子模块 | 接口路径前缀 | 权限标识前缀 | 说明 |
|--------|--------------|--------------|------|
| 办理单位管理 | /mailbox/letter/handle-unit | mailbox:handle-unit | 办理单位CRUD及查询 |
| 信件回复管理 | /mailbox/letter/reply | mailbox:letter-reply | 回复内容管理 |
| 信件办理管理 | /mailbox/letter/handle | mailbox:letter-handle | 办理详情管理 |
| 流转记录管理 | /fz/flow-record | fz:flow-record | 流转记录管理 |
| 信件流程管理 | /fz/letter-flow | fz:letter-flow | 流程节点管理 |

### 1.2 接口列表总览

| 序号 | 接口名称 | HTTP方法 | 接口路径 | 权限标识 |
|------|----------|----------|----------|----------|
| 1 | 创建办理单位 | POST | /mailbox/letter/handle-unit/create | mailbox:handle-unit:create |
| 2 | 更新办理单位 | PUT | /mailbox/letter/handle-unit/update | mailbox:handle-unit:update |
| 3 | 删除办理单位 | DELETE | /mailbox/letter/handle-unit/delete | mailbox:handle-unit:delete |
| 4 | 批量删除办理单位 | DELETE | /mailbox/letter/handle-unit/batch-delete | mailbox:handle-unit:delete |
| 5 | 获取办理单位详情 | GET | /mailbox/letter/handle-unit/get | mailbox:handle-unit:query |
| 6 | 分页查询办理单位 | GET | /mailbox/letter/handle-unit/page | mailbox:handle-unit:query |
| 7 | 获取办理单位列表 | GET | /mailbox/letter/handle-unit/list | mailbox:handle-unit:query |
| 8 | 获取所有办理单位完整信息 | GET | /mailbox/letter/handle-unit/full-list | mailbox:handle-unit:query |
| 9 | 导出办理单位Excel | GET | /mailbox/letter/handle-unit/export-excel | mailbox:handle-unit:export |
| 10 | 创建信件回复 | POST | /mailbox/letter/reply/create | mailbox:letter-reply:create |
| 11 | 创建信件回复草稿 | POST | /mailbox/letter/reply/save-draft | mailbox:letter-reply:create |
| 12 | 修改答复内容 | PUT | /mailbox/letter/reply/update | mailbox:letter-reply:update |
| 13 | 提交回复审核 | POST | /mailbox/letter/reply/submit | mailbox:letter-reply:submit |
| 14 | 删除回复草稿 | DELETE | /mailbox/letter/reply/delete-draft | mailbox:letter-reply:delete |
| 15 | 获取回复详情 | GET | /mailbox/letter/reply/get | mailbox:letter-reply:query |
| 16 | 获取信件办理详情 | GET | /mailbox/letter/handle/get | mailbox:letter-handle:query |
| 17 | 创建信件办理信息 | POST | /mailbox/letter/handle/create | mailbox:letter-handle:create |
| 18 | 更新信件办理信息 | PUT | /mailbox/letter/handle/update | mailbox:letter-handle:update |
| 19 | 获取流转记录列表 | GET | /fz/flow-record/list | fz:flow-record:query |
| 20 | 获取信件流程列表 | GET | /fz/letter-flow/list | fz:letter-flow:query |

---

## 2. 办理单位管理API

### 2.1 创建办理单位

**接口路径：** `POST /mailbox/letter/handle-unit/create`
**权限标识：** `mailbox:handle-unit:create`
**功能说明：** 为信件指派办理单位并指定单位角色（主办/督办/协办）

#### 请求参数

```json
{
    "letterId": 1001,              // 必填，关联信件ID
    "letterNo": "XL202604130001",  // 必填，信件编号
    "sourceUnitId": 100,           // 必填，指派来源单位ID
    "handleUnitId": 200,           // 必填，办理单位ID
    "unitRole": 0                  // 必填，单位角色：0-主办，1-督办，2-协办
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 1,                    // 办理单位记录ID
        "letterId": 1001,
        "letterNo": "XL202604130001",
        "sourceUnitId": 100,
        "sourceUnitName": "市局信访室",
        "handleUnitId": 200,
        "handleUnitName": "交警大队",
        "unitRole": 0,
        "assignTime": "2026-04-13 10:30:00",
        "unitStatus": 0,
        "handleStatus": 0,
        "replyStatus": 0
    }
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 信件状态校验 | 信件状态必须为"待处理"（首次指派主办）或"受理中"（后续指派） | 4001001 | 信件状态不允许指派 |
| 主办单位校验 | 首次指派必须指定主办单位 | 4001002 | 首次指派必须指定主办单位 |
| 主办唯一性校验 | 信件已有主办单位时，不能再指派主办单位 | 4001003 | 该信件已有主办单位 |
| 部门唯一性校验 | 同一信件同一部门只能被指派一次 | 4001004 | 该部门已被指派，不能重复指派 |
| 办理单位有效性校验 | 办理单位必须有效且启用状态 | 4001005 | 该办理单位已禁用，无法指派 |
| 办理单位数量校验 | 同一信件办理单位数量不超过10个 | 4001006 | 办理单位数量已达上限 |

---

### 2.2 更新办理单位

**接口路径：** `PUT /mailbox/letter/handle-unit/update`
**权限标识：** `mailbox:handle-unit:update`
**功能说明：** 修改已指派办理单位的属性信息

#### 请求参数

```json
{
    "id": 1,                       // 必填，办理单位记录ID
    "unitRole": 1,                 // 可选，修改单位角色
    "unitStatus": 1,               // 可选，修改单位状态
    "flowStatus": null,            // 可选，修改流程状态
    "flowResult": "处理完成"       // 可选，修改流程结果
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": true
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 记录存在性校验 | 办理单位记录必须存在 | 4001011 | 办理单位记录不存在 |
| 信件状态校验 | 信件状态必须是可编辑状态（非已完成） | 4001012 | 信件已完成，办理单位信息不可修改 |
| 主办唯一性校验 | 修改为主办单位时，信件不能已有主办单位 | 4001013 | 该信件已有主办单位，请先更改原主办单位角色 |
| 回复状态校验 | 办理状态为"已提交"时，单位角色不可修改 | 4001014 | 该办理单位已提交回复，无法修改角色 |

---

### 2.3 删除办理单位

**接口路径：** `DELETE /mailbox/letter/handle-unit/delete`
**权限标识：** `mailbox:handle-unit:delete`
**功能说明：** 取消已指派的办理单位（软删除）

#### 请求参数

```
?id=1
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": true
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 记录存在性校验 | 办理单位记录必须存在 | 4001021 | 办理单位记录不存在 |
| 信件状态校验 | 信件状态非已完成 | 4001022 | 信件已完成，办理单位不可删除 |
| 回复状态校验 | 办理状态和回复状态为"未提交" | 4001023 | 该办理单位已提交回复，无法取消指派 |

---

### 2.4 批量删除办理单位

**接口路径：** `DELETE /mailbox/letter/handle-unit/batch-delete`
**权限标识：** `mailbox:handle-unit:delete`
**功能说明：** 批量取消指派多个办理单位

#### 请求参数

```json
{
    "ids": [1, 2, 3, 4, 5]         // 必填，办理单位记录ID列表（最多50个）
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "successCount": 3,         // 成功删除数量
        "failCount": 2,            // 失败数量
        "failDetails": [           // 失败详情列表
            {
                "id": 4,
                "reason": "该办理单位已提交回复，无法取消指派"
            },
            {
                "id": 5,
                "reason": "该办理单位已提交回复，无法取消指派"
            }
        ]
    }
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 选择数量校验 | 至少选择1条记录 | 4001031 | 请先选择要删除的办理单位 |
| 批量数量校验 | 单次批量删除上限50个 | 4001032 | 单次批量删除上限为50个，请分批操作 |

---

### 2.5 获取办理单位详情

**接口路径：** `GET /mailbox/letter/handle-unit/get`
**权限标识：** `mailbox:handle-unit:query`
**功能说明：** 获取办理单位的完整详细信息

#### 请求参数

```
?id=1
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 1,
        "letterId": 1001,
        "letterNo": "XL202604130001",
        "sourceUnitId": 100,
        "sourceUnitName": "市局信访室",
        "handleUnitId": 200,
        "handleUnitName": "交警大队",
        "unitRole": 0,
        "assignTime": "2026-04-13 10:30:00",
        "unitStatus": 0,
        "handleStatus": 1,
        "replyStatus": 1,
        "flowStatus": null,
        "flowResult": null,
        "replyTime": "2026-04-14 15:00:00",
        "handleTableId": 10,
        "replyTableId": 20,
        // 关联的回复内容（如存在）
        "replyInfo": {
            "id": 20,
            "replyContent": "经核实，您的诉求已处理完成...",
            "status": 1,
            "isClose": true
        },
        // 关联的办理表信息（如存在）
        "handleInfo": {
            "id": 10,
            "handlerName": "张警官",
            "overallSatisfaction": 1
        }
    }
}
```

---

### 2.6 分页查询办理单位

**接口路径：** `GET /mailbox/letter/handle-unit/page`
**权限标识：** `mailbox:handle-unit:query`
**功能说明：** 分页查询办理单位列表并支持筛选条件

#### 请求参数

```
?pageNo=1
&pageSize=10
&letterId=1001                // 可选，按信件ID筛选
&letterNo=XL202604130001      // 可选，按信件编号筛选
&handleUnitId=200             // 可选，按办理单位ID筛选
&unitRole=0                   // 可选，按单位角色筛选：0-主办，1-督办，2-协办
&unitStatus=0                 // 可选，按单位状态筛选：0-启用，1-禁用
&handleStatus=0               // 可选，按办理状态筛选
&replyStatus=0                // 可选，按回复状态筛选
&assignTimeBegin=2026-04-01   // 可选，指派时间开始范围
&assignTimeEnd=2026-04-30     // 可选，指派时间结束范围
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "total": 100,
        "pageNo": 1,
        "pageSize": 10,
        "pages": 10,
        "list": [
            {
                "id": 1,
                "letterId": 1001,
                "letterNo": "XL202604130001",
                "handleUnitId": 200,
                "handleUnitName": "交警大队",
                "unitRole": 0,
                "unitRoleName": "主办",
                "assignTime": "2026-04-13 10:30:00",
                "unitStatus": 0,
                "handleStatus": 0,
                "handleStatusName": "未提交"
            }
            // ... 更多记录
        ]
    }
}
```

---

### 2.7 获取办理单位列表

**接口路径：** `GET /mailbox/letter/handle-unit/list`
**权限标识：** `mailbox:handle-unit:query`
**功能说明：** 获取指定信件的办理单位简要信息列表

#### 请求参数

```
?letterId=1001                // 必填，信件ID
&includeDisabled=true         // 可选，是否包含禁用状态的办理单位
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": [
        {
            "id": 1,
            "handleUnitId": 200,
            "handleUnitName": "交警大队",
            "unitRole": 0,
            "unitRoleName": "主办",
            "handleStatus": 0,
            "handleStatusName": "未提交",
            "unitStatus": 0
        },
        {
            "id": 2,
            "handleUnitId": 300,
            "handleUnitName": "督察支队",
            "unitRole": 1,
            "unitRoleName": "督办",
            "handleStatus": 0,
            "handleStatusName": "未提交",
            "unitStatus": 0
        }
    ]
}
```

---

### 2.8 获取所有办理单位完整信息

**接口路径：** `GET /mailbox/letter/handle-unit/full-list`
**权限标识：** `mailbox:handle-unit:query`
**功能说明：** 获取指定信件所有办理单位的完整详细信息

#### 请求参数

```
?letterId=1001                // 必填，信件ID
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": [
        {
            "id": 1,
            "handleUnitId": 200,
            "handleUnitName": "交警大队",
            "unitRole": 0,
            "assignTime": "2026-04-13 10:30:00",
            "handleStatus": 1,
            "replyStatus": 1,
            "replyInfo": {
                "id": 20,
                "replyContent": "经核实，您的诉求已处理完成...",
                "upperReplyContent": "内部说明...",
                "status": 1,
                "isClose": true,
                "replyTime": "2026-04-14 15:00:00"
            },
            "handleInfo": {
                "id": 10,
                "handlerName": "张警官",
                "handlerIdCard": "***",
                "overallSatisfaction": 1,
                "investigateSituation": "已核实..."
            }
        }
        // ... 更多办理单位
    ]
}
```

---

### 2.9 导出办理单位Excel

**接口路径：** `GET /mailbox/letter/handle-unit/export-excel`
**权限标识：** `mailbox:handle-unit:export`
**功能说明：** 将办理单位信息导出为Excel文件

#### 请求参数

```
?letterId=1001                // 可选，按信件ID筛选
&unitRole=0                   // 可选，按单位角色筛选
&handleStatus=0               // 可选，按办理状态筛选
&assignTimeBegin=2026-04-01   // 可选，指派时间开始范围
&assignTimeEnd=2026-04-30     // 可选，指派时间结束范围
```

#### 响应结果

返回Excel文件流，文件名格式：`办理单位导出_20260413_143000.xlsx`

Excel列定义：

| 列名 | 数据来源 |
|------|----------|
| 序号 | 自动编号 |
| 信件编号 | letter_no |
| 信件标题 | 关联信件标题 |
| 办理单位名称 | handle_unit_name |
| 单位角色 | unit_role（转换为文字） |
| 指派时间 | assign_time |
| 办理状态 | handle_status（转换为文字） |
| 回复状态 | reply_status（转换为文字） |
| 答复时间 | reply_time |

---

## 3. 信件回复管理API

### 3.1 创建信件回复

**接口路径：** `POST /mailbox/letter/reply/create`
**权限标识：** `mailbox:letter-reply:create`
**功能说明：** 提交信件的正式回复内容，进入审核流程

#### 请求参数

```json
{
    "letterId": 1001,              // 必填，关联信件ID
    "letterNo": "XL202604130001",  // 必填，信件编号
    "handleUnitId": 1,             // 必填，关联办理单位ID
    "replyUnitId": 200,            // 必填，回复单位ID
    "receiveUnitId": 100,          // 可选，接收回复单位ID
    "replyContent": "经核实，您的诉求已处理完成...",  // 必填，回复内容（1-5000字符）
    "upperReplyContent": "内部说明...",   // 可选，上级回复内容
    "isClose": true,               // 必填，是否结案
    "suggestLetterType": "咨询",   // 可选，建议信件类型
    "isSuggestClose": false,       // 可选，是否建议结案
    "remark": ""                   // 可选，备注
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 20,                  // 回复记录ID
        "status": 1,               // 回复状态：待审核
        "createTime": "2026-04-14 15:00:00"
    }
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 权限校验 | 用户必须属于主办单位 | 4002001 | 仅主办单位可提交正式回复 |
| 信件状态校验 | 信件状态为"受理中" | 4002002 | 信件状态不允许提交回复 |
| 办理状态校验 | 办理单位办理状态为"未提交" | 4002003 | 您已提交回复，如需修改请使用修改答复功能 |
| 内容长度校验 | 回复内容1-5000字符 | 4002004 | 回复内容不能为空 |
| 内容长度校验 | 回复内容不超过5000字符 | 4002005 | 回复内容最长5000字符 |

---

### 3.2 创建信件回复草稿

**接口路径：** `POST /mailbox/letter/reply/save-draft`
**权限标识：** `mailbox:letter-reply:create`
**功能说明：** 保存回复内容的草稿而不提交审核

#### 请求参数

```json
{
    "letterId": 1001,
    "letterNo": "XL202604130001",
    "handleUnitId": 1,
    "replyUnitId": 200,
    "replyContent": "初步处理情况如下...",  // 可选，草稿内容可以为空或部分内容
    "upperReplyContent": "",
    "isClose": false,
    "remark": ""
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 20,
        "status": 0,               // 回复状态：草稿
        "createTime": "2026-04-14 10:00:00"
    }
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 权限校验 | 用户必须属于主办单位 | 4002011 | 仅主办单位可保存回复草稿 |
| 内容长度校验 | 回复内容不超过5000字符 | 4002012 | 回复内容最长5000字符 |

---

### 3.3 修改答复内容

**接口路径：** `PUT /mailbox/letter/reply/update`
**权限标识：** `mailbox:letter-reply:update`
**功能说明：** 修改已提交但未答复网民的回复内容

#### 请求参数

```json
{
    "id": 20,                      // 必填，回复记录ID
    "replyContent": "修改后的回复内容...",  // 必填，新的回复内容
    "upperReplyContent": "修改后的上级回复...",
    "isClose": true,
    "remark": ""
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": true
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 记录存在性校验 | 回复记录必须存在 | 4002021 | 回复记录不存在 |
| 权限校验 | 用户必须属于回复单位 | 4002022 | 您无权修改其他单位的回复内容 |
| 状态校验 | 回复状态为可修改状态 | 4002023 | 该回复已答复网民，不可修改 |
| 答复状态校验 | 是否答复网民为"否" | 4002024 | 该回复已答复网民，不可修改 |
| 内容长度校验 | 回复内容1-5000字符 | 4002025 | 回复内容不能为空 |

---

### 3.4 提交回复审核

**接口路径：** `POST /mailbox/letter/reply/submit`
**权限标识：** `mailbox:letter-reply:submit`
**功能说明：** 将草稿状态的回复提交审核

#### 请求参数

```json
{
    "id": 20                       // 必填，回复记录ID（草稿状态）
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 20,
        "status": 1                // 回复状态变更为：待审核
    }
}
```

---

### 3.5 删除回复草稿

**接口路径：** `DELETE /mailbox/letter/reply/delete-draft`
**权限标识：** `mailbox:letter-reply:delete`
**功能说明：** 删除回复草稿记录

#### 请求参数

```
?id=20
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": true
}
```

#### 业务规则校验

| 校验项 | 校验规则 | 错误码 | 错误信息 |
|--------|----------|--------|----------|
| 状态校验 | 回复状态必须为"草稿" | 4002031 | 仅草稿状态的回复可删除 |
| 权限校验 | 用户必须属于回复单位 | 4002032 | 您无权删除其他单位的回复草稿 |

---

### 3.6 获取回复详情

**接口路径：** `GET /mailbox/letter/reply/get`
**权限标识：** `mailbox:letter-reply:query`
**功能说明：** 获取回复记录的完整详细信息

#### 请求参数

```
?id=20
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 20,
        "letterId": 1001,
        "letterNo": "XL202604130001",
        "handleUnitId": 1,
        "replyUnitId": 200,
        "replyUnitName": "交警大队",
        "receiveUnitId": 100,
        "receiveUnitName": "市局信访室",
        "replyContent": "经核实，您的诉求已处理完成...",
        "upperReplyContent": "内部说明...",
        "isClose": true,
        "isReplyPublic": false,
        "isUpperAdopted": false,
        "suggestLetterType": "咨询",
        "isSuggestClose": false,
        "status": 1,
        "statusName": "待审核",
        "remark": "",
        "createTime": "2026-04-14 15:00:00",
        "updateTime": "2026-04-14 16:00:00"
    }
}
```

---

## 4. 信件办理管理API

### 4.1 获取信件办理详情

**接口路径：** `GET /mailbox/letter/handle/get`
**权限标识：** `mailbox:letter-handle:query`
**功能说明：** 获取办理过程中的详细信息

#### 请求参数

```
?id=10
或 ?letterId=1001&handleUnitId=1
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 10,
        "letterId": 1001,
        "letterNo": "XL202604130001",
        "handleUnitId": 1,
        // 回访信息
        "isVisitBack": true,
        "visitBackSituation": "已电话回访...",
        "isNeedReplyAgain": false,
        // 满意度评价
        "overallSatisfaction": 1,
        "overallSatisfactionName": "满意",
        // 调查情况
        "investigateSituation": "已核实情况...",
        "isStatementConsistent": true,
        // 审核信息
        "isAudit": true,
        "auditorName": "李审核",
        "auditOpinion": "审核通过",
        // 接访信息
        "isInterview": false,
        // 办理人信息
        "handlerName": "张警官",
        "handlerIdCard": "***",
        "handlerUnitName": "交警大队"
    }
}
```

---

### 4.2 创建信件办理信息

**接口路径：** `POST /mailbox/letter/handle/create`
**权限标识：** `mailbox:letter-handle:create`
**功能说明：** 创建办理过程中的详细信息

#### 请求参数

```json
{
    "letterId": 1001,
    "letterNo": "XL202604130001",
    "handleUnitId": 1,
    // 回访信息
    "isVisitBack": true,
    "noVisitReason": null,
    "visitBackSituation": "已电话回访，群众表示满意",
    "publicPhoneReflect": "群众反馈处理效果好",
    "isNeedReplyAgain": false,
    // 满意度评价
    "demandSolveSatisfaction": 1,
    "responseSpeedSatisfaction": 1,
    "serviceAttitudeSatisfaction": 1,
    "abilitySatisfaction": 1,
    "followServiceSatisfaction": 1,
    "overallSatisfaction": 1,
    // 调查情况
    "isStatementConsistent": true,
    "investigateSituation": "已核实情况属实",
    // 办理人信息
    "handlerName": "张警官",
    "handlerIdCard": "123456789012345678",
    "handlerUnitId": 200,
    "remark": ""
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "id": 10
    }
}
```

---

### 4.3 更新信件办理信息

**接口路径：** `PUT /mailbox/letter/handle/update`
**权限标识：** `mailbox:letter-handle:update`
**功能说明：** 更新办理过程中的详细信息

#### 请求参数

```json
{
    "id": 10,
    // 可更新的字段同创建接口
    "investigateSituation": "补充调查情况...",
    "handlerName": "张警官",
    "remark": "更新备注"
}
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": true
}
```

---

## 5. 流转记录管理API

### 5.1 获取流转记录列表

**接口路径：** `GET /fz/flow-record/list`
**权限标识：** `fz:flow-record:query`
**功能说明：** 获取信件的流转记录列表

#### 请求参数

```
?letterId=1001                // 必填，信件ID
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": [
        {
            "id": 100,
            "letterId": 1001,
            "letterNo": "XL202604130001",
            "handleType": "投诉",
            "assignDeptId": 100,
            "assignDeptName": "市局信访室",
            "assignUserId": 1001,
            "assignUserName": "张三",
            "sourceAssignDeptId": null,
            "parentFlowId": null,
            "processStatus": 3,
            "processStatusName": "已完结",
            "processContent": "指派交警大队处理",
            "processResult": "处理完成",
            "requireReplyTime": "2026-04-20 18:00:00",
            "actualReplyTime": "2026-04-14 15:00:00",
            "isOverdue": false,
            "delayCount": 0,
            "urgeCount": 0,
            "createTime": "2026-04-13 10:30:00"
        }
        // ... 更多流转记录
    ]
}
```

---

## 6. 信件流程管理API

### 6.1 获取信件流程列表

**接口路径：** `GET /fz/letter-flow/list`
**权限标识：** `fz:letter-flow:query`
**功能说明：** 获取信件的流程节点列表

#### 请求参数

```
?letterId=1001                // 必填，信件ID
```

#### 响应结果

```json
{
    "code": 0,
    "msg": "success",
    "data": [
        {
            "id": 1,
            "letterId": 1001,
            "letterNo": "XL202604130001",
            "flowType": "指派",
            "designateDeptId": 200,
            "designateDeptName": "交警大队",
            "responsibleDeptId": 200,
            "responsibleDeptName": "交警大队",
            "operatorDeptId": 100,
            "operatorDeptName": "市局信访室",
            "flowStatusCode": 1,
            "designateTime": "2026-04-13 10:30:00",
            "actualHandleTime": "2026-04-14 15:00:00",
            "handleContent": "指派交警大队处理",
            "handleResult": "处理完成",
            "createTime": "2026-04-13 10:30:00"
        }
        // ... 更多流程节点
    ]
}
```

---

## 7. 错误码定义

### 7.1 办理单位模块错误码（4001000-4001099）

| 错误码 | 错误信息 | 说明 |
|--------|----------|------|
| 4001001 | 信件状态不允许指派 | 信件状态不是"待处理"或"受理中" |
| 4001002 | 首次指派必须指定主办单位 | 信件无主办单位时首次指派必须为主办 |
| 4001003 | 该信件已有主办单位 | 不能重复指派主办单位 |
| 4001004 | 该部门已被指派，不能重复指派 | 部门唯一性校验失败 |
| 4001005 | 该办理单位已禁用，无法指派 | 办理单位状态为禁用 |
| 4001006 | 办理单位数量已达上限 | 同一信件最多10个办理单位 |
| 4001011 | 办理单位记录不存在 | 记录不存在或已删除 |
| 4001012 | 信件已完成，办理单位信息不可修改 | 信件状态为已完成 |
| 4001013 | 该信件已有主办单位，请先更改原主办单位角色 | 主办唯一性校验 |
| 4001014 | 该办理单位已提交回复，无法修改角色 | 办理状态为已提交 |
| 4001021 | 办理单位记录不存在 | 删除时记录不存在 |
| 4001022 | 信件已完成，办理单位不可删除 | 信件状态为已完成 |
| 4001023 | 该办理单位已提交回复，无法取消指派 | 已提交回复的办理单位不可删除 |
| 4001031 | 请先选择要删除的办理单位 | 批量删除选择为空 |
| 4001032 | 单次批量删除上限为50个，请分批操作 | 批量数量超限 |

### 7.2 信件回复模块错误码（4002000-4002099）

| 错误码 | 错误信息 | 说明 |
|--------|----------|------|
| 4002001 | 仅主办单位可提交正式回复 | 权限校验失败 |
| 4002002 | 信件状态不允许提交回复 | 信件状态不是"受理中" |
| 4002003 | 您已提交回复，如需修改请使用修改答复功能 | 办理状态已提交 |
| 4002004 | 回复内容不能为空 | 内容必填校验 |
| 4002005 | 回复内容最长5000字符 | 内容长度校验 |
| 4002011 | 仅主办单位可保存回复草稿 | 草稿权限校验 |
| 4002012 | 回复内容最长5000字符 | 草稿内容长度校验 |
| 4002021 | 回复记录不存在 | 记录不存在 |
| 4002022 | 您无权修改其他单位的回复内容 | 权限校验失败 |
| 4002023 | 该回复已答复网民，不可修改 | 状态校验失败 |
| 4002024 | 该回复已答复网民，不可修改 | 答复状态校验 |
| 4002025 | 回复内容不能为空 | 修改内容校验 |
| 4002031 | 仅草稿状态的回复可删除 | 状态校验 |
| 4002032 | 您无权删除其他单位的回复草稿 | 权限校验 |

### 7.3 信件办理模块错误码（4003000-4003099）

| 错误码 | 错误信息 | 说明 |
|--------|----------|------|
| 4003001 | 办理记录不存在 | 记录不存在 |
| 4003002 | 您无权修改其他单位的办理信息 | 权限校验失败 |

---

## 8. 通用响应格式

### 8.1 成功响应

```json
{
    "code": 0,
    "msg": "success",
    "data": { ... }
}
```

### 8.2 失败响应

```json
{
    "code": 4001001,
    "msg": "信件状态不允许指派",
    "data": null
}
```

### 8.3 分页响应

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "total": 100,
        "pageNo": 1,
        "pageSize": 10,
        "pages": 10,
        "list": [ ... ]
    }
}
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，完成M02信箱管理模块API设计 | 后端架构设计师 |