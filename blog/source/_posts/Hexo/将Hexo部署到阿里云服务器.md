---
title: 将Hexo部署到阿里云ECS服务器
description: Hexo学习心得
date: 2022-12-23 07:11:00
updated: 
tags:
  - Hexo
categories:
  - Hexo
cover: https://yimoorua-img.oss-cn-chengdu.aliyuncs.com/images/AgAAC6u-TVOZz2AAaN5Gl77-CtWr2-Tr.png
abbrlink: 
feature: true
---
# 将Hexo部署到阿里云ECS服务器
## 1.准备工作
 在开始部署前，需要准备如下：
   1.本地搭建完成的Hexo博客
   2.已购买的一台ECS服务器
  3.已拥有SSL证书（可以申请免费证书）
   4.`X-shell`连接远程服务器的工具（Mac系统可以用`ProtX`）
## 2.安装配置git
### 2.1安装依赖工具包
```bash
yum install -y curl-devel expat-devel gettext-devel openssl-devel zlib-devel 
```
### 2.2安装编译工具
```bash
yum install -y gcc perl-ExtUtils-MakeMaker package
```
### 2.3删除旧版git
```bash
yum remove git -y
```
### 2.4进入根目录
```bash
cd 
```
### 2.5下载git到根目录
鉴于下载地址的时效性，附上[官网地址](https://git-scm.com/download/linux),后续git更新请去官网下载安装包。
```bash
wget https://www.kernel.org/pub/software/scm/git/git-2.34.0.tar.gz --no-check-certificate
```
如果提示没有wget命令，先安装：
```bash
yum install wget -y
```
### 2.6解压git文件
```bash
tar -zxf git-2.34.0.tar.gz
```
### 2.7安装git
进入git目录下
```bash
cd git-2.34.0
```
编译
```bash
make all prefix=/usr/local/git
```
安装git到`/usr/local/git`下
```bash
make install prefix=/usr/local/git
```
### 2.8配置git环境变量
将git加入`PATH`目录中
```bash
echo 'export PATH=$PATH:/usr/local/git/bin' >> /etc/bashrc
```
让配置生效
```bash
source /etc/bashrc
```
查看git是否安装成功
```bash
git --version
```
## 3.创建用户并配置SSH免密登录
### 3.1创建用户并设置密码
这里的`username`改成你自己的用户名，并且为用户设置一个密码。
```bash
adduser [username]
passwd [username]
```
### 3.2为用户分配权限
把用户添加到wheel组
```bash
usermod [username] -G wheel
```
### 3.3本地创建密钥
在本机电脑终端输入：
```bash
ssh-keygen -t rsa
```
![终端截图](https://yimoorua-img.oss-cn-chengdu.aliyuncs.com/md-imgs/63a4e8e708b68301638cbe24.png)
然后一直回车(不用输入)，新建一个密钥。

一般来说在你创建的时候命令行会提示你密钥文件的路径，windows电脑一般是C:\Users\Administrator\.ssh

复制id_rsa.pub文件中的内容备用。
### 3.4配置公钥
回到服务器终端，切换用户
```bash
su - [username]
```
创建`.ssh`文件夹
```bash
mkdir .ssh
```
新建`authorized_keys`
```bash
vim .ssh/authorized_keys
```
如果提示没有vim，则先安装：
```bash
sudo yum install -y vim
```
将刚刚`id_rsa.pub`公钥中的内容，复制粘贴到文件里，保存退出。
## 4.git仓库配置
退出用户登录
```bash
exit
```
此时应该是root用户，创建git目录，并修改目录的所有权和用户权限
```bash
mkdir /home/git/
chown -R [username]:[username] /home/git/
chmod -R 755 /home/git/
```
进入目录，建立git仓库，修改权限
```bash
cd /home/git/
git init --bare blog.git
chown [username]:[username] -R blog.git
```
新建钩子文件`post-receive`
```bash
vim /home/git/blog.git/hooks/post-receive
```
进入文本编辑器，粘贴下面两行。
```bash
#!/bin/bash
git --work-tree=/home/blog --git-dir=/home/git/blog.git checkout -f
```
保存退出。然后修改文件权限
```bash
chmod +x /home/git/blog.git/hooks/post-receive
```
## 5.nginx配置
首先创建blog目录用户nginx转发
```bash
mkdir /home/blog/
chown -R [username]:[username] /home/blog/
chmod -R 755 /home/blog/
```
### 5.1安装nginx
yum安装nginx（可以使用安装包安装，这里就不写了）
```bash
yum install -y nginx
```
启动nginx
```bash
systemctl start nginx.service
```
查看nginx服务状态，绿色的active说明启动成功
```bash
systemctl status nginx.service
```
设置nginx开机启动
```bash
systemctl enable nginx.service
```
重启服务器
```bash
reboot
```
重启后，查看nginx服务状态，绿色的active说明启动成功
```bash
systemctl status nginx.service
```
### 5.2下载SSL证书
打开阿里云控制台SSL证书界面，点击下载按钮
![SSL证书界面](https://yimoorua-img.oss-cn-chengdu.aliyuncs.com/md-imgs/63a4ef4208b683016392fa62.png)
选择Nginx版本
![Nginx版本](https://yimoorua-img.oss-cn-chengdu.aliyuncs.com/md-imgs/63a4ef4208b683016392fa5c.png)
下载得到`.key`和`.pem`两个文件
在Nginx文件夹下创建cert文件夹

```bash
mkdir cert
```
通过X-shell将文件传入cert文件下
![cert文件夹](https://yimoorua-img.oss-cn-chengdu.aliyuncs.com/md-imgs/63a4ef4208b683016392fa54.png)

### 5.3修改nginx转发配置（包含https）
在浏览器中输入服务器公网ip地址，测试是否能打开。
找到nginx的conf配置文件，我的是在`/etc/nginx/nginx.conf`
修改配置文件
```bash
vim /etc/nginx/nginx.conf
```
找到server，修改如下
```txt
server {
          listen       443 ssl http2;
          listen       [::]:443 ssl http2;
          #域名
          server_name  xxxxxx;
          #博客根目录
          root         /home/blog;

          index index.html index.htm;
          #pem文件地址
          ssl_certificate /etc/nginx/cert/xxxxxxxxxxxx.pem;
          #key文件地址
          ssl_certificate_key /etc/nginx/cert/xxxxxxxxxxxx.key;
          ssl_session_cache shared:SSL:1m;
          ssl_session_timeout  10m;
          #加密方式
          ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
          ssl_prefer_server_ciphers on;
      
          # Load configuration files for the default server block.
          include /etc/nginx/default.d/*.conf;
     
          error_page 404 /404.html;
              location = /40x.html {
          }
      
          error_page 500 502 503 504 /50x.html;
              location = /50x.html {
          }
      }
    #将请求转成https
      server {
          listen 80;
          #域名
          server_name xxxxxxx;
          rewrite ^(.*)$ https://$host$1 permanent;
       }
```
重启nginx
```bash
systemctl restart nginx.service
```
查看nginx状态，绿色表示成功！
```bash
systemctl status nginx.service
```
## 6.在hexo站点配置文件中配置
打开本地博客，找到_config.yml文件并打开，修改如下配置
```yml
deploy:
  type: git
  repository: [username]@100.0.0.100:/home/git/blog.git   #用户名@服务器Ip:git仓库位置
  branch: master
```
保存后，输入如下命令将本地博客部署到云服务器上
```bash
hexo clean
hexo g
hexo d
```
