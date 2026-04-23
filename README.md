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
├── openapi.json    # OpenAPI 3.0 规范文件
├── index.html      # Swagger UI 页面
└── README.md       # 项目说明文档
```

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

### 1. 巡检管理 (Inspection)
- `POST /inspection/main/query` - 查询巡检主表

### 2. 维护管理 (Maintenance)
- `POST /maintenance/main/query` - 查询维护主表
- `POST /maintenance/sub/query` - 查询维护子表

### 3. 维修管理 (Repair)
- `POST /repair/main/query` - 查询维修主表

### 4. 演练管理 (Drill)
- `POST /drill/plan/create` - 添加演练计划
- `POST /drill/plan/update` - 更新演练计划
- `POST /drill/plan/query` - 查询演练计划
- `POST /drill/result/submit` - 提交演练结果
- `POST /drill/result/get` - 获取演练结果
- `POST /drill/ticket/query` - 查询演练工单

### 5. 服务改善 (Service Improvement)
- `POST /service/improve/feedback` - 整改措施反馈
- `POST /service/improve/create` - 服务商侧建单
- `POST /service/improve/implement-feedback` - 整改落地详情反馈

### 6. 人员管理 (Personnel)
- `POST /service/person/query` - 查询在库服务人员信息

### 7. 通用查询 (Common Query)
- `POST /service/common-ticket/query` - 租赁机房服务工单通用查询

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
