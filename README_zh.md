# CLaude-in-slack 网页端接口
> 最近发现claude-in-slack不再提供加入到slack里，去官网看了一下原因貌似是太多需求  
> 现在发出来这个脚本是不是有些太晚了

这个项目仅以学习为目的，如果有使用claude的需求可以直接使用slack_sdk连接claude app

## 项目结构
图片

## 第一次登录
图片

## 保存登录状态
每次登陆成功之后，cookies、api-token、user_id、team_id这些信息会被保存在json文件中，文件名为邮箱名
使用 `checkEnv()` 方法保存登录状态，使用 `getEnv()` 保存加载。

## 使用方法
具体请查看这个文件:  
[Claude in command line](server/main_cmd.py)

另外一个web server的版本:  
[Claude in FastAPI](server/main_fastapi.py)


## 聊天

### 创建ClaudeApp
在使用 `createApp()` 创建ClaudeApp之后会创建一个WebSocketServer并返回其url和还未连接至slack的ClaudeApp本身

### 登录或加载登录状态
暂时只支持使用邮箱验证码登录，密码登录功能可能会在以后的某一天加上

对邮箱登录的来说，所有流程都在XOXD.py中
```python
xoxd.Login_init()
xoxd.Login_sendCode(code)

teamlist = xoxd.teamlist
option = teamlist[index] # choose a team

xoxd.Login_selTeam(option)
xoxd.Login_getDAndSave()
```
登陆成功之后，登录状态会被保存

### 自动查找channel并连接wss
使用创建好的ClaudeApp中的 `connect()`, 这个方法会自动匹配有claude的channel然后连接slack提供的wss连接 

### 对话
使用 `postMessage` 发送信息，每条信息会从wss连接中返回，并通过先前创建好的WebSocketServer向所有已连接的web端转发信息

## 执行命令
现在的claude只有一个命令，清空对话记忆，但是我设置了一个通用的以防止它以后新增加别的命令

通过调用ClaudeApp下面的 `execCmd()` 方法来执行命令，例如reset，清除记忆的通知会在wss中接收

## 历史对话记录
接口找到了，但是还没做解析，没想好该怎么写这个逻辑，它是按时间戳和返回对话条数来的，在给定时间戳之前的最大几条数据会被返回，我设置的是30条

如果有需求可以自行尝试解析，方法是ClaudeApp里的 `getHistory()`