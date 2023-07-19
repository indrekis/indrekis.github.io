---
layout: post
title:  "MAME -- емулятор взагалі всього, включаючи Осборни"
date:   2023-07-15 7:23:45 +0300
tags: [CP/M, MAME, Osborne, Z80, retrocomputing]
categories: [retrocomputing, CP/M]
comments: true
excerpt_separator: <!--more-->
---

- [MAME]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#mame)
  - [Робота із образами дисків]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#робота-із-образами-дисків)
  - [Інша діагностика емулятора]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#інша-діагностика-емулятора)
    - [Назви дисплеїв]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#назви-дисплеїв)
    - [Інше]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#інше)
  - [Клавіатура]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#клавіатура)
  - [Шум дисководу і перекомпіляція]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#шум-дисководу-і-перекомпіляція)
- [Виноски]({% post_url /retrocomputing/cpm/2023-07-15-cpm_emu_mame %}#виноски)


# MAME

Шукав емулятори для Osborne, спеціалізованих не знайшов, але виявив, що [MAME](https://www.mamedev.org/) їх підтримує. Трохи недолюблюю [цей емулятор](http://indrekis2.blogspot.com/2013/02/mspc-dos-1xx.html)[^1], але корисність його беззаперечна!

<style>body {text-align: justify}</style>

Osborne 1 підтримується нормально. Osborne Executive помічено як такий, що не працює, але, хоча у нього є свої проблеми[^0], в цілому -- використовувати можна. [Osborne Vixen](https://en.wikipedia.org/wiki/Osborne_Vixen) теж підтримується, хоча ретельно не тестував. 

<!--more-->

[^0]: Неприємна проблема для Executive -- він майже завжди  не зауважує, що образ диску замінили. (Дуже рідко -- зауважує, але поки не знаю, коли це спрацьовує).

Дистрибутив MAME не включає ROM та інші потенційно пропрієтарні модулі[^3]. Вміст EPROM Osborne-ів знайшов майже чудом, через кілька днів пошуків, на якихось дивний сайтах. Щоб ви не мусили повторювати цей процес -- викладаю їх тут: [osbexec](/retrocomputing/cpm/files/MAME/osbexec.zip), [osborne1](/retrocomputing/cpm/files/MAME/osborne1.zip), [osborne1nv](/retrocomputing/cpm/files/MAME/osborne1nv.zip), [vixen](/retrocomputing/cpm/files/MAME/vixen.zip). 

- [Офіційни сайт](https://www.mamedev.org/) -- версія звідти працює добре, але, на жаль, не буде звуків роботи дисководу (виправлення -- див. [далі](##Шум-дисководу)). 
  - Найпростіше керувати з консолі[^2]. 
- [Документація](https://docs.mamedev.org/index.html), [PDF](https://docs.mamedev.org/_files/MAME.pdf). 
  - Зокрема, див. [MAME Command-line Usage and OS-Specific Configuration](https://docs.mamedev.org/commandline/index.html).


За базу беру такий командний рядок: 

```bash
./mame osbexec -window -nomaximize -screen0 '\\.\DISPLAY4' -verbose -flop1 <path_to_image>
```

- osbexec -- назва платформи, що емулюється, тут -- Osborne Executive.
  - osborne1 -- очікувано, Osborne 1,
  - vixen -- Osborne Vixen.
- -window -- виконувати у вікні, 
- -nomaximize -- не максимального розміру, тоді воно буде із нативною для емульованої системи роздільною здатністю,
- -screen0 -- перший (і тут -- єдиний) дисплей системи виводити на вказаний далі дисплей хоста. 
  - '\\.\DISPLAY4' -- так називається мій головний екран, як вияснити -- див. далі. 
  - Якщо екран один або влаштовує значення за замовчуванням -- можна не заморочуватися. 
- -verbose -- виводити на консоль детальну інформацію. Допомагало вирішувати проблеми. 
- -flop1 -- образ диску, ''вставленого'' в перший дисковод. 
- Найпростіший рядок, звичайно, буде:
  ./mame osbexec -flop1 <path_to_image>


| ![](/retrocomputing/cpm/pics/osbe_mame_emu_1.png) |
|:-------------------------------:|
| Приклад запуску. |

| ![](/retrocomputing/cpm/pics/osbe_mame_emu_2.png) |
|:-------------------------------:|
| Жахливе повідомлення, що Osborne Executive не підтримується. Дещо перебільшене. |

Інші корисні опції: 

- Вміст другого дисководу вказується за допомогою опції ``-flop2``.
- -log -- створює error.log (в директорії виконавчого файлу MAME), із повідомленнями про помилку (доповнює -verbose) -- корисне для зневадження.
  - Див. також -oslog -- та ж інформація виводиться системними засобами повідомлення про помилку, наприклад, передається дебагеру, якщо код виконується під ним. Ці опції сумісні.
- -debug -- дуже корисна опція! Запускає інтерактивний дебагер емульованої системи, де можна подивитися, що відбувається в емульованій машині.
  - -debugger <module> вибирає вид модуля -- див. документацію. Окрім варіантів інтерфейсу вбудованого дебаггера: windows, Qt, imgui тощо, цікавим є модуль gdbstub -- тоді до системи можна під'єднатися з допомогою GDB: ``target remote 127.0.0.1:port_n``, за замовчуванням, порт -- 23946, керується -debugger_port. Бажано мати GDB <a href="https://github.com/atsidaev/gdb-z80">з підтримкою Z80</a>.
- -snapshot_directory <path> -- вказати директорію, де будуть зберігатися снепшоти та відео.
  - -snapsize 640x480 -- встановити роздільну здатність снепшота. Важливо для Osborne, оскільки емулятор не відтворює проміжків між вертикальними лініями пікселів (див. макро-фото [тут]({% post_url /retrocomputing/cpm/2023-07-13-osbornes %})).
  - -aviwrite <filename> -- записати (некомпресоване, а тому -- величезне) відео сесії в форматі AVI.
  - -mngwrite <filename> те ж, в форматі <a href="https://en.wikipedia.org/wiki/Multiple-image_Network_Graphics">MNG</a> -- анімованому варіанті PNG, очікувано -- без звуку.
  - -wavwrite <filename> -- а так можна записати звук для MNG. Це, певне, оптимальніше, ніж створювати AVI та компресувати його пізніше.
- -record <filename> -- зберегти ввід.
  - -playback <filename> -- відтворити збережену сесію.
  - Зрозуміло, що конфігурація емульованої системи має співпадати.
  - За замовчуванням, файл зберігається в директорії <mamepath>/inp. Керується: -input_directory <path>.
  - -exit_after_playback -- дозволяє завершити емуляцію після відтворення. (За замовчуванням -- -noexit_after_playback).
- Керування яскравістю: -brightness <value>, -contrast <value>, -gamma <value>.
- Керування гучністю: -volume / -vol <value>, додатково, -sound дозволяє вибрати спосіб відтворення звуку: dsound | coreaudio | sdl | xaudio2 | portaudio | none.
- -nvram_save -- зберегти вміст NVRAM, не потрібна для Osborne, за відсутністю NVRAM, але, взагалі, корисна опція. Шлях можна задати так: -nvram_directory <path>.
-  -[no]mouse -- [не] перехоплює мишку.
- Сімейство опцій діагностики: -listmedia, -listdevices,  -listslots, 
-listclones, -listbrothers -- приклади [далі](#Інше).

MAME дозволяє створювати пакети програмного забезпечення (поточний список виводиться -listsoftware) -- можливо, колись оформлю такий комплект ПЗ для Osborne. 

| ![](/retrocomputing/cpm/pics/osbe_emu_dbg2.png) |
|:-----------------------------:|
| Вбудований дебагер MAME |

| ![](/retrocomputing/cpm/pics/osbe_emu_dbg3.png) |
|:-----------------------------:|
| Використання зовнішнього GDB. Видно, що Z80 не підтримується... |


## Робота із образами дисків

MAME підтримує багато різних форматів образів. Вивести підтримувані конкретною емульованої системою формати можна за допомогою -listmedia:

```bash
$ ./mame osbexec -listmedia
SYSTEM           MEDIA NAME       (brief)    IMAGE FILE EXTENSIONS SUPPORTED
---------------- --------------------------- -------------------------------
osbexec          floppydisk1      (flop1)    .mfi  .dfi  .hfe  .mfm  .td0  .imd  .d77  .d88  .1dd  .cqm  .cqi  .dsk
osbexec          floppydisk2      (flop2)    .mfi  .dfi  .hfe  .mfm  .td0  .imd  .d77  .d88  .1dd  .cqm  .cqi  .dsk

$ ./mame osborne1 -listmedia
SYSTEM           MEDIA NAME       (brief)    IMAGE FILE EXTENSIONS SUPPORTED
---------------- --------------------------- -------------------------------
osborne1         floppydisk1      (flop1)    .mfi  .dfi  .hfe  .mfm  .td0  .imd  .d77  .d88  .1dd  .cqm  .cqi  .dsk
osborne1         floppydisk2      (flop2)    .mfi  .dfi  .hfe  .mfm  .td0  .imd  .d77  .d88  .1dd  .cqm  .cqi  .dsk

```

На жаль, писати MAME вміє лише у формати .mfi -- ''MAME floppy image'' та .mfm -- [''HxC Floppy Emulator floppy disk image''](https://hxc2001.com/). Знову ж, на жаль, поки я не навчився з них зробити щось, придатне для використання за межами MAME.
<!-- Описати, якщо вдасться -->

Якщо запис у образ не підтримується, писати на дискету з емулятора все рівно буде вдаватися, але зміни зникатимуть після вимкнення емулятора.

Образ диску для використання можна передати з командного рядку: -flop1 <imagefile> чи  -flop2 <imagefile>. Але ''диски'' можна змінювати і під час емуляції.

Щоб мати доступ до меню емулятора, потрібно натиснути ``Scroll lock``, тоді деякі клавіші [набувають спеціального значення](https://docs.mamedev.org/usingmame/defaultkeys.html). Щоб вимкнути цю можливість -- натисніть ``Scroll lock`` ще раз -- дивлячись на список далі, краще її на тривалий час не залишати.

- Tab -- викликає меню емулятора.
- Esc -- завершити емулятор.
- P -- пауза емуляції.
- F3 -- soft reset.
- Left Shift+F3 -- hard reset.
- F12 -- зберегти знимок екрану (screen snapshot).
 - В мене чомусь давало неприродні пропорції, щось типу 8:3, 
- Left Shift+F12 -- розпочати запис MNG-відео.
- Left Control+Left Shift+F12  -- розпочати запис AVI-відео.
- Left Alt+Enter -- переключатися між вікном і повноекранним режимом (працює і без Scroll lock). 

Далі -- приклади роботи із меню. UI дивний -- ймовірно, заради портабельності, але -- якраз через це недолюблюю MAME... Оскільки розібратися зайняло в мене купу часу -- наводжу багато скрінів, у надії, що комусь цю дорогу спростить.

| ![](/retrocomputing/cpm/pics/osbe_emu_changedisk1.png) |
|:-------------------------------:|
| Меню емулятора. |

| ![](/retrocomputing/cpm/pics/osbe_emu_changedisk1b.png) |
|:-------------------------------:|
| Меню керування дисководами, доступне через File Manager. |

| ![](/retrocomputing/cpm/pics/osbe_emu_changedisk1c.png) |
|:-------------------------------:|
| Меню вибору образу. |

| ![](/retrocomputing/cpm/pics/osbe_emu_changedisk2.png) |
|:-------------------------------:|
| Діалог зміни диску, який не є у форматі .mfi чи .mfm. -- або read-onle (зміни зникнуть після перезапуску емуляції), або зберегти в один із підтримуваних для запису форматів. Варіант diff не підтримується.  |

| ![](/retrocomputing/cpm/pics/osbe_emu_changedisk3.png) |
|:-------------------------------:|
| Діалог зміни диску у форматі .mfi чи .mfm. -- для яких є підтримка запису. |

| ![](/retrocomputing/cpm/pics/osbe_emu_changedisk4.png) |
|:-------------------------------:|
| Вибір імені файлу. Можливо, я недостатньо ретельно шукав, але єдиний спосіб, що спрацював -- видаляти Backspace і вписувати нове ім'я. |

| ![](/retrocomputing/cpm/pics/osbe_emu_changedisk5.png) |
|:-------------------------------:|
| Останній крок -- вибір типу образу для запису. |

## Інша діагностика емулятора 

Цікаву інформацію щодо емулятора можна отримати наступними командами. Показую текстово, заради можливості пошуку і копіювання. 

### Назви дисплеїв 

Мені зручніше працювати у вікні, і так, щоб картинка потрапляла на головний монітор. Якщо перше вирішилося  -window -nomaximize, то друге було складніше -- для мене назва цього монітору видалася несподіваною, а вияснити, чому -screen0 не працює, вдалося за допомогою -verbose:

```text
<Купа тексту>
Video: Monitor 18446744072111654741 = "\\.\DISPLAY1"
Video: Monitor 1469454067 = "\\.\DISPLAY4" (primary)

```

Кумедніше, що через кілька місяців той же монітор раптом став DISPLAY5.

### Інше 

Всіляка додаткова інформації щодо емуляції Osborne-ів в MAME:

```bash
$ ./mame osbexec  -listclones
Name:            Clone of:

$ ./mame osborne1  -listclones
Name:            Clone of:
osborne1nv       osborne1
osborne1sp       osborne1

$ ./mame vixen -listclones
Name:            Clone of:


$ ./mame osbexec  -listbrothers
Source file:         Name:            Parent:
osborne/osbexec.cpp osbexec

$ ./mame osborne1  -listbrothers
Source file:         Name:            Parent:
osborne/osborne1.cpp osborne1
osborne/osborne1.cpp osborne1nv       osborne1
osborne/osborne1.cpp osborne1sp       osborne1

$ ./mame vixen -listbrothers
Source file:         Name:            Parent:
osborne/vixen.cpp    vixen


$ ./mame osbexec  -listbios
No BIOS options for system Executive (osbexec)

$ ./mame osborne1  -listbios
BIOS options for system Osborne-1 (osborne1):
    vera             BIOS version A
    ver12            BIOS version 1.2
    ver121           BIOS version 1.2.1
    ver13            BIOS version 1.3
    ver14            BIOS version 1.4
    ver143           BIOS version 1.43
    ver144           BIOS version 1.44

$ ./mame vixen -listbios
No BIOS options for system Vixen (vixen)


$ ./mame osbexec  -listdevices
Driver osbexec (Executive):
   <root>                         Executive
     ctc                          Intel 8253 PIT
       counter0                   PIT Counter
       counter1                   PIT Counter
       counter2                   PIT Counter
     flop_list                    Software List
     maincpu                      Zilog Z80 @ 3.99 MHz
     mainirq                      Input Merger (any high)
     mb8877                       Fujitsu MB8877 FDC @ 998.40 kHz
       0                          Floppy drive connector abstraction
         525ssdd                  5.25" single-sided double density floppy drive
           floppysound            Floppy sound @ 44.10 kHz
           flopsndout             Speaker
       1                          Floppy drive connector abstraction
         525ssdd                  5.25" single-sided double density floppy drive
           floppysound            Floppy sound @ 44.10 kHz
           flopsndout             Speaker
     modem                        RS-232 Port
     mono                         Speaker
     palette                      palette
     pia_0                        6821 PIA
     pia_1                        6821 PIA
     printer                      RS-232 Port
     ram                          RAM
     rtc                          Generic ripple counter
     screen                       Video Screen @ 11.98 MHz
     sio                          Z80 SIO @ 3.99 MHz
       cha                        Z80 SIO channel
       chb                        Z80 SIO channel
     speaker                      Filtered DAC

$ ./mame osborne1  -listdevices
Driver osborne1 (Osborne-1):
   <root>                         Osborne-1
     acia                         MC6850 ACIA
     flop_list                    Software List
     gfxdecode                    gfxdecode
     ieee_bus                     IEEE-488 bus
     maincpu                      Zilog Z80 @ 3.99 MHz
     mb8877                       Fujitsu MB8877 FDC @ 998.40 kHz
       0                          Floppy drive connector abstraction
         525ssdd                  5.25" single-sided double density floppy drive
           floppysound            Floppy sound @ 44.10 kHz
           flopsndout             Speaker
       1                          Floppy drive connector abstraction
         525ssdd                  5.25" single-sided double density floppy drive
           floppysound            Floppy sound @ 44.10 kHz
           flopsndout             Speaker
     mono                         Speaker
     palette                      palette
     pia_0                        6821 PIA
     pia_1                        6821 PIA
     rs232                        RS-232 Port
     screen                       Video Screen @ 7.98 MHz
     speaker                      Filtered DAC

$ ./mame vixen  -listdevices
Driver vixen (Vixen):
   <root>                         Vixen
     2n                           Intel 8155 RAM, I/O & Timer @ 3.99 MHz
     5f                           Zilog Z80 @ 3.99 MHz
     5n                           FD1797 FDC @ 998.40 kHz
       0                          Floppy drive connector abstraction
         525dd                    5.25" double density floppy drive
           floppysound            Floppy sound @ 44.10 kHz
           flopsndout             Speaker
       1                          Floppy drive connector abstraction
         525dd                    5.25" double density floppy drive
           floppysound            Floppy sound @ 44.10 kHz
           flopsndout             Speaker
     c3                           Intel 8251 USART
     c7                           Intel 8155 RAM, I/O & Timer @ 3.99 MHz
     discrete                     Discrete Sound
     disk_list                    Software List
     ieee_bus                     IEEE-488 bus
     mono                         Speaker
     palette                      palette
     ram                          RAM
     rs232                        RS-232 Port
     screen                       Video Screen @ 11.98 MHz
     vsync                        Timer


$ ./mame osbexec  -listslots
SYSTEM           SLOT NAME        SLOT OPTIONS     SLOT DEVICE NAME
---------------- ---------------- ---------------- ----------------------------
osbexec          modem            dec_loopback     RS232 Loopback (DEC 12-15336-00)
                                  ie15             IE15 Terminal
                                  keyboard         Serial Keyboard
                                  loopback         RS232 Loopback
                                  mockingboard     Sweet Micro Systems Mockingboard D
                                  null_modem       RS232 Null Modem
                                  patch            RS-232 Patch Box
                                  printer          Serial Printer
                                  pty              Pseudo terminal
                                  rs232_sync_io    RS232 Synchronous I/O
                                  rs_printer       Radio Shack Serial Printer
                                  sunkbd           Sun Keyboard Adaptor
                                  swtpc8212        SWTPC8212 Terminal
                                  terminal         Serial Terminal

                 printer          dec_loopback     RS232 Loopback (DEC 12-15336-00)
                                  ie15             IE15 Terminal
                                  keyboard         Serial Keyboard
                                  loopback         RS232 Loopback
                                  mockingboard     Sweet Micro Systems Mockingboard D
                                  null_modem       RS232 Null Modem
                                  patch            RS-232 Patch Box
                                  printer          Serial Printer
                                  pty              Pseudo terminal
                                  rs232_sync_io    RS232 Synchronous I/O
                                  rs_printer       Radio Shack Serial Printer
                                  sunkbd           Sun Keyboard Adaptor
                                  swtpc8212        SWTPC8212 Terminal
                                  terminal         Serial Terminal

                 mb8877:0         525ssdd          5.25" single-sided double density floppy drive

                 mb8877:1         525ssdd          5.25" single-sided double density floppy drive

$ ./mame osborne1  -listslots
SYSTEM           SLOT NAME        SLOT OPTIONS     SLOT DEVICE NAME
---------------- ---------------- ---------------- ----------------------------
osborne1         rs232            dec_loopback     RS232 Loopback (DEC 12-15336-00)
                                  ie15             IE15 Terminal
                                  keyboard         Serial Keyboard
                                  loopback         RS232 Loopback
                                  mockingboard     Sweet Micro Systems Mockingboard D
                                  null_modem       RS232 Null Modem
                                  patch            RS-232 Patch Box
                                  printer          Serial Printer
                                  pty              Pseudo terminal
                                  rs232_sync_io    RS232 Synchronous I/O
                                  rs_printer       Radio Shack Serial Printer
                                  sunkbd           Sun Keyboard Adaptor
                                  swtpc8212        SWTPC8212 Terminal
                                  terminal         Serial Terminal

                 mb8877:0         525ssdd          5.25" single-sided double density floppy drive
                                  525sssd          5.25" single-sided single density floppy drive

                 mb8877:1         525ssdd          5.25" single-sided double density floppy drive
                                  525sssd          5.25" single-sided single density floppy drive

$ ./mame vixen  -listslots
SYSTEM           SLOT NAME        SLOT OPTIONS     SLOT DEVICE NAME
---------------- ---------------- ---------------- ----------------------------
vixen            rs232            dec_loopback     RS232 Loopback (DEC 12-15336-00)
                                  ie15             IE15 Terminal
                                  keyboard         Serial Keyboard
                                  loopback         RS232 Loopback
                                  mockingboard     Sweet Micro Systems Mockingboard D
                                  null_modem       RS232 Null Modem
                                  patch            RS-232 Patch Box
                                  printer          Serial Printer
                                  pty              Pseudo terminal
                                  rs232_sync_io    RS232 Synchronous I/O
                                  rs_printer       Radio Shack Serial Printer
                                  sunkbd           Sun Keyboard Adaptor
                                  swtpc8212        SWTPC8212 Terminal
                                  terminal         Serial Terminal

                 5n:0             525dd            5.25" double density floppy drive

                 5n:1             525dd            5.25" double density floppy drive

```

**Ремарки**:

- Слоти, виведені опцією -listslots, можна використовувати як опції, щоб передати ім'я пристрою, використаного у цьому слоті, наприклад: ``./mame osborne1 -mb8877:0 525ssdd <other options>``
  - Опції доданого пристрою можна задати з командного рядка, а подивитися -- в UI, в меню Slot Devices.
- Вибрати відмінну від стандартної версію BIOS: ``./mame osborne1 -bios ver144 <other options>``
- osborne1nv -- із розщширенням Nuevo Video, яке дозволяло 80х24 символи, і мало власний знакогенератор. На жаль, не вдалося знайти подробиць про це відео-розширення (хіба про інший пакет оновлень від Nuevo -- [Double density floppy](http://www.bitsavers.org/bits/Osborne/Osborne1/Nuevo_Electronics_DD_Upgrade_Manual.pdf)).
- osborne1sp -- із [Screen PAC](http://www.bitsavers.org/pdf/osborne/osborne1/SCREEN-PAC_Option_Installation_Procedures_Oct83.pdf), розширення, до додавало зовнішній RCA порт та дозволяло виводити зображення із 80 символів на рядок, замість ''рідних'' 52.

Базова ідея використання слотів:

```bash
./mame osbexec -printer printer -modem null_modem -listmedia 
SYSTEM           MEDIA NAME       (brief)    IMAGE FILE EXTENSIONS SUPPORTED
---------------- --------------------------- -------------------------------
osbexec          bitbanger        (bitb)     .
osbexec          printout         (prin)     .prn
osbexec          floppydisk1      (flop1)    .mfi  .dfi  .hfe  .mfm  .td0  .imd  .d77  .d88  .1dd  .cqm  .cqi  .dsk
osbexec          floppydisk2      (flop2)    .mfi  .dfi  .hfe  .mfm  .td0  .imd  .d77  .d88  .1dd  .cqm  .cqi  .dsk
```

Тепер з'явилося два нових пристрої, якими можна керувати за допомогою опції -bitb i -prin, відповідно. Зокрема, -modem=null_modem дозволяє зробити щось таке:  -bitb  socket.localhost:6809, під'єднавшись за допомогою, скажімо, PuTTY, а -printout temp.prn виводитиме надруковане у цей файл. Поки не майже експериментува із цими засобами.
<!--- https://tlindner.macmess.org/?page_id=659 -- деякі приклади -->
<!--- https://forum.vcfed.org/index.php?threads/nabu-pc-emulation-under-mame.1241092/page-13 -->
<!--- https://easyemu.mameworld.info/mameguide/getting_started/running_mame.html -->

## Клавіатура

На жаль, чомусь не працюють з коробки символи [ та ]. Перевірити налаштування можна в меню, Input settings -> Input assignments. Виправив, змінивши в Keyboard selection режим Keyboard mode з Emulated на Natural[^4].


## Шум дисководу і перекомпіляція

MAMEUI шум дисководу підтримувала для Осборнів, але жодна версія з офіційного сайту -- ні. Після тривалих експериментів, стало зрозуміло, що причина в коді. Довелося перекомпільовувати. 

> Кумедно: коли працював із справжнім Osborne Executive, насторожився через диринчання дисководів -- з досвіду роботи із сучаснішими, нагадувало, ніби вони починають виходити з ладу. Аж поки не почув такий же звук в емуляторі -- це просто звуки повільніших дисководів, 250 kbps.

Оскільки в мене є MSYS2/MinGW64 із великим набором пакетів, компіляція [згідно інструкцій](https://docs.mamedev.org/initialsetup/compilingmame.html#using-a-standard-msys2-installation) спрацювала з коробки. Однак, якщо ви не працюєте багато із MSYS2 -- краще завантажити адаптований комплект -- [Tools for building MAME on Windows](https://www.mamedev.org/tools/). Під Linux-ами мало б теж нормально компілювати.

Можна скомпілювати лише для конкретних платформ -- це швидше і створює менший виконавчий файл (всього лиш 100 Мб):

```bash
MINGW64=/mingw64 make SUBTARGET=osborne SOURCES=src/mame/osborne TOOLS=1 REGENIE=1 NOWERROR=1 -j5
```
> Опція TOOLS вказує скомпілювати деякі додаткові інструменти. Виконавчий файл матиме ім'я osborne. -j5 -- компілювати з використанням до 5 паралельних процесів.
> Ретельно не досліджував, але встановлювати змінну потрібно, якщо MSYS2 не за шляхом за замовчуванням.

> Цей текст було написано в січні 2023-го, то зараз, в липні, виникли проблеми -- після черогового оновлення MSYS2, щоб скомпілювати ті ж, не оновлені, джерельні тексти:
> - стала потрібною NOWERROR=1, 
> - в ``src/osd/modules/lib/osdlib.h`` потрібно додати ``#include <cstdint>``.
> Після оновлення з git, помилка з cstdint зникла, але звук не запрацював -- потрібно патчити. Не зважаючи на редизайн коду, патч майже не змінився.

Або -- із підтримкою всіх систем (але -- 300 Мб виконавчий файл, і пара годин компіляції з HDD):

```bash
 make TOOLS=1 -j5
```

Код, в цілому, справляє хороше враження. Певне, без цього, такий великий проект не зміг би функціонувати. 

За емуляцію дисководів відповідає клас ``floppy_image_device`` із ``<repo>/src/devices/imagedev/floppy.h`` та ``floppy.cpp``, метод ``enable_sound(bool)`` вмикає чи вимикає цей засіб. (Також, для успішного фунціювання, потрібні семпли у ``<mamedir>/samples/floppy``). За замовчуванням, підтримка звуку вимкнена, але код емуляції конкретних платформ її вмикає якось так (цей приклад -- із ``<repo>/src/devices/bus/isa/fdc.cpp``): 

```C
FLOPPY_CONNECTOR(config, "fdc:0", pc_dd_floppies, "525dd",
                      isa8_fdc_device::floppy_formats).enable_sound(true);
```

Наприклад:

```C
	FLOPPY_CONNECTOR(config, "mb8877:0", osborne2_floppies, "525ssdd", 
            floppy_image_device::default_mfm_floppy_formats).enable_sound(true); 
	FLOPPY_CONNECTOR(config, "mb8877:1", osborne2_floppies, "525ssdd", 
            floppy_image_device::default_mfm_floppy_formats).enable_sound(true);
```

(Для vixen звук працює без змін).

> Під час випробувань стався курйоз. Я скопіював файли, які збирався редагувати. І ``REGENIE=1``  замість вихідних, ``src/mame/osborneosbexec.cpp``, підхопило ці копії. Тому здалося, що такі команди, додані до ``osborneosbexec.cpp``, не діють.
>
> В процесі пошуку вирішення, спробував в конструкторі класу ``floppy_image_device`` встановити ``m_make_sound`` в true. Це допомогало, але як впливає на інші платформи -- не перевіряв. В принципі, для MAMEUI так є за замовчуванням, тому, думаю -- великої шкоди не мало б бути.

Щоправда, в будь якому випадку, звук руху дисководів для Osborne більше не вимикається, після того як вперше ввімкнувся. Спробував, на IBM PC 5150 все нормально вимикалося. 


# Виноски 

[^1]: В основному, через UI, але почитав код, скопілював і сильно змінив ставлення! 

[^2]: Варіант з GUI ([1](http://www.mameui.info/), [2](https://github.com/Robbbert/mameui)), закинутий 2022 року, теж можна використовувати, але він дивний -- ігнорує задану в GUI конфігурацію після першого запуску і т.д., тому певне не вартує. 

[^3]: [mame-ao](https://github.com/sam-ludlow/mame-ao) обіцяє, що вміє завантажити необхідне з archive.org, я поки не випробовував.

[^4]: Emulated намагається відтворити поведінку оригінального пристрою. Наприклад, для Осборна не будуть працювати клавіші, відсутні на клавіатурі Осборна. Natural відтворює поведінку, яка природніша для сучасних користувачів. Наприклад, в режимі Emulated, backspace не діє -- стирати можна лище клавішею "стрілка вліво", а в Natural -- діятиме так само, як і стрілка. 