# Spring

## Spring 事务

>  Tip：程序是否支持事务首先取决于数据库，比如使用MySQL的话，如果你选择的是InnoDB引擎，那么恭喜你，是可以支持事务的。但是，如果你的MySQL数据库使用的是MyISAM的话，那不好意思，从根本上就不支持事务。

### Spring支持两种方式的事务管理

- 编程式事务管理
- 声明式事务管理

#### 编程式事务管理

通过`TransactionTemplate`或者`TransactionManager`手动管理事务，实际应用中很少使用，但是对于你理解Spring事务管理原理有帮助。

使用`TransactionTemplate`进行编程式事务管理的实例代码如下：

```java
@Autowired
private TransactionTemplate transactionTemplate;
public void testTransaction() {

        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {

                try {

                    // ....  业务代码
                } catch (Exception e){
                    //回滚
                    transactionStatus.setRollbackOnly();
                }

            }
        });
}
```

使用`TransactionManager`进行编程式事务管理的示例代码如下：

```java
@Autowired
private PlatformTransactionManager transactionManager;

public void testTransaction() {

  TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
          try {
               // ....  业务代码
              transactionManager.commit(status);
          } catch (Exception e) {
              transactionManager.rollback(status);
          }
}
```

#### 声明式事务管理

推荐使用（代码入侵最小），实际是通过AOP实现的（基于`@Transactional`的全注解方式使用最多）。

使用`@Transactional`注解进行事务管理的示例代码如下：

```java
@Transactional(propagation = Propagation.REQUIRED)
public void aMethod {
  //do something
  B b = new B();
  C c = new C();
  b.bMethod();
  c.cMethod();
}
```

### Spring 事务管理接口

Spring框架中，事务管理相关最重要的三个接口如下：

- `PlatfromTransactionManager`：（平台）事务管理器，Spring事务策略的核心；
- `TransactionDefinition`：事务定义信息（事务隔离级别、传播行为、超时、只读、回滚规则）；
- `TransactionStatus`：事务运行状态。

我们可以把`PlatfromTransactionManager`接口可以被看作是上层的管理者，而`TransactionDefinition`和`TransactionStatus`这两个接口可以看作是事务的描述。

`PlatfromTransactionManager`会根据`TransactionDefinintion`的定义比如事务超时时间、隔离级别、传播行为等来进行事务管理，而`TransactionStatus`接口则提供一些方法来获取事务相应的状态比如是否是新事物、是否可以回滚等等。

#### PlatfromTransactionManager：事务管理接口

Spring并不直接管理事务，而是提供了多种事务管理器。Spring事务管理器的接口是：`PlatfromTransactionManager`。

通过这个接口，Spring为各个平台如：JDBC（DataSourceTransactionManager）、Hibernate（HibernateTransactionManager）、JPA（JpafromTransactionManager）等都提供了对应的事务管理器，但是具体的实现是各个平台自己的事情了。

`PlatfromTransactionMagener`接口定义了三个方法：

```java
package org.springframework.transaction;

import org.springframework.lang.Nullable;

public interface PlatformTransactionManager {
    //获得事务
    TransactionStatus getTransaction(@Nullable TransactionDefinition var1) throws TransactionException;
    //提交事务
    void commit(TransactionStatus var1) throws TransactionException;
    //回滚事务
    void rollback(TransactionStatus var1) throws TransactionException;
}
```

这里多插一嘴，为什么要定义或者抽象出来`PlatfromTransactionManager`这个接口呢？

主要是因为要将事务管理行为抽象出来，然后不同的平台去实现它，这样我们可以保证提供给外部的行为不变，方便我们拓展。

#### TransactionDefinition：事务属性

事务管理接口`PlatformTransactionManager`通过`gerTransaction（TransactionDefinition definition）`方法来得到一个事务，这个方法里的参数是`TransactionDefinition`类，这个类就定义了一些基本的事务属性。

什么是事务属性呢？事务属性可以理解成事务的一些基本配置，描述了事务策略如何应用到方法上。

事务属性包含了5个方面：

- 隔离级别
- 传播行为
- 回滚规则
- 是否只读
- 事务超时

`TransactionDefinition`接口中定义了5个方法以及一些表示事务熟悉的常量比如隔离级别、传播行为等等。

