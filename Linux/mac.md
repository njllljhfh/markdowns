# MAC系统笔记



## mac系统环境变量

Mac系统下的环境变量：

```swift
a. /etc/profile 
b. /etc/paths 
c. ~/.bash_profile 
d. ~/.bash_login 
e. ~/.profile 
f. ~/.bashrc 
```

其中a和b是`系统级别`的，系统启动就会加载，其余是用户接别的。c,d,e按照从前往后的`顺序读取`，如果c文件存在，则后面的几个文件就会被忽略`不读了`，以此类推。~/.bashrc没有上述规则，它是bash shell打开的时候载入的。这里建议在c中添加环境变量，以下也是以在c中添加环境变量来演示的。

如果装了 **Oh My Zsh**，下面两个文件也可能配置了PATH

```bash
~/.zprofile
~/.zshrc
```



---



