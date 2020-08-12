#### 1.安装并配置Maven（Mac环境）

* 下载地址：http://maven.apache.org/download.cgi，下载maven文件并解压。

* 配置环境变量

  编辑.bash_profile文件：

  ```
  vim ~/.bash_profile
  ```

  配置maven文件地址：

  ```
  #maven
  export M2_HOME=/Users/jiangzhiguo/develop/tool/apache-maven-3.6.3
  export PATH=$PATH:$M2_HOME/bin
  ```

  保存文件，执行如下命令使配置生效：

  ```
  source ~/.bash_profile
  ```

  验证：

  ```
  mvn -v
  ```

  如图表示配置成功

  <img src="/Users/jiangzhiguo/develop/learning/md/image/apache_maven.png" alt="apache_maven" style="zoom:67%;" />

#### 2.编译aar产物并查看pom依赖信息

* 生成module产物

  ```
  flutter build aar --release
  ```

* 查看产物pom文件配置，需要对flutter第三方库依赖的产物aar分别上传maven仓库，如图：

  <img src="/Users/jiangzhiguo/develop/learning/md/image/flutter_output.png" alt="flutter_output" style="zoom:25%;" />

  <img src="/Users/jiangzhiguo/develop/learning/md/image/flutter_dependency_01.png" alt="flutter_dependency_01" style="zoom:25%;" />

  <img src="/Users/jiangzhiguo/develop/learning/md/image/flutter_dependency_02.png" alt="flutter_dependency_02" style="zoom:25%;" />

  <img src="/Users/jiangzhiguo/develop/learning/md/image/flutter_dependency_03.png" alt="flutter_dependency_03" style="zoom:25%;" />



#### 3.编写shell脚本

​    在项目根目录下创建deploy.sh文件

```
#Note that groupId, artifactId, version and packaging arguments are not included here because deploy-file goal will get these information from the given POM.
deploy_aar(){
    mvn deploy:deploy-file \
    -DpomFile="$FILE_PATH/$NAME.pom" \
    -DgeneratePom=false \
    -Dfile="$FILE_PATH/$NAME.aar" \
    -Durl="http://nexus.xiaoheiban.cn/repository/flutter/" \
    -Dversion="1.0.0" \
    -DrepositoryId="nexus" \
    -Dpackaging=aar \
    -s="mvn-settings.xml"
}

ROOT_PATH="/Users/jiangzhiguo/develop/xhb"

export FILE_PATH="$ROOT_PATH/flutter_umbrella/build/host/outputs/repo/com/flutter/flutter_umbrella/flutter_release/1.0"
export NAME="flutter_release-1.0"
deploy_aar

FILE_PATH="$ROOT_PATH/flutter_umbrella/build/host/outputs/repo/io/flutter/plugins/pathprovider/path_provider_release/1.0"
NAME="path_provider_release-1.0"
deploy_aar

FILE_PATH="$ROOT_PATH/flutter_umbrella/build/host/outputs/repo/io/flutter/plugins/sharedpreferences/shared_preferences_release/1.0"
NAME="shared_preferences_release-1.0"
deploy_aar

FILE_PATH="$ROOT_PATH/flutter_umbrella/build/host/outputs/repo/com/plugin/logger_plugin/logger_plugin_release/1.0"
NAME="logger_plugin_release-1.0"
deploy_aar

FILE_PATH="$ROOT_PATH/flutter_umbrella/build/host/outputs/repo/com/xhb/crash_report_plugin/crash_report_plugin_release/1.0"
NAME="crash_report_plugin_release-1.0"
deploy_aar
```

#### 4.配置权限

* 打开本地mavensettings文件 如：/Users/jiangzhiguo/develop/tool/apache-maven-3.6.3/conf/settings.xml，复制一份到功能根目录，重命名为mvn-settings.xml

* 打开mvn-settings,找到<servers></servers>节点，并配置authentication information

  ```
  <servers>
      <server>
        <id>nexus</id>
        <username>develop</username>
        <password>xinkb123!@#</password>
      </server>
    </servers>
  ```

#### 5.部署

* 打开终端，执行./deploy.sh命令

  ```
  ./deploy
  ```

* 如果报错Permission denied，需要修改权限

  ```
  chmod 777 deploy.sh 
  ```

* 部署成果效果图，已晓成绩项目为例

  <img src="/Users/jiangzhiguo/develop/learning/md/image/flutter_maven.png" alt="flutter_maven" style="zoom:25%;" />

##### 参考地址

https://maven.apache.org/guides/mini/guide-3rd-party-jars-remote.html

