# M04 标签分类模块 - API设计

## 文档信息

**产品名称：** gaxx-pro 信件处理系统
**文档版本：** v1.0
**创建日期：** 2026-04-13
**接口规范：** 参考 api-designer.yaml

---

## 1. API接口总览

### 1.1 接口模块划分

| 模块 | 路径前缀 | 说明 |
|------|----------|------|
| 标签分组管理 | /mailbox/label/tag-group | 标签分组的增删改查 |
| 标签管理 | /mailbox/label/tag | 标签的增删改查 |
| 标签关联管理 | /mailbox/label/tag-rel | 信件与标签的关联操作 |

### 1.2 权限标识规范

| 权限标识格式 | 说明 |
|--------------|------|
| mailbox:tag-group:{操作} | 标签分组管理权限 |
| mailbox:tag:{操作} | 标签管理权限 |
| mailbox:tag-rel:{操作} | 标签关联管理权限 |

---

## 2. 标签分组管理接口

### 2.1 接口列表

| 端点 | HTTP方法 | 权限标识 | 说明 |
|------|----------|----------|------|
| /mailbox/label/tag-group/create | POST | mailbox:tag-group:create | 创建标签分组 |
| /mailbox/label/tag-group/update | PUT | mailbox:tag-group:update | 更新标签分组 |
| /mailbox/label/tag-group/delete | DELETE | mailbox:tag-group:delete | 删除标签分组 |
| /mailbox/label/tag-group/delete-list | DELETE | mailbox:tag-group:delete | 批量删除标签分组 |
| /mailbox/label/tag-group/get | GET | mailbox:tag-group:query | 获取标签分组详情 |
| /mailbox/label/tag-group/list | GET | mailbox:tag-group:query | 获取标签分组列表 |
| /mailbox/label/tag-group/update-status | PUT | mailbox:tag-group:update | 更新分组状态 |

### 2.2 接口详细设计

#### 2.2.1 创建标签分组

**接口路径：** `POST /mailbox/label/tag-group/create`

**权限标识：** `mailbox:tag-group:create`

**请求参数：**

```json
{
    "groupCode": "GRP_001",           // 分组编码（可选，不填则自动生成）
    "groupName": "信访类",             // 分组名称（必填）
    "sort": 1,                         // 排序号（可选，默认0）
    "status": 1                        // 状态（可选，默认1启用）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": 1  // 分组ID
}
```

#### 2.2.2 更新标签分组

**接口路径：** `PUT /mailbox/label/tag-group/update`

**权限标识：** `mailbox:tag-group:update`

**请求参数：**

