# Домашнее задание к занятию "Процессы, управление процессами" - Александр Дружбин 

### Задание 1

Рассмотрим загрузку данных и многопоточность. В описанных ниже ситуациях поможет ли использование нескольких потоков для скачивания уменьшить время общей загрузки?

1. 100 файлов на разных Web-серверах, суммарным объёмом 10 Гбайт, через подключение со скоростью 1Мбит\с;
2. 100 файлов на разных Web-серверах, суммарным объёмом 10 Гбайт, через подключение со скоростью 10 Гбит\с;
3. 1 файл объёмом 10 Гбайт находящийся в торрентах;
4. 1 файл объёмом 10 Гбайт находящийся на FTP-сервере;
5. 10 файлов объёмом по 1 Гб находящихся в общей папке компьютера секретаря.

*Приведите ответ для каждого случай в свободной форме (лучше использовать один поток скачивания, несколько, всё равно) со своим комментарием.*

### Решение

1. Лучше использовать **один поток** скачивания т.к. я упрусь в ограничение в 1Мбит\с, которое будет распределено между всеми потоками, и процесс закачки застопорится.
2. Можно использовать **несколько потоков** т.к. скорость закачки позволяет.
3. Думаю, можно использовать **несколько потоков**: торрент-файл скачивается небольшими фрагментами и потом, видимо, собирается в единое целое; соответственно, если каждый поток будет параллельно скачивать разные фрагменты (видимо с разных сидов), общая скорость закачки возрастёт.
4. Лучше использовать **один поток** скачивания т.к. пока не вижу плюсов в многопоточности. С несколькими потоками придётся создавать несколько подключений к FTP серверу, а там может стоять лимит на подключения, да и, судя по гуглу, мы всё равно упрёмся в пропускную способность FTP сервера. Да и файл всего один и будет скачиваться от начала и до конца, вряд ли FTP протокол позволит разбить его между потоками.
5. Если папка общая, то **зачем** и куда скачивать? Либо речь идёт о пользователях на одном ПК и тогда смысла нет, либо это общая папка на облаке, тогда, возможно есть смысл в многопоточности, чтобы параллельно качать несколько файлов.

---

**Задание 1.1**

Попробуйте высказать предположение о **количестве потоков** скачивания, для случаев приведенных выше, если загрузка данных происходит на следующие системы:

- Компьютер Windows 10 64-bit\ i5-xxxx \16 Gb\ 2 TB HDD
- Компьютер Windows 10 32-bit\ i7-xxxx\ 8 Gb\ 2 TB HDD
- Ноутбук Windows 10 64-bit\ i7-xxxx\ 32 Gb\ 500 GB HDD
- Ноутбук Windows 10 64-bit\ i7-xxxx\ 32 Gb\ 2 TB HDD
- Компьютер Windows 8.1 32-bit\ i3-xxxx\ 8 Gb\ 1 TB SSD
- Компьютер Windows 10 64-bit\ i3-xxxx\ 8 Gb\ 1 TB HDD (RAID)

*Необязательно рассматривать все возможные комбинации, достаточно описать своими словами отличия.*

**Примечания:**

1) другими запущенными процессами на компьютерах можно пренебречь;

2) производительность CPU: i7 > i5 > i3.

### Решение

Попробую в общих чертах. Если процесс запущен на 32 разрядной операционной системе, то максимум ему система сможет выделить 2^32 = 4GB памяти. Если же это 64 разрядная операционная система, то теоретически процесс сможет получить 2^48=256TB памяти. Т.е. в теории количество потоков скачивания на 64 разрядных системах может быть больше. Дальше кол-во потоков зависит от производительности CPU (i7 > i5 > i3), следующее ограничение - кол-во RAM (больше - лучше), затем учитываем быстроту физической памяти (SDD быстрее HDD). 

Т.е. лучше всех с многопоточностью справится *Ноутбук Windows 10 64-bit\ i7-xxxx\ 32 Gb\ 500 GB HDD*, а хуже всех *Компьютер Windows 8.1 32-bit\ i3-xxxx\ 8 Gb\ 1 TB SSD*

**Задание 1.2**

Какой из приведенных выше компьютеров постоянно "тормозит" и почему?

### Решение

* *Компьютер Windows 10 64-bit\ i3-xxxx\ 8 Gb\ 1 TB HDD (RAID)* - Core i3 отвечает за сравнительно низкую производительность CPU + мало RAM + накопители объединены в RAID, что снижает производительность операций записи.
---

### Задание 2

Объясните, что делает команда:

`ps -aux | grep root | wc -l  >> root`

*Ответ напишите в свободной форме.*

### Решение

Команда по идее должна считать кол-во процессов, запущенных из-под root и выводить их число в файл root, не перезаписывая при этом то, что уже было в этом файле. Т.е. если бы она соответствовала своему назначению, то при запуске из крона раз в пять минут получилась бы статистика кол-ва запущенных из-под рута процессов. 

Проблема в том, что слово *root* может содержаться в других колонках вывода *ps aux*, а значит в выборку попадут и такие процессы, которые запущены не из-под рута, но, например, оперируют файлами содержащими частицу root в названии.

Короче команда просто считает кол-во строк вывода ps aux, в которых упоминается root в любом виде, и дописывает его в файл.

---

### Задание 3

Напишите команду, которая выводит все запущенные процессы пользователя root в файл *"user_root_ps"*.

### Решение

`ps -U root > user_root_p` или `ps -U root >> user_root_p`

---

### Задание 4

Начинающий администратор захотел вывести все запущенные процессы пользователя с логином "2" в файл *"user_2_ps"*.

Для этого он набрал команду:

`ps -U 2> user_2_ps`

Затем, он аналогично повторил для пользователя с логином "5" вывод в файл "user_5_ps":

`ps -U 5> user_5_ps`

**Вопрос:** 

Почему вывод этих команд и содержимое файлов сильно отличаются друг от друга?  Как должны выглядеть правильные команды?

### Решение

Вывод выдаётся ошибочный: неясно, куда отнести 2 или 5 - к имени пользователя `ps -U 2` или к потоку вывода `2>`, система относит его к потоку вывода. Поскольку ключ -U требует после себя имени юзера, а его нет, сама команда выдаёт ошибку. Нужно поставить пробел: `ps -U 2 > user_2_ps` или `ps -U 5 > user_5_ps`



