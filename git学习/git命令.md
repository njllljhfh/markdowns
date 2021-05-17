

## git 查看本地分支关联的远程分支

```
# 将 local_branch_name 替换为 要查看的本地分支名。
git rev-parse --abbrev-ref local_branch_name@{upstream}
```

---



## git 修改上次 commit 的时间

```
git commit --amend --date="Mon May 10 20:15:19 2021 +0800"
```

