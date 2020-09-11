# 开发开源自己的 Python 包

## 前言

- [官方使用文档 - 发布一个包](https://packaging.python.org/tutorials/packaging-projects/)
- [官方使用文档 - 配置项目的解释](https://packaging.python.org/guides/distributing-packages-using-setuptools/)

### 五步

- 项目创建
- 搭建虚拟运行环境
- 编写项目代码
- 编写安装脚本
- 上传 PyPi

## 项目创建

- 正常的创建 git 项目
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

## 构建项目代码

### 项目结构

```bash
packaging_tutorial
├── LICENSE
├── README.md
├── example_pkg
│   └── __init__.py
├── setup.py
└── tests
```

### setup.py

```bash
import setuptools

with open("README.md", "r") as fh:
    long_description = fh.read()

setuptools.setup(
    name="example-pkg-YOUR-USERNAME-HERE", # Replace with your own username
    version="0.0.1",
    author="Example Author",
    author_email="author@example.com",
    description="A small example package",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/pypa/sampleproject",
    packages=setuptools.find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires='>=3.6',
)
```

### 单元测试

- pytest 是个简单易用的库， 这个需要安装。
- unittest 这是 python 内置的测试工具，可以不要安装。

```bash
# 本地安装依赖包
python3 setup.py install

# 安装项目依赖
python3 -m pip install -r requirements.txt

# 依赖更新安装
pip3 install package --upgrade

# 测试
python3 tests/trading_test.py
```

## 注册 pypi 并上传自己的库

### pypirc 文件

- 在用户目录下创建.pypirc 文件

```bash
[distutils]
index-servers =
 pypi
 pypitest

[pypi]
repository: https://upload.pypi.org/legacy/
username: 账户
password: 密码

[pypitest]
repository: https://test.pypi.org/legacy/
username: 账户
password: 密码
```

## 安装依赖

```bash
# 安装编译工具
python3 -m pip install --user --upgrade setuptools wheel

# 生成上传文件
python3 setup.py sdist bdist_wheel

# 安装上传工具
python3 -m pip install --user --upgrade twine

# 上传文件
python3 -m twine upload --repository testpypi dist/*

```
