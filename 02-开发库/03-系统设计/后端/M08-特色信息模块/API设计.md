# M08 特色信息模块 - API设计

## 文档信息

**产品名称：** gaxx-pro 信件处理系统
**模块编号：** M08
**文档版本：** v1.0
**创建日期：** 2026-04-13
**状态：** 草稿

---

## 1. API接口总览

### 1.1 信件选登管理接口

| 序号 | 接口路径 | HTTP方法 | 接口描述 | 权限标识 |
|------|----------|----------|----------|----------|
| 1 | /mailbox/featured/letter/create | POST | 创建特色信息（选登信件） | mailbox:featured:create |
| 2 | /mailbox/featured/letter/create-list | POST | 批量创建特色信息 | mailbox:featured:create |
| 3 | /mailbox/featured/letter/cancel | PUT | 取消选登（状态变更） | mailbox:featured:delete |
| 4 | /mailbox/featured/letter/cancel-list | PUT | 批量取消选登 | mailbox:featured:delete |
| 5 | /mailbox/featured/letter/get | GET | 获取特色信息详情 | mailbox:featured:query |
| 6 | /mailbox/featured/letter/page | GET | 分页查询特色信息列表 | mailbox:featured:query |
| 7 | /mailbox/featured/letter/list | GET | 获取特色信息列表（不分页） | mailbox:featured:query |

### 1.2 选登类型管理接口

| 序号 | 接口路径 | HTTP方法 | 接口描述 | 权限标识 |
|------|----------|----------|----------|----------|
| 8 | /mailbox/featured/type/create | POST | 创建选登类型 | mailbox:featured-type:create |
| 9 | /mailbox/featured/type/update | PUT | 更新选登类型 | mailbox:featured-type:update |
| 10 | /mailbox/featured/type/delete | DELETE | 删除选登类型 | mailbox:featured-type:delete |
| 11 | /mailbox/featured/type/get | GET | 获取选登类型详情 | mailbox:featured-type:query |
| 12 | /mailbox/featured/type/list | GET | 获取选登类型列表 | mailbox:featured-type:query |
| 13 | /mailbox/featured/type/update-status | PUT | 更新选登类型状态 | mailbox:featured-type:update |

---

## 2. 信件选登接口详细设计

### 2.1 创建特色信息（选登信件）

**接口路径：** `/mailbox/featured/letter/create`
**HTTP方法：** POST
**权限标识：** `mailbox:featured:create`

#### 请求参数

```java
@RequestBody @Valid FeaturedLetterSaveReqVO reqVO
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| letterId | Long | 是 | 信件ID |
| featuredTypeId | Long | 是 | 选登类型ID |
| featuredReason | String | 否 | 选登原因/备注，最大500字 |

#### 响应格式

```java
CommonResult<Long>
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 响应码，0表示成功 |
| msg | String | 错误信息 |
| data | Long | 选登记录ID |

#### Controller代码示例

```java
@PostMapping("/create")
@Operation(summary = "创建特色信息")
@PreAuthorize("@ss.hasPermission('mailbox:featured:create')")
public CommonResult<Long> createFeaturedLetter(@Valid @RequestBody FeaturedLetterSaveReqVO createReqVO) {
    return success(featuredLetterService.createFeaturedLetter(createReqVO));
}
```

---

### 2.2 批量创建特色信息

**接口路径：** `/mailbox/featured/letter/create-list`
**HTTP方法：** POST
**权限标识：** `mailbox:featured:create`

#### 请求参数

```java
@RequestBody @Valid FeaturedLetterBatchSaveReqVO reqVO
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| letterIds | List<Long> | 是 | 信件ID列表，至少1个 |
| featuredTypeId | Long | 是 | 选登类型ID |
| featuredReason | String | 否 | 选登原因/备注，最大500字 |

#### 响应格式

```java
CommonResult<List<Long>>
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 响应码，0表示成功 |
| msg | String | 错误信息 |
| data | List<Long> | 成功创建的选登记录ID列表 |

#### Controller代码示例

