# M03 审核流程模块 - API设计

## 文档信息

**产品名称：** gaxx-pro 信件处理系统
**模块编号：** M03
**文档版本：** v1.0
**创建日期：** 2026-04-13
**状态：** 草稿

---

## 1. API接口列表

### 1.1 接口总览

| 序号 | 接口路径 | HTTP方法 | 接口说明 | 权限标识 |
|------|----------|----------|----------|----------|
| 1 | /mailbox/letter/review/submit-reply | POST | 提交回复内容 | mailbox:letter:review:submit-reply |
| 2 | /mailbox/letter/review/save-reply-draft | POST | 保存回复草稿 | mailbox:letter:review:save-reply-draft |
| 3 | /mailbox/letter/review/submit-handle | POST | 提交办理表 | mailbox:letter:review:submit-handle |
| 4 | /mailbox/letter/review/return-down | POST | 下级单位退回 | mailbox:letter:review:return-down |
| 5 | /mailbox/letter/review/return-modify | POST | 上级退回重新修改 | mailbox:letter:review:return-modify |
| 6 | /mailbox/letter/review/return-rehandle | POST | 上级退回重新办理 | mailbox:letter:review:return-rehandle |
| 7 | /mailbox/letter/review/first-pass | POST | 一审通过 | mailbox:letter:review:first-pass |
| 8 | /mailbox/letter/review/final-pass | POST | 终审通过 | mailbox:letter:review:final-pass |
| 9 | /mailbox/letter/review/direct-final-pass | POST | 直接终审通过 | mailbox:letter:review:direct-final-pass |
| 10 | /mailbox/letter/review/reject | POST | 审核不通过 | mailbox:letter:review:reject |
| 11 | /mailbox/letter/review/reassign-unit | POST | 改派处室 | mailbox:letter:review:reassign-unit |
| 12 | /mailbox/letter/review/get-flow | GET | 获取审核流程 | mailbox:letter:review:query |
| 13 | /mailbox/letter/review/get-flow-history | GET | 获取流程历史 | mailbox:letter:review:query |
| 14 | /mailbox/letter/review/get-review-detail | GET | 获取审核详情 | mailbox:letter:review:query |
| 15 | /fz/rereview/reget-ai-template | GET | 重新获取AI模板 | mailbox:letter:review:reget-ai-template |

---

## 2. 接口详细设计

### 2.1 提交回复内容 (F03-01)

**接口路径：** `/mailbox/letter/review/submit-reply`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:submit-reply`
**功能说明：** 主办单位提交信件回复内容，进入审核流程

#### 请求参数

```java
@Tag(name = "管理后台 - 审核流程")
@RestController
@RequestMapping("/mailbox/letter/review")
@Validated
public class ReviewController {

    @PostMapping("/submit-reply")
    @Operation(summary = "提交回复内容")
    @PreAuthorize("@ss.hasPermission('mailbox:letter:review:submit-reply')")
    public CommonResult<Long> submitReply(@Valid @RequestBody ReplySubmitReqVO reqVO) {
        return success(reviewService.submitReply(reqVO));
    }
}
```

#### 请求VO

```java
@Schema(description = "提交回复请求VO")
public class ReplySubmitReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "回复内容", requiredMode = Schema.RequiredMode.REQUIRED, example = "您好，关于您反映的问题...")
    @NotBlank(message = "回复内容不能为空")
    @Size(max = 5000, message = "回复内容长度不能超过5000字符")
    private String replyContent;

    @Schema(description = "是否同时提交办理表", example = "true")
    private Boolean submitHandleTogether;

    @Schema(description = "办理表信息（同时提交时必填）")
    private HandleInfoVO handleInfo;
}
```

#### 响应结果

```java
CommonResult<Long>
// 返回审核记录ID
```

---

### 2.2 保存回复草稿 (F03-01)

**接口路径：** `/mailbox/letter/review/save-reply-draft`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:save-reply-draft`
**功能说明：** 主办单位保存回复草稿，不进入审核流程

#### 请求参数

```java
@PostMapping("/save-reply-draft")
@Operation(summary = "保存回复草稿")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:save-reply-draft')")
public CommonResult<Long> saveReplyDraft(@Valid @RequestBody ReplyDraftSaveReqVO reqVO) {
    return success(reviewService.saveReplyDraft(reqVO));
}
```

