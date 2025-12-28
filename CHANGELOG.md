# 修改说明 (CHANGELOG)

本文档详细说明了 `elegantbook.cls` 相对于原版 `elegantbook.cls.original` 的所有修改。

## 版本信息

- **原版**：ElegantBook v4.5 (2022/12/31)
- **增强版本日期**：2025/12/28
- **修改行数**：约 500+ 行改动

---

## 一、多语言支持改进

### 新增 `attentionname` 定义

为所有支持的语言添加了 `\attentionname` 命令，用于 `attention` 环境：

| 语言 | 命令 | 输出 |
|------|------|------|
| 中文 (cn) | `\attentionname` | 注意 |
| 英文 (en) | `\attentionname` | Attention |
| 意大利语 (it) | `\attentionname` | Attenzione |
| 法语 (fr) | `\attentionname` | Attention |
| 荷兰语 (nl) | `\attentionname` | Let op |
| 匈牙利语 (hu) | `\attentionname` | Figyelem |
| 德语 (de) | `\attentionname` | Achtung |
| 西班牙语 (es) | `\attentionname` | Atención |
| 蒙古语 (mn) | `\attentionname` | Анхаар |
| 葡萄牙语 (pt) | `\attentionname` | Atenção |
| 日语 (jp) | `\attentionname` | 注意 |

**修改位置**：
- 行 432 (cn)
- 行 470 (en)
- 行 509 (it)
- 行 546 (fr)
- 行 582 (nl)
- 行 619 (hu)
- 行 660 (de)
- 行 696 (es)
- 行 734 (mn)
- 行 771 (pt)
- 行 814 (jp)

---

## 二、Fancy 模式的重大改进

### 2.1 加载 tcolorbox theorems 库

**位置**：行 907

```latex
\RequirePackage[many]{tcolorbox}
+\tcbuselibrary{theorems}
```

**说明**：加载 `theorems` 库以支持 `newtcbtheorem` 命令。

### 2.2 连续编号支持

**位置**：行 911-925

**原代码**：
```latex
\else
  \tcbset{
    new/usesamecnt/.style = {}
  }
\fi
```

**新代码**：
```latex
\else
  % Create a shared counter for all theorem environments when usesamecnt is false
  % This ensures continuous numbering across all theorem types
  \newcounter{ELEGANT@sharedthmcnt}
  \counterwithin{ELEGANT@sharedthmcnt}{\ELEGANT@thmcnt}
  \tcbset{
    new/usesamecnt/.style = {use counter = ELEGANT@sharedthmcnt}
  }
\fi
```

**说明**：
- 创建共享计数器 `ELEGANT@sharedthmcnt`
- 将其与章节或小节计数器关联
- 所有定理环境共享此计数器，实现连续编号

### 2.3 新增样式定义

**位置**：行 974-1012

#### (1) `attstyle` 样式

```latex
attstyle/.style={
  plainstyle,
  coltitle=orange!70!red,
  overlay unbroken and last={
    \node[anchor=south east, outer sep=0pt] at (\linewidth-width,0) {
      \textcolor{orange!70!red}{$\diamondsuit$}};}},
```

**用途**：为 `attention` 环境提供橙红色标题样式。

#### (2) `plainstyle` 样式

```latex
plainstyle/.style={
  fontupper=\rmfamily,
  lower separated=false,
  boxrule=0pt,
  colback=white,
  colframe=white,
  colbacktitle=white,
  coltitle=main,
  fonttitle=\bfseries,
  enhanced,
  breakable,
  top=3pt,
  bottom=3pt,
  left=-3pt,
  right=0pt,
  before skip=0pt,
  after skip=0pt,
  attach title to upper={~},
  attach boxed title to top left={
    yshift=-1000pt,
    xshift=0pt},
  separator sign={}},
```

**用途**：为 `example` 和 `problem` 环境提供纯文本样式（无边框）。

### 2.4 改进 `ELEGANT@title` 代码

**位置**：行 1013-1025

**原代码格式问题**：存在不必要的空行和格式不一致

**新代码**：
```latex
ELEGANT@title/.code n args={2}
  {
    \tcbset
      {
        title=%
          {%
            \csname #1name\endcsname~%
            \ifdef{\thetcbcounter}{\thetcbcounter}{}%
            \ifblank{#2}{}{\ (#2)}%
          }
      }
  },
```

**改进**：修正了注释符 `%` 的位置，确保没有不必要的空格。

### 2.5 重构 `\ELEGANT@newtheorem` 命令

**位置**：行 1039-1127

