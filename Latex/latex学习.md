# LaTEX学习

## 变量定义

将变量`\A`定义为`\B`

```latex
\newcommand{\A}{\B}
\let\A{\B}
\let\A\B
```

## 文件引入

```latex
%另起一页, 一般用来存放章节
\include{xxx}

%直接引入, 
%	1. 一般用来存放导言区(即\documentclass{book}到\begin{document}之间的内容)
%	2. 复杂的图表, 代码
\input{xxx}
```

## 文档结构

```tex
%前言部分, 页码使用小写罗马数字, 章节不编号
\frontmatter

%正文部分, 页码使用阿拉伯数字, 从1开始计数
\mainmatter

%附录部分, 章节使用英文字母A,B,C...编号
\appendix

%后记部分, 章节不编号
\backmatter
```
