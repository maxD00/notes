新建gitlab容器,挂载了宿主机目录,销毁gitlab容器再重建也可以.

```shell
# 新建gitlab容器命令
docker run --detach \
  --hostname 192.168.1.173 \
  --publish 10443:443 --publish 8081:80 --publish 10022:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
 gitlab/gitlab-ce:13.2.2-ce.0
```

此命令中映射了`hostport:containerport`,这时可用`http://192.168.1.173:8081`访问gitlab页面,但是仓库的clone地址还定位为`http://192.168.1.173/xxx/xx`(以http协议地址为例),这是因为在docker内部,gitlab的http协议端口的确为80.

需要修改的配置文件为`/etc/gitlab/gitlab.rb`.

由于容器挂载了宿主机目录`$GITLAB_HOME/config:/etc/gitlab`,所以在宿主机或者docker环境内修改都可以,因为要使用gitlab-cli的更新配置功能,所以直接进入docker内部进行修改.需要修改的参数如下:

```shell
# HTTP协议的仓库地址前缀
external_url "http://192.168.1.173:8081"

# 如果使用HTTP协议,则仓库地址前缀
external_url "https://192.168.1.173:8081"

# 默认,nginx监听地址会从external_url中提取,但是上面docker端口映射为了8081:80,即外部请求会转发到容器内的80端口,所以需要修改
# nginx监听80端口,如果映射为8081:8081,则无需修改(会从external_url提取使用8081)
nginx['listen_port'] = 8081

# 修改ssh协议的对外显示端口
gitlab_rails['gitlab_shell_ssh_port'] = 10022	
```

最后更新配置即可:

`gitlab-cli reconfigure`



