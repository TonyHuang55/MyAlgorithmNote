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

#### @TableId
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

#### @TableField
``@TableField`` 注解常用于以下情况：
1. 对象中的属性名和字段名不一致(非驼峰)
    * 在 value 属性指定数据库表中的字段名
    * ``@TableField(value = "column_name")``
2. 对象中属性字段在表中不存在
    * 在 exist 属性指定该属性在数据库表中不存在
    * ``@TableField(exist = false)``
3. 对象中属性是敏感字段，不希望在查询时展示
    * 在 select 属性指定不返回该字段的查询结果
    * ``@TableField(select = false)``

### Update
#### UpdateById
```java
int updateById(@Param(Constants.ENTITY) T entity);
```

update 语句中只会拼接需要更新的字段，该方法的返回值依然是**数据库受影响的行数**。

#### 根据条件更新
```java
int update(@Param(Constants.ENTITY) T entity, @Param(Constants.WRAPPER) Wrapper<T> updateWrapper);
```

入参需要定义一个 wrapper 作为更新条件。
* QueryWrapper
    * eg:``queryWrapper.eq("column_name",val);``
* UpdateWrapper 
    * 比 QueryWrapper 多了一个 ``set`` 方法
    * eg:``updateWrapper.set("column_name",val);``
    
**注意：这里的 ``column_name`` 都是数据库中的字段名，而非实体中的属性名。**

### Delete
#### DeleteById
```java
int deleteById(Serializable id);
```

执行的 Sql 其实就是 ``DELETE FROM table_name WHERE id = ?`` 。

#### DeleteByMap
```
int deleteByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
```

用一个 Map 封装删除的条件，Map 中键值对的 key 是 ``column_name``，value 是筛选的内容。多个条件之间为 ``and`` 连接。

#### Delete
```java
int delete(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

QueryWrapper 也可以在构造入参处填入一个定义删除条件的对象去使用：

```java
TestClass delConditionObject = new TestClass();
delConditionObject.setColumn1(val1);
delConditionObject.setColumn2(val2);

// 这个 wrapper 的条件就是 column1 = val1 AND column2 = val2
// 建议使用该写法
QueryWrapper<TestClass> wrapper = new QueryWrapper<>(TestClass);
```

#### DeleteBatchIds
```java
int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
```
传递一个 id 的集合，执行的 Sql 即：``DELETE FROM table_name WHERE id IN (array)``

### Select
#### SelectById
```java
T selectById(Serializable id);
```

#### SelectBatchIds
```java
List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
```

当入参的集合中有不存在的 id 时候，查询只会显示存在的结果。

#### SelectOne
```java
T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

如果调用该方法查询的结果大于 1 条，那么该方法会抛出异常。故需保证入参的查询条件只能查询到 ≤ 1 条结果。

#### SelectCount
```java
Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

该方法用于查询数据条目数，执行的 Sql 为：``SELECT COUNT(1) FROM table_name WHERE condition``

#### SelectList
```java
List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

#### SelectPage
```java
<E extends IPage<T>> E selectPage(E page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

分页查询需要配置分页插件，方法如下：
1. 定义一个配置类(打上 ``@Configuration`` 注解)
2. 定义拦截器 ``PaginationInterceptor``，打上 ``@Bean`` 注解。

IPage 有一个实现类 Page，该方法返回的结果可以进一步获取到总条数、总页数、当前页数等信息。

### MP 中 Sql 注入的原理
在 MP 中，ISqlInjector 负责 Sql 注入工作

```java
public interface ISqlInjector {

    /**
     * 检查SQL是否注入(已经注入过不再注入)
     *
     * @param builderAssistant mapper 信息
     * @param mapperClass      mapper 接口的 class 对象
     */
    void inspectInject(MapperBuilderAssistant builderAssistant, Class<?> mapperClass);
}
```

在它的具体实现类 AbstractSqlInjector 中重写了上述方法

```java
@Override
public void inspectInject(MapperBuilderAssistant builderAssistant, Class<?> mapperClass) {
    Class<?> modelClass = extractModelClass(mapperClass);
    if (modelClass != null) {
        String className = mapperClass.toString();
        Set<String> mapperRegistryCache = GlobalConfigUtils.getMapperRegistryCache(builderAssistant.getConfiguration());
        if (!mapperRegistryCache.contains(className)) {
            List<AbstractMethod> methodList = this.getMethodList(mapperClass);
            if (CollectionUtils.isNotEmpty(methodList)) {
                TableInfo tableInfo = TableInfoHelper.initTableInfo(builderAssistant, modelClass);
                // 循环注入自定义方法
                methodList.forEach(m -> m.inject(builderAssistant, mapperClass, modelClass, tableInfo));
            } else {
                logger.debug(mapperClass.toString() + ", No effective injection method was found.");
            }
            mapperRegistryCache.add(className);
        }
    }
}
```

该方法在流中使用 ``inject`` 方法循环注入自定义方法，而 ``inject`` 方法又调用了如下抽象方法：

```java
/**
 * 注入自定义 MappedStatement
 *
 * @param mapperClass mapper 接口
 * @param modelClass  mapper 泛型
 * @param tableInfo   数据库表反射信息
 * @return MappedStatement
 */
