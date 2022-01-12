# Mybatis-Plus
## 整合情形
### Mybatis + MP 整合
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

### Spring + Mybatis + MP 整合
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

### SpringBoot + Mybatis + MP 整合
1. 导入 mybatis-plus-boot-starter 依赖
```
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>x.x.x</version>
</dependency>
```
2. 其他操作同 Spring 整合

## CRUD
### Insert
```java
int insert(T entity);
```

BaseMapper 接口中 insert 方法返回值是**数据库受影响的行数**。

若插入对象的某一属性在数据库中是自增的，那么插入的时候无需赋初值，需要在该属性添加``@TableId(type = IdType.AUTO)``注解，此时 insert SQL 语句也不会再拼接该字段。

``@TableId``注解的源码如下：

```java
public @interface TableId {
    String value() default "";

    // 默认值是未设置主键类型
    IdType type() default IdType.NONE;
}
```

```java
public enum IdType {
    /**
     * 数据库ID自增
     * 该类型请确保数据库设置了 ID自增 否则无效>
     */
    AUTO(0),
    /**
     * 该类型为未设置主键类型(注解里等于跟随全局,全局里约等于 INPUT)
     */
    NONE(1),
    /**
     * 用户输入ID
     * 该类型可以通过自己注册自动填充插件进行填充
     */
    INPUT(2),

    /* 以下3种类型、只有当插入对象ID 为空，才自动填充。 */
    /**
     * 分配ID (主键类型为number或string）,
     * 默认实现类 {@link com.baomidou.mybatisplus.core.incrementer.DefaultIdentifierGenerator}(雪花算法)
     *
     * @since 3.3.0
     */
    ASSIGN_ID(3),
    /**
     * 分配UUID (主键类型为 string)
     * 默认实现类 {@link com.baomidou.mybatisplus.core.incrementer.DefaultIdentifierGenerator}(UUID.replace("-",""))
     */
    ASSIGN_UUID(4),
    /**
     * @deprecated 3.3.0 please use {@link #ASSIGN_ID}
     */
    @Deprecated
    ID_WORKER(3),
    /**
     * @deprecated 3.3.0 please use {@link #ASSIGN_ID}
     */
    @Deprecated
    ID_WORKER_STR(3),
    /**
     * @deprecated 3.3.0 please use {@link #ASSIGN_UUID}
     */
    @Deprecated
    UUID(4);

    private final int key;

    IdType(int key) {
        this.key = key;
    }
}
```

插入完成后，通过 get 方法可以获取到自增的该属性。

