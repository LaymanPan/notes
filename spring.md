### 简单描述bean的生命周期？

> 生命周期流程图

![image-20220308123507613](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308123507613.png)

> 通过getBean()方法获取Bean

![image-20220309181117293](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181117293.png)

![image-20220309181237075](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181237075.png)

![image-20220309181328903](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181328903.png)

> 调用InstantiationAwareBeanPostProcessor的postProcessBeforeInstantiation方法

![image-20220309181653695](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181653695.png)

![image-20220309181733406](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181733406.png)

![image-20220309181756529](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181756529.png)

> 实例化对象调用对象的构造函数

![image-20220309181910990](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181910990.png)

![image-20220309181946415](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309181946415.png)

![image-20220309182015374](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309182015374.png)

> 调用 InstantiationAwareBeanPostProcessor的postProcessAfterInstantiation方法

![image-20220309182233335](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309182233335.png)

![image-20220309182459381](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309182459381.png)

> 调用InstantiationAwareBeanPostProcessor的postProcessProperties

![image-20220309184414643](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309184414643.png)

> 调用InstantiationAwareBeanPostProcessor的postProcessPropertyValues

![image-20220309184520052](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309184520052.png)

> 设置属性值



> 初始化Bean

![image-20220309184652448](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309184652448.png)

> 调用Aware接口

![image-20220309184803765](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309184803765.png)

![image-20220309184832329](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309184832329.png)

> 调用BeanPostProcessor的postProcessBeforeInitialization方法

![image-20220309185003381](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309185003381.png)

![image-20220309185033736](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309185033736.png)

> 调用InitializingBean的afterPropertiesSet方法

![image-20220309185158715](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309185158715.png)

![image-20220309185335754](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309185335754.png)

> 调用init-method方法

![image-20220309185436286](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309185436286.png)

> 调用BeanPostProcessor的postProcessAfterInitialization方法

![image-20220309185545439](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309185545439.png)

![image-20220309185609299](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220309185609299.png)







Spring容器帮助我们去管理对象，从对象的产生到销毁的环节都是由容器进行控制，其中主要包含实例化和初始化两个关键环节，当然在整个过程中会有一些扩展点。下面来详细描述下各个环节和步骤。

> 1. 实例化bean对象通过反射的方式来生成

在源码中有一个createBeanInstance的方法是专门来生成对象的

> 2. 当bean对象创建完成后，对象的属性值都是默认值，所以需要开始给bean填充属性，通过populateBean方法来完成对象属性的填充，期间会涉及到循环依赖的问题

> 3. 向bean对象中设置容器属性，会调用invokeAwareMethods方法来将容器对象设置到具体的bean对象中去

如：applicationContext、BeanFactory、Enviroment、ResourceLoader

BeanFactoryAware、ApplicationContextAware

> 4. 调用BeanPostProcessor中的前置处理方法来进行Bean对象的扩展工作，ApplicationContextPostProcessor

> 5. 调用InvokeinitMethods方法来完成初始化方法的调用，在此方法过程中，需要判断当前bean对象是否实现了initializingBean的接口，如果实现了调用afterPropertiesSet方法来最后设置Bean对象

> 6. 调用BeanPostProcessor的后置处理方法，完成对Bean对象的后置处理工作，AOP就是在此处实现的，实现的接口实现名字叫做AbstractAutoProxyCreator

> 7. 获取到完整对象，通过getBean的方式去进行对象的获取和使用

> 8. 当对象使用完成之后，容器在关闭的时候会销毁对象，首先会去判断是否实现了DisposableBean接口，然后去调用destroyMethod方法

### BeanFactroy和FactoryBean的区别

![image-20220308130015389](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308130015389.png)

BeanFactory和FactoryBean都可以用来创建对象，只不过创建的流程和方式不同。
当使用BeanFactory的时候必须要严格的遵守 Bean 的生命周期，经过一系列繁杂的步骤之后才可以创建单例对象，是流水线式的创建过程。
而FactoryBean是用户可以自定义bean对象的创建流程，不需要按照Bean的生命周期来创建，在此接口中包含了三个方法
getObject();// 获取对象
getObjectType();// 获取对象类型
isSingleton();// 判断是否是单例对象

![image-20220308130328993](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308130328993.png)

如：Mybatis中的SqlSessionFactoryBean

![image-20220308130650858](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308130650858.png)

### Spring中使用的设计模式

> 1. 单例模式

Spring 中的bean都是单例的

> 2. 工厂模式

BeanFactroy

> 3. 模板模式

postProcessorBeanFatory

onRefresh

> 4. 观察者模式

listener,event,multicast

> 5. 适配器模式

Adapter

> 6. 装饰者模式

BeanWrapper

> 7. 责任链模式

使用Aop的时候会有一个责任链模式

> 8. 代理模式

Aop中动态代理

> 9. 委托者模式

delegate

> 10. 建造者模式

SpringApplicationBuilder

> 11. 策略模式

XmlBeanDefinitonReader、 PropertiesBeanDefinitionReader



### ApplicationContext和BeanFactroy的区别

BeanFactory是访问Spring容器的根接口、里面只是提供了某些基本方法的约束和规范，为了满足更多需要，ApplicationContext实现了BeanFactory接口，并在此接口的基础上做了某些扩展功能提供了更加丰富的API

![image-20220308132144014](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308132144014.png)

![image-20220308132330690](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308132330690.png)

### 谈谈你对循环依赖的理解