public abstract MappedStatement injectMappedStatement(Class<?> mapperClass, Class<?> modelClass, TableInfo tableInfo);
```

各种 CRUD 操作正是实现了该抽象方法，以 ``SelectById`` 为例：

```java
public class SelectById extends AbstractMethod {

    @Override
    public MappedStatement injectMappedStatement(Class<?> mapperClass, Class<?> modelClass, TableInfo tableInfo) {
        SqlMethod sqlMethod = SqlMethod.SELECT_BY_ID;
        SqlSource sqlSource = new RawSqlSource(configuration, String.format(sqlMethod.getSql(),
            sqlSelectColumns(tableInfo, false),
            tableInfo.getTableName(), tableInfo.getKeyColumn(), tableInfo.getKeyProperty(),
            tableInfo.getLogicDeleteSql(true, true)), Object.class);
        return this.addSelectMappedStatementForTable(mapperClass, getMethod(sqlMethod), sqlSource, tableInfo);
    }
}
```

## 配置
### 基本配置
#### configLocation
MyBatis 配置文件的位置，如果有单独的 MyBatis 配置，需要将其路径配置到 configLocation 中。

* Spring Boot
   ```
   mybatis-plus.config-location = classpath:mybatis-config.xml
   ```
* Spring MVC
   ```
   <bean id="sqlSessionFactory"
   class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
       <property name="configLocation" value="classpath:mybatis-config.xml"/>
   </bean>
   ```

#### mapperLocations
MyBatis Mapper 所对应的 xml 文件位置，如果在 Mapper 中有自定义方法 (xml 中有自定义实现)，需要进行该配置，告诉 Mapper 所对应的 xml 文件位置。

* Spring Boot
   ```
   mybatis-plus.mapper-locations = classpath*:mybatis/*.xml
   ```
* Spring MVC
   ```
   <bean id="sqlSessionFactory"
   class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
       <property name="mapperLocations" value="classpath*:mybatis/*.xml"/>
   </bean>
   ```

> Maven 多模块项目的扫描路径需以 ``classpath*:`` 开头。(即加载多个 jar 包下的 xml 文件)

#### typeAliasesPackage
MyBatis 别名包扫描路径，通过该属性可以给包中的类注册别名，注册后在 Mapper 对应的 xml 文件中可以直接使用类名，而不用使用全限定的类名 (即 xml 中调用的时候不用包含包名)

* Spring Boot
   ```
   mybatis-plus.type-aliases-package = test.pojo
   ```
* Spring MVC
   ```
   <bean id="sqlSessionFactory"
   class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
       <property name="typeAliasesPackage" value="com.baomidou.mybatisplus.samples.quickstart.entity"/>
   </bean>
   ```

### 进阶配置
#### mapUnderscoreToCamelCase
* 类型： ``boolean``
* 默认值： ``true``

是否开启自动驼峰命名规则 (camel case) 映射，即从经典数据库列名 A_COLUMN (下划线命名) 到经典 Java 属性名 aColumn (驼峰命名) 的类似映射

> 注意：
>   
> 该属性在 MyBatis 中原默认值为 false，在 MyBatis-Plus 中，此属性也将用于生成最终的 SQL 的select body
> 
> 如果数据库命名规则符合规范则无需使用 ``@TableField`` 注解指定数据库字段名

* Spring Boot
   ```
   # 关闭自动驼峰映射，该参数不能和 mybatis-plus.config-location 同时存在
   mybatis-plus.configuration.map-underscore-to-camel-case = false
   ```

#### cacheEnable
* 类型： ``boolean``
* 默认值： ``true``

全局第开启或关闭配置文件中的所有映射器已经配置的任何缓存，默认为 true

* Spring Boot
   ```
   mybatis-plus.configuration.cache-enable = false
   ```

### DB 策略配置
#### idType
* 类型： ``com.baomidou.mybatisplus.annotation.IdType``
* 默认值： ``ID_WORKER``

全局默认主键类型，设置后，即可省略实体对象中的 ``@TableId(type=IdType.AUTO)`` 配置

* Spring Boot
   ```
   mybatis-plus.global-config.db-config.id-type = auto
   ```
* Spring MVC
   ```
   <bean id="sqlSessionFactory"
   class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
       <property name="globalConfig">
           <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig">
               <property name="dbConfig">
                   <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig$DbConfig">
                       <property name="idType" value="AUTO"/>
                   </bean>
               </property>
           </bean>
       </property>
   </bean>
   ```