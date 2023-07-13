---
layout: post
title:  "Робота із образами дисків CP/M"
date:   2023-07-13 17:30:20 +0300
tags: [CP/M, disk images, .IMD, cpmtool, libdsk]
categories: [CP/M, disk images]
comments: true
excerpt_separator: <!--more-->
---

- [Огляд проблеми]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#огляд-проблеми)
  - [Оригінальні образи]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#оригінальні-образи) 
  - [Дисководи]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#дисководи)
  - [Дискети]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#дискети)
  - [Запис за допомогою Imagedisk]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#запис-за-допомогою-imagedisk)
  - [Утиліти для редагування образів]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#утиліти-для-редагування-образів)
    - [Компілювання libdsk та cpmtools]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#компілювання-libdsk-та-cpmtools)
      - [Пошук файлів конфігурації утилітами]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#пошук-файлів-конфігурації-утилітами)
    - [Опис форматів]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#опис-форматів)
  - [Редагування образів]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#редагування-образів)
    - [cpmtools]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#cpmtools)
    - [libdsk]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#libdsk)
- [Додаток -- манівцями]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#додаток----манівцями)
  - [ImageDisk]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#imagedisk)
    - [TD02IMD]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#td02imd)
    - [IMDU: ImageDisk Utility]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#imdu-imagedisk-utility)
    - [BIN2IMD: Binary to ImageDisk .IMD utility]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#bin2imd-binary-to-imagedisk-imd-utility)
  - [Перший успішний workflow]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#перший-успішний-workflow)
