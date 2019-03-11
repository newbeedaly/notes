
#git命令使用

	#下载
	git clone url 拉取代码
	git pull 更新
	
	#上传文件
	git add fileName
	git commit -m '首次提交'

	git status 查看状态

	git push
	type username/password

	#冲突
	git push 冲突
	git pull 拉取代码
	git diff 查看冲突
	vim fileName 打开文件，解决冲突
	git add .
	git commit -m '解决冲突'
	
	git push
	type username / password

	#回到过去
	
	git log 查看提交日志和commitID/版本ID
	git reset --hard commitId 回到过去 找回代码

	git reflog 查看操作记录
	git reset --hard commitId 回到当前 修改当前代码

	#建立里程碑

	在github上，点击版本release,建立一个新版本，比如 1.0
	
	#分支开发之分支合并

	git branch   查看分支

	git remote   查看远程分支
	
	git branch test    创建分支
	
	git branch --set-upstream-to orgin/prod prod    将本地分支关联到远程分支上
	
	git remote add prod https://github.com/newbeedaly/......    创建远程分支
	
	git fetch    更新所有分支
	
	git branch -a    查看所有分支(本地和远程)

	查看分支：git branch
	
	创建分支：git branch 分支名
	
	切换分支：git checkout 分支名
	
	创建+切换分支：git checkout -b 分支名
	
	合并某分支到当前分支：git merge 分支名

	删除分支：git branch -d 分支名

	删除远程分支：git push origin :name

	撤销修改：git checkout -- file


	问题1:工作时，你突然接到一个电话说有个很严重的问题需要紧急修补。 你将按照如下方式来处理：

		1.切换到你的线上分支（production branch）。
		
		2.为这个紧急任务新建一个分支，并在其中修复它。
		
		3.在测试通过之后，切换回线上分支，然后合并这个修补分支，最后将改动推送到线上分支。
		
		4.切换回你最初工作的分支上，继续工作。

	0-先切换到主分支
	
		git checkout master 
		
	1-创建+切换分支：git checkout -b 分支名
		
		2-修复代码
	
	3-合并某分支到当前分支：git merge 分支名

		删除分支：git branch -d 分支名
		删除远程分支：git push origin :name

	4-切换分支：git checkout 分支名



	问题2:从分支上创建一个分支

		1.先克隆一个项目
		
			git clone url

		2.再转移到分支中
		
			$ git status
				位于分支 master
				您的分支与上游分支 'origin/master' 一致。
				无文件要提交，干净的工作区
			$ git checkout gaodian 
				分支 gaodian 设置为跟踪来自 origin 的远程分支 gaodian。
				切换到一个新分支 'gaodian'

		3.再拉取最新的代码
		
			git pull origin gaodian

		4.再创建新的分支
		
			git checkout -b laobandianqi
			这个时候，分支的代码就是基于gaodian这个分支的
		
		5.将分支推送到线上
		
		$ git push origin laobandianqi
			Total 0 (delta 0), reused 0 (delta 0)
			remote: 
			remote: To create a merge request for laobandianqi, visit:
			remote:   http://106.14.59.204/zhubin/store/merge_requests/new?merge_request%5Bsource_branch%5D=laobandianqi
			remote: 
			To http://106.14.59.204/zhubin/store.git
			 * [new branch]      laobandianqi -> laobandianqi
			这个时候线上就有新的分支了。					


	

	#常用命令图

	![git命令图片](https://i.imgur.com/J5sXTMA.jpg)