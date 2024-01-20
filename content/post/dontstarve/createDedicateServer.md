---
title: "饥荒联机版 - 搭建专用服务器"
date: 2023-12-29T09:43:05+08:00
draft: false
math: true

tags: ["饥荒联机版", "专用服务器"]
categories: ""
description: ""
---

### 1、下载专用服务器启动器

打开 Steam，依次点击 **库 -> 主页**，勾选 **工具**，找到 **Don't Strave Together Dedicated Server**，点击安装，在安装过程中，可以进行下一步。

### 2、创建一个世界

进入饥荒联机版，创建一个世界，修改世界设置，勾选好自己想用的模组，点击创建。创建完成后，不需要进入世界，出现选人界面后就可以退出存档了。

在世界列表中，找到刚才创建的世界，点击管理，找到存档在文件夹中的位置（存档名字为 `Cluster_ + 数字`），将整个文件夹复制到上一层文件夹中，可以把复制后的文件夹换一个名字，方便记。（后面我们把这个存档称为联机存档）

### 3、创建联机服务器

点击 [科雷的服务器创建页面](https://accounts.klei.com/account/game/servers?game=DontStarveTogether)，创建一个服务器用于启动刚才创建的世界。复制创建好的票据，等会用到。

在联机存档根目录创建一个 `cluster_token.txt`，将刚才的票据复制进去，保存退出。

### 4、添加模组

创建世界用到的模组需要添加到专用服务器才可以使用，添加一次即可，后面创建的世界可以直接用。（如果没有添加模组，这一步可以跳过）

找到 `Don't Strave Together Dedicated Server` 的安装目录（可以在 steam 中右键点击属性），在 `mods` 文件夹下找到 `dedicated_server_mods_setup.lua` 文件，这个文件是管理模组安装的。

配置分为两种：一是单个模组安装，二是模组合集安装。

单个模组安装：在创意工坊找到需要的模组的界面，界面链接最后 id 后面的数字就是模组的 id，粘贴到 `ServerModSetup("378160973")` 中。

模组合集安装：将订阅合集的 id 粘贴到 `ServerModCollectionSetup("379114180")` 中，跟上面的类似。

```lua
-- Mod Subscribe --
-- [Global Positions](https://steamcommunity.com/sharedfiles/filedetails/?id=378160973)
ServerModSetup("378160973")

-- [Lazy Furnace](https://steamcommunity.com/sharedfiles/filedetails/?id=875994715)
ServerModSetup("875994715")

-- [Show Me](https://steamcommunity.com/sharedfiles/filedetails/?id=2287303119)
ServerModSetup("2287303119")

-- [Epic HealthBar](https://steamcommunity.com/sharedfiles/filedetails/?id=1185229307)
ServerModSetup("1185229307")

-- [Warmhole Marks](https://steamcommunity.com/sharedfiles/filedetails/?id=362175979)
ServerModSetup("362175979")

-- [从骨头复活](https://steamcommunity.com/sharedfiles/filedetails/?id=1271089343)
ServerModSetup("1271089343")

-- [防卡两招](https://steamcommunity.com/sharedfiles/filedetails/?id=1216718131)
ServerModSetup("1216718131")

-- [Drop & Stack](https://steamcommunity.com/sharedfiles/filedetails/?id=1998081438)
ServerModSetup("1998081438")

-- [Simple Health Bar](https://steamcommunity.com/sharedfiles/filedetails/?id=1207269058)
ServerModSetup("1207269058")

-- [Infinite Tent Uses](https://steamcommunity.com/sharedfiles/filedetails/?id=356930882)
ServerModSetup("356930882")

-- [Tips show attack time](https://steamcommunity.com/sharedfiles/filedetails/?id=1898292532)
ServerModSetup("1898292532")

-- [Infinite Thremal Stone](https://steamcommunity.com/sharedfiles/filedetails/?id=466732225)
ServerModSetup("466732225")
```

### 5、配置启动程序

在 `Don't Strave Together Dedicated Server` 的安装目录下，进入 `bin64` 文件夹，创建一个 `launch_dedicated_server.bat` 文件，打开文件，粘贴如下内容。（文件名字随意，创建完毕后可以在桌面创建快捷方式，方便启动）

`-cluster` 后面是联机存档的名字，需要改成自己的。

```bat
@ECHO OFF

set SteamAppId=322330
set SteamGameId=322330

Start "DST Master" dontstarve_dedicated_server_nullrenderer_x64.exe -console -cluster 联机存档名字 -shard Master

Start "DST Caves" dontstarve_dedicated_server_nullrenderer_x64.exe -console -cluster 联机存档名字 -shard Caves
```

### 6、饥荒！启动！

双击第 5 步创建的脚本程序，会出现两个黑框，分别是地上和地下的服务器。如果有模组，第一次启动会比较慢，因为要下载模组。

等待一段时间（没模组大概一分钟内），`Caves` 服务器出现 `Sim Pause` 后，就可以进入饥荒联机版。点击 `浏览游戏`，在搜索框中搜索你创建世界的名字，就可以找到自己创建的联机世界了。

### 7、关闭服务器

如果不关闭服务器，是无法进入正常创建的世界的，直接点叉不太优雅，并且服务器不会对当前游戏状态进行存档。分别在两个黑框中输入 `c_shutdown()` ，然后回车，就可以关闭服务了。