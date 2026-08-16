# XRDIO — एक्स-रे विवर्तन फ़ाइल यूनिवर्सल रीडर 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![लाइसेंस: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![स्थिति: स्थिर](https://img.shields.io/badge/स्थिति-स्थिर-brightgreen.svg)]()
[![प्रारूप: 30+](https://img.shields.io/badge/प्रारूप-30%2B-orange.svg)]()

---

**XRDIO** Python के लिए एक्स-रे विवर्तन (XRD) और स्पेक्ट्रोस्कोपी फ़ाइलों का निश्चित सार्वभौमिक रीडर और कनवर्टर है। `numpy` को एकमात्र अनिवार्य निर्भरता के रूप में उपयोग करते हुए **शुद्ध Python** में लिखा गया, यह स्वामित्व वाले उपकरणों, अस्पष्ट DLL और विक्रेता-विशिष्ट सॉफ़्टवेयर पर निर्भरता समाप्त करने के लिए बनाया गया है — एक हल्की लाइब्रेरी में विरासत और आधुनिक उपकरणों के **30 से अधिक प्रारूपों** के साथ संगतता प्रदान करता है।

## मुख्य विशेषताएं 🚀

- **स्मार्ट टेक्स्ट डिटेक्शन**: लगभग किसी भी प्लेन-टेक्स्ट एक्सटेंशन (`.xy`, `.csv`, `.tsv`, `.txt`, `.ras`, `.asc`, `.mdi`, `.gsa`, `.gsas`, `.edx`, `.inel`, `.chi` आदि) से सारणीबद्ध डेटा पढ़ता है, यूरोपीय दशमलव (`10,4` बनाम `10.4`) और मिश्रित सीमांककों को स्वचालित रूप से संभालता है। अत्यधिक गति के लिए NumPy-वेक्टरकृत इंजन।
- **नेटिव बाइनरी पार्सर**: **Bruker RAW (v1/v2/v4)**, **Philips PC-APD (RD/SD)**, **Canberra MCA/CNF** और **Princeton Instruments SPE** जैसे स्वामित्व बाइनरी प्रारूपों का पूर्ण रिवर्स-इंजीनियरिंग — बाहरी DLL की आवश्यकता नहीं।
- **आधुनिक प्रारूप (XML और HDF5/सिंक्रोट्रॉन)**: **PANalytical XRDML**, **Bruker BRML** (ZIP+XML), **Freiberg XSYG** और **NeXus/HDF5** (बड़े कण त्वरक फ़ाइलें) से विवर्तन पैटर्न निकालता है।
- **IUPAC और विरासत मानक**: **JCAMP-DX**, **Spectra XPS** (Ron Unwin / VGX-900), **Sietronics CPI**, **VAMAS ISO14976** और **pdCIF** (IUCR त्रुटि संकेतन के साथ range-based प्रारूप सहित) के लिए पूर्ण समर्थन।
- **विश्लेषणात्मक मॉड्यूल (`xrdio.processing`)**: शुद्ध NumPy में लागू **SNIP** एल्गोरिदम से बैकग्राउंड हटाना, **मूविंग एवरेज** और **Savitzky-Golay** स्मूदिंग — सभी फ्लुएंट API के माध्यम से श्रृंखलाबद्ध।
- **मल्टी-स्कैन समर्थन**: कई डेटा ब्लॉक (तापमान श्रृंखला, time-resolved, वोल्टेज स्वीप) वाले अनुक्रमिक मापों को लोड करता है, सिंगल-स्कैन कोड के साथ पूर्ण पश्चगामी संगतता के साथ।
- **बिल्ट-इन CLI**: Python की एक भी पंक्ति लिखे बिना सीधे टर्मिनल से मेटाडेटा जांचें और फ़ाइलें बैच-कनवर्ट करें।

## इंस्टॉलेशन 📦

### पूर्वापेक्षाएं
XRDIO को **Python 3.9+** और **NumPy** की आवश्यकता है। कोई अन्य अनिवार्य निर्भरता नहीं है।

### विधि 1: pip से इंस्टॉल करें (अनुशंसित)

पैकेज और `xrdio` कमांड-लाइन टूल को वैश्विक रूप से इंस्टॉल करें:
```bash
pip install .
```

NeXus/HDF5 समर्थन (सिंक्रोट्रॉन फ़ाइलें) या उन्नत सांख्यिकीय फ़िल्टर सक्षम करने के लिए:
```bash
pip install ".[nexus]"            # NeXus/HDF5 के लिए h5py जोड़ता है
pip install ".[analytics]"        # Savitzky-Golay के लिए scipy जोड़ता है
pip install ".[nexus,analytics]"  # दोनों
```

### विधि 2: इंस्टॉलेशन के बिना (वर्चुअल वातावरण / विकास)

केवल निर्भरताएं इंस्टॉल करें और प्रोजेक्ट डायरेक्टरी से सीधे चलाएं:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## उपयोग — कमांड लाइन 💻

### फ़ाइल मेटाडेटा जांचें
```bash
xrdio inspect my_data.xrdml
```

### यूनिवर्सल XY प्रारूप में कनवर्ट करें
```bash
xrdio convert my_data.raw clean_output.xy
```

### एकाधिक फ़ाइलें बैच कनवर्ट करें
```bash
xrdio convert *.raw --output-dir ./converted/
```

## उपयोग — Python API 🐍

```python
import xrdio

# एकल कॉल से कोई भी फ़ाइल पढ़ें — स्वचालित प्रारूप पहचान
data = xrdio.read("old_philips_data.rd")

print(f"नमूना:      {data.sample_name}")
print(f"बिंदु:      {data.n_points}")
print(f"तरंगदैर्ध्य: {data.wavelength:.5f} Å" if data.wavelength else "तरंगदैर्ध्य: N/A")

# NumPy सरणियों तक सीधी पहुंच
two_theta = data.two_theta
intensity  = data.intensity

# निर्यात करें
xrdio.write(data, "output.xye")

# श्रृंखलाबद्ध विश्लेषणात्मक पाइपलाइन
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## समर्थित प्रारूप 📋

| प्रारूप | एक्सटेंशन | पार्सर प्रकार |
| :--- | :---: | :---: |
| जेनेरिक टेक्स्ट और CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ स्मार्ट पार्स |
| टेक्स्ट उपनाम (Rigaku, Inel, MDI...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ स्मार्ट पार्स |
| Bruker RAW (v1/v2/v4) | `.raw` | ✅ नेटिव बाइनरी |
| Bruker BRML (आधुनिक) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ संरचित टेक्स्ट |
| Bruker SPC/PAR | `.spc` | ✅ नेटिव बाइनरी |
| PANalytical XRDML | `.xrdml` | ✅ नेटिव XML |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ नेटिव बाइनरी |
| Philips UDF | `.udf` | ✅ संरचित टेक्स्ट |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ नेटिव बाइनरी |
| Princeton Instruments WinSpec | `.spe` | ✅ नेटिव बाइनरी |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ संरचित टेक्स्ट |
| VAMAS ISO14976 | `.vms` | ✅ संरचित टेक्स्ट |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ संरचित टेक्स्ट |
| Freiberg Instruments XSYG | `.xsyg` | ✅ नेटिव XML |
| pdCIF (पाउडर क्रिस्टलोग्राफी) | `.cif` | ✅ संरचित टेक्स्ट |
| NeXus / HDF5 (सिंक्रोट्रॉन) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (`h5py` आवश्यक) |
| Sietronics Sieray CPI | `.cpi` | ✅ संरचित टेक्स्ट |

## लाइसेंस और लेखकत्व

यह प्रोजेक्ट मुक्त सॉफ़्टवेयर है, **GNU सामान्य सार्वजनिक लाइसेंस v3 (GPLv3)** के तहत वितरित। व्युत्पन्न कार्यों को उसी लाइसेंस के तहत वितरित करने की शर्त पर, आप इसे वाणिज्यिक उद्देश्यों सहित स्वतंत्र रूप से उपयोग, अध्ययन, संशोधित और पुनर्वितरित कर सकते हैं।

**लेखक:** Ricardo Cezar Volert  
**ई-मेल:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*कुरितिबा (पराना राज्य — ब्राज़ील) में पराना संघीय विश्वविद्यालय (UFPR) के भौतिकी विभाग में विकसित।*
