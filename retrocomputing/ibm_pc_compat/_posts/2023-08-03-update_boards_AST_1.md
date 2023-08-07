---
layout: post
title:  "AST Rampage"
date:   2023-08-03 1:23:45 +0300
tags: [retrocomputing, IBM PC та сумісні, DOS]
# categories: [retrocomputing, IBM-PC-compat]
comments: true
excerpt_separator: <!--more-->
---

- [Огляд]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#огляд)
  - [На Amstrad 1640]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#на-amstrad-1640)
  - [EMS, Windows тощо]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#ems-windows-тощо)
    - [Спроба використати додаткові утиліти]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#спроба-використати-додаткові-утиліти)
  - [На Compaq Portable III]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#на-compaq-portable-iii)
- [Додаток -- AST SixPackPlus]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#додаток--ast-sixpackplus)
- [Виноски]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#виноски)


# Огляд

Один із моїх улюблених костилів епохи IBM PC -- [EMS](https://en.wikipedia.org/wiki/Expanded_memory). Причому, її емуляція на 286+ -- не так цікаво, хочеться для справжнього 8086/88-комп'ютера -- IBM-XT-подібного. Плат із такою пам'яттю було помітно більше, ніж ["Дивних плат оновлення"]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}), тому придбати їх на ebay реально, хоча й вимагало певної терплячості, поки з'являється лоти із прийнятними цінами. 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/ASR_Rampage.jpg)|
|:--------------------:|
| Моя AST Rampage. На фото -- 256 Кб RAM. На жаль, роз'єм для Daughter board не розпаяний. |


<style>body {text-align: justify}</style>

<!--more-->

Нарешті, рік тому з'явилася в мене [AST Rampage (8 bit, ISA)](https://minuszerodegrees.net/manuals/AST/Cards/AST%20Rampage%20AT.txt). Одна із базових плат величезного сімейства [AST Rampage](https://www.minuszerodegrees.net/manuals.htm#AST). Додає XT-машині 256Kb-2048Kb EMS пам'яті. Кількість визначається тим, скільки мікросхем пам'яті встановлено[^2]. Підтримує [LIM EMS 4.0](https://en.wikipedia.org/wiki/Expanded_memory) -- корисний для багатозадачності, вміє заповнювати стандартну пам'ять до 640Кб (з фіксованим кроком, залежним від моделі). Для контексту -- див. [огляд таких плат від InfoWorld, 1988](https://books.google.com.ua/books?id=CjoEAAAAMBAJ&lpg=PP1&hl=uk&pg=PT71#v=onepage&q&f=false), [AST Research](https://en.wikipedia.org/wiki/AST_Research) в той час, в значній мірі, "грала першу скрипку".

[^2]: Моя прийшла з 256Кб, вдома валялися сумісні мікросхеми пам'яті, із моєї першої материнської плати 286-го (яку я може колись ще й поремонтую...), то то стало 512Кб. Потім може ще додаткові куплю. 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/AST_rampage_jumpers.jpg){: width="75%" .align-center}|
|:--------------------:|
| Конфігурується джамперами[^3].  |


<details>
<summary> Опис із документації на мою версію плати. </summary>

<pre>
SWITCH SETTINGS

STARTING ADDRESS
          SW1-1   SW1-2   SW1-3   SW1-4
          -----   -----   -----   -----
      0K   OFF     OFF     OFF     OFF
     64K    ON     OFF     OFF     OFF
    128K   OFF      ON     OFF     OFF
    192K    ON      ON     OFF     OFF
    256K   OFF     OFF      ON     OFF
    320K    ON     OFF      ON     OFF
    384K   OFF      ON      ON     OFF
    448K    ON      ON      ON     OFF
    512K   OFF     OFF     OFF      ON
    576K    ON     OFF     OFF      ON
    640K   OFF      ON     OFF      ON

TYPE OF RAM INSTALLED
    BANK 0  BANK 1    SW1-5  SW1-6  SW1-7
    ------  ------    -----  -----  -----
      64K     64K   |   ON     ON    OFF
      64K    256K   |   ON    OFF    OFF
     256K    256K   |  OFF    OFF    OFF
    NOTE: The Rampage requires 256K DRAM memory chips in all of it'sbanks
          with the exception of banks zero and one.  As shown above, 64K
          DRAMS may be used in these two banks if the switches are set
          correctly.
    SW1-8 - PARITY CHECK (recommended ON at all times)

BASE I/O ADDRESS
           208  218  258  268  2A8  2B8  2E8
           ---  ---  ---  ---  ---  ---  ---
    SW2-1   ON  OFF  OFF   ON   ON  OFF   ON
    SW2-2   ON   ON   ON  OFF  OFF  OFF  OFF
    SW2-3   ON   ON  OFF  OFF   ON   ON  OFF
    SW2-4   ON   ON   ON   ON  OFF  OFF  OFF

RAMPAGE MEMORY AVAILABLE AS COVENTIONAL (Base 640K)
             0K    64K  128K  192K  256K  320K  384K  448K  512K  576K
            ----  ----  ----  ----  ----  ----  ----  ----  ----  ----
    SW2-5   OFF    ON   OFF    ON   OFF    ON   OFF    ON   OFF    ON
    SW2-6   OFF    ON    ON   OFF   OFF    ON    ON   OFF   OFF    ON
    SW2-7   OFF    ON    ON    ON    ON   OFF   OFF   OFF   OFF    ON
    SW2-8   OFF    ON    ON    ON    ON    ON    ON    ON    ON   OFF
</pre>
</details>

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/AST_Rampage_2.jpg){: width="75%" .align-center}|
|:--------------------:|
|Ревізія і серійний номер на коробці.| 

[^3]: Існували також плати AST, конфігуровані програмно, наприклад, [Rampage/2](https://stason.org/TULARC/pc/memory-cards/AST-RESEARCH-INC-Memory-RAMPAGE-2.html).

<style>body {text-align: justify}</style>

<!--more-->

## На Amstrad 1640

Власне, машина, для якої ця плата призначена -- 640Кб, 8-бітна ISA, 8086 процесор. 

## EMS, Windows тощо

Встановилася без проблем.  Щоб отримати доступ до EMS, потрібен драйвер -- REMM.SYS. (Refilled пам'ять в межах 640Кб доступна без додаткових дій). 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Amstrad_Rampage_1.jpg)|
|:--------------------:|
|Rampage + Amstrad 1640. Працює. Ремарка: фото, на жаль, традиційної сумнівної якості, але монітор не брудний -- правіше-нижче центру це подряпини, трохи є їх і в інших місцях.| 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Amstrad_Rampage_2.jpg)|
|:--------------------:|
| Звіт CheckIt. Ремарка: основної пам'яті -- 639Кб через XT-IDE[^TB1]. Після години за тим пузатим і мерехтливим монітором, мій великий здався мені вгнутим. :-) | 

[^TB1]: [XTIDE Universal BIOS](https://www.xtideuniversalbios.org/) може резервувати 1Кб зверху для своїх змінних -- це потрібно для сумісності із Turbo Basic ("[Full operating mode](https://www.xtideuniversalbios.org/wiki/WikiStart)"), до якого я відчуваю певну слабкість, варту 1Кб. :-) 

Windows 3.0 ця пам'ять теж сподобалася -- почав працювати помітно швидше, хоча й не вирішально -- Windows на такій машині заледве юзабельний чи так, чи так, але спробувати його було цікаво. Ретельно не підраховував, але було відчуття, що зависає із EMS трішки частіше.

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Amstrad_Rampage_3.jpg)|
|:--------------------:|
| Windows з EMS. | 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Amstrad_Rampage_4.jpg)|
|:--------------------:|
| Word 1.0 ця пам'ять теж сподобалася. | 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Amstrad_Rampage_5.jpg)|
|:--------------------:|
| А ось Excel дивно себе поводить -- і кореляції із тим, що діється в системі не спостерігалося. . | 

### Спроба використати додаткові утиліти 

**Згідно документації**, драйвер REX.SYS дає можливість використовувати пам'ять як RAM-диск, як RAM-дискету (розділено віртуалізацію як HDD i як FDD), або як спулер, за допомогою відповідних драйверів -- SUPERDRV.COM, FASTDISK.SYS, SUPERSPL.COM відповідно. Утиліта INSTALL.COM дозволяє їх сконфігурувати -- щоб не потрібно було вручну підбирати опції. Інструкцію для своєї плати не знайшов, допомогла інструкція до новішої, ["AST Rampage 286 - Users Manual - December 1986"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST%20Rampage%20286%20-%20Users%20Manual%20-%20December%201986.pdf), хоча, аргументи командного рядка "субдрайверів" віртуальних дисків та спулера вона теж не описує.  

**На практиці**, змусити працювати мені не вдалося. Після завантаження FASTDISK.SYS чи SUPERDRV.COM, наступний запуск виконавчого файлу зависає. До спроби запуску виглядає, що все ОК -- ОС продовжує працювати. Випробував для MS DOS 6.22 (1994), MS DOS 3.31 (1987), PC DOS 3.2 (1986), PC DOS 3.1 (1985), двох версії пакетів AST SuperPak -- 5.02 (1985), 6.11 (1986) і різних об'ємів пам'яті. Те ж -- навіть якщо казати утилітам використовувати звичайну пам'ять, не EMS. При чому, INSTALL.COM (є лише в 6.11) бачив тільки 192 Кб із 512 Кб.

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Amstrad_Rampage_6.jpg)|
|:--------------------:|
| Зависає після натискання Y. | 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Amstrad_Rampage_7.jpg)|
|:--------------------:|
| Чомусь лише 192 Кб . | 

