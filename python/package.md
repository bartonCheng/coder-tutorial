# 开发开源自己的 Python 包

## 前言 
[官方使用文档 - 发布一个包](https://packaging.python.org/tutorials/packaging-projects/)
[官方使用文档 - 配置项目的解释](https://packaging.python.org/guides/distributing-packages-using-setuptools/)
想要写出自己的一个包，大概分为五步
- 项目创建
- 搭建虚拟运行环境
- 编写项目代码
- 编写安装脚本
- 上传PyPi

## 项目创建

- 正常的创建git项目
- 选择.gitignore python
- 选择开源协议，可选择 MIT

## 搭建虚拟环境
- 构建虚拟环境使用的是 [Virtualenv](https://virtualenv.pypa.io/en/latest/)

### 使用方法

- [python 官方使用文档](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)
```bash
# 安装
# 最新版本的建议使用这种安装方式，不推荐pip3 install ***
python3 -m pip install virtualenv

# 创建一个独立的 python 环境
virtualenv --no-site-packages venv
# --no-site-packages :已经安装到系统Python环境中的所有第三方包都不会复制过来

# 激活环境
source venv/bin/activate

# 安装激活的文件
python3 -m pip install -r requirements.txt

# 导出依赖包文件
python3 -m pip freeze
```