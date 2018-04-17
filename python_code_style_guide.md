# Python开发规范

## 开发环境
Linux, Python 3.5+, Flask 0.12+

## 规范指南
- 代码风格遵循[PEP8规范](https://www.python.org/dev/peps/pep-0008/)。
- 文档字符串风格遵循[PEP257规范](https://www.python.org/dev/peps/pep-0257/)。

## 静态审查工具
后台开发成员的Python代码须通过上述两种规范的静态审查才可提交。
- PEP8规范审查有两种工具可选：
  1. Pylint，一个Python代码分析工具，默认配置PEP8规范，使用说明见<https://www.jianshu.com/p/c0bd637f706d>。
  2. PyCharm（推荐），一个Python集成开发环境，默认集成了遵循PEP8规范的代码审查功能。
- PEP257规范审查工具为pydocstyle，使用说明见<https://pypi.org/project/pydocstyle/>。
