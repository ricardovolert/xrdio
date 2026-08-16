# XRDIO — Universell Läsare för Röntgendiffraktionsfiler 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![Licens: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Status: Stabil](https://img.shields.io/badge/status-stabil-brightgreen.svg)]()
[![Format: 30+](https://img.shields.io/badge/format-30%2B-orange.svg)]()

---

**XRDIO** är den definitiva universella läsaren och konverteraren för röntgendiffraktions- (XRD) och spektroskopifiler i Python. Skrivet i **ren Python** med `numpy` som enda obligatoriskt beroende, designades det för att eliminera behovet av proprietära verktyg, ogenomskinliga DLL:er och tillverkarspecifik programvara — med kompatibilitet för mer än **30 format** från äldre och moderna instrument i ett enda lättviktsbibliotek.

## Huvudfunktioner 🚀

- **Smart Textdetektering**: Läser tabelldata från nästan alla klartext-tillägg (`.xy`, `.csv`, `.tsv`, `.txt`, `.ras`, `.asc`, `.mdi`, `.gsa`, `.gsas`, `.edx`, `.inel`, `.chi` m.fl.) med automatisk hantering av europeiska decimaltecken (`10,4` vs `10.4`) och blandade avgränsare. NumPy-vektoriserad motor för extrem hastighet.
- **Inbyggda Binärparsers**: Fullständig omvänd ingenjörskonst för proprietära binärformat som **Bruker RAW (v1/v2/v4)**, **Philips PC-APD (RD/SD)**, **Canberra MCA/CNF** och **Princeton Instruments SPE** — inga externa DLL:er krävs.
- **Moderna Format (XML och HDF5/Synkrotron)**: Extraherar diffraktogram från **PANalytical XRDML**, **Bruker BRML** (ZIP+XML), **Freiberg XSYG** och **NeXus/HDF5** (synkrotronanläggningarnas filer).
- **IUPAC- och Äldre Standarder**: Fullt stöd för **JCAMP-DX**, **Spectra XPS** (Ron Unwin / VGX-900), **Sietronics CPI**, **VAMAS ISO14976** och **pdCIF** (inkl. range-based format med IUCR-felnotation).
- **Analytisk Modul (`xrdio.processing`)**: Bakgrundsavdragning med **SNIP**-algoritmen (implementerad i ren NumPy), utjämning via **glidande medelvärde** och **Savitzky-Golay** — allt kedjebart via fluent API.
- **Multi-Scan Stöd**: Läser in sekventiella mätningar (temperaturserie, tidsupplöst, spänningssvep) med flera datablock, med fullständig bakåtkompatibilitet för enkelskanskod.
- **Inbyggd CLI**: Granska metadata och konvertera filer i batch direkt från terminalen utan att skriva en enda rad Python.

## Installation 📦

### Förkunskaper
XRDIO kräver **Python 3.9+** och **NumPy**. Inga andra obligatoriska beroenden finns.

### Metod 1: Installera via pip (Rekommenderas)

Installerar paketet och kommandoradsverktyget `xrdio` globalt:
```bash
pip install .
```

För att aktivera stöd för NeXus/HDF5 (synkrotronfiler) eller avancerade statistiska filter:
```bash
pip install ".[nexus]"            # Lägger till h5py för NeXus/HDF5
pip install ".[analytics]"        # Lägger till scipy för Savitzky-Golay
pip install ".[nexus,analytics]"  # Båda
```

### Metod 2: Utan Installation (Virtuella Miljöer / Utveckling)

Installera bara beroendena och kör direkt från projektkatalogen:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## Användning — Kommandorad 💻

### Granska fil-metadata
```bash
xrdio inspect my_data.xrdml
```
*Utdata:*
```
File:       my_data.xrdml
Format:     PANalytical XRDML
Sample:     Sample_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 punkter)
Step:       0.040°
```

### Konvertera till universellt XY-format
```bash
xrdio convert my_data.raw clean_output.xy
```

### Batchkonvertera flera filer
```bash
xrdio convert *.raw --output-dir ./konverterade/
```

## Användning — Python API 🐍

```python
import xrdio

# Läs valfri fil med ett anrop — automatisk formatdetektering
data = xrdio.read("gamla_philips_data.rd")

print(f"Prov:        {data.sample_name}")
print(f"Datapunkter: {data.n_points}")
print(f"Våglängd:    {data.wavelength:.5f} Å" if data.wavelength else "Våglängd:    N/A")

# Direkt åtkomst till NumPy-arrayer
two_theta = data.two_theta
intensity  = data.intensity

# Exportera
xrdio.write(data, "output.xye")

# Kedjebart analytiskt pipeline
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## Format som Stöds 📋

| Format | Tillägg | Parser-typ |
| :--- | :---: | :---: |
| Generisk text och CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ Smart Parsning |
| Textalias (Rigaku, Inel, MDI...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ Smart Parsning |
| Bruker RAW (v1/v2/v4) | `.raw` | ✅ Inbyggd Binär |
| Bruker BRML (modern) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ Strukturerad Text |
| Bruker SPC/PAR | `.spc` | ✅ Inbyggd Binär |
| PANalytical XRDML | `.xrdml` | ✅ Inbyggd XML |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ Inbyggd Binär |
| Philips UDF | `.udf` | ✅ Strukturerad Text |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ Inbyggd Binär |
| Princeton Instruments WinSpec | `.spe` | ✅ Inbyggd Binär |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ Strukturerad Text |
| VAMAS ISO14976 | `.vms` | ✅ Strukturerad Text |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ Strukturerad Text |
| Freiberg Instruments XSYG | `.xsyg` | ✅ Inbyggd XML |
| pdCIF (Pulverkristallografi) | `.cif` | ✅ Strukturerad Text |
| NeXus / HDF5 (Synkrotron) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (kräver `h5py`) |
| Sietronics Sieray CPI | `.cpi` | ✅ Strukturerad Text |

## Licens och Upphovsman

Detta projekt är fri programvara, distribuerat under **GNU General Public License v3 (GPLv3)**. Du har rätt att använda, studera, modifiera och distribuera det — inklusive för kommersiella ändamål — förutsatt att härledda verk distribueras under samma licens.

**Författare:** Ricardo Cezar Volert  
**E-post:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*Utvecklat i Curitiba (delstaten Paraná — Brasilien) vid institutionen för fysik vid Federala Universitetet i Paraná (UFPR).*