# M07 个人中心模块 - API设计

## 文档信息

**产品名称：** gaxx-pro 信件处理系统
**模块名称：** M07 个人中心模块
**文档版本：** v1.0
**创建日期：** 2026-04-13
**状态：** 设计稿

---

## 1. API接口总览

### 1.1 接口路径规范

- **基础路径：** `/mailbox/personal`
- **权限标识前缀：** `mailbox:personal`

### 1.2 接口清单

| 序号 | 接口路径 | HTTP方法 | 接口描述 | 权限标识 |
|------|----------|----------|----------|----------|
| 1 | /mailbox/personal/favorite/create | POST | 收藏信件 | mailbox:personal:favorite:create |
| 2 | /mailbox/personal/favorite/cancel | PUT | 取消收藏 | mailbox:personal:favorite:update |
| 3 | /mailbox/personal/favorite/cancel-list | PUT | 批量取消收藏 | mailbox:personal:favorite:update |
| 4 | /mailbox/personal/favorite/page | GET | 收藏列表分页 | mailbox:personal:favorite:query |
| 5 | /mailbox/personal/favorite/check | GET | 检查收藏状态 | mailbox:personal:favorite:query |
| 6 | /mailbox/personal/read/mark | POST | 标记已读 | mailbox:personal:read:create |
| 7 | /mailbox/personal/read/mark-list | POST | 批量标记已读 | mailbox:personal:read:create |
| 8 | /mailbox/personal/read/check | GET | 检查已读状态 | mailbox:personal:read:query |
| 9 | /mailbox/personal/read/check-list | GET | 批量检查已读状态 | mailbox:personal:read:query |

---

## 2. 收藏管理API

### 2.1 收藏信件

**接口路径：** `POST /mailbox/personal/favorite/create`

**接口描述：** 用户收藏指定的信件

**权限标识：** `mailbox:personal:favorite:create`

#### 请求参数

```json
{
  "letterId": 1001,
  "letterNo": "TX20260001"
}
```

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterId | Long | 是 | 信件ID |
| letterNo | String | 否 | 信件编号（可选，便于冗余存储） |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": 12345
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码，0表示成功 |
| msg | String | 错误提示信息 |
| data | Long | 收藏记录ID |

#### 业务规则

- 若已存在正常状态的收藏记录，返回错误提示"该信件已被收藏"
- 若存在已取消状态的收藏记录，更新状态为正常并更新收藏时间
- 若信件不存在或已删除，返回错误提示"该信件不存在或已被删除"

---

### 2.2 取消收藏

**接口路径：** `PUT /mailbox/personal/favorite/cancel`

**接口描述：** 取消指定信件的收藏状态

**权限标识：** `mailbox:personal:favorite:update`

#### 请求参数

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterId | Long | 是 | 信件ID（Query参数） |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

#### 业务规则

- 若收藏记录不存在或状态为已取消，返回错误提示"该信件未被收藏"
- 更新收藏记录状态为已取消（favorite_status=2）
- 不删除记录，保留历史数据

---

### 2.3 批量取消收藏

**接口路径：** `PUT /mailbox/personal/favorite/cancel-list`

**接口描述：** 批量取消多个信件的收藏状态

**权限标识：** `mailbox:personal:favorite:update`

