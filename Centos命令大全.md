# Centos命令大全

cd ~ 切换到主目录
cd /tmp 切换到目录tmp
cd dir 切换到当前目录下的dir目录
cd / 切换到根目录
cd .. 切换到上一级目录
cd ../.. 切换到上二级目录
cd ~ 切换到用户目录

新建文件夹： mkdir filename
文件重命名： mv oldfilename newfilename
移动文件： mv filename 路径（若多个文件则，filename1/filename2）
安装rpm： rpm -ivh filename
更新rpm： rpm -Uvh filename
解压tar.gz： tar -zcvf 压缩文件名.tar.gz 被压缩文件名
压缩文件： tar -zxcf 压缩文件名.tar.gz
安装软件： yum install software
查看源列表： yum repolist

查看CPU位数： getconf LONG_BIT
查看内核/操作系统/CPU信息： uname -a
查看CentOS版本： cat /etc/redhat-release
删除文件： rm filename
删除目录以及它所包含的所有内容： rm -rf 目录
查看crontab文件： cat /etc/crontab

查看文件内容编码： enca -L zh_CN filename
文件内容编码转码： iconv -f 原始编码 -t 目标编码 原始文件 > 目标文件
enca -L 当前语言 -x 目标编码 文件名
enca -L zh_CN -x UTF-8 < file1 > file2 (如果不想覆盖源文件)
文件名编码转换： convmv -f 原始编码 -t 目标编码 --notest *.mp3(-r 这个表示递归转换当前目录下的所有子目录)

查看Java运行： ps -ef | grep java（Ctrl+C停止）
查看日志： tailf /tmp/skg_json_log

 

利用vim编辑文件： vim filename
进入INSERT：i
退出INSERT使用其他命令：ESC
读入一个文件内容，并写入到当前编辑器中：:r filename
将该编辑器中的内容写入到一个新文件中：:w newfilename
暂时离开 vi到指令列模式下执行：:! cmd
进入shell命令行，执行完命令后ctril+d推出重新进入vim继续编辑：:sh
保存文件但不退出：:w
强制保存，不退出：:w!
将修改的文件另存到file中，不退出vi：:w file
退出不保存：:q
退出并保存：ZZ(:wq)
强制保存并退出：:wq!
强制退出并不保存：:q!
放弃所有修改，从上次保存文件开始再编辑：:e!