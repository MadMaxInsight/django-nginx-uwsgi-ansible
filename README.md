# Концепция работы 

Концепция работы в GP следующая: разработчик устанавливает локально на своем компьютере копию "боевого" сайта заказчика на виртуальном сервере с помощью VirtualBox, и взаимодействует с ней посредством протокола SFTP как с полноценным удаленным сервером.
Для этого настраивается IDE (тут рассмотрен пример настройки VS Code) а обмен между "боевым" сайтом заказчика и локальным сервером происходит через Git. Ниже в разделе "Разворачивание Vagrant Box" и в "Перенос проекта с "боевого" сайта" рассмотрен порядок действий для 

# Установка

## Подготовка к разворачиванию Vagrant Box

> **Важно:** перед началом установки:
Если Vagrant будет устанавливаться на OS Windows, нужно указывать местоположение для его установки с отсутсвием в пути кириллицы. Для проекта так же нужно указать путь без кириллицы, иначе образ просто не соберется!

Скачайте и установите VirtualBox и Vagrant для своей операционной системы по ссылкам в описании.

Желательно хранить на локальном компьютере по одному образу виртуальных машин (далее - боксы) для каждого типа проектов в зависимости от CMS или фреймворка, на котором работает или будет делаться клиентский сайт. Поэтому предлагаю создавать следующую стуркутуру:

F:\vagrant_projects\
  .\bitrix\
  .\wordpress\
  .\opencart\
  .\dajngo\
  .\react\
  и т.д.

Где каждая директория в папке vagrant_projects будет называться по типу CMS или фреймворка.

## Разворачивание Vagrant Box

> **Важно:** перед началом выполнения действий:
Включите VPN, иначе пакеты внутри vagrant-инструкций не поставятся!  

После того как файловая структура для хранение боксов будет создана, необходимые программы и плагины установлены, открываем терминал по адресу нужной директории в зависимотсти от типа проекта и выполняем следующие действия:

  1. Скачивайте репозиторий командой `git clone`
  2. После скачивания репозитория желательно удалить все лищнее (.git, README.md, LICENSE), кроме содержимого папки ./vagrant
  3. Перейдите в папку ./vagrant командой `cd /vagrant`
  4. При первом запуске установите плагин "vagrant-reload" с помощью команды `vagrant plugin install vagrant-reload`, а так же плагин "vagrant-vbguest" командой `vagrant plugin install vagrant-vbguest --plugin-version 0.21`
  5. После установки плагинов выполните команду `vagrant up` и дождитесь окончания установки, но по идее появится ошибка "Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available.", которая сигналищирует о том что нужно подключить гостевой образ на наш бокс.
  6. Для этого нам необходимо подключится к боксу командой `vagrant ssh`, в результате которой вы должны увидеть что-то типа: "[vagrant@bitrix ~]$" 
  7. Далее выполняем в боксе следующую команду: `sudo /opt/VBoxGuestAdditions-6.1.32/init/vboxadd setup`	
  8. Дадее выходим с бокса командой: `exit`
  9. Перезагружаем бокс - `vagrant reload`
  10. Устанавливаем на свой компьютер Аnsible и все сопутсвующие тулзы командами: `pip3 install ansible` и `pip3 install openshift`
  11. В случае успешной установки нам нужно запустить Аnsible из директории с файлом site.yml внутри командой: `ansible-playbook -i inventory.ini site.yml --key-file "path/to/private_key"` , где private_key - это путь из параметра "IdentityFile" вывода команды `vagrant ssh-config`
  12. Если Аnsible выполнился без ошибок делательно перезагрузить бокс `vagrant reload` ( или командами `vagrant halt` и  `vagrant up`)
  9. Переходим к разделу "Перенос проекта с "боевого" сайта"

## Перенос проекта с "боевого" сайта

в разработке...





### Deploy Django and Postgres on Nginx and uWSGI
We are using Vagrant and Ansible to deploy Django application on Vagrant box but you can reuse same ansible playbook to deploy on any other Ubuntu server 14.04. We are using Python virtualenv to deploy Django application.

Nginx is acting as a reverse proxy, handling `/static/` and `/media/` contents.

uWSGI is serving Django application


####Enviroment
```
1. Vagrant=1.8
2. Ansible=2.1
3. Ubuntu Server=14.04 trusty
4. Python=2.7
5. Django=1.10
6. Postgresql=9.4
7. Nginx
8. uWSGI
```

####NOTE:
1. Update `Vagrantfile` to change your synced_folders.
```
config.vm.synced_folder "./mysite", "/var/www/mysite"
```
2. Update `inventory.ini` to replace your server IP or host name instead of `web`
3. Update `shared_vars.main`, `/roles/setup/vars/main.yml`, `/roles/deploy/vars/main.yml` and `/roles/setup/templates`
 to make your desired changes i.e http port, django settings module, project dir, Nginx and uWSGI logs etc


####Using Vagrant
```
# Switch to directory and run command to create machine and run provisions
vagrant up

# run provisions if machine is already created
vagrant provisions

```


####Using Ansible directly
Switch to directory and run command to run provisions on machine listed in inventory.ini
```
ansible-playbook -i inventory.ini site.yml
```
