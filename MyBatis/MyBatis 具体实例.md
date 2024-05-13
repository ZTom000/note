# MyBatis 具体实例

[TOC]

## 1. 查询用法

### 1.1. 动态查询目标表的前 n 条数据

```xml
    <!-- 数据库：SQLServer Mapper.xml 实例 -->
    <select id="#" resultMap="className">
        SELECT
           *
        FROM table_name
        ORDER BY id
        <choose>
            <when test="num != null">
                OFFSET 0 ROW FETCH NEXT #{num} ROW ONLY
            </when>
            <otherwise>
                OFFSET 0 ROW FETCH NEXT 1000 ROW ONLY
            </otherwise>
        </choose>
    </select>
```

## 2. 插入用法

### 2.1. 批处理实现批量插入

步骤：

- 注入对应数据源的 `SqlSessionFactory` 对象

- 创建 `SqlSession` 对象

- 创建业务 Mapper 接口 `xxxMapper.class` 

- 使用 Mapper 接口处理业务

- 调用 `SqlSession.commit()` 方法提交批处理

- `SqlSession.clearCache()` 关闭清空 `SqlSession` 缓存

- 关闭 `SqlSession`

- 

```java
    // ...

    @Override
    @Transactional(readOnly = false)
    public Boolean batchInsertTestUser() {
        Boolean flag = false;
        // 开启批处理 session
        SqlSession session = sqlSessionFactory.openSession(ExecutorType.BATCH, false);
        try{
            long startTime = System.currentTimeMillis();
            TestUserDao newDao = session.getMapper(TestUserDao.class);
            for(int i = 0; i < 100 ; i++){
                TestUser user = new TestUser();
                user.setName("Test" + i);
                user.setCreateTime(new Date());
                newDao.insert(user);
            }
            // 提交批处理请求
            session.commit();
            flag = true;
            log.info("insert finished spend {} ms time", System.currentTimeMillis() - startTime);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 清除缓存
            session.clearCache();
            // 关闭 session
            session.close();
        }
        return flag;
    }

    // ...
```

```xml
    <!-- TestUserMapper.xml demo -->
    <insert id="insert" parameterType="xxx.demo.entity.TestUser">
        INSERT INTO test_user (`name`, `create_time`)
        VALUES (#{name}, #{createTime})
    </insert>
```

## 3. 查询结果映射

### 3.1. 1 对 1

### 3.2. 1 对多

- 类说明

```java
// entity.java
// 添加序列化注解
@JsonIgnoreProperties(value = { "handler" })
public class Entity {
    private Integer id;
    private List<Entity2> list;
}

// entity.java
public class Entity2 {
    private Integer id;
    private String detail;
}


// mapper.java
@Mapper
public interface EntityDao {
    public List<Entity> getEntityList();
}
```

```xml
<!-- EntityMapper.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTO Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pageketname.dao.EntityDao">
    <resultMap id="entity_result" type="pageketname.entity.Entity">
        <id property="id" column="id"/>
        <!-- 1 对 多使用 <collection> 标签 -->
        <collection property="list" ofType="pageketname.entity.Entity2"
            resultMap="pageketname.dao.Entity2Dao.entity2_result"/>
    </resultMap>
    <select id="getEntityList" resultMap="entity_result">
    </select
</mapper>

<!-- Entity2Mapper.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTO Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pageketname.dao.Entity2Dao">
    <resultMap id="entity2_result" type="pageketname.entity.Entity2">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
    </resultMap>
</mapper>
```

### 3.3. 多对多
