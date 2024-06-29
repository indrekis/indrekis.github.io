---
layout: post
title:  "Звідна таблиця версій DOS -- work in progress"
date:   2024-06-26 1:23:45 +0300
tags: [retrocomputing, IBM PC та сумісні, 86-DOS]
# categories: [retrocomputing, IBM-PC-compat]
comments: true
published: true
excerpt_separator: <!--more-->
---


- [Таблиця]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#таблиця)
- [Джерела]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#джерела)
  - [Версії та їх дати]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#версії-та-їх-дати)
  - [Підтримка носіїв]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#підтримка-носіїв)
  - [Потреба в пам'яті]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#потреба-в-памяті)
- [Джерело образів]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#джерело-образів)
  - [Winworldpc]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#winworldpc)
  - [PCjs]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#pcjs)
  - [Betaarchive]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#betaarchive)
  - [Old-dos]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#old-dos)
  - [Інші]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#інші)
- [Експерименти]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#експерименти)
  - [MS DOS та PC DOS 3.20]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#ms-dos-та-pc-dos-320)
  - [Multitasking DOS 4.00]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#multitasking-dos-400)
- [Виноски]({% post_url /retrocomputing/dos/2024-06-23-DOS_versions_table %}#виноски)

Поки писав про [DOS 4.00]({% post_url /retrocomputing/dos/2024-04-28-DOS40x-source-release %}), [Tandy 1400LT]({% post_url /retrocomputing/ibm_pc_compat/2023-11-18-tandy_1400LT_1 %}) тощо, регулярно мусив уточнювати всілякі подробиці щодо можливостей різних версій DOS. При чому, в різних джерелах є різнобій. То вирішив зібрати все в одному місці і потроху уточнювати. 

<style>body {text-align: justify}</style>

<!--more-->

# Таблиця 

Таблиця ще дуже сира -- частина даних невірна чи неточна, також поки не відображено багато нюансів, наприклад, що підтримка кількох розділів в MBR з'явилася далеко не зразу. 

Знаходиться тут: ''[DOS Versions parameters](https://docs.google.com/spreadsheets/d/18YBM-N-yh463Ntpm7CeGzXE-3sEi2uB02RMtvfaQRMw/edit?gid=0#gid=0)''.

DR-DOS і варіанти (Concurrent DOS, DOS Plus, Novell DOS 7 тощо), PTS-DOS тощо -- планую додати з часом.

Як врахувати OEM версії (та й те, що до 3.20 retail версій від Microsoft не було) -- поки обдумую. І, гарантовано, включатимуться лише обрані -- повний список буде величезним.

Не-англомовні версії поки не розглядатиму.

Коментувати може будь-хто[^CP] -- якщо бачите помилки, або зразу знаєте і виправлення   -- будь ласка, залиште коментар. 

[^CP]: Звичайно, у випадку флуду або зловживань, буду змушений заборонити, але поки сподіваюся -- до цього не дійде. 


# Джерела

## Версії та їх дати 

Список версій і дати виходу поки брав з Вікіпедії:

- https://en.wikipedia.org/wiki/IBM_PC_DOS
- https://en.wikipedia.org/wiki/Comparison_of_DOS_operating_systems
- https://en.wikipedia.org/wiki/MS-DOS
- https://en.wikipedia.org/wiki/Timeline_of_DOS_operating_systems
- https://www.betaarchive.com/wiki/index.php/MS-DOS -- список бета-версій, хоча  на них поки не планую зупинятися, крім особливих випадків.

Там є багато помилок і плутанини, особливо -- з релізами. Це не говорячи про певну умовність дати релізу OEM-версій MS DOS.

Надійніше можна встановити за:

- прес-релізами і рекламі в тогочасних журналах,
- датах найновіших файлах в дистрибутивах.

Думаю, це слід вважати двома різними датами, де друга даватиме обмеження знизу на дату релізу. Хоча, з мовчазними виправленнями часу PC DOS 4.01, це теж неоднозначно. Також, до автентичності і версій образів можуть бути запитання. 

## Підтримка носіїв 

Джерелом є:

- Первинно -- статті на вікіпедії, згадані вище.
- Далі -- прес-релізи та безпосередні експерименти в емуляторах, в окремих випадках -- на ''залізі''. 

## Потреба в пам'яті

Початковий список потреб в пам'яті[^FD4] взяв з ''[Which DOS Version Is The Least Memory Hungry?](https://ctrl-alt-rees.com/2021-01-06-which-dos-version-uses-the-least-memory.html)''. Методологія наступна: завантажується DOS без CONFIG.SYS чи AUTOEXEC.BAT, зберігається вивід CHKDSK. 

Також, планую доповнити виводом MEM, для версій, де ця утиліта є та, можливо, ще CheckIt абощо.

[^FD4]: Це було мені потрібно під час спроб скомпілювати DOS 4.0x сучасними періоду її розробки версіями. 

# Джерело образів

## Winworldpc

Головним поки вважаю https://winworldpc.com. Зокрема: 

- https://winworldpc.com/product/pc-dos/1x
- https://winworldpc.com/product/ms-dos/1x
- https://winworldpc.com/product/86-dos/100

## PCjs

https://www.pcjs.org/ містить великий архів образів. 

## Betaarchive

Другим -- відповідна секція [betaarchive](https://www.betaarchive.com/database/browse.php?cat_section=Abandonware&cat_category=Operating+Systems&cat_platform=PC).

## Old-dos

Останнім є [Old-dos.ru](http://old-dos.ru/index.php?page=files&mode=files&do=list&cat=41) -- якість там гірша, але теж не зовсім погана. 

## Інші

Також, всіляка екзотика може походити з інших джерел: 

- https://archive.org 
- https://vetusware.com/ -- тяжкий, м'яко кажучи, сайт, але іноді дещо бувало тільки там.

# Експерименти

Документуватиму експерименти в цьому розділі -- щоб не довелося повторювати.

## MS DOS та PC DOS 3.20

Окрім OEM-варіантів існував також ''Shrink wrap'' -- для малих постачальників комп'ютерів, які виготовляли клони. 

Образи взяв тут, вони збігаються:

- https://archive.org/details/msdos32iflp 
- https://winworldpc.com/product/ms-dos/320 -- також, багато-багато OEM варіантів, але вони старіші. 

В образах MS DOS всі файли мають дату 07-07-1986. PC DOS 3.20 доступний у двох варіантах, 3.5"/720 Кб, з датою файлів 30-12-1985 та 5.25"/2x360 Кб, де всі файли мають ту ж дату, окрім  BASIC\.COM та BASICA\.COM, у яких: 21-02-1986.

За замовчуванням, вважаю, що підтримка засобів -- однакова в обох випадках, хоча відмінності фіксуватиму.

> Образ крашить PCem, якщо використати 286 процесор -- з таким стикаюся вперше, тому використовую 286. Також, PCem не надто точно підтримує примітивні диски, тому перевірити в ньому роботу з ''примітивними'' FDD -- меншими, ніж 1.2/1.44 Мб, важко. Експерименти з mess -- в майбутньому.

MS DOS:

- FDISK підтримує лише один розділ, але не має проблеми з кількома HDD. 
- Створити великий розділ (250 Мб) FDISK зміг, але форматувати його FORMAT не вміє -- що й очікувалося б. Отриманий розділ -- коректний, з точки зору сучасніших DOS. 
- Після форматування диску, розміром 32 Мб, FORMAT видає помилку, після того решта системи працює нестабільно -- читання дисків, включаючи A:, видає помилки, але отриманий диск доступний. Однак, завантажитися з нього не вдається.
- Диск, розміром 31 Мб нормально форматується і працює. 
- Якщо надати диск з кількома розділами (Primary + extended), створений новішими DOS, все рівно бачить тільки Primary.
- FORMAT копіює і COMMAND.COM.
- Старий вміст MBR (і, може й boot) може збивати з пантелику утиліти -- одного разу довелося чистити, перш ніж вдалося продовжити.

PC DOS:

- Форматує і 32 Мб, але теж не вміє з нього завантажитися. 

| ![](/retrocomputing/dos/pics/DOSTable/MS-DOS-3.20-fdisk01.png) |
| :-------------: |
| Створення диску, MS DOS 3.20. |
| ![](/retrocomputing/dos/pics/DOSTable/MS-DOS-3.20-format01.png) |
| Форматування диску на 32 Мб, MS DOS 3.20 -- видно повідомлення про помилку.  |
| ![](/retrocomputing/dos/pics/DOSTable/PC-DOS-3.20-format01.png) |
| Форматування диску на 32 Мб, PC DOS 3.20.  |

## Multitasking DOS 4.00

Згідно [''Who Knew What When''](http://www.os2museum.com/wp/who-knew-what-when/), сира версія, [джерельні тексти якої викладено на Git]({% post_url /retrocomputing/dos/2024-04-28-DOS40x-source-release %}#компіляція-multitasking-european-ms-dos-40), підтримує лише 5.25'' 360 Кб -- через захардкоджену швидкість передачі даних. 

# Виноски

