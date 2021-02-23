#### 1、将a.jar和b.jar拷贝至同一目录下

#### 2、打开终端并进入到该目录下

#### 3、分别执行命令解压jar（注意项：解压的先后顺序会影响Main入口配置，后者覆盖前者）

      ```
jar -xvf a.jar
jar -xvf b.jar
      ```

#### 4、移除a.jar和b.jar源文件

#### 5、合成新jar

```
jar -cvfM new.jar .
```

###### 提示：输入jar命令可以查看jar命令的用法

