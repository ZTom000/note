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
