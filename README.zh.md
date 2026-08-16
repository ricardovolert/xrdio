# XRDIO — X射线衍射文件通用读取器 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![许可证: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![状态: 稳定](https://img.shields.io/badge/状态-稳定-brightgreen.svg)]()
[![格式: 30+](https://img.shields.io/badge/格式-30%2B-orange.svg)]()

---

**XRDIO** 是 Python 领域最终的 X 射线衍射（XRD）及光谱文件通用读取器与转换器。以 **纯 Python** 编写，仅以 `numpy` 作为唯一必需依赖，旨在消除对专有工具、不透明 DLL 和厂商特定软件的依赖——通过单一轻量级库，兼容超过 **30 种** 来自传统及现代仪器的文件格式。

## 主要功能 🚀

- **智能文本检测**：能够读取几乎任何纯文本扩展名（`.xy`、`.csv`、`.tsv`、`.txt`、`.ras`、`.asc`、`.mdi`、`.gsa`、`.gsas`、`.edx`、`.inel`、`.chi` 等）的表格数据，自动处理欧洲小数点（`10,4` vs `10.4`）及混合分隔符。采用 NumPy 向量化引擎，速度极快。
- **原生二进制解析器**：对 **Bruker RAW (v1/v2/v4)**、**Philips PC-APD (RD/SD)**、**Canberra MCA/CNF** 和 **Princeton Instruments SPE** 等专有二进制格式进行完整逆向工程——无需外部 DLL。
- **现代格式（XML 与 HDF5/同步辐射）**：从 **PANalytical XRDML**、**Bruker BRML**（ZIP+XML）、**Freiberg XSYG** 和 **NeXus/HDF5**（大型粒子加速器文件）中提取衍射图谱。
- **IUPAC 与遗留标准**：完整支持 **JCAMP-DX**、**Spectra XPS**（Ron Unwin / VGX-900）、**Sietronics CPI**、**VAMAS ISO14976** 和 **pdCIF**（包含 IUCR 误差表示的 range-based 格式）。
- **分析模块（`xrdio.processing`）**：使用纯 NumPy 实现的 **SNIP** 算法进行背景扣除，**移动平均** 和 **Savitzky-Golay** 平滑处理——均可通过流式 API 链式调用。
- **多扫描支持**：加载包含多个数据块的连续测量（温度序列、时间分辨、电压扫描），与单扫描代码完全向后兼容。
- **内置 CLI**：无需编写任何 Python 代码，直接在终端检查元数据和批量转换文件。

## 安装 📦

### 前提条件
XRDIO 需要 **Python 3.9+** 和 **NumPy**。不需要其他强制依赖。

### 方法一：通过 pip 安装（推荐）

全局安装软件包及 `xrdio` 命令行工具：
```bash
pip install .
```

启用 NeXus/HDF5 支持（同步辐射文件）或高级统计滤波器：
```bash
pip install ".[nexus]"            # 为 NeXus/HDF5 添加 h5py
pip install ".[analytics]"        # 为 Savitzky-Golay 添加 scipy
pip install ".[nexus,analytics]"  # 两者均启用
```

### 方法二：不安装直接使用（虚拟环境 / 开发）

仅安装依赖，从项目目录直接运行：
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## 用法 — 命令行 💻

### 检查文件元数据
```bash
xrdio inspect my_data.xrdml
```
*输出：*
```
File:       my_data.xrdml
Format:     PANalytical XRDML
Sample:     Sample_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 points)
Step:       0.040°
```

### 转换为通用 XY 格式
```bash
xrdio convert my_data.raw clean_output.xy
```

### 批量转换多个文件
```bash
xrdio convert *.raw --output-dir ./converted/
```

## 用法 — Python API 🐍

```python
import xrdio

# 单次调用读取任何文件 — 自动格式检测
data = xrdio.read("old_philips_data.rd")

print(f"样品名称：  {data.sample_name}")
print(f"数据点数：  {data.n_points}")
print(f"波长：      {data.wavelength:.5f} Å" if data.wavelength else "波长：      N/A")

# 直接访问 NumPy 数组
two_theta = data.two_theta
intensity  = data.intensity

# 导出
xrdio.write(data, "output.xye")

# 可链式调用的分析管线
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## 支持的格式 📋

| 格式 | 扩展名 | 解析器类型 |
| :--- | :---: | :---: |
| 通用文本和 CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ 智能解析 |
| 文本别名（Rigaku、Inel、MDI...） | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ 智能解析 |
| Bruker RAW（v1/v2/v4） | `.raw` | ✅ 原生二进制 |
| Bruker BRML（现代格式） | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ 结构化文本 |
| Bruker SPC/PAR | `.spc` | ✅ 原生二进制 |
| PANalytical XRDML | `.xrdml` | ✅ 原生 XML |
| Philips PC-APD（v3/v5） | `.rd`, `.sd` | ✅ 原生二进制 |
| Philips UDF | `.udf` | ✅ 结构化文本 |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ 原生二进制 |
| Princeton Instruments WinSpec | `.spe` | ✅ 原生二进制 |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ 结构化文本 |
| VAMAS ISO14976 | `.vms` | ✅ 结构化文本 |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ 结构化文本 |
| Freiberg Instruments XSYG | `.xsyg` | ✅ 原生 XML |
| pdCIF（粉末晶体学） | `.cif` | ✅ 结构化文本 |
| NeXus / HDF5（同步辐射） | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5（需要 `h5py`） |
| Sietronics Sieray CPI | `.cpi` | ✅ 结构化文本 |

## 许可证与作者

本项目为自由软件，依据 **GNU 通用公共许可证 v3（GPLv3）** 分发。在派生作品以相同许可证分发的前提下，您可自由使用、研究、修改和再分发，包括商业用途。

**作者：** Ricardo Cezar Volert  
**电子邮件：** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes：** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID：** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*开发于巴西巴拉那州库里蒂巴，巴拉那联邦大学（UFPR）物理系。*
