springboot2.2.1整合mybatis-plus超简单的方式
https://blog.csdn.net/u013330085/article/details/103214187
springboot2.2.1整合mybatisplus+mysql8.0.18多数据源最简便方式

版本(均为目前最新版本)：
springboot : 2.2.1

mybatis-plus : 3.2.0

dynamic-datasource : 2.5.7

mysql : 8.0.18

druid : 1.1.21

废话不说，直接上代码
maven配置文件pox.xml
ps:dynamic-datasource : 2.5.7必须要spring-boot-starter-actuator的依赖，2.5.6之前的则不需要

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.lusen</groupId>
    <artifactId>mybatisplus</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>mybatisplus</name>
    <description>Demo project for Spring Boot</description>
 
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    </properties>
 
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
 
        <!-- Spring Boot Mybatis plus 依赖 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.2.0</version>
        </dependency>
        <!-- Spring Boot Dynamic Datasource 依赖 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>dynamic-datasource-spring-boot-starter</artifactId>
            <version>2.5.7</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!-- MySQL 连接驱动依赖 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.18</version>
            <scope>runtime</scope>
        </dependency>
        <!-- Druid 连接池管理依赖 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.21</version>
        </dependency>
 
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
 
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
 
</project>
springboot配置文件application.yml
#端口
server:
  port: 8080
 
spring:
  application:
    name: mybatisplus-demo
  # DB 配置
  datasource:
    dynamic:
      #主数据源
      primary: db1
      p6spy: true
      druid:
        initial-size: 5
        min-idle: 5
        max-active: 20
        validation-query: SELECT 1 FROM DUAL
        test-while-idle: true
        #test-on-borrow: true
        validation-query-timeout: 20
        filters: stat,wall,slf4j
      datasource:
        # db1-主数据源
        db1:
          url: jdbc:mysql://123.207.xx.xx:3306/lusen?useSSL=false&useUnicode=true&characterEncoding=UTF8&serverTimezone=GMT&allowPublicKeyRetrieval=true
          username: root
          password: xxx
          driver-class-name: com.mysql.cj.jdbc.Driver
        # db2-数据源
        db2:
          url: jdbc:mysql://123.207.xx.xx:3306/mysql?useSSL=false&useUnicode=true&characterEncoding=UTF8&serverTimezone=GMT&allowPublicKeyRetrieval=true
          username: root
          password: xxx
          driver-class-name: com.mysql.cj.jdbc.Driver
        # db3-数据源
        db3:
          url: jdbc:mysql://123.207.xx.xx:3306/mysql?useSSL=false&characterEncoding=UTF8
          username: root
          password: xxx
          driver-class-name: com.mysql.cj.jdbc.Driver
 
mybatis-plus:
  global-config:
    db-config:
      id-type: auto
      capital-mode: true
  configuration:
    #打印sql
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  #mapper的xml文件路径
  mapper-locations: classpath:mybatis/mapper/**/*.xml
接下来新建数据库中对应的实体类
package com.lusen.mybatisplus.domain;
 
import com.baomidou.mybatisplus.annotation.TableName;
 
import java.util.Date;
 
/**
 * @author
 * @description
 * @data2019/11/23
 */
@TableName("t_programmer")//对应数据库表名
public class Programmer
{
    /**
     * 主键id
     */
    private Long id;
 
    /**
     * 程序员类型
     */
    private String type;
 
    /**
     * 姓名
     */
    private String name;
 
    /**
     * 性别
     */
    private String sex;
 
    /**
     * 入坑时间
     */
    private Date joinDate;
 
    public Long getId()
    {
        return id;
    }
 
    public void setId(Long id)
    {
        this.id = id;
    }
 
    public String getType()
    {
        return type;
    }
 
    public void setType(String type)
    {
        this.type = type;
    }
 
    public String getName()
    {
        return name;
    }
 
    public void setName(String name)
    {
        this.name = name;
    }
 
    public String getSex()
    {
        return sex;
    }
 
    public void setSex(String sex)
    {
        this.sex = sex;
    }
 
    public Date getJoinDate()
    {
        return joinDate;
    }
 
    public void setJoinDate(Date joinDate)
    {
        this.joinDate = joinDate;
    }
 
    @Override
    public String toString()
    {
        return "Programmer{" + "id=" + id + ", type='" + type + '\''
            + ", name='" + name + '\'' + ", sex='" + sex + '\'' + ", joinDate="
            + joinDate + '}';
    }
}
新建dao
package com.lusen.mybatisplus.dao;
 
import com.baomidou.dynamic.datasource.annotation.DS;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.lusen.mybatisplus.domain.Programmer;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;
 
import java.util.List;
 
/**
 * @author
 * @description
 * @data2019/11/23
 */
@Repository
public interface ProgrammerDao extends BaseMapper<Programmer>
{
    /**
     *  按名字查询(对应xml文件里的selectByName)
     * @param name
     * @return
     */
    List<Programmer> selectByName(@Param("name") String name);
 
    /**
     * 分页查询
     * @param page
     * @return
     */
    @Select("select * from t_programmer")
    IPage<Programmer> listProgrammer(Page page);
 
}
 

新建dao对应的programmer-mapper.xml 文件
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.lusen.mybatisplus.dao.ProgrammerDao">
 
 
    <select id="selectByName" resultType="com.lusen.mybatisplus.domain.Programmer">
      select * from t_programmer
    </select>
</mapper>
 

新建配置类MybatisPlusConfig
package com.lusen.mybatisplus.config;
 
import com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceAutoConfigure;
import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;
 
/**
 * @author
 * @description
 * @data2019/11/23
 */
@Configuration
@MapperScan({"com.lusen.mybatisplus.dao"})
@EnableTransactionManagement
public class MybatisPlusConfig
{
    /**
     * 分页插件
     */
    @Bean
    public PaginationInterceptor paginationInterceptor()
    {
        PaginationInterceptor paginationInterceptor =
            new PaginationInterceptor();
        paginationInterceptor.setLimit(100);
        return paginationInterceptor;
    }
}
启动类MybatisplusApplication
package com.lusen.mybatisplus;
 
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
 
@SpringBootApplication(exclude = DruidDataSourceAutoConfigure.class)
public class MybatisplusApplication
{
 
    public static void main(String[] args)
    {
        SpringApplication.run(MybatisplusApplication.class, args);
    }
 
}
 

最后测试类ProgrammerDaoTest
package com.lusen.mybatisplus.dao;
 
import com.baomidou.dynamic.datasource.annotation.DS;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.lusen.mybatisplus.domain.Programmer;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
 
import javax.annotation.Resource;
 
import java.util.List;
 
import static org.junit.jupiter.api.Assertions.*;
 
@SpringBootTest
class ProgrammerDaoTest
{
 
    @Resource
    private ProgrammerDao programmerDao;
    
    @Test
    void selectByName()
    {
        List<Programmer> programmerList = programmerDao.selectByName("lusen");
        programmerList.forEach(System.out::println);
    }
    @Test
    void listProgrammer()
    {
        Page<Programmer> page = new Page();
        programmerDao.listProgrammer(page);
        page.getRecords().forEach(System.out::println);
    }
}
目录结构：
https://img-blog.csdnimg.cn/20191123172153402.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzMzAwODU=,size_16,color_FFFFFF,t_70

ok,就是这么简单，详细参考github地址