```json
{
    "id": 1,                           // 分组ID（必填）
    "groupCode": "GRP_001",            // 分组编码（可选）
    "groupName": "信访类-修改",         // 分组名称（必填）
    "sort": 2,                         // 排序号（可选）
    "status": 1                        // 状态（可选）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 2.2.3 删除标签分组

**接口路径：** `DELETE /mailbox/label/tag-group/delete`

**权限标识：** `mailbox:tag-group:delete`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 分组ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 2.2.4 批量删除标签分组

**接口路径：** `DELETE /mailbox/label/tag-group/delete-list`

**权限标识：** `mailbox:tag-group:delete`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| ids | List<Long> | 是 | 分组ID列表 |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 2.2.5 获取标签分组详情

**接口路径：** `GET /mailbox/label/tag-group/get`

**权限标识：** `mailbox:tag-group:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 分组ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "id": 1,
        "groupCode": "GRP_001",
        "groupName": "信访类",
        "sort": 1,
        "status": 1,
        "createTime": "2026-04-13 10:00:00"
    }
}
```

#### 2.2.6 获取标签分组列表

**接口路径：** `GET /mailbox/label/tag-group/list`

**权限标识：** `mailbox:tag-group:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| status | Integer | 否 | 状态过滤 |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": [
        {
            "id": 1,
            "groupCode": "GRP_001",
            "groupName": "信访类",
            "sort": 1,
            "status": 1,
            "tagCount": 5,        // 分组下标签数量
            "createTime": "2026-04-13 10:00:00"
        }
    ]
}
```

#### 2.2.7 更新分组状态

**接口路径：** `PUT /mailbox/label/tag-group/update-status`

**权限标识：** `mailbox:tag-group:update`

**请求参数：**

```json
{
    "id": 1,          // 分组ID（必填）
    "status": 0       // 状态（必填：0禁用/1启用）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

---

## 3. 标签管理接口

### 3.1 接口列表

| 端点 | HTTP方法 | 权限标识 | 说明 |
|------|----------|----------|------|
| /mailbox/label/tag/create | POST | mailbox:tag:create | 创建标签 |
| /mailbox/label/tag/update | PUT | mailbox:tag:update | 更新标签 |
| /mailbox/label/tag/delete | DELETE | mailbox:tag:delete | 删除标签 |
| /mailbox/label/tag/delete-list | DELETE | mailbox:tag:delete | 批量删除标签 |
| /mailbox/label/tag/get | GET | mailbox:tag:query | 获取标签详情 |
| /mailbox/label/tag/page | GET | mailbox:tag:query | 分页查询标签列表 |
| /mailbox/label/tag/list | GET | mailbox:tag:query | 获取标签列表（不分页） |
| /mailbox/label/tag/list-by-letter | GET | mailbox:tag:query | 获取可用于信件的标签列表 |
| /mailbox/label/tag/update-status | PUT | mailbox:tag:update | 更新标签状态 |
| /mailbox/label/tag/sort | PUT | mailbox:tag:update | 更新标签排序 |
| /mailbox/label/tag/export-excel | GET | mailbox:tag:export | 导出标签Excel |

### 3.2 接口详细设计

#### 3.2.1 创建标签

**接口路径：** `POST /mailbox/label/tag/create`

**权限标识：** `mailbox:tag:create`

**请求参数：**

```json
{
    "tagCode": "TAG_001",              // 标签编码（可选，不填则自动生成）
    "tagName": "重点信访",              // 标签名称（必填，2-20字符）
    "tagColor": "#FF0000",             // 标签颜色（可选，默认#1890FF）
    "status": 1,                       // 状态（可选，默认1启用）
    "isCritical": true,                // 是否重点（可选，默认false）
    "isListVisible": true,             // 列表可见（可选，默认true）
    "visibilityScope": 0,              // 可见范围级别（可选，默认0全局）
    "visibleDeptIds": "",              // 可见部门ID列表（可选，逗号分隔）
    "groupId": 1,                      // 所属分组ID（可选）
    "sort": 1                          // 排序号（可选，默认0）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": 1  // 标签ID
}
```

#### 3.2.2 更新标签

**接口路径：** `PUT /mailbox/label/tag/update`

**权限标识：** `mailbox:tag:update`

**请求参数：**

```json
{
    "id": 1,                           // 标签ID（必填）
    "tagCode": "TAG_001",              // 标签编码（可选）
    "tagName": "重点关注",              // 标签名称（必填，2-20字符）
    "tagColor": "#0000FF",             // 标签颜色（可选）
    "status": 1,                       // 状态（可选）
    "isCritical": true,                // 是否重点（可选）
    "isListVisible": true,             // 列表可见（可选）
    "visibilityScope": 0,              // 可见范围级别（可选）
    "visibleDeptIds": "",              // 可见部门ID列表（可选）
    "groupId": 1,                      // 所属分组ID（可选）
    "sort": 2                          // 排序号（可选）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 3.2.3 删除标签

**接口路径：** `DELETE /mailbox/label/tag/delete`

**权限标识：** `mailbox:tag:delete`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 标签ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

**业务说明：** 删除标签时会同时删除所有标签关联记录，并返回关联信件数量提示。

#### 3.2.4 批量删除标签

**接口路径：** `DELETE /mailbox/label/tag/delete-list`

**权限标识：** `mailbox:tag:delete`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| ids | List<Long> | 是 | 标签ID列表 |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 3.2.5 获取标签详情

**接口路径：** `GET /mailbox/label/tag/get`

**权限标识：** `mailbox:tag:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 标签ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "id": 1,
        "tagCode": "TAG_001",
        "tagName": "重点信访",
        "tagColor": "#FF0000",
        "status": 1,
        "isCritical": true,
        "isListVisible": true,
        "visibilityScope": 0,
        "visibleDeptIds": "",
        "groupId": 1,
        "groupName": "信访类",
        "useCount": 5,
        "sort": 1,
        "createTime": "2026-04-13 10:00:00",
        "creator": "admin"
    }
}
```

#### 3.2.6 分页查询标签列表

**接口路径：** `GET /mailbox/label/tag/page`

**权限标识：** `mailbox:tag:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码（默认1） |
| pageSize | Integer | 否 | 每页条数（默认10） |
| tagName | String | 否 | 标签名称（模糊搜索） |
| tagCode | String | 否 | 标签编码（模糊搜索） |
| status | Integer | 否 | 状态过滤 |
| isCritical | Boolean | 否 | 是否重点过滤 |
| visibilityScope | Integer | 否 | 可见范围级别过滤 |
| groupId | Long | 否 | 分组ID过滤 |
| createTime | String[] | 否 | 创建时间范围 |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "total": 100,
        "list": [
            {
                "id": 1,
                "tagCode": "TAG_001",
                "tagName": "重点信访",
                "tagColor": "#FF0000",
                "status": 1,
                "isCritical": true,
                "isListVisible": true,
                "visibilityScope": 0,
                "groupId": 1,
                "groupName": "信访类",
                "useCount": 5,
                "sort": 1,
                "createTime": "2026-04-13 10:00:00"
            }
        ]
    }
}
```

#### 3.2.7 获取标签列表（不分页）

**接口路径：** `GET /mailbox/label/tag/list`

**权限标识：** `mailbox:tag:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| status | Integer | 否 | 状态过滤（默认仅启用状态） |
| groupId | Long | 否 | 分组ID过滤 |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": [
        {
            "id": 1,
            "tagCode": "TAG_001",
            "tagName": "重点信访",
            "tagColor": "#FF0000",
            "isCritical": true,
            "useCount": 5
        }
    ]
}
```

#### 3.2.8 获取可用于信件的标签列表

**接口路径：** `GET /mailbox/label/tag/list-by-letter`

**权限标识：** `mailbox:tag:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| letterId | Long | 是 | 信件ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": [
        {
            "id": 1,
            "tagName": "重点信访",
            "tagColor": "#FF0000",
            "isCritical": true,
            "isLinked": false    // 是否已关联该信件
        },
        {
            "id": 2,
            "tagName": "紧急处理",
            "tagColor": "#FFA500",
            "isCritical": false,
            "isLinked": true     // 已关联
        }
    ]
}
```

**业务说明：** 根据用户所属部门过滤可见标签，仅返回启用状态的标签。

#### 3.2.9 更新标签状态

**接口路径：** `PUT /mailbox/label/tag/update-status`

**权限标识：** `mailbox:tag:update`

**请求参数：**

```json
{
    "id": 1,          // 标签ID（必填）
    "status": 0       // 状态（必填：0禁用/1启用）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 3.2.10 更新标签排序

**接口路径：** `PUT /mailbox/label/tag/sort`

**权限标识：** `mailbox:tag:update`

**请求参数：**

```json
{
    "tags": [
        { "id": 1, "sort": 1 },
        { "id": 2, "sort": 2 },
        { "id": 3, "sort": 3 }
    ]
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

---

## 4. 标签关联管理接口

### 4.1 接口列表

| 端点 | HTTP方法 | 权限标识 | 说明 |
|------|----------|----------|------|
| /mailbox/label/tag-rel/add | POST | mailbox:tag-rel:create | 为信件添加标签 |
| /mailbox/label/tag-rel/add-list | POST | mailbox:tag-rel:create | 为信件批量添加标签 |
| /mailbox/label/tag-rel/remove | DELETE | mailbox:tag-rel:delete | 移除信件标签 |
| /mailbox/label/tag-rel/remove-all | DELETE | mailbox:tag-rel:delete | 移除信件所有标签 |
| /mailbox/label/tag-rel/list-by-letter | GET | mailbox:tag-rel:query | 获取信件的标签列表 |
| /mailbox/label/tag-rel/list-by-tag | GET | mailbox:tag-rel:query | 获取标签关联的信件列表 |
| /mailbox/label/tag-rel/page-by-tag | GET | mailbox:tag-rel:query | 分页查询标签关联的信件 |

### 4.2 接口详细设计

#### 4.2.1 为信件添加标签

**接口路径：** `POST /mailbox/label/tag-rel/add`

**权限标识：** `mailbox:tag-rel:create`

**请求参数：**

```json
{
    "letterId": 1,     // 信件ID（必填）
    "tagId": 1         // 标签ID（必填）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

**业务说明：**
- 验证信件是否存在
- 验证标签是否存在且启用
- 验证标签对当前用户可见
- 验证信件未重复关联该标签
- 验证信件标签数量不超过上限（10个）

#### 4.2.2 为信件批量添加标签

**接口路径：** `POST /mailbox/label/tag-rel/add-list`

**权限标识：** `mailbox:tag-rel:create`

**请求参数：**

```json
{
    "letterId": 1,              // 信件ID（必填）
    "tagIds": [1, 2, 3]         // 标签ID列表（必填）
}
```

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "successCount": 3,      // 成功添加数量
        "failedCount": 0,       // 失败数量
        "failedTags": []        // 失败的标签信息
    }
}
```

#### 4.2.3 移除信件标签

**接口路径：** `DELETE /mailbox/label/tag-rel/remove`

**权限标识：** `mailbox:tag-rel:delete`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| letterId | Long | 是 | 信件ID |
| tagId | Long | 是 | 标签ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 4.2.4 移除信件所有标签

**接口路径：** `DELETE /mailbox/label/tag-rel/remove-all`

**权限标识：** `mailbox:tag-rel:delete`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| letterId | Long | 是 | 信件ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": true
}
```

