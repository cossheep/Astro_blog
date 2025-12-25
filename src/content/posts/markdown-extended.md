---
title: Markdown Extended Features
published: 2024-05-01
updated: 2024-11-29
description: 'Read more about Markdown features in Fuwari'
image: ''
tags: [Demo, Example, Markdown, Fuwari]
category: 'Examples'
draft: false 
---

## GitHub Repository Cards
You can add dynamic cards that link to GitHub repositories, on page load, the repository information is pulled from the GitHub API. 

::github{repo="Fabrizz/MMM-OnSpotify"}

Create a GitHub repository card with the code `::github{repo="<owner>/<repo>"}`.

```markdown
::github{repo="saicaca/fuwari"}
```

## Admonitions

Following types of admonitions are supported: `note` `tip` `important` `warning` `caution`

:::note
Highlights information that users should take into account, even when skimming.
:::

:::tip
Optional information to help a user be more successful.
:::

:::important
Crucial information necessary for users to succeed.
:::

:::warning
Critical content demanding immediate user attention due to potential risks.
:::

:::caution
Negative potential consequences of an action.
:::

### Basic Syntax

```markdown
:::note
Highlights information that users should take into account, even when skimming.
:::

:::tip
Optional information to help a user be more successful.
:::
```

### Custom Titles

The title of the admonition can be customized.

:::note[MY CUSTOM TITLE]
This is a note with a custom title.
:::

```markdown
:::note[MY CUSTOM TITLE]
This is a note with a custom title.
:::
```

### GitHub Syntax