#### 请求VO

```java
@Schema(description = "保存回复草稿请求VO")
public class ReplyDraftSaveReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "回复内容", example = "您好，关于您反映的问题...")
    @Size(max = 5000, message = "回复内容长度不能超过5000字符")
    private String replyContent;

    @Schema(description = "草稿ID（更新草稿时传入）", example = "1")
    private Long draftId;
}
```

#### 响应结果

```java
CommonResult<Long>
// 返回回复记录ID
```

---

### 2.3 提交办理表 (F03-02)

**接口路径：** `/mailbox/letter/review/submit-handle`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:submit-handle`
**功能说明：** 主办单位提交信件办理表信息

#### 请求参数

```java
@PostMapping("/submit-handle")
@Operation(summary = "提交办理表")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:submit-handle')")
public CommonResult<Long> submitHandle(@Valid @RequestBody HandleSubmitReqVO reqVO) {
    return success(reviewService.submitHandle(reqVO));
}
```

#### 请求VO

```java
@Schema(description = "提交办理表请求VO")
public class HandleSubmitReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    // 办理人信息
    @Schema(description = "办理人姓名", requiredMode = Schema.RequiredMode.REQUIRED, example = "张三")
    @NotBlank(message = "办理人姓名不能为空")
    private String handlerName;

    @Schema(description = "办理人身份证号", example = "123456789012345678")
    @Pattern(regexp = "^[1-9]\\d{5}(18|19|20)\\d{2}(0[1-9]|1[0-2])(0[1-9]|[12]\\d|3[01])\\d{3}[0-9Xx]$",
             message = "身份证号格式不正确")
    private String handlerIdCard;

    @Schema(description = "办理人联系电话", example = "13800138000")
    private String handlerPhone;

    @Schema(description = "办理人所在单位", example = "XX分局")
    private String handlerUnit;

    // 回访信息
    @Schema(description = "是否已回访", example = "true")
    private Boolean isRevisited;

    @Schema(description = "回访时间")
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime visitTime;

    @Schema(description = "回访情况", example = "已电话回访，群众表示满意")
    @Size(max = 1000, message = "回访情况长度不能超过1000字符")
    private String visitSituation;

    @Schema(description = "群众反映", example = "群众对处理结果表示认可")
    @Size(max = 1000, message = "群众反映长度不能超过1000字符")
    private String publicReflect;

    @Schema(description = "未回访原因（未回访时必填）", example = "群众电话无法接通")
    @Size(max = 500, message = "未回访原因长度不能超过500字符")
    private String noVisitReason;

    // 接访信息
    @Schema(description = "是否已接访", example = "true")
    private Boolean isReceived;

    @Schema(description = "接访领导", example = "王局长")
    private String receiveLeader;

    @Schema(description = "接访时间")
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime receiveTime;

    @Schema(description = "接访方式：1-现场接访、2-电话接访、3-视频接访", example = "1")
    private Integer receiveType;

    @Schema(description = "接访情况")
    @Size(max = 1000, message = "接访情况长度不能超过1000字符")
    private String receiveSituation;

    // 调查信息
    @Schema(description = "调查情况")
    @Size(max = 2000, message = "调查情况长度不能超过2000字符")
    private String investigationSituation;

    @Schema(description = "无法调查原因")
    @Size(max = 500, message = "无法调查原因长度不能超过500字符")
    private String noInvestigationReason;

    // 满意度评价
    @Schema(description = "诉求解决满意度（1-5分）", example = "5")
    @Min(value = 1, message = "满意度评分最小为1")
    @Max(value = 5, message = "满意度评分最大为5")
    private Integer satisfactionResolve;

    @Schema(description = "响应速度满意度（1-5分）", example = "4")
    @Min(value = 1, message = "满意度评分最小为1")
    @Max(value = 5, message = "满意度评分最大为5")
    private Integer satisfactionResponse;

    @Schema(description = "办理态度满意度（1-5分）", example = "5")
    @Min(value = 1, message = "满意度评分最小为1")
    @Max(value = 5, message = "满意度评分最大为5")
    private Integer satisfactionAttitude;

    @Schema(description = "整体满意度（1-5分）", example = "4")
    @Min(value = 1, message = "满意度评分最小为1")
    @Max(value = 5, message = "满意度评分最大为5")
    private Integer satisfactionOverall;

    @Schema(description = "满意度备注")
    @Size(max = 500, message = "满意度备注长度不能超过500字符")
    private String satisfactionRemark;
}
```

#### 响应结果

```java
CommonResult<Long>
// 返回办理记录ID
```

---

### 2.4 下级单位退回 (F03-03)

**接口路径：** `/mailbox/letter/review/return-down`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:return-down`
**功能说明：** 主办单位将无法处理的信件退回给上级指派单位

