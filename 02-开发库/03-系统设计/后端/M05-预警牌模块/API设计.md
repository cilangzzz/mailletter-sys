# M05 预警牌模块 - API设计

## 1. API接口列表

### 1.1 预警牌配置管理接口

| 接口 | HTTP方法 | 路径 | 权限标识 | 说明 |
|------|----------|------|----------|------|
| 创建预警牌配置 | POST | /mailbox/warning/board/create | mailbox:warning:create | 创建新的预警牌配置 |
| 更新预警牌配置 | PUT | /mailbox/warning/board/update | mailbox:warning:update | 更新预警牌配置信息 |
| 删除预警牌配置 | DELETE | /mailbox/warning/board/delete | mailbox:warning:delete | 删除预警牌配置 |
| 获取预警牌配置详情 | GET | /mailbox/warning/board/get | mailbox:warning:query | 获取单个预警牌配置详情 |
| 获取预警牌配置分页列表 | GET | /mailbox/warning/board/page | mailbox:warning:query | 分页查询预警牌配置列表 |
| 获取预警牌配置列表 | GET | /mailbox/warning/board/list | mailbox:warning:query | 获取预警牌配置列表（不分页） |
| 更新预警牌状态 | PUT | /mailbox/warning/board/update-status | mailbox:warning:update | 更新预警牌启用/停用状态 |
| 更新预警开关 | PUT | /mailbox/warning/board/update-switch | mailbox:warning:update | 更新预警开关状态 |
| 导出预警牌配置Excel | GET | /mailbox/warning/board/export-excel | mailbox:warning:export | 导出预警牌配置Excel |

### 1.2 预警关联管理接口

| 接口 | HTTP方法 | 路径 | 权限标识 | 说明 |
|------|----------|------|----------|------|
| 创建信件预警关联 | POST | /mailbox/warning/letter/create | mailbox:warning:create | 为信件关联预警牌 |
| 更新信件预警关联 | PUT | /mailbox/warning/letter/update | mailbox:warning:update | 更新信件预警关联信息 |
| 删除信件预警关联 | DELETE | /mailbox/warning/letter/delete | mailbox:warning:delete | 删除信件预警关联 |
| 获取信件预警关联详情 | GET | /mailbox/warning/letter/get | mailbox:warning:query | 获取单个信件预警关联详情 |
| 获取信件预警关联分页 | GET | /mailbox/warning/letter/page | mailbox:warning:query | 分页查询信件预警关联 |
| 获取预警状态列表 | GET | /mailbox/warning/letter/status-list | mailbox:warning:query | 查询指定预警状态的信件列表 |
| 重新计算预警状态 | POST | /mailbox/warning/letter/recalculate | mailbox:warning:update | 手动触发重新计算预警状态 |

### 1.3 预警记录查询接口

| 接口 | HTTP方法 | 路径 | 权限标识 | 说明 |
|------|----------|------|----------|------|
| 获取预警记录详情 | GET | /mailbox/warning/record/get | mailbox:warning:query | 获取单个预警记录详情 |
| 获取预警记录分页 | GET | /mailbox/warning/record/page | mailbox:warning:query | 分页查询预警记录 |
| 获取信件预警记录列表 | GET | /mailbox/warning/record/list-by-letter | mailbox:warning:query | 查询某信件的预警记录 |
| 获取单位预警记录列表 | GET | /mailbox/warning/record/list-by-unit | mailbox:warning:query | 查询某单位接收的预警记录 |

### 1.4 节假日配置管理接口

