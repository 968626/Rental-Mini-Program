# 房产中介租房小程序（house）
# 源码获取：https://mbd.pub/o/bread/YZaWk51qbQ==

基于 **微信小程序 + Spring Boot + MySQL** 的房产中介系统，包含面向用户的租房/二手房小程序、面向管理端的后台管理系统（Spring Security 权限控制），可作为毕业设计或二次开发的完整示例。

## 功能特性

### 用户端（微信小程序）

- 首页：新房 / 二手房 / 租房入口、最新房源推荐、房产资讯
- 房源列表与搜索：按类型切换新房、二手房、租房，关键词搜索
- 房源详情：图片、价格、面积、楼层、地址等信息
- 委托发布：我要卖房、我要出租两类委托表单，支持查看与取消我的委托
- 订单：订单列表、支出订单、收入明细
- 用户中心：登录 / 注册、个人资料、修改密码

### 管理端（Web 后台，「房屋中介后台」）

- Spring Security 登录鉴权 + 角色权限（RBAC：`sys_user` / `sys_role` / `sys_permission`）
- 房源管理：新房、二手房、租房的增删改查与条件查询
- 房源配置：城市、装修风格、出售出租类别字典维护
- 用户管理：员工管理（分配角色、重置密码）、客源管理（禁用/启用）
- 委托管理：出售委托、出租委托的接单与删除
- 订单管理：按状态（未付款/已付款/已取消）、类型筛选，查看详情与取消
- 系统管理：菜单管理、权限管理、角色管理、Druid 数据源监控（`/druid/**`，仅 ADMIN 访问）
- 数据分析：城市员工年度销售额、城市年度销售额、员工每月销售额等图表统计

## 技术栈

| 层次 | 技术 |
| --- | --- |
| 小程序端 | 微信原生小程序（WXML / WXSS / JS） |
| 后端框架 | Spring Boot 2.3.7 |
| 安全框架 | Spring Security（含 Thymeleaf 整合） |
| 持久层 | MyBatis + PageHelper 分页 |
| 数据库 | MySQL 5.7+，Druid 连接池 |
| 视图层 | Thymeleaf（后台管理页面） |
| 构建工具 | Maven（JDK 1.8） |

## 项目结构

```
house-master/
├── house/                 # 微信小程序前端
│   ├── pages/             # 页面（首页、房源、委托、订单、用户中心等）
│   ├── components/        # 自定义组件
│   ├── request/           # 网络请求封装（统一 baseUrl）
│   ├── icons/、styles/、utils/
│   └── app.json           # 小程序全局配置（tabBar、页面注册）
├── house-agency/          # Spring Boot 后端 + 管理后台
│   └── src/main/
│       ├── java/com/five/
│       │   ├── controller/    # 接口层（含 controller/customer 小程序端接口）
│       │   ├── service/       # 业务层
│       │   ├── mapper/        # MyBatis Mapper 接口
│       │   ├── domain/、vo/   # 实体与视图对象
│       │   ├── config/        # Web、Security、Druid 配置
│       │   └── common/        # 通用工具、文件上传
│       └── resources/
│           ├── application.yml    # 数据源、端口等配置
│           ├── mapper/            # MyBatis XML
│           └── templates/         # 后台管理页面（Thymeleaf）
├── sql/house.sql          # 数据库建库建表脚本（含初始数据）
├── 截图/                   # 运行效果图
└── 租房小程序.mp4           # 演示视频
```

## 环境要求

- JDK 1.8
- Maven 3.x（或使用项目自带 `mvnw`）
- MySQL 5.7+
- Redis（`spring-boot-starter-data-redis` 依赖已引入，默认 localhost:6379）
- 微信开发者工具

## 快速开始

### 1. 初始化数据库

创建数据库并导入脚本：

```bash
mysql -uroot -p -e "CREATE DATABASE house DEFAULT CHARACTER SET utf8mb4;"
mysql -uroot -p house < sql/house.sql
```

### 2. 修改后端配置

编辑 [house-agency/src/main/resources/application.yml](house-agency/src/main/resources/application.yml)，按本地环境修改数据库账号密码（默认 `root/root`）：

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/house?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false
```

### 3. 启动后端服务

```bash
cd house-agency
mvn spring-boot:run
# 或
mvn package -DskipTests && java -jar target/house-agency-0.0.1-SNAPSHOT.jar
```

服务默认运行在 `http://localhost:8080`，管理后台入口为 `/login`。

### 4. 运行小程序

1. 打开微信开发者工具，导入 `house/` 目录
2. 修改 [house/request/index.js](house/request/index.js) 中的 `baseUrl`（本地调试填 `http://127.0.0.1:8080`，真机调试需改为局域网 IP 或已备案的 https 域名）
3. 编译预览即可

> 提示：微信小程序正式环境要求接口使用 HTTPS 且已在小程序后台配置合法域名，本地开发可在开发者工具中勾选「不校验合法域名」。

## 数据库表说明

| 表 | 说明 |
| --- | --- |
| `houses` | 房源信息 |
| `entrust` | 租房委托 |
| `order` | 订单 |
| `sale` | 二手房出售信息 |
| `news` | 资讯 |
| `decoration` | 装修案例 |
| `city` | 城市/区域 |
| `sys_user`、`sys_role`、`sys_permission` | 用户与 RBAC 权限 |
| `sys_role_user`、`sys_role_permission` | 角色关联表 |
| `persistent_logins` | 记住我登录令牌 |

## 运行截图

### 管理后台

|  |  |  |
| --- | --- | --- |
| ![后台首页](截图/1.png) | ![新房管理](截图/2.png) | ![二手房管理](截图/3.png) |
| ![城市管理](截图/4.png) | ![装修风格](截图/5.png) | ![出售出租类别](截图/6.png) |
| ![员工管理](截图/7.png) | ![客源管理](截图/8.png) | ![出售委托](截图/9.png) |
| ![订单管理](截图/10.png) | ![新闻管理](截图/11.png) | ![销售统计](截图/12.png) |

### 小程序端

|  |  |  |
| --- | --- | --- |
| ![首页](截图/13.png) | ![发布委托](截图/14.png) | ![卖房委托](截图/15.png) |
| ![修改密码](截图/16.png) | ![我的委托](截图/17.png) | ![支出订单](截图/18.png) |

## 许可证

仅供学习与毕业设计参考使用。
