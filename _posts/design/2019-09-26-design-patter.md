---
layout: page-fullwidth
subheadline: 设计模式
title: "代理模式"
meta_teaser: "代理模式属于结构型模式。使用代理模式可以不改变原对象就对其增强，符合开闭原则。Spring aop的核心就是动态代理，所以学习代理模式是非常有必要的。"
teaser: "<em>代理模式</em> 属于结构型模式, 使用代理模式可以不改变原对象就对其增强，符合开闭原则。Spring aop的核心就是动态代理，所以学习代理模式是非常有必要的。"
header:
    image: homepage_typography.jpg
    background-color: "#262930"
    caption: 知乎链接
    caption_url: https://zhuanlan.zhihu.com/p/83088154
image:
    thumb:  homepage_typography-thumb.jpg
    homepage: homepage_typography.jpg
    caption: Image by Antonio
    caption_url: "https://zhuanlan.zhihu.com/p/83088154"
categories:
    - design
    - typography
---
<!--more-->

<div class="row">
<div class="medium-4 medium-push-8 columns" markdown="1">
<div class="panel radius" markdown="1">
**目录**
{: #toc }
*  TOC
{:toc}
</div>
</div><!-- /.medium-4.columns -->

<div class="medium-8 medium-pull-4 columns" markdown="1">

## 代理模式

在代理模式（Proxy Pattern）中，一个类代表另一个类的功能。这种类型的设计模式属于**结构型模式**。

在代理模式中，我们创建具有现有对象的对象，以便向外界提供功能接口。

### 介绍

**意图：**增强对象，但是不修改该对象。

**关键代码：**定义一个代理对象，由代理对象控制对原对象的引用。

**优点：** 高扩展性、智能化、符合开闭原则；降低业务逻辑各部分之间的耦合度 、 提高程序的可重用性。

**缺点：**可能造成系统中类的数量增加，增加了系统的复杂度。

**使用场景：** 事务控制、日志记录、性能统计、安全控制、异常处理等。

**实现方式：** 静态代理、**动态代理**。

**注意事项：** 1、和适配器模式的区别：适配器模式主要改变所考虑对象的接口，而代理模式不能改变所代理类的接口。 2、和装饰器模式的区别：装饰器模式为了增强功能，而代理模式是为了加以控制。

### 实现

#### 静态代理

##### 聚合

###### **UML图**

![](https://guanpeijia.github.io/images/blog/design/proxy/proxyuml.png)

##### **实例**

想租套房子，但是价格乱，房源难找。虽然可以自己慢慢找，但是住房中介有足够的房源，而且他们找房子这方面是专业的，所以让中介帮忙去找，自己只签合同，拿钱。

1.定义一个租房、退房的接口

```java
public interface HouseRenting {

    void renting();

    void checkedOut();
}
```

2.创建个人租房、退房的实现类

```java
public class IndividualRenting implements HouseRenting {

    @Override
    public void renting() {
        System.out.println("签合同");
        System.out.println("交租金");
        System.out.println("入住");
    }

    @Override
    public void checkedOut() {
        System.out.println("收拾个人物品");
        System.out.println("退押金");
    }
}
```

3.创建房屋中介代理类，代理租房、退房

```java
public class IntermediaryRenting implements HouseRenting {

    private HouseRenting houseRenting;

    public IntermediaryRenting(){}

    public IntermediaryRenting(HouseRenting houserenting){
        this.houseRenting = houserenting;
    }

    @Override
    public void renting() {
        System.out.println("找房源");
        System.out.println("谈合同");
        houseRenting.renting();
    }

    @Override
    public void checkedOut() {
        System.out.println("联系房东");
        System.out.println("验房");
        houseRenting.checkedOut();
        System.out.println("退房");
    }
}
```

4.测试代理

```java
public class RentingTest {

    @Test
    public void testTest(){
        HouseRenting houseRenting = new IntermediaryRenting(new IndividualRenting());
        houseRenting.renting();
    }
	//找房源
	//谈合同
	//签合同
	//交租金
	//入住
    
    @Test
    public void testCheckOut(){
        HouseRenting houseRenting = new IntermediaryRenting(new IndividualRenting());
        houseRenting.checkedOut();
    }
    //联系房东
    //验房
    //收拾个人物品
    //退押金
    //退房
}
```

##### 小结

使用聚合实现代理，代理对象和目标对象实现相同的接口，这样两个对象就拥有了所有接口方法，保证了对象内部结构一致。客户端调用的时候，不再实例化目标对象，而是实例化拥有目标对象实例的代理对象（构造函数或者set传入）。代理对象方法调用目标对象实例的相同方法前后可加入增强逻辑，实现代理。

#### 继承

##### 实例

还是上面那个实例，使用继承来增强。

1.建立代理类继承上例中的个人租房、退房的实现类

```java
public class IndividualRentingProxy extends IndividualRenting {

    @Override
    public void renting() {
        System.out.println("找房源");
        System.out.println("谈合同");
        super.renting();
    }

    @Override
    public void checkedOut() {
        System.out.println("联系房东");
        System.out.println("验房");
        super.checkedOut();
        System.out.println("退房");
    }
}
```

2.客户端调用

```java
	@Test
    public void IndividualRentingProxy(){
        IndividualRenting houseRenting = new IndividualRentingProxy();
        houseRenting.checkedOut();
        System.out.println("=================================");
        houseRenting.renting();
    }

//输出
联系房东
验房
收拾个人物品
退押金
退房
=================================
找房源
谈合同
签合同
交租金
入住
```

##### 小结：

使用继承实现代理的关键是不完全重写父类的方法，而是重写方法中调用父类方法（super.），加入增强逻辑。两种实现方式各有自己的优缺点，但是我们使用设计模式，要少用继承，多用组合，这样会有更好的扩展性。

代理模式的目的是增强目标对象，实现方式很多。以上两种是静态代理的实现，都有一个共同的缺点，每增加一个代理，系统中类的数量就会增加，增加了系统的复杂度。

### 动态代理

现在想要对service中所有的crud方法都加入事务控制或者对项目中所有的方法都加一条记录运行时间的日志，这种应用场景，静态代理就不再适合了。静态代理虽然实现了代理，但是缺点非常明显，委托类每增加一个方法，代理类也必须实现相同的方法，而且一个代理类只适用于服务于一种类型的对象。但是如果我们想对某类方法或者所有类的方法都需要增强的时候，静态代理显然不太适用。

Spring中动态代理使用的代理有两种：JDK动态代理和CGLib动态代理。

- JDK动态代理是**面向接口**的。

- CGLib动态代理是通过字节码底层**继承**要来实现（代理类不能被final关键字所修饰）

#### JDK动态代理

##### JDK动态代理核心代码

对于批量类的操作，最容易想到的是反射机制。JDK动态代理利用反射的特性，提供了`java.lang.reflect.Proxy`类和`java.lang.reflect.InvocationHandler`接口。通过这两个类的相互配合，可以实现动态代理。JDK动态代理，"**目标对象**"必须实现接口，以保证代理对象和目标对象的内部结构一致。

**`Proxy`的主要方法是`newProxyInstance`**

```java
// 贴一部分核心代码，实现过程比较复杂，建议debug跟一下。虽然有native和未开源的代码，但核心流程都可见，对于理解jdk动态代理帮助还是挺大的

/**
 * 返回指定接口的代理类的实例，该接口将方法调用分派给指定的调用处理程序
 * 该方法需要传三个参数：类加载器、类实现的接口、和一个InvocationHandler的实现类
 * InvocationHandler实现类的代码就是增强目标对象的具体逻辑
 */
@CallerSensitive
public static Object newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)
    throws IllegalArgumentException
{
   ... ...
   ... ...
   // 在缓存中查找或者生成代理类的class对象
    Class<?> cl = getProxyClass0(loader, intfs);
	... ...
	... ...
	// private static final Class<?>[] constructorParams = { InvocationHandler.class };
	// 构建代理类的构造器
	final Constructor<?> cons = cl.getConstructor(constructorParams);
    final InvocationHandler ih = h;
    ... ...
    // 构建代理类实例，将增强处理器传入
    return cons.newInstance(new Object[]{h});
 }
 
private static Class<?> getProxyClass0(ClassLoader loader,Class<?>... interfaces) {
    ... ...
    ... ...
        // 上面提到的缓存是一个WeakCache对象，也就是这里的proxyClassCache
        // WeakCache中定义了一个ConcurrentMap来缓存代理对象
        //private final ConcurrentMap<Object, ConcurrentMap<Object, Supplier<V>>> map= new ConcurrentHashMap<>();
        return proxyClassCache.get(loader, interfaces);
    }

public V get(K key, P parameter) {
    	... ...
       // 生成代理类的字节码
       Object subKey = Objects.requireNonNull(subKeyFactory.apply(key, parameter)); 
    }

@Override
public Class<?> apply(ClassLoader loader, Class<?>[] interfaces) {
    // 代理类的命名
    long num = nextUniqueNumber.getAndIncrement();
    String proxyName = proxyPkg + proxyClassNamePrefix + num;
    // ProxyGenerator在sun.misc包下，该包未开原，通过下面方法即可生成代理类的字节码
    byte[] proxyClassFile = ProxyGenerator.generateProxyClass(proxyName, interfaces, accessFlags);

}
```

核心流程大约：

- 缓存中查找改目标对象的代理对象，有则返回，没有则开始创建
- 缓存中没有找到，拼接代理类包名和类名，生成.class 文件的字节码
- 找到代理对象或者已经创建完成，则创建代理类的构造器
- 将`InvocationHandler`传入，实例化代理对象

**`InvocationHandler`接口的主要方法是`invoke`**。该方法是实现增强的具体逻辑

```java
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable;
```

![flow.png](https://guanpeijia.github.io/images/blog/design/proxy/00b1cfa41ebe945af2ee2aa7d394ee45.png)

这部分比较晦涩，加之贴的代码较少，没读过源码的话，可能不太容易理解。不过没有关系，我们最主要的**会用**JDK动态代理来实现我们的业务逻辑。没读懂的话，请暂时忘记上文的源码介绍，源码实现可能比较复杂，但是直接使用`JDK`的`API`来实现一个动态代理的话，却是非常简单的。下面就使用`JDK动态代理`来实现上文提到的将项目中所有的方法都加一条记录运行时间日志的功能。看完这个实例，再回头看源码的话，可能会容易理解些。

##### JDK动态代理实例

1.目标对象必须实现接口。定义一个操作用户表的一个接口`CustomerDao`

```java
public interface CustomerDao {

    int save();

    int update();

    int delete();
}
```

2.目标类实现该接口

```java
public class CustomerDaoImpl implements CustomerDao {

    @Override
    public int save() {
        System.out.println("保存");
        return 0;
    }

    @Override
    public int update() {
        System.out.println("更新");
        return 0;
    }

    @Override
    public int delete() {
        System.out.println("删除");
        return 0;
    }
}
```

3.定义记录日志动态代理工厂，创建记录日志代理对象

```java
public class LogProxyFactory {

    public Object getProxyInstance(Object target){
        // 调用JDK `Proxy` 动态代理方法
        return Proxy.newProxyInstance(target.getClass().getClassLoader(),
            target.getClass().getInterfaces(),new LogInvocationHandler(target));
    }
}

```

4. 实现`InvocationHandler`接口，来具体实现记录日志的逻辑

```java
public class LogInvocationHandler implements InvocationHandler {

    // 目标对象
    private Object target;

    // 构造函数传递目标对象
    public LogInvocationHandler(Object target){
        this.target = target;
    }

    // 增强目标对象，利用反射调用目标对象需要增强的方法
    // 需要增强目标对象那些方法，如何增强，逻辑在此处编写。JDK动态代理会动态生成字节码
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("开始时间："+new Date());
        Object invoke = method.invoke(target, args);
        System.out.println("结束时间："+new Date());
        return invoke;
    }
}
```

5.客户端调用

```java
@Test
public void customerDaoTest() {
    // 直接调用目标对象
    CustomerDao customerDao = new CustomerDaoImpl();
    System.out.println(customerDao.getClass());
    customerDao.save();
    customerDao.delete();
    customerDao.update();
    // 调用目标对象的代理对象
    CustomerDao customerDaoProxy= (CustomerDao) new LogProxyFactory().getProxyInstance(customerDao);
    System.out.println(customerDaoProxy.getClass());
    customerDaoProxy.save();
    customerDaoProxy.delete();
    customerDaoProxy.update();
}

// 输出打印
class com.gpj.dynamicproxy.biz.impl.CustomerDaoImpl
保存
删除
更新
class com.sun.proxy.$Proxy2
开始时间：Sat Sep 14 23:27:15 CST 2019
保存
结束时间：Sat Sep 14 23:27:15 CST 2019
开始时间：Sat Sep 14 23:27:15 CST 2019
删除
结束时间：Sat Sep 14 23:27:15 CST 2019
开始时间：Sat Sep 14 23:27:15 CST 2019
更新
结束时间：Sat Sep 14 23:27:15 CST 2019
```

​		上述实例，目的是增强`customerDao`，增加记录日志。所以不再直接创建该实例（`new CustomerDao`），而是通过`new LogProxyFactory().getProxyInstance(customerDao)`,传入`customerDao`目标对象,利用聚合的方式，使用`JDK动态代理`来动态的创建了一个`CustomerDao`的代理对象。如果想增强`GoodsDao`,则只需传入`GoodsDao`的目标对象即可，实现起来非常简单。

​		具体如何增强目标对象，逻辑写在`InvocationHandler`的实现类`LogInvocationHandler`里面。

​		代理对象是动态生成的，这样就防止了项目中类爆炸的情况，具体怎么生成交给`JDK`。上述代码打印出该代理对象得到`class com.sun.proxy.$Proxy2`，具体命名规则追溯源码中` String proxyName = proxyPkg + proxyClassNamePrefix + num`。

##### 反编译代理类$Proxy2

JDK动态代理生成的class到底是什么样子呢？下面，通过获取JDK动态生成的class文件，反编译来看一下，里面到底是什么样子的。

`ProxyGenerator`类中有一个属性`saveGeneratedFiles`,当值为true时，会将动态生成的class文件保存在本地磁盘。

```java
private static final boolean saveGeneratedFiles = (Boolean)AccessController.doPrivileged(new GetBooleanAction("sun.misc.ProxyGenerator.saveGeneratedFiles"));
```

**弃用junit测试类**，写一个main函数来测试上面的实例，则会再项目根路径生成该包和class文件

```java
public static void main(String[] args) {
    System.getProperties().put("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");

    CustomerDao customerDao = new CustomerDaoImpl();

    CustomerDao customerDaoProxy= (CustomerDao) new LogProxyFactory().getProxyInstance(customerDao);
    System.out.println(customerDaoProxy.getClass());
    customerDaoProxy.save();
    customerDaoProxy.delete();
    customerDaoProxy.update();
}
```

反编译查看该class文件

```java
package com.sun.proxy;

import com.gpj.dynamicproxy.biz.CustomerDao;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;

public final class $Proxy0 extends Proxy implements CustomerDao {
    private static Method m1;
    private static Method m5;
    private static Method m2;
    private static Method m4;
    private static Method m0;
    private static Method m3;

    // 构造函数需要传入InvocationHandler增强处理器，如何传入的呢？
    // 再看看实例化代理对象的源码 return cons.newInstance(new Object[]{h});
    public $Proxy0(InvocationHandler var1) throws  {
        super(var1);
    }
	
    // 重写了Object的equals方法
    public final boolean equals(Object var1) throws  {
        try {
            return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }

    // 调用构造函数传入InvocationHandler的invoke方法，将初始化后的Method方法传入
    public final int save() throws  {
        try {
            return (Integer)super.h.invoke(this, m5, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final String toString() throws  {
        try {
            return (String)super.h.invoke(this, m2, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final int delete() throws  {
        try {
            return (Integer)super.h.invoke(this, m4, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final int hashCode() throws  {
        try {
            return (Integer)super.h.invoke(this, m0, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final int update() throws  {
        try {
            return (Integer)super.h.invoke(this, m3, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    //静态代码块利用反射来初始化接口中方法的Method对象
    static {
        try {
            m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
            m5 = Class.forName("com.gpj.dynamicproxy.biz.CustomerDao").getMethod("save");
            m2 = Class.forName("java.lang.Object").getMethod("toString");
            m4 = Class.forName("com.gpj.dynamicproxy.biz.CustomerDao").getMethod("delete");
            m0 = Class.forName("java.lang.Object").getMethod("hashCode");
            m3 = Class.forName("com.gpj.dynamicproxy.biz.CustomerDao").getMethod("update");
        } catch (NoSuchMethodException var2) {
            throw new NoSuchMethodError(var2.getMessage());
        } catch (ClassNotFoundException var3) {
            throw new NoClassDefFoundError(var3.getMessage());
        }
    }
}

```

 通过反编译我们可以看到：

- 动态代理产生的对象重写接口里面所有方法以及Object的equals、hashCode、toString方法
- 静态代码块利用反射来初始化接口中方法的Method对象，将Method传入InvocationHandler的invoke方法
- 构造函数将InvocationHandler传入
- 实际执行的逻辑其实是自己定义的LogInvocationHandler里面的invoke方法逻辑，所以具体怎么增强目标对象，只需完善自己实现的InvocationHandler的invoke方法即可

根据示例，反过来再看源码的话，应该就会轻松很多。



#### CGLIB动态代理

##### 简单介绍

使用JDK动态代理有一个前提条件：目标对象必须实现接口。但是当想增强一个没有实现接口的对象时，JDK动态代理则不再适用。Spring就是使用的cglib代码生成库来代理增强没有实现接口的对象。

CGLIB(Code Generation Library)是一个[开源项目](https://github.com/cglib/cglib)！是一个强大的，高性能，高质量的Code生成类库，它可以在运行期扩展Java类与实现Java接口。它广泛的被许多AOP的框架使用，例如Spring AOP和dynaop。CGLIB包的底层是通过使用一个小而快的[字节码](https://baike.baidu.com/item/字节码)处理框架ASM，来转换字节码并生成新的类。

![image](https://guanpeijia.github.io/images/blog/design/proxy/jnbNov2005-1.PNG)

##### CGLIB动态代理实例

还是JDK动态代理那个示例，使用cglib实现代码如下

1. 引入jar包

```xml
<dependency>
      <groupId>cglib</groupId>
      <artifactId>cglib</artifactId>
      <version>3.3.0</version>
</dependency>
```

​	2.定义一个操作用户表目标对象

```java
public class CustomerServiceImpl {

    public int save() {
        System.out.println("保存");
        return 0;
    }

    public int update() {
        System.out.println("更新");
        return 0;
    }

    public int delete() {
        System.out.println("删除");
        return 0;
    }
}
```

​	3.创建增强工厂，实现`MethodInterceptor`接口，重写回调方法`intercept`

```java
public class LogProxyFactory implements MethodInterceptor {

    private Object target;

    public LogProxyFactory(Object target) {
        this.target = target;
    }

    public Object myCglibCreator(){
        Enhancer enhancer = new Enhancer();
        //注意，cglib的原理是子类增强父类，所以使用cglib动态代理，要求目标类不能是final
        //指定父类
        enhancer.setSuperclass(target.getClass());
        // 设置回调接口对象
        enhancer.setCallback(this);
        //create
        return  enhancer.create();
    }


    /**
     * 回调接口方法
     *          enhancer.setCallback(this);
     * @param o
     * @param method
     * @param args
     * @param methodProxy
     * @return
     * @throws Throwable
     */
    @Override
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy)
        throws Throwable {
        System.out.println("开始时间："+new Date());
        Object invoke = method.invoke(target, args);
        System.out.println("结束时间："+new Date());
        return invoke;
    }
}

```

​	4.客戶端調用

```java
public class CglibProxyTest {

    @Test
    public void test(){
        CustomerServiceImpl customerService = new CustomerServiceImpl();

        CustomerServiceImpl customerDaoProxy= (CustomerServiceImpl) new LogProxyFactory(customerService).myCglibCreator();
        System.out.println(customerDaoProxy.getClass());
        customerDaoProxy.save();
        customerDaoProxy.delete();
        customerDaoProxy.update();
    }

}

//输出日志
class com.gpj.cglibproxy.biz.impl.CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d
开始时间：Sat Sep 21 20:40:03 CST 2019
保存
结束时间：Sat Sep 21 20:40:03 CST 2019
开始时间：Sat Sep 21 20:40:03 CST 2019
删除
结束时间：Sat Sep 21 20:40:03 CST 2019
开始时间：Sat Sep 21 20:40:03 CST 2019
更新
结束时间：Sat Sep 21 20:40:03 CST 2019

```

cglib动态代理的实现也主要是一个类`Enhancer`和一个接口`MethodInterceptor`

- `Enhancer`增强器，来创建代理对象。创建对象主要有三步
  1. 指定父类（目标对象）`public void setSuperclass(Class superclass)`
  2. 设置回调接口对象 `public void setCallback(Callback callback)`
  3. 创建对象 `public Object create()`
- `MethodInterceptor`方法拦截器。该接口继承`Callback`接口，有一个待实现的`intercept`方法，该方法是增强目标对象具体逻辑的实现。

```java
public interface MethodInterceptor extends Callback {
    Object intercept(Object var1, Method var2, Object[] var3, MethodProxy var4) throws Throwable;
}
```

- 生成的代理类命名规则

```java
  String base =
            prefix + "$$" + 
            source.substring(source.lastIndexOf('.') + 1) +
            getTag() + "$$" +
            Integer.toHexString(STRESS_HASH_CODE ? 0 : key.hashCode());
```

- cglib中`DebuggingClassWriter`类继承了asm中的`ClassVisitor`类。ASM直接操作字节码，生成代理对象class文件

```java
byte[] b = ((ClassWriter) DebuggingClassWriter.super.cv).toByteArray();
```

通过这两个类，就可以实现无接口对象的动态代理。cglib动态代理不仅支持代理无实现接口的目标对象，也支持实现接口的对象。

##### 反编译代理类

```java
 @Test
    public void testPoxy(){
        // 1.设置系统变量，保存到本地
        //System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY, "D:\\cglib");
        CustomerServiceImpl customerService = new CustomerServiceImpl();

        CustomerServiceImpl customerDaoProxy= (CustomerServiceImpl) new LogProxyFactory(customerService).myCglibCreator();
        customerDaoProxy.save();
        // 使用ProxyGenerator保存到本地
        byte[] bytes = ProxyGenerator.generateProxyClass("CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d", new Class[]{CustomerServiceImpl.class});
        try(
            FileOutputStream fos =new FileOutputStream(new File("D:/$Proxy.class"))
        ){
            fos.write(bytes);
            fos.flush();
        }catch (Exception e){
            e.printStackTrace();
        }
    }
```

可以设置`System`系统属性或者使用`ProxyGenerator`, 获取生成的字节码，将字节码保存到本地

反编译class文件

```java
package com.gpj.cglibproxy.biz.impl;

import java.lang.reflect.Method;
import net.sf.cglib.core.ReflectUtils;
import net.sf.cglib.core.Signature;
import net.sf.cglib.proxy.Callback;
import net.sf.cglib.proxy.Factory;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

public class CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d extends CustomerServiceImpl implements Factory {
    private boolean CGLIB$BOUND;
    public static Object CGLIB$FACTORY_DATA;
    private static final ThreadLocal CGLIB$THREAD_CALLBACKS;
    private static final Callback[] CGLIB$STATIC_CALLBACKS;
    private MethodInterceptor CGLIB$CALLBACK_0;
    private static Object CGLIB$CALLBACK_FILTER;
    private static final Method CGLIB$update$0$Method;
    private static final MethodProxy CGLIB$update$0$Proxy;
    private static final Object[] CGLIB$emptyArgs;
    private static final Method CGLIB$delete$1$Method;
    private static final MethodProxy CGLIB$delete$1$Proxy;
    private static final Method CGLIB$save$2$Method;
    private static final MethodProxy CGLIB$save$2$Proxy;
    private static final Method CGLIB$equals$3$Method;
    private static final MethodProxy CGLIB$equals$3$Proxy;
    private static final Method CGLIB$toString$4$Method;
    private static final MethodProxy CGLIB$toString$4$Proxy;
    private static final Method CGLIB$hashCode$5$Method;
    private static final MethodProxy CGLIB$hashCode$5$Proxy;
    private static final Method CGLIB$clone$6$Method;
    private static final MethodProxy CGLIB$clone$6$Proxy;

    static void CGLIB$STATICHOOK1() {
        CGLIB$THREAD_CALLBACKS = new ThreadLocal();
        CGLIB$emptyArgs = new Object[0];
        Class var0 = Class.forName("com.gpj.cglibproxy.biz.impl.CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d");
        Class var1;
        Method[] var10000 = ReflectUtils.findMethods(new String[]{"equals", "(Ljava/lang/Object;)Z", "toString", "()Ljava/lang/String;", "hashCode", "()I", "clone", "()Ljava/lang/Object;"}, (var1 = Class.forName("java.lang.Object")).getDeclaredMethods());
        CGLIB$equals$3$Method = var10000[0];
        CGLIB$equals$3$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/Object;)Z", "equals", "CGLIB$equals$3");
        CGLIB$toString$4$Method = var10000[1];
        CGLIB$toString$4$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/String;", "toString", "CGLIB$toString$4");
        CGLIB$hashCode$5$Method = var10000[2];
        CGLIB$hashCode$5$Proxy = MethodProxy.create(var1, var0, "()I", "hashCode", "CGLIB$hashCode$5");
        CGLIB$clone$6$Method = var10000[3];
        CGLIB$clone$6$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/Object;", "clone", "CGLIB$clone$6");
        var10000 = ReflectUtils.findMethods(new String[]{"update", "()I", "delete", "()I", "save", "()I"}, (var1 = Class.forName("com.gpj.cglibproxy.biz.impl.CustomerServiceImpl")).getDeclaredMethods());
        CGLIB$update$0$Method = var10000[0];
        CGLIB$update$0$Proxy = MethodProxy.create(var1, var0, "()I", "update", "CGLIB$update$0");
        CGLIB$delete$1$Method = var10000[1];
        CGLIB$delete$1$Proxy = MethodProxy.create(var1, var0, "()I", "delete", "CGLIB$delete$1");
        CGLIB$save$2$Method = var10000[2];
        CGLIB$save$2$Proxy = MethodProxy.create(var1, var0, "()I", "save", "CGLIB$save$2");
    }

    final int CGLIB$update$0() {
        return super.update();
    }

    public final int update() {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            Object var1 = var10000.intercept(this, CGLIB$update$0$Method, CGLIB$emptyArgs, CGLIB$update$0$Proxy);
            return var1 == null ? 0 : ((Number)var1).intValue();
        } else {
            return super.update();
        }
    }

    final int CGLIB$delete$1() {
        return super.delete();
    }

    public final int delete() {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            Object var1 = var10000.intercept(this, CGLIB$delete$1$Method, CGLIB$emptyArgs, CGLIB$delete$1$Proxy);
            return var1 == null ? 0 : ((Number)var1).intValue();
        } else {
            return super.delete();
        }
    }

    final int CGLIB$save$2() {
        return super.save();
    }

    public final int save() {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            Object var1 = var10000.intercept(this, CGLIB$save$2$Method, CGLIB$emptyArgs, CGLIB$save$2$Proxy);
            return var1 == null ? 0 : ((Number)var1).intValue();
        } else {
            return super.save();
        }
    }

    final boolean CGLIB$equals$3(Object var1) {
        return super.equals(var1);
    }

    public final boolean equals(Object var1) {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            Object var2 = var10000.intercept(this, CGLIB$equals$3$Method, new Object[]{var1}, CGLIB$equals$3$Proxy);
            return var2 == null ? false : (Boolean)var2;
        } else {
            return super.equals(var1);
        }
    }

    final String CGLIB$toString$4() {
        return super.toString();
    }

    public final String toString() {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        return var10000 != null ? (String)var10000.intercept(this, CGLIB$toString$4$Method, CGLIB$emptyArgs, CGLIB$toString$4$Proxy) : super.toString();
    }

    final int CGLIB$hashCode$5() {
        return super.hashCode();
    }

    public final int hashCode() {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            Object var1 = var10000.intercept(this, CGLIB$hashCode$5$Method, CGLIB$emptyArgs, CGLIB$hashCode$5$Proxy);
            return var1 == null ? 0 : ((Number)var1).intValue();
        } else {
            return super.hashCode();
        }
    }

    final Object CGLIB$clone$6() throws CloneNotSupportedException {
        return super.clone();
    }

    protected final Object clone() throws CloneNotSupportedException {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        return var10000 != null ? var10000.intercept(this, CGLIB$clone$6$Method, CGLIB$emptyArgs, CGLIB$clone$6$Proxy) : super.clone();
    }

    public static MethodProxy CGLIB$findMethodProxy(Signature var0) {
        String var10000 = var0.toString();
        switch(var10000.hashCode()) {
        case -1949253121:
            if (var10000.equals("update()I")) {
                return CGLIB$update$0$Proxy;
            }
            break;
        case -508378822:
            if (var10000.equals("clone()Ljava/lang/Object;")) {
                return CGLIB$clone$6$Proxy;
            }
            break;
        case -358764067:
            if (var10000.equals("delete()I")) {
                return CGLIB$delete$1$Proxy;
            }
            break;
        case 1826985398:
            if (var10000.equals("equals(Ljava/lang/Object;)Z")) {
                return CGLIB$equals$3$Proxy;
            }
            break;
        case 1872760011:
            if (var10000.equals("save()I")) {
                return CGLIB$save$2$Proxy;
            }
            break;
        case 1913648695:
            if (var10000.equals("toString()Ljava/lang/String;")) {
                return CGLIB$toString$4$Proxy;
            }
            break;
        case 1984935277:
            if (var10000.equals("hashCode()I")) {
                return CGLIB$hashCode$5$Proxy;
            }
        }

        return null;
    }

    public CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d() {
        CGLIB$BIND_CALLBACKS(this);
    }

    public static void CGLIB$SET_THREAD_CALLBACKS(Callback[] var0) {
        CGLIB$THREAD_CALLBACKS.set(var0);
    }

    public static void CGLIB$SET_STATIC_CALLBACKS(Callback[] var0) {
        CGLIB$STATIC_CALLBACKS = var0;
    }

    private static final void CGLIB$BIND_CALLBACKS(Object var0) {
        CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d var1 = (CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d)var0;
        if (!var1.CGLIB$BOUND) {
            var1.CGLIB$BOUND = true;
            Object var10000 = CGLIB$THREAD_CALLBACKS.get();
            if (var10000 == null) {
                var10000 = CGLIB$STATIC_CALLBACKS;
                if (var10000 == null) {
                    return;
                }
            }

            var1.CGLIB$CALLBACK_0 = (MethodInterceptor)((Callback[])var10000)[0];
        }

    }

    public Object newInstance(Callback[] var1) {
        CGLIB$SET_THREAD_CALLBACKS(var1);
        CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d var10000 = new CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d();
        CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
        return var10000;
    }

    public Object newInstance(Callback var1) {
        CGLIB$SET_THREAD_CALLBACKS(new Callback[]{var1});
        CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d var10000 = new CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d();
        CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
        return var10000;
    }

    public Object newInstance(Class[] var1, Object[] var2, Callback[] var3) {
        CGLIB$SET_THREAD_CALLBACKS(var3);
        CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d var10000 = new CustomerServiceImpl$$EnhancerByCGLIB$$7a67ad5d;
        switch(var1.length) {
        case 0:
            var10000.<init>();
            CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
            return var10000;
        default:
            throw new IllegalArgumentException("Constructor not found");
        }
    }

    public Callback getCallback(int var1) {
        CGLIB$BIND_CALLBACKS(this);
        MethodInterceptor var10000;
        switch(var1) {
        case 0:
            var10000 = this.CGLIB$CALLBACK_0;
            break;
        default:
            var10000 = null;
        }

        return var10000;
    }

    public void setCallback(int var1, Callback var2) {
        switch(var1) {
        case 0:
            this.CGLIB$CALLBACK_0 = (MethodInterceptor)var2;
        default:
        }
    }

    public Callback[] getCallbacks() {
        CGLIB$BIND_CALLBACKS(this);
        return new Callback[]{this.CGLIB$CALLBACK_0};
    }

    public void setCallbacks(Callback[] var1) {
        this.CGLIB$CALLBACK_0 = (MethodInterceptor)var1[0];
    }

    static {
        CGLIB$STATICHOOK1();
    }
}

```

 通过反编译我们可以看到：

- 动态代理产生的对象继承了目标对象
- 动态代理产生的对象增强的方法都是final方法，且增强了Objecct的equals、hashCode、toString、clone等方法。比JDK代理多了clone。
- 实际执行的逻辑其实是自己定义的LogProxyFactory里面的intercept方法逻辑，所以具体怎么增强目标对象，只需完善自己实现的LogProxyFactory的intercept方法即可

#### 小结

- Spring动态代理就是使用了以上两种动态代理方式。当目标对象是实现类时候，Spring默认会使用JDK动态代理；当目标对象没有实现接口时，则Spring会使用CGLIB动态代理来增强对象。
- JDK动态代理只能代理实现类，而CGLIB可以代理实现类，也可以代理非实现类。
- CGLIB使用的是继承，所以目标对象不能被final关键字所修饰，且必须有无参构造器。static、private、final等修饰符所修饰的方法不能被代理。
- JDK1.8以后，JDK动态代理处理速度已明显快于CGLIB动态代理

#### AspectJ动态代理

##### 简单介绍

- [AspectJ](https://www.eclipse.org/aspectj/)是Eclipse旗下一个面向切面的框架，它扩展了Java语言。AspectJ定义了AOP语法，它有一个专门的编译器用来生成遵守Java字节编码规范的Class文件。
- AspectJ是目前AOP框架中最成熟，功能最丰富的语音。AspectJ提出了切入点(pointcut)、通知(advice)、切面(aspect)、织入(weaving)等概念。AspectJ已成为AOP的广泛使用的标准

##### Spring AOP 和 AspectJ 的区别和联系

- Spring AOP是用纯Java实现的。不需要特殊的编译过程。Spring AOP不需要控制类加载器的层次结构，因此适合在Servlet容器或应用程序服务器中使用。
- Spring AOP当前仅支持方法执行连接点（建议在Spring Bean上执行方法）。尽管可以在不破坏核心Spring AOP API的情况下添加对字段拦截的支持，但并未实现字段拦截。如果需要建议字段访问和更新连接点，请考虑使用诸如AspectJ之类的语言。
- Spring无缝地将Spring AOP和IoC与AspectJ集成在一起，以在基于Spring的一致应用程序架构中支持AOP的所有使用。
- Spring支持@AspectJ注解，但是，AOP运行时仍然是纯Spring AOP，并且不依赖于AspectJ编译器或编织器。即Spring AOP即使使用了@AspectJ注解，实现还是JDK动态代理或者CGLIB动态代理。

</div>