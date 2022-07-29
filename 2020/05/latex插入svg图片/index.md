# Latex 插入 svg 图片


<!--more-->

## 1 前提
以下几种方法均是基于对 `inkscape` 的调用，因此需要设置 `inkscape` 在环境变量中

可在命令行中输入 `inkscape -V` 进行测试是否设置成功

## 2 方法一

使用 `svg` 宏包

导言区：

```tex
\usepackage{svg}
\svgsetup{
  inkscapepath=i/svg-inkscape/
}
\svgpath{{svg/}}
```

正文区：

```tex
\includesvg{demo}
```

**注意**：

编译开启 `\write18`, 即编译时需要加上 `-shell-escape` 参数

更多选项参数参见 `svg` 宏包文档

**此方法对于 inkscape 1.0 已经失效**

## 3 方法二

先利用 inkscape 导出 `.pdf` 和 `.pdf_tex` 文件，然后在 latex 文档中插入，注意文件名不要有空格

导出  `.pdf` 和 `.pdf_tex` 文件，在 cmd 中 执行

```bash
# inkscape version 为 1.0
inkscape -D --export-type=pdf --export-latex demo.svg
```
Latex 导言区

```tex
\usepackage{color}
\usepackage{transparent}
\usepackage{graphicx}
\usepackage{import}
\newcommand{\includesvg}[2]{
\def\svgwidth{#1}
\import{i}{#2.pdf_tex}
}

```

正文区

```tex
\begin{figure}
  \centering
  \includesvg{0.8\columnwidth}{demo}
  \caption{Figure}
\end{figure}
```

## 4 方法三

利用 latex 调用 inkscape (version = 1.0)

导言区：

```tex
\usepackage{color}
\usepackage{transparent}
\usepackage{graphicx}
\usepackage{import}

%% 注意图片途径为子目录 i
\newcommand{\executeiffilenewer}[3]{
\ifnum\pdfstrcmp{\pdffilemoddate{#1}}
{\pdffilemoddate{#2}}>0
{\immediate\write18{#3}}\fi
}

\newcommand{\includesvg}[2]{
\executeiffilenewer{i/#2.svg}{i/#2.pdf}
{inkscape -D --export-filename=i/#2.pdf 
--export-latex i/#2.svg}
\def\svgwidth{#1}
\import{i}{#2.pdf_tex}
}
```

正文区：

```tex
\begin{figure}
  \centering
  \includesvg{0.8\columnwidth}{demo}
  \caption{Figure}
\end{figure}
```

**注意**：需开启 `\write18`, 即编译时需要加上 `-shell-escape` 参数

## 5 方法四

同样是利用 latex 调用 inkscape (version = 1.0) 完成 svg 到 pdf 的转化，但是不需要 latex 的文字处理

导言区：

```tex
\usepackage{graphicx}
\graphicspath{{i/}}

\newcommand{\executeiffilenewer}[3]{
\ifnum\pdfstrcmp{\pdffilemoddate{#1}}
{\pdffilemoddate{#2}}>0
{\immediate\write18{#3}}\fi
}

\newcommand{\includesvg}[2]{
\executeiffilenewer{i/#2.svg}{i/#2.pdf}
{inkscape -D --export-filename=i/#2.pdf i/#2.svg}
\includegraphics[width=#1]{#2.pdf}
}
```

正文区：

```tex
\begin{figure}
  \centering
  \includesvg{0.8\columnwidth}{demo}
  \caption{Figure}
\end{figure}
```

**注意**：需开启 `\write18`, 即编译时需要加上 `-shell-escape` 参数




