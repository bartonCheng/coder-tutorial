# Git

## Git 问题

### 描述

- 如何解决类似 curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused 的问题

```bash
# 解决方案
打开 https://www.ipaddress.com/ 输入访问不了的域名

# 在本机的 host 文件中添加
199.232.68.133 raw.githubusercontent.com
199.232.68.133 user-images.githubusercontent.com
199.232.68.133 avatars2.githubusercontent.com
199.232.68.133 avatars1.githubusercontent.com
```
