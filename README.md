# ElegantBook 与 Cleveref 兼容性增强

本项目是对 [ElegantBook](https://github.com/ElegantLaTeX/ElegantBook) 文档类的增强版本，主要改进了与 `cleveref` 宏包的兼容性，并实现了定理环境的连续编号功能。

## 主要改进

### 1. Cleveref 完全支持

原版 ElegantBook 在与 `cleveref` 宏包配合使用时存在兼容性问题。本版本完全解决了这些问题：

- ✅ 自动配置所有定理环境的 `\cref` 和 `\Cref` 命令
- ✅ 支持标签前缀映射（如 `thm:`、`def:`、`lem:` 等）
- ✅ 中文模式下的连接词自动配置（使用"以及"、"到"、"、"等）
- ✅ 支持多个引用的智能连接
- ✅ 兼容 `nameinlink` 和 `capitalize` 选项

### 2. 定理环境连续编号

在 `fancy` 模式下，所有定理类环境（定理、定义、引理、命题、推论等）现在可以实现连续编号：

**原版行为**：
- 定理 1.1, 1.2, 1.3...
- 定义 1.1, 1.2, 1.3...（独立编号）
- 引理 1.1, 1.2, 1.3...（独立编号）

**改进后行为**：
- 定理 1.1
- 定义 1.2（与定理连续）
- 引理 1.3（继续连续）
- 命题 1.4
- 推论 1.5

### 3. 新增 `attention` 环境

新增了 `attention`（注意）环境，用于强调重要内容：

- 中文：注意
- 英文：Attention
- 意大利语：Attenzione
- 法语：Attention
- 荷兰语：Let op
- 匈牙利语：Figyelem
- 德语：Achtung
- 西班牙语：Atención
- 蒙古语：Анхаар
- 葡萄牙语：Atenção
- 日语：注意

### 4. 改进的环境接口

#### Fancy 模式改进

使用 `newtcbtheorem` 替代 `DeclareTColorBox`，提供更标准的接口：

```latex
% 完全没有参数
\begin{theorem}
  定理内容
\end{theorem}

% 只有标题
\begin{theorem}{重要定理}
  定理内容
\end{theorem}

% 标题和标签
\begin{theorem}{Cauchy-Schwarz 不等式}{cs}
  定理内容
\end{theorem}

% 引用
\cref{thm:cs}  % 输出：定理 1.1
```

#### `example` 和 `problem` 环境改进

在 `fancy` 模式下，`example` 和 `problem` 环境现在使用新增的 `plainstyle` 样式，保持简洁的纯文本外观，同时支持与其他定理环境类似的参数语法。

### 5. 自定义定理环境的增强

通过 `\elegantnewtheorem` 创建的自定义定理环境现在也自动支持 cleveref：

```latex
\elegantnewtheorem{thm}{定理}{thmstyle}{thm}[theorem]

\begin{thm}{}{mythm}
  自定义定理内容
\end{thm}

\cref{thm:mythm}  % 自动配置，无需手动设置
```

## 技术细节

### 连续编号实现

当 `usesamecnt=false`（默认）时：
1. 创建共享计数器 `ELEGANT@sharedthmcnt`
2. 所有定理环境使用 `tcb@cnt@theorem` 计数器
3. 通过 `use counter=tcb@cnt@theorem` 实现连续编号

### Cleveref 配置

1. **环境类型配置**：使用 `label type` 参数为每个环境设置正确的类型
2. **前缀映射**：通过 `\crefalias` 将标签前缀（如 `thm:`）映射到环境类型
3. **显示名称配置**：使用 `\crefname` 设置单复数形式
4. **中文连接词**：在中文模式下自动配置 `\crefmiddleconjunction`、`\creflastconjunction` 等

### 新增样式

- **`plainstyle`**：纯文本样式，无边框，标题内联显示，适合 `example` 和 `problem` 环境
- **`attstyle`**：基于 `plainstyle`，使用橙红色标题，带钻石符号装饰

## 使用示例

### 基本使用

```latex
\documentclass[cn,scheme=chinese,mode=fancy]{elegantbook}
\usepackage[nameinlink,capitalize]{cleveref}

\begin{document}

\chapter{测试章节}

\begin{theorem}{Fermat 大定理}{fermat}
  当 $n > 2$ 时，方程 $x^n + y^n = z^n$ 没有正整数解。
\end{theorem}

\begin{definition}{素数}{prime}
  大于 1 的自然数，除了 1 和它本身外，不能被其他自然数整除的数。
\end{definition}

\begin{lemma}{}{helper}
  辅助引理内容。
\end{lemma}

% 自动连续编号：定理 1.1, 定义 1.2, 引理 1.3

% 智能引用
参见 \cref{thm:fermat}。  % 输出：参见定理 1.1
根据 \cref{def:prime,lem:helper}。  % 输出：根据定义 1.2 以及引理 1.3

\end{document}
```

### 自定义定理环境

```latex
% 创建自定义环境，自动继承连续编号
\elegantnewtheorem{conjecture}{猜想}{prostyle}{conj}[theorem]
\elegantnewtheorem{claim}{断言}{thmstyle}{claim}[theorem]

\begin{conjecture}{Goldbach 猜想}{goldbach}
  任何大于 2 的偶数都可以表示为两个素数之和。
\end{conjecture}

\begin{claim}{}{key}
  关键断言。
\end{claim}

% 引用
正如 \cref{conj:goldbach,claim:key} 所示。
```

### 附录引用配置

对于需要区分正文章节和附录引用的情况，参见 [`test_appendix.tex`](test_appendix.tex)：

```latex
\usepackage{etoolbox}

% 配置图表的 cref 名称
\crefname{figure}{图}{图}

% 在 \appendix 命令前重新配置 chapter 引用
\pretocmd{\appendix}{%
  \crefalias{chapter}{appchapter}%
}{}{}

% 配置引用格式
\crefformat{chapter}{#2第\zhnumber{#1}章#3}
\crefformat{appchapter}{#2附录~#1#3}
\crefformat{section}{#2第#1节#3}
```

**效果**：
- 正文：`\cref{chap:intro}` → "第一章"
- 附录：`\cref{appx:A}` → "附录 A"
- 混合引用：`\cref{chap:intro,appx:A}` → "第一章以及附录 A"

## 与原版的差异

### 兼容性

✅ **向后兼容**：所有原版 ElegantBook 的功能完全保留  
✅ **语法兼容**：现有文档无需修改即可使用  
⚠️ **编号变化**：默认情况下定理环境将连续编号（原版是独立编号）

## 文件说明

- **`elegantbook.cls`**：增强版文档类（本项目）
- **`elegantbook.cls.original`**：原版文档类备份
- **`test_elegantbook.tex`**：完整的测试文档，演示所有功能
- **`changes.diff`**：详细的差异对比文件

## 测试

### 查看示例 PDF

仓库中包含预编译的示例文档：

1. **[test_elegantbook.pdf](test_elegantbook.pdf)** - 基本功能演示
   - 定理环境的连续编号
   - Cleveref 的各种引用格式
   - 多种参数语法的支持
   - 中文连接词和括号
   - 自定义定理环境

2. **[test_appendix.pdf](test_appendix.pdf)** - 附录引用配置示例
   - 正文章节与附录的引用格式区分
   - 混合引用正文和附录
   - 附录中的定理环境
   - 完整的配置代码示例

### 自行编译

运行测试文档：

```bash
xelatex test_elegantbook.tex
xelatex test_elegantbook.tex  # 第二次编译以生成正确的交叉引用
```

### 测试验证
- ✅ 定理环境的连续编号
- ✅ Cleveref 的所有引用格式
- ✅ 多种参数语法的支持
- ✅ 中文连接词的正确显示
- ✅ 自定义定理环境的兼容性

## 版本信息

- **基于版本**：ElegantBook v4.5 (2022/12/31)
- **增强日期**：2025/12/28
- **兼容性**：XeLaTeX / LuaLaTeX

## 致谢

感谢 [ElegantLaTeX](https://github.com/ElegantLaTeX) 团队开发的优秀文档类。

## 许可证

本项目继承原 ElegantBook 的许可证，可自由分发和修改。

---

**注意**：本项目是对原版 ElegantBook 的增强，不是官方版本。如需使用原版，请访问 [ElegantBook 官方仓库](https://github.com/ElegantLaTeX/ElegantBook)。

