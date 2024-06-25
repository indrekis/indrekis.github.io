---
layout: post
title:  "До релізу джерельних текстів DOS 4.0x та матеріалів European DOS 4"
date:   2024-06-25 1:23:45 +0300
tags: [retrocomputing, IBM PC та сумісні, DOS]
# categories: [retrocomputing, IBM-PC-compat]
comments: true
published: true
excerpt_separator: <!--more-->
---



- [Огляд]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#огляд)
  - [Вміст]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#вміст)
  - [Огляд DOS 4.00]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#огляд-dos-400)
  - [Огляд Multitasking DOS 4]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#огляд-multitasking-dos-4)
- [Компіляція DOS 4.0x]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#компіляція-dos-40x)
  - [Оригінальний код]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#оригінальний-код)
  - [Виправивши деякі баги]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#виправивши-деякі-баги)
  - [SYS.COM]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#syscom)
- [Компіляція Multitasking (European) MS DOS 4.0]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#компіляція-multitasking-european-ms-dos-40)
  - [Цікавинки]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#цікавинки)
  - [cpmio.asm]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#cpmioasm)
  - [Інтеграл]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#інтеграл)
- [Підсумок]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#підсумок)
- [Посилання]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#посилання)
- [Виноски]({% post_url /retrocomputing/ibm_pc_compat/2024-04-28-DOS40x-source-release %}#виноски)


Два місяці тому[^A26] Microsoft відкрила (майже) повні джерельні тексти [MS DOS 4.00](https://github.com/microsoft/MS-DOS/tree/main/v4.0) та частину славнозвісно загадкового [Multitasking чи European MS DOS 4.0](https://github.com/microsoft/MS-DOS/tree/main/v4.0-ozzie)[^MTD4], приблизно еквівалентну OEM Adaptation Kit (OAK)[^OAK1]. Звичайно, я зразу захотів спробувати скомпілювати та подивитися, що там із [SYS.COM](https://indrekis.github.io/tags/#sys-com). Але, хоча, з підказками спільноти, це вдалося швидко, через звичну безліч справ з студентами і взагалі в УКУ, описати свої знахідки тоді не вдавалося і пишу, вкотре, безнадійно відставши від першовідкривачів. 

Отож, експериментуємо.

[^A26]: 26 квітня 2024 року.

[^OAK1]: Джерельні тексти та об'єктні файли, які Microsoft надавала OEM-виробникам для адаптації під їх комп'ютери. Див, наприклад: [Microsoft MS-DOS OEM Adaption Kit 3.x](https://winworldpc.com/product/microsoft-ms-dos-oem-adaption-kit/3x), [MS-DOS 2.0 OEM Adaptation Kit (February 8, 1983)] (https://archive.org/details/msdos-2.0-oak). Зазвичай, IO.SYS, FORMAT.COM чи SYS.COM постачалися як джерельні тексти, а MSDOS.SYS, як умовно-платформонезалежний, постачався як набір об'єктних файлів. 

[^MTD4]: Базується на DOS 2.0, працює в реальному режимі, не має відношення до "справжнього" MS DOS 4.0. Див. також [Wiki: MS-DOS 4.0 (multitasking)](https://en.wikipedia.org/wiki/MS-DOS_4.0_(multitasking)) та ''[The History of Multitasking MS-DOS](https://starfrost.net/blog/001-mdos4-part-1/)''.

<style>body {text-align: justify}</style>

<!--more-->

# Огляд

Після релізу текстів [DOS 1.xx та DOS 2.00](https://www.os2museum.com/wp/pc-dos-1-1-from-scratch/) Microsoft сказала, що інші версії не будуть доступними, поміж іншого і через юридичні труднощі -- не всім кодом володіє Microsoft і все таке. Ймовірно, якусь роль відіграла і потреба ретельно проглядати коментарі -- зараз мережеву істерику здатні викликати речі, абсолютно безпечні з точки зору 1980-х. Однак, раптом, 26 квітня 2024, в репозиторії з'явилося два дуже цікавих доповнення. 

> Що кумедно, новий коміт  проіснував недовго і зразу був замінений на інший. Але дехто встиг [завантажити перший](https://news.ycombinator.com/item?id=40163766). Причиною виявився образливий коментар про [Tim Patterson](https://en.wikipedia.org/wiki/Tim_Paterson)[^TPD], ім'я якого в новій версії було замінено на абревіатуру TP:
> ```MASM
> ;
> ; Brain-damaged TP ignored ^F in case his BIOS did not flush the
> ; input queue.
> ;
> ```

[^TPD]: Автор перших версій ОС, яка потім стала MS/PC-DOS.

Автор коміту, виглядає, [Mark Zbikowski](https://en.wikipedia.org/wiki/Mark_Zbikowski) -- ми його знаємо за ініціалами, MZ, на початку кожного .exe-файлу[^MZR]. Реліз коду потребував великої праці, зокрема і з залученням IBM. Трохи подробиць є тут: ''[Open Sourcing DOS 4](https://www.hanselman.com/blog/open-sourcing-dos-4)'', [інший лінк](https://cloudblogs.microsoft.com/opensource/2024/04/25/open-sourcing-ms-dos-4-0/), зокрема там перераховуються люди, чиєю заслугою він є.

[^MZR]: Прочитавши в юності цей факт в якомусь підручнику, довго чудувався, як так взагалі може бути -- в СРСР таке собі уявити важко було, звідки це знає автор -- і що живуть же десь люди з доступом до такої інформації... 

## Вміст 

Новий реліз старих сорців включає:

- Майже повну версію MS DOS 4.0x, разом з інструментами, необхідними для компіляції та відповідними BAT-файлами.
  - Присутні інструменти: MASM 5.10, Microsoft C 5.10, NMake 1.00.05, Microsoft Overlay linker 3.65, LIB 3.10, всі 1988 року та деякі інші. 
  - Відсутні тексти DOS Shell (а шкода!). Також, немає коду GWBASIC.EXE та HIMEM.SYS, які є на оригінальних образах дисків дистрибутиву, але їх можна знайти (може -- не тих версій, в інших джерелах) -- див. [посилання](#посилання). 
- Фрагменти Multitasking чи European MS DOS 4.0:
  - Документацію.
  - Образи дисків цього умовного ''OAK''.
  - В них -- набір утиліт, типу CHKDSK.EXE, DETACH.EXE, KILL.EXE, FDISK.COM, FORMAT.COM, SYS.COM  тощо та SM.EXE -- Session Manager, який дозволяє керувати багатозадачністю.
  - Файли для завантаження: IBMBIO.COM, IBMDOS.COM, COMMAND.COM -- дивні назви, враховуючи відсутність в IBM зацікавленості цією версією.
  - Короткі користувацькі інструкції: SM.DOC, COMMANDS.DOC.
  - Джерельні тексти та об'єктні файли: IBMDSK.ASM, IBMMTCON.ASM, IBMBIO.OBJ, IBMDSK.OBJ  тощо. 
  - Також, BIOSOBJ.MAK, який містить згадку про SYSINI.OBJ/.ASM та SYSIMES.OBJ/.ASM, ASM-варіантів яких, на жаль, немає -- про що сказано й в документації. 
  - Найновіша дата в коментарях: 22 травня 1984, при завантаженні ОС каже -- 25 травня 1984. Тобто, це дуже стара версія -- згідно [вікі](https://en.wikipedia.org/wiki/MS-DOS_4.0_(multitasking)), релізи були між 15 травня 1986 та 10 травня 1988.

## Огляд DOS 4.00

Версія DOS 4.00 була випущена 19 липня 1988 та базувалася на DOS 3.30. Однак, згідно [OS/2 Museum](https://www.os2museum.com/wp/how-not-to-release-historic-source-code/) наданий код ближчий до [4.01](https://en.wikipedia.org/wiki/MS-DOS#MS-DOS_4.0_/_MS-DOS_4.x), найпізніша дата в коментах -- серпень 1988.

> Взагалі, поки моя спроба розібратися у версіях PC DOS 4.00, 4.01, MS DOS 4.00, 4.01 мене швидше остаточно заплутала. У різних джерелах їх історія описана по різному, частина оновлень були ''мовчазними'', дати релізів та файлів на наявних образах теж дивні.

Ця версія подвійно знакова. Її розробляла IBM, в основному, своїми силами, внесла багато змін у внутрішні структури даних, ймовірно, тестування було неналежним, тому -- купа багів і дуже погана репутація. Потім ще Мікрософт виправляла потроху -- не забувши похвалити себе. З іншого боку, було введено багато нових технологій: подолання межі 32Мб на розмір дисків -- 32-бітові номери секторів[^D331], підтримка EMS (на жаль, ''заточена'' під плати IBM, але все ж!), DOS Shell, MEM.EXE, заготовка для SETVER (поки захардкоджена в кінці MSDOS.SYS/IBMDOS.COM), окремий інсталятор[^SD1]. Така собі Windows Vista попередніх часів. Чи то вже краще казати ''Така собі Windows 11''? Детальніше про неї див. статтю [''DOS 4.0'' від  OS/2 Museum](https://www.os2museum.com/wp/dos/dos-4-0/).

[^D331]: DOS 3.31 від Compaq теж це вміла, див., наприклад ''[MS-DOS 3.31: The DOS you couldn’t have](https://dfarq.homeip.net/ms-dos-3-31-the-dos-you-couldnt-have/)'' -- але її не було у вільному продажі, крім того, виглядає, це був backport з 4.0x.

[^SD1]: SELECT, найбільш зламаний викладанням на github... Див. далі. 

## Огляд Multitasking DOS 4

Multitasking чи European MS DOS 4.0  -- дуже загадкової версії, яка тільки відносно недавно (2013) стала доступною любителям[^MDC], і яка забезпечувала витісняючу багатозадачність поверх 8086.

- Базувалася на [MS DOS 3.10](https://www.pcjs.org/software/pcx86/sys/dos/microsoft/4.0M/) (іноді трапляється інформація, що 2.xx).
- New Executable формат з'явився саме в  ній, як і імпорт та експорт.
- Підтримувала pipe, shared memory і семафори.
- Витісняюча багатозадачність поверх 8086 -- без всіляких MMU[^PMMU]. Звичайно, обмежена і для спеціалізованих програм.
- Багато апробованих ідей було потім використано в OS/2 та Windows,


[^MDC]: До того саме її існування іноді ставилося під сумнів. 

[^PMMU]: MMU -- Memory Management Unit, точніше, факт його використання, в родині x86 прийнято називати protected mode. 

На жаль, здається, 4.1 поки так ніде й не випливла...

# Компіляція DOS 4.0x

## Оригінальний код

На відміну від попередніх релізів, всі інструменти були на місці, але, все ж, з коробки -- не працювало. 

> Хоча нижче я намагаюся писати максимально детально, але якісь подробиці могли забутися, не зважаючи на нотатки -- експериментував ще на в кінці квітня-початку травня.

Компіляція, в ідеальному випадку, відбувається послідовним виконанням трьох команд (налаштовуємо середовище; компілюємо; копіюємо результат у директорію, де лежить makefile):

```bat
D:\v4.0> setenv.bat
................................
D:\v4.0> nmake
................................
D:\v4.0> cpy.bat
```

Нульова проблема: setenv.bat потребує незначного редагування -- файли заголовків та бібліотек знаходяться в ``src\tools\BLD``, а не в ``src\tools``. Також, оскільки мені було зручніше тримати в корені диску всі версії, то використав ``BAKROOT=d:\v4.0`` -- вважаємо, що репозиторій лежатиме в корені диску D:.

Перша проблема з компіляцією -- коли код заливали на GitHub, використовуючи налаштування за замовчуванням, були зламані символи кінця рядка. І, якщо, скажімо, MASM справлявся і з Unix-варіантом, \n, то ``nosrvbld.exe`` та ``exe2bin`` мають проблеми. Також, DOS 3.31 та 4.01 (і, думаю, старіші), потребують \n\r і в .BAT-файлах. 

> Щоб заборонити Git і надалі ламати символи кінця рядків, додав такий .gitattributes, користуючись [цією підказкою](https://stackoverflow.com/questions/21822650/disable-git-eol-conversions)[^BBTF]:

```text
# Do not change ENDL handling:
#* text=false

# Do not change ENDL 
* -text
```

[^BBTF]: Але, здається, endl в bat-файлах воно все ще ламає...

Друга -- якесь перекодування однобайтових символів у трьохбайтові UTF-8. Це призвело, окрім пошкодження різних частин текстів та коментарів, до таких помилок:

![](/retrocomputing/ibm_pc_compat/pics/DOS400/dos40src-error1.png) 

Третя проблема, яка проявляла себе дещо містично. Дати файлів втрачені -- це історична проблема. Але воно ж буде проблемою для nmake. Якщо дата в DOS-системі старіша -- то кожен раз буде повна перекомпіляція, яка (див. далі) часто не працює. Також, хоча ретельно не досліджував[^SPER] -- може це взагалі була випадковість, за такої пізньої (2024!), з точки зору утиліт 1988-го року дати, іноді вони загадково аварійно закінчуються.

[^SPER]: Поміж таких, спорадичних помилок, які не дуже відтворювалися: під DOS 4.01 -- не вистачало пам'яті на backup.c, навіть якщо без резидентів і драйверів; DOS 3.31 -- іноді не знаходило інклуди (BIOS/msdisk.asm) з-під nmake але не коли вручну викликати. Також, бувало, сам DOSBox помирав на exe2bin.

Користуючись [підказками з Інтернету (Re: [Freedos-devel] MS-DOS 4.0 source released as open source under MIT license)](https://sourceforge.net/p/freedos/mailman/message/58765259/), виправляв наступним чином. 

Для виправлення проблем з символами кінця рядка, пройшовся по файлах наступною командою Unix Shell:
```bash
find ./ \( -type f -iname '*.SKL' -o -iname '*.ASM' -o -iname '*.C' -o -iname '*.INC'  -o -iname '*.INF' -o -iname '*.BAT' -o -iname 'ZERO.DAT' -o -iname 'LOCSCR' -o -iname 'USA-MS.MSG' -o -iname '*.TXT'  -o -iname 'SELECT.PRT' \) -exec unix2dos -f  {} \;
```

Також, в SRC/SELECT/panel.inf було пошкоджено 1304-й рядок (PANEL37). Просто урізав довжину -- не знаючи, як виправити.

Зламане кодування нашвидкоруч виправив так (ідея з того ж джерела, що й попередня):

```bash
find ./ \( -type f -iname '*.SKL' -o -iname '*.ASM' -o -iname '*.C' -o -iname '*.INC' -o -iname '*.INF' -o -iname '*.BAT' -o -iname 'ZERO.DAT' -o -iname 'LOCSCR' \) -exec sed -i -re 's/\xEF\xBF\xBD|\xC4\xBF|\xC4\xB4/#/g' {} \;
```

Виправити дати на старіші[^FXD], під сучасними ОС, тривіально, хоча під DOSBox ніби й не потрібно. Та й, тільки поредагуєш, доводиться знову. А ось з-під DOS це складніше. Допоміг DN -- Dos Navigator. Все ж, він дуже просунутий був на свій час. 

| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/dn_change_Dates.png) |
| :-------------: |
| Змінюємо оптом дату файлів. Спочатку пошуком виводимо файли на панель (як в Total Commander), потім використовуємо пункт меню ``File/File Attributes...``. Щоправда, для директорій воно змінювати дату не вміє, але це й не потрібно. |

[^FXD]: Не маю повної впевненості, що це конче необхідно, але з виправленими датами ніби проблем компіляції було менше. 

Компілював з використанням наступних систем, всі -- з 640 Кб основної пам'яті: 

- **DOSBox-X:**
  - Якщо збільшити швидкодію емулятора -- загадковим чином паде компіляція. Тому обмежився стандартною, ``3000 cycles/ms``.
  - Триває 53 хв. 
    - Ремарка: щойно скомпілювалося на 40700 cycles/ms... Але раніше відтворювалося.
- **MS DOS 3.31** -- для старіших треба було підготувати ще диск, не більший за 32 Мб. Доступно 569 Кб основної пам'яті:
  - На компіляції select9.asm паде, через недостачу пам'яті. 
  - Потрібно виконати nmake в директорії SRC/SELECT -- воно, після падіння, якраз там буде.
  - Після цього -- вийти на крок вище і знову викликати nmake -- тепер завершиться успішно. 
    - Якщо зразу вийти на крок вище і використати nmake, воно знову впаде на тому ж місці. Ймовірно, nmake, працюючи з більшим makefile використовує якраз ту пам'ять, якій потім бракує masm.
  - На емульованому 486/33 МГц компіляція триває приблизно 19 хв.
- **MS DOS 4.01**, доступно 558 Кб RAM:
  - Історія та ж, що і з 3.31.
- **MS DOS 6.22** з HIMEM i EMM386, так що з 640 Кб основної пам'яті доступно 602 Кб.
  - Компілюється до кінця без зупинок. 
- Також, можна скомпілювати під [OS/2 2.x](https://virtuallyfun.com/2024/06/10/building-ms-dos-4-00-under-os-2-2-x/), але сам я не став пробувати.

Під час компіляції час від часу пищить, десь під час перетворення кодових сторінок. 

У всіх випадках, отримані файли побайтово збігаються з вмістом файлів дистрибутиву, взятого [тут](https://winworldpc.com/product/ms-dos/4x), окрім як для SELECT.EXE, SELECT.DAT та SELECT.RPT. На жаль, SELECT -- інсталятор, таки залишається зламаним та потребує додаткової роботи.

Ситуація з випробуваними версіями DOS, однак, цікава -- актуальні на момент розробки версії не можуть просто так, за один захід, скомпілювати цю версію DOS... Цікаво, як викручувалися розробники? 

| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/error248_331.png) |
| :-------------: |
| Падіння компіляції на select9.asm, під час використання DOS 3.31. Відтворюється. |
| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/error248_622_f3.png) |
| А ось так паде, коли з датами наплутано, наприклад, коли системна дата -- 1980 рік. |

| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/error248_331_f2.png) |
| :-------------: |
| Так виглядає успішне завершення компіляції. |

Дискету, з якої можна завантажити, створив просто -- пам'ятаючи, що бутлоадер у цієї версії DOS вже відносно інтелектуальний:

1. Відформатував дискету. 1.44 Мб цілком підійшла.
2. Записав на неї IO.SYS.
3. Записав на неї MSDOS.SYS.
4. Додав все решта -- COMMAND.COM та інші утиліти за бажанням. 

IO.SYS та MSDOS.SYS важливо записувати на щойно відформатовану дискету саме в такому порядку. 

| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/dos400_custom.png) |
| :-------------: |
| Для наочності, трішки змінив надпис (в COMMAND.CLB -- зміна USA-MS.MSG не вплинула на результат, певне треба якось перегенеровувати) Так виглядає успішне завершення компіляції. |

Результати цих змін виклав у master [свого репозиторію](https://github.com/indrekis/MS-DOS).

## Виправивши деякі баги

Користуючись наявними джерельними текстами, можна зробити DOS 4.0x не таким глюкавим. 

> Ремарка: у цьому розділі лише чужі знахідки. 

Одне з основних джерел проблем з цією версією -- багато де вона просто відмовляється вантажитися. Проблема -- замалий стек. Виправляється його збільшенням, у файлі ``SRC/BIOS/MSLOAD.ASM``: 

```masm
; https://www.os2museum.com/wp/how-not-to-release-historic-source-code/comment-page-1/#comment-381416
Mystacks	dw	128 dup (0)	
```

Це виправлення, як і ще декілька цікавих -- багу в DEBUG.COM, в перевірці OEM ID дискет,  взято з [репозиторію Neozeed](https://github.com/neozeed/dos400).

У [цього автора](https://hg.pushbx.org/ecm/msdos4/file/tip) взяв виправлений SELECT. Після перекомпіляції відповідні файли теж починають повністю збігатися з дистрибутивом.

Результати цих змін виклав у гілці ``modern_fixups`` [свого репозиторію](https://github.com/indrekis/MS-DOS/tree/modern_fixups).

## SYS\.COM

Після великого досвіду з [різними версіями SYS\.COM](http://indrekis2.blogspot.com/2017/10/syscom-pc-dos-310.html), не міг не глянути, хай і побіжно, на цю утиліту. Для порівняння обрав SYS.COM з DOS 3.3 OAK, який блукає Інтернетом. Вона достатньо схожа на SYS.COM з 3.10, останню, яку я детально аналізував, але з оригінальним оформленням, на відміну від моїх коментарів у власних дизасемблах. 

Об'єм джерельних текстів дуже зріс -- 220 Кб проти 30 Кб в старішій версії. 

Основна причина -- ''зображення'', як на картинці та детальна документація перед кожною функцією:


| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/sys400_1.png) |
| :-------------: |
| ASCII-Art своєрідний. |

```masm
;******************* START OF SPECIFICATIONS ***********************************
;Routine name:	Find_DPB
;*******************************************************************************
;
;Description:	Find_DPB gets the pointer to the Target DPB and initializes all
;		local valiables required by Move_DIR_Entry and Free_Cluster.
;
;NOTE:		This routine contains code that is specific for DOS 3.3.  It
;		must be removed for subsequent releases.  In and before
;		DOS 3.3 the DPB was one byte smaller.  The field dpb_FAT_size
;		was changed from a byte to a word in DOS 4.00.
;
;
;Entry: 	Called by Verify_File_Location
;
;Called Procedures:
;
;		INT 21 - 32h
;
;Input: 	al = Drive number
;
;Output:	All local variables initalized
;		DS:BX = pointer to DPB
;
;Change History: Created	7/01/87 	FG
;
;******************* END OF SPECIFICATIONS *************************************
;******************+ START OF PSEUDOCODE +**************************************
;
;	START Find_DPB
;
;	get DPB pointer (INT 21 - 32h)
;	initalize first_dir_sector
;	initalize current_dir_sector
;	initalize current_cluster (0 for root)
;	calculate # of clusters required by IBMBIO
;	initalize [cluster_count]
;	calculate # of dir sectors
;	initalize [dir_sectors]
;	initalize [current_entry] to #3
;	allocate memory for FAT + 32 DIR frames
;	allocate memory for data sectors
;
;	ret
;
;	END Find_DPB
;
;******************-  END  OF PSEUDOCODE -**************************************


   PUBLIC Find_DPB

   Find_DPB PROC NEAR

   MOV	AH,GET_DPB			;Get the DPB			       ;AN004;
   INT	21H


```

Щодо логіки, код став краще структурованим -- замість, менш-більш, простирадла коду, тепер таке:

```masm
   mov	dx,OFFSET DOS_BUFFER		; set up DTA
   mov	ah,SET_DMA
   INT	21h

   call Init_Input_Output		; setup messages and parsing	       ;AN000;

;  $if	nc,and				; there is no error and 	       ;AN000;
   JC $$IF1

   call Validate_Target_Drive		; verify target drive is valid	       ;AN000;

;  $if	nc,and				; there is no error		       ;AN000;
   JC $$IF1

   call Get_System_Files		; get system files loaded	       ;AN000;

;  $if	nc,and				; there is no error		       ;AN000;
   JC $$IF1

   call Check_SYS_Conditions		; verify target drive is SYSable       ;AN000;

;  $if	nc,and				; there is no error		       ;AN000;
   JC $$IF1

   call Do_SYS				; perform SYS operation 	       ;AN000;

;  $if	nc,and				; no error and			       ;AN000;
   JC $$IF1

   call Do_End				; clean up loose ends		       ;AN000;


```

Фрагмент коду версії з 3.30, який починається з того ж встановлення DTA:

```masm
	MOV	DX,OFFSET DG:DirEnt
	MOV	AH,SET_DMA
	INT	21h
	jmp	sys

GOTBADDOS:
	push	cs
	pop	ds
ASSUME	DS:DG
	MOV	DX,OFFSET DG:BADVER	; message to dump
	mov	ah,std_con_string_output
	int	21h
	push	es
	xor	ax,ax
	push	ax

foo	proc	far
	ret			    ; Must use this method, version may be < 2.00
foo	endp

ERR0:
ASSUME	DS:DG,ES:DG
	MOV	DX,OFFSET DG:BADPARM_ptr    ; no drive letter
	JMP	xerror

ERR1:
ASSUME	DS:DG,ES:DG
	MOV	DX,OFFSET DG:BADDRV_ptr     ; drive letter invalid
	JMP	xerror

;
; We do not have a disk that has an available system in the root.  See if the
; media is removable and if so, ask the user for a replacement.  If the media
; is not removable, then die gracefully.
;
ERR2:
ASSUME	DS:DG,ES:DG
	MOV	AH,GET_DEFAULT_DRIVE	;Will find out the default drive
	INT	21H			;Default now in AL
	MOV	BL,AL
	INC	BL			; A = 1
	Call	IsRemovable
	jnc	DoPrompt
	MOV	DX,OFFSET DG:NoSys_PTR
	PUSH	DX
	CALL	PRINTF
	MOV	AX,4C01h
	INT	21h
```

Детальніше поки не буду заглиблюватися.

# Компіляція Multitasking (European) MS DOS 4.0

Вирішив, за нагоди, спробувати скомпілювати, використовуючи утиліти з ''звичайного'' DOS 4.00 і цю, багатозадачну версію DOS -- точніше, не цілу її, а IBMBIO.COM, єдиний файл, для якого є (частково) джерельні тексти. Знаходяться вони на другому диску, в директорії BIOS.n

Це потребувало двох виправлень:

До 1224 рядка файлу IBMDSK.ASM додати PTR:

```masm
	cmp	word PTR [SemDiskIO],0001h
```

З BIOSOBJ.MAK прибрати таргети sysini.obj та sysimes.obj, для яких є об'єктники, але немає джерельних текстів -- про що пише і READ_ME:

```makefile
ibmbio.obj:	ibmbio.asm defdbug.inc bugcode.inc
	masm ibmbio;

ibmmtcon.obj:	ibmmtcon.asm ansi.inc defdbug.inc
	masm ibmmtcon;

ibmdsk.obj:	ibmdsk.asm defdbug.inc
	masm ibmdsk;

#sysini.obj:	sysini.asm dossym.inc devsym.inc syscalls.inc
#	masm sysini;

#sysimes.obj:	sysimes.asm
#	masm sysimes;

ibmbio.exe:	ibmbio.obj ibmmtcon.obj ibmdsk.obj sysini.obj sysimes.obj
	link ibmbio ibmmtcon ibmdsk sysini sysimes,ibmbio,ibmbio/map;

# Use 70 as an base segment (hex)
ibmbio.com:	ibmbio.exe
	exe2bin ibmbio ibmbio.com
```

Компілюємо під DOSBox, вважаючи, що ``D:\v4.0\v4.0\src\setenv.bat`` вже запускали:
```BAT
D:\v4.0-ozzie\bin\DISK2\BIOS> nmake -f BIOSOBJ.MAK ibmbio.com
```

Також, компіляція потребує ручного втручання -- для exe2bin вказати базовий сегмен 70 (іншими словами, 0x70).

Отриманий ibmbio.com має доволі багато байт, відмінних від оригіналу, хоча розмір збігається, але вантажиться успішно, в тому числі, працює SM.EXE -- Session Manager, який і реалізовує багатозадачність.

| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/dos40_multitask_boot1.png) |
| :-------------: |
| Завантаження дуже ранньої версії багатозадачного DOS. А ось по ver представляється дуже скромно: ``MS-DOS Version  4.00``. Жорсткого диску (124 Мб) не бачить -- і добре...|

| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/dos40_multitask_sm1.png) |
| :-------------: |
| SM, під яким запущено дві копії command.com. Alt-F10 повертає до SM. Після виходу з SM іноді ігнорує першу команду чи щось таке, але ніби працює.|


## Цікавинки

Таблиця обману версій в ``MSINIT.ASM`` -- те, що потім було винесено в SETVER.EXE:

```MASM
;The following entries don't expect version 4.0
;The entry format: name_length, name, expected version, fake count
;fake_count: ff means the version will be reset when Abort or Exec is encountered
;	     n means the version will be reset after n DOS version calls are issued
;
Version_Fake_Table:			    ;AN007  starting address for special
	DB	12,"IBMCACHE.COM",3,40,255  ;AN007  ibmcache     1
	DB	12,"IBMCACHE.SYS",3,40,255  ;AN007  ibmcache     2
	DB	12,"DXMA0MOD.SYS",3,40,255  ;AN007  lan support  3
	DB	10,"WIN200.BIN"  ,3,40,4    ;AN008  windows      4
	DB	 9,"PSCPG.COM"   ,3,40,255  ;AN008  vittoria     5
	DB	11,"DCJSS02.EXE" ,3,40,255  ;AN008  netview      6
	DB	 8,"ISAM.EXE"    ,3,40,255  ;AN008  basic        7
	DB	 9,"ISAM2.EXE"   ,3,40,255  ;AN008  basic        8
	DB	12,"DFIA0MOD.SYS",3,40,255  ;AN008  lan support  9
	DB	20  dup(0)		    ;AN007
```

## cpmio.asm

Є два файли з промовистими назвами: CPMIO.ASM та CPMIO2.ASM, з коментарем: ``Standard device IO for MSDOS (first 12 function calls)`` та ``Old style CP/M 1-12 system calls to talk to reserved devices``. В старіших версіях файлів з такими іменами ніби немає.

## Інтеграл

| ![](/retrocomputing/ibm_pc_compat/pics/DOS400/dos4_int.png) |
| :-------------: |
| Під час компіляції трапився такий інтегральчик. |

# Підсумок

Дуже цікавий шматок комп'ютерної історії -- принаймні в сімействі IBM PC-сумісних комп'ютерів. Трапилося б воно мені років 15 тому -- міг застрягнути на місяці. 

А поки -- чекаємо на наступні відкриття і нові версії :-).

# Посилання

- ''[Compiling MS-DOS 4.0 from DOS 4.0, on a PS/2!](https://virtuallyfun.com/2024/04/28/compiling-ms-dos-4-0-from-dos-4-0-on-a-ps-2/)'' та [відповідний репозиторій](https://github.com/neozeed/dos400) автора, який ґрунтовно підійшов до виправлення древніх багів цієї версії DOS.
  - Ще [один автор](https://hg.pushbx.org/ecm/msdos4/file/tip), судячи з повідомлень комітів, взявся розвивати цю версію далі. Я взяв хіба його виправлення до SELECT.
- ''[MS-DOS 4.0x](https://winworldpc.com/product/ms-dos/4x)'' -- дистрибутив DOS 4.0x на Winworldpc.
- ''[Why not use MS-DOS 4.0?](https://dfarq.homeip.net/why-not-use-ms-dos-4-0/)'' -- ретроспектива, з приводу релізу джерельних текстів, проблем цієї версії та її плюсів. Як вище вже згадувалося, наявність текстів дозволила багато проблем зразу й виправити.
- В репозиторії: [hharte/MS-DOS](https://github.com/hharte/MS-DOS) -- спроба відвовити джерельні тексти 4.01, на основі бінарної різниці між версіями.
- ''[The History of Multitasking MS-DOS](https://starfrost.net/blog/001-mdos4-part-1/)''.
   - ''[Multitasking MS-DOS 4.0, Goupil OEM](http://www.os2museum.com/wp/multitasking-ms-dos-4-0-goupil-oem/)''
   - ''[Multitasking MS-DOS 4.0 Lives](https://www.os2museum.com/wp/multitasking-ms-dos-4-0-lives/)''
   - ''[Multitasking MS-DOS 4.00](https://www.pcjs.org/software/pcx86/sys/dos/microsoft/4.0M/)'' на PCjs, зразу з емуляцією.
   - ''[Fixing Broken LINK4](http://www.os2museum.com/wp/fixing-broken-link4/)'' -- з multitasking DOS 4.
   - ''[Before OS/2 Was OS/2](https://www.os2museum.com/wp/before-os2-was-os2/)'' -- про дуже ранню версію OS/2, цитуючи автора: ''*The available disks are from October 1986 and January 1987. They show a clear progression from multitasking DOS 4.0 to OS/2 1.0 as it was released in late 1987 (the oldest proto-OS/2 build predates the release by more than a year).*''
   - [MS-DOS 4.0 (multitasking) на вікі](https://en.wikipedia.org/wiki/MS-DOS_4.0_(multitasking)).
- ''[DOS 2.11 From Scratch](https://www.os2museum.com/wp/dos-2-11-from-scratch/)''.
  - ''[PC DOS 1.1 From Scratch](https://www.os2museum.com/wp/pc-dos-1-1-from-scratch/)'' -- попередній реліз потребував для рекомпіляції значно більше роботи.
- Есей на захист DOS 4.00: ''[DOS 4.0, bum rap, and mismatched expectations](http://www.os2museum.com/wp/dos-4-0-bum-rap-and-mismatched-expectations/)''.
- [Сайт Тіма Патерсона (Tim Patterson) на Вебархіві](https://web.archive.org/web/20190523034828/http://www.patersontech.com/dos/origins-of-dos.aspx) та його блог: ''[DosMan Drivel](http://dosmandrivel.blogspot.com/)''.
- Про тексти [GW BASIC від OS/2 Museum](https://www.os2museum.com/wp/gw-basic-source-notes/), від 1983.
- Тексти [himem.sys 2.06](https://github.com/neozeed/himem.sys-2.06), від 21 березня 1989.
- Тематичні тексти від Neozeed:
  - [MZ is back, baby! – Source to MS-DOS 4.0 / European DOS / Multi-tasking DOS drivers released!](https://virtuallyfun.com/2024/04/25/mz-is-back-baby-source-to-ms-dos-4-0-european-dos-multi-tasking-dos-released/)
  - [Compiling MS-DOS 4.0 from DOS 4.0, on a PS/2!](https://virtuallyfun.com/2024/04/28/compiling-ms-dos-4-0-from-dos-4-0-on-a-ps-2/)
  - [Building MS-DOS 4.00 under OS/2 2.x](https://virtuallyfun.com/2024/06/10/building-ms-dos-4-00-under-os-2-2-x/) -- цього пробувати я вже не став.
  - [Відповідний репозиторій](https://github.com/neozeed/dos400).

# Виноски
