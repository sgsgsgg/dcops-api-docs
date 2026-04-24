# DCOPS 运维平台 API 文档

本项目是 DCOPS（Data Center Operations）运维平台的 OpenAPI 3.0 规范文档，使用 Swagger UI 提供在线交互式 API 文档查看。

## 📖 项目简介

DCOPS 运维平台对外开放 API 接口，支持以下业务场景：

- **巡检管理**：巡检工单查询、数据统计
- **维护管理**：维护主表、子表工单查询
- **维修管理**：维修工单查询与管理
- **演练管理**：演练计划创建、更新、查询及结果提交
- **服务改善**：服务质量改进、整改措施反馈
- **人员管理**：在库服务人员信息查询
- **通用查询**:租赁机房服务工单通用查询

## 🚀 快速开始

### 在线访问

本文档已部署到 GitHub Pages，可直接访问：

**[https://sgsgsgg.github.io/dcops-api-docs/](https://sgsgsgg.github.io/dcops-api-docs/)**

### 本地运行

1. 克隆仓库

```bash
git clone https://github.com/sgsgsgg/dcops-api-docs.git
cd dcops-api-docs
```

2. 使用任意 HTTP 服务器启动

```bash
# 方式一：使用 Python 内置服务器
python3 -m http.server 8080

# 方式二：使用 Node.js http-server
npx http-server -p 8080

# 方式三：使用 PHP 内置服务器
php -S localhost:8080
```

3. 在浏览器中打开：

```
http://localhost:8080
```

## 📁 文件说明

```
dcops-api-docs/
├── openapi.json                       # OpenAPI 3.0 规范文件（用于 Swagger UI 文档展示）
├── postman_collection.json            # ⭐ Postman Collection v2.1（推荐导入 Apifox / Postman）
├── postman_environment_test.json      # 测试环境变量
├── postman_environment_prod.json      # 正式环境变量
├── API.md                             # 接口说明 Markdown 版
├── index.html                         # Swagger UI 页面
└── README.md                          # 项目说明文档
```

## ⭐ 推荐：导入 Apifox / Postman（支持 HMAC 自动签名）

`openapi.json` 适合在线文档浏览，但 OpenAPI 3.0 规范**不支持前置脚本**，无法自动生成 HMAC-SHA-512 签名。
为此本仓库额外提供了 **Postman Collection v2.1**，导入后即可自动签名。

### 1. 导入 Collection + Environment

| 客户端 | 操作路径 |
|---|---|
| **Apifox** | `项目设置 → 数据源 → 导入 → Postman → 选择 postman_collection.json` |
| **Postman** | `Import → File → 选择 postman_collection.json 和两个 environment.json` |

两个环境文件任选其一（或两个都导入，在顶部下拉切换）：
- `postman_environment_test.json`（测试环境 `https://dcops.test.tencent.com/relay/open/ifob/rapi`）
- `postman_environment_prod.json`（正式环境 `https://dcops.idc.tencent.com/relay/open/ifob/rapi`）

### 2. 填入 `appkey`

打开环境变量，把 `appkey` 填成管理员分配的 **HEX 字符串**；`SystemId` 默认 `121`，按需调整。

### 3. 选中环境后发送任意请求

Collection 级别的 **Pre-request Script** 会自动：
1. 从环境读 `appkey` / `SystemId`
2. 用 `HmacSHA512(Timestamp + RequestBody, hexDecode(appkey))` 计算签名
3. 把完整 Authorization 值写入环境变量 `authorization`
4. 每个 request 的 header `Authorization: {{authorization}}` 引用它

### 4. Collection 结构

共 **13 个文件夹 / 45 个接口**，严格按 BussType 分组：

| 文件夹 | 接口数 | 文件夹 | 接口数 |
|---|---|---|---|
| 巡检管理 | 1 | 设备管理 | 1 |
| 维护管理 | 4 | 机架开关电 | 3 |
| 维修管理 | 4 | 安全管理 | 4 |
| 变更管理 | 2 | 服务改善 | 2 |
| 事件管理 | 2 | 人员招聘 | 5 |
| 演练管理 | 12 | 培训管理 | 3 |
| 通用查询 | 2 | | |


## 🔗 API 端点

### 测试环境
```
https://dcops.test.tencent.com/relay/open/ifob/rapi/ifob-fre
```

### 正式环境
```
https://dcops.idc.tencent.com/relay/open/ifob/rapi/ifob-fre
```

## 📝 主要接口分类

所有 45 个接口共用同一个 URL：`{baseUrl}/ifob-fre`（测试或正式环境），
业务由请求 body 的 `Action` / `SchemaId` / `BussType` 字段路由，详见 Postman Collection 或 `openapi.json`。

业务分组（与 Postman Collection 文件夹一致）：

- **巡检管理**（1）：巡检主表
- **维护管理**（4）：维护主表 / 维护子表 / 维护租赁主单表 / 维护租赁子单表
- **维修管理**（4）：维修主表 / 【新】维修主表 / 维修事件处理流程节点操作日志（旧）/（新）
- **变更管理**（2）：变更母单 / 变更子单
- **事件管理**（2）：事件工单 / 事件告警设备信息
- **演练管理**（12）：方案/步骤/日历/工单/结果 5 个查询 + 计划/工单创建 + 附件下载
- **设备管理**（1）：设备基础信息
- **机架开关电**（3）：创建 / 批量创建 / 查询
- **安全管理**（4）：人员进出建单/查询 + 物资进出建单/查询
- **服务改善**（2）：整改措施反馈 / 整改落地反馈
- **人员招聘**（5）：招聘需求 / 招聘信息 / 简历上传 / 面试反馈 / 入职反馈
- **培训管理**（3）：员工能力评估 / 服务认证考核 / 运维培训记录
- **通用查询**（2）：在库服务人员 / 租赁机房通用服务工单

## 🔐 鉴权说明

所有接口均需要鉴权，详情请联系：

- **鉴权相关**: 罗俊杰、刘宇
- **接口请求**: 林小龙、周逸博、黄政声
- **业务数据**: 
  - 巡检：黄政声、林小龙、林俊基、李思泽
  - 维护：卫俊瑶、方国亮、许国彪、王鹏
  - 演练：冼振星
  - 变更：付鹏磊

## 📊 数据查询条件说明

所有查询接口支持以下查询形式：

### 1. 精确查询 (=)
```json
{
  "SearchCondition": {
    "SvrId": "123456"
  }
}
```

### 2. 集合查询 (IN)
```json
{
  "SearchCondition": {
    "SN": ["NC00168019", "CA1G041140"]
  }
}
```

### 3. 范围查询 (<, <=, >, >=)
支持的范围指令：`gt`(大于)、`gte`(大于等于)、`lt`(小于)、`lte`(小于等于)

```json
{
  "SearchCondition": {
    "CreateTime": {
      "gt": "2018-02-01 10:29:30",
      "lt": "2018-02-02 10:29:30"
    }
  }
}
```

## 🛠️ 技术栈

- **OpenAPI 3.0** - API 规范标准
- **Swagger UI 5.x** - 交互式 API 文档
- **GitHub Pages** - 文档托管平台

## 📮 联系方式

如有问题或建议，请联系 DCOPS 运维平台团队。

## 📄 许可证

本项目仅供内部使用，版权归腾讯所有。

---

**最后更新**: 2026-04-23

**版本**: v1.0.0
