---
layout: post
title: Maven 使用技巧
date: 2018-05-30 14:13:55
tags:
    - maven
    - compile
---
# Maven 使用技巧

1. 多module,分stage 分开打包
    1. 目录结构 (a,b 依赖common)
        1. assembly
        1. commont
        1. a
        1. b
        1. pom.xml
    1. assembly/pom.xml 通过参数-Ddeploy.app=a 控制哪个模块
<!--more-->

```
    <properties>
        <deploy.app>a</deploy.app>
        <deploy.stage>test</deploy.stage>
    </properties>
    <dependencies>
        <dependency>
          <groupId>com.xxx</groupId>
          <artifactId>xxxx-${deploy.app}</artifactId>
          <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
      <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-assembly-plugin</artifactId>
        <configuration>
          <descriptors>
            <descriptor>src/main/assembly/${deploy.app}.xml</descriptor>
          </descriptors>
          <outputDirectory>${project.parent.basedir}/target</outputDirectory>
          <finalName>xxxx-${deploy.app}-assembly-${project.parent.version}</finalName>
        </configuration>
        <executions>
          <execution>
            <id>assembly-deploy-package</id>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```
    1. src/main/assembly/xx.xml

```
<assembly>
  <id>deploy</id>
  <formats>
    <format>tar.gz</format>
  </formats>
  <includeBaseDirectory>false</includeBaseDirectory>
  <dependencySets>
    <dependencySet>
      <useProjectArtifact>true</useProjectArtifact>
      <includes>
        <include>com.xxxx:xxx-${deploy.app}</include>
      </includes>
    </dependencySet>
  </dependencySets>
  <fileSets>
    <fileSet>
      <outputDirectory>conf</outputDirectory>
      <directory>${project.parent.basedir}/config/common</directory>
    </fileSet>
    <fileSet>
      <outputDirectory>conf</outputDirectory>
      <directory>${project.parent.basedir}/config/${deploy.app}/common</directory>
    </fileSet>
    <fileSet>
      <outputDirectory>conf</outputDirectory>
      <directory>${project.parent.basedir}/config/${deploy.app}/${deploy.stage}</directory>
    </fileSet>
    <fileSet>
      <outputDirectory>bin</outputDirectory>
      <fileMode>0755</fileMode>
      <directory>${project.parent.basedir}/${deploy.app}/src/main/scripts</directory>
    </fileSet>
    <fileSet>
      <outputDirectory>/libs</outputDirectory>
      <directory>${project.parent.basedir}/${deploy.app}/target/lib</directory>
    </fileSet>
  </fileSets>
</assembly>

```

    1. 需要在module pom.xml plugin 加上

```
<plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <executions>
          <execution>
            <id>copy-dependencies</id>
            <goals>
              <goal>copy-dependencies</goal>
            </goals>
            <phase>prepare-package</phase>
            <configuration>
              <outputDirectory>${project.build.directory}/lib</outputDirectory>
              <includeScope>runtime</includeScope>
              <excludeScope>test,provided</excludeScope>
              <overWriteReleases>false</overWriteReleases>
              <overWriteSnapshots>false</overWriteSnapshots>
              <overWriteIfNewer>true</overWriteIfNewer>
            </configuration>
          </execution>
        </executions>
      </plugin>
```

    1. 编译a test 包:mvn clean package -am -pl assembly -Ddeploy.app=a -Ddeploy.stage=test
    1. 编译b prod 包:mvn clean package -am -pl assembly -Ddeploy.app=b -Ddeploy.stage=prod

