# RankMaster

**RankMaster** 是一个高效的用户排名查询系统，设计用于支持百万级用户的实时排名查询。该系统通过结合 Redis 和 MySQL，实现了用户得分的快速排序与持久化存储，适合处理高并发的场景。

## 功能特性

- **用户排名查询**：根据用户得分，在 Redis 的 Sorted Set 中进行快速排名计算。
- **实时更新**：当用户得分变化时，数据会同步到 Redis 和 MySQL，确保排名实时更新。
- **高并发处理**：Redis 提供了快速的排名查询能力，支持高并发用户请求。
- **持久化存储**：MySQL 用于持久化存储用户的得分，确保数据安全和一致性。

## 架构概述

- **Redis**：用于存储和排序用户的得分，利用 Sorted Set 提供高效的排名查询功能。
- **MySQL**：用于存储用户的基本信息和得分，实现持久化。
- **Jedis**：Java Redis 客户端，用于与 Redis 进行交互。
- **JDBC**：用于与 MySQL 进行数据库操作。

## 依赖

在项目的 `pom.xml` 文件中，添加以下依赖：

```xml
<dependencies>
    <!-- Jedis for Redis -->
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>4.3.1</version>
    </dependency>

    <!-- MySQL Connector for Java -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.34</version>
    </dependency>
</dependencies>
```

## 使用方法

### 1. 数据库设置

在 MySQL 中创建一个名为 `user_ranking` 的数据库，并创建 `user_scores` 表：

```sql
CREATE DATABASE user_ranking;

USE user_ranking;

CREATE TABLE user_scores (
    user_id BIGINT PRIMARY KEY,
    score DOUBLE NOT NULL,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### 2. Redis 设置

确保 Redis 已经在本地或服务器上运行，并且已安装 Jedis 进行 Java 与 Redis 的交互。

### 3. 项目配置

在 Java 项目中，配置 Redis 和 MySQL 连接信息：

```java
// MySQL 配置
private static final String DB_URL = "jdbc:mysql://localhost:3306/user_ranking";
private static final String USER = "root"; // 替换为你的MySQL用户名
private static final String PASS = "password"; // 替换为你的MySQL密码

// Redis 配置
private static final String REDIS_HOST = "localhost";
private static final int REDIS_PORT = 6379;
```

### 4. 插入数据

你可以使用以下代码插入测试数据（例如，插入100万条用户记录）：

```java
public class RedisMySQLIntegration {

    public static void main(String[] args) throws SQLException {
        RedisMySQLIntegration integration = new RedisMySQLIntegration();
        integration.insertTestData(1000000); // 插入100万条数据
        integration.close();
    }
}
```

### 5. 查询排名

使用 `getUserRank` 方法查询用户的排名：

```java
long rank = rankingService.getUserRank(1001);
System.out.println("User 1001's rank: " + rank);
```

### 6. 分页查询

通过 `getSurroundingUsers` 方法查询某个用户附近的排名信息：

```java
rankingService.getSurroundingUsers(1001, 10); // 查询用户1001前后10名用户
```

## 项目结构

- `RedisRankingService`：用于与 Redis 进行交互，实现用户得分的存储和排名查询。
- `MySQLDatabase`：用于连接 MySQL 数据库，并实现用户得分的插入与更新。
- `RedisMySQLIntegration`：提供 Redis 和 MySQL 的整合操作，插入和查询测试数据。

## 未来改进

- **性能优化**：可以进一步优化批量插入和查询的性能，通过管道（Pipeline）或批量操作减少开销。
- **异步更新**：将 Redis 和 MySQL 的更新操作异步化，以提升系统的吞吐量。
- **分布式支持**：使用 Redis 集群和分库分表机制，支持更大规模的用户数和高并发请求。

## 许可证

该项目采用 [GPL-3.0 license](https://github.com/CoderDream/rank-master?tab=GPL-3.0-1-ov-file#) 许可证，详细信息请查看 [LICENSE](./LICENSE) 文件。
