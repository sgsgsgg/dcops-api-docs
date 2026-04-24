# 服务对外 API

> 本文档由 `API.docx` 自动转换并润色生成，完整覆盖对外开放接口。

正式环境接口地址 https://dcops.idc.tencent.com/relay/open/ifob/rapi/ifob-fre

测试环境接口地址 https://dcops.test.tencent.com/relay/open/ifob/rapi/ifob-fre

鉴权说明

鉴权相关接口人：罗俊杰、刘宇

接口请求接口人：林小龙、周逸博、黄政声

业务数据接口人：

巡检：黄政声、林小龙、林俊基、李思泽

维护：卫俊瑶、方国亮、许国彪、王鹏

演练：冼振星

变更：付鹏磊

## 检索条件字段使用说明

所有的查询条件字段，均支持以下几种查询形式

1. 精确查询(=)

**示例：**

```json
{
  "Data": {
    "SearchCondition": {
      "SvrId": "123456"
    }
  }
}
```

2. 集合查询(IN)

**示例：**

```json
{
  "Data": {
    "SearchCondition": {
      "SN": [
        "NC00168019"
      ]
    }
  }
}
```

3. 范围查询(<,<=,>,>=)

支持的范围指令包括: gt(大于), gte(大于或等于), lt(小于), lte(小于或等于)，形式如:

**示例：**

```json
{
  "Data": {
    "SearchCondition": {
      "CreateTime": {
        "gt": "2018-02-01 10:29:30",
        "lt": "2018-02-02 10:29:30"
      }
    }
  }
}
```

## 巡检主表

**表结构：**

```sql
CREATE TABLE `inspection_main` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `ticket_id` varchar(255) DEFAULT NULL COMMENT '工单id\n',
  `region_name` varchar(255) DEFAULT NULL COMMENT '区域',
  `campus_name` varchar(255) DEFAULT NULL COMMENT '园区',
  `mozu_name` varchar(255) DEFAULT NULL COMMENT '模组',
  `task_name` varchar(255) DEFAULT NULL COMMENT '任务名称',
  `insp_type` varchar(255) DEFAULT NULL COMMENT '巡检类型',
  `planning_start_time` datetime DEFAULT NULL COMMENT '计划开始时间',
  `real_start_time` datetime DEFAULT NULL COMMENT '实际巡检开始时间',
  `real_end_time` datetime DEFAULT NULL COMMENT '实际巡检结束时间',
  `insp_status` varchar(255) DEFAULT NULL COMMENT '巡检状态',
  `insp_zone` varchar(255) DEFAULT NULL COMMENT '当前巡检区域',
  `insp_progress` varchar(255) DEFAULT NULL COMMENT '巡检进度，例：123/1878',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间\n',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间\n',
  `todo_user` varchar(1023) DEFAULT NULL COMMENT '计划巡检人',
  `real_todo_user` varchar(255) DEFAULT NULL COMMENT '实际巡检人',
  `monitor` varchar(255) DEFAULT NULL COMMENT '计划值班长',
  `real_monitor` varchar(255) DEFAULT NULL COMMENT '实际审核人',
  `audit_time` datetime DEFAULT NULL COMMENT '实际值班长审核时间',
  `check_result` varchar(255) DEFAULT NULL COMMENT '值班长审核结果：通过、不通过',
  `unpass_explain` varchar(255) DEFAULT '' COMMENT '值班长审核说明',
  `is_transfer` varchar(255) DEFAULT NULL COMMENT '是否转单：是、否',
  `is_abnormal` varchar(255) DEFAULT NULL COMMENT '是否异常：正常、异常',
  `abnormal_count` int(11) DEFAULT NULL COMMENT '异常数量',
  `is_timeout` varchar(255) DEFAULT NULL COMMENT '是否超时：未超时、已超时',
  `audit_duration` int(255) DEFAULT NULL COMMENT '值班长审核时长（秒）',
  `duration` int(11) DEFAULT NULL COMMENT '巡检时长（秒）',
  `timeout_duration` int(11) DEFAULT NULL COMMENT '超时时长（秒）',
  `pause_duration` int(11) DEFAULT NULL COMMENT '暂停时长（秒）',
  `point_count` int(11) DEFAULT NULL COMMENT '巡检点总量',
  `device_count` int(11) DEFAULT NULL COMMENT '设备总量',
  `rate` varchar(255) DEFAULT NULL COMMENT '巡检通过率%',
  `imp_date` bigint(20) DEFAULT NULL COMMENT '巡检日期',
  `service_status` varchar(9) DEFAULT NULL COMMENT '服务审批状态',
  `service_approver` varchar(20) DEFAULT NULL COMMENT '服务审批人',
  `service_detail` varchar(255) DEFAULT NULL COMMENT '服务审批说明',
  `sp_insp` varchar(20) DEFAULT NULL COMMENT '巡检服务商',
  `fmc_status` varchar(9) DEFAULT NULL COMMENT 'FMC审批状态',
  `fmc_approver` varchar(20) DEFAULT NULL COMMENT 'FMC审批人',
  `fmc_detail` varchar(255) DEFAULT NULL COMMENT 'FMC审批说明',
  `re_insp_count` int(11) DEFAULT NULL COMMENT '重检区域次数',
  `service_duration` int(11) DEFAULT NULL COMMENT '服务审批时长（秒）',
  `fmc_duration` int(11) DEFAULT NULL COMMENT 'FMC审批时长（秒）',
  `score` int(9) DEFAULT NULL COMMENT '评分',
  `asst_manager_status` varchar(9) DEFAULT NULL COMMENT '数经审批状态',
  `asst_manager_approver` varchar(45) DEFAULT NULL COMMENT '数经审批人',
  `asst_manager_detail` varchar(255) DEFAULT NULL COMMENT '数经审批说明',
  `asst_manager_duration` int(11) DEFAULT NULL COMMENT '数经审批时长（秒）',
  `duty_type` varchar(9) DEFAULT NULL COMMENT '白班/晚班',
  `group_name` varchar(25) DEFAULT NULL COMMENT '指定七彩石分组',
  `is_low` tinyint(4) DEFAULT NULL COMMENT '是否低于全国平均时长',
  `re_insp_data` text COMMENT '重检区域详情',
  `sub_system` varchar(25) DEFAULT NULL COMMENT '模组类型',
  PRIMARY KEY (`id`) USING BTREE,
  KEY `ticket_id` (`ticket_id`) USING BTREE,
  KEY `index_1` (`imp_date`) 
) ENGINE=InnoDB AUTO_INCREMENT=39624 DEFAULT CHARSET=utf8mb4 ROW_FORMAT=DYNAMIC
```

**请求示例：**

```json
{
   "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "120",
  "SchemaId": "inspection_main_service",
  "ReturnTotalRows": 0,
  "BussType": "巡检",
  "Data": {
     "ResultColumns": {
       "Id": "",
      "AbnormalCount": "",
      "CampusName": "",
      "CheckResult": "",
      "CreateTime": "",
      "Duration": "",
      "FmcApprover": "",
      "FmcDetail": "",
      "FmcStatus": "",
      "InspStatus": "",
      "InspType": "",
      "IsAbnormal": "",
      "IsTimeout": "",
      "MozuName": "",
      "PauseDuration": "",
      "PlanningStartTime": "",
      "Rate": "",
      "ReInspCount": "",
      "RealEndTime": "",
      "RealMonitor": "",
      "RealStartTime": "",
      "RealTodoUser": "",
      "ServiceApprover": "",
      "ServiceDetail": "",
      "ServiceStatus": "",
      "TaskName": "",
      "TicketId": "",
      "TimeoutDuration": "",
      "TodoUser": "",
      "UnpassExplain": "",
      "UpdateTime": "",
      "Score": 100,
      "ReInspData": "[{\"begin_time\": \"2024-10-21 11:32:14\", \"end_time\": \"2024-10-21 11:32:19\", \"id\": 82378, \"insp_zone\": \"弱电间\", \"re_insp_desc\": \"11重检11\", \"spend\": \"5秒\", \"ticket_id\": \"20241021002118\"}]" 
    },
    "SearchCondition": {
       "CampusName": "清远-清新",
      "MozuName": [
        "清远清新模组11"
      ],
      "PlanningStartTime": {
         "gte": "2024-02-20 09:07:33" 
      } 
    },
    "Sorts": [{
       "Column": "Id",
      "SortType": "desc"
    }],
    "Limit": {
       "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
   "Return": 0,
  "Details": "",
  "ReqId": "fob-gnetopsd0nyg51pfngg",
  "Data": {
     "List": [
       {
         "Id": 123,
        "TaskName": "仪征东升模组11_日常巡检_2023-05-11 18:00:06",
        "TicketId": "20230511002045",
        "UpdateTime": "2024-02-20 17:07:33" 
      },
      {
         "TaskName": "仪征东升模组11_日常巡检_2023-05-18 06:00:03",
        "TicketId": "20230518001296",
        "UpdateTime": "2024-02-20 16:57:49" 
      } 
    ],
    "TotalRows": 1078
}
}
```

## 维护自建主表

**表结构：**

```sql
CREATE TABLE `maintenance_ticket_main` (
  `id` varchar(50) NOT NULL,
  `ticket_id` varchar(50) DEFAULT NULL COMMENT '工单号',
  `process_title` varchar(100) DEFAULT NULL COMMENT '工单标题',
  `region_name` varchar(50) DEFAULT NULL COMMENT '区域名称',
  `campus_name` varchar(50) DEFAULT NULL COMMENT '园区名称',
  `mozu_name` varchar(50) DEFAULT NULL COMMENT '模组名称',
  `PM_engineer` varchar(100) DEFAULT NULL COMMENT '专业总工程师',
  `PM_phone` varchar(20) DEFAULT NULL COMMENT '工程师联系电话',
  `ticket_start_date` varchar(10) DEFAULT NULL COMMENT '建单日期',
  `devicetypes_name` varchar(100) DEFAULT NULL COMMENT '维护项目名称',
  `maintenance_type` varchar(50) DEFAULT NULL COMMENT '维护类型',
  `maintenance_cycle` varchar(100) DEFAULT NULL COMMENT '维护周期',
  `device_list` json DEFAULT NULL COMMENT '维护设备',
  `MOP` varchar(255) DEFAULT NULL COMMENT 'MOP',
  `SOP` varchar(255) DEFAULT NULL COMMENT 'SOP',
  `to_maintainer` varchar(4) DEFAULT NULL COMMENT '是否供应商维护',
  `to_change` varchar(4) DEFAULT NULL COMMENT '是否涉及变更',
  `maintainer` varchar(100) DEFAULT NULL COMMENT '维护厂商',
  `operator` varchar(1000) DEFAULT NULL COMMENT '实施人',
  `plan_start_date` varchar(10) DEFAULT NULL COMMENT '计划开始日期',
  `plan_end_date` varchar(10) DEFAULT NULL COMMENT '计划结束日期',
  `change_id` varchar(1000) DEFAULT NULL COMMENT '变更单号',
  `fmc_reject` varchar(2) DEFAULT NULL COMMENT 'FMC是否驳回：是/否',
  `fmc_user` varchar(50) DEFAULT NULL COMMENT 'FMC审核人',
  `re_check_list` json DEFAULT NULL COMMENT '前置检查结果',
  `tools_list` json DEFAULT NULL COMMENT '工具清单',
  `item` json DEFAULT NULL COMMENT '物料清单',
  `ticket_status` varchar(10) DEFAULT NULL COMMENT '工单状态',
  `status_light` varchar(2) DEFAULT NULL COMMENT '状态灯',
  `admin_comments` varchar(255) DEFAULT NULL COMMENT '管理员评论',
  `standard_time` float DEFAULT NULL COMMENT '标准耗时',
  `avg_time_consuming` float DEFAULT NULL COMMENT '平均耗时',
  `end_date` varchar(10) DEFAULT NULL COMMENT '结单日期',
  `once_pass` varchar(9) DEFAULT NULL COMMENT '一次通过',
  `use_mop` varchar(2) DEFAULT NULL COMMENT '是否mop维保',
  `madmin_review` varchar(9) DEFAULT NULL COMMENT 'FMC验收结果',
  `unqualified_type` varchar(50) DEFAULT NULL COMMENT '不合格项',
  `reason_for_madmin` varchar(1024) DEFAULT NULL COMMENT 'FMC验收意见',
  `professional` varchar(9) DEFAULT NULL COMMENT '专业',
  `supplier_times` float DEFAULT NULL COMMENT '服务商统计总耗时',
  `workers_count` float DEFAULT NULL COMMENT '服务商统计总人天',
  `ab_sub_ticket_count` int(9) DEFAULT NULL COMMENT '异常子单数量',
  `po_ticket_id` varchar(50) DEFAULT NULL COMMENT '结算单',
  `yellow_count` varchar(9) DEFAULT '0' COMMENT '黄灯子单数量',
  `red_count` varchar(9) DEFAULT '0' COMMENT '黄灯子单数量',
  `fmc_reject_reason` varchar(20) DEFAULT NULL COMMENT 'FMC驳回原因',
  PRIMARY KEY (`id`),
  KEY `idx_ticket_id` (`ticket_id`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='基础设施维保主流程工单'
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "120",
  "SchemaId": "maintenance_ticket_main_service",
  "ReturnTotalRows": 0,
  "BussType": "维护",
  "Data": {
    "ResultColumns": {
      "AdminComments": "",
      "AvgTimeConsuming": "",
      "CampusName": "",
      "ChangeId": "",
      "DeviceList": "",
      "DevicetypesName": "",
      "EndDate": "",
      "FmcReject": "",
      "FmcUser": "",
      "Id": "",
      "Item": "",
      "MOP": "",
      "MadminReview": "",
      "Maintainer": "",
      "MaintenanceCycle": "",
      "MaintenanceType": "",
      "MozuName": "",
      "OncePass": "",
      "Operator": "",
      "PMEngineer": "",
      "PMPhone": "",
      "PlanEndDate": "",
      "PlanStartDate": "",
      "ProcessTitle": "",
      "Professional": "",
      "ReCheckList": "",
      "ReasonForMadmin": "",
      "RegionName": "",
      "SOP": "",
      "StandardTime": "",
      "StatusLight": "",
      "SupplierTimes": "",
      "TicketId": "",
      "TicketStartDate": "",
      "TicketStatus": "",
      "ToChange": "",
      "ToMaintainer": "",
      "ToolsList": "",
      "UnqualifiedType": "",
      "UseMop": "",
      "WorkersCount": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
  "Return": 0,
  "Details": "",
  "ReqId": "fob-gnetopsd0twjuu6efe8",
  "Data": {
    "List": [
      {
        "AdminComments": "",
        "AvgTimeConsuming": 0,
        "CampusName": "重庆-泰和",
        "ChangeId": "",
        "DeviceList": "[\"【5#UPS】CQ-TH-BD2-PA144-UPS-05\", \"【6#UPS】CQ-TH-BD2-PA145-UPS-06\"]",
        "DevicetypesName": "动力UPS",
        "EndDate": "",
        "FmcReject": "",
        "FmcUser": "实施中",
        "Id": "016d838e-cfd2-11ee-8898-1eade401574b",
        "Item": "[]",
        "MOP": "",
        "MadminReview": "",
        "Maintainer": "昌泰云",
        "MaintenanceCycle": "月度",
        "MaintenanceType": "预防性维护",
        "MozuName": "重庆泰和模组01",
        "OncePass": "是",
        "Operator": "jywei",
        "PMEngineer": "v_isptan;v_ihsun;v_i",
        "PMPhone": "",
        "PlanEndDate": "2024-02-20",
        "PlanStartDate": "2024-02-20",
        "ProcessTitle": "重庆泰和模组01-动力UPS-预防性维护月度",
        "Professional": "配电",
        "ReCheckList": "{\"re_check1_1\": \"是\", \"re_check1_2\": \"是\", \"re_check1_3\": \"是\", \"re_check2_1\": \"是\", \"re_check2_2\": \"是\", \"re_check2_3\": \"是\", \"re_check2_4\": \"是\", \"re_check3_1\": \"是\"}",
        "ReasonForMadmin": "",
        "RegionName": "华南",
        "SOP": "",
        "StandardTime": 4,
        "StatusLight": "红",
        "SupplierTimes": 0,
        "TicketId": "20240220002605",
        "TicketStartDate": "2024-02-20",
        "TicketStatus": "实施中",
        "ToChange": "2",
        "ToMaintainer": "自维",
        "ToolsList": "null",
        "UnqualifiedType": "",
        "UseMop": "否",
        "WorkersCount": 0
      }
    ],
    "TotalRows": 325
  }
}
```

## 维护自建子表

**表结构：**