#### 请求参数

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterIds | List<Long> | 是 | 信件ID列表（Query参数） |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": true
}
```

---

### 2.4 收藏列表分页查询

**接口路径：** `GET /mailbox/personal/favorite/page`

**接口描述：** 分页查询当前用户的收藏信件列表

**权限标识：** `mailbox:personal:favorite:query`

#### 请求参数

```json
{
  "pageNo": 1,
  "pageSize": 10,
  "letterStatus": null,
  "letterType": null,
  "keyword": "",
  "startTime": null,
  "endTime": null
}
```

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| letterStatus | Integer | 否 | 信件状态筛选 |
| letterType | Integer | 否 | 信件类型筛选 |
| keyword | String | 否 | 关键词搜索（标题模糊匹配） |
| startTime | LocalDateTime | 否 | 收藏时间起始 |
| endTime | LocalDateTime | 否 | 收藏时间截止 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 25,
    "list": [
      {
        "id": 12345,
        "letterId": 1001,
        "letterNo": "TX20260001",
        "letterTitle": "关于交通违章处理的咨询",
        "letterStatus": 3,
        "letterStatusName": "已办结",
        "letterType": 1,
        "letterTypeName": "厅长信箱",
        "favoriteTime": "2026-04-10 14:30:00",
        "processUnitName": "交通管理处",
        "processProgress": "已回复"
      }
    ]
  }
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| total | Long | 总记录数 |
| list | List | 收藏信件列表 |
| id | Long | 收藏记录ID |
| letterId | Long | 信件ID |
| letterNo | String | 信件编号 |
| letterTitle | String | 信件标题 |
| letterStatus | Integer | 信件状态 |
| letterStatusName | String | 信件状态名称 |
| letterType | Integer | 信件类型 |
| letterTypeName | String | 信件类型名称 |
| favoriteTime | LocalDateTime | 收藏时间 |
| processUnitName | String | 当前办理单位名称 |
| processProgress | String | 办理进度描述 |

---

### 2.5 检查收藏状态

**接口路径：** `GET /mailbox/personal/favorite/check`

**接口描述：** 检查指定信件是否已被当前用户收藏

**权限标识：** `mailbox:personal:favorite:query`

#### 请求参数

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterId | Long | 是 | 信件ID（Query参数） |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "isFavorite": true,
    "favoriteId": 12345,
    "favoriteTime": "2026-04-10 14:30:00"
  }
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| isFavorite | Boolean | 是否已收藏 |
| favoriteId | Long | 收藏记录ID（未收藏时为null） |
| favoriteTime | LocalDateTime | 收藏时间（未收藏时为null） |

---

## 3. 已读管理API

### 3.1 标记已读

**接口路径：** `POST /mailbox/personal/read/mark`

**接口描述：** 标记指定信件为已读状态

**权限标识：** `mailbox:personal:read:create`

#### 请求参数

```json
{
  "letterId": 1001,
  "letterNo": "TX20260001"
}
```

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterId | Long | 是 | 信件ID |
| letterNo | String | 否 | 信件编号 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": 12345
}
```

#### 业务规则

- 若已读记录已存在，更新last_read_time为当前时间
- 若已读记录不存在，创建新记录，设置first_read_time和last_read_time
- 自动标记已读（打开信件详情时调用）和手动标记已读共用此接口

---

### 3.2 批量标记已读

**接口路径：** `POST /mailbox/personal/read/mark-list`

**接口描述：** 批量标记多个信件为已读状态

**权限标识：** `mailbox:personal:read:create`

#### 请求参数

```json
{
  "letterIds": [1001, 1002, 1003]
}
```

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterIds | List<Long> | 是 | 信件ID列表 |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "successCount": 3,
    "failCount": 0
  }
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| successCount | Integer | 成功标记数量 |
| failCount | Integer | 失败数量 |

---

### 3.3 检查已读状态

**接口路径：** `GET /mailbox/personal/read/check`

**接口描述：** 检查指定信件是否已被当前用户阅读

**权限标识：** `mailbox:personal:read:query`

#### 请求参数

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterId | Long | 是 | 信件ID（Query参数） |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "isRead": true,
    "readRecordId": 12345,
    "firstReadTime": "2026-04-08 10:00:00",
    "lastReadTime": "2026-04-10 14:30:00"
  }
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| isRead | Boolean | 是否已读 |
| readRecordId | Long | 已读记录ID（未读时为null） |
| firstReadTime | LocalDateTime | 首次阅读时间 |
| lastReadTime | LocalDateTime | 最后阅读时间 |

---

### 3.4 批量检查已读状态

**接口路径：** `GET /mailbox/personal/read/check-list`

**接口描述：** 批量检查多个信件的已读状态

**权限标识：** `mailbox:personal:read:query`

#### 请求参数

