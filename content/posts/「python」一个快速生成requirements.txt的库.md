---
title: "「python」一个快速生成requirements.txt的库"
date: 2024-10-22T13:29:15+08:00
categories: ['Docs']
author: "Ronan"
---
> 为什么选择 `pipreqs` ？

虽然 Python 提供了 `pip freeze > requirements.txt` 这样的命令生成 `requirements.txt`，但它有一个致命缺陷：它会把你当前环境中安装的所有库都写进去，而不仅仅是你项目实际用到的库。结果就是，一个原本只需要几个依赖的小项目，可能会生成一个长长的`requirements.txt`，这不仅冗余，还可能导致依赖冲突。

`pipreqs` 则聪明得多！它会扫描你的项目代码，只把实际用到的库和版本写进 `requirements.txt`，简洁又精准。


## 安装pipreqs

```python
pip install pipreqs
```


## 如何使用pipreqs？

进入到需要生成 `requirements.txt` 的工程目录下，假设工程在`/path/project`，然后输入以下命令：

```python
pipreqs /path/project
```

或者直接进入到该工程目录下，然后运行 `pipreqs`：

```python
pipreqs .
```
