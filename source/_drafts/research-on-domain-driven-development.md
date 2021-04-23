---
title: Research on domain-driven development
categories:
tags:
  - DDD
---

<img src="http://jack-files.oss-cn-shenzhen.aliyuncs.com/2021-03-31-082221.jpg" alt="分层以及依赖关系"  />

## 应用服务层 Application
### Application

包含应用服务实现。Application 包依赖于 Domain 包和 Application.Contracts 包。

### Application.Contracts

包含应用服务接口和相关的数据传输对象（DTO)。 Application Contract 包依赖于 Domain.Shared 包。

## 领域层 Domain

### Domain

包含实体、仓储接口、领域服务接口及其实现和其他领域对象。Domain 包依赖于 Domain.Shared 包。

### Domain.Shared

包含常量、枚举和其他类型，它不能包含实体、存储库、域服务或任何其他业务对象。**可以安全地与模块中的所有层使用**。此包也可以与第三方客户端使用。

## 基础设施层 Infrastructure

1. 为每个orm/数据库集成创建一个独立的集成包，比如Entity Framework Core 和 MongoDB。例如，创建一个抽象 Entity Framework Core 集成的 CompanyName.ModuleName.EntityFrameworkCore 包。ORM 集成包依赖于 Domain 包。不推荐依赖于 orm 数据库集成包中的其他层。
2. 为每个主要的库创建一个独立的集成包, 在不影响其他包的情况下可以被另一个库替换。

## Http 层

仅依赖 Application.Contracts 包。

