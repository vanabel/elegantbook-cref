# 快速开始

## 1. 最小工作示例

```latex
\documentclass[cn,scheme=chinese,mode=fancy]{elegantbook}
\usepackage[nameinlink,capitalize]{cleveref}

\begin{document}

\chapter{第一章}

\begin{theorem}{示例定理}{demo}
这是一个定理。
\end{theorem}

参见 \cref{thm:demo}。  % 输出：参见定理 1.1

\end{document}
```

## 2. 编译命令

```bash
xelatex main.tex
xelatex main.tex  # 第二次编译生成交叉引用
```

## 3. 常用选项

```latex
% 按节编号
\documentclass[cn,mode=fancy,section]{elegantbook}

% 隐藏答案
\documentclass[cn,mode=fancy,noanswer]{elegantbook}
```

## 4. 引用格式

自动配置，无需手动设置：

| 类型 | 命令 | 输出 |
|------|------|------|
| 章 | `\cref{chap:1}` | 第一章 |
| 节 | `\cref{sec:1}` | 第1.1节 |
| 图 | `\cref{fig:1}` | 图 1.1 |
| 表 | `\cref{tab:1}` | 表 1.1 |
| 公式 | `\cref{eq:1}` | 式 (1.1) |
| 定理 | `\cref{thm:1}` | 定理 1.1 |

## 5. 完整示例

查看 `EXAMPLE.tex` 了解所有功能。

## 6. 常见问题

**Q: 引用显示为 "??"**  
A: 需要编译两次以生成正确的交叉引用

**Q: 想要独立编号而非连续编号**  
A: 使用选项 `usesamecnt=true`

**Q: 如何配置附录引用**  
A: 参见 README.md 中的"附录引用配置"章节

## 7. 更多帮助

- 完整文档：[README.md](README.md)
- 更新日志：[CHANGELOG.md](CHANGELOG.md)
- 完整示例：[EXAMPLE.tex](EXAMPLE.tex)
