

###安装

**Linux 中 yum install git**

### 配置相关

+ `git config --global key value` ： 配置指定属性
+ `git config --global user.name "huyangcheng"` ： 配置用户名
+ `git config --global user.email "576912212@qq.com"` ： 配置邮箱地址
+ `git config --unset user.name` ： 清除指定属性
+ `git config --global alias.st status"` ： 配置别名，输入** git st ** 即执行 ** git status **
+ `git config --global color.ui true` ： 设置颜色显示

> system，global 的区别 ： system 配置于系统中所有用户公用，global 配置于当前用户中，不使用配置与项目中只针对项目生效

### 常用命令

+ `git grep "内容"` ：在代码库中搜索指定内容的文件
+ `git rev-parse --git-dir` ： 显示工作区跟目录
+ `git rev-parse --show-cdup` ： 当前目录到根目录的深度
+ `git config -e [--global|--system]` ： 编辑当前项目或当前用户或系统级配置文件
+ `GIT_CONFIG filename.ext git config key.key1 "value"` ： 在指定文件中添加指定配置
+ `GIT_CONFIG filename.ext git config key.key1 ` ： 获取指定配置文件中的指定属性值
+ `xxxxx` ： xxxxxx
+ `xxxxx` ： xxxxxx
+ `xxxxx` ： xxxxxx


  







