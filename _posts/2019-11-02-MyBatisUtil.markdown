---
layout: post
title: 获取SQLSession工具类
date: 2019-11-02
---

#### MybatisUtil

```bash
package com.util;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;

public class mybatisUtil {
private  static SqlSessionFactory sqlSessionFactory;
static {
    String resources="mybatis.cgf.xml";
    InputStream in =null;
    try {
        //读取resource.xml文件
        in= Resources.getResourceAsStream(resources);
        //匿名对象使用构造
        sqlSessionFactory=new SqlSessionFactoryBuilder().build(in);
    }catch (IOException e) {
        e.printStackTrace();
    }finally {
        if(in!=null){
            try {
                in.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
	public static SqlSession getSession(){
    return  sqlSessionFactory.openSession();
	}

}
```

#### mybatis.cgf.xml

```bash
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration> 
    <environments default="dev">
        <environment id="dev">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="UNPOOLED">
                <property name="url" value="${url}"/>
                <property name="driver" value="${driver}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
<mappers>
    <mapper resource="com/mapper/PeopleMapper.xml"></mapper>
</mappers>
</configuration>
```

