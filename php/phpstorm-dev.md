## phpstorm 开发调试指南

- 本教程适用于 jetbrains 全系列产品

## 安装 【mac win 通用】

- [下载 2020.2 版本](https://www.jetbrains.com/phpstorm/download/other.html)

### 修改验证 host

```bash
# 工具地址
# 链接: https://pan.baidu.com/s/1GGPFxN501H8xJW9ZU8RvfQ  密码: 2ae0
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

- 调出全局设置页面
- 选择 语言和框架 -- 选择 Debug 配置 xdebug 端口 9001
- 选择 Debug 下面的 DBGp Proxy 配置 IDE key： PHPSTORM Host：localhost Port：9001
- 配置 Server 增加一个虚拟 server 配置 Name：phpserver Host：localhost Port：8000
- 应用
- 在项目里 增加配置（在右上角）-- 选择 php web page -- 选择刚才配置好的 phpserver 应用

## mac os 如何关闭 sip 保护机制：

- OSX 10.11 El Capitan（或更高）新添加了一个新的安全机制叫系统完整性保护 System Integrity Protection (SIP)

```bash
# 错误提示为：cp: /usr/lib/php/extensions/no-debug-non-zts-20131226/xdebug.so: Operation not permitted
# 重启电脑 按住 Command + R
# 菜单“实用工具” ==>> “终端” ==>> 输入
csrutil disable
# 开启
csrutil enable
```
