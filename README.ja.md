# XRDIO — X線回折ファイルユニバーサルリーダー 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![ライセンス: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![ステータス: 安定](https://img.shields.io/badge/ステータス-安定-brightgreen.svg)]()
[![対応フォーマット: 30+](https://img.shields.io/badge/フォーマット-30%2B-orange.svg)]()

---

**XRDIO** は、Python 向けのX線回折 (XRD) および分光法ファイルの決定版ユニバーサルリーダー・コンバーターです。必須依存関係として `numpy` のみを使用した **Pure Python** で書かれており、プロプライエタリなツール、不透明なDLL、メーカー固有のソフトウェアへの依存をなくすことを目的として設計されています。単一の軽量ライブラリで、レガシーおよび現代の機器の **30以上のフォーマット** に対応しています。

## 主な機能 🚀

- **スマートテキスト検出**: ほぼすべてのプレーンテキスト拡張子 (`.xy`、`.csv`、`.tsv`、`.txt`、`.ras`、`.asc`、`.mdi`、`.gsa`、`.gsas`、`.edx`、`.inel`、`.chi` など) の表形式データを、ヨーロッパ式小数点 (`10,4` vs `10.4`) や混合区切り文字を自動処理して読み込みます。NumPy ベクトル化エンジンにより極めて高速に動作します。
- **ネイティブバイナリパーサー**: **Bruker RAW (v1/v2/v4)**、**Philips PC-APD (RD/SD)**、**Canberra MCA/CNF**、**Princeton Instruments SPE** などのプロプライエタリなバイナリフォーマットの完全なリバースエンジニアリングを実施。外部DLL不要。
- **モダンフォーマット (XML および HDF5/放射光)**: **PANalytical XRDML**、**Bruker BRML** (ZIP+XML)、**Freiberg XSYG**、**NeXus/HDF5** (放射光施設ファイル) から回折パターンを抽出します。
- **IUPAC・レガシー標準**: **JCAMP-DX**、**Spectra XPS** (Ron Unwin / VGX-900)、**Sietronics CPI**、**VAMAS ISO14976**、**pdCIF** (IUCR誤差表記のrange-based形式を含む) を完全サポート。
- **分析モジュール (`xrdio.processing`)**: 純粋なNumPyで実装された **SNIP** アルゴリズムによるバックグラウンド除去、**移動平均** および **Savitzky-Golay** スムージング — すべてフルエントAPIでチェーン可能。
- **マルチスキャン対応**: 温度シリーズ・時間分解・電圧スイープなど、複数データブロックを含む連続測定を読み込み。シングルスキャンコードとの完全な後方互換性を維持。
- **組み込みCLI**: Pythonを一行も書かずに、ターミナルから直接メタデータを確認したりファイルを一括変換できます。

## インストール 📦

### 前提条件
XRDIO は **Python 3.9+** および **NumPy** が必要です。それ以外の依存関係は必須ではありません。

### 方法1: pip でインストール (推奨)

パッケージと `xrdio` コマンドラインツールをグローバルにインストール:
```bash
pip install .
```

NeXus/HDF5 サポート (放射光ファイル) や高度な統計フィルターを有効にするには:
```bash
pip install ".[nexus]"            # NeXus/HDF5 用に h5py を追加
pip install ".[analytics]"        # Savitzky-Golay 用に scipy を追加
pip install ".[nexus,analytics]"  # 両方
```

### 方法2: インストールなしで使用 (仮想環境 / 開発)

依存関係のみインストールし、プロジェクトディレクトリから直接実行:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## 使い方 — コマンドライン 💻

### ファイルのメタデータを確認する
```bash
xrdio inspect my_data.xrdml
```
*出力例:*
```
File:       my_data.xrdml
Format:     PANalytical XRDML
Sample:     Sample_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 points)
Step:       0.040°
```

### ユニバーサルXY形式に変換する
```bash
xrdio convert my_data.raw clean_output.xy
```

### 複数ファイルの一括変換
```bash
xrdio convert *.raw --output-dir ./converted/
```

## 使い方 — Python API 🐍

```python
import xrdio

# 1回の呼び出しで任意のファイルを読み込む — 自動フォーマット検出
data = xrdio.read("old_philips_data.rd")

print(f"試料名:         {data.sample_name}")
print(f"データ点数:     {data.n_points}")
print(f"波長:           {data.wavelength:.5f} Å" if data.wavelength else "波長:           N/A")

# NumPy 配列に直接アクセス
two_theta = data.two_theta
intensity  = data.intensity

# エクスポート
xrdio.write(data, "output.xye")

# チェーン可能な分析パイプライン
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## 対応フォーマット 📋

| フォーマット | 拡張子 | パーサーの種類 |
| :--- | :---: | :---: |
| 汎用テキスト & CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ スマート解析 |
| テキストエイリアス (Rigaku, Inel, MDI...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ スマート解析 |
| Bruker RAW (v1 / v2 / v4) | `.raw` | ✅ バイナリネイティブ |
| Bruker BRML (モダン) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ 構造化テキスト |
| Bruker SPC/PAR | `.spc` | ✅ バイナリネイティブ |
| PANalytical XRDML | `.xrdml` | ✅ XML ネイティブ |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ バイナリネイティブ |
| Philips UDF | `.udf` | ✅ 構造化テキスト |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ バイナリネイティブ |
| Princeton Instruments WinSpec | `.spe` | ✅ バイナリネイティブ |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ 構造化テキスト |
| VAMAS ISO14976 | `.vms` | ✅ 構造化テキスト |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ 構造化テキスト |
| Freiberg Instruments XSYG | `.xsyg` | ✅ XML ネイティブ |
| pdCIF (粉末結晶学) | `.cif` | ✅ 構造化テキスト |
| NeXus / HDF5 (放射光) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (`h5py` 必須) |
| Sietronics Sieray CPI | `.cpi` | ✅ 構造化テキスト |

## ライセンスと著作権

本プロジェクトはフリーソフトウェアであり、**GNU 一般公衆利用許諾書 v3 (GPLv3)** の下で配布されています。派生作品が同じライセンスの下で配布される限り、商業目的を含め、自由に使用・研究・変更・再配布することができます。

**著者:** Ricardo Cezar Volert  
**メール:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*ブラジル、パラナ州クリチバにあるパラナ連邦大学 (UFPR) 物理学科にて開発。*