这是最重要的改进之一。原版使用 `DeclareTColorBox`，新版改用 `newtcbtheorem`。

#### 原版实现（简化）：

```latex
\DeclareTColorBox[auto counter,number within=\ELEGANT@thmcnt,usesamecnt,usecnt]{#1}{ g o t\label g }{
  common,#3,
  IfValueTF={##1}{ELEGANT@title={#1}{##1}}{...},
  IfValueT={##4}{...}
}
```

**问题**：
- 参数处理复杂
- 与 cleveref 兼容性差
- 无法正确设置 `label type`

#### 新版实现：

```latex
% 1. 为 theorem 创建基础计数器
\ifdefstring{#1}{theorem}{
  \newtcbtheorem[number within=\ELEGANT@thmcnt,usesamecnt,usecnt]{#1@base}{\csname #1name\endcsname}{%
    common,#3,
    label type=#1
  }{#2}
}{
  % 2. 其他环境使用 theorem 的计数器
  \newtcbtheorem[use counter=tcb@cnt@theorem,number within=\ELEGANT@thmcnt]{#1@base}{\csname #1name\endcsname}{%
    common,#3,
    label type=#1
  }{#2}
}

% 3. 创建用户接口环境
\NewDocumentEnvironment{#1}{ g g }{%
  \IfValueTF{##1}{%
    \IfValueTF{##2}{%
      \begin{#1@base}{##1}{##2}%
    }{%
      \begin{#1@base}{##1}{}%
    }%
  }{%
    \begin{#1@base}{}{}%
  }%
}{%
  \end{#1@base}%
}
```

**改进**：
1. 使用 `newtcbtheorem` 创建内部环境 `#1@base`
2. 使用 `\NewDocumentEnvironment` 创建用户接口
3. 正确设置 `label type` 以支持 cleveref
4. 实现连续编号：theorem 创建计数器，其他环境共享

### 2.6 自动配置 Cleveref

**位置**：行 1106-1126

```latex
% Auto-configure cleveref for this environment
\@ifpackageloaded{cleveref}{%
  % Cleveref is already loaded, configure now
  \@ifundefined{#1name}{}{%
    \crefname{#1}{\csname #1name\endcsname}{\csname #1name\endcsname s}%
  }%
  \crefname{#2}{\csname #1name\endcsname}{\csname #1name\endcsname s}%
  \crefalias{#2}{#1}%
}{%
  % Cleveref not loaded yet, defer to \AtBeginDocument
  \AtBeginDocument{%
    \@ifpackageloaded{cleveref}{%
      \@ifundefined{#1name}{}{%
        \crefname{#1}{\csname #1name\endcsname}{\csname #1name\endcsname s}%
      }%
      \crefname{#2}{\csname #1name\endcsname}{\csname #1name\endcsname s}%
      \crefalias{#2}{#1}%
    }{}
  }%
}%
```

**功能**：
1. 检查 cleveref 是否已加载
2. 配置 `\crefname` 设置显示名称
3. 使用 `\crefalias` 映射标签前缀到环境类型
4. 如果 cleveref 未加载，延迟到 `\AtBeginDocument`

### 2.7 重新定义内置定理环境

**位置**：行 1128-1254

原版使用 `\ELEGANT@newtheorem` 定义，新版直接使用 `newtcbtheorem`：

```latex
% 定理
\newtcbtheorem[number within=\ELEGANT@thmcnt]{theorem}{\theoremname}{%
  common,thmstyle,
  label type=theorem
}{thm}
\DeclareTColorBox{theorem*}{ g o }{
    common,thmstyle,
    IfValueTF={##1}{ELEGANT@title={theorem}{##1}}{...}
}

% 定义（使用 theorem 的计数器）
\newtcbtheorem[use counter=tcb@cnt@theorem,number within=\ELEGANT@thmcnt]{definition}{\definitionname}{%
  common,defstyle,
  label type=definition
}{def}
\DeclareTColorBox{definition*}{ g o }{...}

% 其他环境类似：postulate, axiom, corollary, lemma, proposition
```

**重要**：必须先定义 `theorem`，因为它创建了 `tcb@cnt@theorem` 计数器。

### 2.8 新增 `attention` 环境

**位置**：行 1240-1253

```latex
\newtcbtheorem[use counter from=theorem,number within=\ELEGANT@thmcnt]{attention}{\attentionname}{%
  common,attstyle,
  label type=attention
}{att}
\DeclareTColorBox{attention*}{ g o }{
    common,attstyle,
    IfValueTF={##1}{ELEGANT@title={attention}{##1}}{...}
}
```

