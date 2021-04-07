#### Tag
```
#1、查看所有标签
git tag   

#2、本地打新标签
git tag <tag name>   

#3、在某个commit上打tag
git tag <tag name> 16098ee1cbbc8a1884e19c6681735e1792f9b577   

#4、查看commit版本号
git log 

#5、附注标签
git tag -a <tag name> -m <message>
#例如, 打v1.1.0标签
git tag -a v1.1.0 -m 'v1.1.0 release'

#6、本地tag推送到远程
git push origin <tag name>  // 推送一个标签到远程
#or
git push origin --tags   // 推送全部未推送的本地标签

#7、删除本地标签
git tag -d <tag name>

#8、删除远程标签
git push origin --delete tag <tagname>
```

```
# mac sourcetree反复要求输入密码解决方案
git config --global credential.helper osxkeychain
```

```
# Git global setup
git config --global user.name "jiangzhiguo"
git config --global user.email "13340113093@163.com"

# Create a new repository
git clone http://47.97.170.255:8090/tech-share/android/plt-hook.git
cd plt-hook
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master

# Push an existing folder
cd existing_folder
git init
git remote add origin http://47.97.170.255:8090/tech-share/android/plt-hook.git
git add .
git commit -m "Initial commit"
git push -u origin master

# Push an existing Git repository
cd existing_repo
git remote rename origin old-origin
git remote add origin http://47.97.170.255:8090/tech-share/android/plt-hook.git
git push -u origin --all
git push -u origin --tags
```

#### git clone 单个文件或者文件夹

```
# 创建仓库并进入文件夹
git init thirds && cd thirds
//设置允许克隆子目录
git config core.sparsecheckout true
//关联远程仓库地址
git remote add origin https://github.com/flutter/plugins.git
//设置要克隆仓库子目录路径
echo "directory" >> .git/info/sparse-checkout
git pull origin master
```

#### 删除远程文件夹

```
#方法一 以删除test文件夹为例
git rm -r --cached test //--cached不会把本地的test删除
git commit -m 'delete test dir'
git push -u origin master

#方法二
#如果误提交的文件夹比较多，方法一也较繁琐直接修改.gitignore文件,将不需要的文件过滤掉，然后执行命令
git rm -r --cached .
git add .
git commit
git push -u origin master
```

