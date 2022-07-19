# MusicBox
暑期实训项目——宝藏音乐社交盒
# 宝藏音乐社交盒

#### 介绍

暑期实训项目 一款主打社交的宝藏音乐微信小程序

#### 软件架构

小程序架构说明

前端技术栈：uni-app（Vue）、Sass（CSS）、JavaScript

后端技术栈：SpringBoot、Mybatis

缓存中间件：Redis

数据库：MySQL

方服务：网易云API，阿里云对象存储服务


#### 开发环境

前端：HbuilderX，微信开发者工具

后端：IntelliJ IDEA


#### 使用说明

##### 后端：

1. 创建数据库，运行根目录下的 live.sql，并修改 artist 表中对应的微信 APPID 等设置
2. 使用 IDEA 导入 LiveBackEnd目录下的 live-backend-mp 项目，并通过 Modules 的方式导入 live-backend-common 项目
3. 修改 application-dev 中的 MySQL ，Redis，阿里云 OSS 配置

##### 前端：

1. 使用 HbuilderX 导入项目
2. 修改 manifest.json 中的设置（微信小程序的APPID）
3. 修改 App.vue 中的domain配置（修改为后端项目的路径）
4. 在项目根目录运行 npm install
5. 然后点击上方工具栏运行-运行到小程序模拟器-微信开发者工具（如第一次使用需在设置中绑定微信开发者工具路径），待编译完成后项目即可运行。
