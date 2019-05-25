**Отчет по лабораторной работе №2**

<li>Задание №1</li>

В итоге проделанного задания мы создали виртуальную машину с дисками ssd1 и ssd2, настроили LVM(Logical Volume Manager), RAID.

<li>Задание №2</li>

6) Копируем таблицу разделов со старого диска на новый: `sfdisk -d /dev/sda | sfdisk /dev/sdb

 Добавляем в RAID-массив новый диск

![RAID](https://github.com/shaman7296/git/blob/master/lab2/image/RAID.png?raw=true)

Cинхронизация разделов, не входящих в RAID, копируя с "живого" диска на новый: `dd if=/dev/sda1 of=/dev/sdb1`

В итоге проделанного задания мы проэмулировали отказ одного из дисков, удалили диск ssd1, сохранён диск ssd2, добавлен диск ssd3.

<li>Задание №3</li>

4-5) Добавили один новый диск ssd4

![DISK](https://github.com/shaman7296/git/blob/master/lab2/image/DISK.png?raw=true)

 Копируем файловую таблицу со старого диска на новый: `sfdisk -d /dev/sda | sfdisk /dev/sdb`

При выводе мы замечаем, что в новом диске sdb появились разделы: sdb1, sdb2.

Устанавливаем загрузчик на новый диск sdb: `grub-install /dev/sdb` - это загрузчик, который загружает нашу операционную систему, и он нам нужен на новом диске после удаления старого.

В итоге у нас установлен новый RAID-массив md63, проверяем при помощи команды `cat /proc/mdstat`

![RAID1](https://github.com/shaman7296/git/blob/master/lab2/image/RAID1.png?raw=true)

6) Настроили LVM(Logical Volume Manager)

Создаём новый физический том, включив в него ранее созданный RAID-массив: `pvcreate /dev/md63`

![md63](https://github.com/shaman7296/git/blob/master/lab2/image/md63.png?raw=true)

LV var,log,root находятся на диске sda:

![LV](https://github.com/shaman7296/git/blob/master/lab2/image/LV.png?raw=true)

Теперь все данные находятся на одном диске:

![disk](https://github.com/shaman7296/git/blob/master/lab2/image/disk.png?raw=true)

Изменили Volume Group, удалив из него диск старого raid:

![VG](https://github.com/shaman7296/git/blob/master/lab2/image/VG.png?raw=true)

13-15) Изменили размер второго раздела диска ssd5 (sdb) и ssd4 (sda)

![razmer](https://github.com/shaman7296/git/blob/master/lab2/image/razmer.png?raw=true)

Получили sda2 и sdb2 разделы, которые имеют размер > чем размер RAID-устройство.

16) Расширяем размер RAID. увеличили объём памяти ssd4, ssd5.

18) Завершили миграцию основного массива на новые диски, работа с ssd4 и ssd5 закончена.

![mg](https://github.com/shaman7296/git/blob/master/lab2/image/mg.png?raw=true)

19) Перемещаем `/var/log` на новые диски, для этого создаем новый RAID-массив, создаём логический том:

![var](https://github.com/shaman7296/git/blob/master/lab2/image/var.png?raw=true)

21) Правим `/etc/fstab`

![etc](https://github.com/shaman7296/git/blob/master/lab2/image/etc.png?raw=true)

22) Изменяем таблицу разделов командой: `resize2fs`

Перезагружаем систему, всё работает. Что было сделано: замена дисков на лету, с добавлением новых дисков и переносом разделов.