### 2.9 配置中文 Cleveref 连接词

**位置**：行 1256-1262

```latex
\ifdefstring{\ELEGANT@lang}{cn}{%
  \newcommand{\creflastconjunction}{ 以及 }%
  \newcommand{\crefrangeconjunction}{ 到 }%
  \newcommand{\crefmiddleconjunction}{、}%
}{}
```

**效果**：
- `\cref{thm1,thm2}` → "定理 1.1、定理 1.2"
- `\cref{thm1,thm2,thm3}` → "定理 1.1、定理 1.2 以及定理 1.3"
- `\crefrange{thm1}{thm3}` → "定理 1.1 到定理 1.3"

### 2.10 全局 Cleveref 配置

**位置**：行 1267-1325

在 `\AtBeginDocument` 中配置所有内置环境的 cleveref：

```latex
\AtBeginDocument{%
  \@ifpackageloaded{cleveref}{%
    % 配置每个环境的显示名称
    \crefname{theorem}{\theoremname}{\theoremname s}%
    \crefname{definition}{\definitionname}{\definitionname s}%
    % ... 其他环境
    
    % 映射标签前缀到环境类型
    \crefalias{thm}{theorem}%
    \crefalias{def}{definition}%
    % ... 其他前缀
    
    % 中文连接词配置
    \ifdefstring{\ELEGANT@lang}{cn}{%
      \renewcommand{\crefpairconjunction}{ 以及 }%
      \renewcommand{\crefmiddleconjunction}{、}%
      \renewcommand{\creflastconjunction}{ 以及 }%
      % ... 其他连接词
    }{}
  }{}
}
```

---

## 三、Simple 模式改进

### 3.1 新增 `attstyle` 定理样式

**位置**：行 1343-1344

```latex
\newtheoremstyle{attstyle}{3pt}{3pt}{\citshape}{-3pt}{
  \bfseries\color{orange!70!red}}{}{0.5em}{\thmname{#1} \thmnumber{#2} \thmnote{(#3)}}
```

### 3.2 新增 `attention` 环境

**位置**：行 1376

```latex
\ELEGANT@newtheorem{attention}{attstyle}[theorem]
```

---

## 四、`example` 和 `problem` 环境改进

### 4.1 `example` 环境

**位置**：行 1454-1463

**原版**：无论模式如何，都使用纯文本样式

**新版**：
```latex
\ifdefstring{\ELEGANT@mode}{fancy}{
  % In fancy mode, define as theorem-like environment with plainstyle
  \ELEGANT@newtheorem{example}{exam}{plainstyle}
}{%
  % In simple mode, use original plain text style
  \newenvironment{example}[1][]{
    \refstepcounter{exam}
    \par\noindent\textbf{\color{main}{\examplename} \theexam #1 }\rmfamily}{
    \par\ignorespacesafterend}
}
```

**改进**：
- Fancy 模式：使用 `plainstyle`，支持标题和标签参数
- Simple 模式：保持原有纯文本样式

### 4.2 `problem` 环境

**位置**：行 1480-1489

类似 `example` 环境的改进。

---

## 五、其他改进

### 5.1 文件结尾添加换行符

**位置**：行 1788

**原版**：文件结尾没有换行符（会导致 Git 警告）

**新版**：添加了换行符

---

## 修改统计

| 类别 | 数量 |
|------|------|
| 新增行数 | ~450 行 |
| 修改行数 | ~50 行 |
| 删除行数 | ~30 行 |
| 新增环境 | 1 个 (`attention`) |
| 新增样式 | 2 个 (`plainstyle`, `attstyle`) |
| 改进的命令 | 1 个 (`\ELEGANT@newtheorem`) |
| 多语言支持改进 | 11 种语言 |

---

## 兼容性说明

### 向后兼容

✅ **完全兼容**：所有原版 ElegantBook 的功能都保留

### 默认行为变化

⚠️ **编号方式**：
- 原版：定理、定义、引理等各自独立编号
- 新版：默认连续编号（可通过 `usesamecnt` 选项恢复原版行为）

### 推荐使用方式

```latex
% 推荐配置
\documentclass[cn,scheme=chinese,mode=fancy]{elegantbook}
\usepackage[nameinlink,capitalize]{cleveref}
```

---

## 参考资料

1. [tcolorbox 文档](https://ctan.org/pkg/tcolorbox) - theorems 库
2. [cleveref 文档](https://ctan.org/pkg/cleveref) - 智能交叉引用
3. [ElegantBook 官方文档](https://github.com/ElegantLaTeX/ElegantBook)

---

**最后更新**：2025/12/28

