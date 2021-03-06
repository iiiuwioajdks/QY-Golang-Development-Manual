# 青鸢工作室Golang开发手册

## 一.文档

**文档前后端统一使用 yapi，以下是yapi的docker安装**

### 1、启动 [MongoDB](https://cloud.tencent.com/product/mongodb?from=10680)

```javascript
docker run -d --name mongo-yapi mongo
```

### 2、获取 Yapi 镜像，版本信息可在 阿里云镜像仓库 查看

```javascript
docker pull registry.cn-hangzhou.aliyuncs.com/anoy/yapi
```

### 3、初始化 Yapi [数据库](https://cloud.tencent.com/solution/database?from=10680)索引及管理员账号

```javascript
docker run -it --rm \
  --link mongo-yapi:mongo \
  --entrypoint npm \
  --workdir /api/vendors \
  registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
  run install-server

```

### 4、启动 Yapi 服务

```javascript
docker run -d \
  --name yapi \
  --link mongo-yapi:mongo \
  --workdir /api/vendors \
  -p 3000:3000 \
  registry.cn-hangzhou.aliyuncs.com/anoy/yapi \
  server/app.js

```

### 5、使用 Yapi

到服务器开启 3000 端口，进行访问

```javascript
访问 http://localhost:3000   登录账号 admin@admin.com，密码 ymfe.org
```

### 6.文档规范

首先再个人中心右边按钮添加分组，**分组名和组长用户名必填**

然后点进添加的分组，在右上方点击**添加项目**，**项目名称为本次项目的名字**，**所属分组不动**，基本路径如果确定了就填

执行完上述步骤会自动进入项目，**然后点击添加分类，根据接口的各自属性进行分类**，方便前后端查看即可

点击最上方的设置，将 **mock严格模式，开启json5** 两个按钮**打开**，**将权限改为公开**

测试的时候，在每个分类右边的＋号新建接口，填入url和路径，然后确定**点击编辑**进入设置页面调整各种参数。

注意：**请求方法如果是post的话，请求参数设置统一使用json格式**

**请求方法如果是get的话，统一使用restful风格的query**



**Post数据设置：**

打开 JSON-SCHEMA，点击导入json，填入拟定数据格式

**然后设置是否必需，数据格式和是否mock数据，备注必须指明参数的意义**

最后点击保存即可到运行中测试



**token设置：**

测试的时候将token的expire设置长一点，通过登录获得一个通用token，然后在yapi中点击设置，进入环境配置，在header里面配置token，就不用每次都输入token了。



文档的测试写的这么详细主要是因为要跟前端对接，你把url用户名密码给前端，前端就能根据你测试的数据进行开发，这样就能省去写文档的功夫，毕竟go写文档挺痛苦的。

## 二.开发环境搭建(Docker)

### Docker安装

#### 安装依赖包

```powershell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2 
1
```

#### 设置阿里云镜像源

```powershell
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
1
```

#### 安装 Docker-CE

```powershell
sudo yum install docker-ce
```

#### 启动docker

```powershell
# 开机自启
sudo systemctl enable docker 
# 启动docker服务  
sudo systemctl start docker
```

### 配置加速

```bash
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
EOF

# 重启docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```



### 常用容器搭建

- 工作室的mysql暂时统一使用8.0.21版本

```bash
#mysql
docker run --name mysql容器的名字 -v /root/mysql/data:/var/lib/mysql -v /root/mysql/conf.d:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=root -p 3307:3306 -d mysql:8.0.21
```

- 工作室的redis暂时统一使用5.0.10版本

```bash
#redis
docker run --name redis容器的名字 -v /root/redis/data:/data -v /root/redis/redis.conf:/usr/local/etc/redis/redis.conf -p 6379:6379 -d redis:5 redis-server /usr/local/etc/redis/redis.conf --requirepass "qyniubi"
```

- nacos

```bash
#nacos
docker run --name nacos-standalone -e MODE=standalone -e JVM_XMS=512m -e JVM_XMX=512m -e JVM_XMN=256m -p 8848:8848 -d nacos/nacos-server:latest

// url
http://ip:8848/nacos
// account and password
nacos
nacos
```



## 三.单体应用开发脚手架

[iiiuwioajdks/QY-golang-framework: 青鸢后端golang开发脚手架 (github.com)](https://github.com/iiiuwioajdks/QY-golang-framework)

