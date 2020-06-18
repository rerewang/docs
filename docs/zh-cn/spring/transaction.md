# spring事务机制
## spring事务管理器
Spring不直接管理事务，而是通过事务管理器，将事务管理交给JDBC/JTA等平台实现。
```java
public interface PlatformTransactionManager extends TransactionManager {
    TransactionStatus getTransaction(@Nullable TransactionDefinition var1) throws TransactionException;

    void commit(TransactionStatus var1) throws TransactionException;

    void rollback(TransactionStatus var1) throws TransactionException;
}
```
### TransactionDefinition 事务定义
```java
public interface TransactionDefinition {
    int PROPAGATION_REQUIRED = 0;	//当前方法必须运行在事务中，如存在当前事务则加入，否则开启新事务 (默认行为)
    int PROPAGATION_SUPPORTS = 1;	//当前方法不需要事务上下文，如存在当前事务则加入，否则裸奔
    int PROPAGATION_MANDATORY = 2;	//当前方法必须运行在事务中，当前事务不存在则报错  (MANDATORY:强制的)
    int PROPAGATION_REQUIRES_NEW = 3;	//方法运行在新事务中
    int PROPAGATION_NOT_SUPPORTED = 4;	//如存在当前事务则将这个事务挂起，并使用新的数据库连接 (新的连接不使用事务)
    int PROPAGATION_NEVER = 5;		//如存在当前事务则报错
    int PROPAGATION_NESTED = 6;		//已存在当前事务则运行在嵌套事务中，否则开启新事务，设计savepoint (NESTED:嵌套)
    
    int ISOLATION_DEFAULT = -1;
    int ISOLATION_READ_UNCOMMITTED = 1; //读未提交
    int ISOLATION_READ_COMMITTED = 2;	//不可重复读
    int ISOLATION_REPEATABLE_READ = 4;	//可重复读
    int ISOLATION_SERIALIZABLE = 8;		//串行化
    int TIMEOUT_DEFAULT = -1;

    default int getPropagationBehavior() {}		//传播行为

    default int getIsolationLevel() {}	//隔离级别，默认为数据库默认级别

    default int getTimeout() {}

    default boolean isReadOnly() {}		//是否只读，查询操作可以设置为true

    @Nullable
    default String getName() {}

    static TransactionDefinition withDefaults() {}
}
```

### TransactionDefinition 事务状态
提供控制事务执行和查询事务状态的方法
```java
public interface TransactionStatus extends TransactionExecution, SavepointManager, Flushable {
    boolean hasSavepoint();		//是否有回复点
    void flush();		//flush
}

public interface TransactionExecution {
    boolean isNewTransaction();		//是否是新事务
    void setRollbackOnly();
    boolean isRollbackOnly();
    boolean isCompleted();
}
```

## 编程式事务
使用TransactionTemplate手动管理事务，实际应用中很少使用
- TransactionTemplate
	- TransactionCallback
	- TransactionCallbackWithoutResponse
```JAVA
package com.rere.learn.jdbcDemo.programmatic;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.TransactionCallbackWithoutResult;
import org.springframework.transaction.support.TransactionTemplate;

@SpringBootApplication
@Slf4j
public class ProgrammaticTransactionDemoApplication implements CommandLineRunner {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    @Autowired
    private TransactionTemplate transactionTemplate;

    public static void main(String[] args) {
        SpringApplication.run(ProgrammaticTransactionDemoApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        log.info("count before transaction: {}", getCount());
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                jdbcTemplate.execute("insert into foo(bar) value ('bar-trans')");
                log.info("count in transaction: {}", getCount());
                transactionStatus.setRollbackOnly();
            }
        });
        log.info("count after transaction: {}", getCount());
    }

    private long getCount() {
        return (long) jdbcTemplate.queryForList("select count(1) as cnt from foo")
                .get(0).get("cnt");
    }
}
```

## 声明式事务
- @EnableTransactionManagement
- @Transactional(rollbackFor=)