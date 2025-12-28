# 仓库信息

## 项目名称
**ElegantBook Cleveref Enhancement** - ElegantBook 与 Cleveref 兼容性增强

## 仓库结构

```
elegantbook-cref/
├── .git/                      # Git 仓库目录
├── .gitignore                 # Git 忽略文件配置
├── README.md                  # 项目说明文档（主文档）
├── CHANGELOG.md               # 详细修改说明
├── elegantbook.cls            # 增强版文档类
├── elegantbook.cls.original   # 原版文档类（v4.5, 2022/12/31）
├── test_elegantbook.tex       # 完整测试文档
└── changes.diff               # 差异对比文件
```

## 文件说明

### 核心文件

1. **`elegantbook.cls`** (61,676 字节)
   - 增强版 LaTeX 文档类
   - 基于 ElegantBook v4.5
   - 新增约 450 行代码
   - 完全向后兼容

2. **`elegantbook.cls.original`** (50,368 字节)
   - 原版 ElegantBook v4.5 (2022/12/31)
   - 用于比对和参考
   - 保持原样不做修改

### 文档文件

3. **`README.md`** (6,123 字节)
   - 项目主文档
   - 功能介绍
   - 使用示例
   - 快速开始指南

4. **`CHANGELOG.md`** (约 15,000 字节)
   - 详细的修改说明
   - 逐条列出所有改进
   - 技术细节和代码示例
   - 兼容性说明

5. **`test_elegantbook.tex`** (2,998 字节)
   - 完整的测试文档
   - 演示所有功能
   - 验证连续编号
   - 测试 cleveref 引用

### 辅助文件

6. **`changes.diff`** (19,841 字节)
   - Unix diff 格式的差异文件
   - 显示所有代码变更
   - 可用于代码审查

7. **`.gitignore`**
   - 忽略 LaTeX 辅助文件
   - 忽略 PDF 输出
   - 忽略系统文件

## Git 提交历史

### 提交 1：Initial commit
```
commit 13ea324
Date: 2025-12-28

Initial commit: ElegantBook with cleveref support and continuous numbering

主要改进：
- 完全支持 cleveref 宏包，自动配置所有定理环境的引用
- 实现定理环境连续编号功能
- 新增 attention（注意）环境，支持多语言
- 改进 example 和 problem 环境，在 fancy 模式下使用 plainstyle
- 增强 \elegantnewtheorem 命令，自动配置 cleveref
- 中文模式下自动配置连接词（以及、到、、等）

文件：
- elegantbook.cls
- elegantbook.cls.original
- test_elegantbook.tex
- .gitignore
- README.md
```

### 提交 2：Add CHANGELOG
```
commit f01a834
Date: 2025-12-28

Add detailed CHANGELOG documenting all modifications

添加详细的修改说明文档，包括：
- 多语言支持改进
- Fancy 模式的重大改进
- Simple 模式改进
- example 和 problem 环境改进
- 修改统计和兼容性说明

文件：
- CHANGELOG.md
```

## 主要特性

### ✅ 完全支持 Cleveref
- 自动配置所有定理环境
- 支持 `\cref` 和 `\Cref` 命令
- 标签前缀自动映射
- 中文连接词支持

### ✅ 连续编号
- 定理、定义、引理等连续编号
- 默认启用（可通过选项禁用）
- 跨环境类型编号

### ✅ 新增环境
- `attention` 环境（11 种语言支持）
- 改进的 `example` 和 `problem`

### ✅ 增强接口
- 使用 `newtcbtheorem` 标准接口
- 支持多种参数组合
- 自动 cleveref 配置

## 使用方法

### 基本使用

```latex
\documentclass[cn,scheme=chinese,mode=fancy]{elegantbook}
\usepackage[nameinlink,capitalize]{cleveref}

\begin{document}
\begin{theorem}{Fermat 大定理}{fermat}
内容
\end{theorem}

参见 \cref{thm:fermat}
\end{document}
```

### 编译

```bash
xelatex test_elegantbook.tex
xelatex test_elegantbook.tex  # 第二次编译生成交叉引用
```

### 查看差异

```bash
# 查看详细差异
diff -u elegantbook.cls.original elegantbook.cls

# 或查看 changes.diff 文件
less changes.diff
```

## 技术信息

- **LaTeX 引擎**：XeLaTeX / LuaLaTeX
- **依赖包**：
  - tcolorbox (with theorems library)
  - cleveref (可选但推荐)
  - 其他 ElegantBook 原有依赖
- **版本兼容**：基于 ElegantBook v4.5
- **向后兼容**：100% 兼容原版

## 许可证

继承原 ElegantBook 许可证，可自由分发和修改。

## 作者信息

- **原作者**：ElegantLaTeX Team
- **增强版**：基于用户需求改进
- **维护**：社区维护

## 相关链接

- [ElegantBook 官方仓库](https://github.com/ElegantLaTeX/ElegantBook)
- [Cleveref 文档](https://ctan.org/pkg/cleveref)
- [Tcolorbox 文档](https://ctan.org/pkg/tcolorbox)

---

**最后更新**：2025/12/28