```java
@PostMapping("/create-list")
@Operation(summary = "批量创建特色信息")
@PreAuthorize("@ss.hasPermission('mailbox:featured:create')")
public CommonResult<List<Long>> createFeaturedLetterList(@Valid @RequestBody FeaturedLetterBatchSaveReqVO batchReqVO) {
    return success(featuredLetterService.createFeaturedLetterList(batchReqVO));
}
```

---

### 2.3 取消选登（状态变更）

**接口路径：** `/mailbox/featured/letter/cancel`
**HTTP方法：** PUT
**权限标识：** `mailbox:featured:delete`

#### 请求参数

```java
@RequestParam("id") Long id
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 选登记录ID |

#### 响应格式

```java
CommonResult<Boolean>
```

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 响应码，0表示成功 |
| msg | String | 错误信息 |
| data | Boolean | 操作结果，true表示成功 |

#### Controller代码示例

```java
@PutMapping("/cancel")
@Operation(summary = "取消选登")
@Parameter(name = "id", description = "选登记录ID", required = true)
@PreAuthorize("@ss.hasPermission('mailbox:featured:delete')")
public CommonResult<Boolean> cancelFeaturedLetter(@RequestParam("id") Long id) {
    featuredLetterService.cancelFeaturedLetter(id);
    return success(true);
}
```

---

### 2.4 批量取消选登

**接口路径：** `/mailbox/featured/letter/cancel-list`
**HTTP方法：** PUT
**权限标识：** `mailbox:featured:delete`

#### 请求参数

```java
@RequestParam("ids") List<Long> ids
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| ids | List<Long> | 是 | 选登记录ID列表 |

#### 响应格式

```java
CommonResult<Boolean>
```

#### Controller代码示例

```java
@PutMapping("/cancel-list")
@Operation(summary = "批量取消选登")
@Parameter(name = "ids", description = "选登记录ID列表", required = true)
@PreAuthorize("@ss.hasPermission('mailbox:featured:delete')")
public CommonResult<Boolean> cancelFeaturedLetterList(@RequestParam("ids") List<Long> ids) {
    featuredLetterService.cancelFeaturedLetterList(ids);
    return success(true);
}
```

---

### 2.5 获取特色信息详情

**接口路径：** `/mailbox/featured/letter/get`
**HTTP方法：** GET
**权限标识：** `mailbox:featured:query`

#### 请求参数

```java
@RequestParam("id") Long id
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 选登记录ID |

#### 响应格式

```java
CommonResult<FeaturedLetterRespVO>
```

#### Controller代码示例

```java
@GetMapping("/get")
@Operation(summary = "获取特色信息详情")
@Parameter(name = "id", description = "选登记录ID", required = true)
@PreAuthorize("@ss.hasPermission('mailbox:featured:query')")
public CommonResult<FeaturedLetterRespVO> getFeaturedLetter(@RequestParam("id") Long id) {
    FeaturedLetterDO featuredLetter = featuredLetterService.getFeaturedLetter(id);
    return success(BeanUtils.toBean(featuredLetter, FeaturedLetterRespVO.class));
}
```

---

### 2.6 分页查询特色信息列表

**接口路径：** `/mailbox/featured/letter/page`
**HTTP方法：** GET
**权限标识：** `mailbox:featured:query`

#### 请求参数

```java
@Valid FeaturedLetterPageReqVO pageReqVO
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10，最大200 |
| featuredTypeId | Long | 否 | 选登类型ID筛选 |
| handlerUnitId | Long | 否 | 办理单位ID筛选 |
| keyword | String | 否 | 关键词搜索（信件标题） |
| createTime | String[] | 否 | 选登时间范围筛选 |

#### 响应格式

```java
CommonResult<PageResult<FeaturedLetterRespVO>>
```

#### Controller代码示例

```java
@GetMapping("/page")
@Operation(summary = "分页查询特色信息列表")
@PreAuthorize("@ss.hasPermission('mailbox:featured:query')")
public CommonResult<PageResult<FeaturedLetterRespVO>> getFeaturedLetterPage(@Valid FeaturedLetterPageReqVO pageReqVO) {
    PageResult<FeaturedLetterDO> pageResult = featuredLetterService.getFeaturedLetterPage(pageReqVO);
    return success(BeanUtils.toBean(pageResult, FeaturedLetterRespVO.class));
}
```

