# 简介

将 **AI模型** 接入各类 **消息应用**，开发者通过轻量配置即可在二者之间选择一条连线，运行起一个智能对话机器人，在一个项目中轻松完成多条链路的切换。该架构扩展性强，每接入一个应用可复用已有的算法能力，同样每接入一个模型也可作用于所有应用之上。

**模型：**

 - [x] ChatGPT
 
**应用：**

 - [ ] 终端
 - [ ] Web
 - [x] 个人微信
 - [x] 公众号 (个人/企业)
 - [ ] 企业微信
 - [ ] Telegram
 - [ ] QQ
 - [ ] 钉钉 
 - [ ] 飞书

# 快速开始

## 一、准备

### 1.运行环境

支持 Linux、MacOS、Windows 系统（Linux服务器上可长期运行)。同时需安装 Python，建议Python版本在 3.7.1~3.10 之间。

项目代码克隆：

```bash
git clone https://github.com/zhayujie/bot-on-anything
cd bot-on-anything/
```
> 或在 Realase 直接手动下载源码。

### 2.配置说明

核心配置文件为 `config.json`，项目中提供了模板文件 `config-template.json` ，可以从模板复制生成最终生效的 `config.json` 文件：

```bash
cp config-template.json config.json
```

配置文件结构如下：

```bash
{
  "model": {
    "type" : "openai",              # 选用的算法模型
    "openai": {
      # openAI配置
    }
  },
  "channel": {
    "type": "wechat_mp",            # 需要接入的应用 
    "wechat": {
        # 个人微信配置
    },
    "wechat_mp": {
        # 公众号配置
    }
  }
}
```
配置文件在最外层分成 `model` 和 `channel` 两部分，model 部分为模型配置，其中的 `type` 指定了选用哪个模型；`channel` 部分包含了应用渠道的配置，`type` 字段指定了接入哪个应用，同时下方对应的配置块也会生效。

在使用时只需要更改 `model` 和 `channel` 配置块下的 `type` 字段，即可在任意模型和应用间完成切换，连接不同的通路。下面将依次介绍各个 模型 及 应用 的配置和运行过程。


## 二、选择模型

### 1.ChatGPT

#### 1.1 注册 OpenAI 账号

前往 [OpenAI注册页面](https://beta.openai.com/signup) 创建账号，参考这篇 [教程](https://www.cnblogs.com/damugua/p/16969508.html) 可以通过虚拟手机号来接收验证码。创建完账号则前往 [API管理页面](https://beta.openai.com/account/api-keys) 创建一个 API Key 并保存下来，后面需要在项目中配置这个key。

> 项目中使用的对话模型是 davinci，计费方式是约每 750 字 (包含请求和回复) 消耗 $0.02，图片生成是每张消耗 $0.016，账号创建有免费的 $18 额度，使用完可以更换邮箱重新注册。

#### 1.2 配置项说明

```bash
{
  "model": {
    "type" : "openai",
   
    "openai": {
      "api_key": "YOUR API KEY",
      "conversation_max_tokens": 1000,
      "character_desc": "你是ChatGPT, 一个由OpenAI训练的大型语言模型, 你旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。"
    }
}
```
+ `api_key`：填入上面注册账号时创建的 `OpenAI API KEY`
+ `conversation_max_tokens`：表示能够记忆的上下文最大字数（一问一答为一组对话，如果累积的对话字数超出限制，就会优先移除最早的一组对话）
+ `character_desc` 配置中保存着你对机器人说的一段话，他会记住这段话并作为他的设定，你可以为他定制任何人格      


## 三、运行应用

### 1.个人微信

与项目 [chatgpt-on-wechat](https://github.com/zhayujie/chatgpt-on-wechat) 的使用方式相同，目前接入个人微信可能导致账号被限制，暂时不建议使用。

配置项说明：

```bash
"channel": {
    "type": "wechat",
    
    "single_chat_prefix": ["bot", "@bot"],
    "single_chat_reply_prefix": "[bot] ",
    "group_chat_prefix": ["@bot"],
    "group_name_white_list": ["ChatGPT测试群"],
    "image_create_prefix": ["画", "看", "找一张"],
    
    "wechat": {
    }
}
```
个人微信的配置项放在和 `type` 同级的层次，表示这些为公共配置，会复用于其他应用。配置加载时会优先使用模块内的配置，如果未找到便使用公共配置。

在项目根目录下执行 `python3 app.py` 即可启动程序，用手机扫码后完成登录，使用详情参考 [chatgpt-on-wechat](https://github.com/zhayujie/chatgpt-on-wechat)。

### 2.个人订阅号

#### 2.1 依赖安装

安装 [werobot](https://github.com/offu/WeRoBot) 依赖：

```bash
pip3 install werobot
```

#### 2.2 配置

```bash
"channel": {
    "type": "wechat_mp",
        
    "wechat_mp": {
      "token": "YOUR TOKEN",           # token值
      "port": "8088"                   # 程序启动监听的端口
    }
}
```

#### 2.1 运行程序

在项目目录下运行 `python3 app.py`，终端显示如下则表示已成功运行：

```
[INFO][2023-02-16 01:39:53][app.py:12] - [INIT] load config: ...
[INFO][2023-02-16 01:39:53][wechat_mp_channel.py:25] - [WX_Public] Wechat Public account service start!
Bottle v0.12.23 server starting up (using AutoServer())...
Listening on http://127.0.0.1:8088/
Hit Ctrl-C to quit.
```

#### 2.2 设置公众号回调地址

在 [微信公众平台](https://mp.weixin.qq.com/) 中进入个人订阅号，启用服务器配置：

![wx_mp_config.png](docs/images/wx_mp_config.png)

- 服务器地址 (URL)：在浏览器访问该URL需要能访问到服务器上运行的python程序 (默认为8088端口)，智能填写 80/443端口，所以需要在服务器进行端口转发 (如使用nginx)，并将域名地址配置在url处 (仅用ip不行)
- 令牌 (Token)：需和配置中的token一致

#### 2.3 使用

用户关注订阅号后，发送消息即可。

> 注：用户发送消息后，微信后台会向配置的URL地址推送，但如果5s内未回复就会断开连接，同时重试3次，但往往请求openai接口不止5s。本项目中通过异步和缓存将5s超时限制优化至15s，但超出该时间仍无法正常回复。 同时每次5s连接断开时web框架会报错，待后续优化。
 

### 3.企业服务号

在企业服务号中，通过先异步访问openai接口，再通过客服接口主动推送用户的方式，解决了个人订阅号的15s超时问题。

企业服务号配置只需修改type为`wechat_mp_service`，配置块仍复用 `wechat_mp`，在基础上增加了 `app_id` 和 `app_secret` 两个配置项。

```bash
"channel": {
    "type": "wechat_mp_service",
        
    "wechat_mp": {
      "token": "YOUR TOKEN",            # token值
      "port": "8088",                   # 程序启动监听的端口
      "app_id": "YOUR APP ID",          # appID
      "app_secret": "YOUR APP SECRET"   # app secret
    }
}
```