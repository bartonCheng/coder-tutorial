# web

## 使用隐形跳转链接

- 代码里面的演示www.baidu.com网址，也就是目标网址

```bash
<html>
<head>
<meta name="viewport" content="width=device-width,initial-scale=1">
</head>
<frameset border="0" rows="100%,*" cols="100%" frameborder="no">
  <frame name="TopFrame" scrolling="yes" noresize src="https://www.baidu.com">
  <frame name="BottomFrame" scrolling="no" noresize>
  <noframes></noframes>
</frameset>
</html>
```
