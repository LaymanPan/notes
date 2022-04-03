## MyBatis加载原理-Springboot篇

### 引入@MapperScan

在启动类或者其他Configuration类上引入@MapperScan

![image-20220321172815148](Mybatis.assets/image-20220321172815148-16489610345121.png)

查看@MapperScan注解发现其引入了MapperScannerRegistrar类

![image-20220321173335321](Mybatis.assets/image-20220321173335321.png)关注其**ImportBeanDefinitionRegistrar**接口发现有一个方法**registerBeanDefinitionsregisterBeanDefinitions**

这个方法在ConfigurationClassPostProcessor中会被引用到

![image-20220321175018664](Mybatis.assets/image-20220321175018664.png)

![image-20220321175045258](Mybatis.assets/image-20220321175045258.png)

![image-20220321175122506](Mybatis.assets/image-20220321175122506.png)

![image-20220321175152082](Mybatis.assets/image-20220321175152082.png)

### 引入MapperScannerRegistrar

**MapperScannerRegistrar**在**registerBeanDefinitionsregisterBeanDefinitions**

注入了一个**MapperScannerConfigurerMapperScannerConfigurer**类

![image-20220321173827610](Mybatis.assets/image-20220321173827610.png)

这个类实现了**BeanDefinitionRegistryPostProcessor**接口在springboot启动过程中会调用**postProcessBeanDefinitionRegistry**方法额外生成BeanDefinitions

![image-20220321173857184](Mybatis.assets/image-20220321173857184.png)

**MapperScannerConfigurerMapperScannerConfigurer**在**postProcessBeanDefinitionRegistry**方法中扫描**@MapperScan**写入的mapper地址

![image-20220321174129877](Mybatis.assets/image-20220321174129877.png)

**ClassPathMapperScanner**类作为实际扫描classPath中的mapper，并修改扫描的BeanDefinitions将其实现类改为**MapperFactoryBean**

![image-20220321174400507](Mybatis.assets/image-20220321174400507.png)

![image-20220321174547136](Mybatis.assets/image-20220321174547136.png)

以上几部完成了将Mapper类存储到BeanDefinitonRegistry中



### 实例化Mapper

