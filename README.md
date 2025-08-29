# 1) 查看当前分支与改动
git status

# 2) 把所有改动（包含未跟踪文件）先暂存到 stash
git stash push -u -m "wip before merging origin/case-service-j"

# 3) 获取最新远端
git fetch

# 4) 合并远端分支到当前分支
git merge origin/case-service-j

# 5) 把刚才的本地改动取回来
git stash pop

# 6) 逐个解决冲突、测试，然后提交
git add -A
git commit
