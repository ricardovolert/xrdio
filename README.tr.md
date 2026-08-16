# XRDIO — X-Işını Kırınımı Dosyaları için Evrensel Okuyucu 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![Lisans: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Durum: Kararlı](https://img.shields.io/badge/durum-kararlı-brightgreen.svg)]()
[![Format: 30+](https://img.shields.io/badge/formatlar-30%2B-orange.svg)]()

---

**XRDIO**, Python için X-Işını Kırınımı (XRD) ve Spektroskopi dosyalarının nihai evrensel okuyucusu ve dönüştürücüsüdür. Tek zorunlu bağımlılık olarak `numpy` ile **saf Python** ile yazılmış olup, özel araçlara, belirsiz DLL'lere ve üreticiye özgü yazılımlara olan bağımlılığı ortadan kaldırmak için tasarlanmıştır — tek bir hafif kütüphanede **30'dan fazla** eski ve modern cihaz formatıyla uyumlu.

## Temel Özellikler 🚀

- **Akıllı Metin Tespiti**: Neredeyse her düz metin uzantısından (`.xy`, `.csv`, `.tsv`, `.txt`, `.ras`, `.asc`, `.mdi`, `.gsa`, `.gsas`, `.edx`, `.inel`, `.chi` vb.) tablosal verileri, Avrupa ondalık ayıraçlarını (`10,4` - `10.4`) ve karma sınırlayıcıları otomatik olarak işleyerek okur. Aşırı hız için NumPy vektörize motoru.
- **Yerel İkili Ayrıştırıcılar**: **Bruker RAW (v1/v2/v4)**, **Philips PC-APD (RD/SD)**, **Canberra MCA/CNF** ve **Princeton Instruments SPE** gibi özel ikili formatların tam tersine mühendisliği — harici DLL gerektirmez.
- **Modern Formatlar (XML ve HDF5/Senkrotron)**: **PANalytical XRDML**, **Bruker BRML** (ZIP+XML), **Freiberg XSYG** ve **NeXus/HDF5** (büyük parçacık hızlandırıcı dosyaları) dosyalarından kırınım örüntüleri çıkarır.
- **IUPAC ve Eski Standartlar**: **JCAMP-DX**, **Spectra XPS** (Ron Unwin / VGX-900), **Sietronics CPI**, **VAMAS ISO14976** ve **pdCIF** (IUCR hata gösterimi ile range-based format dahil) için tam destek.
- **Analitik Modül (`xrdio.processing`)**: Saf NumPy'da gerçekleştirilmiş **SNIP** algoritması ile arka plan kaldırma, **Hareketli Ortalama** ve **Savitzky-Golay** yumuşatma — tümü akıcı API aracılığıyla zincirlenebilir.
- **Çoklu Tarama Desteği**: Birden fazla veri bloğu içeren sıralı ölçümleri (sıcaklık serileri, time-resolved, voltaj taraması) yükler; tek tarama kodu ile tam geriye dönük uyumluluk.
- **Yerleşik CLI**: Tek bir Python satırı yazmadan doğrudan terminalden meta verileri inceleyin ve dosyaları toplu olarak dönüştürün.

## Kurulum 📦

### Ön Koşullar
XRDIO, **Python 3.9+** ve **NumPy** gerektirir. Başka zorunlu bağımlılık yoktur.

### Yöntem 1: pip ile Kurulum (Önerilen)

Paketi ve `xrdio` komut satırı aracını genel olarak yükler:
```bash
pip install .
```

NeXus/HDF5 desteği (senkrotron dosyaları) veya gelişmiş istatistiksel filtreler için:
```bash
pip install ".[nexus]"            # NeXus/HDF5 için h5py ekler
pip install ".[analytics]"        # Savitzky-Golay için scipy ekler
pip install ".[nexus,analytics]"  # İkisi de
```

### Yöntem 2: Kurulum Olmadan (Sanal Ortamlar / Geliştirme)

Yalnızca bağımlılıkları yükleyin ve proje dizininden doğrudan çalıştırın:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## Kullanım — Komut Satırı 💻

### Dosya meta verilerini inceleyin
```bash
xrdio inspect verim.xrdml
```
*Çıktı:*
```
File:       verim.xrdml
Format:     PANalytical XRDML
Sample:     Numune_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 nokta)
Step:       0.040°
```

### Evrensel XY formatına dönüştürün
```bash
xrdio convert verim.raw temiz_cikti.xy
```

### Birden fazla dosyayı toplu dönüştürün
```bash
xrdio convert *.raw --output-dir ./donusturulenler/
```

## Kullanım — Python API 🐍

```python
import xrdio

# Tek çağrıyla herhangi bir dosyayı okuyun — otomatik format algılama
data = xrdio.read("eski_philips_verisi.rd")

print(f"Numune:      {data.sample_name}")
print(f"Nokta sayısı: {data.n_points}")
print(f"Dalga boyu:  {data.wavelength:.5f} Å" if data.wavelength else "Dalga boyu:  N/A")

# NumPy dizilerine doğrudan erişim
two_theta = data.two_theta
intensity  = data.intensity

# Dışa aktarma
xrdio.write(data, "cikti.xye")

# Zincirlenebilir analitik ardışık düzen
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## Desteklenen Formatlar 📋

| Format | Uzantılar | Ayrıştırıcı Türü |
| :--- | :---: | :---: |
| Genel Metin ve CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ Akıllı Ayrıştırma |
| Metin Takma Adları (Rigaku, Inel, MDI...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ Akıllı Ayrıştırma |
| Bruker RAW (v1/v2/v4) | `.raw` | ✅ Yerel İkili |
| Bruker BRML (modern) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ Yapılandırılmış Metin |
| Bruker SPC/PAR | `.spc` | ✅ Yerel İkili |
| PANalytical XRDML | `.xrdml` | ✅ Yerel XML |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ Yerel İkili |
| Philips UDF | `.udf` | ✅ Yapılandırılmış Metin |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ Yerel İkili |
| Princeton Instruments WinSpec | `.spe` | ✅ Yerel İkili |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ Yapılandırılmış Metin |
| VAMAS ISO14976 | `.vms` | ✅ Yapılandırılmış Metin |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ Yapılandırılmış Metin |
| Freiberg Instruments XSYG | `.xsyg` | ✅ Yerel XML |
| pdCIF (Toz Kristalografisi) | `.cif` | ✅ Yapılandırılmış Metin |
| NeXus / HDF5 (Senkrotron) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (`h5py` gerekli) |
| Sietronics Sieray CPI | `.cpi` | ✅ Yapılandırılmış Metin |

## Lisans ve Yazarlık

Bu proje, **GNU Genel Kamu Lisansı v3 (GPLv3)** altında dağıtılan özgür yazılımdır. Türev çalışmaların aynı lisans altında dağıtılması koşuluyla, ticari amaçlar dahil olmak üzere serbestçe kullanabilir, inceleyebilir, değiştirebilir ve yeniden dağıtabilirsiniz.

**Yazar:** Ricardo Cezar Volert  
**E-posta:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*Brezilya, Paraná Eyaleti, Curitiba'daki Paraná Federal Üniversitesi (UFPR) Fizik Bölümü'nde geliştirilmiştir.*