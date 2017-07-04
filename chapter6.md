#第六章 github及git
##介绍git的相关使用
###基本使用
* 第一步：建立git仓库 
    * cd到你的目录
    ```linux
    git init
    ```
* 第二步：将项目的所有文件添加到仓库中
    * 如果想添加某个特定的文件，只需把.换成特定的文件名即可
    ```linux
    git add .
    ```
* 第三步：将add的文件commit到仓库
```linux
git commit -m "注释"
```
* 第四步：去github上创建自己的Repository，并且记录url，例如：
```linux
https://github.com/walterzhaoJR/shiyanlou_challenge
```
* 第五步 将本地的仓库关联到github上
```linux
git remote add origin https://github.com/walterzhaoJR/shiyanlou_challenge
```
* 第六步 上传github之前，要先pull一下
```linux
git pull origin master
```
* 第七步 上传代码到github远程仓库
```linux
git push -u origin master
```


##一些其他操作
* 克隆
```linux
git clone url
例如：
git clone https://github.com/walterzhaoJR/shiyanlou_challenge
```
* 删除github上的代码
```linux
先clone然后可以git rm，例如：
git rm <file_name>
```