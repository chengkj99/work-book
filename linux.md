# 常用命令

## cp 复制

`cp sourcepath targetpath`

## scp 复制

```shell
scp [可选参数] file_source file_target

scp -r dist/* robot@10.1xx.0.xx:/home/robot/www/brain-plus/meg-brain
```

## nohup 后台运行

```shell
nohup command &
nohup command >log.out 2>&1 &
```

## ps 查询相关进程

```shell
ps -aux | grep nginx

```

## 查看端口占用

```shell
# 查看所有端口
netstat -ntlp

# 查看指定端口
netstat -ntulp | grep 8080
```

## kill 杀掉进程

```shell
kill -9 pid
```

## ssh

```shell
ssh user@ip 登录服务器
```

## curl 文件上传

使用 curl -F (--form) 选项，会添加 `enctype=”multipart/form-data` 到请求头中

```shell

curl -F ‘data=@path/to/local/file’ UPLOAD_ADDRESS

# 上传单个文件
curl -F 'img_avatar=@/home/petehouston/hello.txt' http://localhost/upload

# 上传多个文件
curl -F 'fileX=@/path/to/fileX' -F 'fileY=@/path/to/fileY' ... http://localhost/upload

# 上传文件数组
curl -F 'files[]=@/path/to/fileX' -F 'files[]=@/path/to/fileY' ... http://localhost/upload
```

## 实践

### 构建增量上传

```shell

set -e

######################自定义配置######################

# 项目名称
projectName={YourProjectName}

# 要上传的文件夹，注意不要忽略最后一个斜杠
dist=./dist/$projectName/

# 编译命令
ng build --configuration=test

######################通用配置######################

# 机器信息
ip=xxxxx
user=robot

# 第一次免密登录
ssh-copy-id $user@$ip

# nginx目录
dir=/home/robot/www/$projectName/

# 增量上传
ssh $user@$ip "mkdir -p $dir"
rsync -r --verbose --checksum --progress $dist $user@$ip:$dir/

echo 地址: http://$ip:8080/$projectName/

```

### 打包文件上传

```shell
## 复制上传文件到服务器

set -e

# 项目名称
projectName=gtp-apk-files/src

# 要上传的文件夹，注意不要忽略最后一个斜杠
dist=./dist/$projectName/

# 机器信息
ip=xxxxxx
user=robot

# 第一次免密登录
ssh-copy-id $user@$ip

# nginx目录
dir=/home/robot/www/$projectName/

# 创建文件夹
ssh $user@$ip "mkdir -p $dir"

# 执行命令
scp ./gtp-pda.apk $user@$ip:$dir

echo 通过地址访问: http://$ip:8081

```