```sql
CREATE TABLE `dg_maintenance_ticket_sub` (
  `id` varchar(50) NOT NULL,
  `instance_id` varchar(50) DEFAULT NULL COMMENT '主流程单号',
  `ticket_id` varchar(50) DEFAULT NULL COMMENT '工单号',
  `task_id` varchar(100) DEFAULT NULL COMMENT '任务id',
  `main_ticket_id` varchar(50) DEFAULT NULL COMMENT '主流程工单号',
  `process_title` varchar(100) DEFAULT NULL COMMENT '标题',
  `campus_name` varchar(50) DEFAULT NULL COMMENT '园区名称',
  `mozu_name` varchar(50) DEFAULT NULL COMMENT '模组名称',
  `device_uid` varchar(50) DEFAULT NULL COMMENT '设备uid',
  `device_name` varchar(255) DEFAULT NULL COMMENT '设备名称',
  `device_number` varchar(255) DEFAULT NULL COMMENT '设备编号',
  `professional` varchar(9) DEFAULT NULL COMMENT '专业',
  `deal_user` varchar(2000) DEFAULT NULL COMMENT '负责人',
  `start_time` datetime DEFAULT NULL COMMENT '开始时间',
  `end_time` datetime DEFAULT NULL COMMENT '结束时间',
  `abnormal_check_count` int(9) DEFAULT NULL COMMENT '异常计数',
  `status_light` varchar(2) DEFAULT NULL COMMENT '状态灯',
  `maintain_summary` varchar(1000) DEFAULT NULL COMMENT '维护总结',
  `confirm` varchar(255) DEFAULT NULL COMMENT '确认结单状态',
  `standard_time` float DEFAULT NULL COMMENT '标准耗时',
  `time_consuming` int(9) DEFAULT NULL COMMENT '实际耗时',
  `devicetypes_name` varchar(100) DEFAULT NULL COMMENT '维护项目名称',
  `application_type_name` varchar(50) DEFAULT NULL COMMENT '应用类型',
  `ticket_status` varchar(10) DEFAULT NULL COMMENT '工单状态',
  `maintainer` varchar(50) DEFAULT NULL COMMENT '服务商',
  `audit_results` varchar(9) DEFAULT '合格' COMMENT '工单审核结果',
  `audit_reason` varchar(1000) DEFAULT NULL COMMENT '审核原因',
  `time_out` varchar(9) DEFAULT NULL COMMENT '是否超时：是/否',
  `check_in_time` datetime DEFAULT NULL COMMENT '打卡时间',
  `check_in_status` varchar(9) DEFAULT NULL COMMENT '打卡状态',
  `check_in_place` varchar(100) DEFAULT NULL COMMENT '打卡地点',
  `maintenance_type` varchar(50) DEFAULT NULL COMMENT '维护类型',
  `maintenance_cycle` varchar(100) DEFAULT NULL COMMENT '维护周期',
  `p_ticket_id` varchar(50) DEFAULT NULL COMMENT '问题跟进单号',
  `p_ins_id` varchar(50) DEFAULT NULL COMMENT '问题跟进流程id',
  `upload_start` json DEFAULT NULL COMMENT '上传图片开始',
  `upload_end` json DEFAULT NULL COMMENT '上传图片结束',
  `po_ticket_id` varchar(50) DEFAULT NULL COMMENT '结算单',
  `billing_count` varchar(9) DEFAULT NULL COMMENT '计费数量',
  `upload_photo_time` varchar(20) DEFAULT NULL COMMENT '上传照片时间',
  `face_photo` varchar(200) DEFAULT NULL COMMENT '人脸识别照片',
  `face_user` varchar(200) DEFAULT NULL COMMENT '人脸识别对象',
  `face_detect_result` varchar(200) DEFAULT NULL COMMENT '识别结果',
  `face_detail` varchar(200) DEFAULT NULL COMMENT '识别详情',
  `upload_face_photo_time` varchar(200) DEFAULT NULL COMMENT '上传人脸识别照片时间',
  `time_audit` varchar(200) DEFAULT NULL COMMENT '工时审核',
  `time_abnormal_reason` varchar(200) DEFAULT NULL COMMENT '工时异常原因',
  `scan_code_result` varchar(9) DEFAULT NULL COMMENT '二维码扫描结果',
  `qualification_detect_result` varchar(9) DEFAULT NULL COMMENT '资质识别结果',
  `overseer_check` varchar(9) DEFAULT NULL COMMENT '随工应急工程师审核',
  `overseer_reason` varchar(5000) DEFAULT NULL COMMENT '随工意见',
  `overseer_ticket_id` varchar(100) DEFAULT NULL COMMENT '随工单号',
  `overseer_instance_id` varchar(100) DEFAULT NULL COMMENT '随工单流程id',
  `face_is_wrong` varchar(9) DEFAULT NULL COMMENT '人脸识别二次检查',
  PRIMARY KEY (`id`) USING BTREE,
  KEY `idx_main_ticket_id` (`main_ticket_id`),
  KEY `idx_devicetypes_name` (`devicetypes_name`),
  KEY `idx_ticket_status` (`ticket_status`),
  KEY `idx_status_light` (`status_light`),
  KEY `idx_ticket_id_main_ticket_id_sub` (`ticket_id`,
  `main_ticket_id`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC COMMENT='柴发维保工单'
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "120",
  "SchemaId": "dg_maintenance_ticket_sub_service",
  "ReturnTotalRows": 1,
  "BussType": "维护",
  "Data": {
    "ResultColumns": {
      "AbnormalCheckCount": "",
      "ApplicationTypeName": "",
      "AuditReason": "",
      "AuditResults": "",
      "CampusName": "",
      "CheckInPlace": "",
      "CheckInStatus": "",
      "CheckInTime": "",
      "Confirm": "",
      "DealUser": "",
      "DeviceName": "",
      "DeviceNumber": "",
      "DeviceUid": "",
      "DevicetypesName": "",
      "EndTime": "",
      "Id": "",
      "InstanceId": "",
      "MainTicketId": "",
      "MaintainSummary": "",
      "Maintainer": "",
      "MaintenanceCycle": "",
      "MaintenanceType": "",
      "MozuName": "",
      "PInsId": "",
      "PTicketId": "",
      "ProcessTitle": "",
      "Professional": "",
      "StandardTime": "",
      "StartTime": "",
      "StatusLight": "",
      "TaskId": "",
      "TicketId": "",
      "TicketStatus": "",
      "TimeConsuming": "",
      "TimeOut": "",
      "UploadEnd": "",
      "UploadStart": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
  "data": {
    "List": [
      {
        "AbnormalCheckCount": 0,
        "ApplicationTypeName": "",
        "AuditReason": "",
        "AuditResults": "",
        "CampusName": "重庆-泰和",
        "CheckInPlace": "北碚区水土街道明家山",
        "CheckInStatus": "异常",
        "CheckInTime": "2023-11-01 14:57:35",
        "Confirm": "正常",
        "DealUser": "songpan;vjiahaoli",
        "DeviceName": "T10馈线柜2AH10",
        "DeviceNumber": "CQ-TH-BD3-PA107-HVOC-05",
        "DeviceUid": "3675360333033114115",
        "DevicetypesName": "高压馈线柜",
        "EndTime": "2023-11-02 14:56:30",
        "Id": "00028e18-7853-11ee-9303-525400f8d077",
        "InstanceId": "97180484-77ba-11ee-9303-525400f8d077",
        "MainTicketId": "20231031004416",
        "MaintainSummary": "本次维护未见异常",
        "Maintainer": "昌泰云",
        "MaintenanceCycle": "",
        "MaintenanceType": "",
        "MozuName": "重庆泰和模组04",
        "PInsId": "",
        "PTicketId": "",
        "ProcessTitle": "重庆泰和模组04-T10馈线柜2AH10维护工单",
        "Professional": "",
        "StandardTime": 4,
        "StartTime": "2023-11-01 14:57:43",
        "StatusLight": "绿",
        "TaskId": "0002b47c-7853-11ee-9303-525400f8d077",
        "TicketId": "20231101003079",
        "TicketStatus": "已完成",
        "TimeConsuming": 86335,
        "TimeOut": "实施中",
        "UploadEnd": "",
        "UploadStart": ""
      },
      {
        "AbnormalCheckCount": 0,
        "ApplicationTypeName": "IT电池柜",
        "AuditReason": "",
        "AuditResults": "",
        "CampusName": "重庆-泰和",
        "CheckInPlace": "北碚区水土街道明家山",
        "CheckInStatus": "正常",
        "CheckInTime": "2024-04-22 14:26:54",
        "Confirm": "正常",
        "DealUser": "Fo_郑仕勇",
        "DeviceName": "M3104-A-电池柜-02",
        "DeviceNumber": "CQ-TH-BD3-M3104-MDC-A-BPACK-02",
        "DeviceUid": "3459194705047199744",
        "DevicetypesName": "IT配套蓄电池",
        "EndTime": "2024-04-22 14:33:06",
        "Id": "00083a38-fe28-11ee-93f5-5254006cf489",
        "InstanceId": "51e6fb70-fdf9-11ee-93f5-5254006cf489",
        "MainTicketId": "20240419007917",
        "MaintainSummary": "未见异常",
        "Maintainer": "昌泰云",
        "MaintenanceCycle": "季度维护",
        "MaintenanceType": "预防性维护",
        "MozuName": "重庆泰和模组04",
        "PInsId": "",
        "PTicketId": "",
        "ProcessTitle": "重庆泰和模组04-M3104-A-电池柜-02维护工单",
        "Professional": "",
        "StandardTime": 4,
        "StartTime": "2024-04-22 14:27:24",
        "StatusLight": "绿",
        "TaskId": "00083a92-fe28-11ee-93f5-5254006cf489",
        "TicketId": "20240419010381",
        "TicketStatus": "已完成",
        "TimeConsuming": 372,
        "TimeOut": "",
        "UploadEnd": "",
        "UploadStart": ""
      }
    ],
    "TotalRows": 21860
  },
  "msg": "",
  "status": 0
}
```

## 整改措施反馈

**字段说明：**

```json
{
   "Action": "ServiceImprove",
   "Data": {
    "service_ticket_id": "[string]",
    "improve_ticket_id": "[string]" ,
    "service_type": "[string]",
     "isp_ticket_id": "[string]",
     "measure": "[string]",
     "reason": "[string]",
     "due_time": "[string]",
    "service_manager": "[string]",
    "business_manager": "[string]",
     "feedback_person": "[string]"
  },
   "Method": "IspQcFeedback",
   "SystemId": "[string]" 
}
```

**请求示例：**

```json
{
   "Action": "ServiceImprove",
  "Method": "IspQcFeedback",
  "SystemId": "121",
  "Data": {
     "service_ticket_id": "20230622001583",
    "improve_ticket_id": "20240709008140",
    "isp_ticket_id": "20230622001583",
    "reason": "something wrong",
    "measure": "do something",
    "due_time": "2024-01-09 11:50:59",
    "service_manager": "xxx",
    "business_manager": "xxx",
    "feedback_person": "Alice", "upload_data": [
       "https://test.otob.dcops.qq.com/relay/cosfile/relay/20241017/data_b6c60636f0c740b56653536eb5d12433.csv"
    ] 
  }
}
```

**返回示例：**

```json
{
  "data": null,
  "msg": "",
  "status": 0
}
```

## 服务商侧建单

**字段说明：**

```json
{
   "Action": "ServiceImprove",
   "Data": {
    "service_ticket_id": "[string]",
     "feedback_person": "[string]",
     "isp_ticket_id": "[string]",
     "measure": "[string]",
     "reason": "[string]",
     "due_time": "[string]",
    "service_manager": "[string]",
    "business_manager": "[string]"
  },
   "Method": "IspQcFeedback",
   "SystemId": "[string]" 
}
```

**请求示例：**

```json
{
  "Action": "ServiceImprove",
  "Method": "IspQcFeedback",
  "SystemId": "121",
  "Data": {
    "service_ticket_id": "20230622001583",
    "isp_ticket_id": "20230622001583",
    "reason": "something wrong",
    "measure": "do something",
    "due_time": "2024-01-09 11:50:59",
    "service_manager": "xxx",
    "business_manager": "xxx",
    "feedback_person": "Alice",
    "evaluation_detail": "good job"
  }
}
```

**返回示例：**

```json
{
  "data": {
    "TicketId": 20240710007251
  },
  "msg": "",
  "status": 0
}
```

## 整改落地详情反馈

**字段说明：**

```json
{
   "Action": "ServiceImprove",
   "Data": {
    "service_ticket_id": "[string]",
    "improve_ticket_id": "[string]" ,
    "implement_info": "[string]" 
  },
   "Method": "ImplementFeedback",
   "SystemId": "[string]" 
}
```

**请求示例：**

```json
{
  "Action": "ServiceImprove",
  "Method": "ImplementFeedback",
  "SystemId": "121",
  "Data": {
    "service_ticket_id": "20240318006255",
    "improve_ticket_id": "20240711004295",
    "implement_info": "ok",
    "feedback_upload_data": [
      "https://test.otob.dcops.qq.com/relay/cosfile/relay/20241017/data_b6c60636f0c740b56653536eb5d12433.csv"
    ]
  }
}
```

**返回示例：**

```json
{
  "data": null,
  "msg": "",
  "status": 0
}
```

## 在库服务人员信息查询

**表结构：**

```sql
CREATE TABLE `data_center_operator_info` (
  `id` int(9) NOT NULL AUTO_INCREMENT COMMENT '人员id自增',
  `user_real_name` varchar(255) DEFAULT NULL COMMENT '人员姓名',
  `gender` char(9) DEFAULT NULL COMMENT '性别',
  `photo` varchar(255) DEFAULT NULL COMMENT '照片cos地址',
  `cellphone_number` varchar(255) DEFAULT NULL COMMENT '手机号码',
  `user_account` varchar(255) DEFAULT NULL COMMENT '人员科技账号',
  `personnel_source` varchar(255) DEFAULT NULL COMMENT '人员来源：运营商/服务商',
  `business_type` varchar(255) DEFAULT NULL COMMENT '人员负责业务类型：巡检/变更/维护',
  `person_position` varchar(255) DEFAULT NULL COMMENT '人员岗位',
  `campus_name` varchar(255) DEFAULT NULL COMMENT '所属园区',
  `mozu_name` varchar(2000) DEFAULT NULL COMMENT '所属模组',
  `service_provider` varchar(255) DEFAULT NULL COMMENT '人员所属公司',
  `person_status` varchar(255) DEFAULT NULL COMMENT '在职/离职',
  `city` varchar(255) DEFAULT NULL COMMENT '人员工作城市',
  PRIMARY KEY (`id`)
) ENGINE = InnoDB AUTO_INCREMENT = 116 DEFAULT CHARSET = utf8
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "120",
  "SchemaId": "service_person",
  "ReturnTotalRows": 1,
  "Data": {
    "ResultColumns": {
      "BusinessType": "",
      "CampusName": "",
      "CellphoneNumber": "",
      "City": "",
      "Gender": "",
      "Id": "",
      "MozuName": "",
      "PersonPosition": "",
      "PersonStatus": "",
      "PersonnelSource": "",
      "Photo": "",
      "ServiceProvider": "",
      "UserAccount": "",
      "UserRealName": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
  "data": {
    "List": [
      {
        "BusinessType": "变更",
        "CampusName": "清远-清城",
        "CellphoneNumber": "13413420101",
        "City": "清远",
        "Gender": "男",
        "Id": 1,
        "MozuName": "清远清城模组62",
        "PersonPosition": "电气工程师",
        "PersonStatus": "离职",
        "PersonnelSource": "服务商",
        "Photo": "",
        "ServiceProvider": "昌泰云",
        "UserAccount": "v_xzxxian",
        "UserRealName": "冼振星"
      },
      {
        "BusinessType": "巡检",
        "CampusName": "清远-清新",
        "CellphoneNumber": "13413402025",
        "City": "清远",
        "Gender": "男",
        "Id": 2,
        "MozuName": "清远清新模组11",
        "PersonPosition": "巡检服务工程师",
        "PersonStatus": "在职",
        "PersonnelSource": "服务商",
        "Photo": "\"\"",
        "ServiceProvider": "昌泰云",
        "UserAccount": "v_xzxxian_1",
        "UserRealName": "张水昌"
      },
      {
        "BusinessType": "巡检",
        "CampusName": "清远-清新",
        "CellphoneNumber": "",
        "City": "",
        "Gender": "男",
        "Id": 4,
        "MozuName": "",
        "PersonPosition": "巡检服务工程师",
        "PersonStatus": "",
        "PersonnelSource": "服务商",
        "Photo": "13727101933",
        "ServiceProvider": "昌泰云",
        "UserAccount": "Xo_laijiefeng",
        "UserRealName": "赖杰锋"
      }
    ],
    "TotalRows": 15
  },
  "msg": "",
  "status": 0
}
```

## 租赁机房服务工单通用查询接口

**表结构：**

```sql
CREATE TABLE `service_common_ticket` (
  `id` int(9) NOT NULL AUTO_INCREMENT,
  `campus_name` varchar(64) DEFAULT NULL COMMENT '园区',
  `mozu_name` varchar(64) DEFAULT NULL COMMENT '模组',
  `creator` varchar(32) DEFAULT NULL COMMENT '创建人',
  `workorder_type` varchar(32) DEFAULT NULL COMMENT '工单服务类型',
  `task_type` varchar(32) DEFAULT NULL COMMENT '服务子项内容',
  `dcops_id` varchar(32) DEFAULT NULL COMMENT '服务工单对应的dcops单号',
  `upstream_id` varchar(32) DEFAULT NULL COMMENT '上游单号',
  `related_business_id` varchar(32) DEFAULT NULL COMMENT '关联业务工单号',
  `create_time` varchar(32) DEFAULT NULL COMMENT '服务工单创建时间',
  `executor` varchar(32) DEFAULT NULL COMMENT '现场执行工单人员oa信息',
  `execute_start_time` varchar(32) DEFAULT NULL COMMENT '执行开始时间',
  `execute_end_time` varchar(32) DEFAULT NULL COMMENT '执行结束时间',
  `fmc_auditor` varchar(32) DEFAULT NULL COMMENT 'FMC审核人',
  `fmc_audit_result` varchar(9) DEFAULT NULL COMMENT 'FMC审核结果',
  `fmc_audit_remark` varchar(2000) DEFAULT NULL COMMENT 'FMC审核备注',
  `idc_manager` varchar(32) DEFAULT NULL COMMENT '数经审核人',
  `idc_manager_audit_result` varchar(32) DEFAULT NULL COMMENT '数经审核结果',
  `idc_manager_audit_remark` varchar(2000) DEFAULT NULL COMMENT '数经审核备注',
  `workorder_state` varchar(32) DEFAULT NULL COMMENT '工单状态',
  `attachment` varchar(1000) DEFAULT NULL COMMENT '附件',
  `dispatch_dimension` varchar(32) DEFAULT NULL COMMENT '派单维度/园区/模组/一级机房',
  `bizArea_name` varchar(64) DEFAULT NULL COMMENT '一级机房名',
  `service_desc` varchar(2000) DEFAULT NULL COMMENT '服务说明',
  `deal_desc` varchar(2000) DEFAULT NULL COMMENT '处理说明',
  `plan_start` varchar(32) DEFAULT NULL COMMENT '计划开始时间',
  `plan_end` varchar(32) DEFAULT NULL COMMENT '计划结束时间',
  `is_billing` varchar(9) DEFAULT NULL COMMENT '是否计费',
  `service_provider` varchar(32) DEFAULT NULL COMMENT '服务提供商',
  PRIMARY KEY (`id`)
) ENGINE = InnoDB AUTO_INCREMENT = 47 DEFAULT CHARSET = utf8 COMMENT = '通用服务工单历史记录'
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "120",
  "SchemaId": "service_common_ticket",
  "ReturnTotalRows": 1,
  "Data": {
    "ResultColumns": {
      "Attachment": "",
      "BizAreaName": "",
      "CampusName": "",
      "CreateTime": "",
      "Creator": "",
      "DcopsId": "",
      "DealDesc": "",
      "DispatchDimension": "",
      "ExecuteEndTime": "",
      "ExecuteStartTime": "",
      "Executor": "",
      "FmcAuditRemark": "",
      "FmcAuditResult": "",
      "FmcAuditor": "",
      "Id": "",
      "IdcManager": "",
      "IdcManagerAuditRemark": "",
      "IdcManagerAuditResult": "",
      "IsBilling": "",
      "MozuName": "",
      "PlanEnd": "",
      "PlanStart": "",
      "RelatedBusinessId": "",
      "ServiceDesc": "",
      "ServiceProvider": "",
      "TaskType": "",
      "UpstreamId": "",
      "WorkorderState": "",
      "WorkorderType": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
  "data": {
    "List": [
      {
        "Attachment": "[{\"name\":\"柴发启动电池应用类型设备新增_cfd616515ad0c0629c0ec194fe18fec8_cfd616515ad0c0629c0ec194fe18fec8.xlsx\",\"response\":{\"FileList\":[{\"name\":\"柴发启动电池应用类型设备新增_cfd616515ad0c0629c0ec194fe18fec8_cfd616515ad0c0629c0ec194fe18fec8.xlsx\",\"status\":0,\"url\":\"https://dcops.test.tencent.com/relay/cosfile/relay/20241203/柴发启动电池应用类型设备新增_cfd616515ad0c0629c0ec194fe18fec8_cfd616515ad0c0629c0ec194fe18fec8_cfd616515ad0c0629c0ec194fe18fec8.xlsx\"}]}}]",
        "BizAreaName": "",
        "CampusName": "重庆-泰和",
        "CreateTime": "2024-12-03 13:01:08",
        "Creator": "pengleifu",
        "DcopsId": "10241203002240",
        "DealDesc": "ces ",
        "DispatchDimension": "园区",
        "ExecuteEndTime": "",
        "ExecuteStartTime": "",
        "Executor": "",
        "FmcAuditRemark": "",
        "FmcAuditResult": "",
        "FmcAuditor": "",
        "Id": 28,
        "IdcManager": "",
        "IdcManagerAuditRemark": "",
        "IdcManagerAuditResult": "",
        "IsBilling": "",
        "MozuName": "",
        "PlanEnd": "2024-12-05",
        "PlanStart": "2024-12-04",
        "RelatedBusinessId": "",
        "ServiceDesc": "ces",
        "ServiceProvider": "昌泰云",
        "TaskType": "高危巡检",
        "UpstreamId": "",
        "WorkorderState": "进行中",
        "WorkorderType": "通用巡检"
      }
    ],
    "TotalRows": 1
  },
  "msg": "",
  "status": 0
}
```

## 维修主表

**表结构：**

