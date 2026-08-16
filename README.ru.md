# XRDIO — Универсальный Ридер Файлов Рентгеновской Дифракции 🌌

🇧🇷 [Português](README.md) &nbsp;|&nbsp; 🇺🇸 [English](README.en.md) &nbsp;|&nbsp; 🇪🇸 [Español](README.es.md) &nbsp;|&nbsp; 🇯🇵 [日本語](README.ja.md) &nbsp;|&nbsp; 🇨🇳 [中文](README.zh.md) &nbsp;|&nbsp; 🇷🇺 [Русский](README.ru.md) &nbsp;|&nbsp; 🇮🇳 [हिन्दी](README.hi.md) &nbsp;|&nbsp; 🇸🇦 [العربية](README.ar.md) &nbsp;|&nbsp; 🇹🇷 [Türkçe](README.tr.md) &nbsp;|&nbsp; 🇸🇪 [Svenska](README.sv.md)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![Лицензия: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Статус: Стабильный](https://img.shields.io/badge/статус-стабильный-brightgreen.svg)]()
[![Форматов: 30+](https://img.shields.io/badge/форматы-30%2B-orange.svg)]()

---

**XRDIO** — это окончательный универсальный ридер и конвертер файлов рентгеновской дифракции (РД) и спектроскопии для Python. Написан на **чистом Python** с `numpy` в качестве единственной обязательной зависимости. Разработан для устранения зависимости от проприетарных инструментов, непрозрачных DLL и программного обеспечения производителей — предлагая совместимость с более чем **30 форматами** устаревших и современных приборов в единой лёгкой библиотеке.

## Основные возможности 🚀

- **Интеллектуальное распознавание текста**: Читает табличные данные из практически любого расширения обычного текстового файла (`.xy`, `.csv`, `.tsv`, `.txt`, `.ras`, `.asc`, `.mdi`, `.gsa`, `.gsas`, `.edx`, `.inel`, `.chi` и др.) с автоматической обработкой европейских десятичных знаков (`10,4` vs `10.4`) и смешанных разделителей. Векторизованный движок на NumPy для максимальной скорости.
- **Нативные бинарные парсеры**: Полный реверс-инжиниринг проприетарных бинарных форматов: **Bruker RAW (v1/v2/v4)**, **Philips PC-APD (RD/SD)**, **Canberra MCA/CNF** и **Princeton Instruments SPE** — без внешних DLL.
- **Современные форматы (XML и HDF5/Синхротрон)**: Извлечение дифрактограмм из **PANalytical XRDML**, **Bruker BRML** (ZIP+XML), **Freiberg XSYG** и **NeXus/HDF5** (файлы ускорителей частиц).
- **Стандарты IUPAC и устаревшие форматы**: Полная поддержка **JCAMP-DX**, **Spectra XPS** (Ron Unwin / VGX-900), **Sietronics CPI**, **VAMAS ISO14976** и **pdCIF** (включая range-based формат с нотацией ошибок IUCR).
- **Аналитический модуль (`xrdio.processing`)**: Вычитание фона алгоритмом **SNIP** (реализован на чистом NumPy), сглаживание **скользящим средним** и **Savitzky-Golay** — всё объединяемо через fluent API.
- **Поддержка Multi-Scan**: Загружает последовательные измерения (температурные серии, time-resolved, развёртка по напряжению) с несколькими блоками данных. Полная обратная совместимость с кодом для одиночного скана.
- **Встроенный CLI**: Проверяйте метаданные и конвертируйте файлы в пакетном режиме прямо из терминала — без единой строки Python.

## Установка 📦

### Предварительные требования
Для XRDIO необходимы **Python 3.9+** и **NumPy**. Других обязательных зависимостей нет.

### Способ 1: Установка через pip (Рекомендуется)

Устанавливает пакет и инструмент командной строки `xrdio` глобально:
```bash
pip install .
```

Для поддержки NeXus/HDF5 (синхротронные файлы) или расширенных статистических фильтров:
```bash
pip install ".[nexus]"            # Добавляет h5py для NeXus/HDF5
pip install ".[analytics]"        # Добавляет scipy для Savitzky-Golay
pip install ".[nexus,analytics]"  # Оба
```

### Способ 2: Без установки (виртуальные окружения / разработка)

Установите только зависимости и запускайте из директории проекта:
```bash
pip install -r requirements.txt
python -m xrdio.cli.main --help
```

## Использование — Командная строка 💻

### Просмотр метаданных файла
```bash
xrdio inspect my_data.xrdml
```
*Вывод:*
```
File:       my_data.xrdml
Format:     PANalytical XRDML
Sample:     Sample_10_Alpha
Radiation:  Cu Kα (λ = 1.54060 Å)
Range:      10.000° – 90.000° (2000 points)
Step:       0.040°
```

### Конвертация в универсальный формат XY
```bash
xrdio convert my_data.raw clean_output.xy
```

### Пакетная конвертация нескольких файлов
```bash
xrdio convert *.raw --output-dir ./converted/
```

## Использование — Python API 🐍

```python
import xrdio

# Читает любой файл одним вызовом — автоматическое определение формата
data = xrdio.read("old_philips_data.rd")

print(f"Образец:     {data.sample_name}")
print(f"Точек:       {data.n_points}")
print(f"Длина волны: {data.wavelength:.5f} Å" if data.wavelength else "Длина волны: N/A")

# Прямой доступ к массивам NumPy
two_theta = data.two_theta
intensity  = data.intensity

# Экспорт
xrdio.write(data, "output.xye")

# Объединяемый аналитический конвейер
data.remove_background(method='snip', n_iter=40) \
    .smooth(method='moving_average', window_length=5)
```

## Поддерживаемые форматы 📋

| Формат | Расширения | Тип парсера |
| :--- | :---: | :---: |
| Обычный текст и CSV | `.xy`, `.csv`, `.tsv`, `.txt`, `.asc` | ✅ Смарт-разбор |
| Текстовые псевдонимы (Rigaku, Inel, MDI...) | `.ras`, `.mdi`, `.gsas`, `.udx`, `.inel`, `.chi`... | ✅ Смарт-разбор |
| Bruker RAW (v1 / v2 / v4) | `.raw` | ✅ Нативный бинарный |
| Bruker BRML (современный) | `.brml` | ✅ ZIP + XML |
| Bruker/Siemens UXD | `.uxd` | ✅ Структурированный текст |
| Bruker SPC/PAR | `.spc` | ✅ Нативный бинарный |
| PANalytical XRDML | `.xrdml` | ✅ XML нативный |
| Philips PC-APD (v3/v5) | `.rd`, `.sd` | ✅ Нативный бинарный |
| Philips UDF | `.udf` | ✅ Структурированный текст |
| Canberra MCA / CNF | `.mca`, `.cnf` | ✅ Нативный бинарный |
| Princeton Instruments WinSpec | `.spe` | ✅ Нативный бинарный |
| IUPAC JCAMP-DX | `.jdx`, `.dx` | ✅ Структурированный текст |
| VAMAS ISO14976 | `.vms` | ✅ Структурированный текст |
| Ron Unwin Spectra XPS | `.1`, `.2` | ✅ Структурированный текст |
| Freiberg Instruments XSYG | `.xsyg` | ✅ XML нативный |
| pdCIF (Порошковая кристаллография) | `.cif` | ✅ Структурированный текст |
| NeXus / HDF5 (Синхротрон) | `.nxs`, `.hdf5`, `.h5` | ✅ HDF5 (требует `h5py`) |
| Sietronics Sieray CPI | `.cpi` | ✅ Структурированный текст |

## Лицензия и авторство

Этот проект является свободным программным обеспечением, распространяемым по **Стандартной общественной лицензии GNU v3 (GPLv3)**. Вы вправе свободно использовать, изучать, изменять и распространять его — включая коммерческое использование — при условии, что производные работы распространяются под той же лицензией.

**Автор:** Ricardo Cezar Volert  
**Эл. почта:** [ricardovolert@ufpr.br](mailto:ricardovolert@ufpr.br)  
**Lattes:** [http://lattes.cnpq.br/1090623526951757](http://lattes.cnpq.br/1090623526951757)  
**ORCID:** [https://orcid.org/0000-0002-2939-5128](https://orcid.org/0000-0002-2939-5128)

---

*Разработано в Куритибе (штат Парана — Бразилия) на кафедре физики Федерального университета Параны (UFPR).*
