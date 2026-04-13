# M01 信件核心模块 - API设计

## 文档信息

**模块编号：** M01
**模块名称：** 信件核心模块
**文档版本：** v1.0
**创建日期：** 2026-04-13
**接口路径：** /mailbox/letter/{操作}
**权限标识：** mailbox:letter:{操作}

---

## 1. 接口概览

### 1.1 接口列表

| 序号 | 接口名称 | 接口路径 | HTTP方法 | 权限标识 | 说明 |
|------|----------|----------|----------|----------|------|
| 1 | 创建信件（测试） | /mailbox/letter/create-test | POST | mailbox:letter:create-test | 测试环境快速创建测试信件 |
| 2 | 创建信访件 | /mailbox/letter/create | POST | mailbox:letter:create | 正式创建信访件 |
| 3 | 获取信件详情 | /mailbox/letter/get | GET | mailbox:letter:query | 获取信件完整详细信息 |
| 4 | 更新信件属性 | /mailbox/letter/update | PUT | mailbox:letter:update | 修改信件属性信息 |
| 5 | 删除信件 | /mailbox/letter/delete | DELETE | mailbox:letter:delete | 软删除信件 |
| 6 | 批量删除信件 | /mailbox/letter/delete-list | DELETE | mailbox:letter:delete | 批量软删除信件 |
| 7 | 获取已删除信件列表 | /mailbox/letter/deleted-page | GET | mailbox:letter:query-deleted | 查看已删除信件列表 |
| 8 | 回滚删除的信件 | /mailbox/letter/restore | PUT | mailbox:letter:restore | 恢复已删除的信件 |
| 9 | 批量回滚删除的信件 | /mailbox/letter/restore-list | PUT | mailbox:letter:restore | 批量恢复已删除的信件 |
| 10 | 重新获取AI模板 | /mailbox/letter/ai-template | GET | mailbox:letter:ai-template | 重新获取AI生成的回复模板 |
| 11 | 高级检索信件 | /mailbox/letter/page | GET | mailbox:letter:query | 多条件组合检索信件 |
| 12 | 导出信件Excel | /mailbox/letter/export-excel | GET | mailbox:letter:export | 导出信件列表Excel |
| 13 | 获取信件办理单位列表 | /mailbox/letter/handle-unit/list | GET | mailbox:letter:query | 获取信件的办理单位列表 |
| 14 | 获取信件回复列表 | /mailbox/letter/reply/list | GET | mailbox:letter:query | 获取信件的回复列表 |
| 15 | 获取信件流转记录 | /mailbox/letter/flow-record/list | GET | mailbox:letter:query | 获取信件的流转记录 |
| 16 | 获取信件修改记录 | /mailbox/letter/modify-record/list | GET | mailbox:letter:query | 获取信件的修改记录 |
| 17 | 获取信件附件列表 | /mailbox/letter/attachment/list | GET | mailbox:letter:query | 获取信件的附件列表 |

---

## 2. 接口详细设计

### 2.1 创建信件（测试）

**功能点：** F01-01 创建信件（测试）

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/create-test |
| HTTP方法 | POST |
| 权限标识 | mailbox:letter:create-test |
| 接口描述 | 在测试环境中快速创建测试信件数据，用于系统测试和功能验证 |
| 使用场景 | 仅在测试环境可用，正式环境拒绝请求 |

#### 请求参数

**请求体：** LetterTestCreateReqVO

```json
{
    "source": "厅长信箱",
    "senderName": "张三",
    "senderIdCard": "440102199001011234",
    "senderPhone": "13800138000",
    "senderAddress": "广州市天河区XX路XX号",
    "title": "关于交通违章问题的咨询",
    "content": "来信内容详情...",
    "remark": "内部备注",
    "letterTime": "2026-04-13 10:00:00",
    "incidentArea": "广州市天河区",
    "incidentAreaCode": "440106",
    "incidentLocation": "天河路123号",
    "status": 0,
    "deptSiteNo": "SITE001"
}
```

