#1. 初次运行 Git 前的配置

##用户信息

第一个要配置的是你个人的用户名称和电子邮件地址。这两条配置很重要，每次 Git 提交时都会引用这两条信息，说明是谁提交了更新，所以会随更新内容一起被永久纳入历史记录：

	$ git config --global user.name "dyh"
    $ git config --global user.email cbyniypeu@xxx.com

如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。

##文本编辑器

接下来要设置的是默认使用的文本编辑器。Git 需要你输入一些额外消息的时候，会自动调用一个外部文本编辑器给你用。默认会使用操作系统指定的默认编辑器，一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：

	$ git config --global core.editor emacs

##差异分析工具

还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话：

	$ git config --global merge.tool vimdiff

##查看配置信息

要检查已有的配置信息，可以使用 git config --list 命令：

	$ git config --list
		core.filemode=false
		core.quotepath=false
		core.editor=vim
		user.name=dyh
		user.email=cbyniypeu@163.com
	    ...

有时候会看到重复的变量名，那就说明它们来自不同的配置文件，不过最终 Git 实际采用的是最后一个。

也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可，如：

	$ git config user.name
    	dyh

#2.	取得项目的 Git 仓库

有两种取得 Git 项目仓库的方法。

第一种是在现存的目录下init新的 Git 仓库。

第二种是从已有的 Git 仓库克隆出一个仓库，然后在自己工作目录中初始化新仓库

##本地新建一个仓库

要对现有的某个项目init新的 Git 仓库：

	$ git init

初始化后，在当前目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。不过目前，仅仅是按照既有的结构框架初始化好了里边所有的文件和目录，但我们还没有开始跟踪管理项目中的任何一个文件。
如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

    $ git add README
    $ git commit -m 'initial project'

##从现有仓库克隆

如果想对某个开源项目出一份力，可以先把该项目的 Git 仓库复制一份出来，可以用下面的命令：

	$ git clone git@github.com:dengyhgit/testGithub.git

这会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录，然后从中取出最新版本的文件拷贝。如果进入这个新建的 testGithub 目录，你会看到项目中的所有文件已经在里边了，准备好后续的开发和使用。如果希望在克隆的时候，自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：

	$ git clone git://github.com/schacon/grit.git mygit

唯一的差别就是，现在新建的目录成了 mygit，其他的都和上边的一样。
Git 支持许多数据传输协议。之前的例子使用的是 SSH 协议，不过你也可以用 http(s):// 或者 git:// 表示的 git 传输协议。

#3. 记录每次更新到仓库

##检查当前文件状态

要确定哪些文件当前处于什么状态，可以用 git status 命令。如果在克隆仓库之后立即执行此命令，会看到类似这样的输出：

	$ git status
		位于分支 master
		无文件要提交，干净的工作区

这说明你现在的工作目录相当干净。换句话说，所有已跟踪文件在上次提交后都未被更改过。此外，上面的信息还表明，当前目录下没有出现任何处于未跟踪的新文件，否则 Git 会在这里列出来。最后，该命令还显示了当前所在的分支是 master，这是默认的分支名称

现在让我们用 touch 创建一个新文件 README，保存退出后运行 git status 会看到该文件出现在未跟踪文件列表中：

	$ touch README
	    $ git status
			位于分支 master
			未跟踪的文件:
			  （使用 "git add <file>..." 以包含要提交的内容）
			
			        README
			
			提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）


使用命令 git add 开始跟踪一个新文件。所以，要跟踪 README 文件，运行：

	$ git add README

此时再运行 git status 命令，会看到 README 文件已被跟踪，并处于暂存状态：

	$ git status
	位于分支 master
	要提交的变更：
	  （使用 "git reset HEAD <file>..." 撤出暂存区）
	
	        新文件：   README



##暂存已修改文件

假设此时，你想要在 README 里添加内容，添加后，你准备好提交。不过且慢，再运行 git status 看看：

	$ echo "readme" >> README
	$ git status
	位于分支 master
	要提交的变更：
	  （使用 "git reset HEAD <file>..." 撤出暂存区）
	
	        新文件：   README
	
	尚未暂存以备提交的变更：
	  （使用 "git add <file>..." 更新要提交的内容）
	  （使用 "git checkout -- <file>..." 丢弃工作区的改动）
	
	        修改：     README

	

README 文件出现了两次！一次算未暂存，一次算已暂存，实际上 Git 只不过暂存了你运行 git add 命令时的版本，如果现在提交，那么提交的是添加内容前的版本，而非当前工作目录中的版本。所以，运行了 git add 之后又作了修订的文件，需要重新运行 git add 把最新版本重新暂存起来：

	$ git add README
	    $ git status
		位于分支 master
		要提交的变更：
		  （使用 "git reset HEAD <file>..." 撤出暂存区）
		
		        新文件：   README


##忽略某些文件

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。我们可以创建一个名为 .gitignore 的文件，列出要忽略的文件模式。来看一个实际的例子：
	
	$ cat .gitignore
	    *.[oa]
	    *~

第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的，我们用不着跟踪它们的版本。第二行告诉 Git 忽略所有以波浪符（~）结尾的文件，许多文本编辑软件（比如 Emacs）都用这样的文件名保存副本。此外，你可能还需要忽略 log，tmp 或者 pid 目录，以及自动生成的文档等等。要养成一开始就设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。