---

### 2.7 获取特色信息列表（不分页）

**接口路径：** `/mailbox/featured/letter/list`
**HTTP方法：** GET
**权限标识：** `mailbox:featured:query`

#### 请求参数

```java
@RequestParam(value = "featuredTypeId", required = false) Long featuredTypeId
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| featuredTypeId | Long | 否 | 选登类型ID筛选 |

#### 响应格式

```java
CommonResult<List<FeaturedLetterRespVO>>
```

#### Controller代码示例

```java
@GetMapping("/list")
@Operation(summary = "获取特色信息列表")
@Parameter(name = "featuredTypeId", description = "选登类型ID筛选")
@PreAuthorize("@ss.hasPermission('mailbox:featured:query')")
public CommonResult<List<FeaturedLetterRespVO>> getFeaturedLetterList(@RequestParam(value = "featuredTypeId", required = false) Long featuredTypeId) {
    List<FeaturedLetterDO> list = featuredLetterService.getFeaturedLetterList(featuredTypeId);
    return success(BeanUtils.toBean(list, FeaturedLetterRespVO.class));
}
```

---

## 3. 选登类型接口详细设计

### 3.1 创建选登类型

**接口路径：** `/mailbox/featured/type/create`
**HTTP方法：** POST
**权限标识：** `mailbox:featured-type:create`

#### 请求参数

```java
@RequestBody @Valid FeaturedTypeSaveReqVO reqVO
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 是 | 类型名称，最大100字 |
| parentId | Long | 否 | 上级类型ID，默认0 |
| sort | Integer | 否 | 排序值，默认0 |
| description | String | 否 | 类型描述，最大500字 |

#### 响应格式

```java
CommonResult<Long>
```

---

### 3.2 更新选登类型

**接口路径：** `/mailbox/featured/type/update`
**HTTP方法：** PUT
**权限标识：** `mailbox:featured-type:update`

#### 请求参数

```java
@RequestBody @Valid FeaturedTypeSaveReqVO reqVO
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 类型ID |
| name | String | 是 | 类型名称 |
| parentId | Long | 否 | 上级类型ID |
| sort | Integer | 否 | 排序值 |
| description | String | 否 | 类型描述 |

#### 响应格式

```java
CommonResult<Boolean>
```

---

### 3.3 删除选登类型

**接口路径：** `/mailbox/featured/type/delete`
**HTTP方法：** DELETE
**权限标识：** `mailbox:featured-type:delete`

#### 请求参数

```java
@RequestParam("id") Long id
```

#### 响应格式

```java
CommonResult<Boolean>
```

---

### 3.4 获取选登类型详情

**接口路径：** `/mailbox/featured/type/get`
**HTTP方法：** GET
**权限标识：** `mailbox:featured-type:query`

#### 请求参数

```java
@RequestParam("id") Long id
```

#### 响应格式

```java
CommonResult<FeaturedTypeRespVO>
```

---

### 3.5 获取选登类型列表

**接口路径：** `/mailbox/featured/type/list`
**HTTP方法：** GET
**权限标识：** `mailbox:featured-type:query`

#### 请求参数

```java
@RequestParam(value = "status", required = false) Integer status
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| status | Integer | 否 | 状态筛选：0-启用，1-禁用 |

#### 响应格式

```java
CommonResult<List<FeaturedTypeRespVO>>
```

#### Controller代码示例

```java
@GetMapping("/list")
@Operation(summary = "获取选登类型列表")
@Parameter(name = "status", description = "状态筛选：0-启用，1-禁用")
@PreAuthorize("@ss.hasPermission('mailbox:featured-type:query')")
public CommonResult<List<FeaturedTypeRespVO>> getFeaturedTypeList(@RequestParam(value = "status", required = false) Integer status) {
    List<FeaturedTypeDO> list = featuredTypeService.getFeaturedTypeList(status);
    return success(BeanUtils.toBean(list, FeaturedTypeRespVO.class));
}
```