| 接口 | HTTP方法 | 路径 | 权限标识 | 说明 |
|------|----------|------|----------|------|
| 创建节假日配置 | POST | /mailbox/warning/holiday/create | mailbox:warning:create | 创建节假日配置 |
| 更新节假日配置 | PUT | /mailbox/warning/holiday/update | mailbox:warning:update | 更新节假日配置 |
| 删除节假日配置 | DELETE | /mailbox/warning/holiday/delete | mailbox:warning:delete | 删除节假日配置 |
| 批量创建节假日 | POST | /mailbox/warning/holiday/batch-create | mailbox:warning:create | 批量创建节假日配置 |
| 获取节假日配置详情 | GET | /mailbox/warning/holiday/get | mailbox:warning:query | 获取节假日配置详情 |
| 获取节假日配置分页 | GET | /mailbox/warning/holiday/page | mailbox:warning:query | 分页查询节假日配置 |
| 获取年度节假日列表 | GET | /mailbox/warning/holiday/list-by-year | mailbox:warning:query | 查询某年节假日列表 |

---

## 2. 预警牌配置接口详情

### 2.1 创建预警牌配置

**接口路径**: `POST /mailbox/warning/board/create`

**权限标识**: `mailbox:warning:create`

**请求参数**:

```json
{
  "name": "平安厅预警牌",
  "code": "PAYJ001",
  "siteType": "平安厅",
  "normalDays": 5,
  "urgentDays": 3,
  "veryUrgentDays": 2,
  "superUrgentDays": 1,
  "warningThreshold": 80.00,
  "warningEnabled": true,
  "status": 0,
  "remark": "用于平安厅信访信件预警"
}
```

**请求参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 是 | 预警牌名称，最大100字符 |
| code | String | 是 | 预警牌编码，最大50字符，唯一 |
| siteType | String | 是 | 站点类型 |
| normalDays | Integer | 是 | 普通工作日时限，必须大于0 |
| urgentDays | Integer | 是 | 紧急时限，必须大于0 |
| veryUrgentDays | Integer | 是 | 特急时限，必须大于0 |
| superUrgentDays | Integer | 是 | 超紧急时限，必须大于0 |
| warningThreshold | Decimal | 是 | 预警阈值比例（0-100），默认80 |
| warningEnabled | Boolean | 否 | 预警开关，默认true |
| status | Integer | 否 | 状态，默认0（启用） |
| remark | String | 否 | 备注，最大500字符 |

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": 1  // 预警牌配置ID
}
```

---

### 2.2 更新预警牌配置

**接口路径**: `PUT /mailbox/warning/board/update`

**权限标识**: `mailbox:warning:update`

**请求参数**:

```json
{
  "id": 1,
  "name": "平安厅信访预警牌",
  "normalDays": 7,
  "urgentDays": 4,
  "veryUrgentDays": 2,
  "superUrgentDays": 1,
  "warningThreshold": 85.00,
  "warningEnabled": true,
  "status": 0,
  "remark": "调整时限配置"
}
```

**注意**: 预警牌编码（code）创建后不可修改，不在更新参数中。

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 2.3 删除预警牌配置

**接口路径**: `DELETE /mailbox/warning/board/delete`

**权限标识**: `mailbox:warning:delete`

**请求参数**: `id` (Long) - 预警牌配置ID

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

**业务规则**:
- 如果预警牌已关联信件，需要先解除关联或提示用户确认
- 删除后，已关联信件的预警状态变为"未设置"

---

### 2.4 获取预警牌配置详情

**接口路径**: `GET /mailbox/warning/board/get`

**权限标识**: `mailbox:warning:query`

**请求参数**: `id` (Long) - 预警牌配置ID

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "id": 1,
    "name": "平安厅预警牌",
    "code": "PAYJ001",
    "siteType": "平安厅",
    "normalDays": 5,
    "urgentDays": 3,
    "veryUrgentDays": 2,
    "superUrgentDays": 1,
    "warningThreshold": 80.00,
    "warningEnabled": true,
    "status": 0,
    "remark": "用于平安厅信访信件预警",
    "createTime": "2026-04-13 10:00:00"
  }
}
```

---

### 2.5 获取预警牌配置分页列表

**接口路径**: `GET /mailbox/warning/board/page`

