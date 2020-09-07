## 将Flutter module打包成aar，步骤如下：

##### 1、在Flutter module根目录下创建root_build.gradle文件

```
buildscript {
    repositories {
        google()
        jcenter()
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.0'
    }
}

allprojects {
    repositories {
        google()
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

// 在project.afterEvaluate中读取group version等参数并apply上传maven脚本
subprojects {
    project.afterEvaluate {
        project.plugins.withId('com.android.library') {
            if (project.name != 'app') {
                // 读取shell脚本中运行./gradlew xxx命令时传递的参数
                project.group = project.rootProject.hasProperty('gArtGroupId') ? project.rootProject.ext.gArtGroupId : null
                project.version = project.rootProject.hasProperty('gArtVersion') ? project.rootProject.ext.gArtVersion : null

                // 第三方的包在group里加上.thirdparty
                if (project.projectDir.absolutePath.indexOf("pub-cache/hosted/pub.flutter-io.cn") >= 0
                        || project.projectDir.absolutePath.indexOf("pub-cache\\hosted\\") >= 0) {
                    project.group += ".thirdparty"
                }

                println "group: $project.group:$project.name:$project.version"

                // 给每个module添加上传maven脚本
                def mavenScriptPath = project.rootProject.file('../android_flutter_maven.gradle')
                project.apply from: mavenScriptPath
            }
        }
    }
}

// 很重要，用来覆盖各个自依赖中的group和version
// ./gradlew clean assembleRelease -PfGroupId=${fGroupId} -PfArtifactId=${fArtifactId} -PfVersion=${fVersion}
// 执行了这个命令，根目录的build.gradle就会执行，这时候把参数设置到project.rootProject.ext，后面各个subProject就可以拿到
final def artGroupId = project.hasProperty('fGroupId') && project.fGroupId ? project.fGroupId : null
final def artVersion = project.hasProperty('fVersion') && project.fVersion ? project.fVersion : null
project.rootProject.ext {
    gArtGroupId = artGroupId
    gArtVersion = artVersion
}
```

##### 2、在Flutter module根目录下创建android_flutter_maven.gradle文件

```
apply plugin: 'maven'

// 这里不需要artifacts，uploadArchives命令会自动生成并上传./build/outputs/flutter-release.aar，不然出现下面错误
// A POM cannot have multiple artifacts with the same type and classifier
//artifacts {
//    archives file('./build/outputs/flutter-release.aar')
//}
final def MAVEN_URL = "http://nexus.xxx.cn/repository/flutter-module/"
final def MAVEN_URL_SNAPSHOT = "http://nexus.xxx.cn/repository/flutter-module-snapshots/"
final def MAVEN_ACCOUNT_NAME = "accountName"
final def MAVEN_ACCOUNT_PWD = "accountPassword"

final def artGroupId = project.group
final def artVersion = project.version
final def artifactId = project.hasProperty('fArtifactId') && project.fArtifactId ? project.fArtifactId : null
final def isFlutterModule = project.hasProperty('fModule') && project.fModule ? project.fModule : false

if (artifactId == null || artVersion == null) {
    return
}

// 因为只要执行./gradlew xxx等命令，rootProject和subProject的build.gradle都要执行一次，
// 所以这里要判断当前module和是否和正在处理的module一样，不是相同module就不处理，
// 但是因为不同业务的flutter module名称都是flutter并且artifactId和module名称又不相同，所以要通过isFlutterModule参数区分
if (!project.name.equals(artifactId) && (!project.name.equals("flutter") || !isFlutterModule)) {
    return
}

uploadArchives {
    repositories {
        mavenDeployer {
            println "maven url: ${artGroupId}:${artifactId}:${artVersion}"

            if (artVersion.contains("-SNAPSHOT")) {
                snapshotRepository(url: MAVEN_URL_SNAPSHOT) {
                    authentication(userName: MAVEN_ACCOUNT_NAME, password: MAVEN_ACCOUNT_PWD)
                }
            } else {
                repository(url: MAVEN_URL) {
                    authentication(userName: MAVEN_ACCOUNT_NAME, password: MAVEN_ACCOUNT_PWD)
                }
            }

            pom.groupId = artGroupId
            pom.artifactId = artifactId
            pom.version = artVersion

            pom.project {
                licenses {
                    license {
                        name 'The Apache Software License, artVersion 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
            }
        }
    }
}
```

##### 3、在Flutter module根目录下创建上传shell脚本androidFlutter.sh文件

