1.环境准备  redis mysql
2.数据库还用测试库

CREATE DATABASE springbootdb;

DROP TABLE IF EXISTS  `city`;
CREATE TABLE `city` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '城市编号',
  `province_id` int(10) unsigned  NOT NULL COMMENT '省份编号',
  `city_name` varchar(25) DEFAULT NULL COMMENT '城市名称',
  `description` varchar(25) DEFAULT NULL COMMENT '描述',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

INSERT city VALUES (1 ,1,'温岭市','BYSocket 的家在温岭。');

1.增加spring-boot热部署插件
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                </configuration>
            </plugin>
        </plugins>
    </build>
    
2.将spring-boot部署到本机的tomcat
  a.在 spring-boot-starter-web 里面排除了 spring-boot-starter-tomcat，这是spring-boot内嵌的tomcat模块，本地调试的时候再加进来
  
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <exclusions>
          <exclusion>
              <artifactId>log4j-over-slf4j</artifactId>
              <groupId>org.slf4j</groupId>
          </exclusion>
          <exclusion>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-tomcat</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <scope>provided</scope>
  </dependency>
  
  b.将spring-boot的Application继承SpringBootServletInitializer类，并重写它的configure方法
  
  @SpringBootApplication
  public class Application extends SpringBootServletInitializer {
      /**
       * 如果要发布到自己的Tomcat中的时候，需要继承SpringBootServletInitializer类，并且增加如下的configure方法。
       * 如果不发布到自己的Tomcat中的时候，就无需上述的步骤
       */
      @Override
      protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
          return application.sources(Application.class);
      }
  
      public static void main(String[] args) {
          SpringApplication.run(Application.class, args);
      }
  }
  
  c.使用maven打包:mvc package,并设置打包方式为war
  <packaging>war</packaging>
  d.将target下的war包拷贝到本地的tomcat，修改后缀，运行