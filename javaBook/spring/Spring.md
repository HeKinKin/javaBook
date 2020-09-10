# Spring

## 1.Spring Bean生命周期

Spring容器启动，启动完了会做一个简单的扫描，扫描完了以后把它变成BeanDefintion存到BeanDefintion Map当中，然后再去对BeanDefintion Map做一个遍历，遍历完成之后做一些验证，验证是否单例，是否原型，是否懒加载，是否有Depanson，是否抽象，是否factorybean，是否Bean的名称符合

验证完毕springbean容器会接着往下执行，获取实例化的类存不存在单列池中，有没有被提前暴露，
没有被提前暴露，那么spring bean就会开始来创建

第一步 通过推断构造方法，把当前spring bean所代表的类当中的构造方法得到一个最佳的构造方法。

第二步 通过反射去实例化一个java对象，之后在对这个bean做一些初始化的工作，比如是否要去做BeanDefinition合并，是否支持循环依赖，若支持循环依赖，会暴露当前bean(半成品)所对应的objectFactory.暴露就是存放一个二级缓存，一个map当中

第三步 做属性填充

第四步 然后aware方法的回调，比如一些applicationContextAware
BeanNameAware
ClassLoaderAware

第五步 接着生命周期初始化的回调

第六步 接着如果有aop会做一些代理

第七步 接着事件的发布

最后就是放在单例池当中，放入spring容器当中

## 2.Spring循环依赖的理解？

在整个springframework的体系当中，我们的一个bean是由一个beandefintion来构建的，beandefinition可以理解为springbean的一个建模
如果要理解循环依赖的话，首先要理解springbean的生命周期。一共大体分为以下几个过程。。

Spring容器启动，启动完了会做一个简单的扫描，扫描完了以后把它变成BeanDefintion存到BeanDefintion Map当中，然后再去对BeanDefintion Map做一个遍历，遍历完成之后做一些验证，验证是否单例，是否原型，是否懒加载，是否有Depanson，是否抽象，是否factorybean，是否Bean的名称符合

验证完毕springbean容器会接着往下执行，获取实例化的类存不存在单列池中，有没有被提前暴露，
没有被提前暴露，那么spring bean就会开始来创建

第一步 通过推断构造方法，把当前spring bean所代表的类当中的构造方法得到一个最佳的构造方法。

第二步 通过反射去实例化一个java对象

当反射了一个x对象，会进行填充属性，发觉x依赖了y，就会有y的生命周期流程。与x一样，会判断y是否在单例池中，如果没有在单例池当中的话，再去判断y有没有被提前暴露，这个时候y是没有被提前暴露的，y接着往下执行，就会把y实例化好，然后对y做一些初始化的工作。比如说把y提前暴露 属性填充，当发现y要填充x，发现x并没有被完整的实例化好，所以不能填充，所以走一遍创建x的流程，发现x已经被提前暴露了一个objectfactory产生的一个对象，就完成了一个循环依赖

#### 循环依赖为什么是单例的？

循环依赖是单例的，因为单例会在spring容器初始化的时候走我们的bean生命周期流程

## 3.Spring事务隔离级别

**Spring事务隔离级别比数据库事务隔离级别多一个default**

**1) DEFAULT （默认）**
这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别。另外四个与JDBC的隔离级别相对应。

**2) READ_UNCOMMITTED （读未提交）**
这是事务最低的隔离级别，它允许另外一个事务可以看到这个事务未提交的数据。这种隔离级别会产生脏读，不可重复读和幻像读。

**3) READ_COMMITTED （读已提交）**
保证一个事务修改的数据提交后才能被另外一个事务读取，另外一个事务不能读取该事务未提交的数据。这种事务隔离级别可以避免脏读出现，但是可能会出现不可重复读和幻像读。

**4) REPEATABLE_READ （可重复读）**
这种事务隔离级别可以防止脏读、不可重复读，但是可能出现幻像读。它除了保证一个事务不能读取另一个事务未提交的数据外，还保证了不可重复读。

**5) SERIALIZABLE（串行化）**
这是花费最高代价但是最可靠的事务隔离级别，事务被处理为顺序执行。除了防止脏读、不可重复读外，还避免了幻像读。

## 4.Spring的传播机制

propagation代表事物传播行为，默认值为`Propagation.REQUIRE`