**权限标识**: `mailbox:warning:query`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| name | String | 否 | 预警牌名称，模糊匹配 |
| code | String | 否 | 预警牌编码，精确匹配 |
| siteType | String | 否 | 站点类型 |
| status | Integer | 否 | 状态 |
| warningEnabled | Boolean | 否 | 预警开关状态 |
| createTime | String[] | 否 | 创建时间范围 |

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 100,
    "list": [
      {
        "id": 1,
        "name": "平安厅预警牌",
        "code": "PAYJ001",
        "siteType": "平安厅",
        "normalDays": 5,
        "urgentDays": 3,
        "veryUrgentDays": 2,
        "superUrgentDays": 1,
        "warningThreshold": 80.00,
        "warningEnabled": true,
        "status": 0,
        "createTime": "2026-04-13 10:00:00"
      }
    ]
  }
}
```

---

### 2.6 更新预警牌状态

**接口路径**: `PUT /mailbox/warning/board/update-status`

**权限标识**: `mailbox:warning:update`

**请求参数**:

```json
{
  "id": 1,
  "status": 1  // 0启用，1停用
}
```

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 2.7 更新预警开关

**接口路径**: `PUT /mailbox/warning/board/update-switch`

**权限标识**: `mailbox:warning:update`

**请求参数**:

```json
{
  "id": 1,
  "warningEnabled": false
}
```

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

## 3. 预警关联接口详情

### 3.1 创建信件预警关联

**接口路径**: `POST /mailbox/warning/letter/create`

**权限标识**: `mailbox:warning:create`

**请求参数**:

```json
{
  "warningBoardId": 1,
  "letterId": 100,
  "urgencyLevel": "urgent",
  "startTime": "2026-04-13 09:00:00"
}
```

**请求参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| warningBoardId | Long | 是 | 预警牌配置ID |
| letterId | Long | 是 | 信件ID |
| urgencyLevel | String | 是 | 紧急程度：normal、urgent、very_urgent、super_urgent |
| startTime | DateTime | 否 | 时限起算时间，默认为信件登记时间 |

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": 1  // 预警关联ID
}
```

---

### 3.2 获取信件预警关联详情

**接口路径**: `GET /mailbox/warning/letter/get`

**权限标识**: `mailbox:warning:query`

**请求参数**: `id` (Long) - 预警关联ID，或 `letterId` (Long) - 信件ID

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "id": 1,
    "warningBoardId": 1,
    "warningBoardName": "平安厅预警牌",
    "warningBoardCode": "PAYJ001",
    "letterId": 100,
    "letterNo": "LX2026001",
    "urgencyLevel": "urgent",
    "urgencyLevelName": "紧急",
    "deadlineDays": 3,
    "startTime": "2026-04-13 09:00:00",
    "deadlineTime": "2026-04-17 17:00:00",
    "warningStatus": "warning",
    "warningStatusName": "预警",
    "usedDays": 2,
    "remainingDays": 1,
    "overdueDays": null,
    "lastCheckTime": "2026-04-15 10:00:00"
  }
}
```

---

### 3.3 获取预警状态列表

**接口路径**: `GET /mailbox/warning/letter/status-list`

**权限标识**: `mailbox:warning:query`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| warningStatus | String | 是 | 预警状态：normal、warning、overdue |
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| urgencyLevel | String | 否 | 紧急程度 |
| warningBoardId | Long | 否 | 预警牌配置ID |

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 50,
    "list": [
      {
        "id": 1,
        "letterId": 100,
        "letterNo": "LX2026001",
        "letterTitle": "关于社区噪音问题的信访",
        "urgencyLevel": "urgent",
        "warningStatus": "warning",
        "deadlineTime": "2026-04-17 17:00:00",
        "remainingDays": 1,
        "hostUnit": "社区管理处"
      }
    ]
  }
}
```

---

### 3.4 重新计算预警状态

**接口路径**: `POST /mailbox/warning/letter/recalculate`

**权限标识**: `mailbox:warning:update`

**请求参数**:

