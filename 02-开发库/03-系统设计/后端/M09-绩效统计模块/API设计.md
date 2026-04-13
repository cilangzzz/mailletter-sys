# M09 绩效统计模块 - API设计

## 文档信息

**产品名称：** gaxx-pro 信件处理系统
**模块编号：** M09
**文档版本：** v1.0
**创建日期：** 2026-04-13
**状态：** 草稿

---

## 1. API概述

### 1.1 接口路径规范

所有接口遵循统一路径规范：

```
/{模块}/{功能}/{操作}
```

本模块接口路径前缀：`/mailbox/statistics`

### 1.2 权限标识规范

权限标识格式：`mailbox:statistics:{操作}`

| 操作 | 权限标识 | 说明 |
|------|----------|------|
| 效率统计查询 | mailbox:statistics:efficiency-query | 查看办理效率统计 |
| 效率统计导出 | mailbox:statistics:efficiency-export | 导出效率统计报表 |
| 质量统计查询 | mailbox:statistics:quality-query | 查看回复质量统计 |
| 质量统计导出 | mailbox:statistics:quality-export | 导出质量统计报表 |
| 满意度统计查询 | mailbox:statistics:satisfaction-query | 查看满意度统计 |
| 满意度统计导出 | mailbox:statistics:satisfaction-export | 导出满意度统计报表 |
| 综合绩效查询 | mailbox:statistics:performance-query | 查看综合绩效汇总 |
| 综合绩效导出 | mailbox:statistics:performance-export | 导出综合绩效报表 |
| 统计刷新 | mailbox:statistics:refresh | 手动刷新统计数据 |

### 1.3 通用响应格式

所有接口使用 `CommonResult<T>` 包装响应：

```json
{
  "code": 0,
  "msg": "成功",
  "data": {}
}
```

---

## 2. 办理效率统计接口

### 2.1 获取办理效率统计分页

**接口路径：** `GET /mailbox/statistics/efficiency/page`

**接口描述：** 分页查询各单位办理效率统计数据

**权限标识：** `mailbox:statistics:efficiency-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10，最大100 |
| unitId | Long | 否 | 办理单位编号 |
| unitName | String | 否 | 办理单位名称(模糊查询) |
| parentUnitId | Long | 否 | 上级单位编号(查询下属单位) |
| statPeriod | String | 否 | 统计周期(DAILY/WEEKLY/MONTHLY/QUARTERLY) |
| statYear | Integer | 否 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |
| startDate | Date | 否 | 开始日期(@DateTimeFormat) |
| endDate | Date | 否 | 结束日期(@DateTimeFormat) |
| orderBy | String | 否 | 排序字段(completion_rate/overtime_rate/avg_handle_days) |
| orderDirection | String | 否 | 排序方向(ASC/DESC)，默认DESC |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "total": 50,
    "list": [
      {
        "id": 1,
        "unitId": 100,
        "unitName": "交警大队",
        "unitCode": "JJDD001",
        "statDate": "2026-04-12",
        "statPeriod": "MONTHLY",
        "statYear": 2026,
        "statMonth": 4,
        "totalCount": 120,
        "completedCount": 108,
        "overtimeCount": 12,
        "extendedCount": 5,
        "avgHandleDays": 3.5,
        "completionRate": 90.00,
        "overtimeRate": 10.00,
        "extensionRate": 4.17,
        "reminderCount": 25,
        "avgReminder": 0.21,
        "createTime": "2026-04-13 02:00:00"
      }
    ]
  }
}
```

#### Controller代码示例

```java
@GetMapping("/efficiency/page")
@Operation(summary = "获取办理效率统计分页")
@PreAuthorize("@ss.hasPermission('mailbox:statistics:efficiency-query')")
public CommonResult<PageResult<EfficiencyStatisticsRespVO>> getEfficiencyPage(
        @Valid EfficiencyStatisticsPageReqVO pageReqVO) {
    PageResult<EfficiencyStatisticsDO> pageResult = statisticsService.getEfficiencyPage(pageReqVO);
    return success(BeanUtils.toBean(pageResult, EfficiencyStatisticsRespVO.class));
}
```

