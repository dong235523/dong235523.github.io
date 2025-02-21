---
title: mybatis通过拦截器包装全局查询条件
date: 2025-02-21 11:16:19
categories: 
  - 数据库
cover: false
---

在拦截器中配置全局权限
1、自定义一个拦截器
```
package com.ruoyi.framework.interceptor.impl;

import com.baomidou.mybatisplus.core.plugins.InterceptorIgnoreHelper;
import com.baomidou.mybatisplus.extension.plugins.inner.InnerInterceptor;
import com.ruoyi.common.annotation.IgnoreDataPermission;
import com.ruoyi.common.utils.SecurityUtils;
import net.sf.jsqlparser.JSQLParserException;
import net.sf.jsqlparser.parser.CCJSqlParserUtil;
import net.sf.jsqlparser.statement.select.PlainSelect;
import net.sf.jsqlparser.statement.select.Select;
import net.sf.jsqlparser.statement.select.SelectBody;
import org.apache.ibatis.executor.Executor;
import org.apache.ibatis.executor.statement.StatementHandler;
import org.apache.ibatis.mapping.BoundSql;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.mapping.SqlCommandType;
import org.apache.ibatis.plugin.*;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.SystemMetaObject;
import org.apache.ibatis.session.ResultHandler;
import org.apache.ibatis.session.RowBounds;
import java.lang.reflect.Method;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

@Intercepts({@Signature(type = StatementHandler.class, method = "prepare", args = {Connection.class, Integer.class})})
public class MyDataPermissionInterceptor implements InnerInterceptor, Interceptor {

    @Override
    public void beforeQuery(Executor executor, MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
    	//判断是查询才会进拦截器
        if (SqlCommandType.SELECT != ms.getSqlCommandType()
                || InterceptorIgnoreHelper.willIgnoreDataPermission(ms.getId())
                //自定义权限注解，有这个注解的接口，才会启用数据权限
                || !isIgnoreDataPermission(ms)) {
            return;
        }
        String userName = null;
        //因为系统初始化的时候，拦截器会执行，但是没有user用户，所以需要catch一下，不然报错
        try{
            userName = SecurityUtils.getUsername();
        }catch (Exception e){

        }
        // 这个原理是获取查询语句的的 SQL 片段，再拼接上查询条件，这个方法mybatisplus也适用，因为mybatisplus最终也是生成一个sql
        String newSqlSegment = " create_by ="+"'"+userName+"' ";
        if (newSqlSegment != null && !newSqlSegment.isEmpty()) {
            String originalSql = boundSql.getSql();
            // 这里简单假设原 SQL 是简单的 SELECT 语句，根据实际情况调整拼接逻辑
            String newSql = originalSql;
            if (originalSql.toLowerCase().contains("where")) {
                newSql += " AND " + newSqlSegment;
            } else {
                newSql += " WHERE " + newSqlSegment;
            }
            // 更新 BoundSql 的 SQL 语句
            MetaObject metaObject = SystemMetaObject.forObject(boundSql);
            metaObject.setValue("sql", newSql);
        }
    }
    //判断接口上是否有注解
    private boolean isIgnoreDataPermission(MappedStatement ms) {
        try {
            String id = ms.getId();
            String className = id.substring(0, id.lastIndexOf("."));
            String methodName = id.substring(id.lastIndexOf(".") + 1);
            Class<?> clazz = Class.forName(className);
            Method[] methods = clazz.getMethods();
            for (Method method : methods) {
                if (method.getName().equals(methodName) && method.isAnnotationPresent(IgnoreDataPermission.class)) {
                    return true;
                }
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return false;
    }

    @Override
    public Object plugin(Object target) {
        if (target instanceof StatementHandler) {
            return Plugin.wrap(target, this);
        }
        return target;
    }

    @Override
    public void setProperties(Properties properties) {
        // 设置属性
    }
}

```
2、在MybatisPlusConfig中配置拦截器
```
package com.ruoyi.framework.config;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.extension.plugins.inner.DataPermissionInterceptor;
import com.ruoyi.framework.interceptor.impl.MyDataPermissionInterceptor;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.OptimisticLockerInnerInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.BlockAttackInnerInterceptor;

/**
 * Mybatis Plus 配置
 *
 * @author ruoyi
 */

@EnableCaching
@Configuration
public class MybatisPlusConfig
{
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();

        //乐观锁插件
        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        //防止在程序中删除跑路事件
        mybatisPlusInterceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
        //添加自定义权限拦截器
        mybatisPlusInterceptor.addInnerInterceptor(new MyDataPermissionInterceptor());
        //设置分页插件
        PaginationInnerInterceptor innerInterceptor = new PaginationInnerInterceptor();
        innerInterceptor.setOverflow(false);
        mybatisPlusInterceptor.addInnerInterceptor(innerInterceptor);

        return mybatisPlusInterceptor;
    }

    /**
     * 分页插件，自动识别数据库类型 https://baomidou.com/guide/interceptor-pagination.html
     */
    public PaginationInnerInterceptor paginationInnerInterceptor()
    {
        PaginationInnerInterceptor paginationInnerInterceptor = new PaginationInnerInterceptor();
        // 设置数据库类型为POSTGRESQL
        paginationInnerInterceptor.setDbType(DbType.POSTGRE_SQL);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        paginationInnerInterceptor.setMaxLimit(-1L);
        return paginationInnerInterceptor;
    }

    /**
     * 乐观锁插件 https://baomidou.com/guide/interceptor-optimistic-locker.html
     */
    public OptimisticLockerInnerInterceptor optimisticLockerInnerInterceptor()
    {
        return new OptimisticLockerInnerInterceptor();
    }

    /**
     * 如果是对全表的删除或更新操作，就会终止该操作 https://baomidou.com/guide/interceptor-block-attack.html
     */
    public BlockAttackInnerInterceptor blockAttackInnerInterceptor()
    {
        return new BlockAttackInnerInterceptor();
    }
}
```
3、注解
```
package com.ruoyi.common.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface IgnoreDataPermission{

}
```
注意：IgnoreDataPermission这个注解只能在mapper层接口上使用，因为MyBatis-Plus 的 InnerInterceptor 通常是针对 Mapper 层的 SQL 执行进行拦截处理的，其设计初衷就是在 Mapper 方法调用时触发拦截逻辑。拦截器在实现过程中，可能仅会去检查 Mapper 方法上的注解信息，而不会去扫描 Service 层方法上的注解。例如，拦截器代码中可能通过 MyBatis 的 MappedStatement 对象来获取 Mapper 方法的元数据，没有对 Service 层方法进行相关处理。
对于 MyBatis-Plus 集成好的 selectList 等方法，由于没有显式的接口方法供我们添加注解，所以可以重写查询方法例如：
```
@Mapper
public interface WgfzMapper extends BaseMapper<Wgfz> {

    @IgnoreDataPermission
    @Override
    List<Wgfz> selectList(@Param(Constants.WRAPPER) Wrapper<Wgfz> queryWrapper);
}
``` 
原本的方法List<T> Wrapper<T>都是泛型，重写不能和原本方法一致，所以改成指定bean类型，重写查询后有了实体方法，就可以在方法上加字段了