```json
{
  "warningBoardId": 1  // 可选，指定预警牌ID则只重新计算该预警牌关联的信件
}
```

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "totalChecked": 100,
    "normalCount": 70,
    "warningCount": 20,
    "overdueCount": 10
  }
}
```

---

## 4. 预警记录接口详情

### 4.1 获取预警记录分页

**接口路径**: `GET /mailbox/warning/record/page`

**权限标识**: `mailbox:warning:query`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| letterId | Long | 否 | 信件ID |
| warningLevel | String | 否 | 预警级别 |
| warningType | String | 否 | 提醒类型 |
| sendStatus | Integer | 否 | 发送状态 |
| receiverUnitId | Long | 否 | 接收单位ID |
| sendTime | String[] | 否 | 发送时间范围 |

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 200,
    "list": [
      {
        "id": 1,
        "letterId": 100,
        "letterNo": "LX2026001",
        "warningLevel": "warning",
        "warningLevelName": "预警",
        "warningType": "message",
        "warningTypeName": "站内消息",
        "receiverUnitId": 10,
        "receiverUnitName": "社区管理处",
        "receiverUnitType": "host",
        "content": "信件LX2026001即将超期，剩余1个工作日，请及时处理",
        "sendTime": "2026-04-16 09:00:00",
        "sendStatus": 1,
        "sendStatusName": "已发送"
      }
    ]
  }
}
```

---

### 4.2 获取信件预警记录列表

**接口路径**: `GET /mailbox/warning/record/list-by-letter`

**权限标识**: `mailbox:warning:query`

**请求参数**: `letterId` (Long) - 信件ID

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "id": 1,
      "warningLevel": "warning",
      "warningType": "message",
      "receiverUnitName": "社区管理处",
      "content": "信件LX2026001即将超期...",
      "sendTime": "2026-04-16 09:00:00",
      "sendStatus": 1
    }
  ]
}
```

---

## 5. 节假日配置接口详情

### 5.1 批量创建节假日

**接口路径**: `POST /mailbox/warning/holiday/batch-create`

**权限标识**: `mailbox:warning:create`

**请求参数**:

```json
{
  "year": 2026,
  "holidays": [
    {
      "date": "2026-01-01",
      "dateType": 1,
      "holidayName": "元旦"
    },
    {
      "date": "2026-02-08",
      "dateType": 1,
      "holidayName": "春节"
    }
  ]
}
```

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "createdCount": 2,
    "skippedCount": 0
  }
}
```

---

### 5.2 获取年度节假日列表

**接口路径**: `GET /mailbox/warning/holiday/list-by-year`

**权限标识**: `mailbox:warning:query`

**请求参数**: `year` (Integer) - 年份

**响应结果**:

```json
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "id": 1,
      "date": "2026-01-01",
      "dateType": 1,
      "dateTypeName": "节假日",
      "holidayName": "元旦",
      "remark": ""
    }
  ]
}
```

---

## 6. 错误码定义

| 错误码 | 错误信息 | 说明 |
|--------|----------|------|
| 1_015_001_000 | 预警牌配置不存在 | 操作的预警牌配置ID不存在 |
| 1_015_001_001 | 预警牌编码已存在 | 创建时编码重复 |
| 1_015_001_002 | 预警牌名称已存在 | 创建时名称重复（可选校验） |
| 1_015_001_003 | 预警牌配置不能删除 | 存在关联信件，需先解除关联 |
| 1_015_001_004 | 时限必须大于0 | 时限配置值不合法 |
| 1_015_001_005 | 时限递增规则错误 | 超紧急时限应小于特急时限等 |
| 1_015_002_000 | 预警关联不存在 | 操作的预警关联ID不存在 |
| 1_015_002_001 | 信件已关联预警牌 | 同一信件不能重复关联 |
| 1_015_002_002 | 预警牌配置未启用 | 关联的预警牌配置已停用 |
| 1_015_003_000 | 预警记录不存在 | 查询的预警记录不存在 |
| 1_015_004_000 | 节假日配置不存在 | 操作的节假日配置不存在 |
| 1_015_004_001 | 节假日日期已存在 | 同一日期已有配置 |

---