| 字段名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| source | String | 是 | 数据来源 | 厅长信箱 |
| senderName | String | 是 | 来信人姓名 | 张三 |
| senderIdCard | String | 是 | 来信人身份证号（18位） | 440102199001011234 |
| senderPhone | String | 是 | 来信人手机号（11位） | 13800138000 |
| senderAddress | String | 否 | 来信人联系地址 | 广州市天河区XX路XX号 |
| title | String | 否 | 信件标题（最大200字符） | 关于交通违章问题的咨询 |
| content | String | 否 | 信件内容 | 来信内容详情... |
| remark | String | 否 | 内部备注（最大500字符） | 内部备注 |
| letterTime | LocalDateTime | 否 | 留言时间 | 2026-04-13 10:00:00 |
| incidentArea | String | 否 | 事发地区 | 广州市天河区 |
| incidentAreaCode | String | 否 | 事发地区代码 | 440106 |
| incidentLocation | String | 否 | 案发地点 | 天河路123号 |
| status | Integer | 否 | 信件状态（默认0待处理） | 0 |
| deptSiteNo | String | 否 | 部门站点编号 | SITE001 |

#### 响应结果

**响应体：** CommonResult<Long>

```json
{
    "code": 0,
    "msg": "",
    "data": 1001
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码（0表示成功） |
| msg | String | 错误提示信息 |
| data | Long | 新创建的信件ID |

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_001 | 此功能仅在测试环境可用 | 正式环境调用此接口 |
| 1_030_001_002 | 来信人姓名为必填项 | 缺少必填字段 |
| 1_030_001_003 | 身份证号格式错误 | 身份证号非18位或无效 |
| 1_030_001_004 | 手机号格式错误 | 手机号非11位 |
| 1_030_001_005 | 留言标题长度超过限制 | 标题超过200字符 |

---

### 2.2 创建信访件

**功能点：** F01-02 创建信访件

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/create |
| HTTP方法 | POST |
| 权限标识 | mailbox:letter:create |
| 接口描述 | 正式创建信访件并进入处理流程 |
| 使用场景 | 接收单位工作人员创建正式信访件 |

#### 请求参数

**请求体：** LetterCreateReqVO

```json
{
    "source": "厅长信箱",
    "handleNo": "XF202604001",
    "senderName": "张三",
    "senderIdCard": "440102199001011234",
    "senderPhone": "13800138000",
    "senderAddress": "广州市天河区XX路XX号",
    "title": "关于交通违章问题的咨询",
    "content": "来信内容详情...",
    "letterTime": "2026-04-13 10:00:00",
    "incidentArea": "广州市天河区",
    "incidentAreaCode": "440106",
    "incidentLocation": "天河路123号",
    "deptSiteNo": "SITE001",
    "deptOwnerNo": "DEPT001"
}
```

| 字段名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| source | String | 是 | 数据来源（字典值） | 厅长信箱 |
| handleNo | String | 否 | 外部系统办理编号 | XF202604001 |
| senderName | String | 是 | 来信人姓名 | 张三 |
| senderIdCard | String | 是 | 来信人身份证号 | 440102199001011234 |
| senderPhone | String | 是 | 来信人手机号 | 13800138000 |
| senderAddress | String | 否 | 来信人联系地址 | 广州市天河区XX路XX号 |
| title | String | 是 | 信件标题 | 关于交通违章问题的咨询 |
| content | String | 是 | 信件内容 | 来信内容详情... |
| letterTime | LocalDateTime | 否 | 留言时间 | 2026-04-13 10:00:00 |
| incidentArea | String | 否 | 事发地区 | 广州市天河区 |
| incidentAreaCode | String | 否 | 事发地区代码 | 440106 |
| incidentLocation | String | 否 | 案发地点 | 天河路123号 |
| deptSiteNo | String | 否 | 部门站点编号 | SITE001 |
| deptOwnerNo | String | 否 | 归属部门编号 | DEPT001 |

#### 响应结果

**响应体：** CommonResult<Long>

```json
{
    "code": 0,
    "msg": "",
    "data": 1001
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| msg | String | 错误提示信息 |
| data | Long | 新创建的信件ID |

#### 特殊业务逻辑

1. **重复件检测**：系统自动检测相同身份证号+相似标题的信件，标记为重复件
2. **预警牌关联**：根据留言来源自动关联对应的预警牌配置
3. **归属地解析**：根据身份证号自动解析来信人归属地

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_006 | 姓名为必填项 | 缺少姓名 |
| 1_030_001_007 | 标题为必填项 | 缺少标题 |
| 1_030_001_008 | 内容为必填项 | 缺少内容 |
| 1_030_001_009 | 留言来源为必填项 | 缺少来源 |

---

### 2.3 获取信件详情

**功能点：** F01-03 获取信件详情

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/get |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query |
| 接口描述 | 获取信件的完整详细信息 |
| 使用场景 | 查看信件详情，了解办理进度 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| id | Long | 是 | 信件ID | 1001 |
| includeDeleted | Boolean | 否 | 是否包含已删除信件（默认false） | false |

#### 响应结果

**响应体：** CommonResult<LetterDetailRespVO>

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "id": 1001,
        "letterNo": "XX202604001",
        "source": "厅长信箱",
        "handleNo": "XF202604001",
        "senderName": "张三",
        "senderIdCard": "440102********1234",
        "senderPhone": "138****8000",
        "senderAddress": "广州市天河区XX路XX号",
        "senderLocation": "广东省广州市",
        "title": "关于交通违章问题的咨询",
        "content": "来信内容详情...",
        "remark": "内部备注",
        "letterTime": "2026-04-13 10:00:00",
        "registerTime": "2026-04-13 10:05:00",
        "incidentArea": "广州市天河区",
        "incidentAreaCode": "440106",
        "incidentLocation": "天河路123号",
        "status": 1,
        "closeStatus": null,
        "isDuplicate": 0,
        "isRecorded": 1,
        "replyUnit": null,
        "replyContent": null,
        "replyTime": null,
        "satisfaction": null,
        "deptSiteNo": "SITE001",
        "deptOwnerNo": "DEPT001",
        "createTime": "2026-04-13 10:05:00",
        "handleUnits": [
            {
                "id": 101,
                "unitName": "XX分局",
                "unitCode": "UNIT001",
                "unitType": 1,
                "status": 1,
                "assignTime": "2026-04-13 10:10:00",
                "deadline": "2026-04-18 18:00:00"
            }
        ],
        "replies": [],
        "flowRecords": [
            {
                "id": 1,
                "operationType": "CREATE",
                "operationDesc": "创建信件",
                "operator": "管理员",
                "operatorUnit": "接收单位",
                "operationTime": "2026-04-13 10:05:00"
            }
        ],
        "tags": [],
        "attachments": []
    }
}
```

#### 响应字段说明

| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | Long | 信件ID |
| letterNo | String | 信件编号 |
| source | String | 数据来源 |
| handleNo | String | 办理编号 |
| senderName | String | 来信人姓名 |
| senderIdCard | String | 来信人身份证（根据权限脱敏） |
| senderPhone | String | 来信人手机号（根据权限脱敏） |
| senderAddress | String | 来信人联系地址 |
| senderLocation | String | 来信人归属地 |
| title | String | 信件标题 |
| content | String | 信件内容 |
| remark | String | 内部备注 |
| letterTime | LocalDateTime | 留言时间 |
| registerTime | LocalDateTime | 登记时间 |
| incidentArea | String | 事发地区 |
| incidentAreaCode | String | 事发地区代码 |
| incidentLocation | String | 案发地点 |
| status | Integer | 信件状态 |
| closeStatus | Integer | 结案状态 |
| isDuplicate | Integer | 是否重复件 |
| isRecorded | Integer | 是否录入 |
| replyUnit | String | 回复单位 |
| replyContent | String | 回复内容 |
| replyTime | LocalDateTime | 回复时间 |
| satisfaction | Integer | 满意度 |
| deptSiteNo | String | 部门站点编号 |
| deptOwnerNo | String | 归属部门编号 |
| createTime | LocalDateTime | 创建时间 |
| handleUnits | List | 办理单位列表 |
| replies | List | 回复列表 |
| flowRecords | List | 流转记录列表 |
| tags | List | 标签列表 |
| attachments | List | 附件列表 |

#### 权限敏感信息

| 角色 | 身份证号 | 手机号 | 流转记录 |
|------|----------|--------|----------|
| 接收单位 | 完整显示 | 完整显示 | 完整显示 |
| 主办单位 | 完整显示 | 完整显示 | 部分显示 |
| 督办单位 | 脱敏显示 | 脱敏显示 | 部分显示 |
| 普通用户 | 不显示 | 不显示 | 不显示 |

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_010 | 信件不存在 | 信件ID不存在 |
| 1_030_001_011 | 信件不存在或已删除 | 查询已删除信件但无权限 |

---

### 2.4 更新信件属性

**功能点：** F01-04 更新信件属性

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/update |
| HTTP方法 | PUT |
| 权限标识 | mailbox:letter:update |
| 接口描述 | 修改信件的属性信息 |
| 使用场景 | 接收单位完善或纠正信件信息 |

#### 请求参数

**请求体：** LetterUpdateReqVO

```json
{
    "id": 1001,
    "title": "更新后的标题",
    "content": "更新后的内容",
    "remark": "补充备注信息",
    "incidentArea": "广州市天河区",
    "incidentAreaCode": "440106",
    "incidentLocation": "新案发地点"
}
```

| 字段名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| id | Long | 是 | 信件ID | 1001 |
| title | String | 否 | 留言标题 | 更新后的标题 |
| content | String | 否 | 留言内容 | 更新后的内容 |
| remark | String | 否 | 留言备注 | 补充备注信息 |
| incidentArea | String | 否 | 事发地区 | 广州市天河区 |
| incidentAreaCode | String | 否 | 事发地区代码 | 440106 |
| incidentLocation | String | 否 | 案发地点 | 新案发地点 |

#### 响应结果

**响应体：** CommonResult<Boolean>

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 业务规则

1. **状态限制**：已完成的信件不允许修改
2. **字段限制**：信件编号、信件状态不允许直接修改
3. **权限限制**：只有接收单位可修改信件属性
4. **修改记录**：每次修改自动创建修改记录

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_012 | 已完成的信件不允许修改 | 信件状态为已完成 |
| 1_030_001_013 | 只有接收单位可修改信件属性 | 权限不足 |
| 1_030_001_014 | 信件编号不允许修改 | 尝试修改信件编号 |
| 1_030_001_015 | 信件状态不允许直接修改 | 尝试直接修改状态 |

---

### 2.5 删除信件

**功能点：** F01-05 删除信件

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/delete |
| HTTP方法 | DELETE |
| 权限标识 | mailbox:letter:delete |
| 接口描述 | 软删除信件 |
| 使用场景 | 管理员清理无效或错误数据 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| id | Long | 是 | 信件ID | 1001 |
| reason | String | 是 | 删除原因 | 测试数据清理 |

#### 响应结果

**响应体：** CommonResult<Boolean>

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_010 | 信件不存在 | 信件ID不存在 |
| 1_030_001_016 | 信件已被删除 | 重复删除 |
| 1_030_001_017 | 只有管理员可以删除信件 | 权限不足 |
| 1_030_001_018 | 删除原因必填 | 未提供删除原因 |

---

### 2.6 批量删除信件

**功能点：** F01-06 批量删除信件

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/delete-list |
| HTTP方法 | DELETE |
| 权限标识 | mailbox:letter:delete |
| 接口描述 | 批量软删除信件 |
| 使用场景 | 管理员批量清理数据 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| ids | List<Long> | 是 | 信件ID列表（最大100条） | [1001, 1002, 1003] |
| reason | String | 是 | 删除原因 | 批量清理测试数据 |

#### 响应结果

**响应体：** CommonResult<LetterBatchDeleteRespVO>

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "successCount": 2,
        "skipCount": 1,
        "failCount": 0,
        "failDetails": []
    }
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| successCount | Integer | 成功删除数量 |
| skipCount | Integer | 跳过数量（已删除） |
| failCount | Integer | 失败数量 |
| failDetails | List | 失败详情列表 |

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_019 | 信件编号列表不能为空 | 空列表 |
| 1_030_001_020 | 批量删除数量不能超过100条 | 数量超限 |

---

### 2.7 获取已删除信件列表

**功能点：** F01-07 获取已删除信件

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/deleted-page |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query-deleted |
| 接口描述 | 查看已删除的信件列表 |
| 使用场景 | 管理员管理和审查已删除数据 |

#### 请求参数

**请求体：** LetterDeletedPageReqVO

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| pageNo | Integer | 否 | 页码（默认1） | 1 |
| pageSize | Integer | 否 | 每页条数（默认20） | 20 |
| letterNo | String | 否 | 信件编号 | XX202604001 |
| title | String | 否 | 信件标题（模糊） | 交通 |
| source | String | 否 | 数据来源 | 厅长信箱 |
| deleteTimeStart | LocalDateTime | 否 | 删除时间开始 | 2026-04-01 00:00:00 |
| deleteTimeEnd | LocalDateTime | 否 | 删除时间结束 | 2026-04-10 23:59:59 |
| deleteReason | String | 否 | 删除原因（模糊） | 测试 |

#### 响应结果

**响应体：** CommonResult<PageResult<LetterDeletedRespVO>>

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "total": 50,
        "list": [
            {
                "id": 1001,
                "letterNo": "XX202604001",
                "title": "关于交通违章问题的咨询",
                "source": "厅长信箱",
                "status": 1,
                "deleteTime": "2026-04-05 10:00:00",
                "deleteReason": "测试数据清理",
                "deleter": "管理员"
            }
        ]
    }
}
```

