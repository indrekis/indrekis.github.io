---
layout: post
title:  "Дивні плати оновлення із 1980-х"
date:   2023-07-31 13:23:45 +0300
tags: [retrocomputing, IBM PC та сумісні, DOS]
# categories: [retrocomputing, IBM-PC-compat]
comments: true
excerpt_separator: <!--more-->
---

- ["DOS: Beyond 640K"]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#dos-beyond-640k)
  - [Про книгу]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#про-книгу)
- [Плати оновлення]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#плати-оновлення)
  - [Intel Inboard 386]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#intel-inboard-386)
  - [Intel SnapIn]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#intel-snapin)
  - [Інші оновлення до 386]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#інші-оновлення-до-386)
  - [Orchid Tiny Turbo 286]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#orchid-tiny-turbo-286)
    - [Orchid PCTurbo 286e]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#orchid-pcturbo-286e)
  - [Make-It 486]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#make-it-486)
  - [All Charge Card]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#all-charge-card)
  - [Фінал епохи]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#фінал-епохи)
  - [Додаток -- пам'ять замість HDD]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#додаток----память-замість-hdd)
- [Додаткові посилання]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#додаткові-посилання)
- [Виноски]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#виноски)

# "DOS: Beyond 640K"

Останні роки, замість художньої літератури, часто читаю старі комп'ютерні книжки. Про деякі вже писав, про інші ще планую. Поміж них трапилася ["DOS: Beyond 640K" by James S. Forney, 2nd ed., 1992 року](https://archive.org/details/DOS_Beyond_640K_2nd_edition). Читати її було жахливо. Автор знову і знову повторював одне і те ж, трішки різними словами, а коли дістало так, що  написав комусь поскаржитися, на наступній сторінці автор буквально сказав: "Повторю ще раз" і скопіював попередній абзац. Навіщо ж я її читав? Це хороший спосіб зануритися в атмосферу тодішніх "персоналок" -- часто дуже відмінну від сучасного світу. 

Поміж іншого, там були описані плати оновлення (upgrade) для тогочасних комп'ютерів. І карти ті були настільки, для наших часів, незвичні, що я вирішив про них написати. 

<style>body {text-align: justify}</style>

<!--more-->

## Про книгу

Поміж іншого, описує еволюцію стандарту EMS, зокрема -- з точки зору багатозадачності, компактно ця інформація мені більше ніде не траплялася.


| ![](/retrocomputing/ibm_pc_compat/pics/upd_brd/page61.png) |
|:--------------:|
| Ілюстрація 5-1 з книги, про те як функціонує [EMS](https://en.wikipedia.org/wiki/Expanded_memory). Разом із ілюстрацією 5-2, видно, що EMS -- це такий собі примітивний сторінковий MMU. |

Заради справедливості, може якби я загальну теорію того всього -- будову IBM PC сумісних комп'ютерів, особливості x86, інтерфейси для подолання бар'єру [640Кб](https://en.wikipedia.org/wiki/Conventional_memory), типу [XMS](https://en.wikipedia.org/wiki/Extended_memory#XMS), [EMS](https://en.wikipedia.org/wiki/Expanded_memory), [UMB](https://en.wikipedia.org/wiki/Upper_memory_area), [HMA](https://en.wikipedia.org/wiki/High_memory_area), відповідних драйверів -- [HIMEM.SYS](https://en.wikipedia.org/wiki/HIMEM.SYS), [EMM386](https://en.wikipedia.org/wiki/EMM386) чи [QEMM](https://en.wikipedia.org/wiki/QEMM)[^TR] тощо, не знав, може б вона простіше читалася. Книжка орієнтована на звичайних тогочасних користувачів, і ймовірно, з задачею їх навчати, справлялася добре. Вона розповідає, що це за поняття, чому виникли, як функціонують. Який софт потрібен, щоб із тим всім legacy-driven залізом працювати, як його конфігурувати тощо. Часто порівнюються ціни різних рішень.

Підозрюю, якби вам, користувачу сучасних ОС, раптом (не дай Ктулху), довелося вчитися налаштуванню пам'яті для DOS -- після ядерної війни абощо, книга може бути корисною. Наприклад, якщо про EMM386 і аналоги, написано багато де, то LIM 4.0 EMS емулятори для систем з 286 та більше, ніж 640Кб RAM, менш відомі -- книга підказує [QRAM](https://winworldpc.com/product/qram/2x) від  Quarterdeck та [MOVE'EM](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/78198) від Qualitas (не вдалося поки копію знайти). 
> Але [EMM286](https://www.pcorner.com/list/UTILITY/EMM286.ZIP/INFO/), яким іноді користуюся, довелося шукати самостійно.

Ще, із цікавих пакетів, описано:

- [Headroom](https://winworldpc.com/product/helix-headroom/2x) для відображення TSR із EMS по запиту;
- [VM/386](https://en.wikipedia.org/wiki/VM/386) -- своєрідний гіпервізор для DOS, який дозволяв кожній копії дати свої CONFIG.SYS і AUTOEXEC.BAT та, навіть, одночасно запускати різні версії DOS ([доступний тут](https://archive.org/details/vm386)); 
- [MDOS](https://en.wikipedia.org/wiki/Multiuser_DOS)/[DR Concurrent DOS](https://en.wikipedia.org/wiki/Multiuser_DOS#Concurrent_DOS) -- спроба Digital Research відігратися за програш із DOS, створивши багатокористувацький, багатозадачний варіант; 
- [PC-MOS](https://en.wikipedia.org/wiki/PC-MOS/386#PC-MOS) --  ще один варіант багатокористувацького, багато задачного розширення DOS (зараз [Open Source](https://github.com/roelandjansen/pcmos386v501)). 
- [DesqView](https://en.wikipedia.org/wiki/DESQview) вважаємо звичним, про нього багато де написано;
-  але, заради повноти, про наступні пакети не згадано, хай і менш-більш зрозуміло, чому:
    - [MP/M](https://en.wikipedia.org/wiki/MP/M) -- багатокористувацький варіант [CP/M](https://en.wikipedia.org/wiki/CP/M), предок Concurrent CP/M, 
    - [DoubleDOS](https://en.wikipedia.org/wiki/DoubleDOS) -- двозадачну ОС, підказану [колегою](https://www.facebook.com/groups/computinghistoryua) із [української спільноти ретрокомп'ютерщиків](https://forum.it-museum.com), 
    - ініціативу типу [Multiuser DOS Federation](https://en.wikipedia.org/wiki/Multiuser_DOS_Federation). 

Сліди багатьох із цих утиліток в Інтернеті зараз важко знайти -- а шкода!

[^TR]: В цьому пості я не пояснюю значення тих термінів із темних віків, хіба даю посилання на вікі. Або читайте саму книгу. :-) Опис дозволив би зануритися трохи в настрій тієї епохи, але це речі менш-більш загальновідомі, а щоб бути інформативним для не знайомих із контекстом, текст був би довгим.

Символічно, що із [третього видання](https://www.amazon.com/DOS-Beyond-640K-Jim-Forney/dp/0070216096) цієї книжки згадки про плати було викинуто. 

# Плати оновлення

Що у них так вражає? Зараз, зазвичай, засоби оновлення комп'ютера обмежені -- відеокарту замінити, пам'яті додати чи диск більший встановити та й все. Разом із процесором доводиться оновлювати зразу і материнську плату та пам'ять. (Звичайно, винятки бувають). А тоді випускалися плати, які оновлювали процесор на два-три покоління -- від 16-бітного 8088 без MMU до 32-бітного 386+ із сторінковою пам'яттю і апаратною віртуалізацією згаданого 88-го. В книзі таким апдейтам присвячено главу 16, але вирішив ними не обмежуватися, почитавши тогочасні журнали. 

## Intel Inboard 386

Найбільш вбивча, як на мене -- [Intel Inboard 386](https://en.wikipedia.org/wiki/Intel_Inboard_386) (1987). Варіант  Intel Inboard 386/PC дозволяв оригінальну IBM PC із 8088 процесором перетворити на машину із 386. На додачу, вона ще й містила слот для [387 співпроцесора](https://en.wikipedia.org/wiki/X87#80387). Могла мати свою пам'ять, 1 Мб, та обладнуватися дочірніми платами із 1Mb і більше RAM. Як дуже наголошується в тогочасній літературі, займала лише один ISA слот. 

Оригінальний процесор потрібно було дістати і замість нього під'єднати шлейф від плати ([інструкція тут](http://minuszerodegrees.net/manuals/Intel/Intel%20-%20Installing%20the%20Inboard%20386_PC%20Personal%20Computer%20Enhancement%20%281987%29.pdf)), якщо був 8087 -- його теж позбутися. Оскільки шина вводу-виводу, DMA тощо, залишалися 8-бітними, швидкодія була обмеженою -- це не перетворювало XT-класу машину на повноцінну AT-класу з 386. Також,блок живлення вартувало оновити.  Але, вона, по перше, дозволяло запускати програми, що потребували 386, по друге, швидкодія таки зростала -- за даними із книги, в 4-7 раз. Із швидкодією сильно допомагала пам'ять на платі. 

Існувала також Intel Inboard 386/AT, для оновлення 286-го, але через ціну, згідно слів автора, вона не окупалася -- така плата була дорожчою за Intel Inboard 386/PC.

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/InBoard386.jpg)|
|:--------------------:|
|Intel Inboard 386/PC та CPU шлейф. Взято [тут](https://twitter.com/foone/status/1171983889419497473).|


|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/InBoard386_at.jpg)|
|:--------------------:|
|Intel Inboard 386/AT та CPU шлейф. Взято [тут](https://www.reddit.com/r/retrobattlestations/comments/hqpfxd/intel_inboard_386_collection_also_looking_to_buy/).

| ![](/retrocomputing/ibm_pc_compat/pics/upd_brd/CPU_Section.jpg){: width="50%" .align-center} |
|:--------------------:|
|Оскільки із 387 були затримки, Intel також продавала адаптери для 287. Взято [тут](https://www.cpushack.com/2019/08/14/how-to-386-your-at-intel-inboard-386-at/).|

 
|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/PCMagTestResults.png)|
|:--------------------:|
|Продуктивність InBoard 386/AT згідно PC Mag. Вона навіть трохи обганяє 386 системи -- ймовірно, через кеш і вищу частоту процесора на InBoard. Взято [тут](https://www.cpushack.com/2019/08/14/how-to-386-your-at-intel-inboard-386-at/).|




|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/inboard386_daughter.png){: width="75%" .align-center}|
|:--------------------:|
|Дочірня плата (daughter board) із 1Мб. Взято [тут](https://www.worthpoint.com/worthopedia/pcib1210-intel-inboard-386-pc-2mb-1885730646). Що цікаво, розроблено сучасний варіант такої плати, для любителів: "[Inboard 386/PC 2mb expansion CLONE](https://forum.vcfed.org/index.php?threads/inboard-386-pc-2mb-expansion-clone.78562/)" (фото [тут](https://imgur.com/a/zxVAEMH#MKiRJxn)).|

## Intel SnapIn

[Intel SnapIn](https://www.ardent-tool.com/CPU/Intel_SnapIn.html) реалізовує інший підхід -- в слот 286-системи вставляться плата із 386SX, із власним тактовим генератором, 16Кб SRAM кешу і підтримкою 287. Відео про цю плату розширення: ["Intel "SnapIn 386" CPU Upgrade on a PS/2 Model 30 286!"](https://www.youtube.com/watch?v=69FGJVv8nDc), стаття про те, як [виправляти її проблеми з Windows 3.0](https://jeffpar.github.io/kbarchive/kb/081/Q81284/).


|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/SnapIn80386SX-20-1.jpg){: width="50%" .align-center}|
|:--------------------:|
|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/SnapIn80386SX-20-2.jpg){: width="50%" .align-center}|
|Intel SnapIn 80386SX-20, фото взято [тут](https://twitter.com/foone/status/821578036671692800?lang=zh-Hant)|.

## Інші оновлення до 386

Книга згадує також аналогічне розширення ALL 386SX від  ALL Computers, Inc., але знайти про нього не вдалося нічого -- у зв'язку із занадто тривіальною назвою.

Згідно журналів типу PC Mag, Byte тощо, такий апгрейтів існувало багато. Наприклад, в книзі не згадується аналог Inboard -- [Quad386XT](https://books.google.com.ua/books?id=FC-L2faxEVkC&pg=PA268&lpg=PA268&dq=Quad386XT&source=bl&ots=sJsFSem-Ff&sig=ACfU3U2CjXWHjXOe1h_01Tj55cHmPMVvgA&hl=en&sa=X&ved=2ahUKEwjU2o7_2Oj5AhV9VfEDHcPSAAsQ6AF6BAgWEAM#v=onepage&q=Quad386XT&f=false), навіть візуально схожа, див. за посиланням, (PC Mag 12 Apr 1988, стор. 268).

## Orchid Tiny Turbo 286

Згадаю дві плати, які доповнюють описані в книзі.

Orchid Tiny Turbo 286 (1989): вставляється в ISA слот, 8088 (єдиний, із яким працює) потрібно дістати, вставити в "дочірню" плату на шлейфі, а дочірню плату -- в слот процесора. Завдяки цьому можна було переключатися між використанням процесора на платі та "рідного".  На відміну від перемикачів конфігурації, (типу, на якій частоті працює співпроцесор, яку пам'ять кешувати тощо), цей перемикач виведено назовні -- зміна режиму не потребує розбирати комп'ютер, але, природно, перезавантажує систему. Інший конкурент, [AST Hot Shot](https://www.vintagecomputer.net/browse_thread.cfm?id=689), вмів переключатися програмно, але подробиці довідатися не вдалося.

Підтримує 287. Обладнана власним кешем. Не підтримує кнопку Turbo. Інструкції є [тут](http://minuszerodegrees.net/manuals/Orchid/Orchid%20-%20TinyTurbo%20286%20-%20User%27s%20Manual.pdf). 


|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/orchid-tiny-turbo-286-rev_1.jpg)|
|:--------------------:|
|Orchid Tiny Turbo 286 із шлейфом. Взято [тут](http://dosdays.co.uk/topics/cpu_upgrade_options.php).|

### Orchid PCTurbo 286e

Ідейно схожа на  Tiny Turbo 286, але може виконувати код на обох процесорах -- 286 і 8088 одночасно. Можна вказувати код, який буде виконуватися або на хостовому процесорі і на платі, або тільки на ній, конфігурування включає створення відповідних autoexec-файлів. В турбо-режимі копіює все необхідне в пам'ять акселератора і код виконує там, 8088 на платі служить лише для вводу-виводу. Не потребує маніпуляцій із "рідним" процесором. Обладнана своєю пам'яттю, 1Мб, допускає розширення до 2Мб. 

Якщо вам цього мало, можна підключити до 4-х таких карт одночасно, отримавши 5-процесорну (чи 4-процесорну -- подробиць поки не знаю) машину із звичайної IBM XT. Не любить EGA адаптери, але в цілому -- доволі сумісна. Не підтримує [DOS 5.0](https://jeffpar.github.io/kbarchive/kb/077/Q77094/), як із новішими -- не знаю. Інструкції знайти, на жаль, не вдалося, але трішки інформації є тут: [1](https://worldradiohistory.com/hd2/IDX-Consumer/Archive-Radio-Electronics-IDX/IDX/80s/1987/Radio-Electronics-1987-11-OCR-Page-0094.pdf),  [2](https://worldradiohistory.com/hd2/IDX-Consumer/Archive-Radio-Electronics-IDX/IDX/80s/1987/Radio-Electronics-1987-11-OCR-Page-0095.pdf), фото коробки: [1](https://twitter.com/foone/status/1202052034393853952?lang=en), [2](https://mobile.twitter.com/foone/status/951575785621504001), <!-- список джерел та драйвер, --> огляд подібних акселераторів від PC Mag ([1986-09-16, стор. 128-165, огляд детальний, але половина сторінок -- реклама](https://retrocmp.de/hardware/pc-elevator-286/PC-Mag-1986-09-16.pdf), окремо про цю плату -- стор. 158). 

Прискорення від цієї плати -- в 2..9 раз, згідно PC Mag.

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/orchid_pcturbo_286e_with_mem_expansion_and_ems_extension.png)|
|:--------------------:|
|Orchid PCTurbo 286e з додатковою платою розширення пам'яті (XMS або EMS). Взято [тут](http://dosdays.co.uk/topics/cpu_upgrade_options.php).|

Orchid також випускав схожу плату із 186 процесором! Трішки подробиць тут: ["Ever seen a 80186 accelerator board? I have now. (and so can you)"](https://forum.vcfed.org/index.php?threads/ever-seen-a-80186-accelerator-board-i-have-now-and-so-can-you.34569/). 

Список продуктів цієї компанії із описом, для деяких, як їх конфігурувати: ["Orchid Technology"](https://wiki.preterhuman.net/Orchid_Technology).

## Make-It 486

[Make-It 486 від Improve Technologies (IT)](https://www.cpushack.com/2014/08/30/improve-technologies-make-it-486-286-upgrade), відносно нова (як для цього списку), 1991, на базі [Cyrix Cx486SLC](https://en.wikipedia.org/wiki/Cyrix_Cx486SLC), дозволяла оновити 286 CPU до 486.


|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/ImproveTechnologies_MakeIt-386_Cx87SLC-33_Cx486SLC-e-33MP.jpg)|
|:--------------------:|
| Improve Technologies Make-It 486. Взято [тут](http://dosdays.co.uk/topics/cpu_upgrade_options.php).|
 

## All Charge Card

Повертаючись до книги, вартують згадки ще два пристрої.

All Charge Card від тієї ж ALL Computers. Це плата -- [MMU](https://en.wikipedia.org/wiki/Memory_management_unit), яка, згідно книги та рекламних матеріалів тих часів, реалізовує функціонал MMU, наближений до 386. На жаль, подробиці не вдалося вияснити. Вона вставляється в слот процесора, а процесор -- в неї. Опис див. ["Computer craft", July 1991, стор. 18 і далі](https://ia800805.us.archive.org/29/items/computer-craft-july-1991/Computer-Craft-1991-07.pdf). Там також описано кілька плат оновлення з 286 в 386. Трішки технічних подробиць є в першому [виданні книги](https://archive.org/details/DOS_Beyond_640K_2nd_edition/page/n232/mode/1up?view=theater). Див. також відео використання: ["RETRO The Weirdest CPU Upgrade: ALL Chargecard"](https://www.youtube.com/watch?v=xgMIbo6QoM4). 


|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/all_charge_card_eiriks_2.jpg)|
|:--------------------:|
| All Charge Card. Взято [тут](http://dosdays.co.uk/topics/cpu_upgrade_options.php).|

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/all_charge_card_eiriks_3.jpg)|
|:--------------------:|
|Коробка від All Charge Card. Взято [тут](https://twitter.com/Foone/status/1129252419437641728/photo/1).|

## Фінал епохи

Здається, в 90-х від цього підходу залишилися хіба Overdrive процесори, типу [Pentium OverDrive](https://en.wikipedia.org/wiki/Pentium_OverDrive) -- Pentium, що міг працювати на платі 486-х, [i487SX](https://en.wikipedia.org/wiki/X87#80487), який фактично був i486DX, що відключав головний  i486SX -- але перевіряв його присутність, та [RapidCAD](https://en.wikipedia.org/wiki/RapidCAD) -- 486DX разом із заглушкою для співпроцесора, призначений працювати на платах із 386 та 387. А далі ідея відмерла, принаймні на якийсь час -- враховуючи, що часто вони повертаються на новому витку розвитку.

## Додаток -- пам'ять замість HDD

На завершення, в книзі описано дивний пристрій, єдину іншу згадку про який вдалося знайти в [PC Mag 29 від Mar 1988, стор. 51](https://books.google.com.ua/books?id=20tQCmhyNEMC&lpg=PA1&hl=uk&pg=PA51#v=onepage&q&f=false), та ще нотатку, в іншому журналі, що компанія готує варіант для MAC. Мова про **DartCard** від Newer Technology -- плату розширення пам'яті, яка монтується на місці HDD і за допомогою модулів пам'яті 4-, 16- або 16Мб може містити до 704 Мб в одному відсіку для HDD повного розміру. Їх ще й можна підключати в ланцюжок, до 2.8 Гб -- в 1988 це буди фантастичні числа. Під'єднувалася вона інтерфейсом ATA, існували варіанти SCSI (для Маків) та ESDI для PS/2. За допомогою батареї могла бути non-volatile. Додатково, дозволяла системі бачити її як 15 Мб XMS або 8 Mb LIM EMS пам'яті. Ціна, щоправда: з 4Mb -- \$2595, додаткові 4Мб модулі \$1395, 16Мб -- \$9995. 2.8 Гб трохи дорогі були б. Враховуючи, що [NVDIMM](https://en.wikipedia.org/wiki/NVDIMM) є нащадками BBU (battery backed up) DIMM, можна вважати -- і тут технологія зробила повне коло спіралі.

# Додаткові посилання

- Стаття: "[CPU Upgrade Options](http://dosdays.co.uk/topics/cpu_upgrade_options.php)" містить список, чим можна було оновити 88/86/286/386/486, включаючи як "тривіальну" заміну 8088/8086 на [NEC V20](https://en.wikipedia.org/wiki/NEC_V20), так і хитрі плати типу описаних вище. 
- Розділ [Manuals на minuszerodegrees](http://minuszerodegrees.net/manuals.htm) містить багато інструкцій до таких плат.
- Стаття PC Mag "Accelerator boards: power for a price", Charles Petzold ([1986-09-16, стор. 128-165](https://retrocmp.de/hardware/pc-elevator-286/PC-Mag-1986-09-16.pdf)).
- [The Secret History of Microsoft Hardware](https://www.pcmag.com/news/the-secret-history-of-microsoft-hardware), зокрема -- описує Mach 10 Accelerator board для XT, із 8086 9.54MHz -- на фоні плат вище, не вражає, та Mach 20 із 8-MHz 80286 CPU і слотом для 80287, кажуть навіть OS/2 можна було з її допомогою на XT запускати. Огляд [Mach 10](https://books.google.com.ua/books?id=oDwEAAAAMBAJ&pg=PA63&dq=%22microsoft%22+%22windows%22&hl=uk&sa=X&ved=2ahUKEwiT8PSIkKH1AhWE66QKHZg1Cx0Q6AF6BAgDEAI#v=onepage&q=%22microsoft%22%20%22windows%22&f=false) від InfoWorld, 6 Oct. 1986, стор. 63-64.
- "[Triton Turbo XT](http://www.mainbyte.com/ti99/hardware/triton_turbo/turbo.html)" -- дивний клон XT, який називався апдейтом для [TI-99](https://en.wikipedia.org/wiki/TI-99/4A), а фактично [використовував його як клавіатуру](https://dfarq.homeip.net/triton-turbo-xt/).

# Виноски

