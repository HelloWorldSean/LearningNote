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

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/388811ffdf4d450c8860e77112e8a8bf~tplv-k3u1fbpfcp-watermark.image)

（图片源地址：https://blog.fntsr.tw/articles/189/）

#### docker attach	

Attach local standard input, output, and error streams to a running container

#### docker build	

Build an image from a Dockerfile

#### docker builder	

Manage builds

#### docker checkpoint	

Manage checkpoints
docker commit	

Create a new image from a container’s changes
docker config	

Manage Docker configs
docker container	

Manage containers
docker context	

Manage contexts
docker cp	

Copy files/folders between a container and the local filesystem
docker create

Create a new container
docker deploy	

Deploy a new stack or update an existing stack
docker diff	

Inspect changes to files or directories on a container’s filesystem
docker engine	

Manage the docker engine
docker events	

Get real time events from the server
docker exec	

Run a command in a running container
docker export	

Export a container’s filesystem as a tar archive
docker history	

Show the history of an image
docker image	

Manage images
docker images	

List images
docker import	

Import the contents from a tarball to create a filesystem image
docker info	

Display system-wide information
docker inspect	

Return low-level information on Docker objects
docker kill	

Kill one or more running containers
docker load	

Load an image from a tar archive or STDIN
docker login	

Log in to a Docker registry
docker logout	

Log out from a Docker registry
docker logs	

Fetch the logs of a container
docker manifest	

Manage Docker image manifests and manifest lists
docker network	

Manage networks
docker node	

Manage Swarm nodes
docker pause	

Pause all processes within one or more containers
docker plugin	

Manage plugins
docker port	

List port mappings or a specific mapping for the container
docker ps	

List containers
docker pull	

Pull an image or a repository from a registry
docker push	

Push an image or a repository to a registry
docker rename	

Rename a container
docker restart	

Restart one or more containers
docker rm	

Remove one or more containers
docker rmi	

Remove one or more images
docker run	

Run a command in a new container
docker save	

Save one or more images to a tar archive (streamed to STDOUT by default)
docker search	

Search the Docker Hub for images
docker secret	

Manage Docker secrets
docker service	

Manage services
docker stack	

Manage Docker stacks
docker start	

Start one or more stopped containers
docker stats	

Display a live stream of container(s) resource usage statistics
docker stop	

Stop one or more running containers
docker swarm	

Manage Swarm
docker system	

Manage Docker
docker tag	Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
docker top	Display the running processes of a container
docker trust	Manage trust on Docker images
docker unpause	Unpause all processes within one or more containers
docker update	Update configuration of one or more containers
docker version	Show the Docker version information
docker volume	Manage volumes
docker wait	Block until one or more containers stop, then print their exit codes





### 高级命令



## Docker Compose





## Docker Swarm