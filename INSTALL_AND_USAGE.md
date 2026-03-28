# MinerU 安装与使用指南

MinerU 是一个将 PDF 转换为 Markdown / JSON 的开源工具，支持文字提取、表格识别、公式转 LaTeX、图片提取等功能。

---

## 环境要求

- macOS / Linux / Windows
- Python **3.10 – 3.13**（不支持 3.14+）
- 磁盘空间：≥ 20GB（首次运行会下载模型）
- 内存：≥ 16GB

---

## 安装步骤

### 1. 安装 Python 3.13

macOS 系统默认的 Python 版本可能过高（3.14+），需要手动安装 3.13：

```bash
brew install python@3.13
```

### 2. 创建虚拟环境

```bash
python3.13 -m venv ~/mineru-env
```

### 3. 激活虚拟环境

```bash
source ~/mineru-env/bin/activate
```

激活后命令行提示符会变为 `(mineru-env) %`。

### 4. 安装 MinerU

```bash
pip install uv
uv pip install -U "mineru[all]"
```

---

## 使用方法

### 激活环境（每次使用前执行）

```bash
source ~/mineru-env/bin/activate
```

### 基本命令

```bash
mineru -p <输入PDF路径> -o <输出目录>
```

**示例：**

```bash
mineru -p /Users/zhangzhen/Desktop/attention.pdf -o /Users/zhangzhen/mddir
```

### 常用参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `-p` | 输入 PDF 文件或目录路径（必填） | — |
| `-o` | 输出目录（必填） | — |
| `-b` | 后端：`pipeline` / `hybrid-auto-engine` / `vlm-auto-engine` | `hybrid-auto-engine` |
| `-l` | 语言：`ch` / `en` / `korean` 等 | `ch` |
| `-s` | 起始页（0-indexed） | 第一页 |
| `-e` | 结束页（0-indexed） | 最后一页 |
| `-f` | 是否识别公式：`true` / `false` | `true` |
| `-t` | 是否识别表格：`true` / `false` | `true` |
| `-d` | 设备：`cpu` / `cuda` / `mps` | 自动检测 |

### 常用场景示例

```bash
# 无 GPU / CPU 模式（兼容性最好，速度较慢）
mineru -p your.pdf -o ./output -b pipeline

# 英文文档
mineru -p your.pdf -o ./output -l en

# 只转换第 1–10 页（0-indexed）
mineru -p your.pdf -o ./output -s 0 -e 9

# 关闭公式识别（加快速度）
mineru -p your.pdf -o ./output -f false

# 批量转换整个目录下所有 PDF
mineru -p /path/to/pdf_folder/ -o ./output
```

### 国内用户加速模型下载

首次运行会自动下载约 20GB 的模型文件，国内网络建议切换到 ModelScope 源：

```bash
export MINERU_MODEL_SOURCE=modelscope
mineru -p your.pdf -o ./output
```

---

## 输出文件说明

转换完成后，输出目录下会生成以下文件：

```
mddir/
└── attention/
    ├── attention.md            # 主结果：Markdown 格式文档
    ├── attention_content_list.json   # 结构化内容列表（按页）
    ├── attention_middle.json         # 详细中间数据
    ├── images/                       # 提取的图片
    └── attention_layout.pdf          # 布局可视化（用于质量核验）
```

---

## 网页版界面（Gradio Web UI）

除命令行外，MinerU 也提供浏览器操作界面，无需敲命令，适合临时转换文件。

### 启动

```bash
source ~/mineru-env/bin/activate
mineru-gradio
```

启动成功后在浏览器打开 `http://127.0.0.1:7860`，即可上传 PDF、选择参数、下载结果。

### macOS 代理冲突问题

如果启动报错：
```
Exception: Couldn't start the app because 'http://127.0.0.1:7860/...' failed (code 502).
Check your network or proxy settings to ensure localhost is accessible.
```

原因是系统代理（如 Clash TUN 模式）拦截了本地请求。需要在 Clash 的 **"排除自定义网段"** 中添加：
```
127.0.0.0/8
```

> **注意**：系统代理的绕过规则和 TUN 模式的绕过规则是独立的，两处都需要配置。

配置后重新加载 Clash 配置即可。

---

## 退出虚拟环境

```bash
deactivate
```
