# ubuntu配置vim主题



## 下载 wombat256.vim 主题文件

[wombat256.vim文件下载地址](https://www.vim.org/scripts/script.php?script_id=2465)

当前目录中已经下载好 wombat256.vim 文件，可直接使用（在文件管理器中可以看到该主题文件）





## 在用户家目录下创建文件夹（如果用户家目录下没有这些文件夹）

```shell
cd ~
mkdir .vim
cd .vim
mkdir colors

# 将 wombat256.vim 文件拷贝到 ~/.vim/colors 目录下
```





## 在 ~/.vimrc 配置文件中配置主题

```shell
vim ~/.vimrc
在文件末尾添加如下内容
" change color scheme
set t_Co=256
colo wombat256
```

