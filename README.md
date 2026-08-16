# XRDIO — Universal X-Ray Diffraction File Reader 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![License: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Status: Estável](https://img.shields.io/badge/status-estável-brightgreen.svg)]()
[![Formatos: 30+](https://img.shields.io/badge/formatos-30%2B-orange.svg)]()

---

O **XRDIO** é o leitor e conversor universal de arquivos de Difração de Raios X (XRD) e Espectroscopia definitivo para Python. Escrito em **Puro Python** com `numpy` como única dependência obrigatória, foi arquitetado para eliminar a dependência de ferramentas proprietárias, DLLs obscuras e softwares de fabricante — oferecendo compatibilidade com mais de **30 formatos** do mercado legado e atual em uma única biblioteca leve.

## Principais Recursos 🚀

- **Detecção Inteligente de Texto**: Lê matrizes tabulares em praticamente qualquer extensão de texto puro (`.xy`, `.csv`, `.tsv`, `.txt`, `.ras`, `.asc`, `.mdi`, `.gsa`, `.gsas`, `.edx`, `.udx`, `.2cl`, `.chi`, `.inel`, etc.) com tratamento automático de decimais europeus (`10,4` vs `10.4`) e delimitadores variados. Motor vetorizado em NumPy para velocidade extrema.
- **Parsers Binários Nativos**: Engenharia reversa completa para abrir binários proprietários como **Bruker RAW (v1/v2/v4)**, **Philips PC-APD (RD/SD)**, **Canberra MCA/CNF** e **Princeton Instruments SPE** — sem dependência de DLLs externas.
- **Formatos Modernos (XML e HDF5/Síncrotron)**: Extrai difratogramas de **PANalytical XRDML**, **Bruker BRML** (ZIP+XML), **Freiberg XSYG** e **NeXus/HDF5** (arquivos de grandes aceleradores de partículas).
- **Padrões IUPAC e Legado**: Suporte completo a **JCAMP-DX**, **Spectra XPS** (Ron Unwin / VGX-900), **Sietronics CPI**, **VAMAS ISO14976** e **pdCIF** (incluindo o formato range-based com notação de erro IUCR).
- **Módulo Analítico (`xrdio.processing`)**: Remoção de background com o algoritmo **SNIP** (implementado em puro NumPy), suavização por **Média Móvel** e **Savitzky-Golay** — tudo encadeável via API fluente.
- **Suporte a Multi-Scan**: Carrega medições em série (temperatura, *time-resolved*, tensão) contendo múltiplos blocos de dados, com total retrocompatibilidade com código de scan único.
- **CLI Integrada**: Inspecione metadados e converta arquivos em massa diretamente do terminal, sem escrever uma linha de código Python.

## Instalação 📦

### Pré-requisito
O XRDIO requer **Python 3.9+** e **NumPy**. Nenhuma outra dependência é obrigatória.

### Método 1: Instalação via pip (Recomendada)

Instala o pacote e a ferramenta de linha de comando `xrdio` globalmente:
```bash
pip install .
```

Para habilitar suporte a NeXus/HDF5 (arquivos de síncrotron) ou filtros estatísticos avançados:
```bash
pip install ".[nexus]"       # Adiciona h5py para NeXus/HDF5
pip install ".[analytics]"   # Adiciona scipy para Savitzky-Golay
pip install ".[nexus,analytics]"  # Ambos
```

### Método 2: Sem instalação (ambientes virtuais / desenvolvimento)

Instale apenas as dependências e use direto do diretório do projeto:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## Como Usar — Linha de Comando 💻

A CLI do XRDIO foi projetada para fluxos de trabalho rápidos, sem necessidade de programação.

### Inspecionar metadados de um arquivo
```bash
xrdio inspect meu_dado.xrdml
```
*Saída:*
```
File:       meu_dado.xrdml
Format:     PANalytical XRDML
Sample:     Amostra_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 pontos)
Step:       0.040°
```

### Converter para formato XY universal
```bash
xrdio convert meu_dado.raw saida_limpa.xy
```
Extrai os dados do binário e salva como `2Theta   Intensity` em texto plano.

### Converter múltiplos arquivos em lote
```bash
xrdio convert *.raw --output-dir ./convertidos/
```
Por padrão, o modo em lote converte para `.xy`. Use `--format xye` (ou outra extensão de
escrita suportada) para escolher outro formato de saída.

## Como Usar — Python API 🐍

Integrar o XRDIO em qualquer pipeline de análise (Matplotlib, scikit-learn, etc.) é imediato:

```python
import xrdio

# Lê qualquer arquivo com uma única chamada — detecção automática de formato
data = xrdio.read("dados_antigos_philips.rd")

# Acesso simples aos metadados
print(f"Amostra:           {data.sample_name}")
print(f"Pontos lidos:      {data.n_points}")
print(f"Comprimento de onda: {data.wavelength:.5f} Å" if data.wavelength else "Comprimento de onda: N/A")
print(f"Intervalo angular: {data.start_angle:.3f}° – {data.end_angle:.3f}°")

# Vetores NumPy diretamente acessíveis
two_theta = data.two_theta   # numpy.ndarray float32
intensity  = data.intensity  # numpy.ndarray float32
error      = data.error      # numpy.ndarray ou None

# Exportar para qualquer formato suportado
xrdio.write(data, "saida.xye")  # .xy ou .xye

# Pipeline analítico encadeável
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)

# Multi-scan: acesso individual a cada scan
for i, scan in enumerate(data.scans):
    print(f"Scan {i}: {scan.n_points} pontos")
```

## Formatos Suportados 📋

A biblioteca `xrdio` foi testada com arquivos reais dos principais fabricantes e laboratórios do mundo:

| Formato | Extensões | Tipo de Parser |
| :--- | :---: | :---: |
| Texto Genérico & CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ Smart Parse |
| Aliases de Texto (Rigaku, Inel, MDI, GSAS...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ Smart Parse |
| Bruker RAW (v1 / v2 / v4) | `.raw` | ✅ Binário Nativo |
| Bruker BRML (moderno) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ Texto Estruturado |
| Bruker SPC/PAR | `.spc` | ✅ Binário Nativo |
| PANalytical XRDML | `.xrdml` | ✅ XML Nativo |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ Binário Nativo |
| Philips UDF | `.udf` | ✅ Texto Estruturado |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ Binário Nativo |
| Princeton Instruments WinSpec | `.spe` | ✅ Binário Nativo |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ Texto Estruturado |
| VAMAS ISO14976 | `.vms` | ✅ Texto Estruturado |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ Texto Estruturado |
| Freiberg Instruments XSYG | `.xsyg` | ✅ XML Nativo |
| pdCIF (Powder Crystallography) | `.cif` | ✅ Texto Estruturado |
| NeXus / HDF5 (Síncrotron) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (requer `h5py`) |
| Sietronics Sieray CPI | `.cpi` | ✅ Texto Estruturado |

> **Nota:** O formato `.xls` (Microsoft Excel legado) não é suportado por exigir bibliotecas proprietárias pesadas. Para esses dados, recomendamos exportar como `.csv` antes de usar o XRDIO.

## Estrutura do Objeto `XRDData`

Cada arquivo lido retorna um objeto `XRDData` rico em informações:

| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `two_theta` | `np.ndarray` (float32) | Eixo angular (2θ) ou energético |
| `intensity` | `np.ndarray` (float32) | Contagens / intensidade |
| `error` | `np.ndarray` \| `None` | Desvio padrão por canal (quando disponível) |
| `sample_name` | `str` | Nome da amostra extraído do arquivo |
| `wavelength` | `float` | Comprimento de onda da radiação (Å) |
| `start_angle` | `float` | Ângulo inicial da varredura |
| `end_angle` | `float` | Ângulo final da varredura |
| `step_size` | `float` | Passo angular |
| `metadata` | `dict` | Dados extras (tensão, corrente, data, modelo do equipamento...) |
| `scans` | `list[Scan]` | Lista de scans individuais (Multi-scan) |

## Licença e Autoria

Este projeto é software livre, distribuído sob a **Licença Pública Geral GNU v3 (GPLv3)**. Você tem o direito de usá-lo, estudá-lo, modificá-lo e redistribuí-lo, inclusive para fins comerciais, desde que as obras derivadas sejam distribuídas sob a mesma licença.

**Autor:** Ricardo Cezar Volert  
**E-mail:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*Desenvolvido em Curitiba (Estado do Paraná — Brasil) no Departamento de Física da Universidade Federal do Paraná (UFPR).*
