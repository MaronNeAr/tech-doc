#### Spring Framework核心：IOC容器的实现

###### SpringIOC容器概述

- IOC在对象生成或者初始化时直接将数据注入到对象中，将对象的引用注入到对象数据域中
- 控制反转：把控制权从具体业务对象手中转交到平台或者框架当中
- SpringIOC提供了一个基本的JavaBean容器，通过IOC模式管理依赖关系，并通过依赖注入和AOP切面增强了为POJO对象赋予事物管理、生命周期管理等基本功能
- EJB组件需要编写远程/本地接口、Home接口以及Bean的实现类

###### IOC容器的设计和实现：BeanFactory和ApplicationContext

- <img src="/Users/marlon1475/Library/Application Support/typora-user-images/image-20240308193536298.png" alt="image-20240308193536298" align="left"/>
- Spring通过定义BeanDefinition来管理基于Spring应用中的各种对象以及他们之间的相互依赖关系
- BeanFactory：管理和获取 bean 对象，是否是单例、原型、某个特定对象
- MessageSource：应用程序中的文本信息的本地化和多语言支持
- ResourceLoader：用于加载资源，如文件、类路径下的文件等，即资源加载器
- ApplicationEventPublisher：Spring 事件模型的一部分，用于实现在应用程序中触发和监听事件的机制
- HierarchicalBeanFactory（extends BeanFactory）：引入层次化的概念，允许容器具有父子关系
- ConfigurableBeanFactory（extends HierarchicalBeanFactory）：使得 BeanFactory 更加可配置，提供了一些方法用于动态地配置和管理 bean
- ListableBeanFactory（extends BeanFactory）：查询和获取 bean 定义，包括获取所有 bean 名称、按类型获取 bean 名称、按类型获取 bean 实例等功能
- AutowireCapableBeanFactory（extends BeanFactory）：支持自动装配（Autowiring）的功能
- ApplicationContext（extends ListableBeanFactory, HierarchicalBeanFactory, MessageSource, ApplicationEventPublisher, ResourceLoader）
- ConfigurableApplicationContext（extends ApplicationContext）：使得 ApplicationContext 更加可配置，提供方法用于动态地配置和管理ApplicationContext
- WebApplicationContext（extends ApplicationContext）：支持基于 Web 环境的配置，包括 Web 应用程序的上下文路径、Servlet 上下文
- ThemeSource（extends WebApplicationContext）：实现了主题的解析和切换功能，适用于 Web 应用程序