> [!TIP]
> [The GitHub syntax](https://github.com/orgs/community/discussions/16925) is also supported.

```
> [!NOTE]
> The GitHub syntax is also supported.

> [!TIP]
> The GitHub syntax is also supported.
```

### Spoiler

You can add spoilers to your text. The text also supports **Markdown** syntax.

The content :spoiler[is hidden **ayyy**]!

```markdown
The content :spoiler[is hidden **ayyy**]!

```

**MyBatis-Plus（简称 MP）** 是 **MyBatis 的增强工具**，在 MyBatis 的基础上只做增强、不做改变，为简化开发、提高效率而生。它在国内 Java 开发中非常流行，尤其适合快速构建 CRUD 丰富的业务系统。

---

## ✅ 一、MyBatis-Plus 是什么？

> **MyBatis-Plus = MyBatis + 自动化 CRUD + 条件构造器 + 分页插件 + 代码生成器 + 更多实用功能**

它**完全兼容 MyBatis**，你仍然可以写 XML 或注解 SQL，但同时提供了大量开箱即用的能力，**大幅减少样板代码**。

---

## ✅ 二、核心特性（相比原生 MyBatis）

| 功能 | 说明 |
|------|------|
| **无 XML 快速 CRUD** | 继承 `BaseMapper<T>`，直接获得 20+ 个通用方法（如 `selectById`, `updateById`, `deleteByMap` 等） |
| **条件构造器（Wrapper）** | 链式编程构建 WHERE 条件，无需手写 SQL（如 `query().eq("age", 18).like("name", "张")`） |
| **分页插件** | 一行配置 + 一个方法调用，自动实现物理分页（支持 MySQL、Oracle 等） |
| **代码生成器** | 自动生成 Entity、Mapper、Service、Controller 等全套代码 |
| **乐观锁插件** | 通过 `@Version` 注解实现并发更新控制 |
| **逻辑删除** | 通过 `@TableLogic` 注解实现“软删除”（`is_deleted = 1` 而非真删） |
| **自动填充** | 插入/更新时自动填充字段（如 `create_time`, `update_time`） |
| **SQL 注入器** | 可自定义全局方法（如批量插入、逻辑删除查询等） |

---

## ✅ 三、简单示例对比

### 原生 MyBatis（需写 Mapper 接口 + XML）
```java
// UserMapper.java
List<User> selectByAgeGreaterThan(@Param("age") int age);
```
```xml
<!-- UserMapper.xml -->
<select id="selectByAgeGreaterThan" resultType="User">
  SELECT * FROM user WHERE age > #{age}
</select>
```

### MyBatis-Plus（无需 XML，链式调用）
```java
@Autowired
private UserMapper userMapper;

// 直接使用条件构造器
List<User> users = userMapper.selectList(
    new QueryWrapper<User>().gt("age", 18).like("name", "张")
);
```

或者更简洁（使用 Lambda 避免字段名硬编码）：
```java
List<User> users = userMapper.selectList(
    new LambdaQueryWrapper<User>()
        .gt(User::getAge, 18)
        .like(User::getName, "张")
);
```

### 甚至不用写任何查询语句（通用方法）：
```java
User user = userMapper.selectById(100L); // 根据 ID 查询
userMapper.deleteById(100L);             // 删除
userMapper.insert(user);                 // 插入
userMapper.updateById(user);             // 更新（根据 ID）
```

---

## ✅ 四、典型项目结构（Spring Boot + MP）

1. **Entity 实体类**
   ```java
   @Data
   @TableName("ec_user")
   public class User {
       @TableId(type = IdType.AUTO)
       private Long id;
       private String name;
       private Integer age;
       @TableField(fill = FieldFill.INSERT)
       private Date createTime;
   }
   ```

2. **Mapper 接口**
   ```java
   public interface UserMapper extends BaseMapper<User> {
       // 什么都不用写！已继承通用方法
   }
   ```

3. **Service 层（可选）**
   ```java
   @Service
   public class UserService extends ServiceImpl<UserMapper, User> {
       // 同样继承了 save(), removeById(), list() 等方法
   }
   ```

4. **Controller 调用**
   ```java
   @RestController
   public class UserController {
       @Autowired
       private UserService userService;

       @GetMapping("/users")
       public List<User> getUsers() {
           return userService.list(
               new LambdaQueryWrapper<User>().gt(User::getAge, 18)
           );
       }
   }
   ```

---

## ✅ 五、MyBatis-Plus 与其它方式对比

| 方式 | 是否需要写 SQL | 代码量 | 灵活性 | 学习成本 | 适用场景 |
|------|--------------|--------|--------|----------|--------|
| **JDBC** | 是 | 多 | 高 | 低 | 教学/工具 |
| **MyBatis** | 是（XML/注解） | 中 | 高 | 中 | 复杂 SQL、精细控制 |
| **MyBatis-Plus** | **通常不需要**（复杂时可写） | **极少** | 中高 | 低~中 | **国内主流：快速开发 + 灵活兼顾** |
| **Spring Data JPA** | 否（HQL 可选） | 极少 | 低 | 高 | 国际项目、DDD |
| **Hibernate** | 否 | 少 | 低 | 高 | 全自动生成场景 |

> 💡 **MyBatis-Plus 的优势**：  
> - 保留了 MyBatis 的灵活性（随时可写 XML 覆盖）；  
> - 又提供了接近 JPA 的开发效率；  
> - 特别适合**国内以 MySQL 为主、CRUD 密集、需快速迭代**的项目。

---

## ✅ 六、注意事项

1. **不是银弹**：超复杂报表、多表深度 JOIN 仍建议写 XML。
2. **字段名安全**：推荐使用 `LambdaQueryWrapper` 避免字符串硬编码。
3. **性能**：通用方法已优化，但批量操作建议用 `saveBatch()` 而非循环 `insert()`。
4. **版本兼容**：注意与 Spring Boot、MyBatis 版本匹配。

---

## ✅ 总结

> **MyBatis-Plus 是 MyBatis 的“超级外挂”**：  
> - 对于 80% 的标准增删改查，**零 SQL、零 XML、一行代码搞定**；  
> - 对于 20% 的复杂场景，**无缝回退到原生 MyBatis**，毫无违和感。  

如果你正在用 MyBatis，**强烈建议升级到 MyBatis-Plus** —— 它几乎已成为国内 Java 企业开发的**事实标准**之一。