---

### 2.8 回滚删除的信件

**功能点：** F01-08 回滚删除的信件

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/restore |
| HTTP方法 | PUT |
| 权限标识 | mailbox:letter:restore |
| 接口描述 | 恢复已删除的信件 |
| 使用场景 | 管理员撤销错误的删除操作 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| id | Long | 是 | 信件ID | 1001 |
| reason | String | 否 | 回滚原因 | 误删，需恢复处理 |

#### 响应结果

**响应体：** CommonResult<Boolean>

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_010 | 信件不存在 | 信件ID不存在 |
| 1_030_001_021 | 该信件未被删除，无需回滚 | 信件未删除 |
| 1_030_001_022 | 只有管理员可回滚已删除信件 | 权限不足 |

---

### 2.9 批量回滚删除的信件

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/restore-list |
| HTTP方法 | PUT |
| 权限标识 | mailbox:letter:restore |
| 接口描述 | 批量恢复已删除的信件 |
| 使用场景 | 管理员批量恢复信件 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| ids | List<Long> | 是 | 信件ID列表 | [1001, 1002] |
| reason | String | 否 | 回滚原因 | 批量恢复 |

#### 响应结果

**响应体：** CommonResult<LetterBatchRestoreRespVO>

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "successCount": 2,
        "failCount": 0,
        "failDetails": []
    }
}
```

---

### 2.10 重新获取AI模板

**功能点：** F01-09 重新获取AI模板

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/ai-template |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:ai-template |
| 接口描述 | 重新获取AI生成的回复模板 |
| 使用场景 | 办理单位获取回复建议 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| id | Long | 是 | 信件ID | 1001 |
| saveDraft | Boolean | 否 | 是否保存为草稿（默认false） | true |

#### 响应结果

**响应体：** CommonResult<LetterAiTemplateRespVO>

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "templateContent": "AI生成的回复内容...",
        "version": "v2.0",
        "generateTime": "2026-04-13 11:00:00",
        "savedToDraft": true
    }
}
```