#### 请求参数

```java
@PostMapping("/return-down")
@Operation(summary = "下级单位退回")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:return-down')")
public CommonResult<Boolean> returnDown(@Valid @RequestBody ReturnDownReqVO reqVO) {
    reviewService.returnDown(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "下级退回请求VO")
public class ReturnDownReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "退回原因", requiredMode = Schema.RequiredMode.REQUIRED, example = "本单位职责范围外，无法处理")
    @NotBlank(message = "退回原因不能为空")
    @Size(min = 10, max = 500, message = "退回原因长度需在10-500字符之间")
    private String returnReason;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.5 上级退回重新修改 (F03-04)

**接口路径：** `/mailbox/letter/review/return-modify`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:return-modify`
**功能说明：** 上级单位将回复内容不合格的信件退回给主办单位修改

#### 请求参数

```java
@PostMapping("/return-modify")
@Operation(summary = "上级退回重新修改")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:return-modify')")
public CommonResult<Boolean> returnModify(@Valid @RequestBody ReturnModifyReqVO reqVO) {
    reviewService.returnModify(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "上级退回重新修改请求VO")
public class ReturnModifyReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "审核记录ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "审核记录ID不能为空")
    private Long reviewId;

    @Schema(description = "退回意见", requiredMode = Schema.RequiredMode.REQUIRED, example = "回复内容表述不清，请补充具体处理措施")
    @NotBlank(message = "退回意见不能为空")
    @Size(min = 10, max = 1000, message = "退回意见长度需在10-1000字符之间")
    private String returnOpinion;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.6 上级退回重新办理 (F03-05)

**接口路径：** `/mailbox/letter/review/return-rehandle`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:return-rehandle`
**功能说明：** 上级单位将需要重新调查的信件退回给主办单位

#### 请求参数

```java
@PostMapping("/return-rehandle")
@Operation(summary = "上级退回重新办理")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:return-rehandle')")
public CommonResult<Boolean> returnRehandle(@Valid @RequestBody ReturnRehandleReqVO reqVO) {
    reviewService.returnRehandle(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "上级退回重新办理请求VO")
public class ReturnRehandleReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "审核记录ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "审核记录ID不能为空")
    private Long reviewId;

    @Schema(description = "退回意见", requiredMode = Schema.RequiredMode.REQUIRED, example = "调查情况不详细，请重新调查核实")
    @NotBlank(message = "退回意见不能为空")
    @Size(min = 10, max = 1000, message = "退回意见长度需在10-1000字符之间")
    private String returnOpinion;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.7 一审通过 (F03-06)

**接口路径：** `/mailbox/letter/review/first-pass`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:first-pass`
**功能说明：** 督办单位一审通过回复内容

#### 请求参数

```java
@PostMapping("/first-pass")
@Operation(summary = "一审通过")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:first-pass')")
public CommonResult<Boolean> firstPass(@Valid @RequestBody FirstPassReqVO reqVO) {
    reviewService.firstPass(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "一审通过请求VO")
public class FirstPassReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "审核记录ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "审核记录ID不能为空")
    private Long reviewId;

    @Schema(description = "审核意见", example = "回复内容规范，事实清楚，同意通过")
    @Size(max = 1000, message = "审核意见长度不能超过1000字符")
    private String reviewOpinion;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.8 终审通过 (F03-07)

**接口路径：** `/mailbox/letter/review/final-pass`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:final-pass`
**功能说明：** 接收单位终审通过回复内容

#### 请求参数