- **Propagation.required**：如果当前存在事务，则加入该事务，如果当前不存在事务，则创建一个新的事务。( 也就是说如果A方法和B方法都添加了注解，在默认传播模式下，A方法内部调用B方法，会把两个方法的事务合并为一个事务 ）
- **Propagation.Supports****：如果当前存在事务，则加入该事务；如果当前不存在事务，则以非事务的方式继续运行。
- **Propagation.Mandatory**：如果当前存在事务，则加入该事务；如果当前不存在事务，则抛出异常。
- **Propagation.requires_new****：重新创建一个新的事务，如果当前存在事务，暂停当前的事务。( 当类A中的 a 方法用默认Propagation.REQUIRED模式，类B中的 b方法加上采用 Propagation.REQUIRES_NEW模式，然后在 a 方法中调用 b方法操作数据库，然而 a方法抛出异常后，b方法并没有进行回滚，因为Propagation.REQUIRES_NEW会暂停 a方法的事务 )
- **Propagation.Not_supports****：以非事务的方式运行，如果当前存在事务，暂停当前的事务。
- **Propagation.Never****：以非事务的方式运行，如果当前存在事务，则抛出异常。
- **Propagation.Nested**：和 Propagation.REQUIRED 效果一样。





## 3.@Transactional

#### 1.介绍

`@Transactional`注解相信大家都不陌生，平时在开发中`spring`框架中经常用到的事物注解。
他的作用就是控制程序执行的`原子性`，保证程序要提交就一起提交，如果出现错误，则一起进行回滚操作。
但是在使用`@Transactional`注解的时候有很多需要注意的地方，不然你就会发现这个事物注解会出现失效的情况。

#### 2.@Transcational可以作用在那些地方呢

@Transcational可以作用在接口、类、类方法。

- **作用类**：当把`@Transcational`注解放在类上时，表示所有该类的`public`方法都配置上相同的事物属性
- **作用方法**：当配置了`@Transcational`，方法也配置了`@Transcational`,那么方法上的事物属性会覆盖掉类上声明的事物属性。
- **作用接口**：不推荐这种使用方式，因为一旦标注在`Interface`上并且配置了`Spring AOP`使用`CGLib`动态代理，将会导致`@Transcational`失效

@Transactional
@RestController
@RequestMapping
public class MybatisPlusController {
    @Autowired
    private CityInfoDictMapper cityInfoDictMapper;
    

    @Transactional(rollbackFor = Exception.class)
    @GetMapping("/test")
    public String test() throws Exception {
        CityInfoDict cityInfoDict = new CityInfoDict();
        cityInfoDict.setParentCityId(2);
        cityInfoDict.setCityName("2");
        cityInfoDict.setCityLevel("2");
        cityInfoDict.setCityCode("2");
        int insert = cityInfoDictMapper.insert(cityInfoDict);
        return insert + "";
    }
}



#### `3.@Transcational`有哪些属性

propagation代表事物传播行为，默认值为`Propagation.REQUIRE`

- **Propagation.REQUIRED**：如果当前存在事务，则加入该事务，如果当前不存在事务，则创建一个新的事务。( 也就是说如果A方法和B方法都添加了注解，在默认传播模式下，A方法内部调用B方法，会把两个方法的事务合并为一个事务 ）
- **Propagation.SUPPORTS**：如果当前存在事务，则加入该事务；如果当前不存在事务，则以非事务的方式继续运行。
- **Propagation.MANDATORY**：如果当前存在事务，则加入该事务；如果当前不存在事务，则抛出异常。
- **Propagation.REQUIRES_NEW**：重新创建一个新的事务，如果当前存在事务，暂停当前的事务。( 当类A中的 a 方法用默认Propagation.REQUIRED模式，类B中的 b方法加上采用 Propagation.REQUIRES_NEW模式，然后在 a 方法中调用 b方法操作数据库，然而 a方法抛出异常后，b方法并没有进行回滚，因为Propagation.REQUIRES_NEW会暂停 a方法的事务 )
- **Propagation.NOT_SUPPORTED**：以非事务的方式运行，如果当前存在事务，暂停当前的事务。
- **Propagation.NEVER**：以非事务的方式运行，如果当前存在事务，则抛出异常。
- **Propagation.NESTED**：和 Propagation.REQUIRED 效果一样。

#### 4.注解失效的6大场景

1.如果@Transcational注解应用在非public修饰的方法上，Transcational将会失效。

之所以会失效是因为在Spring AOP代理时，如上图所示TranscationInterceptor（事物拦截器）在目标方法执行前后进行拦截，DynamicAdvisedInterceptor（CglibAopProxy的内部类）的intercept方法或JdkDynamicAopPorxy的invoke方法间接调用
AbstractFallbackTransactionAttributeSource的 computeTransactionAttribute 方法，获取Transactional 注解的事务配置信息。



此方法会检查目标方法的修饰符是否为 public，不是 public则不会获取@Transactional 的属性配置信息。
注意：protected、private 修饰的方法上使用 @Transactional 注解，虽然事务无效，但不会有任何报错，这是我们很容犯错的一点。



2.@Transcational注解属性propagation设置错误

这种失效由于配置错误，若是错误的配置以下三种propagation，事物将不会发生回滚。
这种失效是由于配置错误，若是错误的配置以下三种 propagation，事务将不会发生回滚。

- TransactionDefinition.PROPAGATION_SUPPORTS：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行

- TransactionDefinition.PROPAGATION_NOT_SUPPORTED：以非事务方式运行，如果当前存在事务，则把当前事务挂起。

- TransactionDefinition.PROPAGATION_NEVER：以非事务方式运行，如果当前存在事务，则抛出异常。

  

3.@Transcational注解属性rollbackFor 设置错误

rollbackFor 可以指定能够触发事务回滚的异常类型。Spring默认抛出了未检查unchecked异常（继承自 RuntimeException 的异常）或者 Error才回滚事务；其他异常不会触发回滚事务。如果在事务中抛出其他类型的异常，但却期望 Spring 能够回滚事务，就需要指定 rollbackFor属性。



![image-20200908113912912](C:\Users\hexin\AppData\Roaming\Typora\typora-user-images\image-20200908113912912.png)

```
*// 希望自定义的异常可以进行回滚*
@Transactional(propagation= Propagation.REQUIRED,rollbackFor= MyException.class
```

4.同一个类中方法调用，导致失效

开发中避免不了会对同一个类里面的方法调用，比如有一个类Test，它的一个方法A，A再调用本类的方法B（不论方法B是用public还是private修饰），但方法A没有声明注解事务，而B方法有。则外部调用方法A之后，方法B的事务是不会起作用的。这也是经常犯错误的一个地方。

那为啥会出现这种情况？其实这还是由于使用Spring AOP代理造成的，因为只有当事务方法被当前类以外的代码调用时，才会由Spring生成的代理对象来管理。

	//@Transactional
	@GetMapping("/test")
	private Integer A() throws Exception {
	    CityInfoDict cityInfoDict = new CityInfoDict();
	    cityInfoDict.setCityName("2");
	    /**
	     * B 插入字段为 3的数据
	     */
	    this.insertB();
	    /**
	     * A 插入字段为 2的数据
	     */
	    int insert = cityInfoDictMapper.insert(cityInfoDict);
	
	    return insert;
	}
	
	@Transactional()
	public Integer insertB() throws Exception {
	    CityInfoDict cityInfoDict = new CityInfoDict();
	    cityInfoDict.setCityName("3");
	    cityInfoDict.setParentCityId(3);
	
	    return cityInfoDictMapper.insert(cityInfoDict);
	}
5.异常被你的catch吃了，导致@Transactional失效



这种情况是最常见的一种@Transactional注解失效场景

 

```
@Transactional
    private Integer A() throws Exception {
        int insert = 0;
        try {
            CityInfoDict cityInfoDict = new CityInfoDict();
            cityInfoDict.setCityName("2");
            cityInfoDict.setParentCityId(2);
            /**
             * A 插入字段为 2的数据
             */
            insert = cityInfoDictMapper.insert(cityInfoDict);
            /**
             * B 插入字段为 3的数据
             */
            b.insertB();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

如果B方法内部抛了异常，而A方法此时try catch了B方法的异常，那这个事务还能正常回滚吗？

答案：不能！

会抛出异常：

```
org.springframework.transaction.UnexpectedRollbackException: Transaction rolled back because it has been marked as rollback-only
```

因为当ServiceB中抛出了一个异常以后，ServiceB标识当前事务需要rollback。但是ServiceA中由于你手动的捕获这个异常并进行处理，ServiceA认为当前事务应该正常commit。此时就出现了前后不一致，也就是因为这样，抛出了前面的UnexpectedRollbackException异常。

spring的事务是在调用业务方法之前开始的，业务方法执行完毕之后才执行commit or rollback，事务是否执行取决于是否抛出runtime异常。如果抛出runtime exception 并在你的业务方法中没有catch到的话，事务会回滚。

在业务方法中一般不需要catch异常，如果非要catch一定要抛出throw new RuntimeException()，或者注解中指定抛异常类型@Transactional(rollbackFor=Exception.class)，否则会导致事务失效，数据commit造成数据不一致，所以有些时候try catch反倒会画蛇添足。



6.数据库引擎不支持



这种情况出现的概率并不高，事务能否生效数据库引擎是否支持事务是关键。常用的MySQL数据库默认使用支持事务的innodb引擎。一旦数据库引擎切换成不支持事务的myisam，那事务就从根本上失效了。



## 4.Spring Bean默认是单例模式吗

是的。

https://blog.csdn.net/weixin_44337261/article/details/91696633

#### 解读

**如果一个bean被声明为单例的时候，在处理多次请求的时候在Spring容器里只实例化出一个bean，后续的请求都公用这个对象，这个对象会保存在一个map里面。**当有请求来的时候会先从缓存(map)里查看有没有，有的话直接使用这个对象，没有的话才实例化一个新的对象，所以这是个单例的。但是对于原型(prototype)bean来说当每次请求来的时候直接实例化新的bean，没有缓存以及从缓存查的过程。



![image-20200908160505990](C:\Users\hexin\AppData\Roaming\Typora\typora-user-images\image-20200908160505990.png)

![image-20200908160524369](C:\Users\hexin\AppData\Roaming\Typora\typora-user-images\image-20200908160524369.png)

#### 源码分析

生成bean的时候先判断是单例还是原型

![image-20200908160604760](C:\Users\hexin\AppData\Roaming\Typora\typora-user-images\image-20200908160604760.png)

如果是单例的则先尝试从缓存里获取，没有在新创建



![image-20200908160632594](C:\Users\hexin\AppData\Roaming\Typora\typora-user-images\image-20200908160632594.png)

#### 结论

- 单例的bean只有第一次创建新的bean 后面都会复用该bean，所以不会频繁创建对象。
- **原型的bean每次都会新创建**



**3.1、单例bean的优势**

由于不会每次都新创建新对象所以有一下几个性能上的优势:

**（1）减少了新生成实例的消耗 新生成实例消耗包括两方面，首先，Spring会通过反射或者cglib来生成bean实例这都是耗性能的操作，其次给对象分配内存也会涉及复杂算法**

**（2）减少jvm垃圾回收 由于不会给每个请求都新生成bean实例，所以自然回收的对象少了**

**（3）可以快速获取到bean 因为单例的获取bean操作除了第一次生成之外其余的都是从缓存里获取的所以很快**

**3.2、单例bean的劣势**

单例的bean一个很大的劣势就是他不能做到**线程安全！！！**，由于所有请求都共享一个bean实例，所以这个bean要是有状态的一个bean的话可能在并发场景下出现问题，而原型的bean则不会有这样问题（但也有例外，比如他被单例bean依赖），因为给每个请求都新创建实例。



#### 总结

**Spring 为啥把bean默认设计成单例？**

答案：**为了提高性能！！！从几个方面：1.少创建实例2.垃圾回收3.缓存快速获取**



## 5.有状态的bean和无状态的bean

#### 分析

**有状态会话bean**  ：每个用户有自己特有的一个实例，在用户的生存期内，bean保持了用户的信息，即“有状态”；一旦用户灭亡（调用结束或实例结束），bean的生命期也告结束。即每个用户最初都会得到一个初始的bean。简单来说，有状态就是有数据存储功能。有状态对象(Stateful Bean)，就是有实例变量的对象 ，可以保存数据，是**非线程安全**的。

**无状态会话bean**  ：bean一旦实例化就被加进会话池中，各个用户都可以共用。即使用户已经消亡，bean  的生命期也不一定结束，它可能依然存在于会话池中，供其他用户调用。由于没有特定的用户，那么也就不能保持某一用户的状态，所以叫无状态bean。但无状态会话bean  并非没有状态，如果它有自己的属性（变量），那么这些变量就会受到所有调用它的用户的影响，这是在实际应用中必须注意的。简单来说，无状态就是一次操作，不能保存数据。无状态对象(Stateless Bean)，就是没有实例变量的对象 .不能保存数据，是不变类，是**线程安全**的。



```
package com.sw;

public class TestManagerImpl implements TestManager{
    private User user;    //有一个记录信息的实例

    public void deleteUser(User e) throws Exception {
        user = e ;           //1
        prepareData(e);
    }
    
    public void prepareData(User e) throws Exception {
        user = getUserByID(e.getId());            //2
        .....
        //使用user.getId();                       //3
        .....
        .....
    }    

}

一个有状态的bean
```

#### **解决有状态bean的线程安全问题**

Spring对bean的配置中有四种配置方式，我们只说其中两种：singleton单例模式、prototype原型模式。

当然，scope的值不止这两种，还包括了request,session 等。但用的最多的还是singleton单态，prototype多态。Singleton、Prototype和有无状态的bean是两个概念。。

Singleton作用域表示该bean全局只有一个实例，Spring中bean的scope默认也是singleton，这种情况适用于无状态的Bean。

如Service层、Dao层用默认singleton就行，虽然Service类也有dao这样的属性，但dao这些类都是没有状态信息的，也就是相当于不变(immutable)类，所以不影响。Struts2中的Action因为会有User、BizEntity这样的实例对象，是有状态信息的，在多线程环境下是不安全的，所以Struts2默认的实现是Prototype模式。在Spring中，Struts2的Action中，scope要配成prototype作用域。



```
<bean id="testManager" class="com.sw.TestManagerImpl" scope="singleton" />

<bean id="testManager" class="com.sw.TestManagerImpl" scope="prototype" />
```

默认的配置是singleton。

singleton表示该bean全局只有一个实例。

prototype表示该bean在每次被注入的时候，都要重新创建一个实例，这种情况适用于有状态的Bean。

如果对有状态的bean使用了singleton的话会出现线程安全问题。



例如上面的例子

如果有两个用户同时访问

假定为user1,user2

当user1 调用到程序中的1步骤的时候，该Bean的私有变量user被付值为user1

当user1的程序走到2步骤的时候，该Bean的私有变量user被重新付值为user1_create

理想的状况，当user1走到3步骤的时候，私有变量user应该为user1_create;

但如果在user1调用到3步骤之前，user2开始运行到了1步骤了，由于单态的资源共享，则私有变量user被修改为user2

这种情况下，user1的步骤3用到的user.getId()实际用到是user2的对象。



#### 对于这种情况我们可以这样解决

1.将有状态的bean配置成prototype模式，让每一个线程都创建一个prototype实例。但是这样会产生很多的实例消耗较多的内存空间。

2.使用ThreadLocal变量，为每一条线程设置变量副本。



#### **Springmvc默认是singleton单例模式，Struts2默认的实现是Prototype模式。**

Spring使用ThreadLocal解决线程安全问题。我们知道在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作用域。就是因为Spring对一些Bean（如RequestContextHolder、TransactionSynchronizationManager、LocaleContextHolder等）中非线程安全状态采用ThreadLocal进行处理，让它们也成为线程安全的状态，因为有状态的Bean就可以在多线程中共享了。



## 6.Spring的线程安全

## 7.AOP的理解

AOP，一般称为面向切面，作为面向对象的一种补充，

用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被命名为“切面”（Aspect）

减少系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。

在面向对象编程中，关键的单元是对象，但AOP的关键单元是切面(关注点)，像日志或者事务这些分散的切面(横切关注点——可以影响到整个应用的关注点)应该被尽量地集中到一个地方以方便管理(事务管理、权限、日志、安全)。而AOP就是作用于这些横切关注点，使得代码在当下和将来都变得容易维护。

#### 有哪些通知类型？

![image-20200910091417837](Spring.assets\image-20200910091417837.png)

#### 在Spring AOP中关注点和横切关注点有什么不同？

关注点：我们的应用模块中实现以解决特定业务问题的方法。比如库存管理、航运管理、用户管理等。

横切关注点：贯穿整个应用程序的关注点。像事务管理、权限、日志、安全

#### 核心

AOP实现的关键在于 代理模式，AOP代理主要分为静态代理和动态代理。静态代理的代表为AspectJ；动态代理则以Spring AOP为代表。

（1）AspectJ是静态代理的增强，所谓静态代理，就是AOP框架会在编译阶段生成AOP代理类，因此也称为编译时增强，他会在编译阶段将AspectJ(切面)织入到Java字节码中，运行的时候就是增强之后的AOP对象。

（2）Spring AOP使用的动态代理，所谓的动态代理就是说AOP框架不会去修改字节码，而是每次运行时在内存中临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。

Spring AOP中的动态代理主要有两种方式，JDK动态代理和CGLIB动态代理：

  ①JDK动态代理只提供接口的代理，不支持类的代理。核心InvocationHandler接口和Proxy类，InvocationHandler 通过invoke()方法反射来调用目标类中的代码，动态地将横切逻辑和业务编织在一起；接着，Proxy利用 InvocationHandler动态创建一个符合某一接口的的实例, 生成目标类的代理对象。

   ②如果代理类没有实现 InvocationHandler 接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成指定类的一个子类对象，并覆盖其中特定方法并添加增强代码，从而实现AOP。CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。

（3）静态代理与动态代理区别在于生成AOP代理对象的时机不同，相对来说AspectJ的静态代理方式具有更好的性能，但是AspectJ需要特定的编译器进行处理，而Spring AOP则无需特定的编译器处理。



#### 场景

![image-20200910095019305](Spring.assets\image-20200910095019305.png)

![image-20200910095030338](Spring.assets\image-20200910095030338.png)

![image-20200910095046877](Spring.assets\image-20200910095046877.png)

![image-20200910095059590](Spring.assets\image-20200910095059590.png)

## 8.动态代理

#### 什么是动态代理？

（大白话）

作用非常大，在很多底层框架中都会用得到，比如struts，Spring等都用到了动态代理dao，

它的作用很简单，就是将你要使用的类，重新生成一个子类或本类，这样框架就可以利用这个新生成的类做一些事情，比如在该类的方法前后加一些代码

这样的话，你想像一下，你是不是不用修改任何已经编写好的代码，只要使用代理就可以灵活的加入任何东西，将来不喜欢了，不用也不会影响原来的代码

1. 动态代理是一种设计模式，用于增强代理对象的功能的一种设计方式。更严格的来说代理包括静态代理和动态代理，只不过动态代理对于代码没有侵入性，技术实现难度大了些，所以大家谈的相对较多。

	2. AOP这是spring提出的用于增强spring bean中方法功能的设计思路和具体实现。AOP思路和动态代理很像，AOP的原则是一切皆可代理，口气比动态代理大多了。
AOP的具体实现比如cglib，javasist，cglib应该算是AOP实现的集大成者，这框架的能力强到爆炸，你可以随心所欲操作字节码。比如曾经使用spring AOP魔改jdk的代码。



至于作用就是这些，一般来说使用AOP可以解决我们日常工作中：
	1. 拦截
	2. 在不能动原有代码基础上动他的代码
	3. 为了对上层服务透明，一般也都是AOP
	
	最后举几个每天都在用的AOP例子：
		1. 数据库事务操作
	对于事务操作，只能是start transaction; ....; commit;/rollback;你以为加个注解或者加个配置就能完成那么复杂的事务，这都是AOP拦截对象，做了增强
		2. 监控
	SOA架构下监控是必不可少的，这种监控怎么搞。总不能每一个接口都加上一条监控？所以都是AOP拦截统计获得
		3. spring有一个重试框架
	这个框架允许你对一个方法配置重试次数，重试时间等等，这也都是AOP的作用



#### 有哪些动态代理？

动态代理技术有：原生JDK，CGLIB，Javassist，ASM。其中，Spring常用JDK和CGLIB，

***\*动态代理实质，就是使用字节码技术，重新生成了一个新类，来达到增强的效果。\****



区别：

***\*JDK动态代理的原理是根据定义好的规则，用传入的接口创建一个新类，这就是为什么采用动态代理时为什么只能用接口引用指向代理，而不能用传入的类引用执行动态类。\****

  ***\*CGLib采用的是用创建一个继承实现类的子类，用asm库动态修改子类的代码来实现的，所以可以用传入的类引用执行代理类\****

  CGLib创建的动态代理对象性能比JDK创建的动态代理对象的性能高不少，但是CGLib在创建代理对象时所花费的时间却比JDK多得多，所以对于单例的对象，因为无需频繁创建对象，用CGLib合适，反之，使用JDK方式要更为合适一些。同时，由于CGLib由于是采用动态创建子类的方法，对于final方法，无法进行代理

