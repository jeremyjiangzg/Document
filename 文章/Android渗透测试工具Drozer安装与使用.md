### [Drozer](https://labs.f-secure.com/tools/drozer/)

Comprehensive security and attack framework for Android.

drozer的基本功能就是通过分析AndroidManifest.xml，看四大组件中有没有可export的.

#### 安装

##### Mac电脑安装drozer

* 下载Mac版安装文件drozer(Python.whl)，如图

  <img src="https://github.com/jeremyjiangzg/Document/raw/master/image/drozer.png" alt="drozer" style="zoom:25%;" />

* 打开终端，安装pip

  ```
  sudo easy_install pip
  ```

* 安装drozer

  先通过cd 命令指向到你存放drozer-2.4.4-py2-none-any.whl文件的目录下，在运行下面的命令
  PS：安装过程中可能因为网络原因导致多次下载文件超时报错，不要慌，重新执行一次命令就行，要是出现重新执行命令还是报错，可以尝试关闭终端，重新打开终端，继续输入当前命令慢慢下载，直到成功。

  ```
  sudo pip install drozer-2.4.4-py2-none-any.whl --ignore-installed pyOpenSSL
  ```

* 安装twisted

  ```
  sudo pip install protobuf pyOpenSSL Twisted service_identity
  ```

PS：要是遇到如下错误

```jsx
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
```

解决方法如下（更新下面这个软件以后，在重新执行命令就可以了）

```csharp
xcode-select --install
```

* 下载安装手机agent.apk，如上图

  安装成功后，开启agent

  

##### Drozer使用

* 开启端口转发

  ```
  adb forward tcp:31415 tcp:31415
  ```

* 建立连接

  ```	
  drozer console connect
  ```

  出现如图所示，表示成功

  <img src="https://github.com/jeremyjiangzg/Document/raw/master/image/drozer_install_success.png" alt="drozer_install_success" style="zoom:25%;" />

* 使用app.package.info模块查看apk基本信息（package_name指的是你需要检测的APP包名)

  ```
  run app.package.info -a package_name
  ```

* 使用app.package.attacksurface模块识别攻击面

  所谓攻击面，应该就是指可export的安卓四大组件（activaty、broadcast receiver、content provider、service）

  ```
  run app.package.attacksurface package_name
  ```

* 使用app.activity.info模块查看activity组件信息

  ```
  run app.activity.info -a package_name
  ```

* 使用app.broadcast.info模块查看broadcast 组件信息

  ```
  run app.broadcast.info -a package_name
  ```

* 使用app.provider.info模块查看content provider组件信息

  ```
  run app.provider.info -a package_name
  ```

* 使用app.service.info模块查看service组件信息

  ```
  run app.service.info -a package_name
  ```

* 退出

  ```
  exit
  ```

  