| 参数名 | 类型 | 是否必填 | 说明 |
|--------|------|----------|------|
| letterIds | List<Long> | 是 | 信件ID列表（Query参数） |

#### 响应结果

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "readLetterIds": [1001, 1003],
    "unreadLetterIds": [1002],
    "readStatusMap": {
      "1001": true,
      "1002": false,
      "1003": true
    }
  }
}
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| readLetterIds | List<Long> | 已读信件ID列表 |
| unreadLetterIds | List<Long> | 未读信件ID列表 |
| readStatusMap | Map<Long, Boolean> | 信件ID与已读状态的映射 |

---

## 4. Controller代码模板

### 4.1 收藏管理Controller

```java
package cn.iocoder.yudao.module.fz.controller.admin.personal;

import cn.iocoder.yudao.framework.common.pojo.CommonResult;
import cn.iocoder.yudao.framework.common.pojo.PageResult;
import cn.iocoder.yudao.module.fz.controller.admin.personal.vo.*;
import cn.iocoder.yudao.module.fz.service.personal.PersonalFavoriteService;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.Parameters;
import io.swagger.v3.oas.annotations.tags.Tag;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import javax.validation.Valid;
import java.util.List;

import static cn.iocoder.yudao.framework.common.pojo.CommonResult.success;

@Tag(name = "管理后台 - 个人中心 - 收藏管理")
@RestController
@RequestMapping("/mailbox/personal/favorite")
@Validated
public class PersonalFavoriteController {

    @Resource
    private PersonalFavoriteService favoriteService;

    @PostMapping("/create")
    @Operation(summary = "收藏信件")
    @PreAuthorize("@ss.hasPermission('mailbox:personal:favorite:create')")
    public CommonResult<Long> createFavorite(@Valid @RequestBody FavoriteCreateReqVO reqVO) {
        return success(favoriteService.createFavorite(reqVO));
    }

    @PutMapping("/cancel")
    @Operation(summary = "取消收藏")
    @Parameter(name = "letterId", description = "信件ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:personal:favorite:update')")
    public CommonResult<Boolean> cancelFavorite(@RequestParam("letterId") Long letterId) {
        favoriteService.cancelFavorite(letterId);
        return success(true);
    }

    @PutMapping("/cancel-list")
    @Operation(summary = "批量取消收藏")
    @Parameter(name = "letterIds", description = "信件ID列表", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:personal:favorite:update')")
    public CommonResult<Boolean> cancelFavoriteList(@RequestParam("letterIds") List<Long> letterIds) {
        favoriteService.cancelFavoriteList(letterIds);
        return success(true);
    }

    @GetMapping("/page")
    @Operation(summary = "收藏列表分页查询")
    @PreAuthorize("@ss.hasPermission('mailbox:personal:favorite:query')")
    public CommonResult<PageResult<FavoriteRespVO>> getFavoritePage(@Valid FavoritePageReqVO pageReqVO) {
        return success(favoriteService.getFavoritePage(pageReqVO));
    }

    @GetMapping("/check")
    @Operation(summary = "检查收藏状态")
    @Parameter(name = "letterId", description = "信件ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:personal:favorite:query')")
    public CommonResult<FavoriteCheckRespVO> checkFavorite(@RequestParam("letterId") Long letterId) {
        return success(favoriteService.checkFavorite(letterId));
    }
}
```

### 4.2 已读管理Controller

