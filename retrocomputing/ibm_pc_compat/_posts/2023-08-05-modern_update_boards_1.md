---
layout: post
title:  "Сучасні плати для IBM PC/XT"
date:   2023-08-05 1:23:45 +0300
tags: [retrocomputing, IBM PC та сумісні, DOS]
# categories: [retrocomputing, IBM-PC-compat]
comments: true
excerpt_separator: <!--more-->
---
- [RAM]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#ram)
- [Ввід-вивід]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#ввід-вивід)
  - [HDD]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#hdd)
  - [FDD]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#fdd)
  - [Мережа]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#мережа) 
  - [Інше]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#інше)
- [Сучасні клони ретро-комп'ютерів]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#сучасні-клони-ретро-компютерів)
  - [CP/M]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#cpm)
  - [XT]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#xt)
- [Посилання]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#посилання)
- [Виноски]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}#виноски)

Виявилося,нові плати оновлення та інше обладнання для ретрокомп'ютерів,  розробляють та продають досі. При тому, IBM PC, певне, навіть не найпопулярніша платформа.  

Коротко розповім про деякі плати, які або вже використовую, або хотів би мати -- не намагаючись досягнути повноти. Власне, це було б важко -- список лише на [Tindie](https://www.tindie.com/browse/vintagecomputing/) справляє враження.


<style>body {text-align: justify}</style>

<!--more-->

Цікаво, що більшість цих сучасних плат для давніх PC -- Open Source, завдяки чому існує багато їх варіантів, на будь-який смак. Для частини можна замовити саму плату, плату із "мішечком" запчастин або зібрану та готову до роботи.

# RAM

Існують "конкуренти" [AST Rampage]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}) різних варіантів.

