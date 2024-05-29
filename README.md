# 说明
- author,2024-05-29,chenxizhan2635@163.com

钉钉企业内部应用快速上手程序。

项目地址：<https://github.com/u2635/h5app-corp-quickstart>\
Forked From: <https://github.com/open-dingtalk/h5app-corp-quickstart.git>

## 背景
现场项目遇到了钉钉H5微应用开发，了解原理。
官网文档里提到了这个快速上手项目。

但是 mvn clean package 打包时，前端子项目报错。做了些调整。

阅读钉钉开放平台这个文档 [学习地图 - 钉钉开放平台](https://open.dingtalk.com/document/orgapp/learning-map)，
了解基础概念后再启动此项目。（主要看应用类型、基础概念）。

PS：平台已经更新，没有H5、小程序的区分了，统一体现为“应用能力”。
参考链接 [【升级】关于应用开发平台的开发形态升级说明 - 钉钉开放平台](https://open.dingtalk.com/document/orgapp/application-development-platform?spm=a2q3p.21071111.0.0.66071jq31jq3Pb)

## 启动步骤
前提1：已经了创建了微应用。
参考链接：[如何创建微应用？](https://ding-doc.dingtalk.com/doc#/bgb96b/aw3h75)

前提2：本地有安装好 node 环境。node 16.x 没问题。

### 1. 下载项目
```sh
# 下载项目
git clone https://github.com/u2635/h5app-corp-quickstart
```
### 2. 配置凭证

配置后端：vscode 编辑器打开 `backend/src/main/resources/application.properties`，
填入企业微应用的 的APP_KEY，APP_SECRET, CORP_ID, AGENT_ID
```
dingtalk.app_key=APP_KEY
dingtalk.app_secret=APP_SECRET
dingtalk.agent_id=AGENT_ID
dingtalk.corp_id=CORP_ID
``` 
### 3. 编译
```sh
# 编译前端
cd h5app-corp-quickstart/frontend
yarn install
yarn build

# 编译后端
cd cd h5app-corp-quickstart/backend
mvn clean package
```

### 4. 启动
```sh
mvn -DskipTests=true spring-boot:run -pl backend 
```
默认监听 8080 端口。
### 5. 验证
访问 `http://localhost:8080/welcome/` 返回 welcome 说明启动成功。（curl或者浏览器都可以）。
```sh
$ curl http://localhost:8080/welcome/
welcome
```
### 6. 配置到微应用
需要把程序开放到公网上，才能配置给微应用。

方法有很多，仅作为开发学习使用，有个能 ssh 登录的公网云主机就可以搞定。借助 ssh 的端口转发功能。
```sh
ssh -R *:6980:localhost:8080 119.29.204.110
```

默认端口转发仅限localhost，调整 /etc/ssh/sshd_config  中 GatewayPorts 为 yes，然后重启sshd服务就可以了。
```sh
cat /etc/ssh/sshd_config | grep Gate
GatewayPorts yes

systemctl restart sshd.service
```

### 附：补充说明
需要先编译前端，再编译后端，后端编译过程中把前端编译结果（frontend/build/）当作资源文件复制到了自己项目资源里

## 调整

本来使用maven插件 frontend-maven-plugin 下载 node 和 yarn 到 target/node/，然后使用下载的node构建项目，
但是网路不通，又不熟悉，处理起来比较麻烦，恰好有现成的 node 环境，莫改一下。

## TODO
- [ ] 了解一下 maven 代理，不用魔改也编译成功。 【open,2024-05-29T16:26】
