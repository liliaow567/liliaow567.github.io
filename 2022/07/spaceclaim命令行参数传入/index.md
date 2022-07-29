# Spaceclaim命令行参数传入


<!--more-->

## 1 命令行参数帮助信息
cmd 窗口执行  `spaceclaim /?` 弹出帮助文档

帮助文档实际上位于路径 `%UserProfile%\Documents\SpaceClaimReports\` 中

## 2 传参常用命令
使用 `/ScriptArgs` 传入参数，参数之间用`,` 隔开

```cmd
spaceclaim /RunScript="jou\script.py" /ScriptArgs="15,pas15" /Headless=True /Splash=False /Welcome=False /ExitAfterScript=True
```

然后 `python` 脚本中使用 `args[n]` 来获取，类型为字符串

{{< admonition >}}
* 脚本路径是相对于 `spaceclaim` 的运行路径而言的
* 使用的是 `Ansys 2020R1` 对应的 `spaceclaim` 版本，`api version: 18`
{{< /admonition >}}
