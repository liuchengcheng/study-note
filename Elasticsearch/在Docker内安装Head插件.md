# 在Docker内安装Head插件

<https://www.jianshu.com/p/8091adadfc1b>

### 1、进行container,安装插件
```
docker exec -it elasticsearch /bin/bash
```

### 2、因为elasticsearch的docker image是基于debian制作的，里面除了java，像vim，git这些都没有的，所以首先需要安装这些工具
```
apt-get update
apt-get install vim
apt-get install git

如果是CentOS
yum UPDATE 
yum install vim
yum install git
```

### 3、因为 head 插件在5.0+版本以后是基于nodejs 和 grunt的，所以需要安装nodejs，首先下载nodejs
```
cd /opt
wget https://nodejs.org/dist/v7.7.1/node-v7.7.1-linux-x64.tar.xz
tar -xvf node-v7.7.1-linux-x64.tar.xz
mv node-v7.7.1-linux-x64.tar.xz node
```

### 4、然后把nodejs添加到环境变量里面
```
vim /etc/profile
export NODE_HOME=/opt/node/bin
export PATH=$PATH:$NODE_HOME

退出文件运行source命令，使环境变量生效
source /etc/profile

验证是否安装成功，正确显示版本号为成功
node -v
npm -v
```

### 5、开始安装Head插件
```
cd /usr/share/elasticsearch/plugins/
git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install -g cnpm --registry=https://registry.npm.taobao.org
npm install grunt --save

修改elasticsearch-head下Gruntfile.js文件，默认监听在127.0.0.1下9200端口
connect: {
     server: {
          options: {
               hostname: '0.0.0.0',
               port: 9200,
               base: '.',
               keepalive: true
          }
     }
}
```

### 6、需要安装这些缺失的node modules, 注意需要回到elasticsearch_head目录下安装
```
npm install grunt-contrib-clean grunt-contrib-concat grunt-contrib-watch grunt-contrib-connect grunt-contrib-copy grunt-contrib-jasmine
```

### 7、运行grunt server
```
cd node_modules/grunt/bin
./grunt server
```