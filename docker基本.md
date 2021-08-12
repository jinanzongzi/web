# 基础命令

![image text](命令结构图.png)

## 获取一个镜像
``` s
docker run 镜像名 
OR
docker pull 镜像名
```

## 搜素镜像

``` s
docker search 镜像名称 -f(筛选条件)
```

## 创建Centos容器
``` s
docker run -it(交互状态) -d (后台) 镜像名 --name(给容器起名字)
```
例如：
* ``` docker run -it centos /bin/bash```
* ```s
    docker run -d centos /bin/bash -c "while true;do echo @123@;sleep 1;done" 
     ```

## 查看创建的容器

```s
docker ps -a(显示全部容器 包括已关闭) -q(只显示Id) -l(最后一个创建)
```

## 查看容器的日志

```s
docker logs -tf(显示时间戳和追加更新) --tail number (显示条数) 容器ID
```

## 启动和重启一个容器

启动已停止的容器
```s
docker start 容器ID
docker restart 容器ID
```

## 停止和杀死一个容器

```s
docker stop 容器ID
docker kill 容器ID
```

## 删除一个容器

``` s
docker rm -f(强制删除正在运行的容器) 容器ID
``` 

## 操作后台运行的容器

exec 直接可以输入新的指令
attach 进入容器的当前控制台

```s
docker exec -it(配合这两个参数使用) 容器ID
docker attach 容器Id
```

## 显示容器的操作信息
```s 
docker top 容器ID
```

## 显示容器的具体信息

```s
docker inspect 容器ID
```

## 暂停和取消暂停容器

``` s
docker pause 容器ID
docker unpause 容器ID
``` 
## 将容器中的数据拷贝到主机上

```s
docker cp 容器ID:容器中的文件 主机中的位置
```