```java
package org.springframework.transaction;

import org.springframework.lang.Nullable;

public interface TransactionDefinition {
    int PROPAGATION_REQUIRED = 0;
    int PROPAGATION_SUPPORTS = 1;
    int PROPAGATION_MANDATORY = 2;
    int PROPAGATION_REQUIRES_NEW = 3;
    int PROPAGATION_NOT_SUPPORTED = 4;
    int PROPAGATION_NEVER = 5;
    int PROPAGATION_NESTED = 6;
    int ISOLATION_DEFAULT = -1;
    int ISOLATION_READ_UNCOMMITTED = 1;
    int ISOLATION_READ_COMMITTED = 2;
    int ISOLATION_REPEATABLE_READ = 4;
    int ISOLATION_SERIALIZABLE = 8;
    int TIMEOUT_DEFAULT = -1;
    // 返回事务的传播行为，默认值为 REQUIRED。
    int getPropagationBehavior();
    //返回事务的隔离级别，默认值是 DEFAULT
    int getIsolationLevel();
    // 返回事务的超时时间，默认值为-1。如果超过该时间限制但事务还没有完成，则自动回滚事务。
    int getTimeout();
    // 返回是否为只读事务，默认值为 false
    boolean isReadOnly();

    @Nullable
    String getName();
}
```

#### TransactionStatus：事务状态

`TransactionStatus`接口用来记录事务的状态，该接口定义了一组方法，用来获取或判断事务的相应状态信息。

`PlatformTransactionManager.getTransaction(...)`方法返回一个`TransactionStatus`对象。

TransactionStatus接口内容如下：

```java
public interface TransactionStatus{
    boolean isNewTransaction(); // 是否是新的事务
    boolean hasSavepoint(); // 是否有恢复点
    void setRollbackOnly();  // 设置为只回滚
    boolean isRollbackOnly(); // 是否为只回滚
    boolean isCompleted; // 是否已完成
}
```

### 事务属性详解

#### 事务传播行为

事务传播行为是为了解决业务层方法之间相互调用的事务问题。

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续现有事务中运行，也可能新开启一个事务，并在自己的事务中运行。

在`TransactionDefiniton`定义中包括了以下几个表示传播行为的常量：

```java
public interface TransactionDefinition {
    int PROPAGATION_REQUIRED = 0;
    int PROPAGATION_SUPPORTS = 1;
    int PROPAGATION_MANDATORY = 2;
    int PROPAGATION_REQUIRES_NEW = 3;
    int PROPAGATION_NOT_SUPPORTED = 4;
    int PROPAGATION_NEVER = 5;
    int PROPAGATION_NESTED = 6;
    ......
}
```

不过，为了方便使用，Spring想应地定义了一个枚举类：Propagation

```java
package org.springframework.transaction.annotation;

import org.springframework.transaction.TransactionDefinition;

public enum Propagation {

    REQUIRED(TransactionDefinition.PROPAGATION_REQUIRED),

    SUPPORTS(TransactionDefinition.PROPAGATION_SUPPORTS),

    MANDATORY(TransactionDefinition.PROPAGATION_MANDATORY),

    REQUIRES_NEW(TransactionDefinition.PROPAGATION_REQUIRES_NEW),

    NOT_SUPPORTED(TransactionDefinition.PROPAGATION_NOT_SUPPORTED),

    NEVER(TransactionDefinition.PROPAGATION_NEVER),

    NESTED(TransactionDefinition.PROPAGATION_NESTED);

    private final int value;

    Propagation(int value) {
        this.value = value;
    }

    public int value() {
        return this.value;
    }

}
```

正确的事务传播行为可能的值如下：

1. TransactionDefinition.PROPAGATION_REQUIRED

   > 使用最多的一个事务传播行为，我们平常经常使用的`@Transactional`注解默认使用的就是这个事务传播行为。如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

   - 如果外部方法没有开启事务的话，`PROPAGATION_REQUIRED`修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。
   - 如果外部方法开启事务并且被`PROPAGATION_REQUIRED`的话，所有`PROPAGATION_REQUIRED`修饰的内部方法和外部方法均属于同一事务，只要一个方法回滚，整个事务均回滚。

