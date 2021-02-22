# Docker

## 安装

### Ubuntu

官方文档：https://docs.docker.com/engine/install/ubuntu/

```shell
# 卸载旧版本
sudo apt-get remove docker docker-engine docker.io containerd runc

# 更新apt软件包索引并安装软件包以允许apt通过HTTPS使用存储库
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
    
# 添加Docker的官方GPG密钥：9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# 通过搜索指纹的后8个字符，验证您现在是否拥有带有指纹的密钥
sudo apt-key fingerprint 0EBFCD88

# 添加仓库
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   
# 安装docker引擎，最新版本
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# 安装指定docker版本
# a.列出您的仓库中可用的版本
	apt-cache madison docker-ce
# b.安装指定版本
	sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

#启动docker
sudo systemctl start docker

# 验证docker安装成功
sudo docker run hello-world

```

### CentOS

官方文档：https://docs.docker.com/engine/install/centos/

```shell
# 卸载旧版本
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

# 配置镜像仓库
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
# 安装docker引擎，最新版本
sudo yum install docker-ce docker-ce-cli containerd.io

# 安装指定docker版本
# a.列出您的仓库中可用的版本
	yum list docker-ce --showduplicates | sort -r
# b.安装指定版本
	sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io

#启动docker
sudo systemctl start docker

# 验证docker安装成功
sudo docker run hello-world
```

![image-20210219152720461](C:\Users\yingchao.yuan\AppData\Roaming\Typora\typora-user-images\image-20210219152720461.png)

### Windows & Mac

docker提供了Windows & Mac 提供了 Docker Desktop版本，傻瓜式安装。

Windows: https://hub.docker.com/editions/community/docker-ce-desktop-mac/

Mac: https://hub.docker.com/editions/community/docker-ce-desktop-windows/

### Kitematic

配合Kitematic使用，一键拉取运行镜像 ，支持Windows、Mac、Ubuntu。

下载地址：https://github.com/docker/kitematic/tags

![image-20210219154439694](C:\Users\yingchao.yuan\AppData\Roaming\Typora\typora-user-images\image-20210219154439694.png)

## Docker命令

### 基础命令



### 高级命令



## Docker Compose





## Docker Swarm