---

### 2.2 获取办理效率统计详情

**接口路径：** `GET /mailbox/statistics/efficiency/get`

**接口描述：** 获取指定单位办理效率统计详情及趋势分析

**权限标识：** `mailbox:statistics:efficiency-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 统计记录编号 |
| includeTrend | Boolean | 否 | 是否包含趋势数据，默认false |
| includeTypeDistribution | Boolean | 否 | 是否包含类型分布，默认false |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "id": 1,
    "unitId": 100,
    "unitName": "交警大队",
    "totalCount": 120,
    "completedCount": 108,
    "overtimeCount": 12,
    "avgHandleDays": 3.5,
    "completionRate": 90.00,
    "overtimeRate": 10.00,
    "typeDistribution": {
      "投诉类": 45,
      "咨询类": 30,
      "建议类": 25,
      "求助类": 20
    },
    "trendData": [
      {"date": "2026-04-01", "completionRate": 85.5, "avgHandleDays": 4.2},
      {"date": "2026-04-08", "completionRate": 88.0, "avgHandleDays": 3.8},
      {"date": "2026-04-15", "completionRate": 90.0, "avgHandleDays": 3.5}
    ]
  }
}
```

---

### 2.3 获取超期信件明细列表

**接口路径：** `GET /mailbox/statistics/efficiency/overtime-detail`

**接口描述：** 获取指定单位超期信件明细列表

**权限标识：** `mailbox:statistics:efficiency-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| unitId | Long | 是 | 办理单位编号 |
| statPeriod | String | 是 | 统计周期 |
| statYear | Integer | 是 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认20 |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "total": 12,
    "list": [
      {
        "letterId": 1001,
        "letterNo": "XL202604010001",
        "letterTitle": "关于交通违章处理的咨询",
        "assignTime": "2026-04-01 10:30:00",
        "submitTime": "2026-04-08 15:00:00",
        "overtimeDays": 3,
        "handleStatus": "已提交",
        "reminderCount": 2
      }
    ]
  }
}
```

---

### 2.4 导出办理效率统计Excel

**接口路径：** `GET /mailbox/statistics/efficiency/export-excel`

**接口描述：** 导出办理效率统计报表为Excel文件

**权限标识：** `mailbox:statistics:efficiency-export`

#### 请求参数

同 `GET /mailbox/statistics/efficiency/page` 分页查询参数（无分页参数，导出全部符合条件的记录）

#### 响应格式

直接写入HttpServletResponse，返回Excel文件流。

文件名格式：`办理效率统计_{时间戳}.xlsx`

#### Controller代码示例

```java
@GetMapping("/efficiency/export-excel")
@Operation(summary = "导出办理效率统计Excel")
@PreAuthorize("@ss.hasPermission('mailbox:statistics:efficiency-export')")
@ApiAccessLog(operateType = EXPORT)
public void exportEfficiencyExcel(@Valid EfficiencyStatisticsPageReqVO pageReqVO,
                                  HttpServletResponse response) throws IOException {
    pageReqVO.setPageSize(PageParam.PAGE_SIZE_NONE);
    List<EfficiencyStatisticsDO> list = statisticsService.getEfficiencyPage(pageReqVO).getList();
    List<EfficiencyStatisticsExcelVO> excelList = BeanUtils.toBean(list, EfficiencyStatisticsExcelVO.class);
    ExcelUtils.write(response, "办理效率统计.xlsx", "数据", EfficiencyStatisticsExcelVO.class, excelList);
}
```

---

## 3. 回复质量统计接口

### 3.1 获取回复质量统计分页

**接口路径：** `GET /mailbox/statistics/quality/page`

**接口描述：** 分页查询各单位回复质量统计数据

