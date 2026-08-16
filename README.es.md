# XRDIO — Lector Universal de Archivos de Difracción de Rayos X 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![Licencia: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Estado: Estable](https://img.shields.io/badge/estado-estable-brightgreen.svg)]()
[![Formatos: 30+](https://img.shields.io/badge/formatos-30%2B-orange.svg)]()

---

**XRDIO** es el lector y conversor universal definitivo de archivos de Difracción de Rayos X (DRX) y Espectroscopia en Python. Escrito en **Python puro** con `numpy` como única dependencia obligatoria, fue diseñado para eliminar la dependencia de herramientas propietarias, DLLs oscuras y software de fabricante — ofreciendo compatibilidad con más de **30 formatos** del mercado heredado y actual en una sola biblioteca ligera.

## Características Principales 🚀

- **Detección Inteligente de Texto**: Lee matrices tabulares de prácticamente cualquier extensión de texto plano (`.xy`, `.csv`, `.tsv`, `.txt`, `.ras`, `.asc`, `.mdi`, `.gsa`, `.gsas`, `.edx`, `.udx`, `.inel`, `.chi`, etc.) con manejo automático de decimales europeos (`10,4` frente a `10.4`) y delimitadores mixtos. Motor vectorizado en NumPy para velocidad extrema.
- **Parsers Binarios Nativos**: Ingeniería inversa completa de formatos binarios propietarios como **Bruker RAW (v1/v2/v4)**, **Philips PC-APD (RD/SD)**, **Canberra MCA/CNF** y **Princeton Instruments SPE** — sin necesidad de DLLs externas.
- **Formatos Modernos (XML y HDF5/Sincrotrón)**: Extrae difractogramas de **PANalytical XRDML**, **Bruker BRML** (ZIP+XML), **Freiberg XSYG** y **NeXus/HDF5** (archivos de grandes aceleradores de partículas).
- **Estándares IUPAC y Heredados**: Soporte completo para **JCAMP-DX**, **Spectra XPS** (Ron Unwin / VGX-900), **Sietronics CPI**, **VAMAS ISO14976** y **pdCIF** (incluyendo el formato range-based con notación de error IUCR).
- **Módulo Analítico (`xrdio.processing`)**: Eliminación de fondo con el algoritmo **SNIP** (implementado en NumPy puro), suavizado por **Media Móvil** y **Savitzky-Golay** — todo encadenable vía API fluida.
- **Soporte Multi-Scan**: Carga mediciones en serie (temperatura, *time-resolved*, tensión) con múltiples bloques de datos, con retrocompatibilidad total para código de scan único.
- **CLI Integrada**: Inspeccione metadatos y convierta archivos en lote directamente desde la terminal, sin escribir una sola línea de Python.

## Instalación 📦

### Prerrequisitos
XRDIO requiere **Python 3.9+** y **NumPy**. No se requieren otras dependencias.

### Método 1: Instalación vía pip (Recomendado)

Instala el paquete y la herramienta de línea de comandos `xrdio` globalmente:
```bash
pip install .
```

Para habilitar soporte NeXus/HDF5 (archivos de sincrotrón) o filtros estadísticos avanzados:
```bash
pip install ".[nexus]"            # Añade h5py para NeXus/HDF5
pip install ".[analytics]"        # Añade scipy para Savitzky-Golay
pip install ".[nexus,analytics]"  # Ambos
```

### Método 2: Sin instalación (entornos virtuales / desarrollo)

Instale solo las dependencias y ejecute desde el directorio del proyecto:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## Uso — Línea de Comandos 💻

### Inspeccionar metadatos de un archivo
```bash
xrdio inspect mis_datos.xrdml
```
*Salida:*
```
File:       mis_datos.xrdml
Format:     PANalytical XRDML
Sample:     Muestra_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 puntos)
Step:       0.040°
```

### Convertir al formato XY universal
```bash
xrdio convert mis_datos.raw salida_limpia.xy
```

### Conversión en lote
```bash
xrdio convert *.raw --output-dir ./convertidos/
```

## Uso — Python API 🐍

```python
import xrdio

# Lee cualquier archivo con una sola llamada — detección automática de formato
data = xrdio.read("datos_antiguos_philips.rd")

print(f"Muestra:       {data.sample_name}")
print(f"Puntos leídos: {data.n_points}")
print(f"Longitud de onda: {data.wavelength:.5f} Å" if data.wavelength else "Longitud de onda: N/A")

# Vectores NumPy directamente accesibles
two_theta = data.two_theta
intensity  = data.intensity

# Exportar
xrdio.write(data, "salida.xye")

# Pipeline analítico encadenable
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## Formatos Soportados 📋

| Formato | Extensiones | Tipo de Parser |
| :--- | :---: | :---: |
| Texto Genérico & CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ Smart Parse |
| Aliases de Texto (Rigaku, Inel, MDI...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ Smart Parse |
| Bruker RAW (v1 / v2 / v4) | `.raw` | ✅ Binario Nativo |
| Bruker BRML (moderno) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ Texto Estructurado |
| Bruker SPC/PAR | `.spc` | ✅ Binario Nativo |
| PANalytical XRDML | `.xrdml` | ✅ XML Nativo |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ Binario Nativo |
| Philips UDF | `.udf` | ✅ Texto Estructurado |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ Binario Nativo |
| Princeton Instruments WinSpec | `.spe` | ✅ Binario Nativo |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ Texto Estructurado |
| VAMAS ISO14976 | `.vms` | ✅ Texto Estructurado |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ Texto Estructurado |
| Freiberg Instruments XSYG | `.xsyg` | ✅ XML Nativo |
| pdCIF (Crystallografía de Polvo) | `.cif` | ✅ Texto Estructurado |
| NeXus / HDF5 (Sincrotrón) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (requiere `h5py`) |
| Sietronics Sieray CPI | `.cpi` | ✅ Texto Estructurado |

## Licencia y Autoría

Este proyecto es software libre, distribuido bajo la **Licencia Pública General GNU v3 (GPLv3)**. Usted tiene el derecho de usarlo, estudiarlo, modificarlo y redistribuirlo — incluso para fines comerciales — siempre que las obras derivadas se distribuyan bajo la misma licencia.

**Autor:** Ricardo Cezar Volert  
**Correo electrónico:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*Desarrollado en Curitiba (Estado de Paraná — Brasil) en el Departamento de Física de la Universidad Federal de Paraná (UFPR).*