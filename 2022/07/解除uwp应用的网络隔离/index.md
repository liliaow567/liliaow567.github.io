# 解除UWP应用的网络隔离


<!--more-->

原文链接: <https://zhuanlan.zhihu.com/p/55906778>

## 1 获取应用的SID

在注册表中找到应用的SID，注册表地址是：

`HKEY_CURRENT_USER\Software\Classes\Local Settings\Software\Microsoft\Windows\CurrentVersion\AppContainer\Mappings`

Mappings 文件夹内的内容，就是所有 UWP 应用的对应的 SID。随便点击一个 SID 右边会显示应用名称。找到你要解除网络隔离的应用，复制它的 SID。

## 2 解除应用隔离

使用 cmd 命令

```cmd
CheckNetIsolation.exe loopbackexempt -a -p=%SID%
```
