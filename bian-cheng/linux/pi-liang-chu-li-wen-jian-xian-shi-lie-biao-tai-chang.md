# 批量处理文件显示列表太长

很多时候我们需要批量的处理文件，比如批量的重命名。但是如果数据量太大的话，是会报警告的：

 `zsh: argument list too long: cp` 

这时候就不能蛮干了，用一点小技巧，比如 for 循环 

`for i in *.txt;do rename -d profile $i;done` 

这个命令是把文件夹下所有的以`profile`开头的文件的前缀去掉。这个命令的格式是

 `for i in [what];do [command];done`