## 7. Controller 注解示例

### 7.1 预警牌配置Controller

```java
@Tag(name = "管理后台 - 预警牌配置")
@RestController
@RequestMapping("/mailbox/warning/board")
@Validated
public class WarningBoardController {

    @PostMapping("/create")
    @Operation(summary = "创建预警牌配置")
    @PreAuthorize("@ss.hasPermission('mailbox:warning:create')")
    public CommonResult<Long> createWarningBoard(@Valid @RequestBody WarningBoardSaveReqVO createReqVO) {
        return success(warningBoardService.createWarningBoard(createReqVO));
    }

    @PutMapping("/update")
    @Operation(summary = "更新预警牌配置")
    @PreAuthorize("@ss.hasPermission('mailbox:warning:update')")
    public CommonResult<Boolean> updateWarningBoard(@Valid @RequestBody WarningBoardSaveReqVO updateReqVO) {
        warningBoardService.updateWarningBoard(updateReqVO);
        return success(true);
    }

    @DeleteMapping("/delete")
    @Operation(summary = "删除预警牌配置")
    @Parameter(name = "id", description = "预警牌配置ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:warning:delete')")
    public CommonResult<Boolean> deleteWarningBoard(@RequestParam("id") Long id) {
        warningBoardService.deleteWarningBoard(id);
        return success(true);
    }

    @GetMapping("/get")
    @Operation(summary = "获取预警牌配置详情")
    @Parameter(name = "id", description = "预警牌配置ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:warning:query')")
    public CommonResult<WarningBoardRespVO> getWarningBoard(@RequestParam("id") Long id) {
        WarningBoardDO board = warningBoardService.getWarningBoard(id);
        return success(BeanUtils.toBean(board, WarningBoardRespVO.class));
    }

    @GetMapping("/page")
    @Operation(summary = "获取预警牌配置分页列表")
    @PreAuthorize("@ss.hasPermission('mailbox:warning:query')")
    public CommonResult<PageResult<WarningBoardRespVO>> getWarningBoardPage(@Valid WarningBoardPageReqVO pageReqVO) {
        PageResult<WarningBoardDO> pageResult = warningBoardService.getWarningBoardPage(pageReqVO);
        return success(BeanUtils.toBean(pageResult, WarningBoardRespVO.class));
    }

    @PutMapping("/update-status")
    @Operation(summary = "更新预警牌状态")
    @PreAuthorize("@ss.hasPermission('mailbox:warning:update')")
    public CommonResult<Boolean> updateWarningBoardStatus(@Valid @RequestBody WarningBoardStatusReqVO reqVO) {
        warningBoardService.updateWarningBoardStatus(reqVO.getId(), reqVO.getStatus());
        return success(true);
    }

    @PutMapping("/update-switch")
    @Operation(summary = "更新预警开关")
    @PreAuthorize("@ss.hasPermission('mailbox:warning:update')")
    public CommonResult<Boolean> updateWarningSwitch(@Valid @RequestBody WarningSwitchReqVO reqVO) {
        warningBoardService.updateWarningSwitch(reqVO.getId(), reqVO.getWarningEnabled());
        return success(true);
    }
}
```

---

## 8. 权限配置SQL

```sql
-- 添加预警牌管理菜单（假设父菜单ID为 2000）
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('预警牌管理', '', 2, 5, 2000, 'warning', 'ep:warning', 'mailbox/warning/board/index', 0);

-- 获取刚插入的菜单ID
SET @menuId = LAST_INSERT_ID();

-- 添加按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('预警牌查询', 'mailbox:warning:query', 3, 1, @menuId, 0),
('预警牌新增', 'mailbox:warning:create', 3, 2, @menuId, 0),
('预警牌修改', 'mailbox:warning:update', 3, 3, @menuId, 0),
('预警牌删除', 'mailbox:warning:delete', 3, 4, @menuId, 0),
('预警牌导出', 'mailbox:warning:export', 3, 5, @menuId, 0);
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，完成M05预警牌模块API设计 | Claude |