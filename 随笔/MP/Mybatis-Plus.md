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