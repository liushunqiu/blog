---
title: 'CenOS7部署Gitblit,push触发HOOK'
date: 2018-04-29 21:56:42
tags:
	- Linux
	- Gitblit
---
1.首先到http://gitblit.com/ 下载gitblit文件
```
>cd /usr/local/src
>wget http://dl.bintray.com/gitblit/releases/gitblit-1.8.0.tar.gz
>tar -zxf gitblit-1.8.0.tar.gz gitblit-1.8.0
> mv gitblit-1.8.0 gitblit
```
2.由于gitblit是使用Groovy语言开发的，所以需要JAVA_HOME，需要先提前配置好JAVA_HOME环境变量
3.配置gitblit git仓库，跟web端口号
```
>cd data
>vim defaults.properties
```
4.找到server.httpPort ,git.repositoriesFolder,修改成对应的地址
5.执行gitblit方法
```
>cd gitblit 
>./gitblit.sh
```
6.gitblit默认账号密码是admin，创建版本库。
7.编辑hook触发脚本文件,编辑内容
```
>cd groovy
>vim auto.groovy 
```
#内容如下：
```
import com.gitblit.GitBlit
import com.gitblit.Keys
import com.gitblit.models.RepositoryModel
import com.gitblit.models.TeamModel
import com.gitblit.models.UserModel
import com.gitblit.utils.JGitUtils
import com.gitblit.utils.StringUtils
import java.text.SimpleDateFormat
import org.eclipse.jgit.api.CloneCommand
import org.eclipse.jgit.api.Git
import org.eclipse.jgit.lib.Repository
import org.eclipse.jgit.lib.Config
import org.eclipse.jgit.revwalk.RevCommit
import org.eclipse.jgit.transport.ReceiveCommand
import org.eclipse.jgit.transport.ReceiveCommand.Result
import org.eclipse.jgit.util.FileUtils
import org.slf4j.Logger
import org.eclipse.jgit.api.ResetCommand.ResetType
import org.eclipse.jgit.api.ResetCommand

logger.info("localclone hook triggered by ${user.username} for ${repository.name}")

def rootFolder = '/usr/local/nginx/html'
def bare = false
def cloneAllBranches = true
def cloneBranch = 'refs/heads/master'
def includeSubmodules = true

def repoName = repository.name
def destinationFolder = new File(rootFolder, StringUtils.stripDotGit(repoName))
def srcUrl = 'file://' + new File(gitblit.getRepositoriesFolder(), repoName).absolutePath

// delete any previous clone
if (destinationFolder.exists()) {
	Git git = Git.open(destinationFolder)
    git.fetch().call()
    logger.info("执行git fetch")
    ResetCommand resetCmd = git.reset()
    resetCmd.setMode(ResetType.HARD)
    resetCmd.setRef("FETCH_HEAD") 
    resetCmd.call()
	logger.info("执行git reset")
	git.pull().call()
	logger.info("执行git pull强制同步服务器结束！")
	//FileUtils.delete(destinationFolder, FileUtils.RECURSIVE)
}else{
	// clone the repository
	logger.info("cloning ${srcUrl} to ${destinationFolder}")
	CloneCommand cmd = Git.cloneRepository();
	cmd.setBare(bare)
	if (cloneAllBranches)
		cmd.setCloneAllBranches(true)
	else
		cmd.setBranch(cloneBranch)
	cmd.setCloneSubmodules(includeSubmodules)
	cmd.setURI(srcUrl)
	cmd.setDirectory(destinationFolder)
	Git git = cmd.call();
	git.repository.close()
	// report clone operation success back to pushing Git client
	clientLogger.info("${repoName} cloned to ${destinationFolder}")
}
```
8.编辑版本库，配置我们刚才编写的groovy文件
![](/images/clipboard.png)
9.配置SSH ，避免需要输入密码
