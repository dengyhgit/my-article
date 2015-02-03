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

也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可，像这样：

	$ git config user.name
    	dyh

#2.	取得项目的 Git 仓库

有两种取得 Git 项目仓库的方法。

第一种是在现存的目录下init新的 Git 仓库。

第二种是从已有的 Git 仓库克隆出一个仓库，然后在自己工作目录中初始化新仓库

要对现有的某个项目init新的 Git 仓库：

	$ git init

初始化后，在当前目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。不过目前，仅仅是按照既有的结构框架初始化好了里边所有的文件和目录，但我们还没有开始跟踪管理项目中的任何一个文件。
如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

	$ git add *.c
    $ git add README
    $ git commit -m 'initial project'


从现有仓库克隆

如果想对某个开源项目出一份力，可以先把该项目的 Git 仓库复制一份出来，可以用下面的命令：

	$ git clone git://github.com/schacon/grit.git

这会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录，然后从中取出最新版本的文件拷贝。如果进入这个新建的 grit 目录，你会看到项目中的所有文件已经在里边了，准备好后续的开发和使用。如果希望在克隆的时候，自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：

	$ git clone git://github.com/schacon/grit.git mygrit

唯一的差别就是，现在新建的目录成了 mygrit，其他的都和上边的一样。
Git 支持许多数据传输协议。之前的例子使用的是 git:// 协议，不过你也可以用 http(s):// 或者 user@server:/path.git 表示的 SSH 传输协议。

#3. 记录每次更新到仓库

检查当前文件状态

要确定哪些文件当前处于什么状态，可以用 git status 命令。如果在克隆仓库之后立即执行此命令，会看到类似这样的输出：

	$ git status
	    # On branch master
	    nothing to commit (working directory clean)

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



暂存已修改文件

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

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。可以用 git rm 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

如果只是简单地从工作目录中手工删除文件，运行 git status 时就会看到：

	$ git status
		位于分支 master
		尚未暂存以备提交的变更：
		  （使用 "git add/rm <file>..." 更新要提交的内容）
		  （使用 "git checkout -- <file>..." 丢弃工作区的改动）
		
		        删除：     README

		修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）

然后再运行 git rm 记录此次移除文件的操作：

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
移动文件

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

接下来的两个小节将演示如何取消暂存区域中的文件，以及如何取消工作目录中已修改的文件。不用担心，查看文件状态的时候就提示了该如何撤消，所以不需要死记硬背。来看下面的例子，有两个修改过的文件，我们想要分开提交，但不小心用 git add . 全加到了暂存区域。该如何撤消暂存其中的一个文件呢？其实，git status 的命令输出已经告诉了我们该怎么做：

	$ git add .
	    $ git status
	    # On branch master
	    # Changes to be committed:
	    # (use "git reset HEAD <file>..." to unstage)
	    #
	    # modified: README.txt
	    # modified: benchmarks.rb
	    #

就在 “Changes to be committed” 下面，括号中有提示，可以使用 git reset HEAD <file>... 的方式取消暂存。好吧，我们来试试取消暂存 benchmarks.rb 文件：

	$ git reset HEAD benchmarks.rb
	    benchmarks.rb: locally modified
	    $ git status
	    # On branch master
	    # Changes to be committed:
	    # (use "git reset HEAD <file>..." to unstage)
	    #
	    # modified: README.txt
	    #
	    # Changes not staged for commit:
	    # (use "git add <file>..." to update what will be committed)
	    # (use "git checkout -- <file>..." to discard changes in working directory)
	    #
	    # modified: benchmarks.rb
	    #

这条命令看起来有些古怪，先别管，能用就行。现在 benchmarks.rb 文件又回到了之前已修改未暂存的状态。
取消对文件的修改

如果觉得刚才对 benchmarks.rb 的修改完全没有必要，该如何取消修改，回到之前的状态（也就是修改之前的版本）呢？git status 同样提示了具体的撤消方法，接着上面的例子，现在未暂存区域看起来像这样：

	# Changes not staged for commit:
	    # (use "git add <file>..." to update what will be committed)
	    # (use "git checkout -- <file>..." to discard changes in working directory)
	    #
	    # modified: benchmarks.rb
	    #

