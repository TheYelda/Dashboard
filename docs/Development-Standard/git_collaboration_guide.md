# Git协作规范

## 说明
本仓库有2个分支，master和dev。主要在dev分支上进行开发和测试，功能完善之后再合并到master。
```txt
├─master
└─dev
```

## 准备工作
在github上fork本仓库到你自己的github，将你fork之后的远程仓库克隆到本地，并在你的本地仓库上添加上游仓库，即源仓库。fork一般是fork了仓库的master分支，所以可能还需要自己去切出dev分支然后拉取源仓库dev分支上的内容。
```bash
git clone https://github.com/{你的github}/{仓库名}
# 上游的源仓库地址只需添加一次
git remote add upstream {源仓库地址}
# 切出dev分支并拉取源仓库dev分支上的内容
git checkout -b dev
git pull upstream dev
```

## 日常协作方式
```bash
# 提交你的代码
git add *
git commit -m "add something"
# 拉取上游的源仓库dev分支上的更新，可能会有冲突需要自行解决
git pull upstream dev
# 推送到你自己的远程仓库，注意这里是push到origin dev，不是upstream dev
git push origin dev
```
最后在GitHub网页上发起`pull request`即可。

## 备注
所有拉取远程仓库的更新的操作请在本地仓库上使用命令行完成。
严禁通过其他做法拉取更新（例如在GitHub网站上通过网页操作进行更新然后在本地pull origin dev），以免造成不必要的合并冲突以及多余的commit。

