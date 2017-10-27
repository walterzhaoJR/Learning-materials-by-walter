#6.3 Git rm & rm
## git rm & rm的区别
* rm 只能删除本地的文件，知识删除了物理文件，但是没有从git中删除
* git rm 删除文件的同时，还会将删除的操作记录下来

## 总结
* git rm 后在提交的时候 git commit -m "message" 会自动将删除该文件的操作提交上去，在可以使用git status来观察到删除的文件
* 如果知识通过rm 删除了文件，那么提交的时候就要采用 git commit -am "messgae". 
* rm; git commit -am "message" 就是等同于 git rm;git commit -m "message"

* 所以推荐使用 git rm ; git commit -m "message"
