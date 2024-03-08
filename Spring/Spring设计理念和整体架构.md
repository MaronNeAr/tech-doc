#### Spring设计理念和整体架构

###### SpringIOC

- 包含IOC容器最基本的接口BeanFactory及其实现，如XmlBeanFactory
- 设计了IOC容器高级形态ApplicationContext应用上下文，包括FileSystemXmlApplicationContext、ClassPathXmlApplicationContext

###### SpringAOP

- 集成了AspectJ作为AOP的一个特定实现
- 在JVM动态代理/CG LIB的基础上，实现了一个AOP框架
- SpringAOP实现了一个完整的建立AOP代理对象，实现AOP拦截器，直至实现各种Advice通知

###### SpringMVC

- 以DispatchServlet为核心，实现了MVC模式，包括Web容器环境的集成（Tomcat），Web请求拦截、分发、处理和ModelAndView数据的返回
- 集成各种UI视图和数据展现

###### SpringJDBC/SpringORM

- 提供JdbcTemplate作为模板类，封装了基本的数据库操作方法
- SpringJDBC提供了RDBMS的操作对象，通过面向对象的方式进行数据库操作，比如使用MappingSqlQuery将数据记录映射到对象
- 提供常用的ORM框架，如Mybatis、Hibernate等

###### SpringTX

- 可以看到通用声明式事务的处理的基本过程，比如配置拦截器、事务配置属性
- 对事务进行创建、挂起、提交、回滚等基本过程
- 可以看到事务处理器如DataSourceTransactionManager、HibernateTransactionManager怎样封装不同的事务处理机制（JDBC、Hibernate）

###### Spring远程调用

- Spring应用之间端到端的业务调用如HTTP调用、客户端进行Service调用