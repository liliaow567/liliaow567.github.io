# Windows 系统 使用 VC2019 编写 python 的 C++ 拓展


<!--more-->

## 1 测试环境
1. VC 2019 安装 “使用C++的桌面开发工作负载”，如下图

![](https://s1.ax1x.com/2020/05/09/YMlaUf.png)

2. Anaconda3
3. 64 位系统


## 2 拓展模块目标
C++ 实现基本的加减乘除函数，Python 进行调用

## 3 具体步骤
### 一、创建 DLL 项目
在 VC 2019 中创建一个 动态链接库(DLL) 解决方案

![](https://s1.ax1x.com/2020/05/09/YMl0PS.png)

### 二、项目命名
项目名称 MyMath，创建，自动生成如下文件，解决方案资源管理器界面如下

![](https://s1.ax1x.com/2020/05/09/YMlB8g.png)

### 三、项目属性设置
设置项目属性，分别是 附加包含目录（`C/C++` &rarr; `常规`）；附加库目录（`链接器` &rarr; `常规`）；附加依赖项（`链接器` &rarr; `输入`），三项的值分别添加如下路径或参数：

{{< admonition title="路径参数" >}}
附加包含目录：%ANACONDA_HOME%\include

附加库目录：%ANACONDA_HOME%\libs

附加依赖项：python37.lib 

（%ANACONDA_HOME% 替换为 anaconda3 安装的根目录）
{{< /admonition >}}

### 四、源文件编写
添加源文件 MyMath.cpp，如下

```cpp
// MyMath.cpp
#include "pch.h"
#include "Python.h"

double Add(double x, double y)
{
	return x + y;
}
double Subtract(double x, double y)
{
	return x - y;
}

double Multiply(double x, double y)
{
	return x * y;
}

double Divide(double x, double y)
{
	return x / y;
}

// 封装c++函数
PyObject* MyMath_Add(PyObject* self, PyObject* args)
{
	double x, y;
	if (!PyArg_ParseTuple(args, "dd", &x, &y))
	{
		return NULL;
	}

	return Py_BuildValue("d", Add(x, y));
}
PyObject* MyMath_Subtract(PyObject* self, PyObject* args)
{
	double x, y;
	if (!PyArg_ParseTuple(args, "dd", &x, &y))
	{
		return NULL;
	}
	return Py_BuildValue("d", Subtract(x, y));
}
PyObject* MyMath_Multiply(PyObject* self, PyObject* args)
{
	double x, y;
	if (!PyArg_ParseTuple(args, "dd", &x, &y))
	{
		return NULL;
	}
	return Py_BuildValue("d", Multiply(x, y));
}
PyObject* MyMath_Divide(PyObject* self, PyObject* args)
{
	double x, y;
	if (!PyArg_ParseTuple(args, "dd", &x, &y))
	{
		return NULL;
	}
	return Py_BuildValue("d", Divide(x, y));
}

static PyMethodDef MyMath_methods[] = {
	{"add", MyMath_Add, METH_VARARGS, "This is an add function."},
	{"subtract", MyMath_Subtract, METH_VARARGS, "This is a subtract function."},
	{"multiply", MyMath_Multiply, METH_VARARGS, "This is a multiply function."},
	{"divide", MyMath_Divide, METH_VARARGS, "This is a divide function."},
	{NULL, NULL, 0, NULL}
};

static PyModuleDef MyMath_module = {
	PyModuleDef_HEAD_INIT,
	"MyMath",
	"My Math Module",
	0,
	MyMath_methods
};

PyMODINIT_FUNC PyInit_MyMath()
{
	return PyModule_Create(&MyMath_module);
}

```
### 五、配置和生成解决方案
选择 解决方案配置和平台， 如下，然后生成解决方案

![](https://img-blog.csdnimg.cn/20200419232133986.png)

![](https://s1.ax1x.com/2020/05/09/YMlD2Q.png)

### 六、构建 python 测试项目
 在生成目录中找到生成的 MyMath.dll 文件，移动到 python 项目下，并把后缀改为 .pyd，python 目录结构如下

![](https://s1.ax1x.com/2020/05/09/YMlyKs.png)

### 七、测试结果
测试代码
```python
## main.py
import MyMath

print(MyMath.add(1.5, 3.))
print(MyMath.subtract(1.5, 3.))
print(MyMath.multiply(1.5, 3.))
print(MyMath.divide(1.5, 3.))

print(type(MyMath.add(1.5, 3.)))
```
运行结果：

![](https://s1.ax1x.com/2020/05/09/YMlcbq.png)

## 4 参考
1. [https://docs.python.org/zh-cn/3/extending/extending.html](https://docs.python.org/zh-cn/3/extending/extending.html)
2. [https://docs.microsoft.com/zh-cn/visualstudio/python/working-with-c-cpp-python-in-visual-studio?view=vs-2019](https://docs.microsoft.com/zh-cn/visualstudio/python/working-with-c-cpp-python-in-visual-studio?view=vs-2019)
