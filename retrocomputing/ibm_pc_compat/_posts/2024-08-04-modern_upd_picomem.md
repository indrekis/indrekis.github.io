---
layout: post
title:  "PicoMEM -- крутезна сучасна плата для IBM PC/XT/AT"
date:   2024-08-28 1:23:45 +0300
tags: [retrocomputing, IBM PC та сумісні, плати оновлення]
# categories: [retrocomputing, IBM-PC-compat]
comments: true
excerpt_separator: <!--more-->
---

Нещодавно завелася в мене [PicoMEM](https://github.com/FreddyVRetro/ISA-PicoMEM) -- крутезна плата оновлення, зовсім недавно створена. Я все ще під враження, то ж запрошую познайомитися і читачів. 

- [Опис]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#опис)
- [Тестування]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#тестування)
  - [Тести коректності]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#тести-коректності)
- [Експерименти з UMB]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#експерименти-з-umb)
  - [Особливості DOSXMAX 1.7]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#особливості-dosxmax-17)
  - [Результати]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#результати)
  - [Файли конфігурації]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#файли-конфігурації)
    - [CONFIG.SYS]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#configsys)
    - [AUTOEXEC.BAT]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#autoexecbat)
    - [LDNE2000.BAT]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#ldne2000bat)
- [Посилання]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#посилання)
- [Виноски]({% post_url /retrocomputing/ibm_pc_compat/2024-08-04-modern_upd_picomem %}#виноски)


# Опис

Згадуючи попередній мій огляд [плат оновлення]({% post_url /retrocomputing/ibm_pc_compat/2023-08-05-modern_update_boards_1 %}), PicoMEM, заміняє більшість з них, при чому -- одночасно. Базується на Raspberry Pi Zero, з мікроконтролером RP2040. На момент написання[^SDRB] вміє наступне:

[^SDRB]: Серпень 2024.  Автор називає її "програмно визначена ISA-плата", з часом функціонал доповнюється або змінюється. 


![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/PicoMEM-Front.jpg)

<style>body {text-align: justify}</style>

<!--more-->


- **Емуляція пам'яті** -- як **refill** першого мегабайта, так і до 4Мб **EMS**-пам'яті.
  - Тобто, може заповнювати основну пам'ять, як нижче 640 Кб, так і вище -- аж до відеопам'яті, а також заповнювати дірки між 640 і 1024 Кб -- **UMB**.  
  - Поки, перших 128 Кб емулює з пам'яті мікроконтролера, швидко (в термінах тих машин -- без циклів очікування), решта -- з PSRAM плати, повільно, з додатковими циклами очікування.
    - Тобто, використання PSRAM може бути повільнішим навіть за RAM IBM PC 5150.
  - Грануляція -- 16 Кб. 
  - Карту вже встановленої в системі пам'яті визначає автоматично. 
  - Керується з [налаштувань BIOS](https://github.com/FreddyVRetro/ISA-PicoMEM/wiki/BIOS-Setup-usage), контроль хороший, але не повний. За найменших змін --  забуває налаштування, що певне і розумно.
  - [Драйвер EMS](https://github.com/FreddyVRetro/ISA-PicoMEM/wiki/PicoMEM-device-Drivers) надається -- модифікація драйвера LTEMM.EXE для Lo-tech 2MB EMS Board.
    - Все необхідне він детектує автоматично, тому інсталюється так: ``DEVICE=PMEMM.EXE /n`` -- \n забороняє тестувати пам'ять (це доооовго!). 
  - Кадр EMS може знаходитися лише за адресою 0xD000 або 0xE000.
- **Емулює свій ROM**. 
  - Однак, користувацькі образи ROM поки не підтримуються.
  - Конфігурація дозволяє обрати базову адресу BIOS[^SEGP]: 0xD000 чи 0xC800.
    - Здійснюється файлом [config.txt](https://github.com/FreddyVRetro/ISA-PicoMEM/wiki/Configuration-files) в корені MicroSD-картки, таким рядком: ``BIOS C800``[^OtherOpt].
    - Якщо є XT IDE, краще обрати 0xD000, а XT IDE BIOS розташувати за 0xC800. 
    - Інакше може бути зручнішим вибрати 0xC800 -- на багатьох конфігураціях, хоч і не завжди, це дасть більше простору для EMS-вікна і UMB-блоків.
  - Існують варіанти прошивки для чорно-білих екранів.
  - Оновлення -- звичайне програмування Pi Zero: натискаємо кнопку BOOTSEL, під'єднуємо по мікро-USB -- з'являється віртуальний диск, туди копіюється прошивка. 
    - Плата доволі капризна до кабелів -- в мене запрацював п'ятий із справних, короткий.
- **Емуляція дисководів**. 
  - Образи бере з директорії ``/FLOPPY`` у кореневому каталозі картки. 
    - До 63 образів одночасно.
  - Імена образів -- до 13 літер.
  - Поки розуміє лише ''плоскі'' образи, .img. 
  - Емулює до двох дисководів.
  - Наживо, не з конфігурації BIOS, можна замінити лише диск у дисководі A: і лише у текстовому режимі[^WIBP]. 
  - Для цього потрібно натиснути Alt-Ctrl-F2.
  - Якщо використовується KEYB, DOSKEY тощо, може бути потреба викликати утиліту ``PMINIT /k``, щоб відновити перехоплення відповідного переривання. 
- **Емуляція HDD**. 
  - Образи бере з директорії ``/HDD`` у кореневому каталозі картки. 
  - Вміє створювати нові з налаштувань BIOS.
  - Імена образів -- до 13 літер.
    - До 63 образів одночасно.
  - Поки розуміє лише ''плоскі'' образи, .img, до 4Гб. 
    - Автор рекомендує обмежуватися до розміром 500 Мб, а краще 200 Мб, з міркувань продуктивності.
  - Геометрія, виключно: **H:16, S:63, C:x**, де x визначається з розміру образу.
  - Емулює до чотирьох дисків.
  - Фізичний диск буде HDD0[^SHFF]. 
    - Вони часто використовують DMA, яка поки не підтримується.
  - Диски XT IDE навпаки, будуть після дисків PicoMEM. 
    - Тоді можуть бути проблеми емуляції більше одного диску. 
    - Потрібно заборонити "Use PicoMEM Boot code".
- Образи дисків та **свою конфігурацію** бере з MicroSD-картки, форматованої у [FAT16/32](https://github.com/FreddyVRetro/ISA-PicoMEM/tree/main/images)[^EXTFS]. 
  - Автор рекомендує якнайшвидшу, розміром не менше 4 Гб.
- **Емуляція NE2000**-сумісної картки по WiFi! 
  - Конфігурується файлом ``wifi.txt`` в корені картки: рядок з SSID, рядок з паролем. 
  - Переривання вибирається в налаштуванні BIOS, має відрізнятися від переривання картки.
  - [Драйвер](https://github.com/FreddyVRetro/ISA-PicoMEM/wiki/PicoMEM-device-Drivers) надається, ``PM2000``, все детектує автоматично.
- **USB-мишка**, через OTG перехідник.
  - [Драйвер](https://github.com/FreddyVRetro/ISA-PicoMEM/wiki/PicoMEM-device-Drivers) надається: ``PMMOUSE``, модифікований драйвер ``CTMOUSE``, все детектує автоматично.
- **Емуляція USB-джойстика** -- поки не пробував.
  - Клавіатура поки не підтримується.
- **Емуляція звукової карти**,  Adlib.
  - Потребу I2S-модуль PCM5102, з ним -- чудово працює. 
- Вміє виводити POST-коди.
  - Не випробовував, оскільки потребує [Qwiic-дисплей](https://www.sparkfun.com/products/16916).

Нюанси: 

- На жаль, поки не підтримує DMA в емульованій пам'яті.
  - Через це, якщо використовуються фізичні дисководи і їх буфер потрапить в емульовану RAM, комп'ютер відмовиться працювати. 
  - Те ж може бути з фізичними HDD.
  - Те ж може статися з звуковою картою, але автор каже, що це помітно менш ймовірно, якщо в системі хоча б 512 Кб RAM.
  - Автор обіцяє скоро це виправити. 
- Список машин, які підтримуються, є [в документації](https://github.com/FreddyVRetro/ISA-PicoMEM). Цитувати його не буду, хотів би хіба відзначити:
  - На 12 МГц 386 та деяких 16 МГц 286 не працює -- припускаю, перестає встигати.
  - Нюанси налаштування для деяких систем -- [тут](https://github.com/FreddyVRetro/ISA-PicoMEM/wiki/Computer-Specific-configuration).
- Хотілося б якийсь контроль над таймаутами при завантаженні.
  - Можливо, також ручний контроль над refill.
 


[^EXTFS]: Документація стверджує, що підтримує ще ExtFS -- не пробував. 

[^SEGP]: Адреса сегменту, тобто, лінійні адреси 0xD0000 чи 0xC8000.

[^OtherOpt]: Інші опції: ``PREBOOT`` -- цитуючи документацію, *will force the BIOS to start when the Option ROM is executed. This parameter may not work in some machines, as the BIOS is not totally initialized at the beginning.* та ``IRQ x``, для вказання номера переривання, який буде використовувати плата.

[^WIBP]: Цікаво, як швидко це стане для мене проблемою?..

[^SHFF]: Що буде, якщо їх кілька, автор не пише, а я поки не пробував.

Плата, поки, частково Open Source, автор обіцяє відкрити джерельні тексти прошивки з часом, коли будуть готові. Дуже чекаю! :-)

> А ще, для мене вона є ілюстрацією, наскільки зросла продуктивність розробників зараз -- перше повідомлення від розробника бачу [в самому кінці 2022](https://forum.vcfed.org/index.php?threads/picomem-project-pi-pico-on-an-isa-board.1241199/), враховуючи його слова там -- платі трохи більше двох років, рахуючи від ідеї. Звичайно, автор не все робив сам, але -- все ж, прогрес вражає!  

# Тестування

Тестував на чотирьох машинах: 

- XT-машині на базі Juko ST, 
  - Встановив 8087 -- жахливо гріється,
- на своїй IBM PC 5150,
- на [HT-12 286 + 287, 16 МГц](https://theretroweb.com/motherboards/s/unknown-ht-12-286), 
  - Напаяний на платі 287 теж дууууже гріється, як і всі мої 8087 і 287-мі... Сама система трохи нестабільна -- не знаю, чи через  287, чи через вік плати, чи через PicoMEM.
- на Cyrix 486DLC + Cyrix 387, 66 МГц.

Випробував UMB, EMS, FDD, HDD, NE2000, USB-мишку, звукову карту. 

- XT Juko ST: все працює чудово -- суб'єктивно стабільніше, ніж на брендованих машинах, про які я писав раніше. Звукову карту випробовував тільки на цій машині.
  - Доповнення основної пам'яті безпосередньо над 640 Кб може колись спробую -- для того потрібно EGA чи CGA картку поставити і відповідний монітор -- поки можу для цього хіба монітор від Amstrad 1640 використовувати, це трохи мороки. 
  - Карта може спробувати використати область пам'яті VGA, 0xA000-0xB7FF, якщо вимкнути *Ignore Video Segment*, не став експериментувати, від гріха. 
  - Джойстика не маю, POST-коди вимагають дорогого дисплею -- поки не планую.
  - FtpSrv з MTCP трохи нестабільніший, ніж я звик -- зависає періодами. Не перевіряв, чи це пов'язано з емуляцією, чи просто з перегрівом 8087.
- IBM PC 5150: в цілому, теж все працює, але ретельно не тестував.
  - Дуже здивувався, як на IBM PC 5150 працює EMS, DOS 6.22, NE2000-сумісна картка і USB-мишка.
- 286+287/16 МГц та Cyrix 486DLC + Cyrix 387, 66 МГц -- працює все, окрім WiFi-емуляції NE2000. Звукову карту не випробовував.
  - Пробував різні порти та переривання, конфлікту немає. Ще спробую на інших АТ-системах.
- Також, працює Turbo Basic 1.10, який має проблеми з деякими емуляціями HDD.

MAC-адреса: ``28:CD:C1:11:6A:A1``.

|                                                                                                                                                                                                                                        ![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/mem_distr.jpg)                                                                                                                                                                                                                                        |
| :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| Карта пам'яті для XT та IBM PC 5150. Оскільки XT-IDE тут не використовується, та й інші плати не претендують на цю адресу, обрав як адресу EMS-кадру 0xC8000 -- це дозволило створити неперервний блок UMB розміром 80 Кб -- на жаль, у моїй практиці, софт, зазвичай не розпізнає, коли UMB поділено на кілька блоків і використовує лише один з них. Ремарка: візуалізація трішки незвична -- старша цифра адреси сегменту написана над відповідним сегментом. Тобто, перший x має адресу 0xA0000, а перший V -- [0xB800](https://indrekis2.blogspot.com/2019/04/0xb800.html). Для випробуваних AT, єдина різниця -- 4 M та 4 R в кінці, 64 Кб UMB. Вибачте за якість фото[^RPR]!|

[^RPR]: на жаль, пости забираються дуже помітну частку доступних сил, і намагання пост-фактум покращити фото часто відкладало публікацію на безмежність.

> Кумедний прояв: коли я забув оновити розмір UMB для AT-систем -- де 64 Кб, замість 80 Кб на Juko XT, з USE!UMBS все ніби працювало (тільки тест пам'яті виявляв проблему з останніми 16 Кб UMB), а ``lh edoskey`` і варіації -- жорстко зависали. А ось LastByte відмовлявся ініціалізуватися у такому випадку.


## Тести коректності

EMS пам'ять проходить тест в CheckIt 4.10 та CheckIt PRO 1.11, UMB -- під CheckIt PRO 1.11. Принаймні одним способом тестувався кожен різновид пам'яті на кожній з машин.

На Juko ST XT запускав, у різних комбінаціях налаштувань менеджерів пам'яті, (див. далі), Windows 3.00 та Word i Excel під ним -- працюють. Повііііільно... -- але не повільніше, ніж очікувано на XT.

| ![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/mmem_win_1.jpg ) | 
| :--------: |
| Juko ST XT, UMB + EMS. Щойно запущено Windows 3.00. |
| ![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/mmem_win_2.jpg ) | 
| Juko ST XT, UMB + EMS. Щойно запущено Excel 2.1. |
| ![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/mmem_win_3.jpg ) | 
| Juko ST XT, UMB + EMS. Не закриваючи Excel, запущено Word for Windows 1.0. |

# Експерименти з UMB 

Поекспериментувавши з апаратною картою пам'яті, вирішив повторити [свої експерименти]({% post_url /retrocomputing/ibm_pc_compat/2023-11-18-tandy_1400LT_1 %}#umb) з UMB і максимізацією вільної основної пам'яті на останній ще не охопленій своїй XT -- Juko ST XT. Покладаючись на той досвід, вирішив експериментувати з:

- USE!UMBS, щоб перемістити DOS в UMB, зберігаючи можливість використовувати EMS,
- LastByte -- відмовившись від можливості використовувати EMS, а EMS-кадром скористатися як звичайною  UMB -- це марнотратно, звичайно, але за певних умов більша пам'ять під 1 Мб могла бути важливішою.

> На Juko ST XT тестував максимально ретельно, на IBM PC 5150 -- поверхнево, на інших -- ретельно.

Можливо, інші утиліти, типу Quarterdeck QRAM з його DOS-UP.SYS, спробую іншого разу. 

> Важливо, що випробовував лише з емульованими дисководами і HDD -- проблема з DMA тут не може проявитися, з використанням реальних можуть бути проблеми від таких переміщень DOS в UMB.

Використовую MS DOS 6.22.

Підключаємо драйвер EMS, тоді можна скористатися USE!UMBS або LastByte + HighUMM, щоб надати підтримку UMB, а потім -- можемо використати DOSMAX, разом з його ShellMax (і запуском його ж з Autoexec.bat, щоб доперемістити командний інтерпретатор вверх). З останнім можна скористатися версією 1.7 -- вона підтримує ''чистий'' 8086, та 2.1 -- яка потребує 80186, або -- сумісний з ним V20. (Такий вибір базується на [попередніх експериментах]({% post_url /retrocomputing/ibm_pc_compat/2023-11-18-tandy_1400LT_1 %}#umb).)

Для кожної з цих конфігурацій дивлюся вільну пам'ять, за умови завантажених setver,  драйвера мишки (PMMouse) та eDOSKey.

## Особливості DOSXMAX 1.7

Успішно перемістити вверх може не все -- на дечому зависає. З того всього вивчив його опції:

- /R+ -- давати звіт; /P- -- не зупинятися, /N+ -- не зупинятися навіть у випадку помилок,
- /L+ -- залишити код DOS в основній пам'яті,
- /S+ -- залишити сегменти DOS в основній пам'яті,
- A0 -- залишити COMMAND.COM в основній пам'яті.
  - Що цікаво, якщо є помилка, наприклад, незнайома опція (спочатку я використав ``/report:-``, зрозумілу новішим версіям), драйвер може завантажитися, але неявно матиме на увазі /A0 (і може ще якісь опції) -- якийсь час мусив зневаджувати проблему, що драйвер зависав після виправлення помилки у опції, хоча щось переміщав у UMB навіть коли були помилки.

В цілому, з USE!UMBS максимальну частину DOS вдалося перемістити вверх з такими опціями:

```bat
DEVICE=c:\dosmax17\dosm86.exe /R+ /P- /N+ /A0
```

> Коли вирішив відтворити -- вже не вдалося, потрібно ще додати /S+ -- хоча я впевнений, що працювало і без. Щось ще змінилося, ймовірно...

Для LastByte довелося використати більш жорсткі опції:

```bat
DEVICE=c:\dosmax17\dosm86.exe /R+ /P- /N+ /A0 /S+
```

ShellMax працювати відмовляється, заміна dosm86.exe на dosmax.exe нічого не змінила (крім невеликого зменшення потреби в пам'яті).

DOSXMAX 2.1 всіх тих танців не  потребує, ShellMax працює, але -- потребує 80186/V20.

## Результати

Основної пам'яті: 655360 байт (640 Кб).
EMS без LastByte: 4,177,920 (4,080 Кб)
EMS з LastByte: 0.

|                       | Вільна основна пам'ять | Вільні UMB | Всього UMB | Всього під 1Мб[^ADST] |
| :-------------------: | :--------------------: | :--------: | :--------: | :-------------------: |
|      Mini[^MCG]       |        593,664         |     0      |     0      |        655,360        |
|      Norm             |        573,392         |     0      |     0      |        655,360        |
|       USE!UMBS        |        585,744         |   68,800   |   81,920   |        654,544        |
| USE!UMBS + DOSMAX 2.1 |        633,776         |   20,384   |   44,160   |        699,520        |
| USE!UMBS + DOSMAX 1.7 |        623,584         |   30,768   |   44,160   |        699,520        |
| USE!UMBS + DOSM86 1.7 |        623,552         |   30,768   |   44,160   |        699,520        |
| LastByte              |        585,968         |  129,712   |  142,800   |        798,160        |
| LastByte + DOSMAX 2.1 |        638,448         |   76,576   |  105,040   |        760,400        |
| LastByte + DOSMAX 1.7 |        623,808         |   91,680   |  105,040   |        760,400        |
| LastByte + DOSM86 1.7 |        623,776         |   91,680   |  105,040   |        760,400        |

Файли з сирим виводом ``MEM /C`` -- [тут](/retrocomputing/ibm_pc_compat/files/PicoMEM/mem_res.zip).

Видно, що DOSMAX себе "тихо" запихає в UMB. На свої потреби забирає з UMB 37,760.
DOSMAX 1.7 потребує на 32 байти більше, ніж DOSM86 з того ж пакету в основній пам'яті, це єдина виявлена відмінність.

Підсумовуючи:

- Найбільший об'єм основної пам'яті отримано для LastByte + DOSMAX 2.1, 638,448 байт (623.5 Кб).
  - Однак, при тому EMS недоступна. 
- За збереження EMS, USE!UMBS + DOSMAX 2.1 залишають 633,776 байт, (майже 619 Кб - 618.92 Кб).
- Без всіх цих фокусів -- 573,392, (майже 560 Кб).


Це скромніше, ніж в [Tandy 1400 LT]({% post_url /retrocomputing/ibm_pc_compat/2023-11-18-tandy_1400LT_1 %}#експерименти-та-підсумок), де можна скористатися пам'яттю над 640 Кб (відеокарта -- не VGA), чи [Compaq Portable III/386]({% post_url /retrocomputing/ibm_pc_compat/2024-01-22-Compaq_Portable_III_n_386_1 %}#386), де допомагає EMM386, але, все ж, непогано.

[^MCG]: Лише SetVer. Решта -- повний комплект резидентів та драйверів, описаний вище.

[^ADST]: Як це виглядає DOS 6.22 ``MEM /C``. 

| ![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/lb_1.jpg ) | 
| :--------: |
| LastByte + DOSMAX 2.1 -- ініціалізовано. Видно карту пам'яті і об'єм доступної UMD (128+16 = 144 Кб). |
| ![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/lb_2.jpg ) | 
| DOSMAX завантажує частини DOS в UMB. |
| ![](/retrocomputing/ibm_pc_compat/pics/PicoMEM/lb_3.jpg ) | 
| Дозавантаження системи. Для USE!UMBS процес буде виглядати схоже. |

## Файли конфігурації 

### CONFIG\.SYS 

У варіанті для 80 Кб UMB, (для 64 потрібно виправити адресу для USE!UMBS та розмір для LastByte):

```bat
[common]


[menu]
menuitem=clr, Minimal
menuitem=pm, PicoMEM
menuitem=pmn, PicoMEM + NET
menuitem=ub, PicoMEM + USEUMB
menuitem=ubdm, PicoMEM + USEUMB + DOSMAX
menuitem=ubdmn, PicoMEM + USEUMB + DOSMAX + NET
menuitem=lb, PicoMEM + lastbyte
menuitem=lbdm, PicoMEM + lastbyte + DOSMAX
menuitem=lbdmn, PicoMEM + lastbyte + DOSMAX + NET 

menudefault=pm,5

[clr]
DEVICE=c:\dos\setver.exe

[pm]
files=30
DEVICE=c:\PICOMEM\PMEMM.EXE /n

[pmn]
include=pm

rem DOSMAX 1.7 Options:
rem /R+ -- report; /P- -- no pause, /N+ -- no pause even on error
rem /L+ -- leave  dos code low,
rem /S+ -- leave DOS sub-segments low 
rem /A0 -- leave command.com low 
rem If option is wrong, implies /A0 (at least)
rem 
rem DOSMAX 2.1 has longer form: /report:+ and so on.

[ub]
include=pm
dos=high,umb
DEVICE=c:\umbs\use!umbs.sys E000-F3FF
DEVICEHIGH=c:\dos\setver.exe


[ubdm]
include=pm
dos=high,umb
device=c:\umbs\use!umbs.sys E000-F3FF

REM DOSMAX 2.1:
DEVICE=c:\dosmax21\dosmax.exe /R+ /P- /N+ 
SHELL =C:\dosmax21\shellmax.com c:\command.com /p
REM DOSMAX 1.7 maximal working conf (shellmax does not works):
REM DEVICE=c:\dosmax17\dosm86.exe /R+ /P- /N+ /A0 /S+
REM DEVICE=c:\dosmax17\dosmax.exe /R+ /P- /N+ /A0 /S+

DEVICEHIGH=c:\dos\setver.exe

REM lastbyte -- use EMS frame as a UMB. 

[ubdmn]
include=ubdm


[lb]
include=pm
dos=high,umb
DEVICE=c:\lastbyte\lastbyte.sys ? physical=lim3ems,noems dos=E000:80 name=ilya_tumanov key=200166d2 
REM exclude=c400:64 exclude=e400:112 dos=e000:80 -- examples
DEVICE=c:\lastbyte\highumm.sys

DEVICE=c:\lastbyte\highdrvr.sys c:\dos\setver.exe


[lbdm]
include=pm
dos=high,umb
DEVICE=c:\lastbyte\lastbyte.sys ? physical=lim3ems,noems dos=E000:80 name=ilya_tumanov key=200166d2 
REM exclude=c400:64 exclude=e400:112 dos=e000:80 -- examples
DEVICE=c:\lastbyte\highumm.sys

REM DOSMAX 2.1:
DEVICE=c:\dosmax21\dosmax.exe /R+ /P- /N+ 
SHELL =c:\dosmax21\shellmax.com c:\command.com /p

REM DOSMAX 1.7 maximal working conf (shellmax does not works):
REM DEVICE=c:\dosmax17\dosm86.exe /R+ /P- /N+ /A0 /S+
REM DEVICE=c:\dosmax17\dosmax.exe /R+ /P- /N+ /A0 /S+

DEVICE=c:\lastbyte\highdrvr.sys c:\dos\setver.exe


[lbdmn]
include=lbdm

```

### AUTOEXEC\.BAT 

```bat
@echo off
prompt $p$g
path c:\win300;c:\dos;c:\vc;c:\utils;c:\dn139;c:\
set temp=c:\tmp
set MTCPCFG=c:\mtcp\cur.cfg
REM Allow hotkeys for Ctrl-Shift-F1/F2 (PicoMEM info/change A:)
c:\picomem\pminit /k
rem c:\dos\smartdrv.exe /l /x /v

goto %config%

:clr
goto aft

:pm
c:\picomem\pmmouse
c:\edoskey\doskey.com
goto aft

:pmn
c:\picomem\pmmouse
c:\edoskey\doskey.com
call c:\ldne2000.bat
goto aft

:ub
lh c:\picomem\pmmouse
lh c:\edoskey\doskey.com
goto aft

:ubdm
REM Call dosmax to move command.com part (master env.) to UMB
REM c:\dosmax17\dosm86
REM c:\dosmax17\dosmax
c:\dosmax21\dosmax
lh c:\picomem\pmmouse
lh c:\edoskey\doskey.com
goto aft

:ubdmn
REM c:\dosmax17\dosm86
REM c:\dosmax17\dosmax
lh c:\picomem\pmmouse
lh c:\edoskey\doskey.com
call c:\ldne2000.bat
goto aft

:lb
c:\lastbyte\hightsr.exe c:\picomem\pmmouse
c:\lastbyte\hightsr.exe c:\edoskey\doskey.com
goto aft

:lbdm
REM Call dosmax to move command.com part (master env.) to UMB
REM c:\dosmax17\dosm86
REM c:\dosmax17\dosmax
c:\dosmax21\dosmax
c:\lastbyte\hightsr.exe c:\picomem\pmmouse
c:\lastbyte\hightsr.exe c:\edoskey\doskey.com
goto aft

:lbdmn
REM c:\dosmax17\dosm86
REM c:\dosmax17\dosmax
c:\dosmax21\dosmax
c:\lastbyte\hightsr.exe c:\picomem\pmmouse
c:\lastbyte\hightsr.exe c:\edoskey\doskey.com
call c:\ldne2000.bat
goto aft


:aft

lh vc
```

### LDNE2000\.BAT 

```bat
:LDNE2000
c:\PICOMEM\PM2000.COM 0x60
REM 0x60 2 0x260
set mtcpcfg=c:\mtcp\cur.cfg
C:\mtcp\dhcp.exe
```

# Посилання

- [PicoMEM](https://github.com/FreddyVRetro/ISA-PicoMEM) -- репозиторій на GitHub.
  - [Вирішення проблем](https://github.com/FreddyVRetro/ISA-PicoMEM/wiki/PicoMEM-FAQ)
- [PicoMEM Project : Pi Pico on an ISA Board](https://www.vogons.org/viewtopic.php?t=94320&hilit=update) на Vogons.
- [PicoMEM : Pi Pico on ISA, with full Memory and I/O bus access](https://forum.vcfed.org/index.php?threads/picomem-project-pi-pico-on-an-isa-board.1241199/) -- на VCFed.


# Виноски

