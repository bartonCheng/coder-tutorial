## phpstorm 开发调试指南

- 本教程适用于 jetbrains 全系列产品

## 安装

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

## ThinkPHP

### 安装

```bash
# 最新版本为40
# composer create-project topthink/think=5.1.40 tp5
```
