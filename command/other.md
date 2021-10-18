#### 1、查找安装目录

```
//查找flutter环境目录
which flutter
```

#### 2、alias

规则：alias [参数] [命令别名]=[原命令名称]

注意：“=”前后不能有空格，指令中有空格需要加上单引号或者双引号

```
//列出所有别名
alias

//取消别名
unalias [别名]


```

#### 3、Flutter相关命令

##### a、在运行应用程序前，请运行`flutter analyze`测试你的代码。这个工具是一个静态代码检查工具，它是`dartanalyzer`工具的一个包装，主要用于分析代码并帮助开发者发现可能的错误，比如，Dart分析器大量使用了代码中的类型注释来帮助追踪问题，避免`var`、无类型的参数、无类型的列表文字等。

如果你使用IntelliJ的Flutter插件，那么分析器在打开IDE时就已经自动启用了，如果读者使用的是其它IDE，强烈建议读者启用Dart 分析器，因为在大多数时候，Dart 分析器可以在代码运行前发现大多数问题。

```
flutter analyzee
```

##### b、命令行创建flutter项目

```
//默认情况下，模板支持使用Java编写Android代码，或使用Objective-C编写iOS代码。要使用Kotlin或Swift，请使用-i和/或-a标志
//--androidx 支持androidx
//--org 包名
flutter create -i swift -a kotlin --androidx --org com.xxx projectName
```

#### 4、dart相关命令

```
dart compile exe bin/stagehand.dart
```

```
//Pub命令
//使用pub global命令将文件包注册到全局
pub global activate --source path <dart包路径>

//使用pub global list命令查看全局包列表
pub global list

//使用pub global deactivate命令释放包
pub global deactivate <package>
```



#### 4、快速查找文件

```
mdfind -name 关键字
```

#### 5、Window下配置SSH

```
1、下载安装git
2、在文件夹空白处右击，打开git bash窗口
3、生成秘钥
   #输入命令，并一路回车。 -C是用来识别这个密钥的注释
   ssh-keygen -t rsa -C "bread@food.com"
4、用记事本打开公钥，并复制（默认路径为C:\Users\用户名\.ssh\id_rsa.pub）
5、添加公钥
   登录GitLab网站，右上角头像 -> Settings -> SSH Keys中，添加复制的文本
6、使用ssh方式clone项目
   git clone ssh://git@192.168.111.222:5678/tom/demo.git
   下载过程中，出现如下提示时，需要输入yes
   ECDSA key fingerprint is SHA256:XXXXXX.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
7、配置用户名和邮箱（若不配置，在VSCode中提交代码会报错）
  git config --global user.name "Your Name"
  git config --global user.email "you@example.com"
   
```

##### 6、android studio Build Output控制台输出中文乱码问题

```
Help->Edit Custom CM Options
如果之前没有配置过，会弹出窗口问是否创建配置文件，点击Create
-Dfile.encoding=UTF-8
```

