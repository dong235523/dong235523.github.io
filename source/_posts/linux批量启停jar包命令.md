---
title: linux批量启停jar包命令
date: 2024-05-06 14:11:16
tags: 
  - linux
categories: 运维
cover: false
---


### 批量后台启动jar包命令
```
# 定义日志目录
LOG_DIR="logs"
mkdir -p "$LOG_DIR"

# 定义要执行的 JAR 包列表和延迟时间
JAR_FILES=(
    "xxx1.jar"
    "xxx2.jar"
    "xxx3.jar"
    "xxx4.jar"
    "xxx5.jar"
)
DELAY=100
# 循环遍历执行每个 JAR 包
for JAR_FILE in "${JAR_FILES[@]}"; do
    LOG_FILE="$LOG_DIR/${JAR_FILE%.jar}.log" # 构建日志文件名，例如 hkcloud-nacos.log

    echo "延迟 $DELAY 秒后开始执行 $JAR_FILE ..."
    sleep $DELAY

    echo "开始执行 $JAR_FILE ..."
    nohup java -jar "$JAR_FILE" >> "$LOG_FILE" 2>&1 &
    echo "执行 $JAR_FILE 完成。日志文件位于 $LOG_FILE。"
done

echo "所有 JAR 包已在后台执行。"
```
将上述代码放到一个新建的txt文件中，然后尾缀改成.sh文件即可


### 批量停止jar包命令
```
# 定义要关闭的 JAR 包列表
JAR_FILES=(
    "xxx1.jar"
    "xxx2.jar"
    "xxx3.jar"
    "xxx4.jar"
    "xxx5.jar"
)

# 遍历每个 JAR 文件，尝试关闭相关联的进程
for JAR_FILE in "${JAR_FILES[@]}"; do
    echo "尝试关闭 $JAR_FILE ..."
    PIDS=$(ps aux | grep "$JAR_FILE" | grep -v grep | awk '{print $2}')
    if [ -n "$PIDS" ]; then
        kill -9 $PIDS
        echo "$JAR_FILE 进程已关闭。"
    else
        echo "$JAR_FILE 进程未找到。"
    fi
done

echo "所有 JAR 包已关闭。"
```
将上述代码放到一个新建的txt文件中，然后尾缀改成.sh文件即可