```java
@PostMapping("/final-pass")
@Operation(summary = "终审通过")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:final-pass')")
public CommonResult<Boolean> finalPass(@Valid @RequestBody FinalPassReqVO reqVO) {
    reviewService.finalPass(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "终审通过请求VO")
public class FinalPassReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "审核记录ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "审核记录ID不能为空")
    private Long reviewId;

    @Schema(description = "审核意见", example = "终审通过，回复内容符合规范要求")
    @Size(max = 1000, message = "审核意见长度不能超过1000字符")
    private String reviewOpinion;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.9 直接终审通过 (F03-08)

**接口路径：** `/mailbox/letter/review/direct-final-pass`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:direct-final-pass`
**功能说明：** 接收单位跳过一审直接终审通过

#### 请求参数

```java
@PostMapping("/direct-final-pass")
@Operation(summary = "直接终审通过")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:direct-final-pass')")
public CommonResult<Boolean> directFinalPass(@Valid @RequestBody DirectFinalPassReqVO reqVO) {
    reviewService.directFinalPass(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "直接终审通过请求VO")
public class DirectFinalPassReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "审核记录ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "审核记录ID不能为空")
    private Long reviewId;

    @Schema(description = "审核意见", example = "紧急事项，直接终审通过")
    @Size(max = 1000, message = "审核意见长度不能超过1000字符")
    private String reviewOpinion;

    @Schema(description = "直接终审原因", example = "紧急信件处理")
    @Size(max = 500, message = "直接终审原因长度不能超过500字符")
    private String directReason;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.10 审核不通过 (F03-09)

**接口路径：** `/mailbox/letter/review/reject`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:reject`
**功能说明：** 接收单位或督办单位驳回不合格的回复内容

#### 请求参数

```java
@PostMapping("/reject")
@Operation(summary = "审核不通过")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:reject')")
public CommonResult<Boolean> reject(@Valid @RequestBody RejectReqVO reqVO) {
    reviewService.reject(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "审核不通过请求VO")
public class RejectReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "审核记录ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "审核记录ID不能为空")
    private Long reviewId;

    @Schema(description = "驳回原因", requiredMode = Schema.RequiredMode.REQUIRED, example = "回复内容与事实不符")
    @NotBlank(message = "驳回原因不能为空")
    @Size(min = 10, max = 1000, message = "驳回原因长度需在10-1000字符之间")
    private String rejectReason;

    @Schema(description = "后续处理方式：1-重新修改、2-重新办理", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "后续处理方式不能为空")
    private Integer handleType;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.11 改派处室 (F03-10)

**接口路径：** `/mailbox/letter/review/reassign-unit`
**HTTP方法：** POST
**权限标识：** `mailbox:letter:review:reassign-unit`
**功能说明：** 接收单位更换信件的办理单位

#### 请求参数

```java
@PostMapping("/reassign-unit")
@Operation(summary = "改派处室")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:reassign-unit')")
public CommonResult<Boolean> reassignUnit(@Valid @RequestBody ReassignUnitReqVO reqVO) {
    reviewService.reassignUnit(reqVO);
    return success(true);
}
```

#### 请求VO

```java
@Schema(description = "改派处室请求VO")
public class ReassignUnitReqVO {

    @Schema(description = "信件ID", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "信件ID不能为空")
    private Long letterId;

    @Schema(description = "改派类型：1-改派主办单位、2-增加协办单位、3-取消协办单位", requiredMode = Schema.RequiredMode.REQUIRED, example = "1")
    @NotNull(message = "改派类型不能为空")
    private Integer reassignType;

    @Schema(description = "原单位ID（改派或取消时必填）", example = "1")
    private Long originalUnitId;

    @Schema(description = "新单位ID（改派或增加时必填）", example = "2")
    private Long newUnitId;