**权限标识：** `mailbox:statistics:quality-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| unitId | Long | 否 | 办理单位编号 |
| unitName | String | 否 | 办理单位名称(模糊查询) |
| parentUnitId | Long | 否 | 上级单位编号 |
| statPeriod | String | 否 | 统计周期 |
| statYear | Integer | 否 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |
| startDate | Date | 否 | 开始日期 |
| endDate | Date | 否 | 结束日期 |
| orderBy | String | 否 | 排序字段(first_pass_rate/return_modify_rate/avg_audit_rounds) |
| orderDirection | String | 否 | 排序方向，默认DESC |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "total": 50,
    "list": [
      {
        "id": 1,
        "unitId": 100,
        "unitName": "交警大队",
        "statDate": "2026-04-12",
        "statPeriod": "MONTHLY",
        "replyCount": 100,
        "firstPassCount": 85,
        "finalPassCount": 95,
        "returnModifyCount": 10,
        "returnRehandleCount": 5,
        "avgAuditRounds": 1.2,
        "firstPassRate": 85.00,
        "finalPassRate": 95.00,
        "returnModifyRate": 10.00,
        "returnRehandleRate": 5.00,
        "createTime": "2026-04-13 02:00:00"
      }
    ]
  }
}
```

---

### 3.2 获取回复质量统计详情

**接口路径：** `GET /mailbox/statistics/quality/get`

**接口描述：** 获取指定单位回复质量统计详情及审核流程分析

**权限标识：** `mailbox:statistics:quality-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 统计记录编号 |
| includeAuditFlow | Boolean | 否 | 是否包含审核流程分布，默认false |
| includeReturnReason | Boolean | 否 | 是否包含退回原因分析，默认false |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "id": 1,
    "unitId": 100,
    "unitName": "交警大队",
    "replyCount": 100,
    "firstPassRate": 85.00,
    "avgAuditRounds": 1.2,
    "auditFlowDistribution": {
      "一审通过": 80,
      "直接终审通过": 15,
      "退回修改": 5
    },
    "returnReasonDistribution": {
      "内容不规范": 3,
      "事实不清": 2,
      "需要补充材料": 5
    },
    "avgAuditNodeTime": {
      "一审": 0.5,
      "终审": 1.0
    }
  }
}
```

---

### 3.3 获取低质量单位预警列表

**接口路径：** `GET /mailbox/statistics/quality/warning-list`

**接口描述：** 获取一次通过率低于阈值的单位列表

**权限标识：** `mailbox:statistics:quality-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| threshold | Decimal | 否 | 预警阈值，默认60% |
| statPeriod | String | 否 | 统计周期 |
| statYear | Integer | 否 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "totalWarning": 5,
    "list": [
      {
        "unitId": 200,
        "unitName": "法制科",
        "firstPassRate": 55.00,
        "returnModifyCount": 15,
        "returnRehandleCount": 8
      }
    ]
  }
}
```

---

### 3.4 对比分析多个单位质量

**接口路径：** `GET /mailbox/statistics/quality/compare`

**接口描述：** 对比分析多个单位(最多5个)的回复质量

**权限标识：** `mailbox:statistics:quality-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| unitIds | List<Long> | 是 | 单位编号列表(最多5个) |
| statPeriod | String | 是 | 统计周期 |
| statYear | Integer | 是 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "comparisonData": [
      {
        "unitId": 100,
        "unitName": "交警大队",
        "firstPassRate": 85.00,
        "returnModifyRate": 10.00,
        "avgAuditRounds": 1.2
      },
      {
        "unitId": 101,
        "unitName": "刑侦大队",
        "firstPassRate": 90.00,
        "returnModifyRate": 5.00,
        "avgAuditRounds": 1.0
      }
    ],
    "radarChartData": {
      "dimensions": ["一次通过率", "终审通过率", "退回修改率", "重新办理率", "审核轮次"],
      "values": [
        [85, 95, 10, 5, 1.2],
        [90, 98, 5, 2, 1.0]
      ]
    }
  }
}
```

---

### 3.5 导出回复质量统计Excel

**接口路径：** `GET /mailbox/statistics/quality/export-excel`

