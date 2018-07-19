
#### 分支

https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6


#### 查看状态

```
git status
```
#### 撤销修改

```
撤销所有修改

git checkout . 
```

```
撤销单独文件修改

git chechout --file
```
#### 撤销新增

```
git clean -df

-d表示同时移除目录
-f表示force
-i
-n
因为在git的配置文件中,clean.requireForce=true,如果不加-f,clean将会拒绝执行.
```
#### 远程仓库

```
查看
git remote -v
```

```
新增
git remote add o http://aaaa/aa.git
```

```
删除
git remote remove origin
```

#### 远程仓库分支

```
查看
git branch -r
```

```
拉取远程仓库分支到本地仓库分支
git fetch orgin branch_1 :branch1
远程分支branch_1到本地分支branch1
```

```
git checkout -b dev 新建并切换到本地dev分支

git pull origin dev 本地分支与远程分支相关联

git checkout -b test 在本地新建分支test

git push origin test   远程仓库中也就创建了一个test分支
```

### 分支合并

git merage

git rebase


#### 版本回退


// 回退到之前的版本，都回退，慎重操作
git reset --hard HEAD

// 回退commit信息，源码不回退，目的优化commit提交信息
git reset --soft 564asdasd

#### .gitignore失效
原因因为先将文件add到仓库中管理了，后添加的.gitignore文件

执行下面代码即可
```
git pull origin master
git rm -r --cached .
git status
git add .
git commit -m "del cache"
git push origin master
```