```sql
CREATE TABLE `maintenance_work_order` (
  `id` varchar(100) DEFAULT '' COMMENT '自定义单号',
  `maintenance_id` varchar(100) NOT NULL COMMENT '维修dcops单号',
  `event_id` varchar(100) DEFAULT '无' COMMENT '事件dcops单号',
  `maintenance_status` varchar(100) DEFAULT '' COMMENT '维修状态',
  `park_name` varchar(100) DEFAULT '' COMMENT '园区名称',
  `mozu_name` varchar(100) DEFAULT '' COMMENT '模组名称',
  `maintenance_level` varchar(100) DEFAULT '' COMMENT '维修等级',
  `maintenance_source` varchar(100) DEFAULT '' COMMENT '维修单来源',
  `created_time` varchar(100) DEFAULT '' COMMENT '创建时间',
  `created_by` varchar(100) DEFAULT '' COMMENT '创建人',
  `maintenance_title` varchar(1000) DEFAULT '' COMMENT '维修标题',
  `maintenance_program` varchar(100) DEFAULT '' COMMENT '维修方案',
  `solution_created_time` varchar(100) DEFAULT '' COMMENT '方案制定时间',
  `maintenance_engineer` varchar(100) DEFAULT '' COMMENT '维修工程师',
  `facilities_professional` varchar(100) DEFAULT '' COMMENT '所属专业',
  `maintenance_end_time` varchar(100) DEFAULT '' COMMENT '维修结单时间',
  `another_event_order_number` varchar(100) DEFAULT '无' COMMENT '自定义的事件单号',
  `maintenance_number` varchar(1000) DEFAULT '无' COMMENT '维修流程id',
  `repair_description` varchar(1000) DEFAULT NULL COMMENT '维修描述',
  `po_instance_id` varchar(1000) DEFAULT NULL COMMENT '关联po单流程id',
  `po_number` varchar(100) DEFAULT NULL COMMENT 'po单号',
  `maintenance_subtitle` varchar(1000) DEFAULT NULL COMMENT '维修副标题',
  `change_code` varchar(100) DEFAULT NULL COMMENT '关联变更单号',
  `diagnosis_scene_name` varchar(1000) DEFAULT NULL COMMENT '维修诊断场景',
  `diagnosis_device_number` varchar(1000) DEFAULT NULL COMMENT '维修诊断设备编号',
  `qc_number` varchar(255) DEFAULT '无' COMMENT 'qc单号',
  `is_qc` varchar(255) DEFAULT '否' COMMENT '是否需要qc',
  `is_overtime` varchar(255) DEFAULT '否' COMMENT '是否qc超时',
  `proof_status` varchar(255) DEFAULT '无举证' COMMENT '举证状态',
  `recheck_status` varchar(255) DEFAULT '未检查' COMMENT '复检状态',
  PRIMARY KEY (`maintenance_id`),
  KEY `事件单号` (`event_id`) USING BTREE,
  KEY `模组名称` (`mozu_name`) USING BTREE,
  KEY `自定义id` (`id`) USING BTREE,
  KEY `idx_maintenance_program` (`maintenance_program`)
) ENGINE = InnoDB DEFAULT CHARSET = utf8
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "120",
  "SchemaId": "maintenance_work_order_read",
  "ReturnTotalRows": 1,
  "BussType": "维修",
  "Data": {
    "ResultColumns": {
      "AnotherEventOrderNumber": "",
      "CreatedBy": "",
      "CreatedTime": "",
      "EventId": "",
      "FacilitiesProfessional": "",
      "Id": "",
      "LogList": "",
      "MaintenanceEndTime": "",
      "MaintenanceEngineer": "",
      "MaintenanceId": "",
      "MaintenanceLevel": "",
      "MaintenanceNumber": "",
      "MaintenanceProgram": "",
      "MaintenanceSource": "",
      "MaintenanceStatus": "",
      "MaintenanceSubtitle": "",
      "MaintenanceTitle": "",
      "MozuName": "",
      "ParkName": "",
      "PoInstanceId": "",
      "PoNumber": "",
      "ProcessingTime": "",
      "RepairDescription": "",
      "SolutionCreatedTime": "",
      "DiagnosisDeviceNumber": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 3,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
  "data": {
    "List": [
      {
        "AnotherEventOrderNumber": "无",
        "CreatedBy": "v_iywjyang",
        "CreatedTime": "2024-08-29 15:47:30",
        "DiagnosisDeviceNumber": "",
        "EventId": "无",
        "FacilitiesProfessional": "消防",
        "Id": "F202408290007",
        "LogList": "维修跟进单确认-专业工程师/维修打卡-厂商",
        "MaintenanceEndTime": "2024-08-29 16:00:40",
        "MaintenanceEngineer": "昌泰云",
        "MaintenanceId": "20240829225463",
        "MaintenanceLevel": "L4",
        "MaintenanceNumber": "f191bab5-65da-11ef-9991-1a8b8a88a95f",
        "MaintenanceProgram": "厂商维修",
        "MaintenanceSource": "FMC维修",
        "MaintenanceStatus": "已结单",
        "MaintenanceSubtitle": "重庆泰和模组01-测试维修原因参数",
        "MaintenanceTitle": "重庆泰和模组01-测试维修原因参数",
        "MozuName": "重庆泰和模组01",
        "ParkName": "重庆-泰和",
        "PoInstanceId": "f5cc7dfb-65da-11ef-9991-1a8b8a88a95f",
        "PoNumber": "20240829225465",
        "ProcessingTime": {
          "state": "已超时",
          "time": "32天 1小时 32分钟"
        },
        "RepairDescription": "测试维修原因参数",
        "SolutionCreatedTime": "2024-08-29 15:47:36"
      },
      {
        "AnotherEventOrderNumber": "无",
        "CreatedBy": "v_iywjyang",
        "CreatedTime": "2024-08-15 14:34:47",
        "DiagnosisDeviceNumber": "",
        "EventId": "无",
        "FacilitiesProfessional": "弱电",
        "Id": "F202408150005",
        "LogList": "维修跟进单确认-专业工程师/维修打卡-厂商",
        "MaintenanceEndTime": "2024-08-15 15:18:21",
        "MaintenanceEngineer": "昌泰云",
        "MaintenanceId": "20240815011129",
        "MaintenanceLevel": "L4",
        "MaintenanceNumber": "7758a453-5ad0-11ef-8ce1-3a266ed0cf29",
        "MaintenanceProgram": "厂商维修",
        "MaintenanceSource": "FMC维修",
        "MaintenanceStatus": "已结单",
        "MaintenanceSubtitle": "重庆泰和模组01-test33",
        "MaintenanceTitle": "重庆泰和模组01-test33",
        "MozuName": "重庆泰和模组01",
        "ParkName": "重庆-泰和",
        "PoInstanceId": "7b962787-5ad0-11ef-9412-9aa57c5594fc",
        "PoNumber": "20240815011130",
        "ProcessingTime": {
          "state": "已超时",
          "time": "46天 2小时 45分钟"
        },
        "RepairDescription": "test33",
        "SolutionCreatedTime": "2024-08-15 14:34:54"
      },
      {
        "AnotherEventOrderNumber": "无",
        "CreatedBy": "v_iywjyang",
        "CreatedTime": "2024-08-15 14:32:34",
        "DiagnosisDeviceNumber": "",
        "EventId": "无",
        "FacilitiesProfessional": "弱电",
        "Id": "F202408150003",
        "LogList": "维修跟进单确认-专业工程师/维修打卡-厂商",
        "MaintenanceEndTime": "2024-08-15 15:12:17",
        "MaintenanceEngineer": "昌泰云",
        "MaintenanceId": "20240815011108",
        "MaintenanceLevel": "L3",
        "MaintenanceNumber": "283a20ba-5ad0-11ef-8ce1-3a266ed0cf29",
        "MaintenanceProgram": "厂商维修",
        "MaintenanceSource": "FMC维修",
        "MaintenanceStatus": "已结单",
        "MaintenanceSubtitle": "重庆泰和模组01-test11",
        "MaintenanceTitle": "重庆泰和模组01-test11",
        "MozuName": "重庆泰和模组01",
        "ParkName": "重庆-泰和",
        "PoInstanceId": "2ca76554-5ad0-11ef-8ce1-3a266ed0cf29",
        "PoNumber": "20240815011109",
        "ProcessingTime": {
          "state": "已超时",
          "time": "46天 2小时 47分钟"
        },
        "RepairDescription": "test11",
        "SolutionCreatedTime": "2024-08-15 14:32:41"
      }
    ],
    "TotalRows": 18
  },
  "msg": "",
  "status": 0
}
```

## 【新】维修主表

**表结构：**

```sql
CREATE TABLE `repair_orders` (
  `id` int(9) NOT NULL AUTO_INCREMENT,
  `campus_name` varchar(64) NOT NULL COMMENT '园区',
  `mozu_name` varchar(64) NOT NULL COMMENT '模组',
  `upstream_business` varchar(64) NOT NULL COMMENT '维修来源',
  `upstream_id` varchar(64) DEFAULT NULL COMMENT '上游单号',
  `ticket_id` varchar(64) NOT NULL COMMENT '维修单号',
  `facilities_professional` varchar(32) DEFAULT NULL COMMENT '专业',
  `problem_desc` varchar(2000) DEFAULT NULL COMMENT '故障现象描述',
  `creator` varchar(256) DEFAULT NULL COMMENT '创建人',
  `created_time` varchar(64) NOT NULL COMMENT '创建时间',
  `repair_level` varchar(32) NOT NULL COMMENT '维修等级',
  `repair_business` varchar(32) DEFAULT NULL COMMENT '维修厂商',
  `repair_engineer` varchar(64) DEFAULT NULL COMMENT '维修工程师',
  `repair_status` varchar(64) DEFAULT NULL COMMENT '工单状态',
  `failure_reason` varchar(1000) DEFAULT NULL COMMENT '故障原因/维修原因',
  `end_time` varchar(64) DEFAULT NULL COMMENT '结单时间',
  `po_id` varchar(64) DEFAULT NULL COMMENT '关联po单号',
  `change_id` varchar(64) DEFAULT NULL COMMENT '关联变更单号',
  `ticket_title` varchar(2000) DEFAULT NULL COMMENT '工单标题',
  `is_append` varchar(9) DEFAULT '否' COMMENT '是否补单',
  `append_reason` varchar(2000) DEFAULT NULL COMMENT '补单原因',
  `repair_score` varchar(32) DEFAULT NULL COMMENT '维修评分',
  `is_deal_timely` varchar(9) DEFAULT NULL COMMENT '是否处理及时',
  `overtime_type` varchar(500) DEFAULT NULL COMMENT '超时举证分类',
  `overtime_proof_desc` varchar(2000) DEFAULT NULL COMMENT '举证描述',
  `qc_result` char(9) DEFAULT NULL COMMENT '合格/不合格',
  `is_response_timely` char(9) DEFAULT NULL COMMENT '是否响应及时',
  `is_one_time_repair_passed` char(9) DEFAULT NULL COMMENT '是否一次性维修通过',
  PRIMARY KEY (`id`),
  KEY `idx_ticket_id` (`ticket_id`) 
) ENGINE=InnoDB AUTO_INCREMENT=49384 DEFAULT CHARSET=utf8 COMMENT='维修单信息表'
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "1",
  "SchemaId": "repair_orders",
  "BussType": "维修",
  "ReturnTotalRows": 0,
  "Data": {
    "ResultColumns": {
      "AppendReason": "",
      "CampusName": "",
      "ChangeId": "",
      "CreatedTime": "",
      "Creator": "",
      "EndTime": "",
      "EventId": "",
      "FacilitiesProfessional": "",
      "FailureReason": "",
      "Id": "",
      "IsAppend": "",
      "IsDealTimely": "",
      "IsOneTimeRepairPassed": "",
      "IsResponseTimely": "",
      "MozuName": "",
      "OvertimeProofDesc": "",
      "OvertimeType": "",
      "PoId": "",
      "ProblemDesc": "",
      "QcResult": "",
      "RepairBusiness": "",
      "RepairEngineer": "",
      "RepairLevel": "",
      "RepairScore": "",
      "RepairStatus": "",
      "TaskName": "",
      "TicketId": "",
      "TicketTitle": "",
      "UpstreamBusiness": "",
      "UpstreamId": ""
    },
    "SearchCondition": {
      "TicketId": "10250918001889"
    },
    "Sorts": [
      {
        "Column": "Id",
        "SortType": "desc"
      }
    ],
    "Limit": {
      "Size": 2,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
  "Data": {
    "List": [
      {
        "AppendReason": "",
        "CampusName": "重庆-泰和",
        "ChangeId": "",
        "CreatedTime": "2025-09-18 09:11:04",
        "Creator": "v_iywjyang",
        "EndTime": "2025-09-18 09:24:19",
        "EventId": "",
        "FacilitiesProfessional": "配电",
        "FailureReason": "ces",
        "Id": 100241,
        "IsAppend": "否",
        "IsDealTimely": "是",
        "IsOneTimeRepairPassed": "否",
        "IsResponseTimely": "是",
        "MozuName": "重庆泰和模组01",
        "OvertimeProofDesc": "",
        "OvertimeType": "",
        "PoId": "10250918001893",
        "ProblemDesc": "压缩机故障/通讯异常",
        "QcResult": "不合格",
        "RepairBusiness": "昌泰云",
        "RepairEngineer": "v_iywjyang",
        "RepairLevel": "普通",
        "RepairScore": "100",
        "RepairStatus": "维修完成",
        "TaskName": "",
        "TicketId": "10250918001889",
        "TicketTitle": "重庆泰和模组01-ces",
        "UpstreamBusiness": "现场反馈",
        "UpstreamId": ""
      }
    ],
    "TotalRows": 0
  },
  "Details": "",
  "Return": 0
}
```

