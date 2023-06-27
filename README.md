# Домашнее задание к занятию 3 "Резервное копирование" - Лебедев Алексей, fops-10



---

### Задание 1  

- Составьте команду rsync, которая позволяет создавать зеркальную копию домашней директории пользователя в директорию /tmp/backup
- Необходимо исключить из синхронизации все директории, начинающиеся с точки (скрытые)
- Необходимо сделать так, чтобы rsync подсчитывал хэш-суммы для всех файлов, даже если их время модификации и размер идентичны в источнике и приемнике.
- На проверку направить скриншот с командой и результатом ее выполнения

### Решение:    

- Напишем команду rsync, удовлетворяющую условиям нашей задачи:

```
rsync -avPh --checksum --exclude=".*" . /tmp/backup
```

где:  

- a (или --archive): выполняет рекурсивную копию и сохраняет атрибуты файлов и директорий;
- v (или --verbose): выводит подробную информацию о процессе синхронизации;
- P (или --progress): отображает прогресс передачи файлов и позволяет возобновлять прерванные операции;
- h (или --human-readable): выводит размеры файлов в human-readable формате
- --checksum: вычисляет хэш-суммы для всех файлов и сравнивает их между источником и приемником.
- --exclude=".*": исключает все директории, начинающиеся с точки

Проверим:  


  
  

![ex1](img/rsync1.JPG)    


### Задание 2   

- Написать скрипт и настроить задачу на регулярное резервное копирование домашней директории пользователя с помощью rsync и cron.
- Резервная копия должна быть полностью зеркальной
- Резервная копия должна создаваться раз в день, в системном логе должна появляться запись об успешном или неуспешном выполнении операции
- Резервная копия размещается локально, в директории /tmp/backup
- На проверку направить файл crontab и скриншот с результатом работы утилиты.

  ### Решение:

- Напишем bash-скрипт, который будет удовлетворять условиям нашего задания:
 
 ```
#!/bin/bash

# Путь к домашней директории
source_dir="/home/vagrant"

# Путь к директории резервной копии
backup_dir="/tmp/backup"

# Выполняем rsync для создания зеркальной копии
rsync -avPh --checksum "$source_dir" "$backup_dir"

# Проверяем код возврата rsync и записываем соответствующее сообщение в системный журнал
if [ $? -eq 0 ]; then
    logger "Резервная копия домашней директории успешно создана"
else
    logger "Ошибка при создании резервной копии домашней директории"
fi
```

- Установим планировщик, зайдем в **crontab -e** и пропишем:

```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command


58 13 * * * /home/vagrant/backup.sh
```

Что ж. Ждем 13.58 и смотрим логи:  


  
