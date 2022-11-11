supervisor启动步骤

1. 进入虚拟环境

   ```shell
   conda activate 虚拟环境名称 | source python路径
   ```

2. 安装supervisor

   ```shell
   pip install supervisor
   ```

3. 创建对应的目录(根据需要)

   ```shell
   cd /etc
   mkdir supervisor
   cd supervisor
   mkdir supervisor.conf.d
   ```

4. 生成supervisor配置文件到指定位置，建议放到/etc/supervisor目录下

   ```shell
   echo_supervisord_conf > /etc/supervisor/supervisord.conf.d/supervisord.conf
   ```

5. 修改生成的配置文件最后两行

   ```shell
   ;[include]
   ;files = /etc/supervisor/supervisord.conf.d/*.conf
   ```

   去掉include和file的注释

   file注释可以指定具体的文件，可以包含某个目录下的文件

6.编写对应的配置文件(以celery为例)

```shell
[program:celeryd]
# 启动程序的命令
command=/opt/anaconda3/envs/py3_edc/bin/celery -A celery_app worker -c 4 -l info
# 进程执行前，会切换到这个目录下执行 默认为不切换。。。非必须
directory=/mnt/workspace/edc_dev_deploy/edc_proj
# 相同的listener启动的个数
numprocs=1
# 子进程的stdout的日志路径，可以指定路径，AUTO，none等三个选项。
# 设置为none的话，将没有日志产生。设置为AUTO的话，将随机找一个地方
# 生成日志文件，而且当supervisord重新启动的时候，以前的日志文件会被
# 清空。当 redirect_stderr=true的时候，sterr也会写进这个日志文件 
stdout_logfile=/mnt/workspace/edc_dev_deploy/edc_proj/logs/celeryworker.log
# 这个东西是设置stderr写的日志路径，当redirect_stderr=true。这个就不用
# 设置了，设置了也是白搭。因为它会被写入stdout_logfile的同一个文件中
# 默认为AUTO，也就是随便找个地存，supervisord重启被清空。。非必须设置
stderr_logfile=/mnt/workspace/edc_dev_deploy/edc_proj/logs/celeryworker_error.log
# 是否随supervisord启动一起启动，默认true
autostart=true
# 是否自动重启，和program一个样，分true,false,unexpected等，注意unexpected和exitcodes的关系
autorestart=true
# 也是一样，进程启动后跑了几秒钟，才被认定为成功启动，默认1
startsecs=10
# max num secs to wait b4 SIGKILL (default 10)
stopwaitsecs=600
# 优先级，相对于组和组之间说的 默认999。。非必须选项
priority=1
```

6. 启动supervisor

```shell
supervisord -c 配置文件位置
```

7. 进入supervisorctl查看任务运行的状态

   status:查看任务运行的状态

   reload:重载任务

   restart:重启supervisor

   start:启动supervisor

   reread:重新读取配置文件

   