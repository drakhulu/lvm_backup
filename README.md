## Описание
Скрипт для бекапа LVM-томов. Используется для резервного копирования виртуальных машин (в моем случае KVM), располженных на LVM томах.

Данный скрипт используется в связке с системой резервного копирования Bacula и выполняется дважды.
Первый раз для, непосредственно, создания архива LVM-тома. Второй (уже после копирования на сервер Bacula) - для очистки каталога со временными данными.

## Использование
Скрипт принимает от 2 до 3 аргументов:

    1. *(обязательный)* Имя группы LVM томов
    2. *(обязательный)* Непосредственно имя LVM тома, который будем бекапить
    3. *(не обязательный)* ключевое слово **clean**

Дополнительный параметр **clean** нужен для очистки каталога (по умолчанию /mnt/backup) от уже созданных архивов.

### Переменные внутри скрипта.
Внутри скрипта используется переменная backupFolder (по умолчанию имеет значение /mnt/backup) с указанием пути каталога для сохранения создаваемого архива. Следует иметь ввиду, что объем свободного дискового пространства в этом каталоге должен быть не меньше, чем размер создаваемого архива с резервной копией. А размер архива, в свою очередь, зависит от используемого (занятого) дискового пространства внутри LVM-тома.

### Примеры использования
Создать архив LVM тома zabbix в группе томов vg_vm05.

    ./lvm_backup.sh vg_vm05 zabbix
  
Очистить все архивы из каталога со временными файлами (по умолчанию /mnt/backup), созданные для LVM тома srv-test01 в группе томов vg_vm07:

    ./lvm_backup.sh vg_vm07 srv-test01 clean

## Восстановление файла бекапа
Распаковка архива с резервной копией выполняется в заранее подготовленный LVM-том командой:

    gunzip -c backup_file.bak.gz | dd of=/dev/vg_vm01/my_lvm_name bs=8096
  
или с отображением прогресс бара:

    pv backup_file.bak.gz | gunzip | dd of=/dev/vg_vm01/my_lvm_name bs=8096

