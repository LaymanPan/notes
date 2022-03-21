> 文件同步

```bash
#!/bin/bash
# 文件同步
# 作者：panym

fromFilePath="/usr/local/bin/sync"
destFilePath="/usr/local/bin/sync"
lastUpdateFile="$fromFilePath/lastUpdateLog"
lastUpdateTime=$(cat $lastUpdateFile) || '2021-09-12'
echo $lastUpdateTime
echo $fromFilePath
destServerIp="root@192.168.133.130"
destServerPass="123456"
echo "上次同步时间为: $lastUpdateTime"
# 获取更新的文件
updateFiles=`find $fromFilePath -newermt "$lastUpdateTime"  -name "syncFile*"` 
sshpass -p $destServerPass ssh $destServerIp "if [ ! -d $destFilePath ]; then mkdir -p $destFilePath; fi"
if [ $updateFiles ];
then echo "有文件";
else echo "无文件";exit;
fi
echo "文件个数为: ${#updateFiles[@]}"
for updateFile in $updateFiles
do
	# 复制到远程目录
	fileName=$(basename $updateFile)
	echo $fileName
	sshpass -p $destServerPass scp $updateFile "$destServerIp:$destFilePath/$fileName"
done



nowTime=$(date +"%Y-%m-%d %H:%M:%S")
echo "同步结束 当前时间为：$nowTime"
echo -e $nowTime > /usr/local/bin/sync/lastUpdateLog
```

> tomcat服务器启动

```bash

#!/bin/bash
#2021-09-13
#服务启动


pidFilePath="/data/fdmp/fdmp-apache-tomcat-9.0.31/bin"
startFilePath="/data/fdmp/fdmp-apache-tomcat-9.0.31/bin"

pid=`cat $pidFilePath/tomcat.pid`
# 停止正在运行的服务
if [ -n "$pid" ];
then
kill -9 $pid
rm -f "$pidFilePath/tomcat.pid"
echo "停止服务进程，kill $pid"
fi;

# 启动
$startFilePath/startup.sh
echo "服务启动中"
```

> 健康检查

```bash
#!/bin/bash
# 健康检测
# 2021-0915
# 启动文件路径
RUN_FILE_PATH="/data/fdmp/fdmp-apache-tomcat-9.0.31"
# 请求接口
REQUEST="http://127.0.0.1:8081/fdmp/rest/sso/health"
# 日志文件地址
LOG_FILE_PATH="/data/fdmp/fdmp-apache-tomcat-9.0.31/healthLog"
# 日志文件名
NORMAL_LOG_NAME="health.log"
# 重试次数文件名
RETRY_LOG_NAME="retryTime.log"
# 当前时间
NOW_DATE=`date +"%Y-%m-%d %H:%M:%S"`
# 重试次数
MAX_RETRY_TIME=2
result=`curl $REQUEST`
echo "result is $result"

if [ ! -d $LOG_FILE_PATH ];
then mkdir -p $LOG_FILE_PATH
fi;
if [ ! -f $LOG_FILE_PATH/$RETRY_LOG_NAME ];
then touch $LOG_FILE_PATH/$RETRY_LOG_NAME
fi;
if [ ! -f $LOG_FILE_PATH/$NORMAL_LOG_NAME ];
then touch $LOG_FILE_PATH/$NORMAL_LOG_NAME
fi;
if [ "$result" = "true" ];
then
	echo "$NOW_DATE 正常"
	# 记录正确日志
	echo "$NOW_DATE  正常" >> $LOG_FILE_PATH/$NORMAL_LOG_NAME
else
	# 记录错误日志
	echo "$NOW_DATE 异常" >> $LOG_FILE_PATH/$NORMAL_LOG_NAME	
	retry_time=`cat $LOG_FILE_PATH/$RETRY_LOG_NAME`
	if [ -z $retry_time ];
	then
		retry_time=1
		echo "$retry_time" > $LOG_FILE_PATH/$RETRY_LOG_NAME
	fi;
	echo "服务出现错误 错误次数为$retry_time"
	result=$[retry_time%MAX_RETRY_TIME]
	echo $[retry_time+1] > $LOG_FILE_PATH/$RETRY_LOG_NAME
	if [ 0 -eq $result ];
	then
		# 重启服务
		$RUN_FILE_PATH/run.sh
	fi;
fi;

```