#### 错误码

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_023 | 只有办理单位可获取AI模板 | 权限不足 |
| 1_030_001_024 | 已完成信件无法重新获取AI模板 | 信件状态为已完成 |
| 1_030_001_025 | AI服务暂时不可用，请稍后重试 | AI服务不可用 |

---

### 2.11 高级检索信件

**功能点：** F01-10 高级检索

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/page |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query |
| 接口描述 | 多条件组合检索信件 |
| 使用场景 | 快速查找信件 |

#### 请求参数

**请求体：** LetterPageReqVO

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| pageNo | Integer | 否 | 页码（默认1） | 1 |
| pageSize | Integer | 否 | 每页条数（默认10） | 10 |
| letterNo | String | 否 | 信件编号 | XX202604001 |
| source | String | 否 | 数据来源 | 厅长信箱 |
| status | Integer | 否 | 信件状态 | 0 |
| statusList | List<Integer> | 否 | 信件状态列表 | [0, 1] |
| title | String | 否 | 信件标题（模糊） | 交通 |
| content | String | 否 | 信件内容（模糊） | 答复 |
| senderName | String | 否 | 来信人姓名 | 张三 |
| senderIdCard | String | 否 | 来信人身份证号 | 440102... |
| senderPhone | String | 否 | 来信人手机号 | 138... |
| registerTimeStart | LocalDateTime | 否 | 登记时间开始 | 2026-04-01 |
| registerTimeEnd | LocalDateTime | 否 | 登记时间结束 | 2026-04-30 |
| incidentArea | String | 否 | 事发地区 | 广州市 |
| incidentAreaCode | String | 否 | 事发地区代码 | 440106 |
| mainHandleUnit | String | 否 | 主办单位编码 | UNIT001 |
| superviseUnit | String | 否 | 督办单位编码 | UNIT002 |
| assistUnit | String | 否 | 协办单位编码 | UNIT003 |
| satisfaction | Integer | 否 | 满意度 | 1 |
| warningStatus | Integer | 否 | 预警状态 | 1 |
| isDuplicate | Integer | 否 | 是否重复件 | 1 |
| tagId | Long | 否 | 标签ID | 10 |
| closeStatus | Integer | 否 | 结案状态 | 1 |
| keyword | String | 否 | 全文关键词 | 交通违章 |