> 1. 什么是循环依赖

A 依赖 B  B 依赖 A

![image-20220308132624010](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308132624010.png)

> 2. Spring中Bean对象的创建都要经历实例化和初始化（属性填充.）的过程

![image-20220308133055628](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308133055628.png)

![image-20220308133804174](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308133804174.png)

> 3. 只有一级缓存是否可行？ 不可以

会把成品状态的Bean 对象和半成品状态的Bean对象放到一起，而半成品对象是无法暴露给外部使用的，所以要将成品和半成品分开存储，一级缓存（）中放置成品对象，二级缓存（）放置半成品对象

> 4. 只有二级缓存是否可行? 不可以

如果整个应用程序中不涉及到AOP，那么二级缓存是足以解决循环依赖的问题，如果AOP中存在了循环依赖，那么必须要用三级缓存才能解决

> 5. 为什么需要三级缓存

![image-20220308134528654](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308134528654.png)

三级缓存的Value类型是ObjectFactroy,是一个函数式接口，不是直接进行调用，只有在调用getObject方法的时候才会去调用里面存储的lamda表达式，存在的意义是保证在整个容器运行过程中同名的bean对象只能有一个 

> 如果一个对象被代理，或者说需要生成代理对象，那么是否需要先生成一个原始对象？要的

> 当创建出代理对象之后，会同时存在代理对象和普通对象，那么此时我该用哪一个？

当需要代理对象的时候，或者说代理对象生成的时候必须要覆盖原始对象，也就是说，整个容器中一个bean名称仅有一个bean

在实际调用的过程中，是没有办法确定什么时候对象需要被调用，因此当一个对象被调用的时候，优先判断当前对象是否需要被代理，类似于回调机制，当获取对象之后根据传入的lamda表达式来确认返回的是哪一个确定的对象

如果条件符合返回代理对象、如果条件不符合返回原始对象

![image-20220308140522149](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308140522149.png)

![image-20220308140453763](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308140453763.png)

![image-20220308140432897](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308140432897.png)



### Spring Aop 底层实现

动态代理

aop是ioc的一个扩展功能，先有的ioc,在有的aop，只是在ioc的整个流程中新增的一个扩展点而已：BeanPostProcessor:

bean的创建过程中有一个步骤可以对bean进行扩展实现，aop本身就是一个扩展功能，所以在BeanPostProcessor的后置处理方法中来实现

> 1. 代理对象的创建过程（advice，切面，切点）

> 2. 通过JDK或者CGlib的方式来生成代理对象

> 3. 在执行方法调用的时候，会调用到生成的字节码文件中，直接会找到 DynamicAdvisoredInterceptor类中的intercept方法，从此方法开始执行

> 4. 根据之前定义好的通知来生成拦截器链

> 5. 从拦截器链中依次获取每一个通知开始进行执行，在执行过程中，为了方便找到下一个通知是哪个，会有一个CglibMethodInvocation的对象，找的时候是从-1的位置依次开始查找并执行的



### Spring的事务是如何回滚的

Spring的事务管理是如何实现的？

总：spring的事务是由aop来实现的，首先要生成具体的代理对象，然后按照aop整套流程来执行具体的操作逻辑，正常情况下要通过通知来完成核心功能，但是事务不是通过通知来实现的，而是通过一个TransactionInterceptor来实现的，然后调用invoke来实现具体的逻辑

分：

> 1. 先做准备工作，解析各个方法上事务相关的属性，根据具体的属性来判断是否开启新事务

> 2. 当需要开启的时候，获取数据库连接，关闭自动提交功能，开启事务

> 3. 执行具体的Sql逻辑操作

> 4. 在操作过程中如果执行失败了，那么会通过completTransactionAfterThrowing来完成事务的回滚操作，回滚的具体逻辑是通过doRollBack方法来实现的，实现的时候也是先获取连接对象

> 5. 如果执行正常，则通过commitTransactionAfterReturning来提交事务，提交的具体逻辑是通过doCommit方法来实现的

> 6. 当事务执行完毕后需要清除相关的事务信息cleanupTransactionInfo，如果更细致的话需要知道TransactionInfo，TransactionStatus

![image-20220308163759569](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308163759569.png)



### Spring事务传播机制

![image-20220308165708466](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220308165708466.png)

>  Required：当前有事务则加入，没事务则新建

>  Supports：当前有事务则加入，没事务则不通过事务

>  Mandatory：当前有事务则加入，没事务抛异常

>  Requires_New：新建一个事务

>  Not_Support：不通过事务的方式运行

>  Never：不通过事务的方式运行，当前有事务则抛异常

>  Nested：

某一个事务嵌套另一个事务的时候怎么办？

A 方法调用 B 方法 AB都有事务，但传播特性不同，那么如果A有异常 B 怎么办，B有异常A怎么办

总：事务的船舶特性指的是不同方法的嵌套调用过程中，事务应该如何进行处理，是用同一个事务还是不同的事务，当出现异常的时候是回滚还是提交，两个方法之间的相关影响，在日常工作中 使用的比较多的是Required、Require_New, Nested

分：

1. 先说事务的不同分类，可以分为三类：支持当前事务，不支持当前事务，嵌套事务
2. 如果外层方法是Required 内层方法是 Required、Require_New, Nested
3. 如果外层方法是Require_New 内层方法是 Required、Require_New, Nested
4. 如果外层方法是Nested内层方法是 Required、Require_New, Nested





