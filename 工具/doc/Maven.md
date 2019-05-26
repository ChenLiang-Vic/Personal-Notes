
##

## Maven安装与配置
### Maven下载
首先进入[Maven主页](http://maven.apache.org/),点击download 
![mavendown](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E5%B7%A5%E5%85%B7/img/mavendown.png)
因为是在windows下安装选择下载如下版本即可  
![mavendown2](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E5%B7%A5%E5%85%B7/img/mavendown2.png)
下载解压后如下图所示  
![mavendown3](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E5%B7%A5%E5%85%B7/img/mavendown3.png)
### Maven配置
打开刚刚解压好的文件夹,进入conf文件夹，选择settings.xml文件进行配置  

- 指定本地仓库  
在setting中添加如下代码：
  ```
  <localRepository>E:\maven\MavenRepository</localRepository>
  ```
  其中E:\maven\MavenRepository为自己创建的maven本地仓库,换成你自己创建的maven仓库文件夹即可。  
  
  ![本地仓库](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E5%B7%A5%E5%85%B7/img/%E6%9C%AC%E5%9C%B0%E4%BB%93%E5%BA%93.png)

- 配置阿里云镜像
在setting中添加如下代码：
  ```
  <mirror>
      <id>alimaven</id>
      <mirrorOf>central</mirrorOf>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
  </mirror>
  ```
![ali](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E5%B7%A5%E5%85%B7/img/ali.png)

- 修改JDK版本
在setting中添加如下代码：
  ```
    <profile>  
      <id>jdk-1.8</id>  
      <activation>  
        <activeByDefault>true</activeByDefault>  
        <jdk>1.8</jdk>  
      </activation>  
      <properties>  
        <maven.compiler.source>1.8</maven.compiler.source>  
        <maven.compiler.target>1.8</maven.compiler.target>  
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>  
      </properties>   
    </profile>
  ```

![JDK版本](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E5%B7%A5%E5%85%B7/img/jdk%E7%89%88%E6%9C%AC.png)

### IDEA中使用Maven
File-->Other Settings-->Settings for new project  
![]()  
maven home directory选择自己刚刚下载解压的maven文件夹  
user setting files选择我们刚刚修改的settings.xml文件  
local repository一般会自动识别，没有识别的话自己选择即可。  

接下来，新建maven项目,选择Webapp,之后右下角选择Enable auto-import即可。下载好后打开本地仓库，应该有28~30个文件，如果有的话即为成功。