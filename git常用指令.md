

## 分支系统

### 创建分支

命令：git branch 分支名

### 查看当前分支

命令：git branch

### 查看所有分支

命令：git checkout -a

### 切换当前分支

命令：git checkout 分支名

### 创建分支并且切换分支，推送远程仓库

命令：git checkout -b 分支名

命令：git push origin 分支名

### 合并分支

命令：git merge 分支名
合并只能在master上进行的。
一般，需要将切换到master分支，然后再进行git merge操作。

### 删除分支

命令：git branch -d 分支名
如果某个分支不再需要，就可以将其删除。
这个通常是在master上进行的。









## github

###git clone

`git clone` +地址

举个例子：

```
git clone https://gitee.com/softfuwushe/little_red_horse_2.git
```



### 下载指定分支的指令

`git clone -b `分支名 + 地址

举个例子：

```
git clone -b test https://gitee.com/softfuwushe/little_red_horse_2.git
```

意为下载`little_red_horse_2`上面的`test`分支


