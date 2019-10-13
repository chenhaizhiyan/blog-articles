#git教程
##创建版本库
	mkdir learngit
	cd learngit
	pwd
	ls
	git init
	git add readme.txt
	git commit -m "add a readme file"
##时光机穿梭
	git status
	git diff
###版本回退
	git log
	git reset --hard HEAD^
	git reset --hard 1094a
	git reflog	// 记录每一次命令 
###撤销修改
	git checkout -- readme.txt // git commit或git add时的状态
	// 1、未添加到暂存区时恢复到与版本库相同；
	// 2、已添加到暂存区时恢复到添加到暂存区后的状态。
	没有--时变成了分支管理命令
	git reset HEAD <file> // 撤销暂存区的修改
##远程仓库
###添加远程库
	git remote add origin git@server-name:path/repo-name.git	// 关联一个远程库
	git push -u origin master	// 第一次推送master分支的所有内容
	git push origin master	// 推送最新修改
##储藏
	git stash 储藏未完成的工作
	stash是本地的，不会通过git push命令上传到git server上
	实际应用中推荐给每个stash加一个message，用于记录版本，使用git stash save取代git stash命令。如：git stash save "test-cmd-stash"
	git stash pop : 从stash列表中取出最近的stash并从列表中移除记录
	git stash apply : 应用stash，后面无stash名时默认应用最近的，有名称的按名称应用，应用该stash记录不会从列表中删除
	git stash list : 查看暂存列表
	git stash drop : 移除stash,后面可以跟着stash名字
	git stash clear :删除所有缓存的stash
	git stash show : 查看stash 的diff,后面添加-p或--patch可以查看特定stash的全部diff
	git stash -a 或者 --all : 储藏包括未被跟踪和被忽略的文件