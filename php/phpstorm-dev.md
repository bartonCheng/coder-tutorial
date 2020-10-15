## phpstorm 开发调试指南

- 本教程适用于 jetbrains 全系列产品

## 安装 【mac win 通用】

- [下载 2020.2 版本](https://www.jetbrains.com/phpstorm/download/other.html)
- [破解补丁]()

### 修改验证 host

```bash
sudo vim /etc/hosts

# 添加以下内容
0.0.0.0 account.jetbrains.com
0.0.0.0 www.jetbrains.com
0.0.0.0 www-weighted.jetbrains.com

# host文件
# WIN系统：C:\Windows\System32\drivers\etc
# MAC OS系统：/etc/hosts
```

### 激活 IDE

```bash
# 先将 jetbrains-agent-latest.zip 文件拖拽到 IDE 界面中
# 重启后，在弹出界面中填入安装参数，参数从上面的 安装参数.txt 文件中获取
```

## Mac 下设置

- [xdebug 智能分析依赖](https://xdebug.org/wizard)
- 使用 Pecl 进行安装

### 安装 ThinkPHP

```bash
# 最新版本为40
composer create-project topthink/think=5.1.40 tp5
```

### 安装 php7.2

```bash
# 安装的php 带有pecl插件
brew install php@7.2
brew reinstall php@7.2
# 记录 php.ini 位置
# /usr/local/etc/php/7.2/

# 卸载
brew uninstall php@7.2

# 激活php7.2
brew services start php@7.2

# 测试pecl
pecl -V

# 通过pecl安装xdebug
pecl install xdebug

# 卸载
pecl uninstall xdebug

# 记录xdebug安装目录
# /usr/local/Cellar/php@7.2/7.2.33/pecl/20170718/xdebug.so

```

### 配置 Xdebug

```bash
# 配置文件
[xdebug]
zend_extension="/usr/local/Cellar/php@7.2/7.2.33/pecl/20170718/xdebug.so"
xdebug.remote_enable = On
xdebug.remote_handler = dbgp
xdebug.remote_host= localhost
xdebug.remote_mode = req
xdebug.remote_port = 9001
xdebug.idekey = "PHPSTORM"

# 添加配置文件到 php.ini
```

### 配置 phpstorm

- 点击右上角 添加配置项
  ![img](http://files.whbaqn.com/01.png)
- 添加调试服务器
  ![img](http://files.whbaqn.com/02.png)