**接口描述：** 导出回复质量统计报表为Excel文件

**权限标识：** `mailbox:statistics:quality-export`

#### 响应格式

直接写入HttpServletResponse，返回Excel文件流。

文件名格式：`回复质量统计_{时间戳}.xlsx`

---

## 4. 满意度统计接口

### 4.1 获取满意度统计分页

**接口路径：** `GET /mailbox/statistics/satisfaction/page`

**接口描述：** 分页查询各单位满意度统计数据

**权限标识：** `mailbox:statistics:satisfaction-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| unitId | Long | 否 | 办理单位编号 |
| unitName | String | 否 | 办理单位名称(模糊查询) |
| parentUnitId | Long | 否 | 上级单位编号 |
| statPeriod | String | 否 | 统计周期 |
| statYear | Integer | 否 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |
| unsatisfiedRateMin | Decimal | 否 | 不满意率最低筛选(如查询不满意率>10%的单位) |
| orderBy | String | 否 | 排序字段(satisfied_rate/composite_score) |
| orderDirection | String | 否 | 排序方向，默认DESC |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "total": 50,
    "list": [
      {
        "id": 1,
        "unitId": 100,
        "unitName": "交警大队",
        "statDate": "2026-04-12",
        "statPeriod": "MONTHLY",
        "evaluatedCount": 80,
        "satisfiedCount": 70,
        "neutralCount": 8,
        "unsatisfiedCount": 2,
        "pendingEvalCount": 20,
        "satisfiedRate": 87.50,
        "neutralRate": 10.00,
        "unsatisfiedRate": 2.50,
        "pendingEvalRate": 20.00,
        "compositeScore": 85,
        "createTime": "2026-04-13 02:00:00"
      }
    ]
  }
}
```

---

### 4.2 获取满意度统计详情

**接口路径：** `GET /mailbox/statistics/satisfaction/get`

**接口描述：** 获取指定单位满意度统计详情及各维度分析

**权限标识：** `mailbox:statistics:satisfaction-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 统计记录编号 |
| includeDimensions | Boolean | 否 | 是否包含各维度满意度，默认true |
| includeTrend | Boolean | 否 | 是否包含满意度趋势，默认false |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "id": 1,
    "unitId": 100,
    "unitName": "交警大队",
    "evaluatedCount": 80,
    "satisfiedRate": 87.50,
    "compositeScore": 85,
    "avgResolutionSat": 4.2,
    "avgResponseSat": 4.5,
    "avgAttitudeSat": 4.6,
    "avgAbilitySat": 4.3,
    "avgFollowupSat": 4.0,
    "dimensionRadarData": {
      "dimensions": ["诉求解决", "响应速度", "服务态度", "办事能力", "跟进服务"],
      "values": [4.2, 4.5, 4.6, 4.3, 4.0]
    },
    "trendData": [
      {"date": "2026-03", "satisfiedRate": 82.5, "compositeScore": 80},
      {"date": "2026-04", "satisfiedRate": 87.5, "compositeScore": 85}
    ]
  }
}
```

---

### 4.3 获取满意度趋势图数据

**接口路径：** `GET /mailbox/statistics/satisfaction/trend`

**接口描述：** 获取满意度趋势折线图数据

**权限标识：** `mailbox:statistics:satisfaction-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| unitIds | List<Long> | 否 | 单位编号列表(最多5个)，为空时返回全部 |
| startDate | Date | 是 | 开始日期 |
| endDate | Date | 是 | 结束日期 |
| granularity | String | 否 | 时间粒度(WEEK/MONTH)，默认MONTH |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "trendData": [
      {
        "date": "2026-01",
        "unitData": [
          {"unitId": 100, "unitName": "交警大队", "satisfiedRate": 80.0},
          {"unitId": 101, "unitName": "刑侦大队", "satisfiedRate": 85.0}
        ]
      },
      {
        "date": "2026-02",
        "unitData": [
          {"unitId": 100, "unitName": "交警大队", "satisfiedRate": 82.5},
          {"unitId": 101, "unitName": "刑侦大队", "satisfiedRate": 88.0}
        ]
      }
    ]
  }
}
```