#### 4.2.5 获取信件的标签列表

**接口路径：** `GET /mailbox/label/tag-rel/list-by-letter`

**权限标识：** `mailbox:tag-rel:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| letterId | Long | 是 | 信件ID |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": [
        {
            "id": 1,
            "tagId": 1,
            "tagName": "重点信访",
            "tagColor": "#FF0000",
            "isCritical": true,
            "ownerDeptId": 100,
            "ownerDeptName": "信访处",
            "createTime": "2026-04-13 10:00:00"
        }
    ]
}
```

#### 4.2.6 获取标签关联的信件列表

**接口路径：** `GET /mailbox/label/tag-rel/list-by-tag`

**权限标识：** `mailbox:tag-rel:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| tagId | Long | 是 | 标签ID |
| limit | Integer | 否 | 返回数量限制（默认20） |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": [
        {
            "letterId": 1,
            "letterCode": "XX202604001",
            "letterTitle": "关于某某问题的信访",
            "linkTime": "2026-04-13 10:00:00"
        }
    ]
}
```

#### 4.2.7 分页查询标签关联的信件

**接口路径：** `GET /mailbox/label/tag-rel/page-by-tag`

**权限标识：** `mailbox:tag-rel:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码（默认1） |
| pageSize | Integer | 否 | 每页条数（默认10） |
| tagId | Long | 是 | 标签ID |
| ownerDeptId | Long | 否 | 所属部门过滤 |
| createTime | String[] | 否 | 关联时间范围 |

**响应结果：**

```json
{
    "code": 0,
    "msg": "",
    "data": {
        "total": 50,
        "list": [
            {
                "letterId": 1,
                "letterCode": "XX202604001",
                "letterTitle": "关于某某问题的信访",
                "ownerDeptId": 100,
                "ownerDeptName": "信访处",
                "linkTime": "2026-04-13 10:00:00"
            }
        ]
    }
}
```

---

## 5. 错误码定义

### 5.1 标签分组错误码

| 错误码 | 错误信息 | 说明 |
|--------|----------|------|
| 1_0XX_001_000 | 标签分组不存在 | 查询或操作不存在分组 |
| 1_0XX_001_001 | 已存在该编码的标签分组 | 创建重复编码分组 |
| 1_0XX_001_002 | 已存在该名称的标签分组 | 创建重复名称分组 |
| 1_0XX_001_003 | 标签分组下存在标签，无法删除 | 删除包含标签的分组 |

### 5.2 标签错误码

| 错误码 | 错误信息 | 说明 |
|--------|----------|------|
| 1_0XX_002_000 | 标签不存在 | 查询或操作不存在标签 |
| 1_0XX_002_001 | 已存在该编码的标签 | 创建重复编码标签 |
| 1_0XX_002_002 | 已存在该名称的标签 | 创建重复名称标签 |
| 1_0XX_002_003 | 标签颜色格式不正确 | 颜色值非有效HEX格式 |
| 1_0XX_002_004 | 标签名称长度不符合要求 | 名称不在2-20字符范围内 |
| 1_0XX_002_005 | 标签已禁用，无法添加到信件 | 添加禁用状态标签 |
| 1_0XX_002_006 | 标签对当前用户不可见 | 添加不在可见范围的标签 |
| 1_0XX_002_007 | 标签状态不正确 | 更新状态参数错误 |

### 5.3 标签关联错误码

| 错误码 | 错误信息 | 说明 |
|--------|----------|------|
| 1_0XX_003_000 | 信件不存在 | 添加标签时信件不存在 |
| 1_0XX_003_001 | 该信件已关联此标签 | 重复添加同一标签 |
| 1_0XX_003_002 | 信件标签数量超过上限 | 超过10个标签上限 |
| 1_0XX_003_003 | 该信件未关联此标签 | 移除不存在的关联 |
| 1_0XX_003_004 | 标签关联不存在 | 操作不存在的关联记录 |

---

## 6. 权限SQL配置

### 6.1 标签分组管理权限

```sql
-- 标签分组管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('标签分组管理', '', 2, 1, {父菜单ID}, 'tag-group', 'ep:folder', 'fz/tagGroup/index', 0);

