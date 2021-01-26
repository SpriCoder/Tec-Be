subprocess
---

# 1. 概述
1. subprocess模块允许你生成新的进程，连接它们的输入、输出、错误管道，并且获取它们的返回码。
2. <a href = "https://docs.python.org/zh-cn/3/library/subprocess.html">官方文档</a>

# 2. 关键的对象Popen构造
1. <a href = "https://www.cnblogs.com/a13798508446/p/9619221.html">https://www.cnblogs.com/a13798508446/p/9619221.html</a>

# 3. 一个简单的例子
> 本python文件运行了同级目录下的另一个py文件

```py
# -*- coding: utf-8 -*-
import subprocess

sub = subprocess.Popen("python ./other.py", shell=True, stdout=subprocess.PIPE, stdin=0)
sub.wait()
print(sub.stdout.read().decode("utf-8"))
```