# mybatis-generator

这里有一个简单的表来介绍mybatis-genenerator的使用方式，来生成mapper以及pojo。

#### 设计users表
使用Navicat作为MySQL的GUI工具，写一个创建users表的SQL语句
```sql
DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `id` varchar(64) NOT NULL COMMENT '主键id 用户id',
  `username` varchar(32) NOT NULL COMMENT '用户名 用户名',
  `password` varchar(64) NOT NULL COMMENT '密码 密码',
  `nick_name` varchar(32) DEFAULT NULL COMMENT '昵称 昵称',
  `rea_lname` varchar(128) DEFAULT NULL COMMENT '真实姓名',
  `face` varchar(1024) NOT NULL COMMENT '头像',
  `mobile` varchar(32) DEFAULT NULL COMMENT '手机号 手机号',
  `email` varchar(32) DEFAULT NULL COMMENT '邮箱地址 邮箱地址',
  `gender` int(11) DEFAULT NULL COMMENT '性别 性别 1:男  0:女  2:保密',
  `birthday` date DEFAULT NULL COMMENT '生日 生日',
  `created_time` datetime NOT NULL COMMENT '创建时间 创建时间',
  `updated_time` datetime NOT NULL COMMENT '更新时间 更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```

#### 使用工具生成Mapper以及POJO类
clone [项目](https://github.com/iossocket/my-springboot-demo)，根据自己项目的需要修改配置文件以及项目的包结构。
配置文件generatorConfig.xml，位于上述项目中：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="MysqlContext" targetRuntime="MyBatis3Simple" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <!-- 通用mapper所在目录 -->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mappers" value="com.iossocket.my.mapper.MyMapper"/>
        </plugin>

        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/foodie-shop-dev?useSSL=false"
                        userId="root"
                        password="123456">
        </jdbcConnection>

        <!-- 对应生成的pojo所在包 -->
        <javaModelGenerator targetPackage="com.iossocket.pojo" targetProject="src/main/java"/>

        <!-- 对应生成的mapper所在目录 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources"/>

        <!-- 配置mapper对应的java映射 -->
        <javaClientGenerator targetPackage="com.iossocket.mapper" targetProject="src/main/java" type="XMLMAPPER"/>

        <!-- 数据库表 -->
        <table tableName="users"></table>

    </context>
</generatorConfiguration>
```
运行GeneratorDisplay类的main方法，相应的mapper，pojo，以及配置文件即可生成
```java
package com.iossocket.mybatis.utils;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;
import java.io.File;
import java.util.ArrayList;
import java.util.List;

public class GeneratorDisplay {
    public void generator() throws Exception {

        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        //指定 逆向工程配置文件
        File configFile = new File("src/generatorConfig.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
                callback, warnings);
        myBatisGenerator.generate(null);
    }

    public static void main(String[] args) throws Exception {
        try {
            GeneratorDisplay generatorSqlmap = new GeneratorDisplay();
            generatorSqlmap.generator();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