[MicroRAM від Monotech](https://monotech.fwscart.com/MicroRAM_-_640K_+_UMB_RAM_-_8-bit_ISA/p6083514_19914752.aspx) (\$43): дозволяє заповнити пам'ять від нуля до 640Кб (із кроком 64Кб) а також відображати пам'ять на не використані ділянки адресного простору вище 640Кб -- так-звані [UMB](http://en.wikipedia.org/wiki/Upper_memory_area). Для роботи із ними буде потрібен драйвер -- [USE!UMBS](https://retrocmp.de/hardware/above-plus/use!umbs.txt).

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/microram_back.png)|
|:--------------------:|
| MicroRAM, вигляд ззаду -- з інструкцією. Оскільки пам'ять зараз дешева, замість хитрих відображень, реалізованих в  [AST SixPackPlus]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#додаток--ast-sixpackplus), перемикачі просто вмикають та вимикають відображення окремих блоків по 64Кб, а плата обладнана 1Мб. Фото [звідси](https://monotech.fwscart.com/MicroRAM_-_640K_+_UMB_RAM_-_8-bit_ISA/p6083514_19914752.aspx).|

 
[Lo-tech 1MB RAM Board](https://www.lo-tech.co.uk/wiki/Lo-tech_1MB_RAM_Board) -- аналогічна плата від Lo-tech, хіба що дещо дорожча, коштує \$[58](https://texelec.com/product/lo-tech-1mb-ram/) у готовому до використання[^SC]. На відміну від попередньої, підтримує роботу із системами, де є лише 16Кб -- мінімальна конфігурація оригінальної [IBM PC 5150](https://en.wikipedia.org/wiki/IBM_Personal_Computer).

[^SC]: Більшість таких плат можна дешевше купити як набір для самостійного паяння. 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Lo-tech-1MB-RAM-Board-assembled-r02.jpg)|
|:--------------------:|
| Фото [звідси](https://www.lo-tech.co.uk/wiki/File:Lo-tech-1MB-RAM-Board-assembled-r02.jpg). |


[Lo-tech 2MB EMS Board](https://www.lo-tech.co.uk/wiki/Lo-tech_2MB_EMS_Board) -- до 2Mb eXpanded Memory, сучасний аналог AST Rampage. Потребує драйвер,  [LTEMM.EXE](https://www.lo-tech.co.uk/wiki/LTEMM.EXE). З його допомогою підтримує LIM EMS 4. Адреса вікна в EMS (яке має бути в UMB) та порти вводу-виводу для керування відображенням конфігуруються джамперами. Не може бути використаною кілька раз, щоб отримати 4Мб і більше (на відміну від плат AST).


|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Lo-tech-2MB-EMS-Key-Front.png)|
|:--------------------:|
|  Lo-tech 2MB EMS Board. Фото взято [тут](https://texelec.com/product/lo-tech-ems-2-mb/). |

# Ввід-вивід

## HDD 

Великим класом пристроїв, дуже корисним для фанатів, є XT-to-CF адаптери, що дозволяють на XT використовувати [CompactFlash](https://en.wikipedia.org/wiki/CompactFlash) карти замість (особливо -- 8-бітних) IDE HDD[^ATA] -- стареньких, ледь живих, важкодоступних. Їх існує багато: 

[^ATA]: Це доволі просто, оскільки CompactFlash використовують інтерфейс [PATA](https://en.wikipedia.org/wiki/Parallel_ATA) -- той, що й згадані HDD.

- Крихітна ["Micro XTCF ISA 8 bits"](https://www.tindie.com/products/spark2k06/micro-xtcf-isa-8-bits-very-low-profile/).
- ["XT-CF-Mini Bootable 8-bit ISA CF Card Interface"](https://monotech.fwscart.com/XTCFMini_Bootable_8bit_ISA_CF_Card_Interface/p6083514_19478750.aspx), 
- Універсальна ["XT-IDE Deluxe"](https://monotech.fwscart.com/XT-IDE_Deluxe_-_Bootable_ISA_CF+IDE_Interface_Card/p6083514_19478732.aspx), 
- ["XT-CF-lite rev.2"](https://www.lo-tech.co.uk/wiki/XT-CF-lite_rev.2), 
- ["ISA XT CF Adapter rev. 3"](https://texelec.com/product/isa-compactflash-adapter/).
- ["XT-CF-Lite V4"](http://www.malinov.com/Home/sergeys-projects/xt-cf-lite). 

Ці плати, зазвичай, дозволяють використовувати оновлений BIOS, наприклад, на базі [XTIDE Universal BIOS](https://xtideuniversalbios.org/). ["DoubleROM - IDE Size Limit Remover - Dual Bootable ROM ISA Card"](https://monotech.fwscart.com/DoubleROM_-_IDE_Size_Limit_Remover_-_Dual_Bootable_ROM_ISA_Card/p6083514_19995208.aspx) призначена лише для цього -- додати підтримку в BIOS HD-дисководів, позбутися інших обмежень старих BIOS-ів, за допомогою сучасного [XT-IDE Universal BIOS](https://www.xtideuniversalbios.org/), але можна записати що завгодно.

Цікаво, що більшість таких плат дозволяють перезаписувати BIOS безпосередньо із DOS. 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/XT-IDE-Delux.jpg)|
|:--------------------:|
|  Моя XT-IDE Delux. Купляв [тут](https://www.ebay.com/itm/124122830301). З одного боку, плата дуже класна, хоча вставляючи CF-картку, є загроза погнути піни, а оскільки вони заховані -- біда. Але маю з нею проблеми. Три роки підряд, кожного червня, вмикаю комп'ютер із нею -- плати немає. Першого разу виник обрив між Data3 піном ISA та EEPROM. Візуально дефектів не вдалося знайти, припаяв перемичку. Другого -- те ж із Data7, потім -- ще одного, зараз перемичок три. Ретельно промив спиртом, в надії позбутися залишків агресивного флюсу, то поки нових збоїв не було.|

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/XT-IDE-Delux2.jpg)|
|:--------------------:|
|Зворотна сторона XT-IDE Delux -- дуже подобається інструкція на самій платі[^INST].|

[^INST]: Бачу, що моя травма з 90-х -- знань немає, поради де спитатися -- немає, доступу до Інтернету -- немає, інструкції немає або вона ніяка, тримаєш в руках девайсину і думаєш[^INST2]..., була не тільки в мене -- багато тих плат мають детальну підказку безпосередньо на них. 

[^INST2]: Далі непристойний анекдот про колобка, тільки в плат ніколи не доводилося питати "Чим ти це сказала?". 

Ремарка: "звичайні" СF-to-IDE адаптери 16-бітні та на XT, зазвичай, не працюватимуть. Однак, корисні для машин AT-класу.

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/CF_to_IDE.jpg)|
|:--------------------:|
| CF-IDE40. |

Ще один цікавий адаптер -- спеціалізований. Ноутбук [Tandy 1400 LT (1987)](https://en.wikipedia.org/wiki/Tandy_1400_LT) був обладнаний слотом розширення. Виглядає, що за час актуальності цих комп'ютерів, для слотів нічого не виготовлялося. Однак, через багато років, ентузіасти розробили плату, яка дозволяє використати цей порт для [емулятора HDD на базі CF](https://www.lo-tech.co.uk/wiki/CompactFlash_Adapter_for_Tandy_1400_Laptops). Це відносно просто, завдяки тому, що контакти порта -- ISA, хіба що [із переставленими контактами](https://www.lo-tech.co.uk/wiki/Tandy_1400_Series_Expansion_Slots), тому плата є [варіантом XT-CF](https://www.lo-tech.co.uk/xt-cf-card-for-tandy-1400-series-laptops/).

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/tandy_1400_xt_ide.jpg)|
|:--------------------:|
| Мій адаптер. На жаль, поки не запрацював. |


## FDD  

Дисководи увага не оминула:
- ["DeluxeFloppy - 8-bit ISA Bootable HD Floppy + Serial"](https://monotech.fwscart.com/DeluxeFloppy_-_8-bit_ISA_Bootable_HD_Floppy_+_Serial/p6083514_19478745.aspx) -- універсальний контролер, до якого потрібно підключати справжній дисковод; 
- [Monster FDC](https://www.tindie.com/products/weird/monster-fdc/) для гурманів -- до восьми 5.25 та 3.5 дисководів [різних](https://github.com/skiselev/monster-fdc/blob/main/User_Manual.md) форматів, [від 160 Кб до 2.88 Мб](https://github.com/skiselev/floppy_bios).
- [GoTek USB Floppy Drive Emulator](https://www.gotekemulator.com/), який бере образи із USB-флешки та монтує їх як справжні дисководи;
   - оскільки оригінальна прошивка доволі скромна, популярними є альтернативна прошивка -- [FlashFloppy](https://github.com/keirf/flashfloppy); 
- KryoFlux -- USB-контролер для дисководів із повною підтримкою їх функціоналу (на противагу до поширених [USB 3.5" дисководів](https://www.amazon.com/External-Floppy-Portable-Windows-Required/dp/B00RXEWOAA)), цитуючи [документацію](https://kryoflux.com/?page=kf_features): "Read at lowest level possible - precisely sampling the magnetic flux transition timing";
- Open Source альтернатива до KryoFlux -- [Greaseweazle](https://github.com/keirf/Greaseweazle/wiki) (за допомогою адаптера типу [FDADAP](http://www.dbit.com/fdadap.html) можна навіть 8-дюймові дисководи підключати), хтось навіть [шилд для Arduino](https://github.com/yas-sim/floppy_disk_shield_2d) розробляє.

 
|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/GTK_WHT-01-500x500.png)|
|:--------------------:|
|USB GOTEK floppy emulator в "максимальній" конфігурації (оригінальна помітно скромніша з точки зору інтерфейсу) -- OLED-дисплей із іменем образу, енкодер для вибору, містить п'єзопищалку, яка "емулює" звуки роботи дисководу. Дуже зручний прилад. | 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Greaseweazle_V4.jpg)|
|:--------------------:|
| Greaseweazle V4, фото з офіційного магазину [на ebay](https://www.ebay.co.uk/itm/125781609791). Мій такий в дорозі. |

## Мережа[^C]

[^C]: Та інше connectivity. Бо колеги-зануди із дискусією, що модем -- не мережа, це страшно. :)

16-бітні ISA карти -- не рідкість, але більшість із них відмовляються працювати у 8-бітному режимі -- жодна моя не захотіла[^E8]. Восьмибітові трапляються рідше, а із RJ45[^E2] -- помітно рідкісніші. Тому, наявність нових порадувала. 

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/ISA8_Ethernet-Assembled_Board-Top.jpg)|
|:--------------------:|
| "ISA 8-bit Ethernet Controller", фото [звідси](https://www.tindie.com/products/weird/isa-8-bit-ethernet-controller/). |


[^E8]: Це навіть відкладаючи проблему їх програмного конфігурування -- відповідні утиліти часто потребують 286+. 

[^E2]: Не готовий я поки до витої пари...

Іншим поширеним пристроєм зв'язку був модем. Використання його зараз має свої проблеми, тому альтернатива -- WiFi емулятор модема, може бути цікавою.

Приклад такого проекту -- [Zimodem](https://github.com/bozimmerman/Zimodem), купити можна на [ebay](https://www.ebay.com/itm/204366176209) та [tindie](https://www.tindie.com/products/theoldnet/rs232-serial-wifi-modem-for-vintage-computers-v4/). Використання, що приємно, жодним чином не обмежується IBM PC-машинами.

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/WiFi-Modem.jpg)|
|:--------------------:|
| WiFi-модем разо із мультикартою. Фото [звідси](https://www.ebay.com/itm/204366176209). |

## Інше

Сучасні приклади вирішення проблеми [відсутності RTC]({% post_url /retrocomputing/ibm_pc_compat/2023-08-03-update_boards_AST_1 %}#додаток--ast-sixpackplus):

- [RTC ISA 8 bits](https://www.tindie.com/products/spark2k06/rtc-isa-8-bits-very-low-profile-2/) -- крихітна плата-годинник,
- є урізаним варіантом такої: [8-bit ISA DiskOnChip / RTC board](https://www.smbaker.com/8-bit-isa-diskonchip-rtc-board), яка містить EEPROM (на базі мікросхеми DiskOnChip), відображену на 8Kб вікно в адресному просторі і RTC як бонус.

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/modern_isa_rtc.jpg)|
|:--------------------:|
|Фото із [магазину](https://www.tindie.com/products/spark2k06/rtc-isa-8-bits-very-low-profile-2/), вартість $12.|

# Сучасні клони ретро-комп'ютерів

## CP/M 

[RC2014](https://rc2014.co.uk/) -- сімейство модульних ретрокомп'ютерів на базі Z80. Деякі варіанти вміють запускати CP/M. Зокрема, [RC2014 Pro](https://www.tindie.com/products/semachthemonkey/rc2014-pro-homebrew-z80-computer-kit/) -- з Compact Flash адаптером, UART, 64Кб RAM i MS BASIC в ROM. Простіший варіант -- [RC2014 Classic II](https://www.tindie.com/products/semachthemonkey/rc2014-classic-ii-homebrew-z80-computer-kit/).  Для них є багато інших модулів, зокрема засоби підключення до VGA-моніторів:

- [TMS9918A Video Card Kit](https://www.tindie.com/products/mfkamprath/tms9918a-video-card-kit-for-rc2014/) -- VGA карта на популярному у свій час [ретро-чіпі](https://en.wikipedia.org/wiki/TMS9918),
- [VGA Serial Terminal Kit](https://www.tindie.com/products/maccasoft/vga-serial-terminal-kit-for-rc2014/) -- емулятор терміналу, щоб не потрібно було через USB під'єднувати,
- [Graphics Video Card Kit](https://www.tindie.com/products/maccasoft/graphics-video-card-kit-for-rc2014/).

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/RC2014_Pro.JPG)|
|:--------------------:|
| RC2014 Pro, фото [звідси](https://www.tindie.com/products/semachthemonkey/rc2014-pro-homebrew-z80-computer-kit/).|

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Which_RC2014.PNG)|
|:--------------------:|
| Можливості різних RC2014, фото [звідси](https://www.tindie.com/products/semachthemonkey/rc2014-pro-homebrew-z80-computer-kit/).|


Інші посилання на подібні машини -- див. у репозиторії [RomWBW](https://github.com/wwarthen/RomWBW) -- сучасному варіанті CP/M-подібних ОС, зокрема:

- [Small Computer Central](https://smallcomputercentral.com/) -- одноплатні комп'ютери на різних процесорах, (наприклад [Tiny68K](https://www.retrobrewcomputers.org/doku.php?id=boards:sbc:tiny68k:tiny68k_rev2) на Motorola 68000) , S-100 плати тощо.
- [RetroBrew](https://www.retrobrewcomputers.org/doku.php),

Існує модуль [RimWBW для RC2014](https://www.tindie.com/products/semachthemonkey/512k-rom-512k-ram-romwbw-module-for-rc2014/) із 512 Кб ROM та 512 Кб RAM. А також цілий комп'ютер для цієї ОС: ["SC791 RCBus Z80 RomWBW CP/M Computer Kit"](https://www.tindie.com/products/tindiescx/sc791-rcbus-z80-romwbw-cpm-computer-kit/) -- теж модульний, на базі [RCBus](https://smallcomputercentral.com/rcbus/) -- розширення шини RC2014.

## XT 

Сучасні розробники клонів не оминули класичну [IBM PC/XT](https://en.wikipedia.org/wiki/IBM_Personal_Computer_XT). Купити можна [Homebrew 8088](https://www.ebay.com/itm/155691062888). Згідно офіційного сайту, [включає](https://www.homebrew8088.com/):

- 8088, NEC V20 або  NEC V40,
- 640k RAM, можна більше,
- підтримку PS/2 клавіатури, 
- USB "HDD",
- живлення -- ATX, 

Схеми і код доступні [на GitHub](https://github.com/homebrew8088/8088-PC-Compatible).

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Homebrew8088.jpg)|
|:--------------------:|
|Homebrew 8088, фото [звідси](https://www.ebay.com/itm/155691062888) |

Ще один варіант: [Micro 8088](https://github.com/skiselev/micro_8088), на базі чіпсету Faraday FE2010/FE2010A.

Ще один: [NuXT](https://monotech.fwscart.com/NuXT_v20_-_MicroATX_Turbo_XT_-_10MHz_832K_XT-IDE_Multi-IO_SVGA/p6083514_19777986.aspx), просунута, але дорога.

Нарешті, недавно китайці випустили ноутбук із 8088 процесором, CGA, звуковою картою та опціональним ISA-продовжувачем, XT-CF HDD та USB. Купити, на момент написання, можна на AliExperess: ["Book 8088 DOS system laptop computer CGA graphics card IBM PC XT compatible machine 8088 8086CPU microcomputer principle"](https://www.aliexpress.com/item/1005005528944178.html). Огляди: [1](https://medium.com/@davidly_33504/the-book-8088-is-too-fast-c0a2f01a1cc8) та [2](https://arstechnica.com/gadgets/2023/07/going-deep-with-the-book-8088-the-brand-new-laptop-that-runs-like-its-1981/).

|![](/retrocomputing/ibm_pc_compat/pics/upd_brd/Book-8088-DOS.jpg)|
|:--------------------:|
|Новий 8088 ноутбук, фото з Ali.|

Цікаво, коли вони щось таке на 286 випустять? :-) Хтось із авторів клонів XT згадував на форумі, що може спробувати створити клон [Intel Inboard 386]({% post_url /retrocomputing/ibm_pc_compat/2023-07-31-update_boards_1 %}#intel-inboard-386), але зараз не можу знайти посилання.

# Посилання

- Компанія, що розробляє відповідні плати: [Lo-tech](https://www.lo-tech.co.uk/)
- Ще одна: [Monotech](https://monotech.fwscart.com/) -- 
- Розділ на [Tindie](https://www.tindie.com/browse/vintagecomputing/).

# Виноски

