#Git clone 一个仓库的过程

##具体的操作如下
```linux
mkdir test;

cd test;

git init

git remote add origin https://github.com/walterzhaoJR/TestGit.git

git pull origin master

这下就可以自己添加文件了


git add .

git commit -m "message"

git push -u origin master

```

##一些异常的处理

* 如果执行git remote add origin https://github.com/findingsea/myRepoForBlog.git，出现错误：
fatal: remote origin already exists
则：
```linux
git remote rm origin
```

* 如果git pull 失败 ,提示：fatal: refusing to merge unrelated histories
则：
```linux
git pull origin master --allow-unrelated-histories
```

