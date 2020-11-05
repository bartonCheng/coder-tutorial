# think php 开发手册

## 手册文档

- [thinkphp5.1](https://www.kancloud.cn/manual/thinkphp5_1/353946)
- [thinkphp6.0](https://www.kancloud.cn/manual/thinkphp6_0/1037479)

## thinkphp 5.1

### 安装

```bash
# 最新更新版本 5.1.40
composer create-project topthink/think=5.1.40 tp5
```

### 验证器

- web 程序入口对提交的数据进行统一验证，符合 AOP【面向切面编程】的思想，不论从哪里来的请求，都需要在这里进行数据的校验。
- [tp5.1 验证器的定义规则](https://www.kancloud.cn/manual/thinkphp5_1/354102)

### 中间键

- 在 tp5.1 之前的版本这个功能成为行为(behavior).在 5.1 之后为了和 laravel 对齐，成为中间键，其实就是在对调用控制器之前的对请求的逻辑的修改和控制。可以对请求的响应进行修改，比如 web 开发的跨域问题可以在这里进行解决。

### 异常处理

### 日志处理

### 缓存

### 单元测试

- tp5.1 的单元测试
- phpunit [测试文档](http://www.phpunit.cn/manual/current/zh_cn/installation.html#installation.requirements)

```bash
# tp5.1 的最新版本是2.0.5
composer require topthink/think-testing=2.0.5
```

### tp5.1 对路由传入对数值会做取整处理

```php
// 在指定路由的时候，指定模式就会正常处理
Route::get('api/v1/banner/:id','api/v1.Banner/getBanner')
    ->pattern(['id'=>'[\d.]+']);
```