---

### 3.6 更新选登类型状态

**接口路径：** `/mailbox/featured/type/update-status`
**HTTP方法：** PUT
**权限标识：** `mailbox:featured-type:update`

#### 请求参数

```java
@RequestParam("id") Long id, @RequestParam("status") Integer status
```

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 类型ID |
| status | Integer | 是 | 目标状态：0-启用，1-禁用 |

#### 响应格式

```java
CommonResult<Boolean>
```

#### Controller代码示例

```java
@PutMapping("/update-status")
@Operation(summary = "更新选登类型状态")
@Parameters({
    @Parameter(name = "id", description = "类型ID", required = true),
    @Parameter(name = "status", description = "目标状态：0-启用，1-禁用", required = true)
})
@PreAuthorize("@ss.hasPermission('mailbox:featured-type:update')")
public CommonResult<Boolean> updateFeaturedTypeStatus(@RequestParam("id") Long id, @RequestParam("status") Integer status) {
    featuredTypeService.updateFeaturedTypeStatus(id, status);
    return success(true);
}
```

---

## 4. 错误码定义

### 4.1 信件选登错误码

```java
// ========== 信件选登相关错误码 1_008_001_XXX ==========
ErrorCode FEATURED_LETTER_NOT_EXISTS = new ErrorCode(1_008_001_000, "选登记录不存在");
ErrorCode FEATURED_LETTER_ALREADY_EXISTS = new ErrorCode(1_008_001_001, "该信件已在当前类型下选登");
ErrorCode FEATURED_LETTER_STATUS_ERROR = new ErrorCode(1_008_001_002, "选登记录状态不正确");
ErrorCode FEATURED_LETTER_CANNOT_CANCEL = new ErrorCode(1_008_001_003, "该记录已取消选登，无法重复操作");
ErrorCode FEATURED_LETTER_LETTER_NOT_COMPLETED = new ErrorCode(1_008_001_004, "只能选登已完成的信件");
ErrorCode FEATURED_LETTER_TYPE_DISABLED = new ErrorCode(1_008_001_005, "选登类型已禁用，请选择其他类型");
ErrorCode FEATURED_LETTER_LETTER_NOT_EXISTS = new ErrorCode(1_008_001_006, "关联信件不存在");
```

### 4.2 选登类型错误码

```java
// ========== 选登类型相关错误码 1_008_002_XXX ==========
ErrorCode FEATURED_TYPE_NOT_EXISTS = new ErrorCode(1_008_002_000, "选登类型不存在");
ErrorCode FEATURED_TYPE_NAME_DUPLICATE = new ErrorCode(1_008_002_001, "已存在该名称的选登类型");
ErrorCode FEATURED_TYPE_HAS_CHILDREN = new ErrorCode(1_008_002_002, "存在子类型，无法删除");
ErrorCode FEATURED_TYPE_HAS_LETTERS = new ErrorCode(1_008_002_003, "该类型下存在选登信件，无法删除");
ErrorCode FEATURED_TYPE_STATUS_ERROR = new ErrorCode(1_008_002_004, "选登类型状态不正确");
```

---

## 5. 权限SQL配置

### 5.1 特色信息管理菜单

```sql
-- 特色信息管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('特色信息管理', '', 2, 8, {父菜单ID}, 'featured', 'ep:star-filled', 'mailbox/featured/letter/index', 0);

SET @featuredMenuId = LAST_INSERT_ID();

-- 特色信息按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('特色信息查询', 'mailbox:featured:query', 3, 1, @featuredMenuId, 0),
('特色信息选登', 'mailbox:featured:create', 3, 2, @featuredMenuId, 0),
('取消选登', 'mailbox:featured:delete', 3, 3, @featuredMenuId, 0);
```

### 5.2 选登类型管理菜单