文件 .gitignore 的格式规范如下：

    所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。
    可以使用标准的 glob 模式匹配。
    匹配模式最后跟反斜杠（/）说明要忽略的是目录。
    要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。星号（*）匹配零个或多个任意字符；[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（?）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。

我们再看一个 .gitignore 文件的例子：
	
	# 此为注释 – 将被 Git 忽略
	    # 忽略所有 .a 结尾的文件
	    *.a
	    # 但 lib.a 除外
	    !lib.a
	    # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
	    /TODO
	    # 忽略 build/ 目录下的所有文件
	    build/
	    # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
	    doc/*.txt

##查看已暂存和未暂存的更新

实际上 git status 的显示比较简单，仅仅是列出了修改过的文件，如果要查看具体修改了什么地方，可以用 git diff 命令。它能回答我们的两个问题：当前做的哪些更新还没有暂存？有哪些更新已经暂存起来准备好了下次提交？ git diff 会使用文件补丁的格式显示具体添加和删除的行。

假如再次修改 README 文件,要查看尚未暂存的文件更新了哪些部分，不加参数直接输入 git diff：

	$ echo "test" >> README
	$ git diff
		diff --git a/README b/README
		index 8178c76..5fc37db 100644
		--- a/README
		+++ b/README
		@@ -1 +1,2 @@
		 readme
		+test

此命令比较的是工作目录中当前文件和暂存区域快照之间的差异，也就是修改之后还没有暂存起来的变化内容。

若要看已经暂存起来的文件和上次提交时的快照之间的差异，可以用 git diff --cached 命令。（Git 1.6.1 及更高版本还允许使用git diff --staged）来看看实际的效果：

	$ git diff --cached
		diff --git a/README b/README
		new file mode 100644
		index 0000000..8178c76
		--- /dev/null
		+++ b/README
		@@ -0,0 +1 @@
		+readme
	(注：README还没有向版本库提交)


请注意，单单 git diff 不过是显示还没有暂存起来的改动，而不是这次工作和上次提交之间的差异。所以有时候你一下子暂存了所有更新过的文件后，运行 git diff 后却什么也没有，就是这个原因。

若要看工作区的文件和上次提交时的快照之间的差异，可以用 git diff head 命令：

	$ git diff head
	diff --git a/README b/README
	new file mode 100644
	index 0000000..5fc37db
	--- /dev/null
	+++ b/README
	@@ -0,0 +1,2 @@
	+readme
	+test

##提交更新

现在的暂存区域已经准备妥当可以提交了。在此之前，请一定要确认还有什么修改过的或新建的文件还没有 git add 过，否则提交的时候不会记录这些还没暂存起来的变化。所以，每次准备提交前，先用 git status 看下，是不是都已暂存起来了，然后再运行提交命令 git commit：

	$ git commit

这种方式会启动文本编辑器以便输入本次提交的说明。（默认会启用 shell 的环境变量 $EDITOR 所指定的软件，一般都是 vim 或 emacs。当然也可以按照第一章介绍的方式，使用 git config --global core.editor 命令设定你喜欢的编辑软件。）

编辑器会显示类似下面的文本信息（本例选用 Vim 的屏显方式展示）：

	  1
	  2 # 请为您的变更输入提交说明。以 '#' 开始的行将被忽略，而一个空的提交
	  3 # 说明将会终止提交。
	  4 # 位于分支 master
	  5 # 要提交的变更：
	  6 #   新文件：   README
	  7 #
	  8 # 尚未暂存以备提交的变更：
	  9 #   修改：     README
	 10 #
	 11 # 未跟踪的文件:
	 12 #   test
	 13 #


可以看到，默认的提交消息包含最后一次运行 git status 的输出，放在注释行里，另外开头还有一空行，供你输入提交说明。你完全可以去掉这些注释行，不过留着也没关系，多少能帮你回想起这次更新的内容有哪些。（如果觉得这还不够，可以用 -v 选项将修改差异的每一行都包含到注释中来。）退出编辑器时，Git 会丢掉注释行，将说明内容和本次更新提交到仓库。

另外也可以用 -m 参数后跟提交说明的方式，在一行命令中提交更新：
	
	$ git commit -m "readme file"
	[master b3b0441] readme file
	 1 file changed, 1 insertion(+)
	 create mode 100644 README


好，现在你已经创建了一个提交！可以看到，提交后它会告诉你，当前是在哪个分支（master）提交的，本次提交的完整 SHA-1 校验和是什么（b3b0441），以及在本次提交中，有多少文件修订过，多少行添改和删改过。

记住，提交时记录的是放在暂存区域的快照，任何还未暂存的仍然保持已修改状态，可以在下次提交时纳入版本管理。每一次运行提交操作，都是对你项目作一次快照，以后可以回到这个状态，或者进行比较。
跳过使用暂存区域

尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。Git 提供了一个跳过使用暂存区域的方式，只要在提交的时候，给git commit 加上 -a 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add 步骤（但是不推荐使用这种方法）

##删除文件
要从 Git 中删除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。可以用 git rm 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

如果只是简单地从工作目录中手工删除文件，运行 git status 时就会看到：

	$ git status
		位于分支 master
		尚未暂存以备提交的变更：
		  （使用 "git add/rm <file>..." 更新要提交的内容）
		  （使用 "git checkout -- <file>..." 丢弃工作区的改动）
		
		        删除：     README

		修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）

然后再运行 git rm 记录此次删除文件的操作：

	$ git rm README
	rm 'README'
	$ git status
	位于分支 master
	要提交的变更：
	  （使用 "git reset HEAD <file>..." 撤出暂存区）
	
	        删除：     README


然后 commit 一下

	$ git commit -m "delete readme"
		[master 2d37c35] delete readme
		 1 file changed, 1 deletion(-)
		 delete mode 100644 README



最后提交的时候，该文件就不再纳入版本管理了。如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f（译注：即 force 的首字母），以防误删除文件后丢失修改的内容。

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。换句话说，仅是从跟踪清单中删除。比如一些大型日志文件或者一堆 .a 编译文件，不小心纳入仓库后，要移除跟踪但不删除文件，以便稍后在 .gitignore 文件中补上，用 --cached 选项即可：

	$ git rm --cached readme.txt

后面可以列出文件或者目录的名字，也可以使用 glob 模式。比方说：

	$ git rm log/\*.log

注意到星号 * 之前的反斜杠 \，因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开（译注：实际上不加反斜杠也可以运行，只不过按照 shell 扩展的话，仅仅删除指定目录下的文件而不会递归匹配。上面的例子本来就指定了目录，所以效果等同，但下面的例子就会用递归方式匹配，所以必须加反斜杠。）。此命令删除所有 log/ 目录下扩展名为 .log 的文件。类似的比如：

	$ git rm \*~

会递归删除当前目录及其子目录中所有 ~ 结尾的文件。

##快速标记删除

使用命令 git add -u 快速标记删除, 在系统中批量删除文件后， 运行git add -u 快速在暂存区删除记录， 如：

	$ ls
	t1.txt  t2.txt  t3.txt

	$ rm *.txt
	$ git status
	位于分支 master
	尚未暂存以备提交的变更：
	  （使用 "git add/rm <file>..." 更新要提交的内容）
	  （使用 "git checkout -- <file>..." 丢弃工作区的改动）
	
	        删除：     t1.txt
	        删除：     t2.txt
	        删除：     t3.txt
	
	修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）

	$ git add -u
	$ git status
	位于分支 master
	要提交的变更：
	  （使用 "git reset HEAD <file>..." 撤出暂存区）
	
	        删除：     t1.txt
	        删除：     t2.txt
        删除：     t3.txt


##移动文件

不像其他的 VCS 系统，Git 并不跟踪文件移动操作。如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。不过 Git 非常聪明，它会推断出究竟发生了什么，至于具体是如何做到的

既然如此，当你看到 Git 的 mv 命令时一定会困惑不已。要在 Git 中对文件改名，可以这么做：

	$ git mv file_from file_to

它会恰如预期般正常工作。实际上，即便此时查看状态信息，也会明白无误地看到关于重命名操作的说明：

	$ git mv readem.txt readme.txt
	    $ git status
		位于分支 master
		要提交的变更：
		  （使用 "git reset HEAD <file>..." 撤出暂存区）
		
		        重命名：   readem.txt -> readme.txt


其实，运行 git mv 就相当于运行了下面三条命令：

	$ mv README.txt README
	    $ git rm readem.txt
	    $ git add readme.txt

如此分开操作，Git 也会意识到这是一次改名，所以不管何种方式都一样。当然，直接用 git mv 轻便得多，不过有时候用其他工具批处理改名的话，要记得在提交前删除老的文件名，再添加新的文件名。

#4. 查看提交历史

在提交了若干更新之后，又或者克隆了某个项目，想回顾下提交历史，可以使用 git log 命令查看。

然后在此项目中运行 git log，应该会看到下面的输出：

	$ git log
		commit 76173a972129d17fea4124ac2aecef4691d8522a
		Author: dyh <cbyniypeu@163.com>
		Date:   Tue Feb 3 14:23:02 2015 +0800
		
		    readme.txt add
		
		commit 2d37c35eab329baba9bd9daf207b2a17c12650f8
		Author: dyh <cbyniypeu@163.com>
		Date:   Tue Feb 3 14:12:05 2015 +0800
		
		    delete readme
		
		commit b3b0441ab7a50695abb838b2bea597d07e114e42
		Author: dyh <cbyniypeu@163.com>
		Date:   Tue Feb 3 14:04:07 2015 +0800
		
		    readme file
		
		commit 77beba03ed8f7847df1ce492944d50f9dbd0de5e
		Author: dyh <cbyniypeu@163.com>
		Date:   Tue Feb 3 13:49:14 2015 +0800
		
		    gitignore file


默认不用任何参数的话，git log 会按提交时间列出所有的更新，最近的更新排在最上面。看到了吗，每次更新都有一个 SHA-1 校验和、作者的名字和电子邮件地址、提交时间，最后缩进一个段落显示提交说明。

git log 有许多选项可以帮助你搜寻感兴趣的提交，接下来我们介绍些最常用的。

我们常用 -p 选项展开显示每次提交的内容差异，用 -2 则仅显示最近的两次更新：

	$ git log -p -2
	commit 76173a972129d17fea4124ac2aecef4691d8522a
	Author: dyh <cbyniypeu@163.com>
	Date:   Tue Feb 3 14:23:02 2015 +0800
	
	    readme.txt add
	
	diff --git a/readem.txt b/readem.txt
	new file mode 100755
	index 0000000..e69de29
	
	commit 2d37c35eab329baba9bd9daf207b2a17c12650f8
	Author: dyh <cbyniypeu@163.com>
	Date:   Tue Feb 3 14:12:05 2015 +0800
	
	    delete readme
	
	diff --git a/README b/README
	deleted file mode 100644
	index 8178c76..0000000
	--- a/README
	+++ /dev/null
	@@ -1 +0,0 @@
	-readme



在做代码审查，或者要快速浏览其他协作者提交的更新都作了哪些改动时，就可以用这个选项。此外，还有许多摘要选项可以用，比如 --stat，仅显示简要的增改行数统计：

	$ git log --stat
	commit 76173a972129d17fea4124ac2aecef4691d8522a
	Author: dyh <cbyniypeu@163.com>
	Date:   Tue Feb 3 14:23:02 2015 +0800
	
	    readme.txt add
	
	 readem.txt | 0
	 1 file changed, 0 insertions(+), 0 deletions(-)
	
	commit 2d37c35eab329baba9bd9daf207b2a17c12650f8
	Author: dyh <cbyniypeu@163.com>
	Date:   Tue Feb 3 14:12:05 2015 +0800
	
	    delete readme
	
	 README | 1 -
	 1 file changed, 1 deletion(-)
	...


每个提交都列出了修改过的文件，以及其中添加和移除的行数，并在最后列出所有增减行数小计。还有个常用的 --pretty 选项，可以指定使用完全不同于默认格式的方式展示提交历史。比如用 oneline 将每个提交放在一行显示，这在提交数很大时非常有用。另外还有 short，full 和 fuller可以用，展示的信息或多或少有些不同，请自己动手实践一下看看效果如何。
	
	$ git log --pretty=oneline
		76173a972129d17fea4124ac2aecef4691d8522a readme.txt add
		2d37c35eab329baba9bd9daf207b2a17c12650f8 delete readme
		b3b0441ab7a50695abb838b2bea597d07e114e42 readme file


但最有意思的是 format，可以定制要显示的记录格式，这样的输出便于后期编程提取分析，像这样：

	$ git log --pretty=format:"%h - %an, %ar : %s"
		76173a9 - dyh, 8 分钟前 : readme.txt add
		2d37c35 - dyh, 19 分钟前 : delete readme
		b3b0441 - dyh, 27 分钟前 : readme file
		77beba0 - dyh, 42 分钟前 : gitignore file
		72d8d78 - dyh, 3 小时前 : readme 150203
		e2db966 - dyh, 3 小时前 : myarticle 20150203
		e4e5cc4 - dyh, 3 小时前 : first commit


表 2-1 列出了常用的格式占位符写法及其代表的意义。

选项 说明

    %H 提交对象（commit）的完整哈希字串
    %h 提交对象的简短哈希字串
    %T 树对象（tree）的完整哈希字串
    %t 树对象的简短哈希字串
    %P 父对象（parent）的完整哈希字串
    %p 父对象的简短哈希字串
    %an 作者（author）的名字
    %ae 作者的电子邮件地址
    %ad 作者修订日期（可以用 -date= 选项定制格式）
    %ar 作者修订日期，按多久以前的方式显示
    %cn 提交者(committer)的名字
    %ce 提交者的电子邮件地址
    %cd 提交日期
    %cr 提交日期，按多久以前的方式显示
    %s 提交说明

你一定奇怪作者（author）和提交者（committer）之间究竟有何差别，其实作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。所以，当你为某个项目发布补丁，然后某个核心成员将你的补丁并入项目时，你就是作者，而那个核心成员就是提交者。

用 oneline 或 format 时结合 --graph 选项，可以看到开头多出一些 ASCII 字符串表示的简单图形，形象地展示了每个提交所在的分支及其分化衍合情况。


以上只是简单介绍了一些 git log 命令支持的选项。以下还列出其它常用的选项及其释义。

选项 说明

	    -p 按补丁格式显示每个更新之间的差异。
	    --stat 显示每次更新的文件修改统计信息。
	    --shortstat 只显示 --stat 中最后的行数修改添加移除统计。
	    --name-only 仅在提交信息后显示已修改的文件清单。
	    --name-status 显示新增、修改、删除的文件清单。
	    --abbrev-commit 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。
	    --relative-date 使用较短的相对时间显示（比如，“2 weeks ago”）。
	    --graph 显示 ASCII 图形表示的分支合并历史。
	    --pretty 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。

##限制输出长度

除了定制输出格式的选项之外，git log 还有许多非常实用的限制输出长度的选项，也就是只输出部分提交信息。之前我们已经看到过 -2 了，它只显示最近的两条提交，实际上，这是 -<n> 选项的写法，其中的 n 可以是任何自然数，表示仅显示最近的若干条提交。不过实践中我们是不太用这个选项的，Git 在输出所有提交时会自动调用分页程序（less），要看更早的更新只需翻到下页即可。

另外还有按照时间作限制的选项，比如 --since 和 --until。下面的命令列出所有最近两周内的提交：

	$ git log --since=2.weeks

你可以给出各种时间格式，比如说具体的某一天（“2015-01-15”），或者是多久以前（“2 years 1 day 3 minutes ago”）。

还可以给出若干搜索条件，列出符合的提交。用 --author 选项显示指定作者的提交，用 --grep 选项搜索提交说明中的关键字。（请注意，如果要得到同时满足这两个选项搜索条件的提交，就必须用 --all-match 选项。否则，满足任意一个条件的提交都会被匹配出来）

另一个真正实用的git log选项是路径(path)，如果只关心某些文件或者目录的历史提交，可以在 git log 选项的最后指定它们的路径。因为是放在最后位置上的选项，所以用两个短划线（--）隔开之前的选项和后面限定的路径名。

表 2-3 还列出了其他常用的类似选项。

选项 说明

	    -(n) 仅显示最近的 n 条提交
	    --since, --after 仅显示指定时间之后的提交。
	    --until, --before 仅显示指定时间之前的提交。
	    --author 仅显示指定作者相关的提交。
	    --committer 仅显示指定提交者相关的提交。


#5. 取消操作

任何时候，你都有可能需要撤消刚才所做的某些操作。接下来，我们会介绍一些基本的撤消操作相关的命令。请注意，有些撤销操作是不可逆的，所以请务必谨慎小心，一旦失误，就有可能丢失部分工作成果。
修改最后一次提交

有时候我们提交完了才发现漏掉了几个文件没有加，或者提交信息写错了。想要撤消刚才的提交操作，可以使用 --amend 选项重新提交：

	$ git commit --amend

此命令将使用当前的暂存区域快照提交。如果刚才提交完没有作任何改动，直接运行此命令的话，相当于有机会重新编辑提交说明，但将要提交的文件快照和之前的一样。

启动文本编辑器后，会看到上次提交时的说明，编辑它确认没问题后保存退出，就会使用新的提交说明覆盖刚才失误的提交。

如果刚才提交时忘了暂存某些修改，可以先补上暂存操作，然后再运行 --amend 提交：

	$ git commit -m 'initial commit'
	    $ git add forgotten_file
	    $ git commit --amend

上面的三条命令最终只是产生一个提交，第二个提交命令修正了第一个的提交内容。
取消已经暂存的文件


#6. 远程仓库的使用

要参与任何一个 Git 项目的协作，必须要了解该如何管理远程仓库。远程仓库是指托管在网络上的项目仓库，可能会有好多个，其中有些你只能读，另外有些可以写。同他人协作开发某个项目时，需要管理这些远程仓库，以便推送或拉取数据，分享各自的工作进展。管理远程仓库的工作，包括添加远程库，移除废弃的远程库，管理各式远程库分支，定义是否跟踪这些分支，等等。

##查看当前的远程库

(注：这里重新建一个版本仓库)

要查看当前配置有哪些远程仓库，可以用 git remote 命令，它会列出每个远程库的简短名字。在克隆完某个项目后，至少可以看到一个名为 origin 的远程库，Git 默认使用这个名字来标识你所克隆的原始仓库：

	$ git clone git@github.com:dengyhgit/my-article.git
	正克隆到 'my-article'...
	Warning: Permanently added the RSA host key for IP address '192.30.252.128' to the list of known hosts.
	remote: Counting objects: 25, done.
	remote: Compressing objects: 100% (21/21), done.
	remote: Total 25 (delta 2), reused 25 (delta 2)
	接收对象中: 100% (25/25), 1.28 MiB | 131.00 KiB/s, 完成.
	处理 delta 中: 100% (2/2), 完成.
	检查连接... 完成。


也可以加上 -v 选项（译注：此为 --verbose 的简写，取首字母），显示对应的克隆地址：

	$ git remote -v
	origin  git@github.com:dengyhgit/my-article.git (fetch)
	origin  git@github.com:dengyhgit/my-article.git (push)

这样一来，我就可以非常轻松地从这些用户的仓库中，拉取他们的提交到本地。请注意，上面列出的地址只有 origin 用的是 SSH URL 链接，所以也只有这个仓库我能推送数据上去

##添加远程仓库

要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，运行 git remote add [shortname] [url]：

	$ git remote add article git@github.com:dengyhgit/my-article.git
	$ git remote -v
	article git@github.com:dengyhgit/my-article.git (fetch)
	article git@github.com:dengyhgit/my-article.git (push)


正如之前所看到的，可以用下面的命令从远程仓库抓取数据到本地：

$ git fetch [remote-name]


	$ git fetch origin master
	remote: Counting objects: 24, done.
	remote: Compressing objects: 100% (11/11), done.
	remote: Total 24 (delta 9), reused 23 (delta 8)
	展开对象中: 100% (24/24), 完成.
	来自 github.com:dengyhgit/my-article
	 * branch            master     -> FETCH_HEAD
	   72d8d78..4c38bbb  master     -> origin/master


git fetch：相当于是从远程获取最新版本到本地，不会自动merge, 要手动合并

	$ git status
	位于分支 master
	您的分支和 'origin/master' 出现了偏离，
	并且分别有 1 和 9 处不同的提交。
	  （使用 "git pull" 来合并远程分支）
	无文件要提交，干净的工作区




使用 git pull 命令自动抓取数据下来，然后将远端分支自动合并到本地仓库中当前分支。在日常工作中我们经常这么用，既快且好。实际上，默认情况下 git clone 命令本质上就是自动创建了本地的 master 分支用于跟踪远程仓库中的 master 分支（假设远程仓库确实有 master 分支）。所以一般我们运行 git pull，目的都是要从原始克隆的远端仓库中抓取数据后，合并到工作目录中的当前分支。
推送数据到远程仓库

项目进行到一个阶段，要同别人分享目前的成果，可以将本地仓库中的数据推送到远程仓库。实现这个任务的命令很简单：git push [remote-name] [branch-name]。如果要把本地的 master 分支推送到 origin 服务器上（再次说明下，克隆操作会自动使用默认的 master 和 origin 名字），可以运行下面的命令：

	$ git push origin master

只有在所克隆的服务器上有写权限，或者同一时刻没有其他人在推数据，这条命令才会如期完成任务。如果在你推数据前，已经有其他人推送了若干更新，那你的推送操作就会被驳回。你必须先把他们的更新抓取到本地，合并到自己的项目中，然后才可以再次推送。

	$ git push origin master
	To git@github.com:dengyhgit/my-article.git
	 ! [rejected]        master -> master (fetch first)
	error: 无法推送一些引用到 'git@github.com:dengyhgit/my-article.git'
	提示：更新被拒绝，因为远程版本库包含您本地尚不存在的提交。这通常是因为另外
	提示：一个版本库已向该引用进行了推送。再次推送前，您可能需要先整合远程变更
	提示：（如 'git pull ...'）。
	提示：详见 'git push --help' 中的 'Note about fast-forwards' 小节。

我们可以通过命令 git remote show [remote-name] 查看某个远程仓库的详细信息，比如要看所克隆的 origin 仓库，可以运行：

	$ git remote show origin
	* 远程 origin
	  获取地址：git@github.com:dengyhgit/my-article.git
	  推送地址：git@github.com:dengyhgit/my-article.git
	  HEAD分支：master
	  远程分支：
	    master 已跟踪
	  为 'git push' 配置的本地引用：
	    master 推送至 master (最新)


除了对应的克隆地址外，它还给出了许多额外的信息。它友善地告诉你如果是在 master 分支，就可以用 git pull 命令抓取数据合并到本地。另外还列出了所有处于跟踪状态中的远端分支。

##远程仓库的删除和重命名

在新版 Git 中可以用 git remote rename 命令修改某个远程仓库在本地的简称，比如想把 origin 改成 github，可以这么运行：

	$ git remote rename origin github

碰到远端仓库服务器迁移，或者原来的克隆镜像不再使用，又或者某个参与者不再贡献代码，那么需要移除对应的远端仓库，可以运行 git remote rm命令：

	$ git remote rm origin

#7. 打标签

同大多数 VCS 一样，Git 也可以对某一时间点上的版本打上标签。人们在发布某个软件版本（比如 v1.0 等等）的时候，经常这么做。本节我们一起来学习如何列出所有可用的标签，如何新建标签，以及各种不同类型标签之间的差别。

##列显已有的标签

列出现有标签的命令非常简单，直接运行 git tag 即可：

	$ git tag
	    v0.1
	    v1.3

显示的标签按字母顺序排列，所以标签的先后并不表示重要程度的轻重。

我们可以用特定的搜索模式列出符合条件的标签。如果你只对 1.4.2 系列的版本感兴趣，可以运行下面的命令：

$ git tag -l 'v1.4.2.*'
    v1.4.2.1
    v1.4.2.2
    v1.4.2.3
    v1.4.2.4

##新建标签

Git 使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明。一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。

### 含附注的标签

创建一个含附注类型的标签非常简单，用 -a （译注：取 annotated 的首字母）指定标签名字即可：

	$ git tag -a v1.0 -m "version 1.0"
	$ git tag
	v1.0

而 -m 选项则指定了对应的标签说明，Git 会将此说明一同保存在标签对象中。如果没有给出该选项，Git 会启动文本编辑软件供你输入标签说明。

可以使用 git show 命令查看相应标签的版本信息，并连同显示打标签时的提交对象。

	$ git show v1.0
	tag v1.0
	Tagger: dyh <cbyniypeu@163.com>
	Date:   Tue Feb 3 21:10:48 2015 +0800
	
	version 1.0
	
	commit 3490a9e05be91c3eeca2de0ba14cfe0a7e1f9f39
	Merge: 3d40424 97c2783
	Author: dyh <cbyniypeu@163.com>
	Date:   Tue Feb 3 21:04:18 2015 +0800
	
	    Merge branch 'master' of github.com:dengyhgit/my-article


我们可以看到在提交对象信息上面，列出了此标签的提交者和提交时间，以及相应的标签说明。


###轻量级标签

轻量级标签实际上就是一个保存着对应提交对象的校验和信息的文件。要创建这样的标签，一个 -a，-s 或 -m 选项都不用，直接给出标签名字即可：

	$ git tag v1.1


现在运行 git show 查看此标签信息，就只有相应的提交对象摘要：
	
	$ git show v1.1
	commit b7c0dd4fd36f32f99b5781c93e6505c15023430a
	Author: dyh <cbyniypeu@163.com>
	Date:   Tue Feb 3 21:15:09 2015 +0800


后期加注标签

你甚至可以在后期对早先的某次提交加注标签。比如在下面展示的提交历史中：

	$ git log --pretty=oneline
	b7c0dd4fd36f32f99b5781c93e6505c15023430a test tag -s
	3490a9e05be91c3eeca2de0ba14cfe0a7e1f9f39 Merge branch 'master' of github.com:dengyhgit/my-article
	97c2783f4c88031d8617aa57403701cdfada31d4 rm test
	3d4042400bf6ce5296d33a9d5259ca07b1efd509 Merge branch 'master' of github.com:dengyhgit/my-article
	4c38bbb81ac926e5ced40ba390ffa9d70efb6829 test push
	01e76b4289ae2378ab10489f6bed766406626636 test fetch
	...


我们忘了在提交 “rm test” 后为此项目打上版本号 v0.1，没关系，现在也能做。只要在打标签的时候跟上对应提交对象的校验和（或前几位字符）即可：

	$ git tag -a v0.1 97c2783f4c88031d8617aa57403701cdfada31d4 -m "add tag"


可以看到我们已经补上了标签：
	
	$ git tag
	v0.1
	v1.0
	v1.1


###分享标签

默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。其命令格式如同推送分支，运行git push origin [tagname] 即可：

	$ git push origin v1.1
	对象计数中: 9, 完成.
	Delta compression using up to 4 threads.
	压缩对象中: 100% (8/8), 完成.
	写入对象中: 100% (9/9), 970 bytes | 0 bytes/s, 完成.
	Total 9 (delta 4), reused 0 (delta 0)
	To git@github.com:dengyhgit/my-article.git
	 * [new tag]         v1.1 -> v1.1


如果要一次推送所有本地新增的标签上去，可以使用 --tags 选项：

	$ git push origin --tags
	对象计数中: 2, 完成.
	Delta compression using up to 4 threads.
	压缩对象中: 100% (2/2), 完成.
	写入对象中: 100% (2/2), 272 bytes | 0 bytes/s, 完成.
	Total 2 (delta 0), reused 0 (delta 0)
	To git@github.com:dengyhgit/my-article.git
	 * [new tag]         v0.1 -> v0.1
	 * [new tag]         v1.0 -> v1.0

现在，其他人克隆共享仓库或拉取数据同步后，也会看到这些标签

#8. 分支的新建与合并

现在让我们来看一个简单的分支与合并的例子，实际工作中大体也会用到这样的工作流程：

	    开发某个网站。
	    为实现某个新的需求，创建一个分支。
	    在这个分支上开展工作。
	
	假设此时，你突然接到一个电话说有个很严重的问题需要紧急修补，那么可以按照下面的方式处理：
	
	    返回到原先已经发布到生产服务器上的分支。
	    为这次紧急修补建立一个新分支，并在其中修复问题。
	    通过测试后，回到生产服务器所在的分支，将修补分支合并进来，然后再推送到生产服务器上。
	    切换到之前实现新需求的分支，继续工作。

##分支的新建与切换

首先，我们假设你正在项目中愉快地工作，并且已经提交了几次更新，现在，你决定要修补问题追踪系统上的 #01 问题。这里为了说明要解决的问题，才把新建的分支取名为 pro01。要新建并切换到该分支，运行 git checkout 并加上 -b 参数：

	$ git checkout -b pro01
	切换到一个新分支 'pro01'

这相当于执行下面这两条命令：

	$ git branch pro01
	    $ git checkout pro01


接着你开始尝试修复问题，在提交了若干次更新后，pro01 分支的指针也会随着向前推进，因为它就是当前分支（换句话说，当前的 HEAD 指针正指向 pro01）, pro01 分支随工作进展向前推进

现在你就接到了那个网站问题的紧急电话，需要马上修补。有了 Git ，我们就不需要同时发布这个补丁和 pro01 里作出的修改，也不需要在创建和发布该补丁到服务器之前花费大力气来复原这些修改。唯一需要的仅仅是切换回 master 分支。

不过在此之前，留心你的暂存区或者工作目录里，那些还没有提交的修改，它会和你即将检出的分支产生冲突从而阻止 Git 为你切换分支。切换分支的时候最好保持一个清洁的工作区域。稍后会介绍几个绕过这种问题的办法（分别叫做 stashing 和 commit amending）。目前已经提交了所有的修改，所以接下来可以正常转换到 master 分支：

$ git checkout master
切换到分支 'master'


此时工作目录中的内容和你在解决问题 #01 之前一模一样，你可以集中精力进行紧急修补。这一点值得牢记：Git 会把工作目录的内容恢复为检出某分支时它所指向的那个提交对象的快照。它会自动添加、删除和修改文件以确保目录的内容和你当时提交时完全一样。

接下来，你得进行紧急修补。我们创建一个紧急修补分支 hotfix 来开展工作，直到搞定

	$ git checkout -b 'hotfix'

hotfix 分支是从 master 分支所在点分化出来的

 

有必要作些测试，确保修补是成功的，然后回到 master 分支并把它合并进来，然后发布到生产服务器。用 git merge 命令来进行合并：

	$ git checkout master
    $ git merge hotfix


请注意，合并时出现了“Fast forward”的提示。由于当前 master 分支所在的提交对象是要并入的 hotfix 分支的直接上游，Git 只需把 master 分支指针直接右移。换句话说，如果顺着一个分支走下去可以到达另一个分支的话，那么 Git 在合并两者时，只会简单地把指针右移，因为这种单线的历史分支不存在任何需要解决的分歧，所以这种合并过程可以称为快进（Fast forward）。

现在最新的修改已经在当前 master 分支所指向的提交对象中了，可以部署到生产服务器上去了,合并之后，master 分支和 hotfix 分支指向同一位置。

 

在那个超级重要的修补发布以后，你想要回到被打扰之前的工作。由于当前 hotfix 分支和 master 都指向相同的提交对象，所以 hotfix 已经完成了历史使命，可以删掉了。使用 git branch 的 -d 选项执行删除操作：

	$ git branch -d hotfix

现在回到之前未完成的 #01 问题修复分支上继续工作

	$ git checkout pro01

pro01 分支可以不受影响继续推进。

 

不用担心之前 hotfix 分支的修改内容尚未包含到 pro01 中来。如果确实需要纳入此次修补，可以用 git merge master 把 master pro01；或者等 pro01 完成之后，再将 pro01 分支中的更新并入 master。

##分支的合并

在问题 #01 相关的工作完成之后，可以合并回 master 分支。实际操作同前面合并 hotfix 分支差不多，只需回到 master 分支，运行git merge 命令指定要合并进来的分支：

	$ git checkout master

请注意，这次合并操作的底层实现，并不同于之前 hotfix 的并入方式。因为这次你的开发历史是从更早的地方开始分叉的。由于当前 master 分支所指向的提交对象（C4）并不是 pro01 分支的直接祖先，Git 不得不进行一些额外处理。就此例而言，Git 会用两个分支的末端以及它们的共同祖先进行一次简单的三方合并计算。Git 为分支合并自动识别出最佳的同源合并点。
这次，Git 没有简单地把分支指针右移，而是对三方合并后的结果重新做一个新的快照，并自动创建一个指向它的提交对象。这个提交对象比较特殊，它有两个祖先。值得一提的是 Git 可以自己裁决哪个共同祖先才是最佳合并基础；这和 CVS 或 Subversion（1.5 以后的版本）不同，它们需要开发者手工指定合并基础。所以此特性让 Git 的合并操作比其他系统都要简单不少。

 
既然之前的工作成果已经合并到 master 了，那么 pro01 也就没用了。你可以就此删除它，并在问题追踪系统里关闭该问题。

$ git branch -d pro01

##遇到冲突时的分支合并

有时候合并操作并不会如此顺利。如果在不同的分支中都修改了同一个文件的同一部分，Git 就无法干净地把两者合到一起（译注：逻辑上说，这种问题只能由人来裁决。）。如果你在解决问题 #01 的过程中修改了 hotfix 中修改的部分，将得到类似下面的结果：

	$ git merge pro01

Git 作了合并，但没有提交，它会停下来等你解决冲突。要看看哪些文件在合并时发生冲突，可以用 git status 查阅：

	$ git status
	位于分支 master
	您有尚未合并的路径。
	  （解决冲突并运行 "git commit"）
	
	未合并的路径：
	  （使用 "git add <file>..." 标记解决方案）
	
	        双方修改：   test.txt
	
	修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）


任何包含未解决冲突的文件都会以未合并（unmerged）的状态列出。Git 会在有冲突的文件里加入标准的冲突解决标记，可以通过它们来手工定位并解决这些冲突。可以看到此文件包含类似下面这样的部分：

<<<<<<< HEAD
    test
    =======
    test2
    >>>>>>> pro01

可以看到 ======= 隔开的上半部分，是 HEAD（即 master 分支，在运行 merge 命令时所切换到的分支）中的内容，下半部分是在 pro01 分支中的内容。解决冲突的办法无非是二者选其一或者由你亲自整合到一起。比如你可以通过把这段内容替换为下面这样来解决：

	test
	test2

这个解决方案各采纳了两个分支中的一部分内容，而且我还删除了 <<<<<<<，======= 和 >>>>>>> 这些行。在解决了所有文件里的所有冲突后，运行 git add 将把它们标记为已解决状态（译注：实际上就是来一次快照保存到暂存区域。）。因为一旦暂存，就表示冲突已经解决。

#9. 分支管理

到目前为止，你已经学会了如何创建、合并和删除分支。除此之外，我们还需要学习如何管理分支，在日后的常规工作中会经常用到下面介绍的管理命令。

git branch 命令不仅仅能创建和删除分支，如果不加任何参数，它会给出当前所有分支的清单：

$ git branch
    iss53
    * master
    testing

注意看 master 分支前的 * 字符：它表示当前所在的分支。也就是说，如果现在提交更新，master 分支将随着开发进度前移。若要查看各个分支最后一个提交对象的信息，运行 git branch -v：
	
	$ git branch -v
	* master b7c0dd4 test tag -s
	  pro01  b7c0dd4 test tag -s


要从该清单中筛选出你已经（或尚未）与当前分支合并的分支，可以用 --merge 和 --no-merged 选项（Git 1.5.6 以上版本）。比如用git branch --merge 查看哪些分支已被并入当前分支（译注：也就是说哪些分支是当前分支的直接上游。）：

	$ git branch --merge
	* master
	  pro01


之前我们已经合并了 pro01，所以在这里会看到它。一般来说，列表中没有 * 的分支通常都可以用 git branch -d 来删掉。原因很简单，既然已经把它们所包含的工作整合到了其他分支，删掉也不会损失什么。

另外可以用 git branch --no-merged 查看尚未合并的工作：
	
	$ git branch --no-merged

它会显示还未合并进来的分支。由于这些分支中还包含着尚未合并进来的工作成果，所以简单地用 git branch -d 删除该分支会提示错误，因为那样做会丢失数据：

	$ git branch -d testing

不过，如果你确实想要删除该分支上的改动，可以用大写的删除选项 -D 强制执行，就像上面提示信息中给出的那样。

#10. 旧版本检出

由于某种原因， 项目不得回到之前的旧版本，放心， git 提供回滚到之前任何一次commit中， 使用git reset --hard commitID, 如：

	$ git reset --hard 7496234588c36aed1476e2ef68c06ea7c
	HEAD 现在位于 7496234 update gitstudy.md

注意： 使用 --hard 参数的时候， 将工作区和暂存区将会被清空，慎用。
但是 git 很友好地提供了 --soft 参数， 使用 --soft 将保留当前的工作区和暂存区。

	git reset --soft 7496234588c36aed1476e2ef68c06ea7c




#11. 协议

Git 可以使用四种主要的协议来传输数据：本地传输，SSH 协议，Git 协议和 HTTP 协议。下面分别介绍一下哪些情形应该使用（或避免使用）这些协议。

值得注意的是，除了 HTTP 协议外，其他所有协议都要求在服务器端安装并运行 Git。

##本地协议

最基本的就是本地协议（Local protocol），所谓的远程仓库在该协议中的表示，就是硬盘上的另一个目录。这常见于团队每一个成员都对一个共享的文件系统（例如 NFS）拥有访问权，或者比较少见的多人共用同一台电脑的情况。后面一种情况并不安全，因为所有代码仓库实例都储存在同一台电脑里，增加了灾难性数据损失的可能性。

如果你使用一个共享的文件系统，就可以在一个本地文件系统中克隆仓库，推送和获取。克隆的时候只需要将远程仓库的路径作为 URL 使用，比如下面这样：

	$ git clone /opt/git/project.git

或者这样：

	$ git clone file:///opt/git/project.git

如果在 URL 开头明确使用 file:// ，那么 Git 会以一种略微不同的方式运行。如果你只给出路径，Git 会尝试使用硬链接或直接复制它所需要的文件。如果使用了 file:// ，Git 会调用它平时通过网络来传输数据的工序，而这种方式的效率相对较低。使用 file:// 前缀的主要原因是当你需要一个不包含无关引用或对象的干净仓库副本的时候 — 一般指从其他版本控制系统导入的。我们这里仅仅使用普通路径，这样更快。

要添加一个本地仓库作为现有 Git 项目的远程仓库，可以这样做：

	$ git remote add local_proj /opt/git/project.git

然后就可以像在网络上一样向这个远程仓库推送和获取数据了。

###优点

基于文件仓库的优点在于它的简单，同时保留了现存文件的权限和网络访问权限。如果你的团队已经有一个全体共享的文件系统，建立仓库就十分容易了。你只需把一份裸仓库的副本放在大家都能访问的地方，然后像对其他共享目录一样设置读写权限就可以了。

这也是从别人工作目录中获取工作成果的快捷方法。假如你和你的同事在一个项目中合作，他们想让你检出一些东西的时候，运行类似git pull /home/john/project 通常会比他们推送到服务器，而你再从服务器获取简单得多。

###缺点

这种方法的缺点是，与基本的网络连接访问相比，难以控制从不同位置来的访问权限。如果你想从家里的笔记本电脑上推送，就要先挂载远程硬盘，这和基于网络连接的访问相比更加困难和缓慢。

另一个很重要的问题是该方法不一定就是最快的，尤其是对于共享挂载的文件系统。本地仓库只有在你对数据访问速度快的时候才快。在同一个服务器上，如果二者同时允许 Git 访问本地硬盘，通过 NFS 访问仓库通常会比 SSH 慢。

##SSH 协议

Git 使用的传输协议中最常见的可能就是 SSH 了。这是因为大多数环境已经支持通过 SSH 对服务器的访问 — 即便还没有，架设起来也很容易。SSH 也是唯一一个同时支持读写操作的网络协议。另外两个网络协议（HTTP 和 Git）通常都是只读的，所以虽然二者对大多数人都可用，但执行写操作时还是需要 SSH。SSH 同时也是一个验证授权的网络协议；而因为其普遍性，一般架设和使用都很容易。

通过 SSH 克隆一个 Git 仓库，你可以像下面这样给出 ssh:// 的 URL：

	$ git clone ssh://user@server/project.git

或者不指明某个协议 — 这时 Git 会默认使用 SSH ：

	$ git clone user@server:project.git

如果不指明用户，Git 会默认使用当前登录的用户名连接服务器。

###优点

使用 SSH 的好处有很多。首先，如果你想拥有对网络仓库的写权限，基本上不可能不使用 SSH。其次，SSH 架设相对比较简单 — SSH 守护进程很常见，很多网络管理员都有一些使用经验，而且很多操作系统都自带了它或者相关的管理工具。再次，通过 SSH 进行访问是安全的 — 所有数据传输都是加密和授权的。最后，和 Git 及本地协议一样，SSH 也很高效，会在传输之前尽可能压缩数据。

###缺点

SSH 的限制在于你不能通过它实现仓库的匿名访问。即使仅为读取数据，人们也必须在能通过 SSH 访问主机的前提下才能访问仓库，这使得 SSH 不利于开源的项目。如果你仅仅在公司网络里使用，SSH 可能是你唯一需要使用的协议。如果想允许对项目的匿名只读访问，那么除了为自己推送而架设 SSH 协议之外，还需要支持其他协议以便他人访问读取。

##Git 协议

接下来是 Git 协议。这是一个包含在 Git 软件包中的特殊守护进程； 它会监听一个提供类似于 SSH 服务的特定端口（9418），而无需任何授权。打算支持 Git 协议的仓库，需要先创建 git-export-daemon-ok 文件 — 它是协议进程提供仓库服务的必要条件 — 但除此之外该服务没有什么安全措施。要么所有人都能克隆 Git 仓库，要么谁也不能。这也意味着该协议通常不能用来进行推送。你可以允许推送操作；然而由于没有授权机制，一旦允许该操作，网络上任何一个知道项目 URL 的人将都有推送权限。不用说，这是十分罕见的情况。

###优点

Git 协议是现存最快的传输协议。如果你在提供一个有很大访问量的公共项目，或者一个不需要对读操作进行授权的庞大项目，架设一个 Git 守护进程来供应仓库是个不错的选择。它使用与 SSH 协议相同的数据传输机制，但省去了加密和授权的开销。

###缺点

Git 协议消极的一面是缺少授权机制。用 Git 协议作为访问项目的唯一方法通常是不可取的。一般的做法是，同时提供 SSH 接口，让几个开发者拥有推送（写）权限，其他人通过 git:// 拥有只读权限。Git 协议可能也是最难架设的协议。它要求有单独的守护进程，需要定制 — 我们将在本章的 “Gitosis” 一节详细介绍它的架设 — 需要设定 xinetd 或类似的程序，而这些工作就没那么轻松了。该协议还要求防火墙开放 9418 端口，而企业级防火墙一般不允许对这个非标准端口的访问。大型企业级防火墙通常会封锁这个少见的端口。

##HTTP/S 协议

最后还有 HTTP 协议。HTTP 或 HTTPS 协议的优美之处在于架设的简便性。基本上，只需要把 Git 的裸仓库文件放在 HTTP 的根目录下，配置一个特定的 post-update 挂钩（hook）就可以搞定。此后，每个能访问 Git 仓库所在服务器上 web 服务的人都可以进行克隆操作。下面的操作可以允许通过 HTTP 对仓库进行读取：
	
	$ cd /var/www/htdocs/
	    $ git clone --bare /path/to/git_project gitproject.git
	    $ cd gitproject.git
	    $ mv hooks/post-update.sample hooks/post-update
	    $ chmod a+x hooks/post-update

这样就可以了。Git 附带的 post-update 挂钩会默认运行合适的命令（git update-server-info）来确保通过 HTTP 的获取和克隆正常工作。这条命令在你用 SSH 向仓库推送内容时运行；之后，其他人就可以用下面的命令来克隆仓库：

	$ git clone http://example.com/gitproject.git

在本例中，我们使用了 Apache 设定中常用的 /var/www/htdocs 路径，不过你可以使用任何静态 web 服务 — 把裸仓库放在它的目录里就行。 Git 的数据是以最基本的静态文件的形式提供的。

通过 HTTP 进行推送操作也是可能的，不过这种做法不太常见，并且牵扯到复杂的 WebDAV 设定。由于很少用到，本书将略过对该内容的讨论。如果对 HTTP 推送协议感兴趣，不妨打开这个地址看一下操作方法：http://www.kernel.org/pub/software/scm/git/docs/howto/setup-git-server-over-http.txt 。通过 HTTP 推送的好处之一是你可以使用任何 WebDAV 服务器，不需要为 Git 设定特殊环境；所以如果主机提供商支持通过 WebDAV 更新网站内容，你也可以使用这项功能。
优点

使用 HTTP 协议的好处是易于架设。几条必要的命令就可以让全世界读取到仓库的内容。花费不过几分钟。HTTP 协议不会占用过多服务器资源。因为它一般只用到静态的 HTTP 服务提供所有数据，普通的 Apache 服务器平均每秒能支撑数千个文件的并发访问 — 哪怕让一个小型服务器超载都很难。

你也可以通过 HTTPS 提供只读的仓库，这意味着你可以加密传输内容；你甚至可以要求客户端使用特定签名的 SSL 证书。一般情况下，如果到了这一步，使用 SSH 公共密钥可能是更简单的方案；不过也存在一些特殊情况，这时通过 HTTPS 使用带签名的 SSL 证书或者其他基于 HTTP 的只读连接授权方式是更好的解决方案。

HTTP 还有个额外的好处：HTTP 是一个如此常见的协议，以至于企业级防火墙通常都允许其端口的通信。

###缺点

HTTP 协议的消极面在于，相对来说客户端效率更低。克隆或者下载仓库内容可能会花费更多时间，而且 HTTP 传输的体积和网络开销比其他任何一个协议都大。因为它没有按需供应的能力 — 传输过程中没有服务端的动态计算 — 因而 HTTP 协议经常会被称为傻瓜（dumb）协议。

#12. 在服务器上部署 Git

开始架设 Git 服务器前，需要先把现有仓库导出为裸仓库 — 即一个不包含当前工作目录的仓库。做法直截了当，克隆时用 --bare 选项即可。裸仓库的目录名一般以 .git 结尾，像这样：

		$ git clone --bare  /cygdrive/e/mygit/test /cygdrive/e/mygit/test2/test.git
	克隆到裸版本库 '/cygdrive/e/mygit/test2/test.git'...
	完成。

该命令的输出或许会让人有些不解。其实 clone 操作基本上相当于 git init 加 git fetch，所以这里出现的其实是 git init 的输出，先由它建立一个空目录，而之后传输数据对象的操作并无任何输出，只是悄悄在幕后执行。现在 /cygdrive/e/mygit/test2 目录中已经有了一份 Git 目录数据的副本。

整体上的效果大致相当于：

	$ cp -Rf /cygdrive/e/mygit/test/.git /cygdrive/e/mygit/test2/test.git

但在配置文件中有若干小改动，不过对用户来讲，使用方式都一样，不会有什么影响。它仅取出 Git 仓库的必要原始数据，存放在该目录中，而不会另外创建工作区。

##把裸仓库移到服务器上

有了裸仓库的副本后，剩下的就是把它放到服务器上并设定相关协议。假设一个域名为 git.example.com 的服务器已经架设好，并可以通过 SSH 访问，我们打算把所有 Git 仓库储存在 /opt/git 目录下。只要把裸仓库复制过去：

	$ scp -r my_project.git user@git.example.com:/opt/git

现在，所有对该服务器有 SSH 访问权限，并可读取 /opt/git 目录的用户都可以用下面的命令克隆该项目：
	
	$ git clone user@git.example.com:/opt/git/my_project.git

如果某个 SSH 用户对 /opt/git/my_project.git 目录有写权限，那他就有推送权限。如果到该项目目录中运行 git init 命令，并加上 --shared选项，那么 Git 会自动修改该仓库目录的组权限为可写（译注：实际上 --shared 可以指定其他行为，只是默认为将组权限改为可写并执行 g+sx，所以最后会得到 rws。）。

	$ ssh user@git.example.com
	    $ cd /opt/git/my_project.git
	    $ git init --bare --shared

由此可见，根据现有的 Git 仓库创建一个裸仓库，然后把它放上你和同事都有 SSH 访问权的服务器是多么容易。现在已经可以开始在同一项目上密切合作了。

值得注意的是，这的的确确是架设一个少数人具有连接权的 Git 服务的全部 — 只要在服务器上加入可以用 SSH 登录的帐号，然后把裸仓库放在大家都有读写权限的地方。一切都准备停当，无需更多。