---

### 4.4 获取不满意信件明细

**接口路径：** `GET /mailbox/statistics/satisfaction/unsatisfied-detail`

**接口描述：** 获取指定单位不满意评价信件明细

**权限标识：** `mailbox:statistics:satisfaction-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| unitId | Long | 是 | 办理单位编号 |
| statPeriod | String | 是 | 统计周期 |
| statYear | Integer | 是 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |
| pageNo | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页条数 |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "total": 2,
    "list": [
      {
        "letterId": 1001,
        "letterNo": "XL202604010001",
        "letterTitle": "关于交通违章处理的投诉",
        "overallSatisfaction": "不满意",
        "unsatisfiedReason": "处理结果不满意",
        "feedbackContent": "问题未得到实质性解决",
        "evalTime": "2026-04-10 10:00:00"
      }
    ]
  }
}
```

---

### 4.5 获取满意度与效率关联分析

**接口路径：** `GET /mailbox/statistics/satisfaction/correlation`

**接口描述：** 获取满意度与办理效率的关联散点图数据

**权限标识：** `mailbox:statistics:satisfaction-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| statPeriod | String | 是 | 统计周期 |
| statYear | Integer | 是 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |
| parentUnitId | Long | 否 | 上级单位编号(限定范围) |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "scatterData": [
      {
        "unitId": 100,
        "unitName": "交警大队",
        "avgHandleDays": 3.5,
        "satisfiedRate": 87.5,
        "compositeScore": 85
      },
      {
        "unitId": 101,
        "unitName": "刑侦大队",
        "avgHandleDays": 4.2,
        "satisfiedRate": 80.0,
        "compositeScore": 78
      }
    ],
    "correlationCoefficient": -0.65
  }
}
```

---

### 4.6 获取未评价信件统计

**接口路径：** `GET /mailbox/statistics/satisfaction/unevaluated`

**接口描述：** 获取各单位未收集满意度评价的信件统计

**权限标识：** `mailbox:statistics:satisfaction-query`

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "total": 50,
    "list": [
      {
        "unitId": 100,
        "unitName": "交警大队",
        "pendingEvalCount": 20,
        "shouldEvalCount": 100,
        "pendingEvalRate": 20.00
      }
    ]
  }
}
```

---

### 4.7 导出满意度统计Excel

**接口路径：** `GET /mailbox/statistics/satisfaction/export-excel`

**接口描述：** 导出满意度统计报表为Excel文件

**权限标识：** `mailbox:statistics:satisfaction-export`

#### 响应格式

文件名格式：`满意度统计_{时间戳}.xlsx`

---

## 5. 综合绩效接口

### 5.1 获取综合绩效汇总分页

**接口路径：** `GET /mailbox/statistics/performance/page`

**接口描述：** 分页查询各单位综合绩效汇总数据

**权限标识：** `mailbox:statistics:performance-query`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| pageNo | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页条数，默认10 |
| unitId | Long | 否 | 办理单位编号 |
| unitName | String | 否 | 办理单位名称 |
| parentUnitId | Long | 否 | 上级单位编号 |
| statPeriod | String | 否 | 统计周期 |
| statYear | Integer | 否 | 统计年份 |
| statMonth | Integer | 否 | 统计月份 |
| warningLevel | String | 否 | 预警等级筛选(NORMAL/WARNING/SERIOUS) |
| orderBy | String | 否 | 排序字段(total_rank/total_score) |
| orderDirection | String | 否 | 排序方向，默认ASC(排名升序) |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "total": 50,
    "list": [
      {
        "id": 1,
        "unitId": 100,
        "unitName": "交警大队",
        "statDate": "2026-04-12",
        "statPeriod": "MONTHLY",
        "totalScore": 85,
        "efficiencyScore": 88,
        "qualityScore": 82,
        "satisfactionScore": 85,
        "totalRank": 3,
        "efficiencyRank": 2,
        "qualityRank": 5,
        "satisfactionRank": 4,
        "warningLevel": "NORMAL",
        "yoyChange": 5.2,
        "momChange": 2.1,
        "createTime": "2026-04-13 02:00:00"
      }
    ]
  }
}
```

