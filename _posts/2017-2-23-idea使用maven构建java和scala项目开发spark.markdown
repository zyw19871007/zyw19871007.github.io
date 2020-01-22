---
layout:  post
title:   idea使用maven构建java和scala项目开发spark
date:   2017-02-23 17:51:43
author:  "张耀文"
catalog:   false
tags:
    - java
    - ide
    - scala
---
一 工具版本  
1 Intellij Idea 15.0.2  
2 jdk:1.8.0_40  
3 maven:4.0.0  
4 spark:2.1.0  
5 scala:2.11.8  
6 hadoop:2.7.3  
二 pom.xml

    
    
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>testSpark</groupId>
        <artifactId>testSpark</artifactId>
        <version>1.0-SNAPSHOT</version>
        <properties>
            <scala.version>2.11.8</scala.version>
            <scala.maven.version>2.11.8</scala.maven.version>
            <spark.version>2.1.0</spark.version>
        </properties>
        <dependencies>
            <dependency>
                <groupId>org.apache.spark</groupId>
                <artifactId>spark-core_2.11</artifactId>
                <version>${spark.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.spark</groupId>
                <artifactId>spark-sql_2.11</artifactId>
                <version>${spark.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.spark</groupId>
                <artifactId>spark-mllib_2.11</artifactId>
                <version>${spark.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.spark</groupId>
                <artifactId>spark-streaming_2.11</artifactId>
                <version>${spark.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.spark</groupId>
                <artifactId>spark-streaming-flume_2.11</artifactId>
                <version>${spark.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.spark</groupId>
                <artifactId>spark-streaming-kafka_2.11</artifactId>
                <version>1.6.3</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.3</version>
                <scope>provided</scope>
            </dependency>
            <dependency><!--hadoopclient2.7需要的包-->
                <groupId>commons-io</groupId>
                <artifactId>commons-io</artifactId>
                <version>2.5</version>
                <scope>provided</scope>
            </dependency>
            <dependency><!--scala样例引用的包-->
                <groupId>com.github.scopt</groupId>
                <artifactId>scopt_2.11</artifactId>
                <version>3.5.0</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.scalatest</groupId>
                <artifactId>scalatest_2.11</artifactId>
                <version>3.0.1</version>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.scala-lang</groupId>
                <artifactId>scala-library</artifactId>
                <version>${scala.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.scala-lang</groupId>
                <artifactId>scala-compiler</artifactId>
                <version>${scala.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.scala-lang</groupId>
                <artifactId>scala-reflect</artifactId>
                <version>${scala.version}</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.3.2</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
                <!--<plugin>-->
                    <!--<groupId>net.alchim31.maven</groupId>-->
                    <!--<artifactId>scala-maven-plugin</artifactId>-->
                    <!--<version>3.2.0</version>-->
                    <!--<executions>-->
                        <!--<execution>-->
                            <!--<id>compile-scala</id>-->
                            <!--<phase>compile</phase>-->
                            <!--<goals>-->
                                <!--<goal>add-source</goal>-->
                                <!--<goal>compile</goal>-->
                            <!--</goals>-->
                        <!--</execution>-->
                        <!--<execution>-->
                            <!--<id>test-compile-scala</id>-->
                            <!--<phase>test-compile</phase>-->
                            <!--<goals>-->
                                <!--<goal>add-source</goal>-->
                                <!--<goal>testCompile</goal>-->
                            <!--</goals>-->
                        <!--</execution>-->
                    <!--</executions>-->
                    <!--<configuration>-->
                        <!--<scalaVersion>${scala.version}</scalaVersion>-->
                    <!--</configuration>-->
                <!--</plugin>-->
            </plugins>
        </build>
    </project>

三 注意事项  
1 源文件根目录设置为source root  
2 jar包依赖为provided,spark包在集群上已存在,容易产生冲突  
3 provided:编译和测试,因此需要建立测试文件运行  
4 测试运行指定spark的url:vm参数添加:-Dspark.master=local  
![这里写图片描述](http://img-blog.csdn.net/20170223174544043?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenl3MTk4NzEwMDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

