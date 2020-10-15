# mac 下安装使用 mysql

## 安装 Navicat Premium

- Navicat Premium 是一套数据库开发管理工具，支持连接 MySQL、Oracle 等多种数据库，可以快速轻松地创建、管理和维护数据库
- 这是一个官方 Navicat Premium 15.0.14 MacOS 版中文安装包（2020.4 月更新）
- 其他版本的包不一定能测试通过

```bash
# 工具地址
# 链接: https://pan.baidu.com/s/1Kmch4m6e-dIcO-DL1jxFNA  密码: d6cc
```

## 安装

- 正常安装下载到的 dmg 包文件

```bash
# 切换到破解文件夹，通过下面的命令，替换公钥
./navicat-patcher /Applications/Navicat\ Premium.app/

# 生成一份自签名的代码证书，并总是信任该证书。这一步非常重要
# 打开 “钥匙串访问”，选择菜单栏的 钥匙串访问->证书助理->创建证书，然后证书类型选择 “代码签名”。
# 名称 -- Navicat（自定义），一会要使用得到
# 然后在证书上右键选择“显示简介”，在“使用证书”那里选择始终信任。

# 用 codesign 对 libcc-premium.dylib （如果有的话） 和 Navicat Premium.app 重签名。
sudo codesign -f -s Navicat /Applications/Navicat\ Premium.app/Contents/Frameworks/libcc-premium.dylib
sudo codesign -f -s Navicat /Applications/Navicat\ Premium.app/

# 接下来使用 navicat-keygen 来生成 序列号 和 激活码。
./navicat-keygen ./RegPrivateKey.pem
# 依次选择 1 15 ，注意 不要关闭注册机
# 填写姓名和组织，自定义，激活码需要打开软件

# 断开网络 并打开 Navicat
# 打开软件会自动识别证书文件，选择确定
# 打开软件的激活页面
# 一般来说在线激活肯定会失败，这时候Navicat会询问你是否手动激活
# 在手动激活窗口你会得到一个请求码，复制它并把它粘贴到keygen里。最后别忘了连按至少两下回车结束输入。
# 如果不出意外，你会得到一个看似用Base64编码的激活码。
# 直接复制它，并把它粘贴到Navicat的手动激活窗口，最后点激活按钮。
# 如果没什么意外的话应该能成功激活。
```

## mysql 安装

```bash
# 安装
brew install mysql@5.7

# 链接
brew link --force mysql@5.7

# 启动mysql
brew services start mysql@5.7

# 输出到环境变量
echo 'export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"' >> ~/.zshrc
```

## 卸载 mysql

- 如果之前安装了 mysql5.7 首先要卸载

```bash
brew uninstall mysql@5.7
rm -rf /usr/local/var/mysql
rm /usr/local/etc/my.cnf
```

## 安全设置 mysql

```bash
# 设置安全的访问
mysql_secure_installation
```

## 操作 mysql

```bash
# 登录mysql
mysql -u root -p

# 创建数据库
create database dati_db character set utf8mb4;

# 创建用户
create user 'retail_u'@'%' identified by 'retail_PWD_123';

# 授权用户
grant all privileges on retail_db.* to 'retail_u'@'%';

# 激活设置
flush privileges;
```

### 数据库

```bash
# 查看当前数据库
show databases;

# 显示当前数据库的表
show tables
```

### 数据库操作

```bash
CREATE TABLE t_user(
  key_id VARCHAR(255) NOT NULL PRIMARY KEY,  -- id 统一命名为key_id
  user_name VARCHAR(255) NOT NULL ,
  password VARCHAR(255) NOT NULL ,
  phone VARCHAR(255) NOT NULL,
  deleted INT NOT NULL DEFAULT 0,  -- 逻辑删除标志默认值
  create_time   timestamp NULL default CURRENT_TIMESTAMP, -- 创建时间默认值
  update_time   timestamp NULL default CURRENT_TIMESTAMP -- 修改时间默认值
) ENGINE=INNODB AUTO_INCREMENT=1000 DEFAULT CHARSET=utf8mb4;
```

### 检查 mysql 服务状态

- 先退出 mysql 命令行，输入命令

```bash
systemctl status mysql.service
```
