# ORM

## Object vs RDBMS 

粒度 | 类 | 表
继承 | 有 | 无
唯一性| = | 主键
关联 | 引用 | 外键
访问 | 逐级访问 | join

orm
- 把开发者从大部分的数据持久化工作中解放出来
- 屏蔽底层的数据库操作

## JPA
为对象关系映射提供一种基于POJO的持久化模型

### 注解
- 实体
	- @Entity @MappedSuperclass
	- @table(name)
- 主键
	- @Id
		- @GeneratedValue(strategy generator)
		- @SequenceGenerator(name sequenceName)
- 映射
	- @Column(name nullable length insertable updateable)
	- @JoinTable(name) / @JoinColumn(name)
- 关系
	- @OneToOne / @OneToMany / @ManyToOne / @ManyToMany
	- @OrderBy

### 实体定义
```JAVA
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.Type;
import org.joda.money.Money;

import javax.persistence.*;
import java.io.Serializable;
import java.util.Date;

@Entity    //实体注解
@Table(name = "t_menu")  //定义表名
@Builder
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Coffee implements Serializable {
    @Id  //定义主键
    @GeneratedValue //用于标注主键的生成策略，通过strategy 属性指定。
    private Long id;
    private String name;
    @Column
    @Type(type = "org.jadira.usertype.moneyandcurrency.joda.PersistentMoneyAmount",
        parameters = {@org.hibernate.annotations.Parameter(name = "currencyCode", value = "CNY")})   //Money类
    private Money price;
    @Column(updatable = false)  //不可修改
    @CreationTimestamp
    private Date createTime;
    @Column
    @CreationTimestamp
    private Date updateTime;
}

```
@GeneratedValue 用于标注主键的生成策略，通过strategy 属性指定。默认情况下，JPA 自动选择一个最适合底层数据库的主键生成策略：SqlServer对应identity，MySQL 对应 auto increment。 
在javax.persistence.GenerationType中定义了以下几种可供选择的策略： 
– IDENTITY：采用数据库ID自增长的方式来自增主键字段，Oracle 不支持这种方式； 
– AUTO： JPA自动选择合适的策略，是默认选项； 
– SEQUENCE：通过序列产生主键，通过@SequenceGenerator 注解指定序列名，MySql不支持这种方式 
– TABLE：通过表产生主键，框架借由表模拟序列产生主键，使用该策略可以使应用更易于数据库移植。

#### Hibernate 自动创建表
```java
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.properties.hibernate.show_sql=true
spring.jpa.properties.hibernate.format_sql=true
```
运行结果如下:
```sql
Hibernate: 
    
    drop table if exists hibernate_sequence
Hibernate: 
    
    drop table if exists t_menu
Hibernate: 
    
    drop table if exists t_order
Hibernate: 
    
    drop table if exists t_order_coffe
Hibernate: 
    
    create table hibernate_sequence (
       next_val bigint
    ) engine=InnoDB
Hibernate: 
    
    insert into hibernate_sequence values ( 1 )
Hibernate: 
    
    insert into hibernate_sequence values ( 1 )
Hibernate: 
    
    create table t_menu (
       id bigint not null,
        create_time datetime(6),
        name varchar(255),
        price decimal(19,2),
        update_time datetime(6),
        primary key (id)
    ) engine=InnoDB
Hibernate: 
    
    create table t_order (
       id bigint not null,
        create_time datetime(6),
        customer varchar(255),
        state integer not null,
        update_time datetime(6),
        primary key (id)
    ) engine=InnoDB
Hibernate: 
    
    create table t_order_coffe (
       coffee_order_id bigint not null,
        items_id bigint not null
    ) engine=InnoDB
Hibernate: 
    
    alter table t_order_coffe 
       add constraint FK3d59chfpvxuusqyhctme7csxj 
       foreign key (items_id) 
       references t_menu (id)
Hibernate: 
    
    alter table t_order_coffe 
       add constraint FKk0nulntfjgx31s4b35is63nu3 
       foreign key (coffee_order_id) 
       references t_order (id)
```

### 定义 repository
```JAVA
import com.rere.learn.orm.jpa.model.Coffee;
import org.springframework.data.repository.CrudRepository;

public interface CoffeeRepository extends CrudRepository<Coffee, Long> {
}
```
#### 根据方法定义查询
- find...By... / read...By... / query...By... / get...By...
- count...By...
- ...OrderBy[Asc / Desc]
- And / Or / IgnoreCase
- Top / First / Distinct

#### 分页查询
- PagingAndSortingRepository<T, ID>  //分页和排序接口
- Pageable / Sort 
- Slice<T> / Page<T>

### 实体操作
```JAVA
//save
Coffee espresso = Coffee.builder().name("espresso")
                .price(Money.of(CurrencyUnit.of("CNY"), 20.0))
                .build();
coffeeRepository.save(espresso);
//查询

```

## MyBatis
- 一套优秀的持久层框架
- 支持定制化SQL、存储过程和高级映射

### 什么时候用JPA, MyBatis?
- sql比较简单用JPA
- sql比较复杂、每条sql都要经过dba审核之类的 (大厂)

### Mapper
- @MapperScan()
- @Mapper
- 映射的定义 -- xml和注解

### 简单配置
- mybatis.type-handlers-package //配置一个转换器所在的包，扫描该包下的TypeHandler的实现类,自动注册为转换器
	- TypeHandler? (@todo) //转换参数到sql中；转换查询结果到Java类中
- mybatis.configuration.map-underscore-to-camel-case //驼峰命名

### 定义转换器
```JAVA
import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * 在 Money 和 Long 之间转换的 TypeHandler, 处理 CNY 人民币
 */
public class MoneyTypeHandler extends BaseTypeHandler<Money> {
    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, Money money, JdbcType jdbcType) throws SQLException {
        preparedStatement.setLong(i, money.getAmountMinorLong());
    }

    @Override
    public Money getNullableResult(ResultSet resultSet, String s) throws SQLException {
        return parseMoney(resultSet.getLong(s));
    }

    @Override
    public Money getNullableResult(ResultSet resultSet, int i) throws SQLException {
        return parseMoney(resultSet.getLong(i));
    }

    @Override
    public Money getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return parseMoney(callableStatement.getLong(i));
    }
    
    private Money parseMoney(Long value) {
        return Money.of(CurrencyUnit.of("CNY"), value / 100.00);
    }
}
```

### 定义model
```JAVA
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.joda.money.Money;

import java.util.Date;

@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Coffee {
    private Long id;
    private String name;
    private Money price;
    private Date createTime;
    private Date updateTime;
}
```
### 定义Mapper
```JAVA
import com.rere.learn.orm.mybatis.model.Coffee;
import org.apache.ibatis.annotations.*;

@Mapper
public interface CoffeeMapper {
    @Insert("insert into t_coffee (name, price, create_time, update_time)" +
            "values (#{name}, #{price}, now(), now())")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    int save(Coffee coffee);

    @Select("select * from t_coffee where id = #{id}")
    @Results({
            @Result(id = true, column = "id", property = "id"),
            @Result(column = "create_time", property = "createTime"),
    })
    Coffee findById(@Param("id") Long id);
}
```