```java
package cn.iocoder.yudao.module.fz.controller.admin.personal;

import cn.iocoder.yudao.framework.common.pojo.CommonResult;
import cn.iocoder.yudao.module.fz.controller.admin.personal.vo.*;
import cn.iocoder.yudao.module.fz.service.personal.PersonalReadService;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.Parameters;
import io.swagger.v3.oas.annotations.tags.Tag;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import javax.validation.Valid;
import java.util.List;

import static cn.iocoder.yudao.framework.common.pojo.CommonResult.success;

@Tag(name = "管理后台 - 个人中心 - 已读管理")
@RestController
@RequestMapping("/mailbox/personal/read")
@Validated
public class PersonalReadController {

    @Resource
    private PersonalReadService readService;

    @PostMapping("/mark")
    @Operation(summary = "标记已读")
    @PreAuthorize("@ss.hasPermission('mailbox:personal:read:create')")
    public CommonResult<Long> markRead(@Valid @RequestBody ReadMarkReqVO reqVO) {
        return success(readService.markRead(reqVO));
    }

    @PostMapping("/mark-list")
    @Operation(summary = "批量标记已读")
    @PreAuthorize("@ss.hasPermission('mailbox:personal:read:create')")
    public CommonResult<ReadMarkResultRespVO> markReadList(@Valid @RequestBody ReadMarkListReqVO reqVO) {
        return success(readService.markReadList(reqVO));
    }

    @GetMapping("/check")
    @Operation(summary = "检查已读状态")
    @Parameter(name = "letterId", description = "信件ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:personal:read:query')")
    public CommonResult<ReadCheckRespVO> checkRead(@RequestParam("letterId") Long letterId) {
        return success(readService.checkRead(letterId));
    }

    @GetMapping("/check-list")
    @Operation(summary = "批量检查已读状态")
    @Parameter(name = "letterIds", description = "信件ID列表", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:personal:read:query')")
    public CommonResult<ReadCheckListRespVO> checkReadList(@RequestParam("letterIds") List<Long> letterIds) {
        return success(readService.checkReadList(letterIds));
    }
}
```

---

## 5. 错误码定义

### 5.1 收藏相关错误码

```java
// ========== 收藏管理相关错误码 1_XXX_007_001 ==========

// 收藏管理模块编号：007，功能编号：001
ErrorCode FAVORITE_ALREADY_EXISTS = new ErrorCode(1_XXX_007_001_000, "该信件已被收藏");
ErrorCode FAVORITE_NOT_EXISTS = new ErrorCode(1_XXX_007_001_001, "该信件未被收藏");
ErrorCode FAVORITE_LETTER_NOT_EXISTS = new ErrorCode(1_XXX_007_001_002, "该信件不存在或已被删除");
ErrorCode FAVORITE_LETTER_NO_PERMISSION = new ErrorCode(1_XXX_007_001_003, "您没有权限收藏该信件");
```

### 5.2 已读相关错误码

```java
// ========== 已读管理相关错误码 1_XXX_007_002 ==========

// 已读管理模块编号：007，功能编号：002
ErrorCode READ_LETTER_NOT_EXISTS = new ErrorCode(1_XXX_007_002_000, "该信件不存在或已被删除");
ErrorCode READ_LETTER_NO_PERMISSION = new ErrorCode(1_XXX_007_002_001, "您没有权限阅读该信件");
ErrorCode READ_MARK_FAILED = new ErrorCode(1_XXX_007_002_002, "标记已读失败");
```

---

## 6. 权限配置SQL

```sql
-- 个人中心菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('个人中心', '', 2, 10, {流转菜单ID}, '/personal', 'user', 'personal/index', 0);
SET @personalMenuId = LAST_INSERT_ID();

-- 收藏管理子菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('收藏管理', '', 3, 1, @personalMenuId, '/personal/favorite', 'star', 'personal/favorite/index', 0);
SET @favoriteMenuId = LAST_INSERT_ID();

-- 收藏管理按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('收藏查询', 'mailbox:personal:favorite:query', 3, 1, @favoriteMenuId, 0),
('收藏信件', 'mailbox:personal:favorite:create', 3, 2, @favoriteMenuId, 0),
('取消收藏', 'mailbox:personal:favorite:update', 3, 3, @favoriteMenuId, 0);

-- 已读管理子菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('已读管理', '', 3, 2, @personalMenuId, '/personal/read', 'eye', 'personal/read/index', 0);
SET @readMenuId = LAST_INSERT_ID();

-- 已读管理按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('已读查询', 'mailbox:personal:read:query', 3, 1, @readMenuId, 0),
('标记已读', 'mailbox:personal:read:create', 3, 2, @readMenuId, 0);
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，包含收藏和已读管理API设计 | 后端架构师 |