```
#!/usr/bin/env bash

######################################################################
# 使用方式: ./androidFlutter version artifactId groupId
# 如： ./androidFlutter 0.0.14-SNAPSHOT kwmoduleflutter com.kidswant.flutter
# 注意，在使用该命令之前先把.android/gradle.properties中的FLUTTER_SOURCE变为1
######################################################################

# 定义默认groupId
readonly DEF_GROUP='com.asizon.flutter'
readonly DEF_ARTIFACTID='umbrella'
readonly DEF_BUILD_TYPE='false'

# 读取参数
fVersion=$1         # flutter module的版本和所有的插件的版本保持一致
isRelease=$2         # flutter module的版本和所有的插件的版本保持一致
#fArtifactId=$2     # flutter module的artifactId
#fGroupId=$3        # 所有library的groupId都一样，默认是com.asizon.flutter
fArtifactId=$DEF_ARTIFACTID
fGroupId=$DEF_GROUP

# 检查参数
#if [ -z "${fVersion}" ] || [ -z "${isRelease}" ]
#then
#    echo "fVersion or isRelease is null"
#    exit
#fi
if [ -z "${fVersion}" ] ]
then
    echo "param version is null"
    exit
fi

if [ -z "$isRelease" ] # 当串的长度不大于0时为真
then
  echo "deploy debug version";
  isRelease=$DEF_BUILD_TYPE
elif [ "$isRelease" == 'true' ];
then
  echo "deploy release version";
elif [ "$isRelease" == 'false' ];
then
  echo "deploy release version";
  isRelease=$DEF_BUILD_TYPE
else
  echo "deploy param error!";
  exit
fi

if [ "$isRelease" == "false" ]
then
  fVersion="${fVersion}-SNAPSHOT"
fi

###### 1. 获取/更新包
echo "Start flutter packages get..."
flutter packages get
echo -e "\n"

###### 2. 复制root_build.gradle文件覆盖.android/build.gradle（因为每次执行flutter packages get或者upgrade命令，.android目录都会重新生成）
cp -rf ./root_build.gradle ./.android/build.gradle


###### 3. 读取.flutter-plugin中的插件，进入对应的工程目录，编译成aar并上传
for line in $(cat .flutter-plugins)
#while read -r line
do
  echo "line content: $line"
  if [[ "$line" =~ "=" ]]
  then
    echo "line contains plugin."
  else
    continue
  fi

  plugin_name=${line%%=*}
  plugin_path=${line##*=}
  echo "Build and publish plugin: [$plugin_name]=$plugin_path"

  cd .android || exit

  # 在使用变量比较字符串之前，最好在判断之前加一个判断变量是否为空  或者使用双引号将其括起来，不然会出现异常[: ==: unary operator expected
  if [ "$isRelease" == "false" ]
  then
    echo "start build debug aar for plugin [$plugin_name]"
    ./gradlew :"${plugin_name}":clean :"${plugin_name}":assembleDebug -PfGroupId=${fGroupId} -PfArtifactId="${plugin_name}" -PfVersion="${fVersion}"
  else
    echo "start build release aar for plugin [$plugin_name]"
    # uploadArchives命令自动回生成release模式的aar，不用执行该命令
    ./gradlew :"${plugin_name}":clean :"${plugin_name}":assembleRelease -PfGroupId=${fGroupId} -PfArtifactId="${plugin_name}" -PfVersion="${fVersion}"
  fi

    echo "start upload maven for plugin [$plugin_name]"
    ./gradlew :"${plugin_name}":uploadArchives -PfGroupId=${fGroupId} -PfArtifactId="${plugin_name}" -PfVersion="${fVersion}"

    echo -e "\n"
    cd ../
done
#done < .flutter-plugins

###### 4.上传flutterModule
cd .android || exit

./gradlew clean

if [ "$isRelease" == "false" ]
then
  echo "start build debug aar for [moduleflutter]"
  ./gradlew clean assembleDebug -PfGroupId=${fGroupId} -PfArtifactId="${fArtifactId}" -PfVersion="${fVersion}" -PfModule=true
else
  echo "start build release aar for [moduleflutter]"
  ./gradlew clean assembleRelease -PfGroupId=${fGroupId} -PfArtifactId="${fArtifactId}" -PfVersion="${fVersion}" -PfModule=true
fi

echo "start upload maven for [moduleflutter]"
./gradlew :flutter:uploadArchives -PfGroupId=${fGroupId} -PfArtifactId="${fArtifactId}" -PfVersion="${fVersion}" -PfModule=true

cd ../
```