在第二个括号中，我们看到了抛弃文件修改的命令（至少在 Git 1.6.1 以及更高版本中会这样提示，如果你还在用老版本，我们强烈建议你升级，以获取最佳的用户体验），让我们试试看：

	$ git checkout -- benchmarks.rb
	    $ git status
	    # On branch master
	    # Changes to be committed:
	    # (use "git reset HEAD <file>..." to unstage)
	    #
	    # modified: README.txt
	    #

可以看到，该文件已经恢复到修改前的版本。你可能已经意识到了，这条命令有些危险，所有对文件的修改都没有了，因为我们刚刚把之前版本的文件复制过来重写了此文件。所以在用这条命令前，请务必确定真的不再需要保留刚才的修改。如果只是想回退版本，同时保留刚才的修改以便将来继续工作，可以用下章介绍的 stashing 和分支来处理，应该会更好些。

记住，任何已经提交到 Git 的都可以被恢复。即便在已经删除的分支中的提交，或者用 --amend 重新改写的提交，都可以被恢复（关于数据恢复的内容见第九章）。所以，你可能失去的数据，仅限于没有提交过的，对 Git 来说它们就像从未存在过一样。

#6. 远程仓库的使用

要参与任何一个 Git 项目的协作，必须要了解该如何管理远程仓库。远程仓库是指托管在网络上的项目仓库，可能会有好多个，其中有些你只能读，另外有些可以写。同他人协作开发某个项目时，需要管理这些远程仓库，以便推送或拉取数据，分享各自的工作进展。管理远程仓库的工作，包括添加远程库，移除废弃的远程库，管理各式远程库分支，定义是否跟踪这些分支，等等。本节我们将详细讨论远程库的管理和使用。
查看当前的远程库

要查看当前配置有哪些远程仓库，可以用 git remote 命令，它会列出每个远程库的简短名字。在克隆完某个项目后，至少可以看到一个名为 origin 的远程库，Git 默认使用这个名字来标识你所克隆的原始仓库：

$ git clone git://github.com/schacon/ticgit.git
    Initialized empty Git repository in /private/tmp/ticgit/.git/
    remote: Counting objects: 595, done.
    remote: Compressing objects: 100% (269/269), done.
    remote: Total 595 (delta 255), reused 589 (delta 253)
    Receiving objects: 100% (595/595), 73.31 KiB | 1 KiB/s, done.
    Resolving deltas: 100% (255/255), done.
    $ cd ticgit
    $ git remote
    origin

也可以加上 -v 选项（译注：此为 --verbose 的简写，取首字母），显示对应的克隆地址：

$ git remote -v
    origin git://github.com/schacon/ticgit.git

如果有多个远程仓库，此命令将全部列出。比如在我的 Grit 项目中，可以看到：

$ cd grit
    $ git remote -v
    bakkdoor git://github.com/bakkdoor/grit.git
    cho45 git://github.com/cho45/grit.git
    defunkt git://github.com/defunkt/grit.git
    koke git://github.com/koke/grit.git
    origin git@github.com:mojombo/grit.git

这样一来，我就可以非常轻松地从这些用户的仓库中，拉取他们的提交到本地。请注意，上面列出的地址只有 origin 用的是 SSH URL 链接，所以也只有这个仓库我能推送数据上去（我们会在第四章解释原因）。
添加远程仓库

要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，运行 git remote add [shortname] [url]：

$ git remote
    origin
    $ git remote add pb git://github.com/paulboone/ticgit.git
    $ git remote -v
    origin git://github.com/schacon/ticgit.git
    pb git://github.com/paulboone/ticgit.git

现在可以用字符串 pb 指代对应的仓库地址了。比如说，要抓取所有 Paul 有的，但本地仓库没有的信息，可以运行 git fetch pb：

$ git fetch pb
    remote: Counting objects: 58, done.
    remote: Compressing objects: 100% (41/41), done.
    remote: Total 44 (delta 24), reused 1 (delta 0)
    Unpacking objects: 100% (44/44), done.
    From git://github.com/paulboone/ticgit
    * [new branch] master -> pb/master
    * [new branch] ticgit -> pb/ticgit

现在，Paul 的主干分支（master）已经完全可以在本地访问了，对应的名字是 pb/master，你可以将它合并到自己的某个分支，或者切换到这个分支，看看有些什么有趣的更新。
从远程仓库抓取数据

正如之前所看到的，可以用下面的命令从远程仓库抓取数据到本地：

