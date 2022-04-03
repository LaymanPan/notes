## 判断是否是FactoryBean

### AbstractBeanFactory下的isFactoryBean(String beanName, RootBeanDefinition mbd

顺序分别是Beandefinition.isFactoryBean -> Beandefinition.targetType -> BeanDefinition.BeanClass

![image-20220321185028101](判断是否是FactoryBean.assets/image-20220321185028101.png)![image-20220321185049337](判断是否是FactoryBean.assets/image-20220321185049337.png)

![image-20220321185136969](判断是否是FactoryBean.assets/image-20220321185136969.png)