```sql
-- 选登类型管理菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('选登类型管理', '', 2, 9, {父菜单ID}, 'featured-type', 'ep:menu', 'mailbox/featured/type/index', 0);

SET @typeMenuId = LAST_INSERT_ID();

-- 选登类型按钮权限
INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('选登类型查询', 'mailbox:featured-type:query', 3, 1, @typeMenuId, 0),
('选登类型新增', 'mailbox:featured-type:create', 3, 2, @typeMenuId, 0),
('选登类型修改', 'mailbox:featured-type:update', 3, 3, @typeMenuId, 0),
('选登类型删除', 'mailbox:featured-type:delete', 3, 4, @typeMenuId, 0);
```

---

## 6. Controller完整代码

### 6.1 FeaturedLetterController

```java
package cn.iocoder.yudao.module.mailbox.controller.admin.featured;

import cn.iocoder.yudao.framework.common.pojo.CommonResult;
import cn.iocoder.yudao.framework.common.pojo.PageResult;
import cn.iocoder.yudao.framework.common.util.object.BeanUtils;
import cn.iocoder.yudao.module.mailbox.controller.admin.featured.vo.*;
import cn.iocoder.yudao.module.mailbox.dal.dataobject.featured.FeaturedLetterDO;
import cn.iocoder.yudao.module.mailbox.service.featured.FeaturedLetterService;
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

@Tag(name = "管理后台 - 特色信息")
@RestController
@RequestMapping("/mailbox/featured/letter")
@Validated
public class FeaturedLetterController {

    @Resource
    private FeaturedLetterService featuredLetterService;

    @PostMapping("/create")
    @Operation(summary = "创建特色信息")
    @PreAuthorize("@ss.hasPermission('mailbox:featured:create')")
    public CommonResult<Long> createFeaturedLetter(@Valid @RequestBody FeaturedLetterSaveReqVO createReqVO) {
        return success(featuredLetterService.createFeaturedLetter(createReqVO));
    }

    @PostMapping("/create-list")
    @Operation(summary = "批量创建特色信息")
    @PreAuthorize("@ss.hasPermission('mailbox:featured:create')")
    public CommonResult<List<Long>> createFeaturedLetterList(@Valid @RequestBody FeaturedLetterBatchSaveReqVO batchReqVO) {
        return success(featuredLetterService.createFeaturedLetterList(batchReqVO));
    }

    @PutMapping("/cancel")
    @Operation(summary = "取消选登")
    @Parameter(name = "id", description = "选登记录ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:featured:delete')")
    public CommonResult<Boolean> cancelFeaturedLetter(@RequestParam("id") Long id) {
        featuredLetterService.cancelFeaturedLetter(id);
        return success(true);
    }

    @PutMapping("/cancel-list")
    @Operation(summary = "批量取消选登")
    @Parameter(name = "ids", description = "选登记录ID列表", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:featured:delete')")
    public CommonResult<Boolean> cancelFeaturedLetterList(@RequestParam("ids") List<Long> ids) {
        featuredLetterService.cancelFeaturedLetterList(ids);
        return success(true);
    }

    @GetMapping("/get")
    @Operation(summary = "获取特色信息详情")
    @Parameter(name = "id", description = "选登记录ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:featured:query')")
    public CommonResult<FeaturedLetterRespVO> getFeaturedLetter(@RequestParam("id") Long id) {
        FeaturedLetterDO featuredLetter = featuredLetterService.getFeaturedLetter(id);
        return success(BeanUtils.toBean(featuredLetter, FeaturedLetterRespVO.class));
    }

    @GetMapping("/page")
    @Operation(summary = "分页查询特色信息列表")
    @PreAuthorize("@ss.hasPermission('mailbox:featured:query')")
    public CommonResult<PageResult<FeaturedLetterRespVO>> getFeaturedLetterPage(@Valid FeaturedLetterPageReqVO pageReqVO) {
        PageResult<FeaturedLetterDO> pageResult = featuredLetterService.getFeaturedLetterPage(pageReqVO);
        return success(BeanUtils.toBean(pageResult, FeaturedLetterRespVO.class));
    }

    @GetMapping("/list")
    @Operation(summary = "获取特色信息列表")
    @Parameter(name = "featuredTypeId", description = "选登类型ID筛选")
    @PreAuthorize("@ss.hasPermission('mailbox:featured:query')")
    public CommonResult<List<FeaturedLetterRespVO>> getFeaturedLetterList(
            @RequestParam(value = "featuredTypeId", required = false) Long featuredTypeId) {
        List<FeaturedLetterDO> list = featuredLetterService.getFeaturedLetterList(featuredTypeId);
        return success(BeanUtils.toBean(list, FeaturedLetterRespVO.class));
    }
}
```

