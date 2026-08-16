# XRDIO — قارئ عالمي لملفات حيود الأشعة السينية 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![الرخصة: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![الحالة: مستقر](https://img.shields.io/badge/الحالة-مستقر-brightgreen.svg)]()
[![الصيغ: 30+](https://img.shields.io/badge/الصيغ-30%2B-orange.svg)]()

---

**XRDIO** هو القارئ والمحوّل الشامل النهائي لملفات حيود الأشعة السينية (XRD) والتحليل الطيفي في Python. مكتوب بـ **Python نقي** مع `numpy` كتبعيّة إلزامية وحيدة، صُمِّم للقضاء على الاعتماد على الأدوات التجارية، ومكتبات DLL المبهمة، والبرامج الخاصة بالشركات المصنّعة — مع دعم أكثر من **30 صيغة** من الأجهزة القديمة والحديثة في مكتبة خفيفة واحدة.

## الميزات الرئيسية 🚀

- **الكشف الذكي عن النص**: يقرأ البيانات الجدولية من تقريباً أي امتداد نص عادي (`.xy`، `.csv`، `.tsv`، `.txt`، `.ras`، `.asc`، `.mdi`، `.gsa`، `.gsas`، `.edx`، `.inel`، `.chi`، إلخ) مع معالجة تلقائية للأرقام العشرية الأوروبية (`10,4` مقابل `10.4`) والمحددات المختلطة. محرك متّجه (vectorized) على NumPy لسرعة قصوى.
- **محللات ثنائية أصلية**: هندسة عكسية كاملة للصيغ الثنائية الخاصة مثل **Bruker RAW (v1/v2/v4)**، **Philips PC-APD (RD/SD)**، **Canberra MCA/CNF** و**Princeton Instruments SPE** — دون الحاجة إلى أي DLL خارجية.
- **الصيغ الحديثة (XML وHDF5/المسرّع)**: استخراج مخططات الحيود من **PANalytical XRDML**، **Bruker BRML** (ZIP+XML)، **Freiberg XSYG** و**NeXus/HDF5** (ملفات مسرّعات الجسيمات الكبرى).
- **معايير IUPAC والإرث**: دعم كامل لـ **JCAMP-DX**، **Spectra XPS** (Ron Unwin / VGX-900)، **Sietronics CPI**، **VAMAS ISO14976** و**pdCIF** (بما في ذلك الصيغة المستندة إلى النطاق مع تدوين أخطاء IUCR).
- **وحدة التحليل (`xrdio.processing`)**: إزالة الخلفية بخوارزمية **SNIP** (مُنفَّذة بـ NumPy النقي)، تنعيم بـ **المتوسط المتحرك** و**Savitzky-Golay** — قابلة للتسلسل عبر API سيّال.
- **دعم المسح المتعدد**: تحميل القياسات المتسلسلة (سلاسل درجات الحرارة، time-resolved، مسح الجهد) المحتوية على كتل بيانات متعددة، مع التوافق الكامل مع الكود المكتوب للمسح الفردي.
- **واجهة سطر أوامر مدمجة**: تفقّد البيانات الوصفية وحوّل الملفات دفعةً واحدة مباشرةً من الطرفية دون كتابة سطر Python واحد.

## التثبيت 📦

### المتطلبات الأساسية
يتطلب XRDIO **Python 3.9+** و**NumPy**. لا توجد تبعيات إلزامية أخرى.

### الطريقة الأولى: التثبيت عبر pip (موصى بها)

يثبّت الحزمة وأداة سطر الأوامر `xrdio` بشكل عام:
```bash
pip install .
```

لتفعيل دعم NeXus/HDF5 (ملفات المسرّع) أو المرشحات الإحصائية المتقدمة:
```bash
pip install ".[nexus]"            # يضيف h5py لـ NeXus/HDF5
pip install ".[analytics]"        # يضيف scipy لـ Savitzky-Golay
pip install ".[nexus,analytics]"  # كلاهما
```

### الطريقة الثانية: بدون تثبيت (البيئات الافتراضية / التطوير)

ثبّت التبعيات فقط وشغّل من مجلد المشروع مباشرةً:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## الاستخدام — سطر الأوامر 💻

### فحص البيانات الوصفية للملف
```bash
xrdio inspect my_data.xrdml
```
*الإخراج:*
```
File:       my_data.xrdml
Format:     PANalytical XRDML
Sample:     Sample_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 points)
Step:       0.040°
```

### التحويل إلى صيغة XY العالمية
```bash
xrdio convert my_data.raw clean_output.xy
```

### تحويل دُفعي لعدة ملفات
```bash
xrdio convert *.raw --output-dir ./converted/
```

## الاستخدام — Python API 🐍

```python
import xrdio

# قراءة أي ملف بنداء واحد — اكتشاف تلقائي للصيغة
data = xrdio.read("old_philips_data.rd")

print(f"العينة:       {data.sample_name}")
print(f"النقاط:       {data.n_points}")
print(f"طول الموجة:   {data.wavelength:.5f} Å" if data.wavelength else "طول الموجة:   N/A")

# وصول مباشر إلى مصفوفات NumPy
two_theta = data.two_theta
intensity  = data.intensity

# تصدير
xrdio.write(data, "output.xye")

# خط أنابيب تحليلي قابل للتسلسل
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## الصيغ المدعومة 📋

| الصيغة | الامتدادات | نوع المحلل |
| :--- | :---: | :---: |
| نص عام و CSV | `.xy`، `.csv`، `.tsv`، `.txt`، `.asc` | ✅ تحليل ذكي |
| اسماء مستعارة نصية (Rigaku، Inel، MDI...) | `.ras`، `.mdi`، `.gsas`، `.udx`، `.inel`، `.chi`... | ✅ تحليل ذكي |
| Bruker RAW (v1/v2/v4) | `.raw` | ✅ ثنائي أصلي |
| Bruker BRML (حديث) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ نص منظّم |
| Bruker SPC/PAR | `.spc` | ✅ ثنائي أصلي |
| PANalytical XRDML | `.xrdml` | ✅ XML أصلي |
| Philips PC-APD (v3/v5) | `.rd`، `.sd` | ✅ ثنائي أصلي |
| Philips UDF | `.udf` | ✅ نص منظّم |
| Canberra MCA / CNF | `.mca`، `.cnf` | ✅ ثنائي أصلي |
| Princeton Instruments WinSpec | `.spe` | ✅ ثنائي أصلي |
| IUPAC JCAMP-DX | `.jdx`، `.dx` | ✅ نص منظّم |
| VAMAS ISO14976 | `.vms` | ✅ نص منظّم |
| Ron Unwin Spectra XPS | `.1`، `.2` | ✅ نص منظّم |
| Freiberg Instruments XSYG | `.xsyg` | ✅ XML أصلي |
| pdCIF (بلورية المسحوق) | `.cif` | ✅ نص منظّم |
| NeXus / HDF5 (مسرّع الجسيمات) | `.nxs`، `.hdf5`، `.h5` | ✅ HDF5 (يتطلب `h5py`) |
| Sietronics Sieray CPI | `.cpi` | ✅ نص منظّم |

## الرخصة والتأليف

هذا المشروع برنامج حر، موزَّع تحت **رخصة GNU العامة الإصدار 3 (GPLv3)**. لك الحق في استخدامه ودراسته وتعديله وإعادة توزيعه — بما في ذلك للأغراض التجارية — بشرط أن تُوزَّع الأعمال المشتقة تحت الرخصة ذاتها.

**المؤلف:** Ricardo Cezar Volert  
**البريد الإلكتروني:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*طُوِّر في كوريتيبا (ولاية بارانا — البرازيل) في قسم الفيزياء بجامعة بارانا الفيدرالية (UFPR).*