### 1. 创建新分支

##### 最新pprd分支为基准拉取开发分支

``` shell 
git checkout -b feat-optimize-1280 origin/pprd 
```

##### 将本地分支推送到远程分支
``` shell 
git push origin feat-optimize-1280
```

##### 设置本地分支对应的远程分支
``` shell
git branch --set-upstream-to=origin/feat-optimize-1280
```

### 2. 删除分支

##### 删除远程分支
``` shell
git push origin --delete tmp
```

##### 删除本地分支
``` shell
git branch -D tmp
```

### 3.回退代码到某此提交
``` shell
git reset --hard 139dcfaa558e3276b30b6b2e5cbbb9c00bbdca96
git push -f -u origin sit
```

### 4.修改分支命令
``` shell
git branch -m br_rename_old br_rename_new
```