-- 标签分组操作按钮
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('标签分组查询', 'mailbox:tag-group:query', 3, 1, @menuId, 0),
('标签分组新增', 'mailbox:tag-group:create', 3, 2, @menuId, 0),
('标签分组修改', 'mailbox:tag-group:update', 3, 3, @menuId, 0),
('标签分组删除', 'mailbox:tag-group:delete', 3, 4, @menuId, 0);
```

### 6.2 标签管理权限

```sql
-- 标签管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('标签管理', '', 2, 2, {父菜单ID}, 'tag', 'ep:price-tag', 'fz/tag/index', 0);

-- 标签操作按钮
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('标签查询', 'mailbox:tag:query', 3, 1, @menuId, 0),
('标签新增', 'mailbox:tag:create', 3, 2, @menuId, 0),
('标签修改', 'mailbox:tag:update', 3, 3, @menuId, 0),
('标签删除', 'mailbox:tag:delete', 3, 4, @menuId, 0),
('标签导出', 'mailbox:tag:export', 3, 5, @menuId, 0);
```

### 6.3 标签关联权限

```sql
-- 标签关联按钮（通常嵌入信件详情页面，不单独菜单）
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('信件标签添加', 'mailbox:tag-rel:create', 3, 1, {信件管理菜单ID}, 0),
('信件标签移除', 'mailbox:tag-rel:delete', 3, 2, {信件管理菜单ID}, 0),
('信件标签查询', 'mailbox:tag-rel:query', 3, 3, {信件管理菜单ID}, 0);
```

---

## 7. Controller注解规范示例

### 7.1 标签Controller示例

```java
@Tag(name = "管理后台 - 标签管理")
@RestController
@RequestMapping("/mailbox/label/tag")
@Validated
public class TagController {

