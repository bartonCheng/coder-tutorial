# mac 下安装使用 mysql

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
