## 区块链浏览器搭建

### 〇、参考
* GitHub项目 [https://github.com/iquidus/explorer](https://github.com/iquidus/explorer)
* 浏览器示例网址 [https://sphr.network](https://sphr.network)
* 淘宝cnpm安装 [https://blog.csdn.net/quuqu/article/details/64121812](https://blog.csdn.net/quuqu/article/details/64121812)
* Ubuntu添加新用户 [https://blog.csdn.net/acelove40/article/details/54343629](https://blog.csdn.net/acelove40/article/details/54343629)
* Ubuntu安装MongoDB [https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)
* 守护进程npm start [https://segmentfault.com/q/1010000004455598](https://segmentfault.com/q/1010000004455598)
* crontab基础使用 [https://www.jianshu.com/p/6f91a1350b9b](https://www.jianshu.com/p/6f91a1350b9b)
* rpc命令搭建blockchain-explorer [https://blog.csdn.net/jqq53016353/article/details/77394627](https://blog.csdn.net/jqq53016353/article/details/77394627)
* 【报错解决】npm找不到gssapi/gssapi.h [https://www.jianshu.com/p/793cebbbf87e](https://www.jianshu.com/p/793cebbbf87e)

### 壹、资源准备与环境要求

Name | Version
---|---
System | Ubuntu(Server) 16.04
MongoDB | v3.6
NodeJS | v8.9.4
Npm | v5.6.x
*coind	| null

### 贰、为Ubuntu配置普通用户
1. Create User: `sudo adduser [pink]`.
2. Enter Password Two Times.
3. Grant **sudo** Privilege: `sudo usermod -a -G adm [pink]` `sudo usermod -a -G sudo  [pink]`.
4. Change User: `su [pink]`.

### 叁、安装NodeJS
***0. Download Archive***

访问nodejs官网的下载页面 https://nodejs.org/en/download/ ， 找到指定的系统的指定版本，右击复制连接，terminal输入：
```bash
> wget https://nodejs.org/dist/v8.9.4/node-v8.9.4-linux-x64.tar.xz
```

P.S. 如果提示没有wget 请用`apt-get`或者`yum`自行安装


***1. Unzip Archive***

由于这个压缩包是tar.xz格式的,解压命令如下：
```bash
> tar xvJf node-v8.9.4-linux-x64.tar.xz
```
P.S. 如果提示没有tar 请用`apt-get`或者`yum`自行安装

***2. Configure environment variables***
```bash
# 转移文件夹
> mv node-v8.9.4-linux-x64 node
> mv node /usr/local

# 测试node
> cd /usr/local/node/bin
> ./node -v
> ./npm -v

# 配置环境变量
> vim ~/.bashrc
# 添加

export NODE_PATH="/usr/local/node/lib/node_modules"
PATH=$PATH:/usr/local/node/bin/:~/npm-global/bin
export PATH

> source ~/.bashrc

# Recheck
> node -v
> npm -v
```


### 肆、安装MongoDB
***0.Import the public key used by the package management system.***
```
> sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
```

***1.Create a list file for MongoDB.***

Ubuntu 16.04
```
> echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
```

***2.Reload local package database.***
```
> sudo apt-get update
```

***3.Install the MongoDB packages.***
```
> sudo apt-get install -y mongodb-org
> sudo apt-get install -y mongodb-org=3.6.5 mongodb-org-server=3.6.5 mongodb-org-shell=3.6.5 mongodb-org-mongos=3.6.5 mongodb-org-tools=3.6.5

# then enter
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-org-shell hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```

***4.Start MongoDB.***
```
> sudo service mongod start
```

***5.Check MongoDB Status.***
```
> less /var/log/mongodb/mongod.log

#result 如果显示这个就代表MongoDB已经正常启动了
[initandlisten] waiting for connections on port 27017
```

***6.Stop & Restart MongoDB.***
```
# stop
> sudo service mongod stop

# restart 
> sudo service mongod restart

```

***Ot.Remove MongoDB.***

*如果想卸载MongoDB，请谨慎操作*
1. Stop MongoDB. `sudo service mongod stop`
2. Remove Packages. `sudo apt-get purge mongodb-org*`
3. Remove Data Directories. `sudo rm -r /var/log/mongodb` `sudo rm -r /var/lib/mongodb`


### 伍、创建MongoDB数据库
1. Enter MongoDB cli: `mongo`
2. Create databse: `use explorerdb`
3. Create user with read/write access: `db.createUser( { user: "iquidus", pwd: "3xp!0reR", roles: [ "readWrite" ] } )`


### 陆、调整`*coind*`
1. Get ProcessId: `lsof -i :xxxx`
2. Kill ProcessId: `kill -9 [id]`
3. Vim `~/.*coin/*coin.conf`
```
addnode=***.***.***.***
rpcconnect=127.0.0.1
rpcport=****
server=1
gen=1
rpcuser=****
rpcpassword=****
```
4. Restart *coind: `./*coind -reindex -txindex`
5. Test
```
> ./*coind getblockhash [**block Id**]
> ./*coind getblock [**block hash**]
> ./*coind getrawtransaction [**txId**]
> ./*coind decoderawtransaction [**raw tx Id**]
```


### 柒、安装
0. Make workspace: `mkdir ~/workspace && cd ~/workspace`.
1. Download from Github: `git clone https://github.com/iquidus/explorer explorer`.
2. Install cnpm(taobao): `npm install -g cnpm --registry=https://registry.npm.taobao.org`
3. Install modules: `cd explorer && cnpm install --production`
4. Configure: `cp ./settings.json.template ./settings.json`
5. Modify settings.json
```
{
  "title": "Your-Mainnet",

  "address": "127.0.0.1:3001",

  // coin name
  "coin": "***",

  // coin symbol
  "symbol": "***",

  // logo
  "logo": "/images/logo.png",

  // favicon
  "favicon": "public/favicon.ico",

  // Uses bootswatch themes (http://bootswatch.com/)
  // Valid options:
  //     Cerulean, Cosmo, Cyborg, Darkly, Flatly, Journal, Lumen, Paper,
  //     Readable, Sandstone, Simplex, Slate, Spacelab, Superhero, United, Yeti
  // theme (see /public/themes for available themes)
  "theme": "Cyborg",

  // port to listen for requests on.
  "port" : 3001,

  // database settings (MongoDB)
  "dbsettings": {
    "user": "iquidus",
    "password": "3xp!0reR",
    "database": "explorerdb",
    "address": "localhost",
    "port": 27017
  },

  //update script settings
  "update_timeout": 10,
  "check_timeout": 250,

  // wallet settings
  "wallet": {
    "host": "localhost",
    "port": ****,
    "user": “****”,
    "pass": “****”
  },

  // confirmations
  "confirmations": **,

  // language settings
  "locale": "locale/en.json",

  // menu settings
  "display": {
    "api": true,
    "markets": false,
    "richlist": false,
    "twitter": false,
    "facebook": false,
    "googleplus": false,
    "search": true,
    "movement": true,
    "network": true
  },

  // index page (valid options for difficulty are POW, POS or Hybrid)
  "index": {
    "show_hashrate": true,
    "difficulty": "POW",
    "last_txs": 100
  },

  // ensure links on API page are valid
  "api": {
    "blockindex": 1337,
    "blockhash": "00000f0f9f2fdaa2b1afe45a496d***a7c5ba7736491515a04f5300ad22ea670",
    "txhash": "97a1bc41fdbfe459ebdbe7e39d3aa004d0e0939778019***158c9eff3334b0b0",
    "address": "jNHgLqyyK9bgRFrhn2***Azf9g4Wkh8mXR"
  },

  // market settings
  //supported markets: bittrex, poloniex, yobit, empoex, bleutrade, cryptopia, ccex
  //default market is loaded by default and determines last price in header
  "markets": {
    "coin": "JBS",
    "exchange": "BTC",
    "enabled": ["bittrex"],
    "cryptopia_id": "1658",
    "ccex_key" : "Get-Your-Own-Key",
    "default": "bittrex"
  },

  // richlist/top100 settings
  "richlist": {
    "distribution": true,
    "received": true,
    "balance": true
  },
  // movement page settings
  // min amount: show transactions greater than this value
  // low flag: greater than this value flagged yellow
  // high flag: greater than this value flagged red
  "movement": {
    "min_amount": 100,
    "low_flag": 1000,
    "high_flag": 5000
  },

  // twitter, facebook, googleplus
  "twitter": "iquidus",
  "facebook": "yourfacebookpage",
  "googleplus": "yourgooglepluspage",

  //genesis
  "genesis_tx": "f3059ade905ef43303d8217b92faade6e***ca7bb0aa5a31e4080eb7b465bae8",
  "genesis_block": "00000d025c7b1f9e3***36f65d971a81687ca8af60c4ee60c4251c9e0de15572",

  //heavy (enable/disable additional heavy features)
  "heavy": false,

  //amount of txs to index per address (stores latest n txs)
  "txcount": 100000,

  //show total sent & received on address page (set false if PoS)
  "show_sent_received": true,

  // how to calculate current coin supply
  // COINBASE : total sent from coinbase (PoW)
  // GETINFO : retreive from getinfo api call (PoS)
  // HEAVY: retreive from heavys getsupply api call
  // BALANCES : total of all address balances
  // TXOUTSET : retreive from gettxoutsetinfo api call
  "supply": "COINBASE",

  // how to acquire network hashrate
  // getnetworkhashps: uses getnetworkhashps api call, returns in GH/s
  // netmhashps: uses getmininginfo.netmhashpsm returns in MH/s
  "nethash": "getnetworkhashps",

  // nethash unitd: sets nethash API return units
  // valid options: "P" (PH/s), "T" (TH/s), "G" (GH/s), "M" (MH/s), "K" (KH/s)
  "nethash_units": "G",

  // Address labels
  // example : "JhbrvAmM7kNpwA6wD5KoAsbtikLWWMNPcM": {"label": "This is a burn address", "type":"danger", "url":"http://example.com"}
  // label (required) = test to display
  // type (optional) = class of label, valid types: default, primary, warning, danger, success
  // url (optional) = url to link to for more information
  "labels": {
  //  "JSoEdU717hvz8KQVq2HfcqV9A79Wihzusu": {"label": "Developers address", "type":"primary", "url":"http://example.com"},
  //  "JSWVXHWeYNknPdG9uDrcBoZHztKMFCsndw": {"label": "Cryptsy"}
  }
}
```


P.S. 报错 & 解决方案
* npm找不到gssapi/gssapi.h: `$ sudo apt-get install libkrb5-dev`
* script is already running: `rm tmp/index.pid`
* Port using: Get ProcessId `lsof -i :3001`, then kill Pid `kill -9 [Pid]`

### 捌、运行explorer
1. 普通运行(当terminal断开时终止): `npm start`
2. 区块同步(当前处于项目根目录): `node scripts/sync.js index update`
3. 进程守护运行npm: `nohup npm start &`

### 玖、使用cronjob实现定时更新区块
1. 添加cron任务: `crontab -e`
2. 选择`GNU nano 2.5.3`
3. 输入`*/1 * * * * cd /home/pink/workspace/explorer && /usr/bin/nodejs scripts/sync.js index update > /dev/null 2>&1`
4. 键入`Ctrl+x`-`Y`-`Enter`，保存
5. 查看cron任务: `crontab -l`

### 拾、查看和使用区块链浏览器
浏览器输入`http://[your ip]:3001`