    @Schema(description = "改派原因", requiredMode = Schema.RequiredMode.REQUIRED, example = "原单位职责调整，需改派其他单位")
    @NotBlank(message = "改派原因不能为空")
    @Size(min = 10, max = 500, message = "改派原因长度需在10-500字符之间")
    private String reassignReason;
}
```

#### 响应结果

```java
CommonResult<Boolean>
// 返回操作成功状态
```

---

### 2.12 获取审核流程 (F03-11)

**接口路径：** `/mailbox/letter/review/get-flow`
**HTTP方法：** GET
**权限标识：** `mailbox:letter:review:query`
**功能说明：** 获取信件当前审核流程进度

#### 请求参数

```java
@GetMapping("/get-flow")
@Operation(summary = "获取审核流程")
@Parameter(name = "letterId", description = "信件ID", required = true, example = "1")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:query')")
public CommonResult<ReviewFlowRespVO> getFlow(@RequestParam("letterId") Long letterId) {
    return success(reviewService.getFlow(letterId));
}
```

#### 响应VO

```java
@Schema(description = "审核流程响应VO")
public class ReviewFlowRespVO {

    @Schema(description = "信件ID")
    private Long letterId;

    @Schema(description = "审核记录ID")
    private Long reviewId;

    @Schema(description = "审核编号")
    private String reviewNo;

    @Schema(description = "审核状态")
    private Integer reviewStatus;

    @Schema(description = "审核状态名称")
    private String reviewStatusName;

    @Schema(description = "当前流程节点")
    private String currentNode;

    @Schema(description = "当前处理角色")
    private String currentRole;

    @Schema(description = "提交时间")
    private LocalDateTime submitTime;

    @Schema(description = "一审时间")
    private LocalDateTime firstReviewTime;

    @Schema(description = "终审时间")
    private LocalDateTime finalReviewTime;

    @Schema(description = "驳回次数")
    private Integer rejectCount;

    @Schema(description = "重新修改次数")
    private Integer modifyCount;

    @Schema(description = "重新办理次数")
    private Integer rehandleCount;

    @Schema(description = "回复版本号")
    private Integer version;

    @Schema(description = "流程节点列表")
    private List<FlowNodeVO> flowNodes;
}
```

---

### 2.13 获取流程历史 (F03-11)

**接口路径：** `/mailbox/letter/review/get-flow-history`
**HTTP方法：** GET
**权限标识：** `mailbox:letter:review:query`
**功能说明：** 获取信件完整的审核流程历史记录

#### 请求参数

```java
@GetMapping("/get-flow-history")
@Operation(summary = "获取流程历史")
@Parameter(name = "letterId", description = "信件ID", required = true, example = "1")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:query')")
public CommonResult<List<ReviewFlowHistoryRespVO>> getFlowHistory(@RequestParam("letterId") Long letterId) {
    return success(reviewService.getFlowHistory(letterId));
}
```

#### 响应VO

```java
@Schema(description = "审核流程历史响应VO")
public class ReviewFlowHistoryRespVO {

    @Schema(description = "流程记录ID")
    private Long flowId;

    @Schema(description = "流程编号")
    private String flowNo;

    @Schema(description = "流程类型")
    private Integer flowType;

    @Schema(description = "流程类型名称")
    private String flowTypeName;

    @Schema(description = "流程名称")
    private String flowName;

    @Schema(description = "流程描述")
    private String flowDescription;

    @Schema(description = "来源信件状态")
    private Integer fromStatus;

    @Schema(description = "目标信件状态")
    private Integer toStatus;

    @Schema(description = "操作人姓名")
    private String operatorName;

    @Schema(description = "操作人单位名称")
    private String operatorUnitName;

    @Schema(description = "流程开始时间")
    private LocalDateTime startTime;

    @Schema(description = "流程结束时间")
    private LocalDateTime endTime;

    @Schema(description = "流程耗时（分钟）")
    private Integer durationMinutes;

    @Schema(description = "备注")
    private String remark;
}
```

---

### 2.14 获取审核详情

**接口路径：** `/mailbox/letter/review/get-review-detail`
**HTTP方法：** GET
**权限标识：** `mailbox:letter:review:query`
**功能说明：** 获取审核记录详情

#### 请求参数

```java
@GetMapping("/get-review-detail")
@Operation(summary = "获取审核详情")
@Parameter(name = "reviewId", description = "审核记录ID", required = true, example = "1")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:query')")
public CommonResult<ReviewDetailRespVO> getReviewDetail(@RequestParam("reviewId") Long reviewId) {
    return success(reviewService.getReviewDetail(reviewId));
}
```

#### 响应VO

```java
@Schema(description = "审核详情响应VO")
public class ReviewDetailRespVO {