2. TransactionDefinition.PROPAGETION_REQUIRES_NEW

   > 创建一个新的事务，如果当前存在事务，则把当前事务挂起。也就是说不管外部方法是否开启事务，`PROPAGATION.REQUIRED_NEW`修饰的内部方法会开启自己的新事务，且开启的事务相互独立，互不干扰。

3. TransactionDefinition.PROPAGATION_NESTED

   > 如果当前存在事务，就在嵌套事务内执行；如果当前没有事务，就执行与`TransactionDefinition.PROPAGATION_REQUIRED`类似的操作。

   - 在外部开启事务的情况下，在内部开启一个新的事务，作为嵌套事务存在。
   - 如果外部方法无事务，则单独开启一个事务，与`PROPAGATION_REQUIRED`类似。

4. TransactionDefinition.PROPAGATION_MANDATORY

   如果当前存在事务，则加入改事务；如果当前没有事务，则抛出异常。（mamdatory：强制性）

若是配置了以下三种事务传播行为，事务将不会发生回滚：

- TransactionDefinition.PROPAGATION_SUPPORT：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行；
- TransactionDefinition.PROPAGATION_NOT_SUPPORT：以非事务方式运行，如果当前存在事务，则把当前事务挂起；
- TransactionDefiniton.PROPAGATION_NEVER：以非事务方式运行，如果当前存在事务，则抛出异常。

**REQUIRES_NEW和NESTED的区别**

```java
class A {
    public void invoke() {
        try {
            new B().invoke();
        catch (Exception e) {
            new C().invoke();
        }
        // 此处可能还有其他业务代码
        ...
    };
}

class B {
    public void invoke() {};
}

class C {
    public void invoke() {};
}
```

无论使用`REUQIRES_NEW`或是`NESTED`，在调用B的invoke如果发生异常，都能正确完成业务逻辑

- `REQUIRES_NEW`执行到B时，A事务被挂起，B会新开启一个事务执行，B发生异常后，B中的修改都会回滚，然后外部事务继续执行；
- `NESTED`执行到B时，会创建一个savePoint，如果B执行失败，会将数据回滚到这个savePoint。

重点来了，如果B处正常执行，就会产生区别了

- `REQUIRES_NEW`如果B正常执行，则B中的数据在A提交之前已经完成提交，其他线程已经可见其修改，这就意味着可能有脏数据的产生；同时，如果接下来A的其他逻辑发生了异常，A回滚，但是B已经完成提交，B就不会回滚了。
- `NESTED`如果B正常执行，此时B中的修改并不会立即提交，而是在A提交时一并提交，如果A下面的逻辑发生异常，A回滚时B中的修改也会发生回滚，就可以避免上述情况的发生。

#### 事务隔离级别

`TransactionDefinition`接口中定义了五个表示隔离级别的常量：

```java
public interface TransactionDefinition {
    ......
    int ISOLATION_DEFAULT = -1;
    int ISOLATION_READ_UNCOMMITTED = 1;
    int ISOLATION_READ_COMMITTED = 2;
    int ISOLATION_REPEATABLE_READ = 4;
    int ISOLATION_SERIALIZABLE = 8;
    ......
}
```

和事务传播行为那块一样，为了方便使用，Spring 也相应地定义了一个枚举类：`Isolation`

```java
public enum Isolation {

  DEFAULT(TransactionDefinition.ISOLATION_DEFAULT),

  READ_UNCOMMITTED(TransactionDefinition.ISOLATION_READ_UNCOMMITTED),

  READ_COMMITTED(TransactionDefinition.ISOLATION_READ_COMMITTED),

  REPEATABLE_READ(TransactionDefinition.ISOLATION_REPEATABLE_READ),

  SERIALIZABLE(TransactionDefinition.ISOLATION_SERIALIZABLE);

  private final int value;

  Isolation(int value) {
    this.value = value;
  }

  public int value() {
    return this.value;
  }

}
```

下面我依次对每一种事务隔离级别进行介绍：

- `TransactionDefinition.ISOLATION_DEFAULT` ：使用后端数据库默认的隔离级别，MySQL 默认采用的 `REPEATABLE_READ` 隔离级别， Oracle 默认采用的 `READ_COMMITTED` 隔离级别；
- `TransactionDefinition.ISOLATION_READ_UNCOMMITTED` ：最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读；**
- `TransactionDefinition.ISOLATION_READ_COMMITTED` ： 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生；**
- `TransactionDefinition.ISOLATION_REPEATABLE_READ` ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生；**
- `TransactionDefinition.ISOLATION_SERIALIZABLE` ：最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

