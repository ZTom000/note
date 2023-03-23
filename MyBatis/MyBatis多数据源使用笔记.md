# MyBatis 多数据源使用笔记

[toc]

## 1. 多数据源配置方法

### 1.1. 实现 Mybatis Configuration 类

说明：配置多个数据源时需要根据不同数据源实现不同的 Mybatis Configuration 类，该类在 Spring Boot 启动时会自动创建该数据源所需要的 `dataSource` ， `transactionManager` ， `sqlSessionFactory` 这三个 bean 并注入到 IoC 容器中，供后续使用。

``` java
@Configuration
// 配置 mapper 扫描器
@MapperScan(basePackages = SecurityDataSourceConfiguration.PACKAGE, sqlSessionFactoryRef = "securitySqlSessionFactory") 
public class SecurityDataSourceConfiguration {
    // 自定义 dao 包路径
    public static final String PACKAGE = "com.ztom.cloudac.domain.security.dao";
    // 自定义 mapper.xml 路径
    public static final String MAPPER_LOCATION = "classpath:mapper/security/*.xml";
	
    // 注入配置文件中的数据库配置信息
    @Value("${security.datasource.url}")
    private String url;

    @Value("${security.datasource.username}")
    private String username;

    @Value("${security.datasource.password}")
    private String password;

    @Value("${security.datasource.driver-class-name}")
    private String driverClass;
	
    // 创建数据源 bean
    @Bean(name = "securityDataSource")
    public DataSource securityDataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driverClass);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
	
    // 创建事务管理器
    @Bean(name = "securityTransactionManager")
    public DataSourceTransactionManager securityTransactionManager() {
        return new DataSourceTransactionManager(securityDataSource());
    }
	
    // 创建 sqlSessionFactory
    @Bean(name = "securitySqlSessionFactory")
    public SqlSessionFactory securitySqlSessionFactory(@Qualifier("securityDataSource") DataSource securityDataSource) throws Exception {
        final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(securityDataSource);
        sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(SecurityDataSourceConfiguration.MAPPER_LOCATION));
        return sessionFactory.getObject();
    }
}
```

### 1.2. 编写数据库配置信息

说明：数据库信息根据具体数据库的信息配置。

``` yaml
security:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource #当前数据源操作类型
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.1.100:3306/db_cloudac_auth?serverTimezone=GMT%2b8&useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&failOverReadOnly=false
    username: root
    password: ztom1023 #123456
```

### 1.3. 事务管理器使用方法

说明：在具体实现方法的 `@Transactional` 注解中添加 `transactionManager`  的值，该值为具体数据源所实现的事务管理器 bean 的名字，否则会出现 Spring 无法确定使用哪个事务管理器而抛出的异常。

``` java
@Service
@Transactional(transactionManager = "defaultTransactionManager")
public class ServiceImpl implements Service {
	@Override
    @Transactional(transactionManager = "defaultTransactionManager", readOnly = true)
    public void method(long id) {
		// do something...
        return 0;
    }
}
```