    @Schema(description = "审核记录ID")
    private Long id;

    @Schema(description = "审核编号")
    private String reviewNo;

    @Schema(description = "信件ID")
    private Long letterId;

    @Schema(description = "审核状态")
    private Integer reviewStatus;

    @Schema(description = "审核状态名称")
    private String reviewStatusName;

    @Schema(description = "提交审核时间")
    private LocalDateTime submitTime;

    @Schema(description = "一审时间")
    private LocalDateTime firstReviewTime;

    @Schema(description = "一审人姓名")
    private String firstReviewerName;

    @Schema(description = "终审时间")
    private LocalDateTime finalReviewTime;

    @Schema(description = "终审人姓名")
    private String finalReviewerName;

    @Schema(description = "驳回次数")
    private Integer rejectCount;

    @Schema(description = "重新修改次数")
    private Integer modifyCount;

    @Schema(description = "重新办理次数")
    private Integer rehandleCount;

    @Schema(description = "回复版本号")
    private Integer version;

    @Schema(description = "审核意见列表")
    private List<ReviewOpinionVO> opinions;

    @Schema(description = "回复内容")
    private String replyContent;

    @Schema(description = "办理表信息")
    private HandleInfoVO handleInfo;
}
```

---

### 2.15 重新获取AI模板 (F01-09)

**接口路径：** `/mailbox/letter/review/reget-ai-template`
**HTTP方法：** GET
**权限标识：** `mailbox:letter:review:reget-ai-template`
**功能说明：** 办理单位重新获取AI生成的回复模板

#### 请求参数

```java
@GetMapping("/reget-ai-template")
@Operation(summary = "重新获取AI模板")
@Parameter(name = "letterId", description = "信件ID", required = true, example = "1")
@Parameter(name = "saveDraft", description = "是否保存为草稿", example = "true")
@PreAuthorize("@ss.hasPermission('mailbox:letter:review:reget-ai-template')")
public CommonResult<AiTemplateRespVO> regetAiTemplate(@RequestParam("letterId") Long letterId,
                                                        @RequestParam(value = "saveDraft", required = false, defaultValue = "false") Boolean saveDraft) {
    return success(reviewService.regetAiTemplate(letterId, saveDraft));
}
```

#### 响应VO

```java
@Schema(description = "AI模板响应VO")
public class AiTemplateRespVO {

    @Schema(description = "信件ID")
    private Long letterId;

    @Schema(description = "AI模板内容")
    private String templateContent;

    @Schema(description = "模板版本")
    private Integer templateVersion;

    @Schema(description = "生成时间")
    private LocalDateTime generateTime;

    @Schema(description = "草稿ID（保存草稿时返回）")
    private Long draftId;
}
```

---

## 3. 权限标识汇总

| 权限标识 | 权限名称 | 适用角色 |
|----------|----------|----------|
| mailbox:letter:review:submit-reply | 提交回复 | 主办单位 |
| mailbox:letter:review:save-reply-draft | 保存回复草稿 | 主办单位 |
| mailbox:letter:review:submit-handle | 提交办理表 | 主办单位 |
| mailbox:letter:review:return-down | 下级退回 | 主办单位 |
| mailbox:letter:review:return-modify | 上级退回重新修改 | 接收单位、督办单位 |
| mailbox:letter:review:return-rehandle | 上级退回重新办理 | 接收单位、督办单位 |
| mailbox:letter:review:first-pass | 一审通过 | 督办单位、接收单位 |
| mailbox:letter:review:final-pass | 终审通过 | 接收单位 |
| mailbox:letter:review:direct-final-pass | 直接终审通过 | 接收单位 |
| mailbox:letter:review:reject | 审核不通过 | 接收单位、督办单位 |
| mailbox:letter:review:reassign-unit | 改派处室 | 接收单位 |
| mailbox:letter:review:query | 查询审核流程 | 所有角色 |
| mailbox:letter:review:reget-ai-template | 重新获取AI模板 | 办理单位 |

---

## 4. 错误码定义

```java
// ========== 审核流程模块错误码 1_003_XXX_XXX ==========

