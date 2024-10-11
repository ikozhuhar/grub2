### GRUB2. Загрузка системы в Linux

#### <a name='toc'>Содержание</a>

1. [Загрузчик GRUB2](#grub2)
2. [Включить отображение меню Grub](#entrymenugrub)
3. [Попасть в систему без пароля несколькими способами](#&&&&&&&&&&&&&)
4. [Установить систему с LVM, после чего переименовать VG](#&&&&&&&&&&)
5. [Дополнительные источники](#recommended_sources)

#### 1. [[⬆]](#toc) <a name='grub2'>Загрузчик GRUB2</a>

В каталоге `/etc/grub.d` хранятся шаблоны, определяющие настройки GRUB2. Также некоторые его параметры хранятся в файле `/etc/default/grub`. По шаблонам из /etc/grub.d и файлу `/etc/default/grub` программой `/usr/sbin/grubmkconfig` создается рабочий конфигурационный файл `/boot/grub/grub.cfg`, который по задумке разработчиков GRUВ2 вы не должен редактироваться вручную. 

Поэтому есть две стратегии настройки GRUB2. Первая заключается в непосредственном редактировании файла `/boot/grub/grub.cfg`,  вторая заключается в редактировании файлов из каталога `/etc/grub.d` и файла `/etc/default/grub`. После чего нужно будет ввести кoмaндy·grubmkconfig для создания файла `/boot/grub/grub.cfg` по заданным настройкам. 

Если вы просмотреть конфигурационный файл `/boot/grub/grub.cfg` то становится понятно, что программа `grub-mkconfig` собирает воедино вее файлы из каталога /etc/grub.d, настроек из `/etc/default/grub` и вносит все в общий конфигурационный файл `/boot/grub/grub.cfg`

#### Настройки файла /etc/default/grub
 
![image](https://github.com/user-attachments/assets/49a1d4dd-ddeb-47a9-a030-4227edd79764)


#### 2. [[⬆]](#toc) <a name='entrymenugrub'>Включить отображение меню Grub</a>

#####  Включить отображение меню Grub
Чтобы показать меню GRUB можно при загрузке зажать:

1. клавишу Shift (на компьютерах с BIOS)
2. клавишу Esc (для современных компьютеров с UEFI)

Если это не помогло, нужно отредактировать конфигурационный файл GRUB. Загрузитесь в Linux и включите отображение меню GRUB, добавив (раскомментировав) следующие строки в `/etc/default/grub`:
```
sudo nano /etc/default/grub
GRUB_TIMEOUT=20
```
![image](https://github.com/user-attachments/assets/f1cfc73a-84a9-4e7b-bbc1-fc762660a1a2)

Эта опция включает таймаут 20 секунд, которые должен ждать GRUB при загрузки на этапе выбора операционной системы.

Проверьте, есть ли в конфиг файле строка:
```
GRUB_TIMEOUT_STYLE=hidden
```

Если такая строка есть, закоментируйте ее или измените на
```
GRUB_TIMEOUT_STYLE=menu
```
После изменения настроек в файле grub нужно обновить его конфигурацию командой:
```
sudo update-grub
```
![image](https://github.com/user-attachments/assets/eb5225fd-4b8e-427d-89ce-5b329cd73f59)

Перезагрузите компьютер.

Если меню GRUB все еще не показывается, возможно GRUB не поддерживает видео режим вашего графической адаптера. Вы можете вместо графического GRUB меню отобразить консольное меню. Для этого добавьте в файл `etc/default/grub` строку:
```
GRUB_TERMINAL=console
```

Сохраните файл и обновите конфигурацию
```
sudo update-grub
```
Перезагрузитесь и убедитесь, что GRUB теперь показывает загрузочное меню.  

![image](https://github.com/user-attachments/assets/ae603526-9bbe-44c9-8394-4ccc384a38d7)



#### 2. [[⬆]](#toc) <a name='availability'>Попасть в систему без пароля несколькими способами</a>

#####  Способ 1. init=/bin/bash
Для получения доступа необходимо открыть консоль, выполнить reboot и при выборе ядра для загрузки нажать `e` - в данном контексте edit. Попадаем в окно, где мы можем изменить параметры загрузки. Находим строку linux, находим запись `ro` и меняем ее на `rw` и в конце строки дописываем `init=/bin/bash`. Далее жмем `сtrl-x` для загрузки системы. Также можно `ro` на этом шаге на менять, а загрузится в режиме Read-Only, а уже после загрузки системы можно ее перемонтировать командой `mount -o remount,rw /`.  

![image](https://github.com/user-attachments/assets/0574e83c-526c-4cef-aaa2-4640f9d699fa)


#####  Способ 2. Recovery mode
В меню загрузчика на первом уровне выбрать второй пункт (Advanced options…), далее загрузить пункт меню с указанием recovery mode в названии.  Получим меню режима восстановления.

![image](https://github.com/user-attachments/assets/1eedee3c-86aa-47cf-ac5a-77b820f6f0a3)

В этом меню сначала включаем поддержку сети (network) для того, чтобы файловая система перемонтировалась в режим read/write (либо это можно сделать вручную). Далее выбираем пункт root и попадаем в консоль с пользователем root. Если вы ранее устанавливали пароль для пользователя root (по умолчанию его нет), то необходимо его ввести.  В этой консоли можно производить любые манипуляции с системой.

#####  Способ 3. Консоль GRUB
Чтобы попасть в консоль GRUB, нужно нажать клавишу `C` во время отображения меню загрузки.

![image](https://github.com/user-attachments/assets/2338bab3-e4fe-4545-b5c4-9ceb4724f036)

Командой `ls` определяем на каком диске находится система. Вводим команду и получаем список всех дисков и разделов:

![image](https://github.com/user-attachments/assets/7320cb9b-f44d-449d-9307-7f8217a89f24)

Теперь нужно пройтись по всем дискам и разделам, в поисках двух файлов. Эти файлы начинаются на vmlinuz и initrd.img. В поиске этих файлов поможет та же команда ls. Скорее всего файлы будут лежать в корневой директории раздела '/'. Начинаем перебирать все диски и разделы:
```
ls (hd1,gpt2)/
```
Перебираем до тех пор, пока не найдём фалы vmlinuz и initrd.img. Верный результат будет выглядеть примерно так:
![image](https://github.com/user-attachments/assets/5d6c0dad-aba4-4b56-80e9-8cdf51d528d6)


##### Запускаем Linux

Теперь надо запустить Linux. Для загрузки системы необходимо ввести следующие команды:
```
set root=(hd1,gpt2)
linux /vmlinuz-4.4.0-53-generic root=/dev/sda1
initrd /initrd.img-4.4.0-53-generic
boot
```
В приведённом примере необходимо заменить все пути и названия файлов на свои.

Чтобы облегчить задачу по набору всех значков в именах файлов, можно время от времени нажимать TAB на клавиатуре. Консоль сама будет завершать названия файлов. К примеру, набрали из второй строки "linux /vm", затем нажали TAB, строчка сама дописалась до "linux /vmlinuz-4.4.0-53-generic".

Если при вводе вышеуказанных команд консоль не вернула никаких сообщений, то всё сделано правильно и загрузка начнётся после ввода `boot`.

> *ALERT! /dev/sda1 does not exist Dropping to shell!*

При загрузке система монтируется на определенный раздел, в который её устанавливали. К примеру, если установка происходила в /dev/sda1 надо смонтировать систему туда. Но если система была установлена не в /dev/sda1, то во время запуска система выдаст вышеуказанную ошибку. Для решение необходимо задать правильный раздел, потому что `/dev/sda1` в нашем случае не подходит. Для этого вводим команду:
```
blkid
```
Появится список всех смонтированных разделов и их адреса. Находим что-то похожее на `root`, переписываем команду и запускаем систему:
```
set root=(hd1,gpt2)
linux /vmlinuz-4.4.0-53-generic root=/dev/rootrootrootrootroot
initrd /initrd.img-4.4.0-53-generic
boot
```


#### 3. [[⬆]](#toc) <a name='availability'>Установить систему с LVM, после чего переименовать VG</a>

![image](https://github.com/user-attachments/assets/30e4b43d-32cd-4be0-95ed-362d05dc80c9)

![image](https://github.com/user-attachments/assets/c43116a6-b889-4c49-b0b1-408a8960e9dc)

Нас интересует вторая строка с именем Volume Group. Приступим к переименованию:  

![image](https://github.com/user-attachments/assets/9519a309-80a2-40c2-9b69-4c18a7faaadf)


##### Смотрим результат

![image](https://github.com/user-attachments/assets/d662325a-9bda-4197-a35d-a5d1283eb26d)


Далее правим `/boot/grub/grub.cfg`. Везде заменяем старое название VG на новое (в файле дефис меняется на два дефиса ubuntu--vg ubuntu--otus).
После чего можем перезагружаться и, если все сделано правильно, успешно грузимся с новым именем Volume Group и проверяем:

```
sudo nano /boot/grub/grub.cfg
```
![image](https://github.com/user-attachments/assets/90a9eed0-15cd-4069-b395-dd882179cf51)


#### 4. [[⬆]](#toc) <a name='recommended_sources'>Дополнительные источники</a>

1. [GRUB - загрузчик системы](https://help.ubuntu.ru/wiki/grub)
2. [GRUB консоль. Запускаем Linux](https://www.alexgur.ru/articles/2275/)
3. [Настройка загрузчика Grub](https://losst.pro/nastrojka-zagruzchika-grub)
4. [https://forum.ubuntu.ru/index.php?topic=74165.0](https://forum.ubuntu.ru/index.php?topic=74165.0) 
5. Весь Linux Для тех, кто хочет стать профессионалом, стр.311