---

### 5.2 获取综合绩效详情

**接口路径：** `GET /mailbox/statistics/performance/get`

**接口描述：** 获取指定单位综合绩效详情及各维度得分明细

**权限标识：** `mailbox:statistics:performance-query`

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "id": 1,
    "unitId": 100,
    "unitName": "交警大队",
    "totalScore": 85,
    "efficiencyScore": 88,
    "qualityScore": 82,
    "satisfactionScore": 85,
    "totalRank": 3,
    "keyIndicators": {
      "completionRate": 90.0,
      "avgHandleDays": 3.5,
      "firstPassRate": 85.0,
      "satisfiedRate": 87.5
    },
    "scoreBreakdown": {
      "efficiency": {
        "completionScore": 45,
        "overtimeScore": 27,
        "handleDaysScore": 16
      },
      "quality": {
        "firstPassScore": 42.5,
        "returnModifyScore": 27,
        "auditRoundsScore": 12.5
      },
      "satisfaction": {
        "satisfiedRateScore": 43.75,
        "compositeScoreScore": 25.5,
        "unsatisfiedScore": 15.75
      }
    },
    "warningReasons": []
  }
}
```

---

### 5.3 获取预警单位汇总

**接口路径：** `GET /mailbox/statistics/performance/warning-summary`

**接口描述：** 获取绩效预警单位汇总列表

**权限标识：** `mailbox:statistics:performance-query`

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "totalWarning": 5,
    "seriousCount": 2,
    "warningCount": 3,
    "list": [
      {
        "unitId": 200,
        "unitName": "法制科",
        "totalScore": 55,
        "warningLevel": "SERIOUS",
        "warningReasons": ["综合得分低于60分", "满意度得分低于50分"]
      }
    ]
  }
}
```

---

### 5.4 导出综合绩效Excel

**接口路径：** `GET /mailbox/statistics/performance/export-excel`

**权限标识：** `mailbox:statistics:performance-export`

---

## 6. 统计刷新接口

### 6.1 手动刷新统计数据

**接口路径：** `POST /mailbox/statistics/refresh`

**接口描述：** 手动触发统计数据实时刷新（每小时最多1次）

**权限标识：** `mailbox:statistics:refresh`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| refreshType | String | 是 | 刷新类型(EFFICIENCY/QUALITY/SATISFACTION/ALL) |
| unitIds | List<Long> | 否 | 指定刷新的单位编号列表(为空时刷新权限范围内所有) |
| statDate | Date | 否 | 指定刷新日期(为空时刷新当天) |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "taskId": 12345,
    "status": "RUNNING",
    "message": "统计刷新任务已启动",
    "estimatedDuration": 120
  }
}
```

---

### 6.2 获取统计刷新任务状态

**接口路径：** `GET /mailbox/statistics/refresh/status`

**接口描述：** 获取统计刷新任务执行状态

**权限标识：** `mailbox:statistics:refresh`

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| taskId | Long | 是 | 任务编号 |

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "taskId": 12345,
    "taskName": "效率统计刷新",
    "status": "SUCCESS",
    "startTime": "2026-04-13 10:00:00",
    "endTime": "2026-04-13 10:02:00",
    "duration": 120,
    "processedCount": 50,
    "errorCount": 0
  }
}
```

---

### 6.3 获取数据最后更新时间

**接口路径：** `GET /mailbox/statistics/last-update-time`

**接口描述：** 获取各统计数据的最后更新时间

**权限标识：** `mailbox:statistics:efficiency-query` 或其他查询权限

#### 响应格式