## 维修-根据维修id查询事件处理流程各节点的操作日志记录

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "120",
  "SchemaId": "event_operation_log",
  "ReturnTotalRows": 1,
  "BussType": "维修",
  "Data": {
    "ResultColumns": {
      "Id": "",
      "NodeName": "",
      "UpdatedBy": "",
      "UpdatedTime": "",
      "DealDetail": "",
      "MaintenanceId": ""
    },
    "SearchCondition": {
      "MaintenanceId": "20240815011129"
    }
  }
}
```

**返回示例：**

```json
{
  "data": {
    "List": [
      {
        "DealDetail": "{\"维修设备信息确认\": [{\"alarm_unit\": \"CQ-TH-BD2-GA123-LCP-04\", \"device_Name\": \"4#柴发并机控制柜\", \"device_model\": \"EASYGEN-3200\", \"devicetypes_name\": \"控制柜（箱）\", \"mozu_name\": \"重庆泰和模组01\", \"park_name\": \"重庆-泰和\", \"room_name\": \"重庆泰和2号楼GA123\"}]}",
        "Id": 202556,
        "MaintenanceId": "20240815011129",
        "NodeName": "维修设备信息确认-维修商",
        "UpdatedBy": "v_blastliu",
        "UpdatedTime": "2024-08-15 15:17:35"
      }
    ],
    "TotalRows": 1
  },
  "msg": "",
  "status": 0
}
```

## 【新】维修-根据维修id查询事件处理流程各节点的操作日志记录

**表结构：**

```sql
CREATE TABLE `repair_orders_ope_logs` (
  `id` int(9) NOT NULL AUTO_INCREMENT,
  `ticket_id` varchar(32) NOT NULL COMMENT '维修单号',
  `node_name` varchar(128) NOT NULL COMMENT '流程中节点名称',
  `deal_user` varchar(64) DEFAULT NULL COMMENT '处理人',
  `start_time` varchar(32) DEFAULT NULL COMMENT '节点开始时间',
  `end_time` varchar(32) DEFAULT NULL COMMENT '节点结束时间',
  `deal_detail` longtext COMMENT '节点处理详情',
  `deal_user_role` varchar(64) DEFAULT NULL COMMENT '处理人角色',
  `task_id` varchar(32) DEFAULT NULL COMMENT '节点任务id',
  PRIMARY KEY (`id`)
) ENGINE = InnoDB AUTO_INCREMENT = 4328 DEFAULT CHARSET = utf8 COMMENT = '维修工单操作日志'
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "1",
  "SchemaId": "repair_orders_ope_logs",
  "ReturnTotalRows": 1,
  "BussType": "维修",
  "Data": {
    "ResultColumns": {
      "DealDetail": "",
      "DealUser": "",
      "DealUserRole": "",
      "EndTime": "",
      "Id": "",
      "NodeName": "",
      "StartTime": "",
      "TaskId": "",
      "TicketId": ""
    },
    "SearchCondition": {
      "TicketId": "10250819026813"
    },
    "Sorts": [
      {
        "Column": "Id",
        "SortType": "desc"
      }
    ],
    "Limit": {
      "Size": 2,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
   "Data": {
     "List": [
       {
         "DealDetail": "{\"是否需要厂家维修\": \"是\", \"诊断意见\": null, \"现场照片\": null}",
        "DealUser": "v_iywjyang",
        "DealUserRole": "值班工程师",
        "EndTime": "2025-08-19 18:46:38",
        "Id": 4317,
        "NodeName": "值班工程师-故障诊断",
        "StartTime": "2025-08-19 18:46:23",
        "TaskId": "6700000070345",
        "TicketId": "10250819026813" 
      },
      {
         "DealDetail": "{\"问题描述\": \"ces\", \"FMC审核结果\": \"通过\", \"FMC审核备注\": \"1\", \"business_pm\": \"Fo_杨波\"}",
        "DealUser": "v_iywjyang",
        "DealUserRole": "FMC",
        "EndTime": "2025-08-19 18:46:46",
        "Id": 4318,
        "NodeName": "FMC-确认维修",
        "StartTime": "2025-08-19 18:46:38",
        "TaskId": "6700000070347",
        "TicketId": "10250819026813" 
      } 
    ],
    "TotalRows": 0 
  },
  "Details": "",
  "Return": 0
}
```

## 演练-获取标准场景下拉列表

**请求示例：**

```json
{
   "Action": "DrillAddOrEditPlan",
  "Data": {
     "professional": "[string]" ,
    "Method": "GetSceneLevelList",
    "SystemId": "[string]"
}
}
```

**真实请求示例：**

```json
{
  "Action": "DrillAddOrEditPlan",
  "Method": "GetSceneLevelList",
  "SystemId": "2",
  "Data": {
    "professional": "ALL"
  }
}
```

**成功返回示例：**

```json
{
  "data": [
    {
      "professional": "公共系统",
      "scene_level_1": "其它",
      "scene_level_2": "其它"
    },
    {
      "professional": "公共系统",
      "scene_level_1": "非法入侵",
      "scene_level_2": "园区非法入侵"
    }
  ],
  "msg": "",
  "status": 0
}
```

## 演练-增修定制化属地化演练方案

**请求示例：**

```json
{
   "Action": "DrillAddOrEditPlan",
  "Data": {
     "FacilitatorName": "[string]",
    "FacilitatorTicketId": "[string]",
    "campus": "[string]",
    "drill_major": "[string]",
    "eop_url": [
       "[mixed]"
    ],
    "exercise_plan": "[string]",
    "gds_id": "[string]",
    "mozu": "[string]",
    "plan_id": "[string]",
    "pm": "[string]",
    "region": "[string]",
    "scene_level_1": "[string]",
    "scene_level_2": "[string]",
    "step1_notify": "[string]",
    "step1_table": [{
       "check_item": "[string]",
      "check_point": "[string]",
      "score": "[mixed]",
      "step_main": "[string]",
      "step_sn": "[mixed]" 
    }],
    "step2_notify": "[string]",
    "step2_table": [{
       "check_item": "[string]",
      "check_point": "[string]",
      "score": "[mixed]",
      "step_main": "[string]",
      "step_sn": "[mixed]" 
    }] 
  },
  "Method": "CreateTicket",
  "SystemId": "[string]"
}
```

**重要字段说明：**

gds_id：必须保证值是唯一的字符串，由请求方生成

**真实请求示例：**

```json
{
  "Action": "DrillAddOrEditPlan",
  "Method": "CreateTicket",
  "SystemId": 2,
  "Data": {
    "FacilitatorName": "万国",
    "FacilitatorTicketId": "GDS-DrillPlan-20241231103500",
    "campus": "清远-清新",
    "drill_major": "配电系统",
    "eop_url": [
      {
        "response": {
          "FileList": [
            {
              "url": "https://test.otob.dcops.qq.com/relay/cosfile/relay/20250305/IT机房(水消)区域火灾_1af8853bab7a8eac40a816d64b6cee6c.xlsx",
              "name": "IT机房(水消)区域火灾.xlsx"
            }
          ]
        }
      }
    ],
    "exercise_plan": "蓄电池严重故障",
    "region": "华南",
    "gds_id": "45365476878",
    "mozu": "清远清新模组11",
    "plan_id": "VEmHMhtSmcjJ8eqpxbpNia",
    "scene_level_1": "蓄电池严重故障",
    "scene_level_2": "蓄电池发热漏液等异常",
    "step1_notify": "故障定位描述",
    "step1_table": [
      {
        "check_item": "检查项",
        "check_point": "检查点",
        "score": 12,
        "step_main": "故障定位",
        "step_sn": 101
      }
    ],
    "step2_notify": "故障处理描述",
    "step2_table": [
      {
        "check_item": "处理项",
        "check_point": "检查点",
        "score": 12,
        "step_main": "故障处理",
        "step_sn": 201
      }
    ]
  }
}
```

**成功返回示例：**

```json
{
  "data": {
    "Data": {
      "InstanceId": "3200000157971",
      "TicketId": 10250618005809 ,
      "Details": "",
      "Return": 0
    },
    "msg": "",
    "status": 0
  }
}
```

## 演练-演练方案查询

**真实请求示例：**

```json
{
   "Action": "QueryData",
  "Method": "run",
  "SystemId": "1",
  "SchemaId": "drill_cd_sub_plan",
  "ReturnTotalRows": 1,
  "Data": {
     "ResultColumns": {
       "CampusName": "",
      "CreateDate": "",
      "DrillMajor": "",
      "EopUrl": "",
      "ExercisePlan": "",
      "Id": "",
      "LastEditTime": "",
      "MozuName": "",
      "RegionName": "",
      "SceneLevel1": "",
      "SceneLevel2": "",
      "Status": "",
      "Step1Notify": "",
      "Step2Notify": "" 
    },
    "SearchCondition": {
       "MozuName": [
        "清远清新模组11"
      ]
    },
    "Sorts": [],
    "Limit": {
       "Size": 20,
      "Start": 0
    }
  }
}
```

**成功返回示例：**

```json
{
   "Data": {
     "List": [
       {
         "CampusName": "清远-清新",
        "CreateDate": "2023-09-15",
        "DrillMajor": "配电系统",
        "EopUrl": "",
        "ExercisePlan": "PDU故障导致机架掉电",
        "Id": "2cHFMjnvR2pQSNVBhpPocB",
        "LastEditTime": "2024-01-02 16:00:09",
        "MozuName": "清远清新模组11",
        "RegionName": "华南",
        "SceneLevel1": "",
        "SceneLevel2": "",
        "Status": "正常",
        "Step1Notify": "xx开关断开",
        "Step2Notify": "PDU故障" 
      }
    ],
    "TotalRows": 14 
  },
  "Details": "",
  "Return": 0
}
```

## 演练-演练方案步骤查询

**真实请求示例：**

```json
{
   "Action": "QueryData",
  "Method": "run",
  "SystemId": "1",
  "SchemaId": "drill_cd_sub_plan_step",
  "ReturnTotalRows": 1,
  "Data": {
     "ResultColumns": {
       "CheckItem": "",
      "CheckPoint": "",
      "Id": "",
      "PlanId": "",
      "Score": "",
      "StepMain": "",
      "StepSn": "" 
    },
    "SearchCondition": {
       "PlanId": "2cHFMjnvR2pQSNVBhpPocB" ,
      "Sorts": [
      ],
      "Limit": {
         "Size": 20,
        "Start": 0
}
}
}
}
```

**成功返回示例：**

```json
{
  "Data": {
    "List": [
      {
        "CheckItem": "现场确认告警状态,通报现场情况",
        "CheckPoint": "跑位准确",
        "Id": 129,
        "PlanId": "2cHFMjnvR2pQSNVBhpPocB",
        "Score": 100,
        "StepMain": "故障定位",
        "StepSn": 101
      },
      {
        "CheckItem": "更换PDU",
        "CheckPoint": "aa",
        "Id": 130,
        "PlanId": "2cHFMjnvR2pQSNVBhpPocB",
        "Score": 80,
        "StepMain": "故障处理",
        "StepSn": 201
      },
      {
        "CheckItem": "ffff",
        "CheckPoint": "aa",
        "Id": 131,
        "PlanId": "2cHFMjnvR2pQSNVBhpPocB",
        "Score": 20,
        "StepMain": "故障处理",
        "StepSn": 202
      }
    ],
    "TotalRows": 3
  },
  "Details": "",
  "Return": 0
}
```

## 演练-更新演练计划

**请求示例：**

```json
{
   "Action": "DrillCreatePlan",
  "Data": {
     "plan": {
       "drill_date": "[string]",
      "gds_id": "[string]",
      "mozu_name": "[string]",
      "plan_id": "[string]",
      "plan_month": "[string]",
      "status": "[string]"
    },
    "req_id": "[string]" ,
    "Method": "UpdatePlanTicket",
    "SystemId": "[string]"
}
}
```

**重要字段说明：**

gds_id：必须保证值是唯一的字符串，由请求方生成

**真实请求示例：**

```json
{
  "Action": "DrillCreatePlan",
  "Method": "UpdatePlanTicket",
  "SystemId": "2",
  "data": {
    "req_id": "3830aeff-45f2-44a3-8d9c-cced989c8728",
    "plan": {
      "drill_date": "2024-12-24",
      "gds_id": "1111111111111",
      "mozu_name": "上海富特模组01",
      "plan_id": "20241226002",
      "plan_month": "2024-10",
      "status": "已取消"
    }
  }
}
```

**添加成功返回示例：**

```json
{
  "data": {
    "TicketId": 20241128002919,
    "calendars": [
      {
        "campus_name": "深圳-光明",
        "dcfm": null,
        "dcm_confirm": "同意",
        "dcm_ticket_id": null,
        "drill_date": "2024-12-29",
        "drill_major": "配电系统",
        "drill_type": "定制化",
        "eg_confirm": "同意",
        "eg_ticket_id": null,
        "exercise_plan": "单路市电中断",
        "fmc_ticket_id": null,
        "id": "gfhbtrtrng",
        "mozu_name": "深圳光明模组01",
        "plan_id": "2QcHVrGwzpri9f8RtZZZdF",
        "plan_month": "2024-12",
        "plan_status": "等待派单",
        "plan_ticket_id": 20241128002919,
        "region_name": "华南",
        "scene_level_1": null,
        "todo_ticket_id": null
      }
    ]
  },
  "msg": "",
  "status": 0
}
```

**计划重复起单返回示例：**

```json
{
  "data": "gfhbtrtrng gds_id计划ID已经存在，添加计划失败",
  "msg": "",
  "status": -1
}
```

**接口抖动多次请求返回示例：**

```json
{
  "data": "fdsfdgfdhgfdhgfhfg req_id已经请求过，添加计划失败",
  "msg": "",
  "status": -1
}
```

## 演练-添加演练计划

**请求示例：**

```json
{
   "Action": "DrillCreatePlan",
  "Data": {
     "FacilitatorName": "万国",
    "FacilitatorTicketId": "5458769760998",
    "plans": [{
       "drill_date": "[string]",
      "gds_id": "[string]",
      "mozu_name": "[string]",
      "plan_id": "[string]",
      "plan_month": "[string]" 
    }],
    "req_id": "[string]" ,
    "Method": "CreatePlanTicket",
    "SystemId": "[string]"
}
}
```

**重要字段说明：**

gds_id：必须保证值是唯一的字符串，由请求方生成，返回数的calendars.id就是这个值（值不允许出现“_”）

req_id：必须保证值是唯一的字符串，由请求方生成，用于请求失败后，再次提交上次的req_id

**真实请求示例：**

```json
{
  "Action": "DrillCreatePlan",
  "Method": "CreatePlanTicket",
  "SystemId": "2",
  "data": {
    "FacilitatorName": "万国",
    "FacilitatorTicketId": "GDS-DrillPlan-20241231103500",
    "req_id": "3830aeff-45f2-44a3-8d9c-cced989c8728",
    "plans": [
      {
        "drill_date": "2025-12-21",
        "gds_id": "6512",
        "mozu_name": "上海富特模组01",
        "plan_id": "2",
        "plan_month": "2025-12"
      }
    ],
    "facilitatorName": "万国",
    "facilitatorTicketId": "GDS-DrillPlan-20241231103500"
  },
  "action": "DrillCreatePlan",
  "method": "CreatePlanTicket"
}
```

**添加成功返回示例：**

```json
{
  "data": {
    "TicketId": 20241128002919,
    "calendars": [
      {
        "campus_name": "深圳-光明",
        "dcfm": null,
        "dcm_confirm": "同意",
        "dcm_ticket_id": null,
        "drill_date": "2024-12-29",
        "drill_major": "配电系统",
        "drill_type": "定制化",
        "eg_confirm": "同意",
        "eg_ticket_id": null,
        "exercise_plan": "单路市电中断",
        "fmc_ticket_id": null,
        "id": "gfhbtrtrng",
        "mozu_name": "深圳光明模组01",
        "plan_id": "2QcHVrGwzpri9f8RtZZZdF",
        "plan_month": "2024-12",
        "plan_status": "等待派单",
        "plan_ticket_id": 20241128002919,
        "region_name": "华南",
        "scene_level_1": null,
        "todo_ticket_id": null
      }
    ]
  },
  "msg": "",
  "status": 0
}
```

**计划重复起单返回示例：**

```json
{
  "data": "gfhbtrtrng gds_id计划ID已经存在，添加计划失败",
  "msg": "",
  "status": -1
}
```

**接口抖动多次请求返回示例：**

```json
{
  "data": "fdsfdgfdhgfdhgfhfg req_id已经请求过，添加计划失败",
  "msg": "",
  "status": -1
}
```

## 演练-演练计划查询

**真实请求示例：**

```json
{
   "Action": "QueryData",
  "Method": "run",
  "SystemId": "1",
  "SchemaId": "drill_standard_calendar",
  "ReturnTotalRows": 1,
  "Data": {
     "ResultColumns": {
       "CampusName": "",
      "Dcfm": "",
      "DcmConfirm": "",
      "DcmTicketId": "",
      "DrillDate": "",
      "DrillMajor": "",
      "DrillType": "",
      "EgConfirm": "",
      "EgTicketId": "",
      "ExercisePlan": "",
      "FmcTicketId": "",
      "Id": "",
      "MozuName": "",
      "PlanId": "",
      "PlanMonth": "",
      "PlanStatus": "",
      "PlanTicketId": "",
      "RegionName": "",
      "SceneLevel1": "",
      "TodoTicketId": "" 
    },
    "SearchCondition": {
       "MozuName": [
        "清远清新模组11"
      ]
    },
    "Sorts": [],
    "Limit": {
       "Size": 20,
      "Start": 0
    }
  }
}
```

**成功返回示例：**

```json
{
   "Data": {
     "List": [
       {
         "CampusName": "清远-清新",
        "Dcfm": "chadxie",
        "DcmConfirm": "同意",
        "DcmTicketId": "20240102007550",
        "DrillDate": "2024-01-29",
        "DrillMajor": "配电系统",
        "DrillType": "",
        "EgConfirm": "同意",
        "EgTicketId": "20240102007574",
        "ExercisePlan": "机架掉电演练- PDU故障",
        "FmcTicketId": "",
        "Id": "2nsHyTjuFVPfh8CqE8Myhh",
        "MozuName": "清远清新模组11",
        "PlanId": "",
        "PlanMonth": "2024-01",
        "PlanStatus": "已完成",
        "PlanTicketId": "20240102007547",
        "RegionName": "华南",
        "SceneLevel1": "",
        "TodoTicketId": "20240129002119" 
      }
    ],
    "TotalRows": 14 
  },
  "Details": "",
  "Return": 0
}
```

## 演练-演练结果提交

**示例：**

```json
{
   "Action": "DrillCreateToDoTicket",
  "Data": {
     "FacilitatorName": "万国",
    "FacilitatorTicketId": "5458769760998",
    "campus_name": "[string]",
    "change_id": "[string]",
    "drill_date": "[string]",
    "drill_major": "[string]",
    "drill_type": "[string]",
    "exercise_plan": "[string]",
    "mozu_name": "[string]",
    "operation": "[string]",
    "optimizer": "[string]",
    "plan_id": "[string]",
    "plan_month": "[string]",
    "problem_table": [{
       "discovery_date": "[string]",
      "is_risk": "[string]",
      "measure": "[string]",
      "plan_end_date": "[string]",
      "problem_content": "[string]",
      "problem_type": "[string]",
      "users": "[string]" 
    }],
    "region_name": "[string]",
    "step1_table": [{
       "check_item": "[string]",
      "check_point": "[string]",
      "ck": "[string]",
      "end_time": "[string]",
      "score": "[string]",
      "start_time": "[string]",
      "time_consum_end": "[int]",
      "users": "[string]" 
    }],
    "step2_table": [{
       "check_item": "[string]",
      "check_point": "[string]",
      "ck": "[string]",
      "end_time": "[string]",
      "score": "[string]",
      "start_time": "[string]",
      "time_consum_end": "[int]",
      "users": "[string]" 
    }],
    "summary": "[string]",
    "tid": "[string]",
    "time_consum_end": "[int]",
    "time_consum_end_1": "[int]",
    "txt_end": "[string]",
    "txt_end_1": "[string]",
    "txt_start": "[string]",
    "upload_img": [
       "[string]"
    ],
    "users_select": "[string]" ,
    "Method": "CreateToDoTicketTicket",
    "SystemId": "[string]"
}
}
```

**重要字段说明：**

problem_table：没有问题数据传空数组[]值，不能成为null或不传值

upload_img：没有图片数据传空数组[]值，不能成为null或不传值

tid：这是添加演练计划接口返回的计划ID（data.calendars.id）,也就是添加演练计划请求传的Data.plans.gds_id值

**成功返回示例：**

```json
{
   "data": {
     "TicketId": 20241128003933,
    "ticketData": {
       "campus_name": "深圳-光明",
      "change_id": "",
      "drill_date": "2024-11-30",
      "drill_major": "配电系统",
      "drill_type": "定制化",
      "exercise_plan": "单路市电中断",
      "mozu_name": "深圳光明模组01",
      "operation": "否",
      "optimizer": "",
      "plan_id": "2QcHVrGwzpri9f8RtZZZdF",
      "plan_month": "2024-12",
      "problem_table": [
         {
           "discovery_date": "2024-11-30",
          "is_risk": "否",
          "measure": "措施",
          "plan_end_date": "2024-12-10",
          "problem_content": "流程内容",
          "problem_type": "流程问题",
          "users": "跟进用户" 
        } 
      ],
      "region_name": "华南",
      "step1_table": [
         {
           "check_item": "检查项",
          "check_point": "检查点",
          "ck": "达标;跑位有偏差",
          "end_time": "2024-11-30 12:12:42",
          "score": 10,
          "start_time": "2024-11-30 12:12:12",
          "time_consum_end": 30,
          "users": "v_xzxxian;v_guanbchen" 
        } 
      ],
      "step2_table": [
         {
           "check_item": "检查项",
          "check_point": "检查点",
          "ck": "跑位有偏差",
          "end_time": "2024-11-30 12:13:42",
          "score": 10,
          "start_time": "2024-11-30 12:12:42",
          "time_consum_end": 60,
          "users": "v_xzxxian;v_guanbchen" 
        } 
      ],
      "summary": "演练总结",
      "tid": "gfhbtrtrng",
      "time_consum_end": 90,
      "time_consum_end_1": 30,
      "txt_end": "2024-11-30 12:13:42",
      "txt_end_1": "2024-11-30 12:12:42",
      "txt_start": "2024-11-30 12:12:12",
      "upload_img": [
         {
           "response": {
             "FileList": [
               {
                 "url": "https://test.otob.dcops.qq.com/relay/cosfile/relay/20251209/1765263232_7080992c3c975cdf4c2d7219ce373d70.png",
                "name": "测试用.png"
              } 
            ] 
          },
          "percentage": 100 
        } 
      ],
      "users_select": "v_xzxxian;v_guanbchen" 
    } 
  },
  "msg": "",
  "status": 0
}
```

**计划不存在返回示例：**

```json
{
  "data": "5345gfdddd6计划不存在",
  "msg": "",
  "status": -1
}
```

## 演练-通过单号获取演练结果信息

**请求示例：**

```json
{
   "Action": "DrillCreateToDoTicket",
  "Data": {
     "ticket_id": "[string]" ,
    "Method": "GetToDoTicketTicketInfo",
    "SystemId": "[string]"
}
}
```

**真实请求示例：**

```json
{
  "Action": "DrillCreateToDoTicket",
  "Method": "GetToDoTicketTicketInfo",
  "SystemId": "2",
  "data": {
    "ticket_id": "10241212141566"
  }
}
```

**成功返回示例：**

```json
{
  "data": {
    "all_time_offset_rate": "",
    "campus_name": "清远-清新",
    "campus_time_offset_rate": "",
    "change_id": "",
    "compliance_check": "",
    "compliance_check_note": "",
    "compliance_rate": "",
    "dc_manager_user": "",
    "drill_major": "弱电系统",
    "drill_mode": "计划",
    "drill_type": "标准",
    "end_time": "",
    "end_user": "",
    "exercise_plan": "维护平台失效",
    "fmc_check_status": "未审批",
    "fmc_ticket_id": "",
    "group_users": "",
    "instance_id": "bee1dabe-b858-11ef-a9c7-3e9258cb8dff",
    "is_om_service": "否",
    "is_pass_note": "",
    "mozu_name": "清远清新模组11",
    "om_comfirm_note": "",
    "om_confirm": "",
    "om_summary": "",
    "om_users": "",
    "operation": "",
    "operations_users": "",
    "optimizer": "",
    "plan_id": "Cejo9r85ZPsxcHK3nmDTN6",
    "region_name": "华南",
    "resluts": "",
    "resluts_list": "",
    "score_count": "",
    "start_time": "",
    "step1_notify": "DCOPS平台系统突发异常",
    "step2_notify": "1）内外网DCOPS平台维护板块系统异常；无法进行维护申请提单或实施单无法操作/收派 2）其他业务板块系统异常； 3）后台服务器卡住，导致系统无法建单及流转操作",
    "summary": "",
    "ticket_check_time": "",
    "ticket_check_user": "",
    "ticket_creat_time": "",
    "ticket_id": "10241212141566",
    "ticket_reslut": "",
    "ticket_status": "待开始",
    "time_consum_end": "",
    "time_consum_end_1": "",
    "users_count": "",
    "working_hours_check": "",
    "pdf_url": "PDF文档URL" ,
    "msg": "",
    "status": 0
  }
}
```

## 演练-演练实施工单信息分页查询

**真实请求示例：**

```json
{
  "Action": "QueryData",
  "Method": "run",
  "SystemId": "2",
  "SchemaId": "drill_tode_ticket_info",
  "ReturnTotalRows": 1,
  "Data": {
    "ResultColumns": {
       "AllTimeOffsetRate": "",
      "CampusName": "",
      "CampusTimeOffsetRate": "",
      "ChangeId": "",
      "ComplianceCheck": "",
      "ComplianceCheckNote": "",
      "ComplianceRate": "",
      "DcManagerUser": "",
      "DrillMajor": "",
      "DrillMode": "",
      "DrillType": "",
      "EndTime": "",
      "EndUser": "",
      "ExercisePlan": "",
      "FmcCheckStatus": "",
      "FmcTicketId": "",
      "GroupUsers": "",
      "InstanceId": "",
      "IsOmService": "",
      "IsPassNote": "",
      "MozuName": "",
      "OmComfirmNote": "",
      "OmConfirm": "",
      "OmSummary": "",
      "OmUsers": "",
      "Operation": "",
      "OperationsUsers": "",
      "Optimizer": "",
      "PdfUrl": "",
      "PlanId": "",
      "RegionName": "",
      "Resluts": "",
      "ReslutsList": "",
      "ScoreCount": "",
      "StartTime": "",
      "Step1Notify": "",
      "Step2Notify": "",
      "Summary": "",
      "TicketCheckTime": "",
      "TicketCheckUser": "",
      "TicketCreatTime": "",
      "TicketId": "",
      "TicketReslut": "",
      "TicketStatus": "",
      "TimeConsumEnd": "",
      "TimeConsumEnd1": "",
      "UsersCount": "",
      "WorkingHoursCheck": ""
    },
    "SearchCondition": {
      "MozuName": [
        "张家口怀来东园模组32"
      ],
      "TicketStatus": "已完成",
      "TicketCheckTime": {
        "gte": "2025-01-01 00:00:00",
        "lt": "2026-01-01 00:00:00"
      } ,
      "Sorts": [{
         "Column": "TicketId",
        "SortType": "desc"
      }
    ],
    "Limit": {
      "Size": 10,
      "Start": 0
}
}
}
}
```

**成功返回示例：**

```json
{
  "data": {
    "List": [
      {
        "AllTimeOffsetRate": 0,
        "CampusName": "张家口-怀来东园",
        "CampusTimeOffsetRate": 0,
        "ChangeId": "",
        "ComplianceCheck": "",
        "ComplianceCheckNote": "",
        "ComplianceRate": 0.4117647,
        "DcManagerUser": "",
        "DrillMajor": "弱电系统",
        "DrillMode": "计划",
        "DrillType": "标准",
        "EndTime": "2025-05-20 09:36:51",
        "EndUser": "v_qigangli",
        "ExercisePlan": "变更平台失效",
        "FmcCheckStatus": "已起单",
        "FmcTicketId": "10250527002181",
        "GroupUsers": "v_guanbchen(陈冠磬)",
        "InstanceId": "f23fcce1-abc1-11ef-985c-4218c644975b",
        "IsOmService": "否",
        "IsPassNote": "",
        "MozuName": "张家口怀来东园模组32",
        "OmComfirmNote": "",
        "OmConfirm": "",
        "OmSummary": "",
        "OmUsers": "",
        "Operation": "否",
        "OperationsUsers": "v_guanbchen",
        "Optimizer": "v_qigangli",
        "PdfUrl": "",
        "PlanId": "EuZDwM5FMV7fMqsGJ2HLYg",
        "RegionName": "华北",
        "Resluts": "",
        "ReslutsList": "",
        "ScoreCount": 0,
        "StartTime": "2025-05-20 09:35:47",
        "Step1Notify": "DCOPS平台系统突发异常",
        "Step2Notify": "1）内外网DCOPS平台变更板块系统异常；无法进行变更申请提单或实施单无法操作/收派 2）其他业务板块系统异常。",
        "Summary": "",
        "TicketCheckTime": "",
        "TicketCheckUser": "",
        "TicketCreatTime": "",
        "TicketId": "20241126005191",
        "TicketReslut": "",
        "TicketStatus": "已完成",
        "TimeConsumEnd": 37,
        "TimeConsumEnd1": 27,
        "UsersCount": 1,
        "WorkingHoursCheck": ""
      }
    ],
    "TotalRows": 104
  },
  "msg": "",
  "status": 0
}
```

## 演练-演练结果步骤信息查询

**真实请求示例：**

```json
{
   "Action": "QueryData",
  "Method": "run",
  "SystemId": "1",
  "SchemaId": "drill_step_list",
  "ReturnTotalRows": 1,
  "Data": {
     "ResultColumns": {
       "CheckItem": "",
      "Ck": "",
      "DrillType": "",
      "EndTime": "",
      "Id": "",
      "Score": "",
      "StartTime": "",
      "StepName": "",
      "TicketId": "",
      "TimeConsumEnd": "",
      "Users": "" ,
      "SearchCondition": {
         "TicketId": [
          "20230913001231"
        ] ,
        "Sorts": [
        ],
        "Limit": {
           "Size": 20,
          "Start": 0
}
}
}
}
}
```

**成功返回示例：**

```json
{
   "Data": {
     "List": [
       {
         "CheckItem": "1.1 到达告警位置，同步现场信息",
        "Ck": "达标",
        "DrillType": "标准",
        "EndTime": "",
        "Id": 136,
        "Score": 0,
        "StartTime": "",
        "StepName": "故障定位",
        "TicketId": "20230913001231",
        "TimeConsumEnd": 0,
        "Users": "geweenge(葛玮)" 
      },
      {
         "CheckItem": "1.2 现场查看确认告警是否真实，检查列头柜及机柜周边有无明显的异味和烟雾，确认机柜PDU、电源模块及供电回路有无明显的短路、相间接地的故障点；",
        "Ck": "达标",
        "DrillType": "标准",
        "EndTime": "",
        "Id": 137,
        "Score": 0,
        "StartTime": "",
        "StepName": "故障定位",
        "TicketId": "20230913001231",
        "TimeConsumEnd": 0,
        "Users": "leocxiong(熊操)" 
      }
    ],
    "TotalRows": 13 
  },
  "Details": "",
  "Return": 0
}
```

## 演练-通过单号获取演练附件

**请求示例：**

```json
{
   "Action": "DrillCreateToDoTicket",
  "Data": {
     "ticket_ids": [
       "[string]"
    ] 
  },
  "Method": "GetToDoTicketAttachments",
  "SystemId": "[string]"
}
```

**真实请求示例：**

```json
{
  "Action": "DrillCreateToDoTicket",
  "Data": {
    "ticket_ids": [
      "10250919002986"
    ]
  },
  "Method": "GetToDoTicketAttachments",
  "SystemId": "2"
}
```

**成功返回示例：**

```json
{
  "data": [
    {
      "create_time": "2025-09-19 10:52:25",
      "drill_ticket_id": "10250919003104",
      "files": "",
      "id": "2",
      "imgs": "[{\"raw\": {\"uid\": 1758250331935}, \"uid\": 1758250331935, \"url\": \"blob:https://dcops.test.woa.com/8053033d-d73e-43ab-a1da-28b2a534106c\", \"name\": \"微信截图_20240822105326 (1).png\", \"size\": 1884426, \"status\": \"success\", \"response\": {\"FileList\": [{\"uid\": 1758250332737, \"url\": \"https://test.otob.dcops.qq.com/relay/cosfile/relay/20250919/1758248676_470438dd3218b8f90547b24d683f43d9.png\", \"name\": \"微信截图_20240822105326 (1).png\", \"status\": \"success\"}]}, \"percentage\": 100}]",
      "videos": "[{\"raw\": {\"uid\": 1758250339544}, \"uid\": 1758250339544, \"name\": \"idc_drill_sql_result_20250912113751.txt\", \"size\": 6026, \"status\": \"success\", \"response\": {\"FileList\": [{\"uid\": 1758250339732, \"url\": \"https://test.otob.dcops.qq.com/relay/cosfile/relay/20250919/1758250340_ba9098330d3b850b5d1337c15e51a274.txt\", \"name\": \"idc_drill_sql_result_20250912113751.txt\", \"status\": \"success\"}]}, \"percentage\": 100}]"
    },
    {
      "create_time": "2025-09-19 10:50:15",
      "drill_ticket_id": "10250919002986",
      "files": "",
      "id": "1",
      "imgs": "[{\"raw\": {\"uid\": 1758250186195}, \"uid\": 1758250186195, \"url\": \"blob:https://dcops.test.woa.com/76f9f018-d687-45e5-8f47-348e403125a5\", \"name\": \"微信截图_20240822105326 (1).png\", \"size\": 1884426, \"status\": \"success\", \"response\": {\"FileList\": [{\"uid\": 1758250187049, \"url\": \"https://test.otob.dcops.qq.com/relay/cosfile/relay/20250919/1758248676_470438dd3218b8f90547b24d683f43d9.png\", \"name\": \"微信截图_20240822105326 (1).png\", \"status\": \"success\"}]}, \"percentage\": 100}]",
      "videos": "[{\"raw\": {\"uid\": 1758250207414}, \"uid\": 1758250207414, \"name\": \"idc_drill_sql_result_20250912112839.txt\", \"size\": 13162, \"status\": \"success\", \"response\": {\"FileList\": [{\"uid\": 1758250207660, \"url\": \"https://test.otob.dcops.qq.com/relay/cosfile/relay/20250919/1758250208_2d0e0340e5c06a74637937bfeb5a2673.txt\", \"name\": \"idc_drill_sql_result_20250912112839.txt\", \"status\": \"success\"}]}, \"percentage\": 100}]"
    }
  ],
  "msg": "",
  "status": 0
}
```

说明：imgs是图片数组的json，videos是视频的数组的json，url是附件的下载连接。

## 演练-通过单号获取演练PDF报告

**请求示例：**

```json
{
   "Action": "DrillCreateToDoTicket",
  "Data": {
     "ticket_id": "[string]" ,
    "Method": "GetToDoTicketPdfUrl",
    "SystemId": "[string]"
}
}
```

**真实请求示例：**

```json
{
  "Action": "DrillCreateToDoTicket",
  "Method": "GetToDoTicketPdfUrl",
  "SystemId": "2",
  "Data": {
    "ticket_id": "10250911003038"
  }
}
```

**成功返回示例：**

```json
{
  "data": {
    "Data": {
      "call_stack": "",
      "code": 0,
      "exc_info": "",
      "req_id": "bc8efe1f-1aa4-48d2-868e-463ad279e5dd",
      "result": [
        "https://test.otob.dcops.qq.com/relay/cosfile/ylpdfzhuanxiang/2025-09-18 11:59:10 TABgvIlP2jrTdrill_order.pdf"
      ],
      "system_name": "proxy"
    },
    "Details": "",
    "Return": 0 ,
    "msg": "",
    "status": 0
}
}
```

## 机架开关电-派单POST

**示例：**

```json
{
   "Action": "RackPowerTicket",
  "Data": {
     "app": "[string]",
    "bizAreaName": "[string]",
    "busiType": "[string]",
    "creator": "[string]",
    "desc": "[string]",
    "deviceList": [{
       "assetId": "[string]",
      "idcUnitId": "[string]",
      "idcUnitName": "[string]",
      "pn": "[string]",
      "rackCode": "[string]",
      "rackId": "[string]",
      "rackSpCode": "[string]",
      "sn": "[string]" 
    }],
    "dispatchTime": "[string]",
    "expectTime": "[string]",
    "instanceId": "[string]",
    "projectType": "[string]",
    "rackList": [{
       "idcUnitId": "[string]",
      "idcUnitName": "[string]",
      "rackCode": "[string]",
      "rackId": "[string]",
      "rackSpCode": "[string]" 
    }],
    "taskId": "[string]" ,
    "Method": "CreateRackPowerTicket",
    "SystemId": "[string]"
}
}
```

**成功返回示例：**

```json
{
  "data": "操作成功",
  "msg": "",
  "status": 0
}
```

## 机架开关电-工单查询POST

**示例：**

```json
{
   "Action": "RackPowerTicket",
  "Data": {
     "instanceId": "[string]",
    "taskId": "[string]" ,
    "Method": "GetRackPowerTicket",
    "SystemId": "[string]"
}
}
```

**成功返回示例：**

```json
{
  "data": {
    "app": "idcrm",
    "deviceList": [
      {
        "assetId": "A-05",
        "idcUnitId": "05",
        "idcUnitName": "清远腾讯清新DC1号楼101",
        "pn": "A-05",
        "rackCode": "",
        "rackId": "763306",
        "rackSpCode": "",
        "result": "",
        "sn": "A-05"
      }
    ],
    "idcInstId": "",
    "instanceId": "OFF202211301532",
    "rackList": [
      {
        "rackId": "",
        "rackCode": "",
        "result": "",
        "message": "",
        "handler": "",
        "failType": "",
        "failRemark": "",
        "nodeId": "",
        "emailSendTime": "",
        "completeTime": ""
      }
    ],
    "taskId": "OFF202211301532"
  },
  "msg": "",
  "status": 0
}
```

## 机架开关电-批量派单POST

**示例：**

```json
{
   "Action": "RackPowerTicket",
  "Data": {
     "racks": [{
       "app": "[string]",
      "busiType": "[string]",
      "creator": "[string]",
      "desc": "[string]",
      "dispatchTime": "[string]",
      "expectTime": "[string]",
      "instanceId": "[string]",
      "projectType": "[string]",
      "rackList": [{
         "deviceList": [{
           "assetId": "[string]",
          "pn": "[string]",
          "rackId": "[string]",
          "sn": "[string]" 
        }],
        "rackId": "[string]" 
      }],
      "taskId": "[string]" 
    }] 
  },
  "Method": "CreateRackPowerTicketList",
  "SystemId": "[string]"
}
```

**成功返回示例：**

```json
{
  "data": "操作成功",
  "msg": "",
  "status": 0
}
```

## 安全管理-人员进出建单POST

**请求体：**

```json
{
  "Action": "PeopleInOut",
  "Data": {
    "system_id": "2",
    "campus": "北京-兆丰",
    "ticket_description": "调用方app:业务工单号",
    "ticket_title": "",
    "bizarea": "北京腾讯兆丰DC",
    "factor": "其他",
    "beizhu": "测试",
    "idc_unit": "北京腾讯兆丰DC2号楼M103,北京腾讯兆丰DC2号楼M105",
    "entry_time": "2024-12-25 13:00:00",
    "leave_time": "2024-12-25 13:00:00",
    "visitors": [
      {
        "company": "腾讯",
        "email": "v_twxie@tencent.com",
        "gender": "男",
        "id_number": "445102200210171213",
        "id_type": "身份证",
        "name": "谢腾纬",
        "number": 1,
        "phone_number": "13727908666",
        "photo": "",
        "wechat_number": "13727908666"
      },
      {
        "company": "",
        "email": "",
        "gender": "",
        "id_number": "445102200210171214",
        "id_type": "身份证",
        "name": "小李",
        "number": 2,
        "phone_number": "13727908666",
        "photo": "",
        "wechat_number": ""
      }
    ],
    "partners": [
      {
        "company": "",
        "email": "",
        "gender": "",
        "id_number": "445102200210171215",
        "id_type": "身份证",
        "name": "小明",
        "number": 1,
        "phone_number": "13727908666",
        "photo": "",
        "wechat_number": ""
      }
    ],
    "car": [
      {
        "car_number": "粤U46862",
        "driver_name": "谢腾纬",
        "driver_number": "13727908666"
      }
    ],
    "other_list": "其他区域",
    "room": "北京兆丰腾讯BD1PR0229,北京兆丰腾讯BD2PR0127"
  },
  "Method": "OutCreateTicketVisitor",
  "SystemId": "2"
}
```

**成功结果：**

```json
{
  "Return": 200,
  "Details": "",
  "ReqId": "ifob-freda8bq2scxxpm",
  "Data": {
    "Return": 0,
    "Details": "",
    "Data": {
      "InstanceId": "f1a6f34d-3c3c-11f0-b1e4-2affcecd634b",
      "TicketId": 10250529001869
    }
  }
}
```

## 安全管理-人员进出工单信息查询POST

**查询方式一：**

```json
{
  "Action": "PeopleInOut",
  "Data": {
    "system_id": "2",
    "campus_name": "北京-兆丰",
    "check_result": "已通过",
    "create_time": {
      "end": "2025-06-03 13:33:11",
      "start": "2025-04-03 13:33:11"
    },
    "finish_time": {
      "end": "2025-06-03 13:34:11",
      "start": "2025-06-03 13:33:11"
    },
    "ticket_id": ""
  },
  "Method": "OutQueryVisitorData",
  "SystemId": "2"
}
```

**成功结果：**

```json
{
  "Return": 200,
  "Details": "",
  "ReqId": "ifob-fredacnl1gl2xb0",
  "Data": [
    {
      "applicant_list": [
        {
          "company": "腾讯",
          "email": "v_twxie@tencent.com",
          "gender": "男",
          "id_number": "445102200210171213",
          "id_type": "身份证",
          "name": "谢腾纬",
          "number": 1,
          "phone_number": "13727908666",
          "photo": "",
          "wechat_number": "13727908666"
        },
        {
          "company": "",
          "email": "",
          "gender": "",
          "id_number": "445102200210171214",
          "id_type": "身份证",
          "name": "小李",
          "number": 2,
          "phone_number": "13727908666",
          "photo": "",
          "wechat_number": ""
        },
        {
          "company": "",
          "email": "",
          "gender": "",
          "id_number": "445102200210171215",
          "id_type": "身份证",
          "name": "小明",
          "number": 1,
          "phone_number": "13727908666",
          "photo": "",
          "wechat_number": ""
        }
      ],
      "car_list": [
        {
          "car_number": "粤U46862",
          "driver_name": "谢腾纬",
          "driver_number": "13727908666"
        }
      ],
      "partner_list": [
        {
          "company": "",
          "email": "",
          "gender": "",
          "id_number": "445102200210171215",
          "id_type": "身份证",
          "name": "小明",
          "number": 1,
          "phone_number": "13727908666",
          "photo": "",
          "wechat_number": ""
        }
      ],
      "ticket_detail": {
        "applicant": "v_twxie",
        "beizhu": "测试",
        "bizarea": "北京腾讯兆丰DC",
        "campus": "北京-兆丰",
        "entry_time": "2024-12-25 13:00",
        "exit_time": "2024-12-25 13:00",
        "factor": "其他",
        "function_room": "北京兆丰腾讯BD1PR0229, 北京兆丰腾讯BD2PR0127, 北京腾讯兆丰DC2号楼M103, 北京腾讯兆丰DC2号楼M105",
        "it_room": "北京腾讯兆丰DC2号楼M103, 北京腾讯兆丰DC2号楼M105",
        "other_area": "其他区域",
        "result": "已通过",
        "start_time": "2025-05-29  14:50:18",
        "finish_time": "2025-06-03 13:33:11",
        "ticket_id": "10250529002237",
        "visitor_email": ""
      },
      "ticket_id": "10250529002237",
      "ticket_type": "visitor"
    }
  ]
}
```

**查询方式二：**

```json
{
  "Action": "PeopleInOut",
  "Data": {
    "system_id": "2",
    "campus_name": "北京-兆丰",
    "check_result": "",
    "create_time": {},
    "finish_time": {},
    "ticket_id": "10250529001885"
  },
  "Method": "OutQueryVisitorData",
  "SystemId": "2"
}
```

**成功结果：**

```json
{
  "Return": 200,
  "Details": "",
  "ReqId": "ifob-freda8d8fs7n84a",
  "Data": [
    {
      "applicant_list": [
        {
          "company": "string",
          "email": "v_twxie@tencent.com",
          "gender": "男",
          "id_number": "445102200210171213",
          "id_type": "身份证",
          "name": "谢腾纬",
          "number": 1,
          "phone_number": "13727908666",
          "photo": "",
          "wechat_number": "13727908666"
        }
      ],
      "car_list": [
        {
          "car_number": "粤U46862",
          "driver_name": "谢腾纬",
          "driver_number": "13727908666"
        }
      ],
      "partner_list": [],
      "ticket_detail": {
        "applicant": "v_twxie",
        "beizhu": "测试",
        "bizarea": "北京腾讯兆丰DC",
        "campus": "北京-兆丰",
        "entry_time": "2024-12-25 13:00",
        "exit_time": "2024-12-25 13:00",
        "factor": "其他",
        "function_room": "北京腾讯兆丰DC2号楼M103, 北京腾讯兆丰DC2号楼M105",
        "it_room": "北京腾讯兆丰DC2号楼M103, 北京腾讯兆丰DC2号楼M105",
        "other_area": "其他区域",
        "result": "进行中",
        "start_time": "2025-05-29  11:41:02",
        "ticket_id": "10250529001885",
        "visitor_email": ""
      },
      "ticket_id": "10250529001885",
      "ticket_type": "visitor"
    }
  ]
}
```

## 安全管理-物资进出建单POST

**请求体：**

```json
{
  "Action": "GoodsInOut",
  "Data": {
    "ticket_description": "调用方app:",
    "system_id": "2",
    "campus": "北京-兆丰",
    "ticket_title": "",
    "bizarea": "北京腾讯兆丰DC",
    "reason_mark": "测试",
    "building": "北京兆丰腾讯BD1",
    "factor": "其他",
    "email": "v_twxie@tencent.com",
    "idcUnit": "",
    "pass_date": "2023-04-05 00:00:00",
    "visitor": [
      {
        "company": "腾讯",
        "email": "v_twxie@tencent.com",
        "gender": "男",
        "id_number": "445102200210171213",
        "id_type": "身份证",
        "name": "谢腾纬",
        "phone_number": "13727908666",
        "photo": "",
        "wechat_number": ""
      }
    ],
    "pass_material": [
      {
        "assets_id": "100-444",
        "count": "2",
        "model": "tx-123",
        "type": "计算机",
        "unit": "个"
      }
    ]
  },
  "Method": "OutCreateTicketMaterial",
  "SystemId": "2"
}
```

**成功起单返回：**

```json
{
  "Return": 200,
  "Details": "",
  "ReqId": "ifob-freda8cv8nqux7f",
  "Data": {
    "Return": 0,
    "Details": "",
    "Data": {
      "InstanceId": "7458462b-3c44-11f0-b1e4-2affcecd634b",
      "TicketId": 10250529001918
    }
  }
}
```

## 安全管理-物资进出工单信息查询POST

**按起单时间/结单时间查询：**

```json
{
  "Action": "GoodsInOut",
  "Data": {
    "system_id": "2",
    "campus_name": "北京-兆丰",
    "check_result": "已通过",
    "create_time": {
      "end": "2025-01-03 12:59:02",
      "start": "2025-01-03 12:57:02"
    },
    "finish_time": {
      "end": "2025-01-03 14:47:14",
      "start": "2025-01-03 14:45:14"
    },
    "ticket_id": ""
  },
  "Method": "OutQueryMaterialData",
  "SystemId": "2"
}
```

**按工单号查询：**

```json
{
  "Action": "GoodsInOut",
  "Data": {
    "system_id": "2",
    "campus_name": "北京-兆丰",
    "check_result": "",
    "create_time": {},
    "finish_time": {},
    "ticket_id": "20250103021091"
  },
  "Method": "OutQueryMaterialData",
  "SystemId": "2"
}
```

**成功查询接口返回：**

```json
{
  "Return": 200,
  "Details": "",
  "ReqId": "ifob-freda8dc1r8173b",
  "Data": [
    {
      "applicant_list": [
        {
          "company": "腾讯",
          "email": "v_twxie@tencent.com",
          "gender": "男",
          "id_number": "445102200210171213",
          "id_type": "身份证",
          "name": "谢腾纬",
          "phone_number": "13727908666",
          "photo": "",
          "wechat_number": ""
        }
      ],
      "material_list": [
        {
          "assets_id": "100-444",
          "count": "2",
          "model": "tx-123",
          "relation_id_const": "",
          "type": "计算机",
          "unit": "个"
        }
      ],
      "ticket_detail": {
        "applicant": "谢腾纬",
        "bizarea": "北京腾讯兆丰DC",
        "buildings_name": "北京兆丰腾讯BD1",
        "campus_name": "北京-兆丰",
        "create_time": "2025-01-03  12:58:02",
        "complete_time": "2025-01-03  14:46:14",
        "creator": "v_twxie",
        "email_account": "v_twxie@tencent.com",
        "manager_status": "已通过",
        "pass_time": "",
        "reason_remark": "测试",
        "reason_type": "其他",
        "ticket_id": "20250103021091"
      },
      "ticket_id": "20250103021091",
      "ticket_type": "material"
    }
  ]
}
```

## 人员招聘-需求单查询

**建表语句：**

```sql
CREATE TABLE `requirement_info` (
  `id` int(9) NOT NULL AUTO_INCREMENT,
  `ticket_id` varchar(25) DEFAULT NULL COMMENT '工单号',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `group_name` varchar(25) DEFAULT NULL COMMENT '园区/小组',
  `requirement_ticket` varchar(25) NOT NULL COMMENT '需求单号',
  `requirement_type` varchar(9) DEFAULT NULL COMMENT '需求类型',
  `requirement_status` varchar(25) DEFAULT NULL COMMENT '需求状态',
  `billing_position` varchar(25) NOT NULL COMMENT '面试岗位',
  `need_cnt` int(9) NOT NULL DEFAULT '0' COMMENT '需求数量',
  `head_cnt` int(9) NOT NULL COMMENT '剩余HC',
  `expect_time` varchar(25) NOT NULL COMMENT '期望到岗时间',
  `workplace` varchar(25) NOT NULL COMMENT '工作地点',
  `demand` varchar(255) DEFAULT NULL COMMENT '需求人',
  `review_desc` text COMMENT '需求复核说明',
  `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '逻辑删除',
  `resume_count` int(9) DEFAULT NULL COMMENT '推送简历数量',
  `pass_resume_count` int(9) DEFAULT NULL COMMENT '简历筛选数量',
  `interview_count` int(9) DEFAULT NULL COMMENT '面试人数',
  `pass_interview_count` int(9) DEFAULT NULL COMMENT '面试通过人数',
  `real_onboarding_time` varchar(25) DEFAULT NULL COMMENT '实际入职时间',
  `duration` int(9) DEFAULT NULL COMMENT '招聘时长',
  `is_pass` text COMMENT '是否合格',
  `applicant` varchar(25) DEFAULT NULL COMMENT '申请人',
  `hr_contact` varchar(255) DEFAULT NULL COMMENT 'HR接口人',
  `sync_time` varchar(25) DEFAULT NULL COMMENT '需求同步时间',
  `done_time` varchar(25) DEFAULT NULL COMMENT '招聘完成时间',
  `fmc` varchar(255) DEFAULT NULL COMMENT 'FMC',
  `pmc` varchar(255) DEFAULT NULL COMMENT 'PMC',
  `service_provider` varchar(45) DEFAULT NULL COMMENT '供应商',
  `reason` varchar(255) DEFAULT NULL COMMENT '申请原因',
  PRIMARY KEY (`id`) USING BTREE,
  UNIQUE KEY `idx_unique_requirement_info` (`requirement_ticket`,
  `billing_position`),
  KEY `ticket_id` (`ticket_id`) USING BTREE 
) ENGINE=InnoDB AUTO_INCREMENT=269 DEFAULT CHARSET=utf8mb4 COMMENT='招聘需求信息'
```

支持查询字段

| 字段名称   | 字段 ID               |
|--------|---------------------|
| 主键ID   | `Id`                |
| 工单ID   | `TicketId`          |
| 创建时间   | `CreatedAt`         |
| 更新时间   | `UpdatedAt`         |
| 需求单号   | `RequirementTicket` |
| 面试岗位   | `BillingPosition`   |
| 需求状态   | `RequirementStatus` |
| 需求类型   | `RequirementType`   |
| 供应商    | `ServiceProvider`   |
| 园区     | `GroupName`         |
| 需求数量   | `NeedCnt`           |
| 期望到岗时间 | `ExpectTime`        |
| 工作地点   | `Workplace`         |
| 供应商    | `ServiceProvider`   |

查询条件

| 字段名称 | 字段 ID               |
|------|---------------------|
| 工单ID | `TicketId`          |
| 需求单号 | `RequirementTicket` |
| 面试岗位 | `BillingPosition`   |
| 需求状态 | `RequirementStatus` |
| 需求类型 | `RequirementType`   |
| 供应商  | `ServiceProvider`   |
| 园区   | `GroupName`         |

**请求体：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "2",
  "SchemaId": "recruit_requirement",
  "Data": {
    "ResultColumns": {
      "TicketId": "",
      "CreatedAt": "",
      "RequirementTicket": ""
    },
    "SearchCondition": {
      "RequirementTicket": "20250922028961"
    }
  },
  "BussType": "人员招聘"
}
```

**返回示例：**

```json
{
  "data": {
    "List": [
      {
        "BillingPosition": "应急工程师",
        "CreatedAt": "2025-09-22 14:47:22",
        "RequirementTicket": "20250922028961",
        "RequirementType": "新增需求",
        "ServiceProvider": "昌泰云",
        "TicketId": "20250925034688"
      }
    ],
    "TotalRows": 0
  },
  "msg": "",
  "status": 0
}
```

## 人员招聘-人员状态查询

**建表语句：**

```sql
CREATE TABLE `requirement_records` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `ticket_id` varchar(25) DEFAULT NULL COMMENT '工单号',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` varchar(19) DEFAULT NULL COMMENT '更新时间',
  `group_name` varchar(25) DEFAULT NULL COMMENT '园区/小组',
  `requirement_ticket` varchar(25) DEFAULT NULL COMMENT '需求单号',
  `billing_position` varchar(25) DEFAULT NULL COMMENT '面试岗位',
  `name` varchar(25) DEFAULT NULL COMMENT '姓名',
  `resume_file` json DEFAULT NULL COMMENT '简历附件',
  `resume_status` varchar(9) DEFAULT NULL COMMENT '简历筛选状态',
  `resume_desc` text COMMENT '筛选说明',
  `is_filtered` tinyint(4) DEFAULT '0' COMMENT '是否已筛选',
  `is_qualified` varchar(45) DEFAULT NULL COMMENT '结论',
  `mark` varchar(45) DEFAULT NULL COMMENT '分数',
  `info` text COMMENT '分数说明',
  `interview_time` varchar(25) DEFAULT NULL COMMENT '面试时间',
  `interview_link` text COMMENT '会议链接',
  `interview_status` varchar(9) DEFAULT NULL COMMENT '面试状态',
  `interview_score` int(11) DEFAULT '0' COMMENT '面试得分',
  `interview_desc` text COMMENT '评价说明',
  `interview_record` json DEFAULT NULL COMMENT '面试记录',
  `is_interviewed` tinyint(4) DEFAULT '0' COMMENT '是否已面试',
  `onboarding_status` varchar(255) DEFAULT NULL COMMENT '入职状态',
  `expected_onboarding_time` varchar(25) DEFAULT NULL COMMENT '预计入职时间',
  `is_real_onboarding` varchar(25) DEFAULT '' COMMENT '是否实际入职',
  `real_onboarding_time` varchar(25) DEFAULT NULL COMMENT '实际入职时间',
  `is_confirmed` tinyint(4) DEFAULT '0' COMMENT '是否已确认',
  `service_provider` varchar(45) DEFAULT NULL COMMENT '供应商',
  `is_deleted` tinyint(4) DEFAULT '0' COMMENT '逻辑删除',
  `reason` varchar(255) DEFAULT NULL COMMENT '申请原因',
  PRIMARY KEY (`id`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4
```

**字段说明：**

查询字段: 主键ID字段ID: Id字段名称: 工单ID字段ID: TicketId字段名称: 创建时间字段ID: CreatedAt字段名称: 最后更新时间字段ID: UpdatedAt字段名称: 园区字段ID: GroupName字段名称: 需求单号字段ID: RequirementTicket字段名称: 岗位字段ID: BillingPosition字段名称: 姓名字段ID: Name字段名称: 简历文件字段ID: ResumeFile字段名称: 简历状态字段ID: ResumeStatus字段名称: 简历描述字段ID: ResumeDesc字段名称: 是否已筛选字段ID: IsFiltered字段名称: 面试时间字段ID: InterviewTime字段名称: 面试链接字段ID: InterviewLink字段名称: 面试状态字段ID: InterviewStatus字段名称: 面试描述字段ID: InterviewDesc字段名称: 是否已面试字段ID: IsInterviewed字段名称: 入职状态字段ID: OnboardingStatus字段名称: 预计入职时间字段ID: ExpectedOnboardingTime字段名称: 是否实际入职字段ID: IsRealOnboarding字段名称: 实际入职时间字段ID: RealOnboardingTime字段名称: 是否已确认字段ID: IsConfirmed字段名称: 服务商字段ID: ServiceProvider字段名称: 是否删除字段ID: IsDeleted

二、 支持的查询条件 (Conditions)

查询字段: 工单ID字段ID: TicketId查询字段: 需求单号字段ID: RequirementTicket查询字段: 面试岗位字段ID: BillingPosition查询字段: 姓名字段ID: Name查询字段: 简历状态字段ID: ResumeStatus查询字段: 面试状态字段ID: InterviewStatus查询字段: 入职状态字段ID: OnboardingStatus查询字段: 园区字段ID: GroupName

**请求体：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "2",
  "SchemaId": "human_recruit",
  "Data": {
    "ResultColumns": {
      "TicketId": "",
      "CreatedAt": "",
      "Name": "",
      "ResumeStatus": "",
      "ServiceProvider": "",
      "RequirementTicket": ""
    },
    "SearchCondition": {
      "RequirementTicket": "20250922028961"
    }
  },
  "BussType": "人员招聘"
}
```

**返回示例：**

```json
{
  "data": {
    "List": [
      {
        "CreatedAt": "2025-09-22 15:44:57",
        "Name": "张思宁",
        "RequirementTicket": "20250922028961",
        "ResumeStatus": "筛选通过",
        "ServiceProvider": "昌泰云",
        "TicketId": "20250925034688"
      }
    ],
    "TotalRows": 0
  },
  "msg": "",
  "status": 0
}
```

## 人员招聘-上传简历

**示例：**

```json
{
  "Action": "HumanRecruit",
  "Method": "UploadResume",
  "SystemId": "2",
  "Data": {
    "name": "张三1",
    "requirement_ticket": "20250930042294",
    "ticket_id": "20250930042402",
    "billing_position": "电气工程师",
    "resume_file": "https://test.otob.dcops.qq.com/relay/cosfile/relay/20251029/1761724321_d998c3f7635f68c1bdf4eba3e4cc4faa.xlsx"
  }
}
```

**示例：**

```json
{
  "Return": 0,
  "Details": "",
  "ReqId": "ifob-freddvg9oq73mep",
  "Data": {
    "id": 81,
    "ticket_id": "20250930042402",
    "created_at": "2025-10-30T14:41:05.196+08:00",
    "updated_at": "2025-10-30T14:41:05.196+08:00",
    "requirement_ticket": "20250930042294",
    "billing_position": "电气工程师",
    "name": "张三1",
    "resume_file": "[{\"name\":\"1761724321_d998c3f7635f68c1bdf4eba3e4cc4faa.xlsx\",\"status\":\"success\",\"url\":\"https://test.otob.dcops.qq.com/relay/cosfile/relay/20251029/1761724321_d998c3f7635f68c1bdf4eba3e4cc4faa.xlsx\"}]",
    "resume_status": "待筛选",
    "resume_desc": null,
    "is_filtered": false,
    "is_deleted": false,
    "is_qualified": null,
    "mark": null,
    "info": null
  }
}
```

## 人员招聘-面试时间反馈

**请求体：**

```json
{
   "Action": "HumanRecruit",
  "Method": "InterviewTimeFeedback",
  "SystemId": "2",
  "data": {
     "duration": 2,
    "user_name": "v_zixxyang",
    "ticket_id": "20250930042402",
    "interview_start_time": "202511150000",
    "name": "王五",
    "requirement_ticket": "20250930042269"
}
}
```

**返回示例：**

```json
{
  "data": {
    "Data": {
      "call_stack": "",
      "code": 0,
      "exc_info": "",
      "req_id": "d516f198-6317-42d5-8646-1d9fad08469f",
      "result": {
        "code": 200,
        "interview_link": "https://meeting.tencent.com/dm/JJveTyJ95e2t",
        "msg": "成功获取面试链接"
      },
      "system_name": "proxy"
    },
    "Details": "",
    "ReqId": "fob-gnetopsde0ga821zj6q",
    "Return": 0
  },
  "msg": "",
  "status": 0
}
```

## 人员招聘-入职状态反馈

**请求体：**

```json
{
   "Action": "HumanRecruit",
  "Method": "OnboardingStatusFeedback",
  "SystemId": "2",
  "data": {
     "billing_position": "测试",
    "expected_onboarding_time": "20251023093000",
    "is_real_onboarding": "未入职",
    "name": "老六",
    "onboarding_status": "入职",
    "real_onboarding_time": "20251024093000",
    "requirement_ticket": "20250930042298",
    "ticket_id": "1234567890",
    "user_name": "v_zixxyang"
}
}
```

**返回示例：**

```json
{
  "data": {
    "Data": {
      "call_stack": "",
      "code": 0,
      "exc_info": "",
      "req_id": "32e1c236-8c0a-4667-bd46-ba4d1993e174",
      "result": {
        "code": 400,
        "msg": "反馈失败,数据未修改"
      },
      "system_name": "proxy"
    },
    "Details": "",
    "ReqId": "fob-gnetopsde0ga04n9q87",
    "Return": 0
  },
  "msg": "",
  "status": 0
}
```

## 变更主单

**建表语句：**

```sql
CREATE TABLE `change_info` (
  `Id` varchar(255) NOT NULL COMMENT '变更单id',
  `ParkName` varchar(255) NOT NULL DEFAULT '无' COMMENT '园区名称',
  `MozuName` text COMMENT '模组名称',
  `ChangeSceneName` varchar(255) DEFAULT NULL COMMENT '变更场景名称',
  `ChangeTheme` varchar(255) DEFAULT NULL COMMENT '变更主题',
  `IsOnsiteSupport` varchar(255) DEFAULT NULL COMMENT '需优化现场支持',
  `ChangeReasonType` varchar(255) DEFAULT NULL COMMENT '变更原因类型',
  `IsReplenishment` varchar(255) DEFAULT NULL COMMENT '是否补单',
  `ChangeReason` varchar(255) DEFAULT NULL COMMENT '变更原因',
  `ChangesFocusPeople` text COMMENT '变更关注人',
  `BuildName` text COMMENT '楼宇名称',
  `PlanStartTime` varchar(255) DEFAULT NULL COMMENT '计划开始时间',
  `PlanEndTime` varchar(255) DEFAULT NULL COMMENT '计划结束时间',
  `ChangeLevel` varchar(255) DEFAULT ' ' COMMENT '变更级别',
  `Major` varchar(255) DEFAULT NULL COMMENT '专业',
  `ChangeType` varchar(255) DEFAULT NULL COMMENT '变更类型',
  `ChangeName` varchar(255) DEFAULT NULL COMMENT '变更名称',
  `Implementer` varchar(255) DEFAULT NULL COMMENT '实施人',
  `CreateTime` varchar(255) DEFAULT NULL COMMENT '创建时间',
  `CompleteTime` varchar(255) DEFAULT NULL COMMENT '结单时间',
  `ApprovalStatus` varchar(255) DEFAULT NULL COMMENT '审批状态',
  `TicketId` varchar(50) DEFAULT NULL COMMENT '审批流程单号',
  `InstanceId` varchar(50) DEFAULT NULL COMMENT '审批流程ID',
  `ChangeOptions` int(9) DEFAULT NULL COMMENT '变更方案id',
  `WBOrder` varchar(255) DEFAULT NULL COMMENT '触发变更dcops单号',
  `IsWB` int(9) DEFAULT NULL COMMENT '是否其他触发0变更1其他',
  `Draft` varchar(9) DEFAULT '未结单' COMMENT '变更单状态：未结单/已结单',
  `Creator` varchar(255) DEFAULT NULL COMMENT '创建人',
  `IsAuto` varchar(50) DEFAULT NULL COMMENT '是否自动化变更',
  `IsEvent` varchar(9) DEFAULT NULL COMMENT '是否事件(暂时无用)',
  `EventOrder` varchar(255) DEFAULT NULL COMMENT '触发变更自定义单号',
  `NoticeMsg` text COMMENT '开始公告内容',
  `IsSendNotice` varchar(9) DEFAULT '' COMMENT '是否发送结单公告：是/否',
  `EndNoticeMsg` text COMMENT '结单公告内容',
  `IsConfigAlarm` varchar(9) DEFAULT '未配置' COMMENT '是否配置告警：未配置/已配置',
  `IsConfigUser` varchar(255) DEFAULT NULL COMMENT '告警配置人',
  `ChangeWorkHour` varchar(255) DEFAULT NULL COMMENT '变更工时',
  `FirstSendBulletin` varchar(255) DEFAULT NULL COMMENT '首次发送公告时间',
  `LastSendBulletin` varchar(255) DEFAULT NULL COMMENT '最后发送公告时间',
  `ChangePlanType` varchar(32) DEFAULT NULL COMMENT '变更计划类型',
  `IsRiskStatement` varchar(9) DEFAULT '否' COMMENT '是否有其他风险说明',
  `RiskStatementDetail` varchar(255) DEFAULT '无' COMMENT '风险说明详情',
  `IsCanDelay` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否可延期（1可延期0不可延期）',
  PRIMARY KEY (`Id`),
  KEY `idx_IsAuto` (`IsAuto`(10)),
  KEY `idx_ParkName` (`ParkName`(116)),
  KEY `Idx_ChangeLevel` (`ChangeLevel`),
  KEY `Idx_CreateTime` (`CreateTime`),
  KEY `idx_status_createtime` (`ApprovalStatus`,
  `CreateTime`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='变更单信息表'
```

| 字段名                | 字段说明                                                 |
|--------------------|------------------------------------------------------|
| Id                 | 变更单id                                                |
| ParkName           | 园区名称                                                 |
| MozuName           | 模组名称                                                 |
| ChangeSceneName    | 变更场景名称                                               |
| ChangeTheme        | 变更主题                                                 |
| IsOnsiteSupport    | 需优化现场支持                                              |
| ChangeReasonType   | 变更原因类型                                               |
| ChangePlanType     | 变更计划类型                                               |
| IsReplenishment    | 是否补单                                                 |
| ChangeReason       | 变更原因                                                 |
| ChangesFocusPeople | 变更关注人                                                |
| BuildName          | 楼宇名称                                                 |
| PlanStartTime      | 计划开始时间                                               |
| PlanEndTime        | 计划结束时间                                               |
| ChangeLevel        | 变更级别                                                 |
| Major              | 专业                                                   |
| ChangeType         | 变更类型                                                 |
| ChangeName         | 变更名称                                                 |
| Implementer        | 实施人                                                  |
| CreateTime         | 创建时间                                                 |
| CompleteTime       | 结单时间                                                 |
| ApprovalStatus     | 审批状态                                                 |
| TicketId           | 审批流程单号                                               |
| InstanceId         | 审批流程ID                                               |
| ChangeOptions      | 变更方案id                                               |
| WBOrder            | 维保变更单号                                               |
| IsWB               | 是否维保0变更1维保                                           |
| Draft              | 变更单状态：未结单/已结单                                        |
| Creator            | 创建人                                                  |
| IsAuto             | 是否自动化变更                                              |
| IsEvent            | 是否事件                                                 |
| EventOrder         | 事件单号                                                 |
| NoticeMsg          | 开始公告内容                                               |
| IsSendNotice       | 是否发送结单公告：是/否                                         |
| EndNoticeMsg       | 结单公告内容                                               |
| IsConfigAlarm      | 是否配置告警：未配置/已配置                                       |
| IsConfigUser       | 告警配置人                                                |
| PingshenResult     | 评审结果                                                 |
| PingshenAdvice     | 评审意见                                                 |
| SchemeType         | 1普通模板、0标准化模板-全国统用、2标准化模板-模组自用、3标准化模板自动检测             |
| ChangeScheme       | 方案名称                                                 |
| ConfirmStatus      | 确认变更状态：未触发/待处理/已确认                                   |
| AccountStatus      | 实施单结单状态：未结单/已结单（只能放SearchCondition，不能放ResultColumns） |
| PendingApprove     | 当前处理人                                                |
| IsCanDelay         | 是否可延期（1可延期0不可延期）                                     |

**请求体：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "121",
  "SchemaId": "change_info_read_only",
  "ReturnTotalRows": 1,
  "BussType": "变更",
  "Data": {
    "ResultColumns": {
      "ApprovalStatus": "",
      "BuildName": "",
      "ChangeLevel": "",
      "ChangeName": "",
      "ChangeOptions": "",
      "ChangePlanType": "",
      "ChangeReason": "",
      "ChangeReasonType": "",
      "ChangeSceneName": "",
      "ChangeScheme": "",
      "ChangeTheme": "",
      "ChangeType": "",
      "ChangesFocusPeople": "",
      "CompleteTime": "",
      "ConfirmStatus": "",
      "CreateTime": "",
      "Creator": "",
      "Draft": "",
      "EndNoticeMsg": "",
      "EventOrder": "",
      "Id": "",
      "Implementer": "",
      "InstanceId": "",
      "IsAuto": "",
      "IsCanDelay": "",
      "IsConfigAlarm": "",
      "IsConfigUser": "",
      "IsEvent": "",
      "IsOnsiteSupport": "",
      "IsReplenishment": "",
      "IsSendNotice": "",
      "IsWB": "",
      "Major": "",
      "MozuName": "",
      "NoticeMsg": "",
      "ParkName": "",
      "PendingApprove": "",
      "PingshenAdvice": "",
      "PingshenResult": "",
      "PlanEndTime": "",
      "PlanStartTime": "",
      "SchemeType": "",
      "TicketId": "",
      "WBOrder": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

## 变更子单

**建表语句：**

```sql
CREATE TABLE `change_child` (
  `c_id` varchar(255) DEFAULT NULL COMMENT '变更子单号',
  `m_id` varchar(255) DEFAULT NULL COMMENT '变更母单号',
  `implementer` varchar(255) DEFAULT NULL COMMENT '变更指挥人(实施人)',
  `plan_start_time` varchar(255) DEFAULT NULL COMMENT '计划开始时间',
  `plan_end_time` varchar(255) DEFAULT NULL COMMENT '计划结束时间',
  `real_start_time` varchar(255) DEFAULT NULL COMMENT '实际开始时间',
  `real_end_time` varchar(255) DEFAULT NULL COMMENT '实际结束时间',
  `created_time` varchar(255) DEFAULT NULL COMMENT '创建时间',
  `complete_time` varchar(255) NOT NULL COMMENT '结单时间',
  `impact_power_off` varchar(255) DEFAULT NULL COMMENT '影响电力中断',
  `impact_area_type` varchar(255) DEFAULT NULL COMMENT '影响范围类型',
  `rack` varchar(3000) DEFAULT '[]' COMMENT '机架',
  `l1_motor` varchar(3000) DEFAULT '[]' COMMENT '一级机房',
  `normal_impact` varchar(2000) DEFAULT NULL COMMENT '正常影响描述',
  `abnormal_impact` varchar(2000) DEFAULT NULL COMMENT '异常影响描述',
  `step_id` int(9) DEFAULT NULL COMMENT '步骤id',
  `status` varchar(255) DEFAULT '未执行' COMMENT '子单执行状态：未执行/执行中/执行结束',
  `id` int(9) NOT NULL AUTO_INCREMENT,
  `DeviceSonList` longtext COMMENT '设备信息',
  `motor_manage_unit` varchar(5000) DEFAULT '[]' COMMENT '机房管理单元',
  `canyu_name` varchar(300) DEFAULT NULL COMMENT '参与人',
  `changess_result` varchar(255) DEFAULT NULL COMMENT '变更结果：成功/失败/异常/取消',
  `change_summary` text COMMENT '变更结果说明',
  `summary_document` text COMMENT '变更总结(异常变更上传文档)',
  `confirm_status` varchar(10) DEFAULT '未触发' COMMENT '确认变更状态：未触发/待处理/已确认',
  `relate_wb_sub_instance_id` varchar(64) DEFAULT NULL COMMENT '关联维保流程子单流程号',
  `confirm_person` varchar(20) DEFAULT NULL COMMENT '确认变更状态用户',
  `carry_out_status` varchar(9) DEFAULT '未激活' COMMENT '实施状态：未激活/待实施/实施中/已完成',
  `account_status` varchar(9) DEFAULT '未结单' COMMENT '结单状态：未结单/已结单',
  `over_user` varchar(255) DEFAULT NULL COMMENT '结单人',
  `youhua_name` varchar(255) DEFAULT NULL COMMENT '优化参与人',
  `c_ticket_id` varchar(255) DEFAULT NULL COMMENT '实施单流程单号',
  `alarm_convergence_rate` varchar(50) DEFAULT '0' COMMENT '告警收敛率',
  `combined_interaction_rate` varchar(50) DEFAULT '0' COMMENT '线上线下互动率',
  `confirm_time` varchar(255) DEFAULT NULL COMMENT '服务台确认时间',
  `base_alarm_list` longtext COMMENT '变更结单时告警基线',
  `is_more_sop` varchar(9) DEFAULT '否' COMMENT '设备是否多sop',
  `apply_time` varchar(255) DEFAULT NULL COMMENT 'FOC申请时间',
  `over_plan_call` tinyint(4) DEFAULT '0' COMMENT '超计划时间未结束提醒（0默认1已提醒）',
  `alarm_excess_push` tinyint(4) DEFAULT '0' COMMENT '告警超预期推送（0默认1已提醒）',
  `single_key` varchar(255) DEFAULT '无' COMMENT '实施单基线标识',
  `problem_record` text COMMENT '问题记录',
  `abnormal_remarks` text NOT NULL COMMENT '告警数量异常备注',
  `is_charging` varchar(9) NOT NULL DEFAULT '否' COMMENT '是否计费',
  PRIMARY KEY (`id`),
  KEY `c_id` (`c_id`),
  KEY `m_id` (`m_id`),
  KEY `account_status` (`account_status`),
  KEY `real_start_time` (`real_start_time`),
  KEY `carry_out_status` (`carry_out_status`),
  KEY `idx_over_plan_call` (`over_plan_call`),
  KEY `index_complete_time` (`complete_time`) 
) ENGINE=InnoDB AUTO_INCREMENT=93927 DEFAULT CHARSET=utf8 COMMENT='变更子单信息'
```

| 字段名                  | 字段说明                 |
|----------------------|----------------------|
| CId                  | 变更子单号                |
| MId                  | 变更母单号                |
| Implementer          | 实施人                  |
| PlanStartTime        | 计划开始时间               |
| PlanEndTime          | 计划结束时间               |
| RealStartTime        | 实际开始时间               |
| RealEndTime          | 实际结束时间               |
| CreatedTime          | 创建时间                 |
| CompleteTime         | 结单时间                 |
| ImpactPowerOff       | 影响电力中断               |
| ImpactAreaType       | 影响范围类型               |
| Rack                 | 机架                   |
| L1Motor              | 一级机房                 |
| NormalImpact         | 正常影响描述               |
| AbnormalImpact       | 异常影响描述               |
| Status               | 子单执行状态：未执行/执行中/执行结束  |
| Id                   | Id                   |
| DeviceSonList        | 设备信息                 |
| MotorManageUnit      | 机房管理单元               |
| AlarmConvergenceRate | 告警收敛率                |
| CanyuName            | 参与人                  |
| ChangessResult       | 变更结果：成功/失败/异常/取消     |
| ChangeSummary        | 变更结果说明               |
| SummaryDocument      | 变更总结(异常变更上传文档)       |
| ConfirmStatus        | 确认变更状态：未触发/待处理/已确认   |
| ConfirmPerson        | 确认变更状态用户             |
| CarryOutStatus       | 实施状态：未激活/待实施/实施中/已完成 |
| AccountStatus        | 结单状态：未结单/已结单         |
| ChangeName           | 变更名称                 |
| ChangeLevel          | 变更级别                 |
| ParkName             | 园区名称                 |
| MozuName             | 模组名称                 |
| ApprovalStatus       | 审批状态                 |
| IsAuto               | 是否自动化                |
| CTicketId            | 实施单dcops单号           |
| ChangeSceneName      | 变更场景名称               |
| Creator              | 创建人                  |
| IsCharging           | 是否计费                 |
| IsMoreSop            | 是否多设备                |

**请求体：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "121",
  "SchemaId": "change_child_read_only",
  "ReturnTotalRows": 1,
  "BussType": "变更",
  "Data": {
    "ResultColumns": {
      "AbnormalImpact": "",
      "AccountStatus": "",
      "AlarmConvergenceRate": "",
      "ApprovalStatus": "",
      "CId": "",
      "CTicketId": "",
      "CanyuName": "",
      "CarryOutStatus": "",
      "ChangeLevel": "",
      "ChangeName": "",
      "ChangeSceneName": "",
      "ChangeSummary": "",
      "ChangessResult": "",
      "CompleteTime": "",
      "ConfirmPerson": "",
      "ConfirmStatus": "",
      "CreatedTime": "",
      "Creator": "",
      "DeviceSonList": "",
      "Id": "",
      "ImpactAreaType": "",
      "ImpactPowerOff": "",
      "Implementer": "",
      "IsAuto": "",
      "IsCharging": "",
      "IsMoreSop": "",
      "L1Motor": "",
      "MId": "",
      "MotorManageUnit": "",
      "MozuName": "",
      "NormalImpact": "",
      "ParkName": "",
      "PlanEndTime": "",
      "PlanStartTime": "",
      "Rack": "",
      "RealEndTime": "",
      "RealStartTime": "",
      "Status": "",
      "SummaryDocument": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

## 服务台事件工单查询接口

| 字段名                | 描述     | 数据类型   | 示例值                      | 是否支持条件查询 |
|--------------------|--------|--------|--------------------------|----------|
| EventTitle         | 事件标题   | string | 南京江宁模组42 IT方仓电池组放电4min以上 | 否        |
| ParkName           | 园区名称   | string | 南京-江宁                    | 是        |
| MozuName           | 模组名称   | string | 南京江宁模组42                 | 是        |
| Id                 | 事件单号   | string | E202512240553            | 是        |
| EventLevel         | 事件等级   | string | L0、L1、L2、L3、L4           | 是        |
| EventOrderStatus   | 事件工单状态 | string | 处理中、已结单、已挂起              | 是        |
| EventDescribe      | 事件描述   | string | 南京江宁模组42 IT方仓电池组放电4min以上 | 否        |
| EventSource        | 事件来源   | string | 现场反馈、监控告警···             | 是        |
| EventCreatedTime   | 事件创建时间 | string | 2025-12-24 10:47:29      | 是        |
| EventEndTime       | 事件结束时间 | string | 2025-12-24 10:47:29      | 是        |
| EventEndReason     | 事件结束原因 | string | 快速恢复-短时自复····            | 是        |
| EventCreatedBy     | 事件创建人  | string | 集中监控、用户ID-X              | 是        |
| IsWrongAlarm       | 是否误报   | string | 是/否                      | 是        |
| InstructionManual  | 备注说明   | string | 现场处理后恢复                  | 否        |
| IsChange           | 是否变更   | string | 是/否                      | 是        |
| IsOnSite           | 是否现场跟进 | string | 是/否                      | 是        |
| IsRepair           | 是否转维修  | string | 是/否                      | 是        |
| ManualRecoveryTime | 人工恢复时间 | string | 2025-12-24 10:47:29      | 否        |
| IsOverTime         | 是否超时   | string | 是/否                      | 是        |
| IsFirstLevelEvent  | 是否一级事件 | string | 是/否                      | 是        |
| EventTriggerReason | 事件触发原因 | string | XXXX                     | 否        |
| EventOccurTime     | 事件发生时间 | string | 2025-12-24 10:47:29      | 是        |
| ChangeMark         | 变更备注   | string | XXXX                     | 否        |
| OnSiteTodoUser     | 现场处理人  | string | 用户ID-X                   | 否        |

**请求体：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "121",
  "SchemaId": "event_work_order",
  "ReturnTotalRows": 1,
  "BussType": "事件",
  "Data": {
    "SearchCondition": {
      "ParkName": "清远-清新"
    },
    "ResultColumns": {
      "EventTitle": "",
      "ParkName": "",
      "MozuName": "",
      "Id": "",
      "EventLevel": "",
      "EventOrderStatus": "",
      "EventDescribe": "",
      "EventSource": "",
      "EventCreatedTime": "",
      "EventEndTime": "",
      "EventEndReason": "",
      "EventCreatedBy": "",
      "IsWrongAlarm": "",
      "InstructionManual": "",
      "IsChange": "",
      "IsOnSite": "",
      "IsRepair": "",
      "ManualRecoveryTime": "",
      "IsOverTime": "",
      "IsFirstLevelEvent": "",
      "EventTriggerReason": "",
      "EventOccurTime": "",
      "ChangeMark": "",
      "OnSiteTodoUser": ""
    },
    "Limit": {
      "Start": 0,
      "Size": 10
    }
  }
}
```

## 查询事件单工单的关联设备

| 参数名      | 类型   | 是否必填 | 描述                                          |
|----------|------|------|---------------------------------------------|
| EventIds | list | 是    | 工单号列表【必须大于1个单号、最大不能超过50个单号】 / 【必须用E开头的事件单号】 |

**请求示例：**

```json
{
  "SystemId": "121",
  "Action": "ServiceQueryData",
  "Method": "Run",
  "BussType": "事件",
  "SchemaId": "event_alarm_device_info",
  "AppendInfo": {
    "EventIds": [
      "E202512240553"
    ]
  },
  "Data": {
    "ResultColumns": {
      "DeviceList": ""
    }
  }
}
```

**请求成功：**

```json
{
  "Return": 0,
  "Details": "",
  "ReqId": "ifob-fredhbpo1sd7t85gq",
  "Data": {
    "E202512240553": [
      "NJ-JN-BD04-BTA304B-BTMM-ITBR01"
    ]
  }
}
```

**请求失败：**

```json
{
   "data": null,
  "msg": "事件单号不能为空",
  "status": 400
}
```

## DeviceDb-device_simple

| 字段名       | 字段说明                       |
|-----------|----------------------------|
| 设备编号      | DeviceNumber               |
| 逻辑系统      | SystemName                 |
| 应用类型      | ApplicationTypeName        |
| 设备类型      | DevicetypesName            |
| 标准化品牌     | DeviceBrand                |
| IDCDB型号   | DeviceModel                |
| 所属设备编号    | DeviceBelongDeviceNumber   |
| 设备状态      | DeviceStatus               |
| 设备初验时间    | DeviceActivationTime       |
| 设备种类      | DeviceCategory             |
| 展示编号      | DeviceNumberShow           |
| 所属应用类型名称  | BelongApplicationTypeName  |
| 所属专业      | DeviceBelongrofessionName  |
| 序号        | DeviceNo                   |
| 标牌生成数量    | AppLabelPasteMethod        |
| 区域编码      | IdcareaCode                |
| DeviceUid | DeviceUid                  |
| 设备生产日期    | DeviceProdDate             |
| 楼宇        | BuildingName               |
| 完整应用类型    | AppCompleteName            |
| 重要级别      | ApplicationTypeImportLevel |
| 设备名称      | DeviceName                 |
| IDCDB品牌   | BrandName                  |
| 标准化型号     | BrandModel                 |
| 整机部件      | CompleteMachine            |
| 模组        | MozuName                   |
| 是否监控      | IsMonitor                  |
| 维保服务状态    | MaintenanceServiceStatus   |
| 路由设备编号    | DeviceNumberRoute          |
| 所属房间      | RoomName                   |
| 设备终验时间    | DeviceFinalInspectionDate  |
| 方仓功能间     | FuncRoomName               |
| 区域名称      | IdcareaName                |
| 方仓功能间编码   | FuncRoomCode               |
| 设备型号id    | DeviceModelId              |
| 是否录入生产日期  | AppIsExtendPara            |

**请求体：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "Run",
  "SystemId": "121",
  "SchemaId": "device_simple",
  "ReturnTotalRows": 1,
  "BussType": "DeviceDb",
  "Data": {
    "ResultColumns": {
      "DeviceNumber": "",
      "SystemName": "",
      "ApplicationTypeName": "",
      "DevicetypesName": "",
      "DeviceBrand": "",
      "DeviceModel": "",
      "DeviceBelongDeviceNumber": "",
      "DeviceStatus": "",
      "DeviceActivationTime": "",
      "DeviceCategory": "",
      "DeviceNumberShow": "",
      "BelongApplicationTypeName": "",
      "DeviceBelongrofessionName": "",
      "DeviceNo": "",
      "AppLabelPasteMethod": "",
      "IdcareaCode": "",
      "DeviceUid": "",
      "DeviceProdDate": "",
      "BuildingName": "",
      "AppCompleteName": "",
      "ApplicationTypeImportLevel": "",
      "DeviceName": "",
      "BrandName": "",
      "BrandModel": "",
      "CompleteMachine": "",
      "MozuName": "",
      "IsMonitor": "",
      "MaintenanceServiceStatus": "",
      "DeviceNumberRoute": "",
      "RoomName": "",
      "DeviceFinalInspectionDate": "",
      "FuncRoomName": "",
      "FuncRoomCode": "",
      "DeviceModelId": "",
      "AppIsExtendPara": ""
    },
    "SearchCondition": {
      "DevicetypesName": "",
      "CampusName": "",
      "MozuName": "",
      "DeviceNumber": "",
      "ApplicationTypeName": ""
    },
    "Sorts": [],
    "Limit": {
      "Size": 10,
      "Start": 0
    }
  }
}
```

## 维护租赁主单表

**表结构：**

```sql
CREATE TABLE `simple_main_ticket` (
  `ticket_id` varchar(15) NOT NULL,
  `instance_id` varchar(40) DEFAULT NULL,
  `create_time` varchar(20) DEFAULT NULL COMMENT '建单时间',
  `end_time` varchar(20) DEFAULT NULL COMMENT '结束时间',
  `ticket_status` varchar(9) DEFAULT NULL COMMENT '工单状态',
  `region_name` varchar(9) DEFAULT NULL COMMENT '区域',
  `campus_name` varchar(20) DEFAULT NULL COMMENT '园区',
  `mozu_name` varchar(50) DEFAULT NULL COMMENT '模组名称',
  `devicetypes_name` varchar(100) DEFAULT NULL COMMENT '设备类型',
  `maintenance_cycle` varchar(9) DEFAULT NULL COMMENT '维护周期',
  `template_selection` varchar(9) DEFAULT NULL COMMENT '模板选择',
  `ticket_mode` varchar(9) DEFAULT NULL COMMENT '派单模式',
  `to_change` varchar(9) DEFAULT NULL COMMENT '是否有变更',
  `change_id` varchar(1000) DEFAULT NULL COMMENT '变更单号',
  `plan_start_date` varchar(10) DEFAULT NULL COMMENT '计划开始日期',
  `plan_end_date` varchar(10) DEFAULT NULL COMMENT '计划结束日期',
  `audit_results` varchar(6) DEFAULT NULL COMMENT '审核结果',
  `once_pass` varchar(9) DEFAULT NULL COMMENT '一次通过',
  `fmc_refusal` varchar(200) DEFAULT NULL COMMENT 'fmc驳回意见',
  `re_type` varchar(9) DEFAULT NULL COMMENT 'fmc驳回类别',
  `fmc_user` varchar(20) DEFAULT NULL COMMENT 'fmc审核人',
  `maintenance_plan` varchar(200) DEFAULT NULL COMMENT '维护模板',
  `status_light` varchar(9) DEFAULT NULL COMMENT '状态灯',
  `eg_opinion` varchar(1000) DEFAULT NULL COMMENT '工程师审核意见',
  `reason` varchar(1000) DEFAULT NULL COMMENT '延期取消原因',
  `original_ticket_id` varchar(20) DEFAULT NULL COMMENT '原工单号',
  `eg_pass` varchar(20) DEFAULT '是' COMMENT '工程师通过',
  PRIMARY KEY (`ticket_id`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "120",
  "SchemaId": "simple_main_ticket",
  "ReturnTotalRows": 1,
  "BussType": "维护",
  "Data": {
    "ResultColumns": {
      "AuditResults": "",
      "CampusName": "",
      "ChangeId": "",
      "CreateTime": "",
      "DevicetypesName": "",
      "EgOpinion": "",
      "EgPass": "",
      "EndTime": "",
      "FmcRefusal": "",
      "FmcUser": "",
      "InstanceId": "",
      "MaintenanceCycle": "",
      "MaintenancePlan": "",
      "MozuName": "",
      "OncePass": "",
      "OriginalTicketId": "",
      "PlanEndDate": "",
      "PlanStartDate": "",
      "ReType": "",
      "Reason": "",
      "RegionName": "",
      "StatusLight": "",
      "TemplateSelection": "",
      "TicketId": "",
      "TicketMode": "",
      "TicketStatus": "",
      "ToChange": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
  "Data": {
    "List": [
      {
        "AuditResults": "",
        "CampusName": "上海-宝信",
        "ChangeId": "",
        "CreateTime": "2025-04-24 00:00:28",
        "DevicetypesName": "水冷精密空调",
        "EgOpinion": "",
        "EgPass": "",
        "EndTime": "",
        "FmcRefusal": "",
        "FmcUser": "",
        "InstanceId": "3700000003138",
        "MaintenanceCycle": "季度",
        "MaintenancePlan": "",
        "MozuName": "上海宝信模组01",
        "OncePass": "是",
        "OriginalTicketId": "",
        "PlanEndDate": "2025-06-30",
        "PlanStartDate": "2025-05-01",
        "ReType": "",
        "Reason": "",
        "RegionName": "华东",
        "StatusLight": "",
        "TemplateSelection": "",
        "TicketId": "10250424001031",
        "TicketMode": "",
        "TicketStatus": "处理中",
        "ToChange": ""
      }
    ],
    "TotalRows": 0
  },
  "Details": "",
  "Return": 0
}
```

## 维护租赁子单表

**表结构：**

```sql
CREATE TABLE `simple_sub_ticket` (
  `ticket_id` varchar(15) NOT NULL,
  `instance_id` varchar(40) DEFAULT NULL,
  `task_id` varchar(40) DEFAULT NULL COMMENT '任务id',
  `main_ticket_id` varchar(15) DEFAULT NULL COMMENT '主单id',
  `title` varchar(100) DEFAULT NULL COMMENT '标题',
  `device_number` text COMMENT '设备编号',
  `create_time` varchar(20) DEFAULT NULL COMMENT '建单时间',
  `end_time` varchar(20) DEFAULT NULL COMMENT '结束时间',
  `ticket_status` varchar(9) DEFAULT NULL COMMENT '工单状态',
  `campus_name` varchar(20) DEFAULT NULL COMMENT '园区名称',
  `mozu_name` varchar(50) DEFAULT NULL COMMENT '模组名称',
  `devicetypes_name` varchar(100) DEFAULT NULL COMMENT '设备类型',
  `maintenance_cycle` varchar(9) DEFAULT NULL COMMENT '维护周期',
  `end_file` json DEFAULT NULL COMMENT '维护文档',
  `status_light` varchar(2) DEFAULT NULL COMMENT '状态灯',
  `confirm_results` varchar(4) DEFAULT '正常' COMMENT '维护结果',
  `confirm_summary` varchar(1000) DEFAULT NULL COMMENT '维护总结',
  `ticket_type` varchar(9) DEFAULT NULL COMMENT '工单类型',
  `p_ticket_id` varchar(1000) DEFAULT NULL COMMENT '问题跟进单单号',
  `p_ins_id` varchar(1000) DEFAULT NULL COMMENT '问题跟进单流程单号',
  `abnormal_check_count` varchar(9) DEFAULT NULL COMMENT '异常灯数量',
  `deal_user` varchar(500) DEFAULT NULL COMMENT '处理人',
  `face_photo` varchar(500) DEFAULT NULL COMMENT '人脸识别照片',
  `face_user` varchar(500) DEFAULT NULL COMMENT '人脸识别对象',
  `face_detect_result` varchar(500) DEFAULT NULL COMMENT '识别结果',
  `face_detail` varchar(500) DEFAULT NULL COMMENT '识别详情',
  `upload_face_photo_time` varchar(500) DEFAULT NULL COMMENT '上传人脸识别照片时间',
  `upload_photo_time` varchar(500) DEFAULT NULL COMMENT '上传照片时间',
  `audit_results` varchar(200) DEFAULT NULL COMMENT '审核结果',
  `audit_reason` varchar(200) DEFAULT NULL COMMENT '审核原因',
  `device_name` varchar(200) DEFAULT NULL COMMENT '设备名称',
  `reason` varchar(1000) DEFAULT NULL COMMENT '延期取消原因',
  PRIMARY KEY (`ticket_id`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "120",
  "SchemaId": "simple_sub_ticket",
  "ReturnTotalRows": 1,
  "BussType": "维护",
  "Data": {
    "ResultColumns": {
      "AbnormalCheckCount": "",
      "AuditReason": "",
      "AuditResults": "",
      "CampusName": "",
      "ConfirmResults": "",
      "ConfirmSummary": "",
      "CreateTime": "",
      "DealUser": "",
      "DeviceName": "",
      "DeviceNumber": "",
      "DevicetypesName": "",
      "EndFile": "",
      "EndTime": "",
      "FaceDetail": "",
      "FaceDetectResult": "",
      "FacePhoto": "",
      "FaceUser": "",
      "InstanceId": "",
      "MainTicketId": "",
      "MaintenanceCycle": "",
      "MaintenancePlan": "",
      "MozuName": "",
      "PInsId": "",
      "PTicketId": "",
      "Reason": "",
      "ScanCodeResult": "",
      "StatusLight": "",
      "TaskId": "",
      "TicketId": "",
      "TicketStatus": "",
      "TicketType": "",
      "TimeAbnormalReason": "",
      "TimeAudit": "",
      "TimeConsuming": "",
      "Title": "",
      "UploadFacePhotoTime": "",
      "UploadPhotoTime": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
   "Data": {
     "List": [
       {
         "AbnormalCheckCount": "",
        "AuditReason": "",
        "AuditResults": "",
        "CampusName": "上海-宝信",
        "ConfirmResults": "正常",
        "ConfirmSummary": "2455435",
        "CreateTime": "2025-05-29 17:01:23",
        "DealUser": "",
        "DeviceName": "",
        "DeviceNumber": "SH-BX-BD3-C151-CST-05;SH-BX-BD3-C151-CST-06;SH-BX-BD3-C151-CST-07;SH-BX-BD3-C151-CST-08",
        "DevicetypesName": "蓄冷罐",
        "EndFile": "[{\"raw\": {\"uid\": 1748509291766}, \"uid\": 1748509291766, \"name\": \"7441c216a94d29fcd97f76fda453028.jpg\", \"size\": 264573, \"status\": \"success\", \"response\": {\"FileList\": [{\"uid\": 1748509291968, \"url\": \"https: \", \"name\": \"7441c216a94d29fcd97f76fda453028.jpg\", \"status\": \"success\"}]}, \"percentage\": 100}]",
        "EndTime": "2025-05-29 17:01:34",
        "FaceDetail": "",
        "FaceDetectResult": "",
        "FacePhoto": "",
        "FaceUser": "",
        "InstanceId": "3700000003718",
        "MainTicketId": "10250502000458",
        "MaintenanceCycle": "年度",
        "MaintenancePlan": "",
        "MozuName": "上海宝信模组01",
        "PInsId": "",
        "PTicketId": "",
        "Reason": "",
        "ScanCodeResult": "",
        "StatusLight": "绿",
        "TaskId": "",
        "TicketId": "10250502000458",
        "TicketStatus": "已完成",
        "TicketType": "通用",
        "TimeAbnormalReason": "",
        "TimeAudit": "",
        "TimeConsuming": "",
        "Title": "上海宝信模组01-蓄冷罐-年度预防性维护计划",
        "UploadFacePhotoTime": "",
        "UploadPhotoTime": "" 
      } 
    ],
    "TotalRows": 0 
  },
  "Details": "",
  "Return": 0
}
```

## 人员能力评估考核数据

**表结构：**

```sql
CREATE TABLE `emp_competency_assessment_1` (
  `id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'id主键',
  `campus` longtext NOT NULL COMMENT '园区',
  `mozu` longtext NOT NULL COMMENT '模组',
  `keji_account` varchar(255) NOT NULL COMMENT '账号',
  `keji_user_name` varchar(255) NOT NULL COMMENT '姓名',
  `object` varchar(255) NOT NULL COMMENT '岗位',
  `exam_start_date` varchar(100) NOT NULL COMMENT '考试开始时间',
  `exam_end_date` varchar(100) NOT NULL COMMENT '考试结束时间',
  `ability_scores` json NOT NULL COMMENT '各项能力分数',
  `total_score` float NOT NULL COMMENT '总分',
  `exam_line` longtext NOT NULL COMMENT '考试链接',
  `student_status` varchar(9) NOT NULL COMMENT '学员状态',
  `exam_name` varchar(255) NOT NULL COMMENT '培训名称',
  `fac_pm` longtext NOT NULL COMMENT '项目经理',
  `t_idc_asst_manager` longtext NOT NULL COMMENT '数经',
  PRIMARY KEY (`id`) 
) ENGINE=InnoDB AUTO_INCREMENT=2445 DEFAULT CHARSET=utf8 COMMENT='人员能力评估考核数据第二版'
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "121",
  "SchemaId": "emp_competency_assessment_1",
  "ReturnTotalRows": 1,
  "BussType": "培训",
  "Data": {
    "ResultColumns": {
      "AbilityScores": "",
      "Campus": "",
      "ExamEndDate": "",
      "ExamLine": "",
      "ExamName": "",
      "ExamStartDate": "",
      "FacPm": "",
      "Id": "",
      "KejiAccount": "",
      "KejiUserName": "",
      "Mozu": "",
      "Object": "",
      "StudentStatus": "",
      "TIdcAsstManager": "",
      "TotalScore": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**返回示例：**

```json
{
   "Return": 0,
  "Details": "",
  "ReqId": "ifob-fredhrxjpetj7kljk",
  "Data": {
     "List": [
       {
         "AbilityScores": "{\"体系流程\": 15, \"实操调试\": 15, \"应急处置\": 15, \"技术理论\": 12, \"系统架构\": 15, \"职业素养\": 9, \"运维标准\": 15}",
        "Campus": "张家口-怀来瑞北",
        "ExamEndDate": "2025-12-30 23:59:59",
        "ExamLine": "https://lexiangla.com/teams/k100040/exams/cd723092df0311f08c934aeede3c9b0e?company_from=d1d3930a1b5d11e8a4495254005b9a60",
        "ExamName": "能力评估-应急工程师TB机房",
        "ExamStartDate": "2025-12-23 18:00:00",
        "FacPm": "kewenjie",
        "Id": 2396,
        "KejiAccount": "renxiaojie",
        "KejiUserName": "v_ixjren(任晓捷)",
        "Mozu": "张家口怀来瑞北模组61,张家口怀来瑞北模组62",
        "Object": "应急工程师",
        "StudentStatus": "必修",
        "TIdcAsstManager": "mikexdhuang;zhongyshen",
        "TotalScore": 96 
      } 
    ], "TotalRows": 22 
  }
}
```

## 服务认证考核

**表结构：**

```sql
CREATE TABLE `service_training_results_record` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `scope_of_training` varchar(255) DEFAULT NULL COMMENT '培训范围',
  `training_category` varchar(100) DEFAULT NULL COMMENT '培训分类',
  `training_name` varchar(255) DEFAULT NULL COMMENT '培训名称',
  `training_date` varchar(255) DEFAULT NULL COMMENT '培训日期',
  `trainee` varchar(255) DEFAULT NULL COMMENT '培训人员',
  `chinese_name` varchar(255) DEFAULT NULL COMMENT '中文名',
  `service_company` varchar(255) DEFAULT NULL COMMENT '服务商公司',
  `campus_name` varchar(255) DEFAULT NULL COMMENT '园区',
  `mozu_name` varchar(255) DEFAULT NULL COMMENT '模组',
  `job_title` varchar(255) DEFAULT NULL COMMENT '岗位',
  `learning_status` varchar(255) DEFAULT NULL COMMENT '学习状态',
  `learning_time` varchar(255) DEFAULT NULL COMMENT '学习时长',
  `standard_time` varchar(255) DEFAULT NULL COMMENT '标准培训时长',
  `participate_progress` varchar(255) DEFAULT NULL COMMENT '课程参与时长百分比',
  `need_to_check` varchar(255) DEFAULT NULL COMMENT '是否有考核',
  `exam_required` varchar(255) DEFAULT NULL COMMENT '是否为必考人员',
  `assessment_date` varchar(255) DEFAULT NULL COMMENT '考核日期',
  `check_status` varchar(255) DEFAULT NULL COMMENT '考核状态',
  `assessment_score` varchar(255) DEFAULT NULL COMMENT '考核分数',
  `update_time` varchar(255) DEFAULT NULL COMMENT '成绩录入时间',
  `exam_pass` varchar(255) DEFAULT NULL COMMENT '考核是否通过',
  `remake_score` varchar(255) DEFAULT NULL COMMENT '补考分数',
  `remake_date` varchar(255) DEFAULT NULL COMMENT '补考日期',
  `pass_certification` varchar(255) DEFAULT '未审批' COMMENT '是否通过认证',
  `certification_date` varchar(255) DEFAULT NULL COMMENT '认证时间',
  `assessment_ticket_id` varchar(20) DEFAULT NULL COMMENT '培训实施工单',
  PRIMARY KEY (`id`) USING BTREE 
) ENGINE=InnoDB AUTO_INCREMENT=8302 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC
```

**请求示例：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "121",
  "SchemaId": "service_training_results_record",
  "ReturnTotalRows": 1,
  "BussType": "培训",
  "Data": {
    "ResultColumns": {
      "AssessmentDate": "",
      "AssessmentScore": "",
      "AssessmentTicketId": "",
      "CampusName": "",
      "CertificationDate": "",
      "CheckStatus": "",
      "ChineseName": "",
      "ExamPass": "",
      "ExamRequired": "",
      "Id": "",
      "JobTitle": "",
      "LearningStatus": "",
      "LearningTime": "",
      "MozuName": "",
      "NeedToCheck": "",
      "ParticipateProgress": "",
      "PassCertification": "",
      "RemakeDate": "",
      "RemakeScore": "",
      "ScopeOfTraining": "",
      "ServiceCompany": "",
      "StandardTime": "",
      "Trainee": "",
      "TrainingCategory": "",
      "TrainingDate": "",
      "TrainingName": "",
      "UpdateTime": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**响应数据：**

```json
{
   "Return": 0,
  "Details": "",
  "ReqId": "ifob-fredhsivpirxnc297",
  "Data": {
     "List": [
       {
         "AssessmentDate": "2024-06-07 09:00:00",
        "AssessmentScore": "90",
        "AssessmentTicketId": "",
        "CampusName": "",
        "CertificationDate": "",
        "CheckStatus": "已完成",
        "ChineseName": "Fo_严汉鸿",
        "ExamPass": "通过",
        "ExamRequired": "必考人员",
        "Id": 6841,
        "JobTitle": "维护服务工程师",
        "LearningStatus": "已完成",
        "LearningTime": "01:05:34",
        "MozuName": "",
        "NeedToCheck": "是",
        "ParticipateProgress": "54.2%",
        "PassCertification": "未审批",
        "RemakeDate": "",
        "RemakeScore": "",
        "ScopeOfTraining": "服务商培训",
        "ServiceCompany": "鸿云",
        "StandardTime": "120",
        "Trainee": "yanhanhong",
        "TrainingCategory": "服务商年度培训",
        "TrainingDate": "2024-06-07 08:00:00",
        "TrainingName": "Dcops维护线上化培训",
        "UpdateTime": "2024-06-07 10:23:02" 
      }],
      "TotalRows": 122
    }
}
```

## 运维培训数据

**表结构：**

```sql
CREATE TABLE `training_results_record` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `scope_of_training` varchar(255) DEFAULT NULL COMMENT '培训范围',
  `training_name` varchar(255) DEFAULT NULL COMMENT '培训名称',
  `training_date` varchar(255) DEFAULT NULL COMMENT '培训日期',
  `trainee` varchar(255) DEFAULT NULL COMMENT '培训人员',
  `chinese_name` varchar(255) DEFAULT NULL COMMENT '中文名',
  `campus_name` varchar(255) DEFAULT NULL COMMENT '园区',
  `mozu_name` varchar(2000) DEFAULT NULL COMMENT '模组',
  `job_title` varchar(255) DEFAULT NULL COMMENT '岗位',
  `learning_status` varchar(255) DEFAULT NULL COMMENT '学习状态',
  `learning_time` varchar(255) DEFAULT NULL COMMENT '学习时长',
  `standard_time` varchar(255) DEFAULT NULL COMMENT '标准培训时长',
  `participate_progress` varchar(255) DEFAULT NULL COMMENT '课程参与时长百分比',
  `need_to_check` varchar(255) DEFAULT NULL COMMENT '是否有考核',
  `exam_required` varchar(255) DEFAULT NULL COMMENT '是否为必考人员',
  `assessment_date` varchar(255) DEFAULT NULL COMMENT '考核日期',
  `check_status` varchar(255) DEFAULT NULL COMMENT '考核状态',
  `assessment_score` varchar(255) DEFAULT NULL COMMENT '考核分数',
  `update_time` varchar(255) DEFAULT NULL COMMENT '成绩录入时间',
  `exam_pass` varchar(255) DEFAULT NULL COMMENT '考核是否通过',
  `remake_score` varchar(255) DEFAULT NULL COMMENT '补考分数',
  `remake_date` varchar(255) DEFAULT NULL COMMENT '补考日期',
  `training_category` varchar(255) DEFAULT NULL COMMENT '培训分类',
  `assessment_ticket_id` varchar(20) DEFAULT NULL COMMENT '工单实施单号',
  PRIMARY KEY (`id`) USING BTREE 
) ENGINE=InnoDB AUTO_INCREMENT=50571 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC
```

**请求体：**

```json
{
  "Action": "ServiceQueryData",
  "Method": "run",
  "SystemId": "121",
  "SchemaId": "training_results_record",
  "ReturnTotalRows": 1,
  "BussType": "培训",
  "Data": {
    "ResultColumns": {
      "AssessmentDate": "",
      "AssessmentScore": "",
      "AssessmentTicketId": "",
      "CampusName": "",
      "CheckStatus": "",
      "ChineseName": "",
      "ExamPass": "",
      "ExamRequired": "",
      "Id": "",
      "JobTitle": "",
      "LearningStatus": "",
      "LearningTime": "",
      "MozuName": "",
      "NeedToCheck": "",
      "ParticipateProgress": "",
      "RemakeDate": "",
      "RemakeScore": "",
      "ScopeOfTraining": "",
      "StandardTime": "",
      "Trainee": "",
      "TrainingCategory": "",
      "TrainingDate": "",
      "TrainingName": "",
      "UpdateTime": ""
    },
    "SearchCondition": {},
    "Sorts": [],
    "Limit": {
      "Size": 20,
      "Start": 0
    }
  }
}
```

**响应体：**

```json
{
  "Return": 0,
  "Details": "",
  "ReqId": "ifob-fredhsj3zr6ur3e7b",
  "Data": {
    "List": [
      {
        "AssessmentDate": "2024-08-19 09:00:00",
        "AssessmentScore": "",
        "AssessmentTicketId": "",
        "CampusName": "张家口-怀来瑞北",
        "CheckStatus": "缺考",
        "ChineseName": "",
        "ExamPass": "",
        "ExamRequired": "必考人员",
        "Id": 36,
        "JobTitle": "现场值班长",
        "LearningStatus": "缺勤",
        "LearningTime": "",
        "MozuName": "张家口怀来瑞北模组61,张家口怀来瑞北模组62",
        "NeedToCheck": "是",
        "ParticipateProgress": "",
        "RemakeDate": "",
        "RemakeScore": "",
        "ScopeOfTraining": "全国",
        "StandardTime": "",
        "Trainee": "v_dcbwu",
        "TrainingCategory": "",
        "TrainingDate": "2023-08-23 14:00:00",
        "TrainingName": "数据中心逆变器系统培训",
        "UpdateTime": ""
      }
    ],
    "TotalRows": 1580
  }
}
```