#### 事务超时属性

所谓事务超时，就是指一个事务所允许执行的最长时间，如果超过该时间限制但事务还没有完成，则自动回滚事务。在`TransactionDefinition`中以int的值来表示超时时间，其单位时秒，默认值为-1，这表示事务的超时时间取决于底层事务系统或者没有超时时间。

#### 事务只读属性

```java
package org.springframework.transaction;

import org.springframework.lang.Nullable;

public interface TransactionDefinition {
    ......
    // 返回是否为只读事务，默认值为 false
    boolean isReadOnly();

}
```

对于只有读取数据查询的事务，可以指定事务类型为readonly，即只读事务。只读事务不涉及数据的修改，数据库会提供一些优化手段，适合用在有多条数据库查询操作的方法中。

分享一下关于事务只读属性，其他人的解答：

- 如果你一次执行单条查询语句，则没有必要启用事务支持，数据库默认支持 SQL 执行期间的读一致性；
- 如果你一次执行多条查询语句，例如统计查询，报表查询，在这种场景下，多条查询 SQL 必须保证整体的读一致性，否则，在前条 SQL 查询之后，后条 SQL 查询之前，数据被其他用户改变，则该次整体的统计查询将会出现读数据不一致的状态，此时，应该启用事务支持。

#### 事务回滚规则

这些规则定义了哪些异常会导致事务回滚而哪些不会。默认情况下，事务只有遇到运行期异常（RuntimeException的子类）时才会回滚，Error也会导致事务回滚，但是，在遇到检查型（Checked）异常时不会回滚。

如果你想要回滚你定义的特定的异常类型的话，可以这样：

```java
@Transactional(rollbackFor= MyException.class)
```

### @Transactional注解使用详解

#### `@Transactional`的作用范围

- 方法：推荐将注解使用于方法上，不过需要注意的是：该注解只能应用到public方法上，否则不生效；
- 类：如果这个注解使用在类上的话，表明该注解对该类所有的public方法都生效；
- 接口：不推荐在接口上使用。

#### `@Transactional` 事务注解原理

面试中在问AOP的时候可能会被问到的下一个问题。

我们知道，`@Transactional`的工作机制是基于AOP实现的，AOP又是使用动态代理实现的。如果目标对象实现了接口，默认情况下会采用JDK动态代理，如果目标对象没有实现接口，会使用CGLIB动态代理。

多提一嘴：`createAopProxy()` 方法决定了是使用 JDK 还是 Cglib 来做动态代理，源码如下：

```java
public class DefaultAopProxyFactory implements AopProxyFactory, Serializable {

	@Override
	public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
		if (config.isOptimize() || config.isProxyTargetClass() || hasNoUserSuppliedProxyInterfaces(config)) {
			Class<?> targetClass = config.getTargetClass();
			if (targetClass == null) {
				throw new AopConfigException("TargetSource cannot determine target class: " +
						"Either an interface or a target is required for proxy creation.");
			}
			if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
				return new JdkDynamicAopProxy(config);
			}
			return new ObjenesisCglibAopProxy(config);
		}
		else {
			return new JdkDynamicAopProxy(config);
		}
	}
  .......
}
```

如果一个类或一个类中的public方法被标注`@Transactional`注解的话，Spring容器就会在启动的时候为其创建一个代理类，在调用被`@Transactional`注解的public方法的时候，实际调用的是和，`TransactionInterceptor`类中的`invoke()`方法。这个方法的作用就是在目标方法之前开启事务，方法执行过程中如果遇到异常就回滚事务，方法调用完成之后提交事务。

#### `@Transactional`的使用注意事项总结

- `@Transactional`注解只有作用到public方法上事务才生效，不推荐在接口上使用；
- 避免在同一个类中调用`@Transactional`注解的方法，这样会导致事务失效；
- 正确的设置`@Transactional`的`rollbackFor`和`propagation`属性，否则事务可能会回滚失败；
- 被`@Transactional`注解的方法所在的类必须被Spring管理，否则不生效；
- 底层使用的数据库必须支持事务机制，否则不生效；
- ......