#### 响应结果

**响应体：** CommonResult<PageResult<LetterRespVO>>

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "total": 100,
        "list": [
            {
                "id": 1001,
                "letterNo": "XX202604001",
                "source": "厅长信箱",
                "title": "关于交通违章问题的咨询",
                "senderName": "张三",
                "status": 1,
                "registerTime": "2026-04-13 10:05:00",
                "createTime": "2026-04-13 10:05:00"
            }
        ]
    }
}
```

---

### 2.12 导出信件Excel

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/export-excel |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:export |
| 接口描述 | 导出信件列表Excel |
| 使用场景 | 批量导出信件数据 |

#### 请求参数

同高级检索接口 LetterPageReqVO

#### 响应结果

直接输出Excel文件流，文件名：信件列表.xls

---

### 2.13 获取信件办理单位列表

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/handle-unit/list |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query |
| 接口描述 | 获取信件的办理单位列表 |
| 使用场景 | 查看信件办理单位信息 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| letterId | Long | 是 | 信件ID | 1001 |

#### 响应结果

**响应体：** CommonResult<List<LetterHandleUnitRespVO>>

```json
{
    "code": 0,
    "msg": "",
    "data": [
        {
            "id": 101,
            "letterId": 1001,
            "unitName": "XX分局",
            "unitCode": "UNIT001",
            "unitType": 1,
            "status": 1,
            "assignTime": "2026-04-13 10:10:00",
            "deadline": "2026-04-18 18:00:00"
        }
    ]
}
```

---

### 2.14 获取信件回复列表

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/reply/list |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query |
| 接口描述 | 获取信件的回复列表 |
| 使用场景 | 查看信件回复历史 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| letterId | Long | 是 | 信件ID | 1001 |

#### 响应结果

**响应体：** CommonResult<List<LetterReplyRespVO>>

---

### 2.15 获取信件流转记录

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/flow-record/list |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query |
| 接口描述 | 获取信件的流转记录 |
| 使用场景 | 查看信件处理流程 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| letterId | Long | 是 | 信件ID | 1001 |

#### 响应结果

**响应体：** CommonResult<List<LetterFlowRecordRespVO>>

---

### 2.16 获取信件修改记录

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/modify-record/list |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query |
| 接口描述 | 获取信件的修改记录 |
| 使用场景 | 查看信件修改历史 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| letterId | Long | 是 | 信件ID | 1001 |

#### 响应结果

**响应体：** CommonResult<List<LetterModifyRecordRespVO>>

---

### 2.17 获取信件附件列表

#### 基本信息

| 项目 | 内容 |
|------|------|
| 接口路径 | /mailbox/letter/attachment/list |
| HTTP方法 | GET |
| 权限标识 | mailbox:letter:query |
| 接口描述 | 获取信件的附件列表 |
| 使用场景 | 查看信件相关附件 |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| letterId | Long | 是 | 信件ID | 1001 |
| attachType | Integer | 否 | 附件类型（1来信/2回复） | 1 |

#### 响应结果

**响应体：** CommonResult<List<LetterAttachmentRespVO>>

---

## 3. 权限SQL配置

```sql
-- 创建信件管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('信件管理', '', 2, 0, {父菜单ID}, 'letter', 'ep:document', 'mailbox/letter/index', 0);