- [Виноски]({% post_url /retrocomputing/cpm/2023-07-13-disk_images %}#виноски)

# Огляд проблеми

<style>body {text-align: justify}</style>


[IBM PC-сумісні комп'ютери](https://en.wikipedia.org/wiki/IBM_PC_compatible)[^1] трохи стандартизували формати дискет, тому, для комп'ютерів цієї епохи домінують  побайтові образи -- й так зрозуміло, що сектор 512 байт, формат визначається [BPB](https://en.wikipedia.org/wiki/BIOS_parameter_block) у [бут-секторі](https://en.wikipedia.org/wiki/Volume_boot_record), а де не визначається (для дисків епохи DOS 1.xx) -- можна вгадати за розміром. Ілюстрація -- [плагін для Total Commander-а](http://indrekis2.blogspot.com/2022/08/64-total-commander-fat.html), який працює лише із ''плоскими'' образами, відкриває абсолютну більшість образів дискет, скачаних із сайтів з ретро-софтом. 

Звичайно, залишалися нестандартні формати, дискети із захистом від копіювання, формати інших операційних систем -- не сімейства MS-DOS і похідних від них. Завдяки низькорівневій природі апаратури дисководів, можливими були безліч варіацій. Для роботи із ними було створено спеціалізоване програмне забезпечення. Великого поширення набули [Teledisk](http://dunfield.classiccmp.org/img42841/td0notes.txt) (типове розширення .td0) та [Imagedisk](http://dunfield.classiccmp.org/img/index.htm) (.imd) -- у своїх образах вони зберігають службову інформацію, дозволяючи відтворити такі ''хитрі'' образи[^2].

Однак, із CP/M ситуація інша. Різні комп'ютери обладнувалися різними, часто не сумісними між собою, дисководами. Вони [різні за розміром](http://www.nj7p.org/Computers/Disk%20Subsystems/floppies.html) -- 8", 5.25", 3.5", 3"; різні за принципами -- soft sectors vs hard sectors; кількістю сторін (іншими словами -- магнітних голівок), кількістю треків, розміром секторів, [skewing-ом](https://www.seasip.info/Cpm/skew.html) тощо.

<!--more-->

| ![Формати дискет, підтримувані Osborne Executive](/retrocomputing/cpm/pics/osbe_floppy_formats.png "Формати дискет, підтримувані Osborne Executive") |
|:--------------------------------------------------:|
| Формати дискет, підтримувані Osborne Executive", [стор. 427, Osborne Executive Reference Guide](/retrocomputing/cpm/files/3F00186-00_ExecutiveRef_1983.pdf). Дискети 5.25", soft sector. |

Труднощі є і на вищому рівні абстракції -- навіть за повністю визначеного фізично образу, програмні формати навіть в межах CP/M могли помітно відрізнятися, і на самих дискетах ця інформація не зберігалася -- ніяких тобі [BPB](https://github.com/indrekis/FATImage_TCMD_plugin/blob/master/FAT_definitions.h)...

Тому маємо наступні проблеми:

1. Образ повинен містити інформацію, достатню, щоб відтворити правильну будову диску під час запису.
    - Зазвичай, це буде образ, створений Teledisk чи Imagedisk, або, принаймні -- перетворений у цей формат.
2. Потрібен сумісний дисковід на більш сучасному -- але, ймовірно, все ще старенькому, комп'ютері.
    - Або підключати його до сучасного комп'ютера пристроєм типу [kryoflux](https://kryoflux.com/) чи [greaseweazle](https://github.com/keirf/greaseweazle).
3. Потрібні сумісні дискети. 
4. Потрібна можливість редагувати такі образи. 
5. Бажаною є можливість читати такі образи з дискет.

Розповім про кожну із проблем. 

## Оригінальні образи 

Цікаво також мати оригінальні образи, які розповсюджувалися з комп'ютером Знайти їх виявилося доволі просто, принаймні для систем, з якими експериментував. Джерела -- [Web Archive](https://archive.org/search?query=osborne+executive&page=3), зокрема: [Osborne Executive CPM
](https://archive.org/details/osborneexecutivecpm) та [bitsavers](http://www.bitsavers.org/bits/Osborne/), (також, [документація від bitsavers](http://www.bitsavers.org/pdf/osborne/)). Для простоти, відповідні матеріали додаю до [цього репозиторію](/retrocomputing/cpm/files/osbe_orig_imgs/). Інше програмне забезпечення знаходив у купі різних місць -- деякі перераховано у [розділі посилань]<!-- (links.md) -->.

Видається, знайшов усі диски оригінального комплекту -- три диски CP/M Plus ([System Disk](/retrocomputing/cpm/files/osbe_orig_imgs/Disk01.IMD), [General Utilities Disk](/retrocomputing/cpm/files/osbe_orig_imgs/Disk02.IMD), [Advanced Utilities Disk](/retrocomputing/cpm/files/osbe_orig_imgs/Disk03.IMD), разом з описами [тут](/retrocomputing/cpm/files/osbe_orig_imgs/Osborne%20Executive%20Basic.zip)), [диск BASIC-ів](/retrocomputing/cpm/files/osbe_orig_imgs/Osborne%20Executive%20Basic.zip) (MBASIC/CBASIC), [диск SuperCalc](/retrocomputing/cpm/files/osbe_orig_imgs/Osborne%20Executive%20SuperCalc.zip), [два диски WordStar](/retrocomputing/cpm/files/osbe_orig_imgs/Osborne%20Executive%20WordStar.zip), [диск UCSD p-System](/retrocomputing/cpm/files/osbe_orig_imgs/ose-psys.td0). 

Для своєї зручності підготував [образ із утилітами](/retrocomputing/cpm/files/DISK_TOOLS.IMD), які часто використовую, разом із текстовим редактором [TE]<!-- (te_1.md) -->.

## Дисководи

Osborne Executive використовує менш-більш стандартні 5.25" дисководи, із soft sectors -- початок секторів розмічається програмно. Тому вдалося використати більш сучасні дисководи із епохи IBM PC-сумісних комп'ютерів. 

Потенційною проблемою було те, що ''новіші'' 5.25" дисководи є двох видів, 360 Кб і 1.2 Мб. Оскільки доріжка в 1.2 Мб дисководі помітно вужча, записані на ньому 360 Кб дискети читаються на відповідних дисководах не завжди[^3].  Забігаючи наперед, для запису дисків було використано Imagedisk, спочатку на 360 Кб дисководі, але організаційно це було незручно, тому поекспериментував і з 1.2 Мб -- за допомогою її опції Double step, вдається успішно записувати дискети для ''Осборна'' на 1.2Мб дисководі. 

Оскільки обладнання для під'єднання 5.25" дисководів до сучасних комп'ютерів, типу Kryoflux, у мене поки немає, скористався проміжною машиною, самозбірним 386-based комп'ютером із ethernet мережевою картою та ftp-сервером із пакету [MTCP](https://www.brutman.com/mTCP/). 

Щоправда, дискети формату Osborne-1 поки не вдалося записати. Планую поекспериментувати із іншими дисководами -- ймовірно, залежить від індивідуальних особливостей. 
<!-- TODO: Змінити, якщо вдасться -->

## Дискети

''Звичайні'' 360 Кб -- DD дискети для IBM PC-сумісних комп'ютерів підійшли (завдяки можливостям Imagedisk), тому особливих проблем на цьому етапі не виникло -- маю ще майже десяток працездатних. 1.2 Мб не вдалося успішно використати. 

## Запис за допомогою Imagedisk 

Я не проводив системного дослідження, за перших експериментів Imagedisk справився краще, ніж TeleDisk та більше сподобався, то надалі я цим пакетом і обмежився[^4].

Скріншоти зроблені в DOSBox-X, природно, що записувати звідти я не можу, але виглядають вони краще, ніж фото[^5], тому, де можливо, обмежився ними. 

На початку Imagedisk показує ''Splash screen'', вийти з якого можна за допомогою Esc: 

![](/retrocomputing/cpm/pics/imd_scr_01.png)

Тоді буде видно доступні операції:

![](/retrocomputing/cpm/pics/imd_scr_02.png)


Спочатку потрібно встановити ключові опції -- клавішею S:

![](/retrocomputing/cpm/pics/imd_photo_01.jpg)

Після цього, повертаємося до попереднього екрану та літерою W вмикаємо запис:

![](/retrocomputing/cpm/pics/imd_photo_02.jpg)

Читати можна тією ж утилітою, потім опрацьовуючи образи, як описано нижче. 

## Утиліти для редагування образів 

Основними формати образів, з якими доводиться мати справу: 

- Imd -- формат Imagedisk. Робота з реальним ''залізом'' в мене зосереджена навколо нього.
- Td0 -- формат Teledisk. Частина образів постачаються саме у цьому форматі.
  - Достатньо вміти конвертувати його в imd.
- Mfi або mfm -- формати [MAME]<!-- (cpm_emu_1.md#mame) -->, які цей емулятор вміє писати.
  - Достатньо вміти конвертувати цей формат в .imd. MAME вміє читати такий файл і перетворювати його в .mfi для запису. 
  - На жаль, поки цю задачу не вдалося вирішити[^6] -- збережене в MAME добути з цих файлів не вдалося. Серйозною проблемою це не є -- поки всі задачі вдалося вирішити в [RunCPM]<!-- (cpm_emu_1.md#runcpm) -->.
<!-- TODO: за нагоди пошукати рішення -->

Оптимальним варіантом для сучасних систем видається використання cpmtools, скомпільований із libdsk.

- [cpmtools](http://www.moria.de/~michael/cpmtools/) знає про формат файлової системи СP/M,
- [libdsk](https://www.seasip.info/Unix/LibDsk/) дозволяє безпосередньо маніпулювати образами Imagedisk із збереженою низькорівневою інформацією.

### Компілювання libdsk та cpmtools

0. Для компіляції використовую MSYS2/MinGW64, де встановлено всі необхідні пакети -- компілятори, autotools, бібліотеки (потрібні пакети розробики для libz i libbz2) тощо.
1. Скачуємо останні версії обох пакетів з офіційних сайтів: [cpmtools](http://www.moria.de/~michael/cpmtools/), [libdsk](https://www.seasip.info/Unix/LibDsk/). На момент написання цього тексту, це libdsk-1.5.19 та cpmtools-2.23.
  - На github трапляються різні варіанти цих пакетів, не бачив сенсу з ними розбиратися. 
2. Розархівовуємо обидва пакети: 

```bash
unzip libdsk1519.zip
tar -xvf libdsk-1.5.19.tar.gz
tar -xvf cpmtools-2.23.tar.gz 
cd libdsk-1.5.19.tar.gz
```

3. Нам потрібна динамічна бібліотека, тому потрібно зробити певні зміни до коду скриптів компіляції libdsk:

```bash
configure.ac :
- LT_INIT
+ LT_INIT([win32-dll])

- if test "$ac_cv_prog_gcc" = "yes"; then
-  CFLAGS="-Wall -DNOTWINDLL $CFLAGS"
- fi
``` 

Перша зміна -- для dll під Windows, друга -- оскільки gcc 2.95, проблему якого це вирішувало, згідно коментаря, давно не актуальний.

Для спрощення -- вимикаємо компіляцію документації:

```makefile
doc/Makefile.am :
-%.txt:	%.lyx
-	lyx -e text $<
-	mv `basename $@ .txt`.text $@	

-%.tex:	%.lyx
-	lyx -e latex $<

-%.pdf:	%.lyx
-	lyx -e pdf $<

```

Необхідно для коректної компіляції DLL:

```makefile
lib/Makefile.am : 
- AM_CPPFLAGS=-I$(top_srcdir)/include
+ AM_CPPFLAGS=-I$(top_srcdir)/include -DLIBDSK_EXPORTS=1

- libdsk_la_LDFLAGS = -version-info 9:0:4
+ libdsk_la_LDFLAGS = -version-info 9:0:4 -no-undefined
```

4. Перегенеровуємо скрипти autotools, конфігуруємо та компілюємо:

```bash
touch AUTHORS NEWS 
autoreconf -fiv
./configure --with-zlib --with-bzlib 
```

Тепер потрібно в згенерованому config.h додати:

```C
config.h :
- /* #undef HAVE_GETTEMPFILENAME */
+ #define HAVE_GETTEMPFILENAME 1
```

configure перевіряє цю функцію викликом GetTempFileName() -- без аргументів, вона не лінкується, і скрипт вирішує, що такої функції немає. Тоді проект ніби компілюється, але, розархівування образів у тимчасовий файл провалюється із дивними помилками, типу ''файл не знайдено''.

5. Компілюємо ``make``, або й ``make -j5`` -- проблем із паралельною компіляцією не було.
6. Для наступної компіляції cpmtools, зручно зразу й заінсталювати: ``make install`` -- файли та утиліти бібліотеки потраплять у відповідні директорії /mingw64.
7. Переходимо до компіляції cpmtools. Вона потребує ncursesw, але можна обійтися. 
8.  Генеруємо Makefile:

```makefile
cd ../cpmtools-2.23
./configure  --with-libdsk  --with-defformat=osbexec1
```

Оскільки я використовуватиму ці утиліти, в першу чергу, для роботи з Osborne Executive, скористався нагодою вибрати його формат (див. [Опис форматів](#опис-форматів)) за замовчуванням.

9. Перед компіляцією потрібно внести дрібне виправлення до Makefile, не став розбиратися із причинами: 

```makefile
 Makefile :
- LIBS=           -ldsk -lncurses 
+ LIBS=           -ldsk -lncursesw  
```

10. Компілюємо викликом make. 

Отримані утиліти потребуватимуть наступні динамічні бібліотеки: libbz2-1.dll, libdsk-5.dll, libiconv-2.dll, libintl-8.dll, libncursesw6.dll, zlib1.dll.

Завантажую [архів libdsk](/retrocomputing/cpm/files/libdsk-1.5.19.tar.gz) із змінами, описаними вище, отриманий викликом ``make dist``.

#### Пошук файлів конфігурації утилітами

Так скомпільовані cpmtools будуть шукати diskdefs за шляхом <MSYS2PATH>/mingw64/share/diskdefs та **в поточній директорії**. 
> За замовчуванням, cpmtools для Win32 з офіційного сайту шукають його в директорії /cpmtools поточного диску, UNIX-систем -- в ${prefix}/share/

Утиліти libdsk шукатимуть файд конфігурації libdskrc за шляхом ~/.libdskrc, або **libdskrc у піддиректорії share шляху, переданого у змінній LIBDSK**. Це поведінка "з коробки".

Керувати форматом, що використовується за замовчуванням, можна за допомогою змінної середовища ``CPMTOOLSFMT``.

### Опис форматів

Обидві пакети утиліт потребують опис конкретних дисків -- через їх велику різноманітність ''в дикій природі''.

Опис властивостей дисків різних Osborne-ів дуже неповний і в cpmtools, і в Disk-Utils/libdsk. Тому мусив додати, базуючись на наведених тут: 
[Osborne 1: Non-stock and user-made software](https://forum.vcfed.org/index.php?threads/osborne-1-non-stock-and-user-made-software.71846/) -- без них на експерименти пішло б помітно більше часу.

- Для cpmtools їх додано до файлу diskdefs, 
- Для libdsk -- файл libdskrc.


<details>

<summary>Записи diskdefs для дисків Osborne:</summary>

<!-- https://tomordonez.com/jekyll-text-expand-collapsible-markdown/ -->


<pre>
<!-- omit in toc -->
# OSB1 Osborne 1 - SSSD 48 tpi 5.25" - 256 x 10, image size = 102 400.
diskdef osb1ssdd
  seclen 256
  tracks 40
  sectrk 10
  blocksize 2048
  maxdir 64
  skew 2
  boottrk 3
  os 2.2
# DENSITY MFM ,LOW  <!-- omit in toc -->
# BSH 4 BLM 15 EXM 1 DSM 45 DRM 63 AL0 080H AL1 0 OFS 3 <!-- omit in toc -->
end

<!-- omit in toc -->
# OSB2 Osborne 1 - SSDD 48 tpi 5.25" - 1024 x 5
  diskdef osb1ssdd
  seclen 1024
  tracks 40
  sectrk 5
  blocksize 1024
  maxdir 64
  skew 1
  boottrk 3
  os 2.2
end

<!-- omit in toc -->
# OSB6 Osborne 1 + Osmosis - DSDD 96 tpi 5.25" - 512 x 10
diskdef osb1osm
  seclen 512
  tracks 80
  sectrk 10
  blocksize 2048
  maxdir 128
  skew 2
  boottrk 6
  os 2.2
end

<!-- omit in toc -->
# OSB7 Osborne Nuevo - DSDD 48 tpi 5.25" - 1024 x 5
diskdef osb1nv
  seclen 1024
  tracks 40
  sectrk 10
  blocksize 2048
  maxdir 128
  skew 2
  offset 10240
  boottrk 0
  os 2.2
end

<!-- omit in toc -->
# OSB8 Osborne Nuevo/Vixen/4 - DSDD 48 tpi 5.25" - 1024 x 5
diskdef osbVix
  seclen 1024
  tracks 80
  sectrk 5
  blocksize 2048
  maxdir 128
  skew 2
  boottrk 2
  os 2.2
end

<!-- omit in toc -->
# OSBB Osborne Nuevo 2.1 - DSDD 96 tpi 5.25" - 1024 x 5
diskdef osb1nv2
  seclen 1024
  tracks 160
  sectrk 5
  blocksize 2048
  maxdir 256
  skew 1
  boottrk 2
  os 2.2
end

<!-- omit in toc -->
# OSB3 Osborne Executive - SSDD 48 tpi 5.25" - 1024 x 5
diskdef osbexec1
  seclen 1024
  tracks 40
  sectrk 5
  blocksize 1024
  maxdir 64
  skew 1
  boottrk 3
  os 2.2
end

<!-- omit in toc -->
# OSB9 Osborne Executive w/Z3 - DSDD 96 tpi 5.25" - 1024 x 5
diskdef osbexec2
  seclen 1024
  tracks 160
  sectrk 5
  blocksize 2048
  maxdir 128
  skew 2
  boottrk 2
  os 2.2
end

<!-- omit in toc -->
# OSBA Osborne Executive Dig. Arts - DSDD 48 tpi 5.25" - 1024 x 5
diskdef osbexec3
  seclen 1024
  tracks 80
  sectrk 5
  blocksize 2048
  maxdir 128
  skew 2
  boottrk 2
  os 2.2
end
</pre>

</details>

<details>
<summary>Записи libdskrc  для дисків Osborne: </summary>

<pre>
[osb1sssd]
description = OSB1 Osborne 1 - SSSD 48 tpi 5.25" - 256 x 10
cylinders = 40
heads = 1
secsize = 256
sectors = 10
secbase = 1
datarate = SD

[osb1ssdd]
description = OSB2 Osborne 1 - SSDD 48 tpi 5.25" - 1024 x 5
cylinders = 40
heads = 1
secsize = 1024
sectors = 5
secbase = 1
datarate = DD

[osb1g2]
description = OSB4 Osborne G2 System - DSDD 48 tpi 5.25" - 1024 x 5
sides = extsurface
cylinders = 80
heads = 2
secsize = 1024
sectors = 5
secbase = 1
datarate = DD

[osb1g2h]
description = OSB5 Osborne G2 System - DSDD 96 tpi 5.25" - 1024 x 5
sides = extsurface
cylinders = 160
heads = 2
secsize = 1024
sectors = 5
secbase = 1
datarate = DD

[osb1osm]
description = OSB6 Osborne 1 + Osmosis - DSDD 96 tpi 5.25" - 512 x 10
sides = alt
cylinders = 80
heads = 2
secsize = 512
sectors = 10
secbase = 1
datarate = DD

[osb1nv]
description = OSB7 Osborne Nuevo - DSDD 48 tpi 5.25" - 1024 x 5
sides = extsurface
cylinders = 80
heads = 2
secsize = 1024
sectors = 5
secbase = 1
datarate = DD

[osbVix]
description = OSB8 Osborne Vixen - DSDD 48 tpi 5.25" - 1024 x 5
sides = alt
cylinders = 80
heads = 2
secsize = 1024
sectors = 5
secbase = 1
datarate = DD

[osb1nv2]
description = OSBB Osborne Nuevo 2.1 - DSDD 96 tpi 5.25" - 1024 x 5
sides = alt
cylinders = 160
heads = 2
secsize = 1024
sectors = 5
secbase = 1
datarate = DD

[osbexec1]
description = OSB3 Osborne Executive - SSDD 48 tpi 5.25" - 1024 x 5
cylinders = 40
heads = 1
secsize = 1024
sectors = 5
secbase = 1
datarate = DD


[osbexec2]
description = OSB9 Osborne Executive w/Z3 - DSDD 96 tpi 5.25" - 1024 x 5
sides = alt
cylinders = 160
heads = 2
secsize = 1024
sectors = 5
secbase = 1
datarate = DD


[osbexec3]
description = OSBA Osborne Executive Dig. Arts - DSDD 48 tpi 5.25" - 1024 x 5
sides = alt
cylinders = 80
heads = 2
secsize = 1024
sectors = 5
secbase = 1
datarate = DD
</pre>

</details>

> Ремарка: [skew](https://www.seasip.info/Cpm/skew.html) -- задає порядок чергування секторів, як оптимізацію -- ранні персональні ком'ютери, обладнані дисководами, не встигали прочитати кілька секторів підряд, тому сектори чергувалися так, щоб до моменту, коли можна починати читати наступний, він якраз наближався до магнітної голівки. Без цього виникають великі паузи -- доводиться чекати повного оберту диску для читання наступного сектора. Проблемою є те, що коли ''відступ'' невідомий, вміст файлів переплутується. 

## Редагування образів 

### cpmtools

Головною тріадою ''повсякденної'' роботи з образами є cpmls/cpmcp/cpmrm -- вивести список, скопіювати в чи з образу, видалити файл(и).

Всім утилітам потрібно опцією -f передавати тип образу -- автовизначення тут неможливе. А ось тип контейнера -- IMD, TD0 тощо, із підтримуваних, визначається автоматично. Якщо визначення не влаштовує, можна скористатися опцією ``-T``, наприклад, ``-T tele`` чи ``-T imd``, де імена типів визначаються libdsk (підказка програм про цю опцію не згадує).

<details>

<summary>Приклади використання cpmls </summary>

<pre>
$ cpmls ./DISK_TE.IMD    # Базове використання, тип образу -- за замовчуванням
0:
c.h
cpm3.sys
pip.com
te_kp.cf
te_kp.com
te_ws100.cf
te_ws100.com

$ cpmls ./OS1BASIC.IMD   # Невірний тип образу
cpmls: cannot read superblock (Data error.)

$ cpmls ./os1basic.td0   # Не той тип образу -- не залежно від формату контейнера
cpmls: cannot read superblock (Data error.)

$ cpmls -f osb1sssd ./OS1BASIC.IMD  # Вказано правильний тип
0:
autost.com
cbas2.com
crun2.com
mbasic.com
xdir.com
xref.com

$ cpmls -f osb1sssd  ./os1basic.td0  # Вказано правильний тип
0:
autost.com
cbas2.com
crun2.com
mbasic.com
xdir.com
xref.com

$ # Різні види форматування

$ cpmls -l ./DISK_TE.IMD    # UNIX-style 
0:
-rw-rw-rw-    1320 Jan 01 1970  c.h
-r--r--r--   17920 Jan 01 1970  cpm3.sys
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmls -d ./DISK_TE.IMD    # CP/M 2.2 dir output
PIP      COM : TE_WS100 CF  : TE_WS100 COM : TE_KP    CF
TE_KP    COM : CPM3     SYS : C        H

$ cpmls -D ./DISK_TE.IMD    # P2DOS 2.3 ddir-like output. 
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
C       .H       2K     10
CPM3    .SYS    18K    140 RS
PIP     .COM     9K     68 R
TE_KP   .CF      2K      8
TE_KP   .COM    19K    148
TE_WS100.CF      2K      8
TE_WS100.COM    19K    151
    7 Files occupying     71K,     112K Free.

$ cpmls -F ./DISK_TE.IMD    # CP/M 3.x DIR.COM output. 
Directory For Drive A:  User  0

    Name     Bytes   Recs   Attributes   Prot      Update          Create
------------ ------ ------ ------------ ------ --------------  --------------

C        H       2k     11              None
CPM3     SYS    18k    140     RS       None
PIP      COM     9k     68     R        None
TE_KP    CF      2k      9              None
TE_KP    COM    19k    148              None
TE_WS100 CF      2k      9              None
TE_WS100 COM    19k    151              None

Total Bytes     =     67k  Total Records =     536  Files Found =    7
Total 1k Blocks =     71   Used/Max Dir Entries For Drive A:   10/  64

$ cpmls -A ./DISK_TE.IMD    # E2fs lsattr-like output.
0:
--------- c.h
----s---- cpm3.sys
--------- pip.com
--------- te_kp.cf
--------- te_kp.com
--------- te_ws100.cf
--------- te_ws100.com

$ # Додатково -- друкувати індекс файлу. Видно, що с.h було створено останнім.

$ cpmls -i ./DISK_TE.IMD    
0:
   9 c.h
   6 cpm3.sys
   0 pip.com
   4 te_kp.cf
   5 te_kp.com
   1 te_ws100.cf
   2 te_ws100.com

$ cpmls -i -l ./DISK_TE.IMD 
0:
   9 -rw-rw-rw-    1320 Jan 01 1970  c.h
   6 -r--r--r--   17920 Jan 01 1970  cpm3.sys
   0 -r-xr-xr-x    8704 Jan 01 1970  pip.com
   4 -rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
   5 -rwxrwxrwx   18944 Jan 01 1970  te_kp.com
   1 -rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
   2 -rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ # А ось це мені не зрозуміло...

$ cpmls -D DISK_EXP.IMD TE_WS100.COM
No files found

$ cpmls -D DISK_EXP.IMD TE_WS*.COM
No files found

$ cpmls -D DISK_EXP.IMD TE*.COM
No files found

$ cpmls -D DISK_EXP.IMD *.COM
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
PIP     .COM     9K     68 R
TE_KP   .COM    19K    148
TE_WS100.COM    19K    151
    3 Files occupying     71K,     112K Free.

$ cpmls -D DISK_EXP.IMD *.C*
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
PIP     .COM     9K     68 R
TE_KP   .CF      2K      8
TE_KP   .COM    19K    148
TE_WS100.CF      2K      8
TE_WS100.COM    19K    151
    5 Files occupying     71K,     112K Free.

</pre>

</details>

<details>

<summary>Приклади використання cpmcp та cpmrm </summary>

<pre>
$ ls -l
total 144
-rw-r--r-- 1 indrekis None 133830 Apr  7 02:45 DISK_EXP.IMD
-rw-r--r-- 1 indrekis None  57070 Feb 21 22:44 DISK_EXP2.td0

$ cpmls -l ./DISK_EXP.IMD
0:
-rw-rw-rw-    1320 Jan 01 1970  c.h
-r--r--r--   17920 Jan 01 1970  cpm3.sys
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmcp ./DISK_EXP.IMD 0:c.h .  # Добуваємо з образу

$ ls -l
total 148
-rw-r--r-- 1 indrekis None 133830 Apr  7 02:45 DISK_EXP.IMD
-rw-r--r-- 1 indrekis None  57070 Feb 21 22:44 DISK_EXP2.td0
-rw-r--r-- 1 indrekis None   1320 Apr 19 01:39 c.h

$ # Добуваємо з образу як текст (документація стверджує, 
$ # що перетворює з формату CP/M в UNIX формат, але виглядає,
$ # що під Windows -- в формат Windows).
$ cpmcp -t ./DISK_EXP.IMD 0:c.h ./c1.h

$ ls -l
total 152
-rw-r--r-- 1 indrekis None 133830 Apr  7 02:45 DISK_EXP.IMD
-rw-r--r-- 1 indrekis None  57070 Feb 21 22:44 DISK_EXP2.td0
-rw-r--r-- 1 indrekis None   1320 Apr 19 01:39 c.h
-rw-r--r-- 1 indrekis None   1392 Apr 19 01:40 c1.h

$ # Перезаписує на хості, але -- не в образі:
$ cpmcp ./DISK_EXP.IMD ./c.h 0:
cpmcp: can not create 00c.h: file already exists

$ cpmrm ./DISK_EXP.IMD c.h  # Тому спершу стираємо

$ cpmls -l ./DISK_EXP.IMD
0:
-r--r--r--   17920 Jan 01 1970  cpm3.sys
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmcp ./DISK_EXP.IMD ./c.h 0: 

$ cpmls -l ./DISK_EXP.IMD
0:
-rw-rw-rw-    1320 Jan 01 1970  c.h
-r--r--r--   17920 Jan 01 1970  cpm3.sys
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ rm *.h   # Clear folder for clarity

$ ls
DISK_EXP.IMD  DISK_EXP2.td0

$ cpmcp ./DISK_EXP.IMD 0:*.* .  # Copy all files from the image to current folder

$ ls
DISK_EXP.IMD  DISK_EXP2.td0  c.h  cpm3.sys  pip.com  te_kp.cf  te_kp.com  te_ws100.cf  te_ws100.com

$ cp DISK_EXP.IMD EMPTY.IMD 

$ cpmrm EMPTY.IMD *.*   # Empty image

$ cpmls -l EMPTY.IMD    # Справді немає файлів

$ cpmls -D EMPTY.IMD    # Ще раз перевіряємо
No files found

$ cpmcp ./EMPTY.IMD *.COM *.CF 0: # Копіюємо групу файлів користувачу 0

$ cpmls -l EMPTY.IMD
0:
-rwxrwxrwx    8704 Jan 01 1970  pip.com
-rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmcp ./EMPTY.IMD *.COM *.CF 1:  # А тепер ще користувачу 1

$ cpmls -l EMPTY.IMD   # cpmls вміє вивести файли всіх користувачів
0:
-rwxrwxrwx    8704 Jan 01 1970  pip.com
-rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

1:
-rwxrwxrwx    8704 Jan 01 1970  pip.com
-rw-rw-rw-    1035 Jan 01 1970  te_kp.cf
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rw-rw-rw-    1040 Jan 01 1970  te_ws100.cf
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ # Інший тип образу у тому ж контейнері <!-- omit in toc -->
  # DISK_EXP2.IMD -- DISK_EXP2.td0, converted to .IMD <!-- omit in toc -->

$ cpmcp -f osb1sssd DISK_EXP2.IMD 0:xdir.com .

$ cpmcp -f osb1sssd DISK_EXP2.IMD c.h 0:

$ cpmls -f osb1sssd -l DISK_EXP2.IMD
0:
-rwxrwxrwx    2048 Jan 01 1970  autost.com
-rw-rw-rw-    1320 Jan 01 1970  c.h
-rwxrwxrwx   20992 Jan 01 1970  cbas2.com
-rwxrwxrwx   17408 Jan 01 1970  crun2.com
-rwxrwxrwx   24320 Jan 01 1970  mbasic.com
-rwxrwxrwx    2304 Jan 01 1970  xdir.com
-rwxrwxrwx    7168 Jan 01 1970  xref.com

$ cpmrm -f osb1sssd DISK_EXP2.IMD c.h

$ cpmls -f osb1sssd -l DISK_EXP2.IMD
0:
-rwxrwxrwx    2048 Jan 01 1970  autost.com
-rwxrwxrwx   20992 Jan 01 1970  cbas2.com
-rwxrwxrwx   17408 Jan 01 1970  crun2.com
-rwxrwxrwx   24320 Jan 01 1970  mbasic.com
-rwxrwxrwx    2304 Jan 01 1970  xdir.com
-rwxrwxrwx    7168 Jan 01 1970  xref.com


$ # Інший тип контейнера

$ cpmls -f osb1sssd -l DISK_EXP2.td0
0:
-rwxrwxrwx    2048 Jan 01 1970  autost.com
-rwxrwxrwx   20992 Jan 01 1970  cbas2.com
-rwxrwxrwx   17408 Jan 01 1970  crun2.com
-rwxrwxrwx   24320 Jan 01 1970  mbasic.com
-rwxrwxrwx    2304 Jan 01 1970  xdir.com
-rwxrwxrwx    7168 Jan 01 1970  xref.com

$ cpmcp -f osb1sssd DISK_EXP2.td0 0:xdir.com . # З образу -- копіює

$ cpmcp -f osb1sssd DISK_EXP2.td0 c.h 0:   # Але змінювати образ не вміє
cpmcp: can not umount device: Disc is read-only.

$ # Стирати теж не вміє, але й про помилку не каже

$ cpmrm -f osb1sssd DISK_EXP2.td0 xdir.com 

$ echo $?
0

$ cpmls -f osb1sssd -l DISK_EXP2.td0
0:
-rwxrwxrwx    2048 Jan 01 1970  autost.com
-rwxrwxrwx   20992 Jan 01 1970  cbas2.com
-rwxrwxrwx   17408 Jan 01 1970  crun2.com
-rwxrwxrwx   24320 Jan 01 1970  mbasic.com
-rwxrwxrwx    2304 Jan 01 1970  xdir.com
-rwxrwxrwx    7168 Jan 01 1970  xref.com

$ # Нюанс з директоріями. Якщо зробити так:

$ cpmcp IMAGE.IMD ASM1\hello1.asm 0:

$ # Воно створить файл ''ASM1\HEL'' -- символ ''\'' іноді припустимий
  # в іменах файлів. Тому потрібно робити так: <!-- omit in toc -->

$ cpmcp DISK_TOOLS_W.IMD ASM1\hello1.asm 0:hello1.asm

</pre>

</details>


<details>

<summary>Приклади використання cpmchmod та cpmchattr</summary>
<pre>
$ cpmls -D DISK_EXP.IMD *.COM *.SYS
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
CPM3    .SYS    18K    140 RS
PIP     .COM     9K     68 R
TE_KP   .COM    19K    148
TE_WS100.COM    19K    151
    4 Files occupying     71K,     112K Free.

$ cpmls -l DISK_EXP.IMD *.COM *.SYS
0:
-r--r--r--   17920 Jan 01 1970  cpm3.sys
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmchattr DISK_EXP.IMD "12rs" TE_KP.COM

$ cpmchattr DISK_EXP.IMD "n" CPM3.SYS

$ cpmls -l DISK_EXP.IMD *.COM *.SYS
0:
-rw-rw-rw-   17920 Jan 01 1970  cpm3.sys
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-r-xr-xr-x   18944 Jan 01 1970  te_kp.com
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmls -D DISK_EXP.IMD *.COM *.SYS
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
CPM3    .SYS    18K    140
PIP     .COM     9K     68 R
TE_KP   .COM    19K    148 RS
TE_WS100.COM    19K    151
    4 Files occupying     71K,     112K Free.

$ cpmchattr DISK_EXP.IMD "1234" TE_KP.COM

$ cpmls -D DISK_EXP.IMD *.COM
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
PIP     .COM     9K     68 R
TE_KP   .COM    19K    148 RS
TE_WS100.COM    19K    151
    3 Files occupying     71K,     112K Free.

$ cpmls -l DISK_EXP.IMD *.COM
0:
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-r-xr-xr-x   18944 Jan 01 1970  te_kp.com
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmchattr DISK_EXP.IMD "AmRS" TE_KP.COM

$ cpmls -l DISK_EXP.IMD *.COM
0:
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmls -D DISK_EXP.IMD *.COM
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
PIP     .COM     9K     68 R
TE_KP   .COM    19K    148
TE_WS100.COM    19K    151
    3 Files occupying     71K,     112K Free.

$ cpmchmod  DISK_EXP.IMD 000 TE_KP.COM

$ cpmls -l DISK_EXP.IMD *.COM
0:
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-r-xr-xr-x   18944 Jan 01 1970  te_kp.com
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmls -D DISK_EXP.IMD *.COM
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
PIP     .COM     9K     68 R
TE_KP   .COM    19K    148 R
TE_WS100.COM    19K    151
    3 Files occupying     71K,     112K Free.

$ cpmchmod  DISK_EXP.IMD 777 TE_KP.COM

$ cpmls -l DISK_EXP.IMD *.COM
0:
-r-xr-xr-x    8704 Jan 01 1970  pip.com
-rwxrwxrwx   18944 Jan 01 1970  te_kp.com
-rwxrwxrwx   19328 Jan 01 1970  te_ws100.com

$ cpmls -D DISK_EXP.IMD *.COM
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
PIP     .COM     9K     68 R
TE_KP   .COM    19K    148
TE_WS100.COM    19K    151
    3 Files occupying     71K,     112K Free.

$ cpmchattr DISK_EXP.IMD "RS" *.COM

$ cpmls -D DISK_EXP.IMD *.COM
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
PIP     .COM     9K     68 RS
TE_KP   .COM    19K    148 RS
TE_WS100.COM    19K    151 RS
    3 Files occupying     71K,     112K Free.

$ cpmchattr DISK_EXP.IMD "RS" *.*

$ cpmls -D DISK_EXP.IMD *.*
     Name    Bytes   Recs  Attr     update             create
------------ ------ ------ ---- -----------------  -----------------
C       .H       2K     10 RS
CPM3    .SYS    18K    140 RS
PIP     .COM     9K     68 RS
TE_KP   .CF      2K      8 RS
TE_KP   .COM    19K    148 RS
TE_WS100.CF      2K      8 RS
TE_WS100.COM    19K    151 RS
    7 Files occupying     71K,     112K Free.

</pre>

</details>

> Виглядає, що cpmchmod вміє лише встановлювати read-only та system атрибути, хіба що -- mode передається у стилі UNIX. 
> 
> Утиліта cpmchattr дозволяє змінювати атрибути:
> - 1-4 -- F1-F4, користувацькі, 
> - r -- read-only,
> - s -- system,
> - a -- archived,
> 
> Крім того, в стрічці команди, n -- очистити всі атрибути, m -- очистити ті, що йдуть далі. Тобто, "nrs" -- очищає всі, робить read-only i system, "12mr34" -- встановлює F1, F2, очишає read-only, F3 та F4.

<!-- Зроблено так, оскільки в Jekyll в collapsible не працює формтування -->

<details>

<summary> Приклади використання fsck.cpm та fsed.cpm </summary>

<pre>
$ fsck.cpm  DISK_EXP.IMD
Phase 1: check extent fields
Phase 2: check extent connectivity
DISK_EXP.IMD: 10/64 files (0.0% non-contigous), 73/185 blocks

$ fsck.cpm -f osb1sssd DISK_EXP2.TD0
Phase 1: check extent fields
Phase 2: check extent connectivity
DISK_EXP2.TD0: 6/64 files (0.0% non-contigous), 40/46 blocks
</pre>

</details>

> Утиліта fsed.cpm дозволяє редагувати файлову систему образу, а  fsck.cpm перевіряє, і, кажуть -- дозволяє виправляти помилки. Джерельні тексти cpmtools навіть включають приклад пошкоджених файлових систем -- директорію badfs.
> 
> | ![](/retrocomputing/cpm/pics/cpm_ed_chp_1.png) |
> |:----------------------------:|
> | Редактор файлової системи CP/M -- відображення каталогу. Підказки щодо відповідності різних представлень тієї ж інформації. |

### libdsk

Пакет libdsk допомагає cpmtools працювати із контейнерами образів, але в комплектів  має і свої корисні утиліти.

<details>
<summary>dskid -- інформація про образи.</summary> 

<pre>
$ dskid DISK_EXP2.IMD
Stage 1
Stage 2
Stage 3
Stage 4
Stage 5
DISK_EXP2.IMD:
  Driver:      IMD file driver
  Sidedness:     Alt
  Cylinders:     40
  Heads:          1
  Sectors:       10
  First sector:   1
  Sector size:  256
  Data rate:     SD
  Record mode:  FM
  Complement:   No
  R/W gap:     0x2a
  Format gap:  0x52

  Drive status:  0x20
  Comment:       [1991-07-29T16:40:37] CP/M 2.2 Basic Disk for Osborne 1
Stage 6

$ dskid DISK_EXP.IMD
Stage 1
Stage 2
Stage 3
Stage 4
Stage 5
DISK_EXP.IMD:
  Driver:      IMD file driver
  Sidedness:     Alt
  Cylinders:     40
  Heads:          1
  Sectors:        9
  First sector:   1
  Sector size: 1024
  Data rate:     SD
  Record mode:  MFM
  Complement:   No
  R/W gap:     0x2a
  Format gap:  0x52

  Drive status:  0x20

Stage 6

</pre>

</details>

<details>

<summary>dskparse -- деталізований HEX-dump образу.</summary> 

Фрагмент виводу: 
<pre>
$ dskparse DISK_EXP2.IMD
                                                                               
--------  IMD disk image ------------------------------------------------------
00000000: 49 4d 44 20 4c 69 62 44 |IMD LibD| Signature / date stamp            
00000008: 73 6b 20 31 2e 35 2e 31 |sk 1.5.1|                                   
00000010: 39 3a 20 31 39 2f 30 34 |9: 19/04|                                   
00000018: 2f 32 30 32 33 20 30 32 |/2023 02|                                   
00000020: 3a 33 39 3a 35 38 0d 0a |:39:58..|                                   
00000028: 5b 31 39 39 31 2d 30 37 |[1991-07| Comment                           
00000030: 2d 32 39 54 31 36 3a 34 |-29T16:4|                                   
00000038: 30 3a 33 37 5d 20 43 50 |0:37] CP|                                   
00000040: 2f 4d 20 32 2e 32 20 42 |/M 2.2 B|                                   
00000048: 61 73 69 63 20 44 69 73 |asic Dis|                                   
00000050: 6b 20 66 6f 72 20 4f 73 |k for Os|                                   
00000058: 62 6f 72 6e 65 20 31 1a |borne 1.|                                   
                                                                               
                                                                               
--------  Header for track 0 --------------------------------------------------
00000060: 02                      |.       | Recording mode                    
00000061:    00                   | .      | Cylinder = 0                      
00000062:       00                |  .     | Head = 0                          
00000063:          0a             |   .    | Sectors = 10                      
00000064:             01          |    .   | Sector size = 256                 
00000065:                01 02 03 |     ...| Sector IDs                        
00000068: 04 05 06 07 08 09 0a    |....... |                                   
0000006f:                      01 |       .| Sector 0 status                   
00000070: c3 5c d2 c3 58 d2 7f 00 |.\..X...| Sector 0 uncompressed             
00000078: 20 20 20 20 20 20 20 20 |        |                                   
00000080: 20 20 20 20 20 20 20 20 |        |                                   
00000088: 43 4f 50 59 52 49 47 48 |COPYRIGH|                                   
00000090: 54 20 28 43 29 20 31 39 |T (C) 19|                                   
00000098: 37 39 2c 20 44 49 47 49 |79, DIGI|                                   
000000a0: 54 41 4c 20 52 45 53 45 |TAL RESE|                                   
000000a8: 41 52 43 48 20 20 00 00 |ARCH  ..|                                   
000000b0: 00 00 00 00 00 00 00 00 |........|                                   
000000b8: 00 00 00 00 00 00 00 00 |........|                                   
000000c0: 00 00 00 00 00 00 00 00 |........|                                   
000000c8: 00 00 00 00 00 00 00 00 |........|                                   
000000d0: 00 00 00 00 00 00 00 00 |........|                                   
000000d8: 00 00 00 00 00 00 00 00 |........|                                   
000000e0: 00 00 00 00 00 00 00 00 |........|                                   
000000e8: 00 00 00 00 00 00 00 00 |........|                                   
000000f0: 00 00 00 00 00 00 00 00 |........|                                   
000000f8: 08 cf 00 00 5f 0e 02 c3 |...._...|                                   
00000100: 05 00 c5 cd 8c cf c1 c9 |........|                                   
00000108: 3e 0d cd 92 cf 3e 0a c3 |>....>..|                                   
00000110: 92 cf 3e 20 c3 92 cf c5 |..> ....|                                   
00000118: cd 98 cf e1 7e b7 c8 23 |....~..#|                                   
00000120: e5 cd 8c cf e1 c3 ac cf |........|                                   
00000128: 0e 0d c3 05 00 5f 0e 0e |....._..|                                   
00000130: c3 05 00 cd 05 00 32 ee |......2.|                                   
00000138: d6 3c c9 0e 0f c3 c3 cf |.<......|                                   
00000140: af 32 ed d6 11 cd d6 c3 |.2......|                                   
00000148: cb cf 0e 10 c3 c3 cf 0e |........|                                   
00000150: 11 c3 c3 cf 0e 12 c3 c3 |........|                                   
00000158: cf 11 cd d6 c3 df cf 0e |........|                                   
00000160: 13 c3 05 00 cd 05 00 b7 |........|                                   
00000168: c9 0e 14 c3 f4 cf 11 cd |........|                                   
00000170: 01                      |.       | Sector 1 status                   
00000171:    d6 c3 f9 cf 0e 15 c3 | .......| Sector 1 uncompressed             
00000178: f4 cf 0e 16 c3 c3 cf 0e |........|                                   
00000180: 17 c3 05 00 1e ff 0e 20 |....... |                                   
00000188: c3 05 00 cd 13 d0 87 87 |........|                                   
00000190: 87 87 21 ef d6 b6 32 04 |..!...2.|                                   
00000198: 00 c9 3a ef d6 32 04 00 |..:..2..|                                   
000001a0: c9 fe 61 d8 fe 7b d0 e6 |..a..{..|                                   
000001a8: 5f c9 3a ab d6 b7 ca 96 |_.:.....|                                   
000001b0: d0 3a ef d6 b7 3e 00 c4 |.:...>..|                                   
000001b8: bd cf 11 ac d6 cd cb cf |........|                                   
000001c0: ca 96 d0 3a bb d6 3d 32 |...:..=2|                                   
000001c8: cc d6 11 ac d6 cd f9 cf |........|                                   
000001d0: c2 96 d0 11 07 cf 21 80 |......!.|                                   
000001d8: 00 06 80 cd 42 d3 21 ba |....B.!.|                                   
000001e0: d6 36 00 23 35 11 ac d6 |.6.#5...|                                   
000001e8: cd da cf ca 96 d0 3a ef |......:.|                                   
000001f0: d6 b7 c4 bd cf 21 08 cf |.....!..|                                   
000001f8: cd ac cf cd c2 d0 ca a7 |........|                                   
00000200: d0 cd dd d0 c3 82 d2 cd |........|                                   
00000208: dd d0 cd 1a d0 0e 0a 11 |........|                                   
00000210: 06 cf cd 05 00 cd 29 d0 |......).|                                   
00000218: 21 07 cf 46 23 78 b7 ca |!..F#x..|                                   
00000220: ba d0 7e cd 30 d0 77 05 |..~.0.w.|                                   
00000228: c3 ab d0 77 21 08 cf 22 |...w!.."|                                   
00000230: 88 cf c9 0e 0b cd 05 00 |........|                                   
00000238: b7 c8 0e 01 cd 05 00 b7 |........|                                   
00000240: c9 0e 19 c3 05 00 11 80 |........|                                   
00000248: 00 0e 1a c3 05 00 21 ab |......!.|                                   
00000250: d6 7e b7 c8 36 00 af cd |.~..6...|                                   
00000258: bd cf 11 ac d6 cd ef cf |........|                                   
00000260: 3a ef d6 c3 bd cf 11 28 |:......(|                                   
00000268: d2 21 00 d7 06 06 1a be |.!......|                                   
00000270: c2                      |.       |                                   
00000271:    01                   | .      | Sector 2 status                   
00000272:       cf d2 13 23 05 c2 |  ...#..| Sector 2 uncompressed             
00000278: fd d0 c9 cd 98 cf 2a 8a |......*.|                                   
00000280: cf 7e fe 20 ca 22 d1 b7 |.~. ."..|                                   
00000288: ca 22 d1 e5 cd 8c cf e1 |."......|                                   
00000290: 23 c3 0f d1 3e 3f cd 8c |#...>?..|                                   
00000298: cf cd 98 cf cd dd d0 c3 |........|                                   
000002a0: 82 d2 1a b7 c8 fe 20 da |...... .|                                   
000002a8: 09 d1 c8 fe 3d c8 fe 5f |....=.._|                                   
000002b0: c8 fe 2e c8 fe 3a c8 fe |.....:..|                                   
000002b8: 3b c8 fe 3c c8 fe 3e c8 |;..<..>.|                                   
000002c0: c9 1a b7 c8 fe 20 c0 13 |..... ..|                                   
000002c8: c3 4f d1 85 6f d0 24 c9 |.O..o.$.|                                   
000002d0: 3e 00 21 cd d6 cd 59 d1 |>.!...Y.|                                   
000002d8: e5 e5 af 32 f0 d6 2a 88 |...2..*.|                                   
000002e0: cf eb cd 4f d1 eb 22 8a |...O..".|                                   
000002e8: cf eb e1 1a b7 ca 89 d1 |........|                                   
000002f0: de 40 47 13 1a fe 3a ca |.@G...:.|                                   
000002f8: 90 d1 1b 3a ef d6 77 c3 |...:..w.|                                   
00000300: 96 d1 78 32 f0 d6 70 13 |..x2..p.|                                   
00000308: 06 08 cd 30 d1 ca b9 d1 |...0....|                                   
00000310: 23 fe 2a c2 a9 d1 36 3f |#.*...6?|                                   
00000318: c3 ab d1 77 13 05 c2 98 |...w....|                                   
00000320: d1 cd 30 d1 ca c0 d1 13 |..0.....|                                   
00000328: c3 af d1 23 36 20 05 c2 |...#6 ..|                                   
00000330: b9 d1 06 03 fe 2e c2 e9 |........|                                   
00000338: d1 13 cd 30 d1 ca e9 d1 |...0....|                                   
00000340: 23 fe 2a c2 d9 d1 36 3f |#.*...6?|                                   
00000348: c3 db d1 77 13 05 c2 c8 |...w....|                                   
00000350: d1 cd 30 d1 ca f0 d1 13 |..0.....|                                   
00000358: c3 df d1 23 36 20 05 c2 |...#6 ..|                                   
00000360: e9 d1 06 03 23 36 00 05 |....#6..|                                   
00000368: c2 f2 d1 eb 22 88 cf e1 |...."...|                                   
00000370: 01 0b                   |..      |                                   
00000372:       01                |  .     | Sector 3 status                   
00000373:          00 23 7e fe 3f |   .#~.?| Sector 3 uncompressed             
00000378: c2 09 d2 04 0d c2 01 d2 |........|                                   
00000380: 78 b7 c9 44 49 52 20 45 |x..DIR E|                                   
00000388: 52 41 20 54 59 50 45 53 |RA TYPES|                                   
00000390: 41 56 45 52 45 4e 20 55 |AVEREN U|                                   
00000398: 53 45 52 02 02 12 00 0a |SER.....|                                   
000003a0: 2a 21 10 d2 0e 00 79 fe |*!....y.|                                   
000003a8: 06 d0 11 ce d6 06 04 1a |........|                                   
000003b0: be c2 4f d2 13 23 05 c2 |..O..#..|                                   
000003b8: 3c d2 1a fe 20 c2 54 d2 |<... .T.|                                   
000003c0: 79 c9 23 05 c2 4f d2 0c |y.#..O..|                                   
000003c8: c3 33 d2 af 32 07 cf 31 |.3..2..1|                                   
000003d0: ab d6 c5 79 1f 1f 1f 1f |...y....|                                   
000003d8: e6 0f 5f cd 15 d0 cd b8 |.._.....|                                   
000003e0: cf 32 ab d6 c1 79 e6 0f |.2...y..|                                   
000003e8: 32 ef d6 cd bd cf 3a 07 |2.....:.|                                   
000003f0: cf b7 c2 98 d2 31 ab d6 |.....1..|                                   
000003f8: cd 98 cf cd d0 d0 c6 41 |.......A|                                   
00000400: cd 8c cf 3e 3e cd 8c cf |...>>...|                                   
00000408: cd 39 d0 11 80 00 cd d8 |.9......|                                   
00000410: d0 cd d0 d0 32 ef d6 cd |....2...|                                   
00000418: 5e d1 c4 09 d1 3a f0 d6 |^....:..|                                   
00000420: b7 c2 a5 d5 cd 2e d2 21 |.......!|                                   
00000428: c1 d2 5f 16 00 19 19 7e |.._....~|                                   
00000430: 23 66 6f e9 77 d3 1f d4 |#fo.w...|                                   
00000438: 5d d4 ad d4 10 d5 8e d5 |].......|                                   
00000440: a5 d5 21 f3 76 22 00 cf |..!.v"..|                                   
00000448: 21 00 cf e9 01 df d2 c3 |!.......|                                   
00000450: a7 cf 52 45 41 44 20 45 |..READ E|                                   
00000458: 52 52 4f 52 00 01 f0 d2 |RROR....|                                   
00000460: c3 a7 cf 4e 4f 20 46 49 |...NO FI|                                   
00000468: 4c 45 00 cd 5e d1 3a f0 |LE..^.:.|                                   
00000470: d6 b7 c2                |...     |                                   
00000473:          01             |   .    | Sector 4 status                   
00000474:             09 d1 21 ce |    ..!.| Sector 4 uncompressed             
00000478: d6 01 0b 00 7e fe 20 ca |....~. .|                                   
00000480: 33 d3 23 d6 30 fe 0a d2 |3.#.0...|                                   
00000488: 09 d1 57 78 e6 e0 c2 09 |..Wx....|                                   
00000490: d1 78 07 07 07 80 da 09 |.x......|                                   
00000498: d1 80 da 09 d1 82 da 09 |........|                                   
000004a0: d1 47 0d c2 08 d3 c9 7e |.G.....~|                                   
000004a8: fe 20 c2 09 d1 23 0d c2 |. ...#..|                                   
000004b0: 33 d3 78 c9 06 03 7e 12 |3.x...~.|                                   
000004b8: 23 13 05 c2 42 d3 c9 21 |#...B..!|                                   
000004c0: 80 00 81 cd 59 d1 7e c9 |....Y.~.|                                   
000004c8: af 32 cd d6 3a f0 d6 b7 |.2..:...|                                   
000004d0: c8 3d 21 ef d6 be c8 c3 |.=!.....|                                   
000004d8: bd cf 3a f0 d6 b7 c8 3d |..:....=|                                   
000004e0: 21 ef d6 be c8 3a ef d6 |!....:..|                                   
000004e8: c3 bd cf cd 5e d1 cd 54 |....^..T|                                   
000004f0: d3 21 ce d6 7e fe 20 c2 |.!..~. .|                                   
000004f8: 8f d3 06 0b 36 3f 23 05 |....6?#.|                                   
00000500: c2 88 d3 1e 00 d5 cd e9 |........|                                   
00000508: cf cc ea d2 ca 1b d4 3a |.......:|                                   
00000510: ee d6 0f 0f 0f e6 60 4f |......`O|                                   
00000518: 3e 0a cd 4b d3 17 da 0f |>..K....|                                   
00000520: d4 d1 7b 1c d5 e6 03 f5 |..{.....|                                   
00000528: c2 cc d3 cd 98 cf c5 cd |........|                                   
00000530: d0 d0 c1 c6 41 cd 92 cf |....A...|                                   
00000538: 3e 3a cd 92 cf c3 d4 d3 |>:......|                                   
00000540: cd a2 cf 3e 3a cd 92 cf |...>:...|                                   
00000548: cd a2 cf 06 01 78 cd 4b |.....x.K|                                   
00000550: d3 e6 7f fe 20 c2 f9 d3 |.... ...|                                   
00000558: f1 f5 fe 03 c2 f7 d3 3e |.......>|                                   
00000560: 09 cd 4b d3 e6 7f fe 20 |..K.... |                                   
00000568: ca 0e d4 3e 20 cd 92 cf |...> ...|                                   
00000570: 04 78 fe 0c             |.x..    |                                   
00000574:             01          |    .   | Sector 5 status                   
00000575:                d2 0e d4 |     ...| Sector 5 uncompressed             
00000578: fe 09 c2 d9 d3 cd a2 cf |........|                                   
00000580: c3 d9 d3 f1 cd c2 d0 c2 |........|                                   
00000588: 1b d4 cd e4 cf c3 98 d3 |........|                                   
00000590: d1 c3 86 d6 cd 5e d1 fe |.....^..|                                   
00000598: 0b c2 42 d4 01 52 d4 cd |..B..R..|                                   
000005a0: a7 cf cd 39 d0 21 07 cf |...9.!..|                                   
000005a8: 35 c2 82 d2 23 7e fe 59 |5...#~.Y|                                   
000005b0: c2 82 d2 23 22 88 cf cd |...#"...|                                   
000005b8: 54 d3 11 cd d6 cd ef cf |T.......|                                   
000005c0: 3c cc ea d2 c3 86 d6 41 |<......A|                                   
000005c8: 4c 4c 20 28 59 2f 4e 29 |LL (Y/N)|                                   
000005d0: 3f 00 cd 5e d1 c2 09 d1 |?..^....|                                   
000005d8: cd 54 d3 cd d0 cf ca a7 |.T......|                                   
000005e0: d4 cd 98 cf 21 f1 d6 36 |....!..6|                                   
000005e8: ff 21 f1 d6 7e fe 80 da |.!..~...|                                   
000005f0: 87 d4 e5 cd fe cf e1 c2 |........|                                   
000005f8: a0 d4 af 77 34 21 80 00 |...w4!..|                                   
00000600: cd 59 d1 7e fe 1a ca 86 |.Y.~....|                                   
00000608: d6 cd 8c cf cd c2 d0 c2 |........|                                   
00000610: 86 d6 c3 74 d4 3d ca 86 |...t.=..|                                   
00000618: d6 cd d9 d2 cd 66 d3 c3 |.....f..|                                   
00000620: 09 d1 cd f8 d2 f5 cd 5e |.......^|                                   
00000628: d1 c2 09 d1 cd 54 d3 11 |.....T..|                                   
00000630: cd d6 d5 cd ef cf d1 cd |........|                                   
00000638: 09 d0 ca fb d4 af 32 ed |......2.|                                   
00000640: d6 f1 6f 26 00 29 11 00 |..o&.)..|                                   
00000648: 01 7c b5 ca f1 d4 2b e5 |.|....+.|                                   
00000650: 21 80 00 19 e5 cd d8 d0 |!.......|                                   
00000658: 11 cd d6 cd 04 d0 d1 e1 |........|                                   
00000660: c2 fb d4 c3 d4 d4 11 cd |........|                                   
00000668: d6 cd da cf 3c c2 01 d5 |....<...|                                   
00000670: 01 07 d5 cd a7          |.....   |                                   
00000675:                01       |     .  | Sector 6 status                   
00000676:                   cf cd |      ..| Sector 6 uncompressed             
00000678: d5 d0 c3 86 d6 4e 4f 20 |.....NO |                                   
00000680: 53 50 41 43 45 00 cd 5e |SPACE..^|                                   
00000688: d1 c2 09 d1 3a f0 d6 f5 |....:...|                                   
00000690: cd 54 d3 cd e9 cf c2 79 |.T.....y|                                   
00000698: d5 21 cd d6 11 dd d6 06 |.!......|                                   
000006a0: 10 cd 42 d3 2a 88 cf eb |..B.*...|                                   
000006a8: cd 4f d1 fe 3d ca 3f d5 |.O..=.?.|                                   
000006b0: fe 5f c2 73 d5 eb 23 22 |._.s..#"|                                   
000006b8: 88 cf cd 5e d1 c2 73 d5 |...^..s.|                                   
000006c0: f1 47 21 f0 d6 7e b7 ca |.G!..~..|                                   
000006c8: 59 d5 b8 70 c2 73 d5 70 |Y..p.s.p|                                   
000006d0: af 32 cd d6 cd e9 cf ca |.2......|                                   
000006d8: 6d d5 11 cd d6 cd 0e d0 |m.......|                                   
000006e0: c3 86 d6 cd ea d2 c3 86 |........|                                   
000006e8: d6 cd 66 d3 c3 09 d1 01 |..f.....|                                   
000006f0: 82 d5 cd a7 cf c3 86 d6 |........|                                   
000006f8: 46 49 4c 45 20 45 58 49 |FILE EXI|                                   
00000700: 53 54 53 00 cd f8 d2 fe |STS.....|                                   
00000708: 10 d2 09 d1 5f 3a ce d6 |...._:..|                                   
00000710: fe 20 ca 09 d1 cd 15 d0 |. ......|                                   
00000718: c3 89 d6 cd f5 d0 3a ce |......:.|                                   
00000720: d6 fe 20 c2 c4 d5 3a f0 |.. ...:.|                                   
00000728: d6 b7 ca 89 d6 3d 32 ef |.....=2.|                                   
00000730: d6 cd 29 d0 cd bd cf c3 |..).....|                                   
00000738: 89 d6 11 d6 d6 1a fe 20 |....... |                                   
00000740: c2 09 d1 d5 cd 54 d3 d1 |.....T..|                                   
00000748: 21 83 d6 cd 40 d3 cd d0 |!...@...|                                   
00000750: cf ca 6b d6 21 00 01 e5 |..k.!...|                                   
00000758: eb cd d8 d0 11 cd d6 cd |........|                                   
00000760: f9 cf c2 01 d6 e1 11 80 |........|                                   
00000768: 00 19 11 00 cf 7d 93 7c |.....}.||                                   
00000770: 9a d2 71 d6 c3 e1       |..q...  |                                   
00000776:                   01    |      . | Sector 7 status                   
00000777:                      d5 |       .| Sector 7 uncompressed             
00000778: e1 3d c2 71 d6 cd 66 d3 |.=.q..f.|                                   
00000780: cd 5e d1 21 f0 d6 e5 7e |.^.!...~|                                   
00000788: 32 cd d6 3e 10 cd 60 d1 |2..>..`.|                                   
00000790: e1 7e 32 dd d6 af 32 ed |.~2...2.|                                   
00000798: d6 11 5c 00 21 cd d6 06 |..\.!...|                                   
000007a0: 21 cd 42 d3 21 08 cf 7e |!.B.!..~|                                   
000007a8: b7 ca 3e d6 fe 20 ca 3e |..>.. .>|                                   
000007b0: d6 23 c3 30 d6 06 00 11 |.#.0....|                                   
000007b8: 81 00 7e 12 b7 ca 4f d6 |..~...O.|                                   
000007c0: 04 23 13 c3 43 d6 78 32 |.#..C.x2|                                   
000007c8: 80 00 cd 98 cf cd d5 d0 |........|                                   
000007d0: cd 1a d0 cd 00 01 31 ab |......1.|                                   
000007d8: d6 cd 29 d0 cd bd cf c3 |..).....|                                   
000007e0: 82 d2 cd 66 d3 c3 09 d1 |...f....|                                   
000007e8: 01 7a d6 cd a7 cf c3 86 |.z......|                                   
000007f0: d6 42 41 44 20 4c 4f 41 |.BAD LOA|                                   
000007f8: 44 00 43 4f 4d cd 66 d3 |D.COM.f.|                                   
00000800: cd 5e d1 3a ce d6 d6 20 |.^.:... |                                   
00000808: 21 f0 d6 b6 c2 09 d1 c3 |!.......|                                   
00000810: 82 d2 00 00 00 00 00 00 |........|                                   
00000818: 00 00 00 00 00 00 00 00 |........|                                   
00000820: 00 00 00 00 24 24 24 20 |....$$$ |                                   
00000828: 20 20 20 20 53 55 42 00 |    SUB.|                                   
00000830: 00 00 00 00 00 00 00 00 |........|                                   
00000838: 00 00 00 00 00 00 00 00 |........|                                   
00000840: 00 00 00 00 00 00 00 00 |........|                                   
00000848: 00 00 00 00 00 00 00 00 |........|                                   
00000850: 00 00 00 00 00 00 00 00 |........|                                   
00000858: 00 00 00 00 00 00 00 00 |........|                                   
00000860: 00 00 00 00 00 00 00 00 |........|                                   
00000868: 00 00 00 00 00 00 00 00 |........|                                   
00000870: 00 00 00 00 00 00 00    |....... |                                   
00000877:                      01 |       .| Sector 8 status                   
00000878: 02 02 12 00 0a 2a c3 11 |.....*..| Sector 8 uncompressed             
00000880: d7 99 d7 a5 d7 ab d7 b1 |........|                                   
00000888: d7 eb 22 43 da eb 7b 32 |.."C..{2|                                   
00000890: d6 e4 21 00 00 22 45 da |..!.."E.|                                   
00000898: 39 22 0f da 31 41 da af |9"..1A..|                                   
000008a0: 32 e0 e4 32 de e4 21 74 |2..2..!t|                                   
000008a8: e4 e5 79 fe 29 d0 4b 21 |..y.).K!|                                   
000008b0: 47 d7 5f 16 00 19 19 5e |G._....^|                                   
000008b8: 23 56 2a 43 da eb e9 03 |#V*C....|                                   
000008c0: e5 c8 d9 90 d8 ce d9 12 |........|                                   
000008c8: e5 0f e5 d4 d9 ed d9 f3 |........|                                   
000008d0: d9 f8 d9 e1 d8 fe d9 7e |.......~|                                   
000008d8: e3 83 e3 45 e3 9c e3 a5 |...E....|                                   
000008e0: e3 ab e3 c8 e3 d7 e3 e0 |........|                                   
000008e8: e3 e6 e3 ec e3 f5 e3 fe |........|                                   
000008f0: e3 04 e4 0a e4 11 e4 2c |.......,|                                   
000008f8: dc 17 e4 1d e4 26 e4 2d |.....&.-|                                   
00000900: e4 41 e4 47 e4 4d e4 0e |.A.G.M..|                                   
00000908: e3 53 e4 04 da 04 da 9b |.S......|                                   
00000910: e4 21 ca d7 cd e5 d7 fe |.!......|                                   
00000918: 03 ca 00 00 c9 21 d5 d7 |.....!..|                                   
00000920: c3 b4 d7 21 e1 d7 c3 b4 |...!....|                                   
00000928: d7 21 dc d7 cd e5 d7 c3 |.!......|                                   
00000930: 00 00 42 64 6f 73 20 45 |..Bdos E|                                   
00000938: 72 72 20 4f 6e 20 20 3a |rr On  :|                                   
00000940: 20 24 42 61 64 20 53 65 | $Bad Se|                                   
00000948: 63 74 6f 72 24 53 65 6c |ctor$Sel|                                   
00000950: 65 63 74 24 46 69 6c 65 |ect$File|                                   
00000958: 20 52 2f 4f 24 e5 cd c9 | R/O$...|                                   
00000960: d8 3a 42 da c6 41 32 c6 |.:B..A2.|                                   
00000968: d7 01 ba d7 cd d3 d8 c1 |........|                                   
00000970: cd d3 d8 21 0e da 7e 36 |...!..~6|                                   
00000978: 01                      |.       | Sector 9 status                   
00000979:    00 b7 c0 c3 09 e5 cd | .......| Sector 9 uncompressed             
00000980: fb d7 cd 14 d8 d8 f5 4f |.......O|                                   
00000988: cd 90 d8 f1 c9 fe 0d c8 |........|                                   
00000990: fe 0a c8 fe 09 c8 fe 08 |........|                                   
00000998: c8 fe 20 c9 3a 0e da b7 |.. .:...|                                   
000009a0: c2 45 d8 cd 06 e5 e6 01 |.E......|                                   
000009a8: c8 cd 09 e5 fe 13 c2 42 |.......B|                                   
000009b0: d8 cd 09 e5 fe 03 ca 00 |........|                                   
000009b8: 00 af c9 32 0e da 3e 01 |...2..>.|                                   
000009c0: c9 3a 0a da b7 c2 62 d8 |.:....b.|                                   
000009c8: c5 cd 23 d8 c1 c5 cd 0c |..#.....|                                   
000009d0: e5 c1 c5 3a 0d da b7 c4 |...:....|                                   
000009d8: 0f e5 c1 79 21 0c da fe |...y!...|                                   
000009e0: 7f c8 34 fe 20 d0 35 7e |..4. .5~|                                   
000009e8: b7 c8 79 fe 08 c2 79 d8 |..y...y.|                                   
000009f0: 35 c9 fe 0a c0 36 00 c9 |5....6..|                                   
000009f8: 79 cd 14 d8 d2 90 d8 f5 |y.......|                                   
00000a00: 0e 5e cd 48 d8 f1 f6 40 |.^.H...@|                                   
00000a08: 4f 79 fe 09 c2 48 d8 0e |Oy...H..|                                   
00000a10: 20 cd 48 d8 3a 0c da e6 | .H.:...|                                   
00000a18: 07 c2 96 d8 c9 cd ac d8 |........|                                   
00000a20: 0e 20 cd 0c e5 0e 08 c3 |. ......|                                   
00000a28: 0c e5 0e 23 cd 48 d8 cd |...#.H..|                                   
00000a30: c9 d8 3a 0c da 21 0b da |..:..!..|                                   
00000a38: be d0 0e 20 cd 48 d8 c3 |... .H..|                                   
00000a40: b9 d8 0e 0d cd 48 d8 0e |.....H..|                                   
00000a48: 0a c3 48 d8 0a fe 24 c8 |..H...$.|                                   
00000a50: 03 c5 4f cd 90 d8 c1 c3 |..O.....|                                   
00000a58: d3 d8 3a 0c da 32 0b da |..:..2..|                                   
00000a60: 2a 43 da 4e 23 e5 06 00 |*C.N#...|                                   
00000a68: c5 e5 cd fb d7 e6 7f e1 |........|                                   
00000a70: c1 fe 0d ca c1 d9 fe 0a |........|                                   
00000a78: ca                      |.       |                                   
                                                                               
                                                                               
--------  Header for track 1 --------------------------------------------------
00000a79:    02                   | .      | Recording mode                    
00000a7a:       01                |  .     | Cylinder = 1                      
00000a7b:          00             |   .    | Head = 0                          
00000a7c:             0a          |    .   | Sectors = 10                      
00000a7d:                01       |     .  | Sector size = 256                 
00000a7e:                   01 02 |      ..| Sector IDs                        
00000a80: 03 04 05 06 07 08 09 0a |........|                                   
00000a88: 01                      |.       | Sector 0 status                   
00000a89:    c1 d9 fe 08 c2 16 d9 | .......| Sector 0 uncompressed             
00000a90: 78 b7 ca ef d8 05 3a 0c |x.....:.|                                   
00000a98: da 32 0a da c3 70 d9 fe |.2...p..|                                   
00000aa0: 7f c2 26 d9 78 b7 ca ef |..&.x...|                                   
00000aa8: d8 7e 05 2b c3 a9 d9 fe |.~.+....|                                   
00000ab0: 05 c2 37 d9 c5 e5 cd c9 |..7.....|                                   
00000ab8: d8 af 32 0b da c3 f1 d8 |..2.....|                                   
00000ac0: fe 10 c2 48 d9 e5 21 0d |...H..!.|                                   
00000ac8: da 3e 01 96 77 e1 c3 ef |.>..w...|                                   
00000ad0: d8 fe 18 c2 5f d9 e1 3a |...._..:|                                   
00000ad8: 0b da 21 0c da be d2 e1 |..!.....|                                   
00000ae0: d8 35 cd a4 d8 c3 4e d9 |.5....N.|                                   
00000ae8: fe 15 c2 6b d9 cd b1 d8 |...k....|                                   
00000af0: e1 c3 e1 d8 fe 12 c2 a6 |........|                                   
00000af8: d9 c5 cd b1 d8 c1 e1 e5 |........|                                   
00000b00: c5 78 b7 ca 8a d9 23 4e |.x....#N|                                   
00000b08: 05 c5 e5 cd 7f d8 e1 c1 |........|                                   
00000b10: c3 78 d9 e5 3a 0a da b7 |.x..:...|                                   
00000b18: ca f1 d8 21 0c da 96 32 |...!...2|                                   
00000b20: 0a da cd a4 d8 21 0a da |.....!..|                                   
00000b28: 35 c2 99 d9 c3 f1 d8 23 |5......#|                                   
00000b30: 77 04 c5 e5 4f cd 7f d8 |w...O...|                                   
00000b38: e1 c1 7e fe 03 78 c2 bd |..~..x..|                                   
00000b40: d9 fe 01 ca 00 00 b9 da |........|                                   
00000b48: ef d8 e1 70 0e 0d c3 48 |...p...H|                                   
00000b50: d8 cd 06 d8 c3 01 da cd |........|                                   
00000b58: 15 e5 c3 01 da 79 3c ca |.....y<.|                                   
00000b60: e0 d9 3c ca 06 e5 c3 0c |..<.....|                                   
00000b68: e5 cd 06 e5 b7 ca 91 e4 |........|                                   
00000b70: cd 09 e5 c3 01 da 3a 03 |......:.|                                   
00000b78: 00 c3 01 da 21 03 00 71 |....!..q|                                   
00000b80: c9 eb 4d 44 c3 d3 d8 cd |..MD....|                                   
00000b88: 23                      |#       |                                   
00000b89:    01                   | .      | Sector 1 status                   
00000b8a:       d8 32 45 da c9 3e |  .2E..>| Sector 1 uncompressed             


$ dskparse DISK_EXP2.TD0
                                                                               
--------  Teledisk file header ------------------------------------------------
00000000: 54 44                   |TD      | Magic number                      
00000002:       00                |  .     | Volume sequence                   
00000003:          3f             |   ?    | Volume ID                         
00000004:             15          |    .   | Version                           
00000005:                80       |     .  | Data rate                         
00000006:                   01    |      . | Drive type                        
00000007:                      80 |       .| Double track (comment present)    
00000008: 00                      |.       | DOS mode                          
00000009:    01                   | .      | Sides                             
0000000a:       00 21             |  .!    | Header CRC                        
                                                                               
--------  Comment record ------------------------------------------------------
0000000c:             ce d1       |    ..  | Comment CRC                       
0000000e:                   48 00 |      H.| Comment length                    
00000010: 5b 06 1d                |[..     | Date: 1991-07-29                  
00000013:          10 28 25       |   .(%  | Time: 16:40:37                    
00000016:                   43 50 |      CP| Comment                           
00000018: 2f 4d 20 32 2e 32 20 42 |/M 2.2 B|                                   
00000020: 61 73 69 63 20 44 69 73 |asic Dis|                                   
00000028: 6b 20 66 6f 72 20 4f 73 |k for Os|                                   
00000030: 62 6f 72 6e 65 20 31 00 |borne 1.|                                   
00000038: 53 53 53 44 20 32 35 36 |SSSD 256|                                   
00000040: 20 62 79 74 65 20 73 65 | byte se|                                   
00000048: 63 74 6f 72 2c 20 31 2d |ctor, 1-|                                   
00000050: 31 30 2c 20 31 3a 31 00 |10, 1:1.|                                   
00000058: 00 00 00 00 00 00       |......  |                                   
                                                                               
--------  Track header --------------------------------------------------------
0000005e:                   0a    |      . | Track header: SPT      =  10      
0000005f:                      00 |       .| Track header: Cylinder =   0      
00000060: 80                      |.       | Track header: Head     =   0 FM   
00000061:    07                   | .      | Track header: CRC                 
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 0 -------------------------
00000062:       00                |  .     | ID: Cylinder                      
00000063:          00             |   .    | ID: Head                          
00000064:             01          |    .   | ID: Sector                        
00000065:                01       |     .  | ID: Sector size                   
00000066:                   00    |      . | Syndrome                          
00000067:                      9b |       .| Header CRC                        
00000068: b5 00                   |..      | Sector data length                
0000006a:       02                |  .     | Sector data encoding              
0000006b:          00             |   .    | RLE2: Type                        
0000006c:             08          |    .   | RLE2: Length                      
0000006d:                c3 5c d2 |     .\.| RLE2: Uncompressed data           
00000070: c3 58 d2 7f 00          |.X...   |                                   
00000075:                01       |     .  | RLE2: Type                        
00000076:                   08    |      . | RLE2: Length                      
00000077:                      20 |        | RLE2: Compressed data             
00000078: 20                      |        |                                   
00000079:    00                   | .      | RLE2: Type                        
0000007a:       26                |  &     | RLE2: Length                      
0000007b:          43 4f 50 59 52 |   COPYR| RLE2: Uncompressed data           
00000080: 49 47 48 54 20 28 43 29 |IGHT (C)|                                   
00000088: 20 31 39 37 39 2c 20 44 | 1979, D|                                   
00000090: 49 47 49 54 41 4c 20 52 |IGITAL R|                                   
00000098: 45 53 45 41 52 43 48 20 |ESEARCH |                                   
000000a0: 20                      |        |                                   
000000a1:    01                   | .      | RLE2: Type                        
000000a2:       25                |  %     | RLE2: Length                      
000000a3:          00 00          |   ..   | RLE2: Compressed data             
000000a5:                00       |     .  | RLE2: Type                        
000000a6:                   78    |      x | RLE2: Length                      
000000a7:                      08 |       .| RLE2: Uncompressed data           
000000a8: cf 00 00 5f 0e 02 c3 05 |..._....|                                   
000000b0: 00 c5 cd 8c cf c1 c9 3e |.......>|                                   
000000b8: 0d cd 92 cf 3e 0a c3 92 |....>...|                                   
000000c0: cf 3e 20 c3 92 cf c5 cd |.> .....|                                   
000000c8: 98 cf e1 7e b7 c8 23 e5 |...~..#.|                                   
000000d0: cd 8c cf e1 c3 ac cf 0e |........|                                   
000000d8: 0d c3 05 00 5f 0e 0e c3 |...._...|                                   
000000e0: 05 00 cd 05 00 32 ee d6 |.....2..|                                   
000000e8: 3c c9 0e 0f c3 c3 cf af |<.......|                                   
000000f0: 32 ed d6 11 cd d6 c3 cb |2.......|                                   
000000f8: cf 0e 10 c3 c3 cf 0e 11 |........|                                   
00000100: c3 c3 cf 0e 12 c3 c3 cf |........|                                   
00000108: 11 cd d6 c3 df cf 0e 13 |........|                                   
00000110: c3 05 00 cd 05 00 b7 c9 |........|                                   
00000118: 0e 14 c3 f4 cf 11 cd    |....... |                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 1 -------------------------
0000011f:                      00 |       .| ID: Cylinder                      
00000120: 00                      |.       | ID: Head                          
00000121:    02                   | .      | ID: Sector                        
00000122:       01                |  .     | ID: Sector size                   
00000123:          00             |   .    | Syndrome                          
00000124:             16          |    .   | Header CRC                        
00000125:                01 01    |     .. | Sector data length                
00000127:                      00 |       .| Sector data encoding              
00000128: d6 c3 f9 cf 0e 15 c3 f4 |........| Uncompressed sector data          
00000130: cf 0e 16 c3 c3 cf 0e 17 |........|                                   
00000138: c3 05 00 1e ff 0e 20 c3 |...... .|                                   
00000140: 05 00 cd 13 d0 87 87 87 |........|                                   
00000148: 87 21 ef d6 b6 32 04 00 |.!...2..|                                   
00000150: c9 3a ef d6 32 04 00 c9 |.:..2...|                                   
00000158: fe 61 d8 fe 7b d0 e6 5f |.a..{.._|                                   
00000160: c9 3a ab d6 b7 ca 96 d0 |.:......|                                   
00000168: 3a ef d6 b7 3e 00 c4 bd |:...>...|                                   
00000170: cf 11 ac d6 cd cb cf ca |........|                                   
00000178: 96 d0 3a bb d6 3d 32 cc |..:..=2.|                                   
00000180: d6 11 ac d6 cd f9 cf c2 |........|                                   
00000188: 96 d0 11 07 cf 21 80 00 |.....!..|                                   
00000190: 06 80 cd 42 d3 21 ba d6 |...B.!..|                                   
00000198: 36 00 23 35 11 ac d6 cd |6.#5....|                                   
000001a0: da cf ca 96 d0 3a ef d6 |.....:..|                                   
000001a8: b7 c4 bd cf 21 08 cf cd |....!...|                                   
000001b0: ac cf cd c2 d0 ca a7 d0 |........|                                   
000001b8: cd dd d0 c3 82 d2 cd dd |........|                                   
000001c0: d0 cd 1a d0 0e 0a 11 06 |........|                                   
000001c8: cf cd 05 00 cd 29 d0 21 |.....).!|                                   
000001d0: 07 cf 46 23 78 b7 ca ba |..F#x...|                                   
000001d8: d0 7e cd 30 d0 77 05 c3 |.~.0.w..|                                   
000001e0: ab d0 77 21 08 cf 22 88 |..w!..".|                                   
000001e8: cf c9 0e 0b cd 05 00 b7 |........|                                   
000001f0: c8 0e 01 cd 05 00 b7 c9 |........|                                   
000001f8: 0e 19 c3 05 00 11 80 00 |........|                                   
00000200: 0e 1a c3 05 00 21 ab d6 |.....!..|                                   
00000208: 7e b7 c8 36 00 af cd bd |~..6....|                                   
00000210: cf 11 ac d6 cd ef cf 3a |.......:|                                   
00000218: ef d6 c3 bd cf 11 28 d2 |......(.|                                   
00000220: 21 00 d7 06 06 1a be c2 |!.......|                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 2 -------------------------
00000228: 00                      |.       | ID: Cylinder                      
00000229:    00                   | .      | ID: Head                          
0000022a:       03                |  .     | ID: Sector                        
0000022b:          01             |   .    | ID: Sector size                   
0000022c:             00          |    .   | Syndrome                          
0000022d:                2b       |     +  | Header CRC                        
0000022e:                   01 01 |      ..| Sector data length                
00000230: 00                      |.       | Sector data encoding              
00000231:    cf d2 13 23 05 c2 fd | ...#...| Uncompressed sector data          
00000238: d0 c9 cd 98 cf 2a 8a cf |.....*..|                                   
00000240: 7e fe 20 ca 22 d1 b7 ca |~. ."...|                                   
00000248: 22 d1 e5 cd 8c cf e1 23 |"......#|                                   
00000250: c3 0f d1 3e 3f cd 8c cf |...>?...|                                   
00000258: cd 98 cf cd dd d0 c3 82 |........|                                   
00000260: d2 1a b7 c8 fe 20 da 09 |..... ..|                                   
00000268: d1 c8 fe 3d c8 fe 5f c8 |...=.._.|                                   
00000270: fe 2e c8 fe 3a c8 fe 3b |....:..;|                                   
00000278: c8 fe 3c c8 fe 3e c8 c9 |..<..>..|                                   
00000280: 1a b7 c8 fe 20 c0 13 c3 |.... ...|                                   
00000288: 4f d1 85 6f d0 24 c9 3e |O..o.$.>|                                   
00000290: 00 21 cd d6 cd 59 d1 e5 |.!...Y..|                                   
00000298: e5 af 32 f0 d6 2a 88 cf |..2..*..|                                   
000002a0: eb cd 4f d1 eb 22 8a cf |..O.."..|                                   
000002a8: eb e1 1a b7 ca 89 d1 de |........|                                   
000002b0: 40 47 13 1a fe 3a ca 90 |@G...:..|                                   
000002b8: d1 1b 3a ef d6 77 c3 96 |..:..w..|                                   
000002c0: d1 78 32 f0 d6 70 13 06 |.x2..p..|                                   
000002c8: 08 cd 30 d1 ca b9 d1 23 |..0....#|                                   
000002d0: fe 2a c2 a9 d1 36 3f c3 |.*...6?.|                                   
000002d8: ab d1 77 13 05 c2 98 d1 |..w.....|                                   
000002e0: cd 30 d1 ca c0 d1 13 c3 |.0......|                                   
000002e8: af d1 23 36 20 05 c2 b9 |..#6 ...|                                   
000002f0: d1 06 03 fe 2e c2 e9 d1 |........|                                   
000002f8: 13 cd 30 d1 ca e9 d1 23 |..0....#|                                   
00000300: fe 2a c2 d9 d1 36 3f c3 |.*...6?.|                                   
00000308: db d1 77 13 05 c2 c8 d1 |..w.....|                                   
00000310: cd 30 d1 ca f0 d1 13 c3 |.0......|                                   
00000318: df d1 23 36 20 05 c2 e9 |..#6 ...|                                   
00000320: d1 06 03 23 36 00 05 c2 |...#6...|                                   
00000328: f2 d1 eb 22 88 cf e1 01 |..."....|                                   
00000330: 0b                      |.       |                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 3 -------------------------
00000331:    00                   | .      | ID: Cylinder                      
00000332:       00                |  .     | ID: Head                          
00000333:          04             |   .    | ID: Sector                        
00000334:             01          |    .   | ID: Sector size                   
00000335:                00       |     .  | Syndrome                          
00000336:                   49    |      I | Header CRC                        
00000337:                      01 |       .| Sector data length                
00000338: 01                      |.       |                                   
00000339:    00                   | .      | Sector data encoding              
0000033a:       00 23 7e fe 3f c2 |  .#~.?.| Uncompressed sector data          
00000340: 09 d2 04 0d c2 01 d2 78 |.......x|                                   
00000348: b7 c9 44 49 52 20 45 52 |..DIR ER|                                   
00000350: 41 20 54 59 50 45 53 41 |A TYPESA|                                   
00000358: 56 45 52 45 4e 20 55 53 |VEREN US|                                   
00000360: 45 52 02 02 12 00 0a 2a |ER.....*|                                   
00000368: 21 10 d2 0e 00 79 fe 06 |!....y..|                                   
00000370: d0 11 ce d6 06 04 1a be |........|                                   
00000378: c2 4f d2 13 23 05 c2 3c |.O..#..<|                                   
00000380: d2 1a fe 20 c2 54 d2 79 |... .T.y|                                   
00000388: c9 23 05 c2 4f d2 0c c3 |.#..O...|                                   
00000390: 33 d2 af 32 07 cf 31 ab |3..2..1.|                                   
00000398: d6 c5 79 1f 1f 1f 1f e6 |..y.....|                                   
000003a0: 0f 5f cd 15 d0 cd b8 cf |._......|                                   
000003a8: 32 ab d6 c1 79 e6 0f 32 |2...y..2|                                   
000003b0: ef d6 cd bd cf 3a 07 cf |.....:..|                                   
000003b8: b7 c2 98 d2 31 ab d6 cd |....1...|                                   
000003c0: 98 cf cd d0 d0 c6 41 cd |......A.|                                   
000003c8: 8c cf 3e 3e cd 8c cf cd |..>>....|                                   
000003d0: 39 d0 11 80 00 cd d8 d0 |9.......|                                   
000003d8: cd d0 d0 32 ef d6 cd 5e |...2...^|                                   
000003e0: d1 c4 09 d1 3a f0 d6 b7 |....:...|                                   
000003e8: c2 a5 d5 cd 2e d2 21 c1 |......!.|                                   
000003f0: d2 5f 16 00 19 19 7e 23 |._....~#|                                   
000003f8: 66 6f e9 77 d3 1f d4 5d |fo.w...]|                                   
00000400: d4 ad d4 10 d5 8e d5 a5 |........|                                   
00000408: d5 21 f3 76 22 00 cf 21 |.!.v"..!|                                   
00000410: 00 cf e9 01 df d2 c3 a7 |........|                                   
00000418: cf 52 45 41 44 20 45 52 |.READ ER|                                   
00000420: 52 4f 52 00 01 f0 d2 c3 |ROR.....|                                   
00000428: a7 cf 4e 4f 20 46 49 4c |..NO FIL|                                   
00000430: 45 00 cd 5e d1 3a f0 d6 |E..^.:..|                                   
00000438: b7 c2                   |..      |                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 4 -------------------------
0000043a:       00                |  .     | ID: Cylinder                      
0000043b:          00             |   .    | ID: Head                          
0000043c:             05          |    .   | ID: Sector                        
0000043d:                01       |     .  | ID: Sector size                   
0000043e:                   00    |      . | Syndrome                          
0000043f:                      7f |       .| Header CRC                        
00000440: 01 01                   |..      | Sector data length                
00000442:       00                |  .     | Sector data encoding              
00000443:          09 d1 21 ce d6 |   ..!..| Uncompressed sector data          
00000448: 01 0b 00 7e fe 20 ca 33 |...~. .3|                                   
00000450: d3 23 d6 30 fe 0a d2 09 |.#.0....|                                   
00000458: d1 57 78 e6 e0 c2 09 d1 |.Wx.....|                                   
00000460: 78 07 07 07 80 da 09 d1 |x.......|                                   
00000468: 80 da 09 d1 82 da 09 d1 |........|                                   
00000470: 47 0d c2 08 d3 c9 7e fe |G.....~.|                                   
00000478: 20 c2 09 d1 23 0d c2 33 | ...#..3|                                   
00000480: d3 78 c9 06 03 7e 12 23 |.x...~.#|                                   
00000488: 13 05 c2 42 d3 c9 21 80 |...B..!.|                                   
00000490: 00 81 cd 59 d1 7e c9 af |...Y.~..|                                   
00000498: 32 cd d6 3a f0 d6 b7 c8 |2..:....|                                   
000004a0: 3d 21 ef d6 be c8 c3 bd |=!......|                                   
000004a8: cf 3a f0 d6 b7 c8 3d 21 |.:....=!|                                   
000004b0: ef d6 be c8 3a ef d6 c3 |....:...|                                   
000004b8: bd cf cd 5e d1 cd 54 d3 |...^..T.|                                   
000004c0: 21 ce d6 7e fe 20 c2 8f |!..~. ..|                                   
000004c8: d3 06 0b 36 3f 23 05 c2 |...6?#..|                                   
000004d0: 88 d3 1e 00 d5 cd e9 cf |........|                                   
000004d8: cc ea d2 ca 1b d4 3a ee |......:.|                                   
000004e0: d6 0f 0f 0f e6 60 4f 3e |.....`O>|                                   
000004e8: 0a cd 4b d3 17 da 0f d4 |..K.....|                                   
000004f0: d1 7b 1c d5 e6 03 f5 c2 |.{......|                                   
000004f8: cc d3 cd 98 cf c5 cd d0 |........|                                   
00000500: d0 c1 c6 41 cd 92 cf 3e |...A...>|                                   
00000508: 3a cd 92 cf c3 d4 d3 cd |:.......|                                   
00000510: a2 cf 3e 3a cd 92 cf cd |..>:....|                                   
00000518: a2 cf 06 01 78 cd 4b d3 |....x.K.|                                   
00000520: e6 7f fe 20 c2 f9 d3 f1 |... ....|                                   
00000528: f5 fe 03 c2 f7 d3 3e 09 |......>.|                                   
00000530: cd 4b d3 e6 7f fe 20 ca |.K.... .|                                   
00000538: 0e d4 3e 20 cd 92 cf 04 |..> ....|                                   
00000540: 78 fe 0c                |x..     |                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 5 -------------------------
00000543:          00             |   .    | ID: Cylinder                      
00000544:             00          |    .   | ID: Head                          
00000545:                06       |     .  | ID: Sector                        
00000546:                   01    |      . | ID: Sector size                   
00000547:                      00 |       .| Syndrome                          
00000548: dc                      |.       | Header CRC                        
00000549:    01 01                | ..     | Sector data length                
0000054b:          00             |   .    | Sector data encoding              
0000054c:             d2 0e d4 fe |    ....| Uncompressed sector data          
00000550: 09 c2 d9 d3 cd a2 cf c3 |........|                                   
00000558: d9 d3 f1 cd c2 d0 c2 1b |........|                                   
00000560: d4 cd e4 cf c3 98 d3 d1 |........|                                   
00000568: c3 86 d6 cd 5e d1 fe 0b |....^...|                                   
00000570: c2 42 d4 01 52 d4 cd a7 |.B..R...|                                   
00000578: cf cd 39 d0 21 07 cf 35 |..9.!..5|                                   
00000580: c2 82 d2 23 7e fe 59 c2 |...#~.Y.|                                   
00000588: 82 d2 23 22 88 cf cd 54 |..#"...T|                                   
00000590: d3 11 cd d6 cd ef cf 3c |.......<|                                   
00000598: cc ea d2 c3 86 d6 41 4c |......AL|                                   
000005a0: 4c 20 28 59 2f 4e 29 3f |L (Y/N)?|                                   
000005a8: 00 cd 5e d1 c2 09 d1 cd |..^.....|                                   
000005b0: 54 d3 cd d0 cf ca a7 d4 |T.......|                                   
000005b8: cd 98 cf 21 f1 d6 36 ff |...!..6.|                                   
000005c0: 21 f1 d6 7e fe 80 da 87 |!..~....|                                   
000005c8: d4 e5 cd fe cf e1 c2 a0 |........|                                   
000005d0: d4 af 77 34 21 80 00 cd |..w4!...|                                   
000005d8: 59 d1 7e fe 1a ca 86 d6 |Y.~.....|                                   
000005e0: cd 8c cf cd c2 d0 c2 86 |........|                                   
000005e8: d6 c3 74 d4 3d ca 86 d6 |..t.=...|                                   
000005f0: cd d9 d2 cd 66 d3 c3 09 |....f...|                                   
000005f8: d1 cd f8 d2 f5 cd 5e d1 |......^.|                                   
00000600: c2 09 d1 cd 54 d3 11 cd |....T...|                                   
00000608: d6 d5 cd ef cf d1 cd 09 |........|                                   
00000610: d0 ca fb d4 af 32 ed d6 |.....2..|                                   
00000618: f1 6f 26 00 29 11 00 01 |.o&.)...|                                   
00000620: 7c b5 ca f1 d4 2b e5 21 ||....+.!|                                   
00000628: 80 00 19 e5 cd d8 d0 11 |........|                                   
00000630: cd d6 cd 04 d0 d1 e1 c2 |........|                                   
00000638: fb d4 c3 d4 d4 11 cd d6 |........|                                   
00000640: cd da cf 3c c2 01 d5 01 |...<....|                                   
00000648: 07 d5 cd a7             |....    |                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 6 -------------------------
0000064c:             00          |    .   | ID: Cylinder                      
0000064d:                00       |     .  | ID: Head                          
0000064e:                   07    |      . | ID: Sector                        
0000064f:                      01 |       .| ID: Sector size                   
00000650: 00                      |.       | Syndrome                          
00000651:    e9                   | .      | Header CRC                        
00000652:       01 01             |  ..    | Sector data length                
00000654:             00          |    .   | Sector data encoding              
00000655:                cf cd d5 |     ...| Uncompressed sector data          
00000658: d0 c3 86 d6 4e 4f 20 53 |....NO S|                                   
00000660: 50 41 43 45 00 cd 5e d1 |PACE..^.|                                   
00000668: c2 09 d1 3a f0 d6 f5 cd |...:....|                                   
00000670: 54 d3 cd e9 cf c2 79 d5 |T.....y.|                                   
00000678: 21 cd d6 11 dd d6 06 10 |!.......|                                   
00000680: cd 42 d3 2a 88 cf eb cd |.B.*....|                                   
00000688: 4f d1 fe 3d ca 3f d5 fe |O..=.?..|                                   
00000690: 5f c2 73 d5 eb 23 22 88 |_.s..#".|                                   
00000698: cf cd 5e d1 c2 73 d5 f1 |..^..s..|                                   
000006a0: 47 21 f0 d6 7e b7 ca 59 |G!..~..Y|                                   
000006a8: d5 b8 70 c2 73 d5 70 af |..p.s.p.|                                   
000006b0: 32 cd d6 cd e9 cf ca 6d |2......m|                                   
000006b8: d5 11 cd d6 cd 0e d0 c3 |........|                                   
000006c0: 86 d6 cd ea d2 c3 86 d6 |........|                                   
000006c8: cd 66 d3 c3 09 d1 01 82 |.f......|                                   
000006d0: d5 cd a7 cf c3 86 d6 46 |.......F|                                   
000006d8: 49 4c 45 20 45 58 49 53 |ILE EXIS|                                   
000006e0: 54 53 00 cd f8 d2 fe 10 |TS......|                                   
000006e8: d2 09 d1 5f 3a ce d6 fe |..._:...|                                   
000006f0: 20 ca 09 d1 cd 15 d0 c3 | .......|                                   
000006f8: 89 d6 cd f5 d0 3a ce d6 |.....:..|                                   
00000700: fe 20 c2 c4 d5 3a f0 d6 |. ...:..|                                   
00000708: b7 ca 89 d6 3d 32 ef d6 |....=2..|                                   
00000710: cd 29 d0 cd bd cf c3 89 |.)......|                                   
00000718: d6 11 d6 d6 1a fe 20 c2 |...... .|                                   
00000720: 09 d1 d5 cd 54 d3 d1 21 |....T..!|                                   
00000728: 83 d6 cd 40 d3 cd d0 cf |...@....|                                   
00000730: ca 6b d6 21 00 01 e5 eb |.k.!....|                                   
00000738: cd d8 d0 11 cd d6 cd f9 |........|                                   
00000740: cf c2 01 d6 e1 11 80 00 |........|                                   
00000748: 19 11 00 cf 7d 93 7c 9a |....}.|.|                                   
00000750: d2 71 d6 c3 e1          |.q...   |                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 7 -------------------------
00000755:                00       |     .  | ID: Cylinder                      
00000756:                   00    |      . | ID: Head                          
00000757:                      08 |       .| ID: Sector                        
00000758: 01                      |.       | ID: Sector size                   
00000759:    00                   | .      | Syndrome                          
0000075a:       fd                |  .     | Header CRC                        
0000075b:          b3 00          |   ..   | Sector data length                
0000075d:                02       |     .  | Sector data encoding              
0000075e:                   00    |      . | RLE2: Type                        
0000075f:                      9b |       .| RLE2: Length                      
00000760: d5 e1 3d c2 71 d6 cd 66 |..=.q..f| RLE2: Uncompressed data           
00000768: d3 cd 5e d1 21 f0 d6 e5 |..^.!...|                                   
00000770: 7e 32 cd d6 3e 10 cd 60 |~2..>..`|                                   
00000778: d1 e1 7e 32 dd d6 af 32 |..~2...2|                                   
00000780: ed d6 11 5c 00 21 cd d6 |...\.!..|                                   
00000788: 06 21 cd 42 d3 21 08 cf |.!.B.!..|                                   
00000790: 7e b7 ca 3e d6 fe 20 ca |~..>.. .|                                   
00000798: 3e d6 23 c3 30 d6 06 00 |>.#.0...|                                   
000007a0: 11 81 00 7e 12 b7 ca 4f |...~...O|                                   
000007a8: d6 04 23 13 c3 43 d6 78 |..#..C.x|                                   
000007b0: 32 80 00 cd 98 cf cd d5 |2.......|                                   
000007b8: d0 cd 1a d0 cd 00 01 31 |.......1|                                   
000007c0: ab d6 cd 29 d0 cd bd cf |...)....|                                   
000007c8: c3 82 d2 cd 66 d3 c3 09 |....f...|                                   
000007d0: d1 01 7a d6 cd a7 cf c3 |..z.....|                                   
000007d8: 86 d6 42 41 44 20 4c 4f |..BAD LO|                                   
000007e0: 41 44 00 43 4f 4d cd 66 |AD.COM.f|                                   
000007e8: d3 cd 5e d1 3a ce d6 d6 |..^.:...|                                   
000007f0: 20 21 f0 d6 b6 c2 09 d1 | !......|                                   
000007f8: c3 82 d2                |...     |                                   
000007fb:          01             |   .    | RLE2: Type                        
000007fc:             09          |    .   | RLE2: Length                      
000007fd:                00 00    |     .. | RLE2: Compressed data             
000007ff:                      00 |       .| RLE2: Type                        
00000800: 0b                      |.       | RLE2: Length                      
00000801:    24 24 24 20 20 20 20 | $$$    | RLE2: Uncompressed data           
00000808: 20 53 55 42             | SUB    |                                   
0000080c:             01          |    .   | RLE2: Type                        
0000080d:                24       |     $  | RLE2: Length                      
0000080e:                   00 00 |      ..| RLE2: Compressed data             
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 8 -------------------------
00000810: 00                      |.       | ID: Cylinder                      
00000811:    00                   | .      | ID: Head                          
00000812:       09                |  .     | ID: Sector                        
00000813:          01             |   .    | ID: Sector size                   
00000814:             00          |    .   | Syndrome                          
00000815:                69       |     i  | Header CRC                        
00000816:                   01 01 |      ..| Sector data length                
00000818: 00                      |.       | Sector data encoding              
00000819:    02 02 12 00 0a 2a c3 | .....*.| Uncompressed sector data          
00000820: 11 d7 99 d7 a5 d7 ab d7 |........|                                   
00000828: b1 d7 eb 22 43 da eb 7b |..."C..{|                                   
00000830: 32 d6 e4 21 00 00 22 45 |2..!.."E|                                   
00000838: da 39 22 0f da 31 41 da |.9"..1A.|                                   
00000840: af 32 e0 e4 32 de e4 21 |.2..2..!|                                   
00000848: 74 e4 e5 79 fe 29 d0 4b |t..y.).K|                                   
00000850: 21 47 d7 5f 16 00 19 19 |!G._....|                                   
00000858: 5e 23 56 2a 43 da eb e9 |^#V*C...|                                   
00000860: 03 e5 c8 d9 90 d8 ce d9 |........|                                   
00000868: 12 e5 0f e5 d4 d9 ed d9 |........|                                   
00000870: f3 d9 f8 d9 e1 d8 fe d9 |........|                                   
00000878: 7e e3 83 e3 45 e3 9c e3 |~...E...|                                   
00000880: a5 e3 ab e3 c8 e3 d7 e3 |........|                                   
00000888: e0 e3 e6 e3 ec e3 f5 e3 |........|                                   
00000890: fe e3 04 e4 0a e4 11 e4 |........|                                   
00000898: 2c dc 17 e4 1d e4 26 e4 |,.....&.|                                   
000008a0: 2d e4 41 e4 47 e4 4d e4 |-.A.G.M.|                                   
000008a8: 0e e3 53 e4 04 da 04 da |..S.....|                                   
000008b0: 9b e4 21 ca d7 cd e5 d7 |..!.....|                                   
000008b8: fe 03 ca 00 00 c9 21 d5 |......!.|                                   
000008c0: d7 c3 b4 d7 21 e1 d7 c3 |....!...|                                   
000008c8: b4 d7 21 dc d7 cd e5 d7 |..!.....|                                   
000008d0: c3 00 00 42 64 6f 73 20 |...Bdos |                                   
000008d8: 45 72 72 20 4f 6e 20 20 |Err On  |                                   
000008e0: 3a 20 24 42 61 64 20 53 |: $Bad S|                                   
000008e8: 65 63 74 6f 72 24 53 65 |ector$Se|                                   
000008f0: 6c 65 63 74 24 46 69 6c |lect$Fil|                                   
000008f8: 65 20 52 2f 4f 24 e5 cd |e R/O$..|                                   
00000900: c9 d8 3a 42 da c6 41 32 |..:B..A2|                                   
00000908: c6 d7 01 ba d7 cd d3 d8 |........|                                   
00000910: c1 cd d3 d8 21 0e da 7e |....!..~|                                   
00000918: 36                      |6       |                                   
                                                                               
--------  Sector header: Cylinder 0  Head 0  Sector 9 -------------------------
00000919:    00                   | .      | ID: Cylinder                      
0000091a:       00                |  .     | ID: Head                          
0000091b:          0a             |   .    | ID: Sector                        
0000091c:             01          |    .   | ID: Sector size                   
0000091d:                00       |     .  | Syndrome                          
0000091e:                   54    |      T | Header CRC                        
0000091f:                      01 |       .| Sector data length                
00000920: 01                      |.       |                                   
00000921:    00                   | .      | Sector data encoding              
00000922:       00 b7 c0 c3 09 e5 |  ......| Uncompressed sector data          
00000928: cd fb d7 cd 14 d8 d8 f5 |........|                                   
00000930: 4f cd 90 d8 f1 c9 fe 0d |O.......|                                   
00000938: c8 fe 0a c8 fe 09 c8 fe |........|                                   
00000940: 08 c8 fe 20 c9 3a 0e da |... .:..|                                   
00000948: b7 c2 45 d8 cd 06 e5 e6 |..E.....|                                   
00000950: 01 c8 cd 09 e5 fe 13 c2 |........|                                   
00000958: 42 d8 cd 09 e5 fe 03 ca |B.......|                                   
00000960: 00 00 af c9 32 0e da 3e |....2..>|                                   
00000968: 01 c9 3a 0a da b7 c2 62 |..:....b|                                   
00000970: d8 c5 cd 23 d8 c1 c5 cd |...#....|                                   
00000978: 0c e5 c1 c5 3a 0d da b7 |....:...|                                   
00000980: c4 0f e5 c1 79 21 0c da |....y!..|                                   
00000988: fe 7f c8 34 fe 20 d0 35 |...4. .5|                                   
00000990: 7e b7 c8 79 fe 08 c2 79 |~..y...y|                                   
00000998: d8 35 c9 fe 0a c0 36 00 |.5....6.|                                   
000009a0: c9 79 cd 14 d8 d2 90 d8 |.y......|                                   
000009a8: f5 0e 5e cd 48 d8 f1 f6 |..^.H...|                                   
000009b0: 40 4f 79 fe 09 c2 48 d8 |@Oy...H.|                                   
000009b8: 0e 20 cd 48 d8 3a 0c da |. .H.:..|                                   
000009c0: e6 07 c2 96 d8 c9 cd ac |........|                                   
000009c8: d8 0e 20 cd 0c e5 0e 08 |.. .....|                                   
000009d0: c3 0c e5 0e 23 cd 48 d8 |....#.H.|                                   
000009d8: cd c9 d8 3a 0c da 21 0b |...:..!.|                                   
000009e0: da be d0 0e 20 cd 48 d8 |.... .H.|                                   
000009e8: c3 b9 d8 0e 0d cd 48 d8 |......H.|                                   
000009f0: 0e 0a c3 48 d8 0a fe 24 |...H...$|                                   
000009f8: c8 03 c5 4f cd 90 d8 c1 |...O....|                                   
00000a00: c3 d3 d8 3a 0c da 32 0b |...:..2.|                                   
00000a08: da 2a 43 da 4e 23 e5 06 |.*C.N#..|                                   
00000a10: 00 c5 e5 cd fb d7 e6 7f |........|                                   
00000a18: e1 c1 fe 0d ca c1 d9 fe |........|                                   
00000a20: 0a ca                   |..      |                                   
                                                                               
--------  Track header --------------------------------------------------------
00000a22:       0a                |  .     | Track header: SPT      =  10      
00000a23:          01             |   .    | Track header: Cylinder =   1      
00000a24:             80          |    .   | Track header: Head     =   0 FM   
00000a25:                8e       |     .  | Track header: CRC                 
                                                                               
--------  Sector header: Cylinder 1  Head 0  Sector 0 -------------------------
00000a26:                   01    |      . | ID: Cylinder                      
00000a27:                      00 |       .| ID: Head                          
00000a28: 01                      |.       | ID: Sector                        
00000a29:    01                   | .      | ID: Sector size                   
00000a2a:       00                |  .     | Syndrome                          
00000a2b:          b5             |   .    | Header CRC                        
00000a2c:             01 01       |    ..  | Sector data length                
00000a2e:                   00    |      . | Sector data encoding              
00000a2f:                      c1 |       .| Uncompressed sector data          
00000a30: d9 fe 08 c2 16 d9 78 b7 |......x.|                                   
00000a38: ca ef d8 05 3a 0c da 32 |....:..2|                                   
00000a40: 0a da c3 70 d9 fe 7f c2 |...p....|                                   
00000a48: 26 d9 78 b7 ca ef d8 7e |&.x....~|                                   
00000a50: 05 2b c3 a9 d9 fe 05 c2 |.+......|                                   
00000a58: 37 d9 c5 e5 cd c9 d8 af |7.......|                                   
00000a60: 32 0b da c3 f1 d8 fe 10 |2.......|                                   
00000a68: c2 48 d9 e5 21 0d da 3e |.H..!..>|                                   
00000a70: 01 96 77 e1 c3 ef d8 fe |..w.....|                                   
00000a78: 18 c2 5f d9 e1 3a 0b da |.._..:..|                                   
00000a80: 21 0c da be d2 e1 d8 35 |!......5|                                   
00000a88: cd a4 d8 c3 4e d9 fe 15 |....N...|                                   
00000a90: c2 6b d9 cd b1 d8 e1 c3 |.k......|                                   
00000a98: e1 d8 fe 12 c2 a6 d9 c5 |........|                                   
00000aa0: cd b1 d8 c1 e1 e5 c5 78 |.......x|                                   
00000aa8: b7 ca 8a d9 23 4e 05 c5 |....#N..|                                   
00000ab0: e5 cd 7f d8 e1 c1 c3 78 |.......x|                                   
00000ab8: d9 e5 3a 0a da b7 ca f1 |..:.....|                                   
00000ac0: d8 21 0c da 96 32 0a da |.!...2..|                                   
00000ac8: cd a4 d8 21 0a da 35 c2 |...!..5.|                                   
00000ad0: 99 d9 c3 f1 d8 23 77 04 |.....#w.|                                   
00000ad8: c5 e5 4f cd 7f d8 e1 c1 |..O.....|                                   
00000ae0: 7e fe 03 78 c2 bd d9 fe |~..x....|                                   
00000ae8: 01 ca 00 00 b9 da ef d8 |........|                                   
00000af0: e1 70 0e 0d c3 48 d8 cd |.p...H..|                                   
00000af8: 06 d8 c3 01 da cd 15 e5 |........|                                   
00000b00: c3 01 da 79 3c ca e0 d9 |...y<...|                                   
00000b08: 3c ca 06 e5 c3 0c e5 cd |<.......|                                   
00000b10: 06 e5 b7 ca 91 e4 cd 09 |........|                                   
00000b18: e5 c3 01 da 3a 03 00 c3 |....:...|                                   
00000b20: 01 da 21 03 00 71 c9 eb |..!..q..|                                   
00000b28: 4d 44 c3 d3 d8 cd 23    |MD....# |                                   
                                                                               
--------  Sector header: Cylinder 1  Head 0  Sector 1 -------------------------
00000b2f:                      01 |       .| ID: Cylinder                      
00000b30: 00                      |.       | ID: Head                          
00000b31:    02                   | .      | ID: Sector                        
00000b32:       01                |  .     | ID: Sector size                   
00000b33:          00             |   .    | Syndrome                          
00000b34:             b5          |    .   | Header CRC                        
00000b35:                cd 00    |     .. | Sector data length                
00000b37:                      02 |       .| Sector data encoding              
00000b38: 00                      |.       | RLE2: Type                        
00000b39:    0a                   | .      | RLE2: Length                      
00000b3a:       d8 32 45 da c9 3e |  .2E..>| RLE2: Uncompressed data           
00000b40: 01 c3 01 da             |....    |                                   
00000b44:             01          |    .   | RLE2: Type                        
00000b45:                1e       |     .  | RLE2: Length                      
00000b46:                   00 00 |      ..| RLE2: Compressed data             
00000b48: 00                      |.       | RLE2: Type                        
00000b49:    ba                   | .      | RLE2: Length                      
00000b4a:       00 21 0b d7 5e 23 |  .!..^#| RLE2: Uncompressed data           
00000b50: 56 eb e9 0c 0d c8 1a 77 |V......w|                                   
</pre>

</details>


<details>
<summary>dskscan -- інформація про сектори у образі.</summary> 

Фрагмент виводу:
<pre>
dskscan DISK_EXP2.IMD
Comment: [1991-07-29T16:40:37] CP/M 2.2 Basic Disk for Osborne 1
Cylinder  0 Head 0:
    Data rate: 250
    Encoding: fm
    Cyl 00    Head 0    Sec   1 size  256
    Cyl 00    Head 0    Sec   2 size  256
    Cyl 00    Head 0    Sec   3 size  256
    Cyl 00    Head 0    Sec   4 size  256
    Cyl 00    Head 0    Sec   5 size  256
    Cyl 00    Head 0    Sec   6 size  256
    Cyl 00    Head 0    Sec   7 size  256
    Cyl 00    Head 0    Sec   8 size  256
    Cyl 00    Head 0    Sec   9 size  256
    Cyl 00    Head 0    Sec  10 size  256
Cylinder  0 Head 1:
    Found nothing
Cylinder  1 Head 0:
    Data rate: 250
    Encoding: fm
    Cyl 01    Head 0    Sec   1 size  256
    Cyl 01    Head 0    Sec   2 size  256
    Cyl 01    Head 0    Sec   3 size  256
    Cyl 01    Head 0    Sec   4 size  256
    Cyl 01    Head 0    Sec   5 size  256
    Cyl 01    Head 0    Sec   6 size  256
    Cyl 01    Head 0    Sec   7 size  256
    Cyl 01    Head 0    Sec   8 size  256
    Cyl 01    Head 0    Sec   9 size  256
    Cyl 01    Head 0    Sec  10 size  256
Cylinder  1 Head 1:
    Found nothing
Cylinder  2 Head 0:
    Data rate: 250
    Encoding: fm
    Cyl 02    Head 0    Sec   1 size  256
    Cyl 02    Head 0    Sec   2 size  256

</pre>

</details>

<details>

<summary>dsktrans -- перетворення між типами образів, певне -- найважливіша утиліта пакету для мене.</summary> 

<pre>
$ # У всіх прикладах нижче тип вхідного образу визначається 
  # автоматично, але, за потреби, можна й вказати, за допомогою -itype <!-- omit in toc -->
$ dsktrans -otype imd DISK_EXP2.TD0 DISK_EXP3.IMD
Input driver: TeleDisk file driver
Output driver:IMD file driver

$ dsktrans -otype tele DISK_EXP2.IMD DISK_EXP3.TD0
Input driver: IMD file driver
Output driver:TeleDisk file driver

$ # ``Плоский образ'' -- лише послідовність байт
$ dsktrans -otype raw DISK_EXP2.IMD DISK_EXP3.IMG
Input driver: IMD file driver
Output driver:Raw file driver (alternate sides)
</pre>

</details>

> На жаль, створити IMD-образ, сумісний з Osborne Executive, як далі описано на прикладі Imagedisk, мені поки не вдалося -- так: ``dsktrans -otype imd -format osbexec1`` не спрацювало. 
<!-- TODO: Раптом колись вдасться -- занотувати -->

<details>

<summary>Підтримувані контейнери та формати (із врахуванням вмісту libdskrc)</summary> 

Всі утиліти libdsk вміють виводити цю інформацію.

<pre>
$ dskid -types
Disk image types supported:

   ntwdm      : NT WDM floppy driver
   floppy     : Win32 floppy driver
   gotek      : Gotek 1440k disc image collection
   gotek72    : Gotek 720k disc image collection
   remote     : Remote LibDsk instance
   rcpmfs     : Reverse CP/MFS driver
   dsk        : CPCEMU .DSK driver
   edsk       : Extended .DSK driver
   apridisk   : APRIDISK file driver
   copyqm     : CopyQM file driver
   tele       : TeleDisk file driver
   ldbs       : LibDsk block store
   ldbst      : LDBS (text form)
   sap        : SAP file driver
   qrst       : Quick Release Sector Transfer
   imd        : IMD file driver
   ydsk       : YAZE YDSK driver
   raw        : Raw file driver (alternate sides)
   rawoo      : Raw file driver (out and out)
   rawob      : Raw file driver (out and back)
   myz80      : MYZ80 hard drive driver
   simh       : SIMH disc image driver
   nanowasp   : NanoWasp image file driver
   logical    : Raw file logical sector order
   jv3        : JV3 file driver
   dc42       : Disk Copy 4.2
   cfi        : CFI file driver

$ dskid -formats
Disk formats supported:

   pcw180     : PCW / IBM 180k
   cpcsys     : CPC System
   cpcdata    : CPC Data
   pcw720     : PCW / IBM 720k
   pcw1440    : PcW16 / IBM 1440k
   ibm160     : IBM 160k (CP/M-86 / DOSPLUS)
   ibm320     : IBM 320k (CP/M-86 / DOSPLUS)
   ibm360     : IBM 360k (CP/M-86 / DOSPLUS)
   ibm720     : IBM 720k (144FEAT)
   ibm1200    : IBM 1.2M (144FEAT)
   ibm1440    : IBM 1.4M (144FEAT)
   acorn160   : Acorn 160k
   acorn320   : Acorn 320k
   acorn640   : Acorn 640k
   acorn800   : Acorn 800k
   acorn1600  : Acorn 1600k
   pcw800     : PCW 800k
   pcw200     : PCW 200k
   bbc100     : BBC 100k
   bbc200     : BBC 200k
   mbee400    : Microbee 400k
   mgt800     : MGT 800k
   trdos640   : TR-DOS 640k
   ampro200   : Ampro 40 track single-sided
   ampro400d  : Ampro 40 track double-sided
   ampro400s  : Ampro 80 track single-sided
   ampro800   : Ampro 80 track double-sided
   pcw1200    : PcW16 / IBM 1200k
   mac400     : Macintosh GCR 400k
   mac800     : Macintosh GCR 800k
   myz80      : MYZ80 8Mb
   pcpm320    : IBM 320k (CP/M-86 / DOSPLUS)
   osbexec3   : OSBA Osborne Executive Dig. Arts - DSDD 48 tpi 5.25" - 1024 x 5
   osbexec2   : OSB9 Osborne Executive w/Z3 - DSDD 96 tpi 5.25" - 1024 x 5
   osbexec1   : OSB3 Osborne Executive - SSDD 48 tpi 5.25" - 1024 x 5
   osb1nv2    : OSBB Osborne Nuevo 2.1 - DSDD 96 tpi 5.25" - 1024 x 5
   osbVix     : OSB8 Osborne Vixen - DSDD 48 tpi 5.25" - 1024 x 5
   osb1nv     : OSB7 Osborne Nuevo - DSDD 48 tpi 5.25" - 1024 x 5
   osb1osm    : OSB6 Osborne 1 + Osmosis - DSDD 96 tpi 5.25" - 512 x 10
   osb1g2h    : OSB5 Osborne G2 System - DSDD 96 tpi 5.25" - 1024 x 5
   osb1g2     : OSB4 Osborne G2 System - DSDD 48 tpi 5.25" - 1024 x 5
   osb1ssdd   : OSB2 Osborne 1 - SSDD 48 tpi 5.25" - 1024 x 5
   osb1sssd   : OSB1 Osborne 1 - SSSD 48 tpi 5.25" - 256 x 10
</pre>

</details>

Інші: 
- dsklabel -- встановити чи побачити мітку диску. На жаль, в мене для CP/M образів не працює. Не вникав.
- dskutil -- інтерактивний редактор секторів.
- Багато інших інструментів та можливостей, аж по COM-сервер.

# Додаток -- манівцями

Лінь спробувати скомпілювати libdsk, а потім cpmtools із нею, завела мене трохи в манівці -- опишу тут свій досвід, може ще знадобиться. Але так працювати з образами видається неоптимальним.

Див. також цікаву статтю від людини, що вирішувала схожу задачу: [Osborne Restoration part 16: transferring CPM software](https://www.richardloxley.com/2018/04/27/osborne-restoration-part-16-transferring-cpm-software/)

## ImageDisk

Цей пакет є лише для DOS, але, оскільки [джерельні тексти доступні](http://dunfield.classiccmp.org/img/index.htm), перша думка була -- перекомпілювати, хоча б частково -- деякі утиліти, які не потребують доступу до фізичних дисків, під Win64. Ідея провалилася -- для написання автор створив свій [компілятор С-подібної мови](https://dunfield.themindfactory.com/) -- об'єм роботи нераціонально великий. 

Користуючись DOSBox-X[^7], цими утилітами можна вирішувати наступні задачі:

### TD02IMD

Утиліта для перетворення образів td0 в imd.

![](/retrocomputing/cpm/pics/td02imd_scr_1.png)

### IMDU: ImageDisk Utility

Перетворити з IMD в ''плоский'' образ 
```
IMDU /B DISK.IMD DISK.IMG 
```

''reInterLeave'':
```
IMDU TMP.IMD DISK1.IMD IL=3
```

Вивести подробиці про всі сектори та доріжки:
<details>
<summary> IMDU /B DISK.IMD DISK.IMG </summary>
<pre>
IMageDisk Utility 1.18 / Mar 07 2012
IMD 1.18: 21/12/2019 20:08:22

Osborne Executive

System disk 1 of 3

 0/0 300 kbps DD  5x1024
      1   2   3   4   5  
      D   D   D   D   D  
 1/0  D   D   D   D   D  
 2/0  D   D   D   D   D  
 3/0  D   DE5 D   D   D  
 4/0  D   D   D   D   D  
 5/0  D   D   D   D   D  
 6/0  D   D   D   D   D  
 7/0  D   D   D   D   D  
 8/0  D   D   D   D   D  
 9/0  D   D   D   D   D  
10/0  D   D   D   D   D  
11/0  D   D   D   D   D  
12/0  D   D   D   D   D  
13/0  D   D   D   D   D  
14/0  D   D   D   D   D  
15/0  D   D   D   D   D  
16/0  D   D   D   D   D  
17/0  D   D   D   D   D  
18/0  D   D   D   D   D  
19/0  D   D   D   D   D  
20/0  D   D   D   D   D  
21/0  D   D00 D00 D00 D00
22/0  D00 D   D   D   D  
23/0  D   D   D   D   D  
24/0  D   D   D   D   D  
25/0  D   D   D   D   D  
26/0  D   D   D   D   D  
27/0  DE5 DE5 DE5 DE5 DE5
28/0  DE5 DE5 DE5 DE5 DE5
29/0  DE5 DE5 DE5 DE5 DE5
30/0  DE5 DE5 DE5 DE5 DE5
31/0  DE5 DE5 DE5 DE5 DE5
32/0  DE5 DE5 DE5 DE5 DE5
33/0  DE5 DE5 DE5 DE5 DE5
34/0  DE5 DE5 DE5 DE5 DE5
35/0  DE5 DE5 DE5 DE5 DE5
36/0  DE5 DE5 DE5 DE5 DE5
37/0  DE5 DE5 DE5 DE5 DE5
38/0  DE5 DE5 DE5 DE5 DE5
39/0  DE5 DE5 D   DE5 DE5
40 tracks(40/0), 200 sectors (70 Compressed)
</pre>
</details>

Можна також додати (опція AC=file), замінити (RC=file) або видобути (EC=file) коментар з файлу образу.

### BIN2IMD: Binary to ImageDisk .IMD utility

Найбільш складна у використанні утиліта. Дозволяє створити образ диску із заданою геометрією. 

Характеристики основного формату дисків Osborne Executive:
- Розмір сектора: 1Кб = 1024 байти.
- Сторона -- одна (одна магнітна голівка).
- Треків: 40.
- Секторів на треку: 5,
- 250 kbps MFM.

Тоді мінімальним способом отримати правильний образ із сирого (побайтового):
```
bin2img DISK01.IMG DISK01T.IMD /1 N=40 SS=1024 DM=5 SM=1-5 /V2
```

![](/retrocomputing/cpm/pics/bin2imd_scr_1.png)

Використані опції:
- /1 -- односторонній образ,
- N=40 -- кількість треків (циліндрів),
- SS=1024 -- розмір секторів, 
- DM=5 -- Data Mode, 250 kbps MFM, 
- SM=1-5 -- карта секторів, пронумерувати сектори 1, 2, 3, 4, 5,
- /V2 -- рівень багатослівності виводу (verbose).

Для зміни interleave -- скористайтеся [IMDU](#imdu-imagedisk-utility).

За іншими цінними утилітами -- див. документацію.

## Перший успішний workflow 

Першим успішним способом змінювати образи сумісним із Osborne Executive був наступний:
1. Образ IMD конвертуємо в IMG за допомогою ``IMDU /B``, під DOS.
2. За допомогою cpmtools[^8]: cpmls, cpmrm, cpmcp, змінюється IMG-образ, під Windows.
3. Створюємо оновлений IMD образ, з допомогою ``bin2img``, під DOS.



# Виноски

[^1]: Писав трішки про початок цієї епохи: [MS/PC DOS 1.0](http://indrekis2.blogspot.com/2013/02/mspc-dos-10.html), [MS/PC DOS 1.XX в емуляторах](http://indrekis2.blogspot.com/2013/02/mspc-dos-1xx.html), [MS/PC DOS 1.XX "Ось ти який, північний олень!"](http://indrekis2.blogspot.com/2013/02/mspc-dos-1xx_25.html), [DOS FCB](http://indrekis2.blogspot.com/2013/02/dos-fcb.html).

[^2]: Заради лаконічності, і щоб не втонути у настільки широкій темі, свідомо дуже спрощую. В сучасному світі за подробицями можна заглянути [сюди](https://github.com/keirf/greaseweazle), [сюди](https://kryoflux.com/) або [сюди](https://hxc2001.com/), чи, хоча б, на [Wiki](https://en.wikipedia.org/wiki/Floppy_disk) та глави ''Sector-level organization'' і ''Floppy disk representation'' інструкції [MAME](https://docs.mamedev.org/_files/MAME.pdf). На щастя, з [Осборном]({% post_url /retrocomputing/cpm/2023-07-13-osbornes %}) так низько (по рівнях абстракції) опускатися не довелося

[^3]: Цитуючи [вікі](https://en.wikipedia.org/wiki/Floppy_disk): ''*A blank 40‑track disk formatted and written on an 80‑track drive can be taken to its native drive without problems, and a disk formatted on a 40‑track drive can be used on an 80‑track drive. Disks written on a 40‑track drive and then updated on an 80 track drive become unreadable on any 40‑track drives due to track width incompatibility.*''

[^4]: Крім того, утиліта TD02IMD з цього пакету дозволяє перетворювати образи з формату teledisk, тому такий вибір ні в чому не обмежив.

[^5]: Вибачте за якість фото, але мій вибір був між такими і взагалі відсутніми.

[^6]: [Floptool](https://docs.mamedev.org/tools/floptool.html) не вміє писати в інші формати, як і сам MAME, [HxCFloppyImageConverter](https://github.com/latchdevel/HxCFloppyImageConverter), [2](https://hxc2001.com/) не допоміг, [disk-analyse](https://github.com/keirf/Disk-Utilities) теж. Спроба за допомогою floptool записати в [M20](http://www.z80ne.com/m20/index.php?argument=sections/transfer/imagereadwrite/imagereadwrite.inc) -- в надії, що з них можна буде добути далі, провалилися, оскільки floptool паде з Segmentation fault.

[^7]: Думаю, підійде будь-який варіант DOSBox чи іншого емулятора DOS-систем, DOSBox-X лише найзручніший для мене.

[^8]: З якихось причин, наперед скопільовані пакети cpmtools під Windosw в Інеті мені траплялися виключно без libdsk -- а значить, без можливості роботи з .IMD безпосередньо.