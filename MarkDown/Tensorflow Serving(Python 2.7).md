# Tensorflow Serving(Python 2.7)
---
## 安装（ubuntu server 16 x64）
### 安装Bazel（源码编译）
需要Bazel 0.5.4及以上版本，[ Bazel installation instructions](http://bazel.build/docs/install.html)

* 安装依赖
`apt-get install build-essential openjdk-8-jdk python zip unzip`

* 下载源码
Download `bazel-<version>-dist.zip` from the [release page](https://github.com/bazelbuild/bazel/releases)

* 解压并编译
新建文件夹后，`unzip bazel-<version>-dist.zip`
运行`bash ./compile.sh`

* 移动编译文件
将 output/bazel 文件复制到usr/local/bin中

* 下载安装程序
在[地址](https://github.com/bazelbuild/bazel/releases)下载`bazel-<version>-installer-linux-x86_64.sh`
运行`chmod +x bazel-<version>-installer-linux-x86_64.sh`
`./bazel-<version>-installer-linux-x86_64.sh`

* 添加环境变量
在 ~/.bashrc底部添加语句`export PATH="$PATH:$HOME/bin"`

### 安装gRPC
```
sudo pip install grpcio
```

### 安装依赖包
```
sudo apt-get update && sudo apt-get install -y \
        build-essential \
        curl \
        libcurl3-dev \
        git \
        libfreetype6-dev \
        libpng12-dev \
        libzmq3-dev \
        pkg-config \
        python-dev \
        python-numpy \
        python-pip \
        software-properties-common \
        swig \
        zip \
        zlib1g-dev
        
pip install tensorflow-serving-api
```

### 安装tensorflow-model-server
```
echo "deb [arch=amd64] http://storage.googleapis.com/tensorflow-serving-apt stable tensorflow-model-server tensorflow-model-server-universal" | sudo tee /etc/apt/sources.list.d/tensorflow-serving.list

curl https://storage.googleapis.com/tensorflow-serving-apt/tensorflow-serving.release.pub.gpg | sudo apt-key add -

sudo apt-get update && sudo apt-get install tensorflow-model-server

# 如需更新，运行
sudo apt-get upgrade tensorflow-model-server
```

###源码安装
```
git clone --recurse-submodules https://github.com/tensorflow/serving
cd serving
bazel build -c opt tensorflow_serving/...
bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server
# test
bazel test -c opt tensorflow_serving/...
```

##docker 官方
docker配置文件[Dockerfile.devel](https://github.com/tensorflow/serving/blob/master/tensorflow_serving/tools/docker/Dockerfile.devel)
```
# 构建容器
docker build --pull -t tfs -f Dockerfile.devel .
# 运行容器
docker run -it tfs
docker run -d --name tfs $USER/tensorflow-serving-devel --name tfs
# 进入容器
docker exec -it tfs /bin/bash
# Clone, configure and test Tensorflow Serving in the running container
# 更新后无TensorFlow文件夹及configure文件，issue#892
git clone --recurse-submodules https://github.com/tensorflow/serving
cd serving/tensorflow
./configure
cd ..
bazel test tensorflow_serving/...
```

##docker bitnami

```
# docker pull bitnami/tensorflow-serving:latest
# docker run --name tensorflow-serving bitnami/tensorflow-serving:latest

# 安装docker-composer
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
## bitnami
curl -sSL https://raw.githubusercontent.com/bitnami/bitnami-docker-tensorflow-serving/master/docker-compose.yml > docker-compose.yml
docker-compose up -d

# 拉取serving镜像
docker pull bitnami/tensorflow-serving:latest
# 存储位置
docker run -v /path/to/tensorflow-serving-persistence:/bitnami bitnami/tensorflow-serving:latest

# 连接其他容器
# Download the Inception trained data
mkdir /tmp/model-data
curl -o '/tmp/model-data/inception-v3-2016-03-01.tar.gz' 'http://download.tensorflow.org/models/image/imagenet/inception-v3-2016-03-01.tar.gz'
cd /tmp/model-data
tar xzf inception-v3-2016-03-01.tar.gz
# Create a network
docker network create app-tier --driver bridge
# Launch the TensorFlow Serving server instance
docker run -d --name tensorflow-serving \
    --volume /tmp/model-data:/bitnami/model-data
    --network app-tier \
    bitnami/tensorflow-serving:latest
# Launch your TensorFlow Inception client instance
docker run -it --rm \
    --volume /tmp/model-data:/bitnami/model-data
    --network app-tier \
    bitnami/tensorflow-inception:latest inception_client --server=tensorflow-serving:9000 --image=path/to/image.jpg

# 配置
# Run the TensorFlow Serving image
docker run --name tensorflow-serving -v /path/to/tensorflow-serving-persistence:/bitnami bitnami/tensorflow-serving:latest
# Edit the configuration
vi /path/to/tensorflow-serving-persistence/conf/tensorflow-serving.conf
# Restart TensorFlow Serving
docker restart tensorflow-serving

# Logging
docker logs tensorflow-serving

# 维护
# 获取更新
docker pull bitnami/tensorflow-serving:latest
# 停止及备份
docker stop tensorflow-serving
rsync -a /path/to/tensorflow-serving-persistence /path/to/tensorflow-serving-persistence.bkp.$(date +%Y%m%d-%H.%M.%S)
# 删除现行容器
docker rm -v tensorflow-serving
# 运行新容器
docker run --name tensorflow-serving bitnami/tensorflow-serving:latest
```

```

```
