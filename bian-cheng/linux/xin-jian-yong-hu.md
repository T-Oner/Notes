# 新建用户

sudo adduser \[username\]

## 给用户添加 sudo 权限

sudo usermod -G sudo \[username\]

## 移动用户的 home 路径

第一：修改/etc/passwd文件

第二：usermod命令 第二种：usermod

usermod -d /usr/newfolder -u uid username

-u后面一定要接uid啊，然后是username

附：usermod详细参数

语 法：usermod \[-LU\]\[-c &lt;备注&gt;\]\[-d &lt;登入目录&gt;\]\[-e &lt;有效期限&gt;\]\[- f &lt;缓冲天数&gt;\]\[-g &lt;群组&gt;\]\[-G &lt;群组&gt;\]\[-l &lt;帐号名称&gt;\]\[-s \]\[-u \] \[用户帐号\]

id查看

id 用户名

例：修改oracle用户的主目录到/u01/app/oracle

id oracle

uid=501\(oracle\) gid=501\(oinstall\) groups=501\(oinstall\)

usermod -d /u01/app/oracle -u 501 oracle

su - oracle

-bash-4.1$ pwd /u01/app/oracle

--修改成功

