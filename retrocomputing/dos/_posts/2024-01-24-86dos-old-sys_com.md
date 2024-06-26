---
layout: post
title:  "SYS.COM з 86-DOS 0.11 та 0.34"
date:   2024-01-24 1:23:45 +0300
tags: [retrocomputing, IBM PC та сумісні, 86-DOS, SYS.COM]
# categories: [retrocomputing, IBM-PC-compat]
comments: true
published: true
excerpt_separator: <!--more-->
---

Насправді, ця стаття -- трішки обман. Все почалося з того, що до [86-DOS]({% post_url /retrocomputing/dos/2024-01-23-86dos-old-1 %}) я поліз, щоб глянути на їх SYS.COM, який багато раз дизасемблював для різних версій MS/PC-DOS. 

<style>body {text-align: justify}</style>

<!--more-->

Але виявилося, що версія з 86-DOS 0.34 повністю збігається з версією з 86-DOS 1.00, яку я вже дизасемблював: ["Аналіз SYS.COM з попередника, 86-DOS"](https://indrekis2.blogspot.com/2013/07/syscom-86-dos.html).

Версія з 0.11 відрізняється рівно на два байти, якщо глянути в код -- адреса буфера зсунута в 0.34 на байт вперед і в 0.11 бракує \$ в кінці стрічки "Bad drive specification"[^PCE]. 

[^PCE]: Як варіант, це може бути збій образу. Стикався з подібним -- маю оригінальні диски Amstrad 1640, вони читаються без помилок, але відрізняються жменею байтів сям і там від правильних. Не знаю, як це можливо -- контрольні суми секторів, ото все... Але цей варіант не видається мені ймовірним. 

Тобто, відносно 0.11, в 0.34 виправлено баг з виводом повідомлення про помилку, інших змін не було.

Більш цікавим виявився вміст після кінця цих файлів, але в межах останнього сектора (на жаль, не кластера)[^DDD].

[^DDD]: Розмір файлу ці ОС зберігають лише з точністю до сектора, хоч технічно їх FAT може зберігати з точністю до байту. Див. також ["Misconceptions on Top of Misconceptions"](https://www.os2museum.com/wp/misconceptions-on-top-of-misconceptions/): "*For executable programs, this was not an issue. When loading a program, CP/M or 86-DOS loaded a certain number of records/sectors. If there was some junk in the last record (very likely), it didn’t matter because it was never executed as code and may have been overwritten by data.*".

| ![](/retrocomputing/ibm_pc_compat/pics/86DOS-1/slack_sys_com_011.png) |
|:--------------------------------------------------:|
| Останній сектор SYS.COM з 86-DOS 0.11. Червона галочка -- місце, де мав бути символ долара. Нічого після нього не мало б використовуватися в коді -- це просто вміст пам'яті, який випадково потрапив на диск. |

| ![](/retrocomputing/ibm_pc_compat/pics/86DOS-1/slack_sys_com_034.png) |
|:--------------------------------------------------:|
| Останній сектор SYS.COM з 86-DOS 0.34. Виділено останній байт, використаний в коді. |

| ![](/retrocomputing/ibm_pc_compat/pics/86DOS-1/slack_sys_com_100.png) |
|:--------------------------------------------------:|
| Останній сектор SYS.COM з 86-DOS 1.00. Виділено останній байт, використаний в коді. (На жаль, для 1.10 нічого цікавого в останньому секторі немає. |

Завантажити відповідні файли можна [тут](/retrocomputing/ibm_pc_compat/files/86DOS-1/86-DOS-SYS.COM_011_034_100_110.ZIP). 

# Виноски