-- 创建信件管理按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('信件查询', 'mailbox:letter:query', 3, 1, @menuId, 0),
('信件创建', 'mailbox:letter:create', 3, 2, @menuId, 0),
('信件测试创建', 'mailbox:letter:create-test', 3, 3, @menuId, 0),
('信件修改', 'mailbox:letter:update', 3, 4, @menuId, 0),
('信件删除', 'mailbox:letter:delete', 3, 5, @menuId, 0),
('信件导出', 'mailbox:letter:export', 3, 6, @menuId, 0),
('已删除信件查询', 'mailbox:letter:query-deleted', 3, 7, @menuId, 0),
('信件恢复', 'mailbox:letter:restore', 3, 8, @menuId, 0),
('AI模板获取', 'mailbox:letter:ai-template', 3, 9, @menuId, 0);
```

---

## 4. 错误码汇总

| 错误码 | 错误信息 | 场景 |
|--------|----------|------|
| 1_030_001_001 | 此功能仅在测试环境可用 | 正式环境调用测试接口 |
| 1_030_001_002 | 来信人姓名为必填项 | 缺少必填字段 |
| 1_030_001_003 | 身份证号格式错误 | 身份证格式不正确 |
| 1_030_001_004 | 手机号格式错误 | 手机号格式不正确 |
| 1_030_001_005 | 留言标题长度超过限制 | 标题超长 |
| 1_030_001_006 | 姓名为必填项 | 创建信访件缺少姓名 |
| 1_030_001_007 | 标题为必填项 | 创建信访件缺少标题 |
| 1_030_001_008 | 内容为必填项 | 创建信访件缺少内容 |
| 1_030_001_009 | 留言来源为必填项 | 创建信访件缺少来源 |
| 1_030_001_010 | 信件不存在 | 信件ID不存在 |
| 1_030_001_011 | 信件不存在或已删除 | 查询已删除信件无权限 |
| 1_030_001_012 | 已完成的信件不允许修改 | 信件状态限制 |
| 1_030_001_013 | 只有接收单位可修改信件属性 | 权限限制 |
| 1_030_001_014 | 信件编号不允许修改 | 字段限制 |
| 1_030_001_015 | 信件状态不允许直接修改 | 字段限制 |
| 1_030_001_016 | 信件已被删除 | 重复删除 |
| 1_030_001_017 | 只有管理员可以删除信件 | 权限限制 |
| 1_030_001_018 | 删除原因必填 | 缺少删除原因 |
| 1_030_001_019 | 信件编号列表不能为空 | 批量删除空列表 |
| 1_030_001_020 | 批量删除数量不能超过100条 | 数量超限 |
| 1_030_001_021 | 该信件未被删除，无需回滚 | 回滚未删除信件 |
| 1_030_001_022 | 只有管理员可回滚已删除信件 | 权限限制 |
| 1_030_001_023 | 只有办理单位可获取AI模板 | 权限限制 |
| 1_030_001_024 | 已完成信件无法重新获取AI模板 | 状态限制 |
| 1_030_001_025 | AI服务暂时不可用，请稍后重试 | AI服务不可用 |

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，完成17个接口设计 | Claude |