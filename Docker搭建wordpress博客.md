# 准备
拉取 wordpress, mysql 镜像
1. `docker pull wordpress`
2. `docker pull mysql`

# 启动 wordpress, mysql 容器
1. 启动 wordpress 容器, 将容器 80 端口映射到主机端口 8080
    `docker run -d -p 8080:80 --name wordpress01 wordpress`

2. 启动 mysql 容器, 映射数据库端口到主机的 3306, 设置 root 密码为 123456
    `docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql`

3. 查看容器状态 `docker ps`
    ![容器状态](/home/yunqing/Github/Myblog/Docker搭建wordpress博客/查看容器状态.png)

# 配置 mysql 容器
1. 进入容器
    `docker exec -it mysql01 /bin/bash`

2. 使用命令 `mysql` 进入MYSQL终端，创建 wordpress 数据库
    `create database wordpress`
    ![创建数据库](/home/yunqing/Github/Myblog/Docker搭建wordpress博客/创建数据库.png)

3. 退出容器

# 访问 wordpress 开始建站
1. 我是搭建在云上并且绑定了域名，所有直接访问域名就好本地搭建的访问`localhost:80`
2. 根据之前创建mysql容器的信息填写数据库信息 注意数据库主机填写docker0网卡的ip 一般是 172.17.0.1 可以通过`ip addr`查看
    ![](/home/yunqing/Github/Myblog/Docker搭建wordpress博客/wordpress安装页面.png)
3. 不出意外的话建站基本完成，之后就是 wordpess 相关的主题和插件配置了

# 其他一些问题
安装主题时上传提示超过php.ini 限制
1. 进入 wordpress 容器
    `docker exec -it wordpress01 /bin/bash`

2. 在 /usr/local/etc/php 目录下创建 php.ini 文件（处理可能不规范，但是懒得学这么多，反正在容器里）

    ```shell
    cd /usr/local/etc/php
    cp ./php.ini-production ./php.ini
    ```

3. 容器内没有 vi 相关命令，使用 sed 命令查找并修改参数 upload_max_filesize, 将文件限制提升为 5M
    `sed -i "s/upload_max_filesize\ =\ 2M/upload_max_filesize\ =\ 5M/g" php.ini`

4. 查看一下修改是否写入	
    `cat php.ini |grep up	load_max_filesize`
    ![修改上传限制](/home/yunqing/Github/Myblog/Docker搭建wordpress博客/修改上传限制.png)

5. 一般直接生效了 刷新下网站 没有的话重启一下容器就好了
    `docker restart wordpress01`

6. 推荐主题 `Argon`