### 6.2 FeaturedTypeController

```java
package cn.iocoder.yudao.module.mailbox.controller.admin.featured;

import cn.iocoder.yudao.framework.common.pojo.CommonResult;
import cn.iocoder.yudao.framework.common.util.object.BeanUtils;
import cn.iocoder.yudao.module.mailbox.controller.admin.featured.vo.*;
import cn.iocoder.yudao.module.mailbox.dal.dataobject.featured.FeaturedTypeDO;
import cn.iocoder.yudao.module.mailbox.service.featured.FeaturedTypeService;
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

@Tag(name = "管理后台 - 选登类型")
@RestController
@RequestMapping("/mailbox/featured/type")
@Validated
public class FeaturedTypeController {

    @Resource
    private FeaturedTypeService featuredTypeService;

    @PostMapping("/create")
    @Operation(summary = "创建选登类型")
    @PreAuthorize("@ss.hasPermission('mailbox:featured-type:create')")
    public CommonResult<Long> createFeaturedType(@Valid @RequestBody FeaturedTypeSaveReqVO createReqVO) {
        return success(featuredTypeService.createFeaturedType(createReqVO));
    }

    @PutMapping("/update")
    @Operation(summary = "更新选登类型")
    @PreAuthorize("@ss.hasPermission('mailbox:featured-type:update')")
    public CommonResult<Boolean> updateFeaturedType(@Valid @RequestBody FeaturedTypeSaveReqVO updateReqVO) {
        featuredTypeService.updateFeaturedType(updateReqVO);
        return success(true);
    }

    @DeleteMapping("/delete")
    @Operation(summary = "删除选登类型")
    @Parameter(name = "id", description = "类型ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:featured-type:delete')")
    public CommonResult<Boolean> deleteFeaturedType(@RequestParam("id") Long id) {
        featuredTypeService.deleteFeaturedType(id);
        return success(true);
    }

    @GetMapping("/get")
    @Operation(summary = "获取选登类型详情")
    @Parameter(name = "id", description = "类型ID", required = true)
    @PreAuthorize("@ss.hasPermission('mailbox:featured-type:query')")
    public CommonResult<FeaturedTypeRespVO> getFeaturedType(@RequestParam("id") Long id) {
        FeaturedTypeDO featuredType = featuredTypeService.getFeaturedType(id);
        return success(BeanUtils.toBean(featuredType, FeaturedTypeRespVO.class));
    }

    @GetMapping("/list")
    @Operation(summary = "获取选登类型列表")
    @Parameter(name = "status", description = "状态筛选：0-启用，1-禁用")
    @PreAuthorize("@ss.hasPermission('mailbox:featured-type:query')")
    public CommonResult<List<FeaturedTypeRespVO>> getFeaturedTypeList(
            @RequestParam(value = "status", required = false) Integer status) {
        List<FeaturedTypeDO> list = featuredTypeService.getFeaturedTypeList(status);
        return success(BeanUtils.toBean(list, FeaturedTypeRespVO.class));
    }

    @PutMapping("/update-status")
    @Operation(summary = "更新选登类型状态")
    @Parameters({
        @Parameter(name = "id", description = "类型ID", required = true),
        @Parameter(name = "status", description = "目标状态：0-启用，1-禁用", required = true)
    })
    @PreAuthorize("@ss.hasPermission('mailbox:featured-type:update')")
    public CommonResult<Boolean> updateFeaturedTypeStatus(@RequestParam("id") Long id,
                                                          @RequestParam("status") Integer status) {
        featuredTypeService.updateFeaturedTypeStatus(id, status);
        return success(true);
    }
}
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，包含信件选登和选登类型完整API设计 | Claude |