// 审核记录相关 1_003_001_XXX
ErrorCode REVIEW_NOT_EXISTS = new ErrorCode(1_003_001_000, "审核记录不存在");
ErrorCode REVIEW_STATUS_ERROR = new ErrorCode(1_003_001_001, "审核状态不正确");
ErrorCode REVIEW_ALREADY_PASSED = new ErrorCode(1_003_001_002, "审核已通过，不能重复操作");
ErrorCode REVIEW_ALREADY_REJECTED = new ErrorCode(1_003_001_003, "审核已驳回，不能重复操作");

// 回复内容相关 1_003_002_XXX
ErrorCode REPLY_CONTENT_EMPTY = new ErrorCode(1_003_002_000, "回复内容不能为空");
ErrorCode REPLY_CONTENT_TOO_LONG = new ErrorCode(1_003_002_001, "回复内容长度超过限制");
ErrorCode REPLY_NOT_DRAFT = new ErrorCode(1_003_002_002, "回复非草稿状态，不能修改");
ErrorCode REPLY_ALREADY_SUBMITTED = new ErrorCode(1_003_002_003, "回复已提交，不能重复提交");

// 办理表相关 1_003_003_XXX
ErrorCode HANDLE_INFO_INCOMPLETE = new ErrorCode(1_003_003_000, "办理表信息不完整");
ErrorCode HANDLE_IDCARD_INVALID = new ErrorCode(1_003_003_001, "身份证号格式不正确");
ErrorCode HANDLE_SATISFACTION_INVALID = new ErrorCode(1_003_003_002, "满意度评分超出范围");
ErrorCode HANDLE_VISIT_TIME_INVALID = new ErrorCode(1_003_003_003, "回访时间不能大于当前时间");

// 权限相关 1_003_004_XXX
ErrorCode REVIEW_NO_PERMISSION = new ErrorCode(1_003_004_000, "没有审核权限");
ErrorCode NOT_HOST_UNIT = new ErrorCode(1_003_004_001, "只有主办单位可以操作");
ErrorCode NOT_RECEIVE_UNIT = new ErrorCode(1_003_004_002, "只有接收单位可以操作");
ErrorCode NOT_SUPERVISION_UNIT = new ErrorCode(1_003_004_003, "只有督办单位可以操作");

// 状态相关 1_003_005_XXX
ErrorCode LETTER_STATUS_ERROR = new ErrorCode(1_003_005_000, "信件当前状态不允许该操作");
ErrorCode LETTER_ALREADY_REPLY = new ErrorCode(1_003_005_001, "已提交回复的信件不能退回");
ErrorCode LETTER_ALREADY_COMPLETE = new ErrorCode(1_003_005_002, "已完成的信件不能操作");
ErrorCode LETTER_FIRST_REVIEW_REQUIRED = new ErrorCode(1_003_005_003, "请先完成一审");

// 流程相关 1_003_006_XXX
ErrorCode FLOW_RETURN_REASON_EMPTY = new ErrorCode(1_003_006_000, "退回原因不能为空");
ErrorCode FLOW_RETURN_REASON_TOO_SHORT = new ErrorCode(1_003_006_001, "退回原因长度不足");
ErrorCode FLOW_REJECT_REASON_EMPTY = new ErrorCode(1_003_006_002, "驳回原因不能为空");
ErrorCode FLOW_REASSIGN_REASON_EMPTY = new ErrorCode(1_003_006_003, "改派原因不能为空");
ErrorCode FLOW_NO_HOST_UNIT = new ErrorCode(1_003_006_004, "必须有主办单位，不能取消");

// AI模板相关 1_003_007_XXX
ErrorCode AI_SERVICE_UNAVAILABLE = new ErrorCode(1_003_007_000, "AI服务暂时不可用，请稍后重试");
ErrorCode AI_TEMPLATE_LIMIT_EXCEEDED = new ErrorCode(1_003_007_001, "AI模板生成次数超过限制");
ErrorCode AI_CONTENT_TOO_SHORT = new ErrorCode(1_003_007_002, "信件内容过短，无法生成有效模板");
```

---

## 变更历史

| 版本 | 日期 | 变更内容 | 变更人 |
|-----|------|---------|--------|
| v1.0 | 2026-04-13 | 初始版本，完成15个API接口设计 | Claude |