    @Resource
    private TagService tagService;

    @PostMapping("/create")
    @Operation(summary = "创建标签")
    @PreAuthorize("@ss.hasPermission('mailbox:tag:create')")
    public CommonResult<Long> createTag(@Valid @RequestBody TagSaveReqVO createReqVO) {
        return success(tagService.createTag(createReqVO));
    }

    @PutMapping("/update")
    @Operation(summary = "更新标签")
    @PreAuthorize("@ss.hasPermission('mailbox:tag:update')")
    public CommonResult<Boolean> updateTag(@Valid @RequestBody TagSaveReqVO updateReqVO) {
        tagService.updateTag(updateReqVO);
        return success(true);
    }

    @DeleteMapping("/delete")
    @Operation(summary = "删除标签")
    @Parameter(name = "id", description = "标签编号", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:tag:delete')")
    public CommonResult<Boolean> deleteTag(@RequestParam("id") Long id) {
        tagService.deleteTag(id);
        return success(true);
    }

    @GetMapping("/get")
    @Operation(summary = "获取标签详情")
    @Parameter(name = "id", description = "标签编号", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:tag:query')")
    public CommonResult<TagRespVO> getTag(@RequestParam("id") Long id) {
        TagDO tag = tagService.getTag(id);
        return success(BeanUtils.toBean(tag, TagRespVO.class));
    }

    @GetMapping("/page")
    @Operation(summary = "获取标签分页列表")
    @PreAuthorize("@ss.hasPermission('mailbox:tag:query')")
    public CommonResult<PageResult<TagRespVO>> getTagPage(@Valid TagPageReqVO pageReqVO) {
        PageResult<TagDO> pageResult = tagService.getTagPage(pageReqVO);
        return success(BeanUtils.toBean(pageResult, TagRespVO.class));
    }
}
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，包含标签分组、标签、标签关联三类接口设计 | Claude |