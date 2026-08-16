# XRDIO — Universal X-Ray Diffraction File Reader 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![License: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Status: Stable](https://img.shields.io/badge/status-stable-brightgreen.svg)]()
[![Formats: 30+](https://img.shields.io/badge/formats-30%2B-orange.svg)]()

---

**XRDIO** is the definitive universal reader and converter for X-Ray Diffraction (XRD) and Spectroscopy files in Python. Written in **Pure Python** with `numpy` as the only mandatory dependency, it was designed to eliminate reliance on proprietary tools, obscure DLLs, and vendor-specific software — offering compatibility with more than **30 formats** from both legacy and modern instruments in a single lightweight library.

## Key Features 🚀

- **Smart Text Detection**: Reads tabular data from virtually any plain-text extension (`.xy`, `.csv`, `.tsv`, `.txt`, `.ras`, `.asc`, `.mdi`, `.gsa`, `.gsas`, `.edx`, `.udx`, `.2cl`, `.chi`, `.inel`, etc.) with automatic handling of European decimals (`10,4` vs `10.4`) and mixed delimiters. NumPy-vectorized engine for extreme speed.
- **Native Binary Parsers**: Full reverse-engineering of proprietary binary formats including **Bruker RAW (v1/v2/v4)**, **Philips PC-APD (RD/SD)**, **Canberra MCA/CNF**, and **Princeton Instruments SPE** — no external DLLs required.
- **Modern Formats (XML & HDF5/Synchrotron)**: Extracts diffractograms from **PANalytical XRDML**, **Bruker BRML** (ZIP+XML), **Freiberg XSYG**, and **NeXus/HDF5** (synchrotron facility files).
- **IUPAC & Legacy Standards**: Full support for **JCAMP-DX**, **Spectra XPS** (Ron Unwin / VGX-900), **Sietronics CPI**, **VAMAS ISO14976**, and **pdCIF** (including range-based format with IUCR error notation).
- **Analytical Module (`xrdio.processing`)**: Background removal with the **SNIP** algorithm (implemented in pure NumPy), **Moving Average** and **Savitzky-Golay** smoothing — all chainable via fluent API.
- **Multi-Scan Support**: Loads sequential measurements (temperature series, time-resolved, voltage sweeps) containing multiple data blocks, with full backward compatibility for single-scan code.
- **Built-in CLI**: Inspect metadata and batch-convert files directly from the terminal without writing a single line of Python.

## Installation 📦

### Prerequisites
XRDIO requires **Python 3.9+** and **NumPy**. No other dependencies are mandatory.

### Method 1: Install via pip (Recommended)

Installs the package and the `xrdio` command-line tool globally:
```bash
pip install .
```

To enable NeXus/HDF5 support (synchrotron files) or advanced statistical filters:
```bash
pip install ".[nexus]"            # Adds h5py for NeXus/HDF5
pip install ".[analytics]"        # Adds scipy for Savitzky-Golay
pip install ".[nexus,analytics]"  # Both
```

### Method 2: Without installation (virtual environments / development)

Install only the dependencies and run directly from the project directory:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## Usage — Command Line 💻

The XRDIO CLI is designed for quick workflows without any programming.

### Inspect file metadata
```bash
xrdio inspect my_data.xrdml
```
*Output:*
```
File:       my_data.xrdml
Format:     PANalytical XRDML
Sample:     Sample_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 points)
Step:       0.040°
```

### Convert to universal XY format
```bash
xrdio convert my_data.raw clean_output.xy
```
Extracts raw data from the proprietary binary and saves as plain-text `2Theta   Intensity`.

### Batch convert multiple files
```bash
xrdio convert *.raw --output-dir ./converted/
```
Batch mode converts to `.xy` by default. Use `--format xye` (or another supported writer
extension) to pick a different output format.

## Usage — Python API 🐍

Integrating XRDIO into any analysis pipeline (Matplotlib, scikit-learn, etc.) is immediate:

```python
import xrdio

# Read any file with a single call — automatic format detection
data = xrdio.read("old_philips_data.rd")

# Access metadata
print(f"Sample:      {data.sample_name}")
print(f"Points:      {data.n_points}")
print(f"Wavelength:  {data.wavelength:.5f} Å" if data.wavelength else "Wavelength:  N/A")
print(f"Range:       {data.start_angle:.3f}° – {data.end_angle:.3f}°")

# Direct access to NumPy arrays
two_theta = data.two_theta   # numpy.ndarray float32
intensity  = data.intensity  # numpy.ndarray float32
error      = data.error      # numpy.ndarray or None

# Export to any supported format
xrdio.write(data, "output.xye")   # .xy or .xye

# Chainable analytical pipeline
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)

# Multi-scan: access each scan individually
for i, scan in enumerate(data.scans):
    print(f"Scan {i}: {scan.n_points} points")
```

## Supported Formats 📋

The `xrdio` library has been tested with real files from the world's leading manufacturers and laboratories:

| Format | Extensions | Parser Type |
| :--- | :---: | :---: |
| Generic Text & CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ Smart Parse |
| Text Aliases (Rigaku, Inel, MDI, GSAS...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ Smart Parse |
| Bruker RAW (v1 / v2 / v4) | `.raw` | ✅ Binary Native |
| Bruker BRML (modern) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ Structured Text |
| Bruker SPC/PAR | `.spc` | ✅ Binary Native |
| PANalytical XRDML | `.xrdml` | ✅ XML Native |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ Binary Native |
| Philips UDF | `.udf` | ✅ Structured Text |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ Binary Native |
| Princeton Instruments WinSpec | `.spe` | ✅ Binary Native |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ Structured Text |
| VAMAS ISO14976 | `.vms` | ✅ Structured Text |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ Structured Text |
| Freiberg Instruments XSYG | `.xsyg` | ✅ XML Native |
| pdCIF (Powder Crystallography) | `.cif` | ✅ Structured Text |
| NeXus / HDF5 (Synchrotron) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (requires `h5py`) |
| Sietronics Sieray CPI | `.cpi` | ✅ Structured Text |

> **Note:** The `.xls` (legacy Microsoft Excel) format is not supported as it requires heavy proprietary libraries. For such data, we recommend exporting as `.csv` before using XRDIO.

## `XRDData` Object Structure

Every file read returns an `XRDData` object packed with information:

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `two_theta` | `np.ndarray` (float32) | Angular (2θ) or energy axis |
| `intensity` | `np.ndarray` (float32) | Counts / intensity |
| `error` | `np.ndarray` \| `None` | Per-channel standard deviation (when available) |
| `sample_name` | `str` | Sample name extracted from file |
| `wavelength` | `float` | Radiation wavelength (Å) |
| `start_angle` | `float` | Scan start angle |
| `end_angle` | `float` | Scan end angle |
| `step_size` | `float` | Angular step size |
| `metadata` | `dict` | Extra data (voltage, current, date, instrument model...) |
| `scans` | `list[Scan]` | List of individual scans (Multi-scan) |

## License and Authorship

This project is free software, distributed under the **GNU General Public License v3 (GPLv3)**. You are free to use, study, modify, and redistribute it — including for commercial purposes — provided that derivative works are distributed under the same license.

**Author:** Ricardo Cezar Volert  
**E-mail:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*Developed in Curitiba (State of Paraná — Brazil) at the Department of Physics of the Federal University of Paraná (UFPR).*