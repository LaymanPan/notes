> /etc/login.defs Linux口令周期设置生效问题(linux下密码过期时间)

![image-20211018092127127](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018092127127.png)

第一行，密码使用最长时间为90天，90天后会有提醒。
第二行，密码使用最短时间为1天，1天之内是不能修改密码的。
第三行，密码复杂度，最少8位
第四行，密码过期后会提醒7天，7天之后还没改密码的话，帐号会被冻结失效。

> /etc/pam.d/system-auth 限制终端非法登录

1. 检测是否含有pam_tally2.so 模块

   ![image-20211018095407862](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018095407862.png)

2. 配置 /etc/pam.d/system-auth

   ![image-20211018095507574](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018095507574.png)

注意添加的位置，要写在第一行，即#%PAM-1.0的下面。

以上策略表示：普通帐户和 root 的帐户登录连续 3 次失败，就统一锁定 10 秒， 10 秒后可以解锁,root 用户 如果超过3次失败则限制20秒。如果不想限制 root 帐户，可以把 even_deny_root root_unlock_time。且在锁定期间 即使输入了正确密码 也会认定失败。

此操作只影响终端登录。

**查看用户失败次数**
![image-20211018095742525](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018095742525.png)

[root@localhost ~]# pam_tally2      --------------------查看所有用户登录失败次数

[root@localhost ~]# pam_tally2 --user root   ------------指定查看登录失败的用户次数

**解锁指定用户**

[root@iZ25dsfp6c3dZ ~]# pam_tally2 -r -u root

参考链接：

http://www.361way.com/pam-tally2/4277.html

> /etc/pam.d/sshd 限制ssh非法登录

1. 编辑 /etc/pam.d/sshd

   ![image-20211018095940164](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018095940164.png)

> /etc/profile 空闲超时自动退出

**配置/etc/profile TMOUT变量 单位为秒 如下图表示60秒无操作则退出**

![image-20211018100801466](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018100801466.png)

执行 **source /etc/profile**生效配置 

**export TMOUT=60** 生效空闲超时退出

![image-20211018101726976](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018101726976.png)

**UNSET TMOUT** 失效空闲超时

![image-20211018101704150](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20211018101704150.png)

