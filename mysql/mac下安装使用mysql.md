# mac 下安装使用 mysql

## mysql 安装

```bash
# 安装
brew install mysql@5.7

# 启动mysql
brew services start mysql@5.7

# 执行安全设置-设置密码
mysql_secure_installation

```

## 操作mysql

```bash
# 登录mysql
mysql -u root -p 

# 创建数据库
create database dati_db character set utf8mb4;
```