$ git fetch [remote-name]

此命令会到远程仓库中拉取所有你本地仓库中还没有的数据。运行完成后，你就可以在本地访问该远程仓库中的所有分支，将其中某个分支合并到本地，或者只是取出某个分支，一探究竟。（我们会在第三章详细讨论关于分支的概念和操作。）

如果是克隆了一个仓库，此命令会自动将远程仓库归于 origin 名下。所以，git fetch origin 会抓取从你上次克隆以来别人上传到此远程仓库中的所有更新（或是上次 fetch 以来别人提交的更新）。有一点很重要，需要记住，fetch 命令只是将远端的数据拉到本地仓库，并不自动合并到当前工作分支，只有当你确实准备好了，才能手工合并。

如果设置了某个分支用于跟踪某个远端仓库的分支（参见下节及第三章的内容），可以使用 git pull 命令自动抓取数据下来，然后将远端分支自动合并到本地仓库中当前分支。在日常工作中我们经常这么用，既快且好。实际上，默认情况下 git clone 命令本质上就是自动创建了本地的 master 分支用于跟踪远程仓库中的 master 分支（假设远程仓库确实有 master 分支）。所以一般我们运行 git pull，目的都是要从原始克隆的远端仓库中抓取数据后，合并到工作目录中的当前分支。
推送数据到远程仓库

项目进行到一个阶段，要同别人分享目前的成果，可以将本地仓库中的数据推送到远程仓库。实现这个任务的命令很简单：git push [remote-name] [branch-name]。如果要把本地的 master 分支推送到 origin 服务器上（再次说明下，克隆操作会自动使用默认的 master 和 origin 名字），可以运行下面的命令：

$ git push origin master

只有在所克隆的服务器上有写权限，或者同一时刻没有其他人在推数据，这条命令才会如期完成任务。如果在你推数据前，已经有其他人推送了若干更新，那你的推送操作就会被驳回。你必须先把他们的更新抓取到本地，合并到自己的项目中，然后才可以再次推送。有关推送数据到远程仓库的详细内容见第三章。
查看远程仓库信息

我们可以通过命令 git remote show [remote-name] 查看某个远程仓库的详细信息，比如要看所克隆的 origin 仓库，可以运行：

$ git remote show origin
    * remote origin
    URL: git://github.com/schacon/ticgit.git
    Remote branch merged with 'git pull' while on branch master
    master
    Tracked remote branches
    master
    ticgit

除了对应的克隆地址外，它还给出了许多额外的信息。它友善地告诉你如果是在 master 分支，就可以用 git pull 命令抓取数据合并到本地。另外还列出了所有处于跟踪状态中的远端分支。

上面的例子非常简单，而随着使用 Git 的深入，git remote show 给出的信息可能会像这样：

$ git remote show origin
    * remote origin
    URL: git@github.com:defunkt/github.git
    Remote branch merged with 'git pull' while on branch issues
    issues
    Remote branch merged with 'git pull' while on branch master
    master
    New remote branches (next fetch will store in remotes/origin)
    caching
    Stale tracking branches (use 'git remote prune')
    libwalker
    walker2
    Tracked remote branches
    acl
    apiv2
    dashboard2
    issues
    master
    postgres
    Local branch pushed with 'git push'
    master:master

它告诉我们，运行 git push 时缺省推送的分支是什么（译注：最后两行）。它还显示了有哪些远端分支还没有同步到本地（译注：第六行的caching 分支），哪些已同步到本地的远端分支在远端服务器上已被删除（译注：Stale tracking branches 下面的两个分支），以及运行git pull 时将自动合并哪些分支（译注：前四行中列出的 issues 和 master 分支）。
远程仓库的删除和重命名

在新版 Git 中可以用 git remote rename 命令修改某个远程仓库在本地的简称，比如想把 pb 改成 paul，可以这么运行：

$ git remote rename pb paul
    $ git remote
    origin
    paul

注意，对远程仓库的重命名，也会使对应的分支名称发生变化，原来的 pb/master 分支现在成了 paul/master。

碰到远端仓库服务器迁移，或者原来的克隆镜像不再使用，又或者某个参与者不再贡献代码，那么需要移除对应的远端仓库，可以运行 git remote rm命令：

$ git remote rm paul
    $ git remote
    origin