Додатково, REX.SYS називає себе "RAMpage Extended Memory Emulator", але коди дизасемблював, виявилося, що він лише емулює сервіси int 15h для роботи із extended memory: "[COPY EXTENDED MEMORY](http://www.ctyme.com/intr/rb-1527.htm)" та "[GET EXTENDED MEMORY SIZE](http://www.ctyme.com/intr/rb-1529.htm)", і, здається, тільки ці[^DA]. Тому утилітам, які вміють використовувати XMS (Extended Memory), така емуляція теж не подобалася. 

[^DA]:  Хоча, без ретельнішого пошуку, ймовірно -- із застосуванням дебагера, сказати точно важко.

> Містить стрічку: ``Len Galasso was here!AST AST AST AST <багато повторів>``.


> Інструкції та драйвери брав із [minuszerodegrees.net](https://www.minuszerodegrees.net/manuals.htm#AST), але, для повноти, викладаю і в себе: ["AST SuperPak software - Version 5.02.zip"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST%20SuperPak%20software%20-%20Version%205.02.zip), та ["AST SuperPak software - Version 5.02.zip"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST%20SuperPak%20software%20-%20Version%206.11.zip), ["AST Rampage.txt"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST%20Rampage.txt), ["AST Rampage AT.txt"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST%20Rampage%20AT.txt), ["AST RampagePlus 286.txt"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST%20RampagePlus%20286.txt), ["AST Rampage 286 - Users Manual - December 1986.pdf"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST%20Rampage%20286%20-%20Users%20Manual%20-%20December%201986.pdf), ["AST_RampagePlus_286.pdf"](/retrocomputing/ibm_pc_compat/files/upd_brd/AST_RampagePlus_286.pdf).

## На Compaq Portable III

Випробував її також на машині із 286 -- [Compaq Portable III Model 1](https://en.wikipedia.org/wiki/Compaq_Portable_III), із екстендером для ISA-плат. Працює чудово, але лише як EMS -- ділити між Extended/Expanded memory вміють лише новіші, типу [**AST Rampage 286 та 286Plus**](https://minuszerodegrees.net/misc/AST_RampagePlus_286.pdf) -- тепер шукаю собі й таку. 

Планую студентам презентацію провести з цього комп'ютера -- VGA карта, яку можна підключити до нашого проектора, через екстендер теж чудово працює, але з наявною пам'яттю (640Kb) Windows 3.x відмовляється працювати в захищеному режимі, і якщо це не заважає Word чи Excel, то PowerPoint хоче саме захищеного режиму. На жаль, як і інші пакети презентування для Windows. "Рідні" розширювачі RAM для цієї машини існують, але трапляються помітно рідше, ніж AST Rampage.

Фото поки не маю, можливо, додам в майбутньому. 

# Додаток -- AST SixPackPlus

Якщо занурившись в минуле глибше, вартує згадки [AST SixPackPlus](http://minuszerodegrees.net/manuals/AST_SixPakPlus_LONG_Users_Manual.pdf), 1983. Траплялося на просторах Інету, що лише за 1983 її було продано на 13 мільйонів доларів. Вона призначена для IBM PC (5150) та IBM XT (5160), і надавала такі можливості:

- Доповнити RAM до 640Кб. Максимально може містити 384 Кб -- визначається кількістю мікросхем. Додавати їх можна було по 64 Кб за раз -- це мало сенс у той час: через велику ціну пам'яті, був сенс докупляти її потроху. 
- Містила годинник реального часу та календар із батарейкою -- не маючи свого годинника, ті системи змушували користувача кожен раз вводити дату та час. 
- RS-232 порт (COM-port), LPT-порт (паралельний), опціональний [game-порт для підключення джойстика](https://en.wikipedia.org/wiki/Game_port).
- Все це -- з використанням лише одного слоту ISA. А їх на тих машинках, майже без вбудованих пристроїв на материнській платі, завжди бракує.  

> Власне, через відсутність годинника у тих, доволі популярних, комп'ютер, DOS, за відсутності autoexec.bat, завжди питається дату та час.  Інші рішення проблеми часу описані тут: "[IBM 5150/5155/5160  -  Examples of Types of RTC Solutions](http://www.minuszerodegrees.net/5150_5160/rtc_type_examples/5150_5160_rtc_type_examples.htm)" -- обов'язково подивіться фото. Найдивніші плати були: [ROM interposer](http://www.minuszerodegrees.net/5160/ds1216e/ds1216e.htm), платка із RTC, що вставлялася на місце BIOS; [CPU interposer](https://www.minuszerodegrees.net/manuals/Microsync/Microsync%20dClock%20-%20User's%20Manual%20-%20Revision%20G.pdf) -- плата на місці CPU, із слотом для нього; Floppy drive interposer -- між дисководом та контролером; окрім очевидного -- ISA плати, були плати, які одягалися на ISA слот, контактуючи "лапками" до контактів вставленої плати, але не займаючи слота. Та й сама AST SixPackPlus була популярною через те, що займаючи один слот -- яких завжди бракувало, виконувала багато функцій, заміняючи хай не шість, але зо три плати.



|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/ast-sixpackplus-full.jpg )|
|:--------------------:|
|AST SixPackPlus, взято [тут](http://21stdigitalhome.blogspot.com/2019/01/the-ibm-pc-5150-part-6-choc-full-ast.html). Існує багато варіантів, візуально різних.|

Якщо таки вдасться завести працездатну IBM PC 5150 -- маю шанс, почну і таку шукати. 

# Посилання

- [Microsoft KB Archive/66420](https://www.betaarchive.com/wiki/index.php/Microsoft_KB_Archive/66420#Expanded_Memory) -- велика стаття про види пам'яті та як її використовували 16-бітні Windows.
- [Windows 3.1 and Memory Management](http://www.tech-insider.org/windows/research/acrobat/920221/2MEMWW.pdf) -- глава 5 з "[Windows Resource Kit](https://www.tech-insider.org/windows/research/acrobat/920221/0PART.pdf)" для Windows 3.1.
- Відео: [Windows 3.0, Real-Mode, Small-Frame EMS and Origami](https://www.youtube.com/watch?v=XqFSWnMpVic), в описі є трохи технічних подробиць.
- [Про HT12 чіпсет](https://www.vogons.org/viewtopic.php?f=46&t=33076&start=20) для 286, зокрема HT12EMS.SYS дозволяв відображати пам'ять на UMB.
- [Expanded memory managers and DOS](https://www.vogons.org/viewtopic.php?p=1114766):
  - "*Anyway, Windows 3.0 does support LIM4 or EEMS, it's spiritual predecessor. Windows 3.0 supports 64KB and 256KB page frames for EMS.*" 
  - "*Using DOS 6.x and running MemMaker can help to make Windows 3.0 see proper EMS, also. For testing purposes, I mean.*"
  - "*Windows 2.x has trouble with CuteMouse being loaded. MS Mouse v6.24 does not cause trouble, however. I guess the same goes for MS Mouse drivers in general.*"
  - "*Windows 2.x is DOS 4 aware, which causes trouble with later DOSes that use the older DOS 3 kernal structure (that's DOS 5, 6 etc). Hence, SETVER or DOSVER must be used to fake a DOS 3.x version (I use 3.30).*"
  - "*An obsolete version of REMM.SYS ships with Windows 2.03 (I tried it originally, before I found an updated REMM.SYS online).*"

# Виноски

