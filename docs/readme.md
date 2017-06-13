### Activiti Quick Start Guide 

1. 介绍
   这个教程展示了怎么简单的把BPM装进你的应用里。在这个项目里你使用一个命令行的项目。
   Activiti有更高级的流程设计工具，用来设计更复杂的逻辑。这些工具包括Eclipse-base的流程编辑工具，为了简单，这个教程只用Activiti的Java Apis

2. Create and Setup Maven Project
   新建maven工程，然后在pom.xml中加上依赖:

   ```
   <dependency>
     		<groupId>org.activiti</groupId>
     		<artifactId>activiti-engine</artifactId>
     		<version>$actVer</version>
     	</dependency>
     	<dependency>
     		<groupId>org.slf4j</groupId>
     		<artifactId>slf4j-api</artifactId>
     		<version>1.7.21</version>
     	</dependency>
     	<dependency>
     		<groupId>org.slf4j</groupId>
     		<artifactId>slf4j-log4j12</artifactId>
     		<version>1.7.21</version>
     	</dependency>
     	<dependency>
     		<groupId>com.h2database</groupId>
     		<artifactId>h2</artifactId>
     		<version>1.4.193</version>
     	</dependency>
   ```

   使用下载的版本替代$actVer变量，然后进行maven build，确保build success

3. Create Process Engine
   Activiti使用slf4j进行日志记录，底层使用log4j.
   配置log4j.properties

   ```
   log4j.rootLogger=DEBUG, ACT

   log4j.appender.ACT=org.apache.log4j.ConsoleAppender
   log4j.appender.ACT.layout=org.apache.log4j.PatternLayout
   log4j.appender.ACT.layout.ConversionPattern= %d{hh:mm:ss,SSS} [%t] %-5p %c %x - %m%n
   ```

   新建流程创建入口类： 

   ```
   public class OnboardingRequest {

       public static void main(String[] args) {
           ProcessEngineConfiguration cfg = new StandaloneProcessEngineConfiguration()
                   .setJdbcUrl("jdbc:h2:mem:activiti:DB_CLOSE_DELAY=1000")
                   .setJdbcUsername("sa")
                   .setJdbcPassword("")
                   .setJdbcDriver("org.h2.Driver")
                   .setDatabaseSchemaUpdate(ProcessEngineConfiguration.DB_SCHEMA_UPDATE_TRUE);
           ProcessEngine processEngin = cfg.buildProcessEngine();
           String pName = processEngin.getName();
           String ver = ProcessEngine.VERSION;
           System.out.println("ProcessEngine [" + pName + "] Version: [" + ver + "]");
       }
   }
   ```

   修改pom.xml, 新增asseble plugin

   ```
   <plugin>
           <groupId>org.apache.maven.plugins</groupId>
           <artifactId>maven-assembly-plugin</artifactId>
           <version>2.4.1</version>
           <configuration>
             <!-- get all project dependencies -->
             <descriptorRefs>
               <descriptorRef>jar-with-dependencies</descriptorRef>
             </descriptorRefs>
             <!-- MainClass in mainfest make a executable jar -->
             <archive>
               <manifest>
                 <mainClass>com.example.OnboardingRequest</mainClass>
               </manifest>
             </archive>
           </configuration>
           <executions>
             <execution>
               <id>make-assembly</id>
               <!-- bind to the packaging phase -->
               <phase>package</phase>
               <goals>
                 <goal>single</goal>
               </goals>
             </execution>
           </executions>
         </plugin>
   ```

   打成可执行jar包.修改

4. 修改onboardingrequest.java，进行执行程序。mvn package,执行观察结果。

5. Conclusion

   这是个很简单的例子吧，几乎不要敲代码。这里看来流程设计是最后落到xml上的，xml也是很复杂的。但是前面也提到了说会有其他设计的ui所以应该还好。然后engine的创建和执行，与其他engine也很类似。