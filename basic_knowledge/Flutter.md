#### 1、dart:ui library

* show、hide关键字

  ```
  // Import only foo and bar.
  import 'package:lib1/lib1.dart' show foo, bar;
  
  // Import all names EXCEPT foo.
  import 'package:lib2/lib2.dart' hide foo;
  ```


#### 2、Flutter package

* 骨架屏 [shimmer](https://pub.dev/packages/shimmer)

  

#### 3、Flutter 修改默认生成的 `minSdkVersion`

```
问题
flutter pub get 会自动生成 android 目录，但是如果要修改 build.gradle 文件里面的内容，需要每次都要手动改一下，很麻烦
解决办法
修改 flutter 自动生成的 build.gradle 的模板文件
模板文件路径: flutter_sdk/packages/flutter_tools/templates/module/android/host_app_common/app.tmpl/build.gradle.tmpl
```

#### 4、以来方式

```
# git仓库
dependencies:
  flutter_package:
    git:
      url: https://github.com/xxx/flutter_package.git  #必填，仓库地址
      ref: commitID  #选填，表示git引用，可以是commit hash, tag 或者 branch
      path: packages/flutter_package #代码相对路径，当代码不在仓库根目录时需要填
      
#本地代码
dependencies:
  flutter_package:
    path: ./flutter_package    #不仅支持相对路径，还支持绝对路径
```

#### 5、版本冲突的解决办法

```
#强制版本
#在打包工程中使用确定的版本号可以解决部分冲突问题，前提是所指定的版本没有兼容性问题
dependencies:
  path_provider: 1.6.22
  
#版本覆盖
#在打包工程中利用dependency_overrides列出需要覆盖版本的三方库及想要的版本号
dependency_overrides:
  path_provider: ^1.6.22
```

#### 6、Flutter管理不同源的方式

* 公有pub仓库：该方式常用，不作说明
* 私有pub仓库

```
dependencies:
  flutter_package: 
    hosted:
      name: flutter_package
      url: http://your-package-server.com
    version: ^6.0.0

```

* git仓库

```
dependencies:
  flutter_package:
    git:
      url: https://github.com/xxx/flutter_package.git  #必填，仓库地址
      ref: commitID  #选填，表示git引用，可以是commit hash, tag 或者 branch
      path: packages/flutter_package #代码相对路径，当代码不在仓库根目录时需要填
```

* 本地代码

```
dependencies:
  flutter_package:
    path: ../flutter_package    #不仅支持相对路径，还支持绝对路径
```

```
版本号
语义版本号分为X.Y.Z三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新。

如果只是修复bug，需要更新Z位。
如果是新增了功能，但是向下兼容，需要更新Y位。
如果有大变动，向下不兼容，需要更新X位。
```

