# Домашнее задание к занятию «`Защита хоста`» - `Яковлев Артем`

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

---

## Задание 1

1. Установите **eCryptfs**.
2. Добавьте пользователя cryptouser.
3. Зашифруйте домашний каталог пользователя с помощью eCryptfs.


*В качестве ответа  пришлите снимки экрана домашнего каталога пользователя с исходными и зашифрованными данными.*  

```bash
sudo apt update
sudo apt install -y ecryptfs-utils

#sudo adduser --encrypt-home cryptouser
sudo adduser cryptouser
sudo usermod -aG sudo cryptouser

su - cryptouser
pwd
touch readmefile
#ls -al /home/cryptouser
ls -al ~
exit

sudo ls -al /home/cryptouser
sudo ecryptfs-migrate-home -u cryptouser #Миграция домашнего каталога пользователя cryptouser
sudo ls -al /home/cryptouser

ecryptfs-unwrap-passphrase #Информация для восстановления
```
**Скриншот выполнения ls -al /home/cryptouser до шифрования**
![Скриншот выполнения ls -al /home/cryptouser до шифрования](https://github.com/temagraf/host_protect/blob/main/img/13-2-1-1.png "ССкриншот выполнения ls -al /home/cryptouser до шифрования")
**Скриншот выполнения ls -al /home/cryptouser после шифрования**
![Скриншот выполнения ls -al /home/cryptouser после шифрования](https://github.com/temagraf/host_protect/blob/main/img/13-2-1-2.png "Скриншот выполнения ls -al /home/cryptouser после шифрования")

---

## Задание 2

1. Установите поддержку **LUKS**.
2. Создайте небольшой раздел, например, 100 Мб.
3. Зашифруйте созданный раздел с помощью LUKS.

*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*

```bash
apt list gparted cryptsetup #LUKS (gparted) и cryptsetup установлены по умолчанию
#sudo apt install -y gparted cryptsetup
cryptsetup --version

sudo fdisk -l #Определяем подключенную флешку
mount #Если флешка подмонтирована - отключаем
sudo umount /dev/sdb4 
sudo cryptsetup -y -v --type luks2 luksFormat /dev/sdb4 #Подготавливаем раздел (luksFormat)
sudo cryptsetup luksOpen /dev/sdb4 disk #Открываем раздел
ls /dev/mapper/disk

sudo dd if=/dev/zero of=/dev/mapper/disk
sudo mkfs.ext4 /dev/mapper/disk

mkdir .secret 
sudo mount /dev/mapper/disk .secret/ #Монтируем раздел

sudo umount .secret
sudo cryptsetup luksClose disk #Закрываем раздел
```

**Скриншот поэтапного выполнения**
![Скриншот поэтапного выполнения](https://github.com/temagraf/host_protect/blob/main/img/13-2-2-1.png "Скриншот поэтапного выполнения")


---

## Задание 3 *

1. Установите **apparmor**.
2. Повторите эксперимент, указанный в лекции.
3. Отключите (удалите) apparmor.


*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*

```bash
sudo apt list apparmor-profiles apparmor-utils apparmor-profiles-extra #apparmor уже установлен
#sudo apt install -y apparmor-profiles apparmor-utils apparmor-profiles-extra
sudo aa-status
#sudo apparmor_status
sudo systemctl status apparmor.service

#sudo service apparmor stop
sudo systemctl stop apparmor.service
sudo aa-teardown

sudo cp /usr/bin/man /usr/bin/man1
sudo cp /bin/ping /usr/bin/man
sudo getcap $(which ping)
sudo getcap $(which man)
sudo setcap cap_net_raw+ep $(which man)

ping -c 1 localhost
man -c 1 localhost
sudo aa-enforce man
man -c 1 localhost

sudo mv /usr/bin/man1 /usr/bin/man
```
**Скриншот поэтапного выполнения**
![Скриншот поэтапного выполнения](https://github.com/temagraf/host_protect/blob/main/img/13-2-3-1.png "Скриншот поэтапного выполнения")
**Скриншот поэтапного выполнения (продолжение)**
![Скриншот поэтапного выполнения](https://github.com/temagraf/host_protect/blob/main/img/13-2-3-2.png "Скриншот поэтапного выполнения")
**Скриншот поэтапного выполнения (продолжение)**
![Скриншот поэтапного выполнения](https://github.com/temagraf/host_protect/blob/main/img/13-2-3-3.png "Скриншот поэтапного выполнения")
---