```json
{
  "code": 0,
  "msg": "成功",
  "data": {
    "efficiencyUpdateTime": "2026-04-13 02:00:00",
    "qualityUpdateTime": "2026-04-13 02:30:00",
    "satisfactionUpdateTime": "2026-04-13 03:00:00",
    "performanceUpdateTime": "2026-04-13 04:00:00"
  }
}
```

---

## 7. 权限SQL配置

```sql
-- 绩效统计菜单
INSERT INTO system_menu (name, permission, type, sort, parent_id, path, icon, component, status)
VALUES ('绩效统计', '', 2, 0, {父菜单ID}, 'statistics', 'ep:data-analysis', 'statistics/index', 0);

-- 绩效统计子菜单按钮权限
SET @menuId = LAST_INSERT_ID();

INSERT INTO system_menu (name, permission, type, sort, parent_id, status) VALUES
('办理效率统计查询', 'mailbox:statistics:efficiency-query', 3, 1, @menuId, 0),
('办理效率统计导出', 'mailbox:statistics:efficiency-export', 3, 2, @menuId, 0),
('回复质量统计查询', 'mailbox:statistics:quality-query', 3, 3, @menuId, 0),
('回复质量统计导出', 'mailbox:statistics:quality-export', 3, 4, @menuId, 0),
('满意度统计查询', 'mailbox:statistics:satisfaction-query', 3, 5, @menuId, 0),
('满意度统计导出', 'mailbox:statistics:satisfaction-export', 3, 6, @menuId, 0),
('综合绩效查询', 'mailbox:statistics:performance-query', 3, 7, @menuId, 0),
('综合绩效导出', 'mailbox:statistics:performance-export', 3, 8, @menuId, 0),
('统计刷新', 'mailbox:statistics:refresh', 3, 9, @menuId, 0);
```

---

## 8. 错误码定义

```java
// ========== 绩效统计模块相关错误码 1_009_XXX_XXX ==========

// 统计任务相关错误码 1_009_001_XXX
ErrorCode STATISTICS_TASK_NOT_FOUND = new ErrorCode(1_009_001_000, "统计任务不存在");
ErrorCode STATISTICS_TASK_RUNNING = new ErrorCode(1_009_001_001, "统计任务正在执行中，请稍后查询");
ErrorCode STATISTICS_TASK_FAILED = new ErrorCode(1_009_001_002, "统计任务执行失败：{}");
ErrorCode STATISTICS_REFRESH_LIMITED = new ErrorCode(1_009_001_003, "刷新次数已达上限，每小时最多刷新1次");

// 统计数据相关错误码 1_009_002_XXX
ErrorCode STATISTICS_DATA_NOT_FOUND = new ErrorCode(1_009_002_000, "统计数据不存在");
ErrorCode STATISTICS_PERIOD_INVALID = new ErrorCode(1_009_002_001, "统计周期参数无效");
ErrorCode STATISTICS_DATE_RANGE_INVALID = new ErrorCode(1_009_002_002, "统计日期范围无效，最大不超过1年");
ErrorCode STATISTICS_UNIT_NOT_IN_SCOPE = new ErrorCode(1_009_002_003, "该单位不在您的统计权限范围内");

// 导出相关错误码 1_009_003_XXX
ErrorCode STATISTICS_EXPORT_NO_DATA = new ErrorCode(1_009_003_000, "当前筛选条件下无数据可导出");
ErrorCode STATISTICS_EXPORT_LIMITED = new ErrorCode(1_009_003_001, "导出数据量超过上限，建议分批导出");
ErrorCode STATISTICS_EXPORT_PERMISSION_DENIED = new ErrorCode(1_009_003_002, "您没有导出统计数据的权限");

// 对比分析相关错误码 1_009_004_XXX
ErrorCode STATISTICS_COMPARE_UNIT_LIMIT = new ErrorCode(1_009_004_000, "对比分析最多支持5个单位");
ErrorCode STATISTICS_COMPARE_UNIT_REQUIRED = new ErrorCode(1_009_004_001, "请选择对比单位");
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，完成API设计 | Claude |