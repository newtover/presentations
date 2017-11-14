---
## Пара слов про юникод в Python

  - Александр Тарелкин, EPAM
  - SPb Python Meetup \#30

---

  - Введение
  - Строковые типы и немного истории
  - Стандартный ввод
  - Ошибки `UnicodeDecodeError` и&#160;`UnicodeEncodeError`
  - Где живет ascii?

---

  - Все, вроде, имеют представление про Unicode и кодировки
  - НО(!) многие почему-то не используют это представление на практике

---

  Unicode HOWTO
  - https://docs.python.org/2/howto/unicode.html
  - https://docs.python.org/3/howto/unicode.html

---

Мы читаем, пишем и считаем символы, а&#160;компьютер оперирует байтами.

Нужно два разных типа: про символы и про&#160;байты.

---

На практике так было не всегда. Вспомним&#160;историю.

---

### Конец 60-х, стандарт ASCII

- 97 обозначает букву `a`, 
- 90 обозначает `Z`,
- 49 обозначает `1`,
- 32 обозначает пробел,
- 10 обозначает перевод строки

То есть числам от 0 до 127 сопоставлены какие-то символы или инструкции.

---

Удобно выровнять по целому количеству байт, заодно есть лишний бит. Пригодится.

---

### Начало 90-х, стандарт Юникод

  - 97 обозначает букву `a`, 
  - 49 обозначает `1`,
  - 32 обозначает пробел,
  - 1084 обозначает кириллическую малую букву `м`,
  - 9773 обозначает символ серпа и молота (&#9773;).

То есть числам от 0 до ~65,5 тысяч сопоставлены какие-то символы. Потом выделили более миллиона.

---

Так во многих языках в начале 2000-х был один тип &#x2014; и для байтов, и для символов. Но в довесок мог быть тип и чисто для&#160;символов.

---

В питоне это тип `unicode`, описанный в PEP 100.

---

```
Python 2.7.14 |Anaconda, Inc.| (default, Nov 8 2017, 22:44:41)
[GCC 7.2.0] on linux2
Type "help", "copyright", "credits" or "license" ...
>>> s1 = 'жаба'
>>> s2 = u'жаба'
>>> s1
'\xd0\xb6\xd0\xb0\xd0\xb1\xd0\xb0!1'
>>> s2
u'\u0436\u0430\u0431\u0430!1'
>>>
```

---

```
>>> import unicodedata as ud
>>> for ch in s2: print 'U+{:04o} {} {}'.format(
...   ord(ch), ud.category(ch), ud.name(ch))
...
U+2066 Ll CYRILLIC SMALL LETTER ZHE
U+2060 Ll CYRILLIC SMALL LETTER A
U+2061 Ll CYRILLIC SMALL LETTER BE
U+2060 Ll CYRILLIC SMALL LETTER A
U+0041 Po EXCLAMATION MARK
U+0061 Nd DIGIT ONE 
```

---

```
>>> s1
'\xd0\xb6\xd0\xb0\xd0\xb1\xd0\xb0!1'
>>> s2
u'\u0436\u0430\u0431\u0430!1'
>>>
```

---

```
>>> u'abc'.encode('utf-8')  # encode text into bytes
'abc'
>>> 'abc'.decode('cp1251')  # decode text from bytes
u'abc'
```

---

```
>>> s = r'C:\Users\newtover\test.txt~'  # string of bytes
>>> max(ord(ch) for ch in s)            # all are <= 127
126
>>> print s.decode('shift_jisx0213').encode('utf-8')
C:¥Users¥newtover¥test.txt‾
```

---

### Стандартный ввод

---

```
>>> s1
'\xd0\xb6\xd0\xb0\xd0\xb1\xd0\xb0!1'
>>> s2
u'\u0436\u0430\u0431\u0430!1'
>>> len(s1)
10
>>> len(s2)
6
```

---

```
>>> import sys
>>> print sys.stdin.encoding
UTF-8
>>> s1.decode(sys.stdin.encoding) == s2
True
>>> s2.encode(sys.stdin.encoding) == s1
True
```

---

```
(py27) vagrant@vagrant:~$ sudo locale-gen ru_RU.KOI8-R
Generating locales (this might take a while)...
  ru_RU.KOI8-R... done
Generation complete.
```

---

```
(py27) vagrant@vagrant:~$ LC_CTYPE=ru_RU.KOI8-R python
Python 2.7.14 |Anaconda, Inc.| (default, Nov 8 2017, 22:44:41)
[GCC 7.2.0] on linux2
Type "help", "copyright", "credits" or "license" ...
>>> import sys; sys.stdin.encoding
'KOI8-R'
>>> 'жаба'
'\xd0\xb6\xd0\xb0\xd1\x8e\xd0\xb1\xd0\xb0\xd1'
>>> u'жаба'
u'\u043f\u2564\u043f\u255f\u043f\u2560\u043f\u255f'
```

---

```
>>> s2 = _; import unicodedata as ud
>>> for ch in s2[:4]: print 'U+{:04o} {} {}'.format(
...  ord(ch), ud.category(ch), ud.name(ch))
...
U+2077 Ll CYRILLIC SMALL LETTER PE
U+22544 So BOX DRAWINGS DOWN SINGLE AND HORIZONTAL DOUBLE
U+2077 Ll CYRILLIC SMALL LETTER PE
U+22537 So BOX DRAWINGS VERTICAL DOUBLE AND RIGHT SINGLE
```

---

```
>>> 'п╤п╟п╠п╟'
'\xd0\xb6\xd0\xb0\xd1\x8e\xd0\xb1\xd0\xb0\xd1'
>>> u'п╤п╟п╠п╟'
u'\u043f\u2564\u043f\u255f\u043f\u2560\u043f\u255f'
```

---

```
(py27) vagrant@vagrant:~$ LC_CTYPE=ru_RU.KOI8-R luit python
Python 2.7.14 |Anaconda, Inc.| (default, Nov  8 2017, 22:44:41)
[GCC 7.2.0] on linux2
Type "help", "copyright", "credits" or "license" ...
>>> import sys; sys.stdin.encoding
'KOI8-R'
>>> 'жаба'
'\xd6\xc1\xc2\xc1'
>>> u'жаба'
u'\u0436\u0430\u0431\u0430'
```

---

```
>>> import re
>>> re.search('м', 'жаба')     # ничего не нашли, хорошо
>>> re.search('м|н', 'жаба')   # тоже ничего не нашли, хорошо
>>> re.search('[мн]', 'жаба')  # упс, чего это вдруг?
<_sre.SRE_Match object at 0x7f04f2d46308>
>>> re.findall('[мн]', 'жаба')
['\xd0', '\xd0', '\xd0', '\xd0']
```

---

### Откуда берется `UnicodeDecodeError`

---

