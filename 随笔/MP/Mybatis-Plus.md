# Mybatis-Plus
## Mybatis + MP 整合
1. Mapper 继承 BaseMapper，使其拥有 BaseMapper 中的所有方法
2. 使用 MybatisSqlSessionFactoryBuilder 进程构建
   ```java
   // 配置文件
   String config = "xxx.xml";
   InputStream inputStream = Resources.getResourceAsStream(config);
   SqlSessionFactory factory = new MybatisSqlSessionFactoryBuilder().build(inputStream);
   SqlSession sqlSession = factory.openSession();
   Mapper mapper = sqlSession.getMapper(Mapper.class);
   ```
3. 实体类类名和数据库表名的映射需要在实体类使用注解： `@TableName("table_name")`

## Spring + Mybatis + MP 整合
1. 编写 jdbc.properties
2. 编写 applicationContext.xml
    ```
    <!-- 配置 properties 文件扫描器 -->
    <context:property-placeholder location="classpath:*.properties"/>
    
    <!-- 定义数据源 -->
    <bean id="dataSource" class="com.alibaba.druid.poll.DruidDataSource"
          destroy-method="close">
        <property name="url" value="&{jdbc.url}"/> 
        <property name="username" value="&{jdbc.username}"/> 
        <property name="password" value="&{jdbc.password}"/> 
        <property name="driverClassName" value="&{jdbc.driverClassName}"/> 
        <property name="maxActive" value="10"/> 
        <property name="minIdle" value="5"/>
    </bean>
    
    <!-- 使用 MP 提供的 sqlSessionFactory，完成与 Spring 与 MP 的整合 -->
    <bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    
    <!-- 扫描 mapper 接口，使用 Mybatis 原生的扫描器 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basepackage" value="xxx.mapper">
    </bean>
    ```
