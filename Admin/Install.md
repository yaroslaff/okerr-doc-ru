# Установка собственного сервера okerr
## Физический сервер или виртуальный?
Okerr - достаточно большой и сложный проект: веб-приложение (apache/uwsgi), база данный, почтовый сервер, брокер сообщений, несколько systemd демонов okerr. Если вы используете любое другое ПО на сервер, нужно осторожно совмещать их. Так что, мы **настоятельно рекомендуем** использовать выделенный (физический или виртуальный) сервер для сервера okerr. По крайней мере, когда вы устанавливаете его первый раз

`okerr-install.py` - рекомендуемый способ установки, и он будет описан здесь. Так же есть чеклист для [manual install](ru/Manual install), но он не рекомендуется, если вы не очень хорошо знакомы с okerr.

## Шаг 1: создание LXC контейнера
~~~
# host
lxc-create -n okerr -t download -- --dist=debian --release=buster --arch=amd64
~~~

## Шаг 2a (рекомендуемый путь. делайте так)
~~~
# host: start and attach
lxc-start -n okerr
lxc-attach -n okerr

# VM
apt install git python3
git clone https://gitlab.com/yaroslaff/okerr-dev.git /opt/okerr
~~~

### Шаг 2b (альтернативный путь (исходные коды okerr на хостовой машине). НЕ делайте так, если вам это не нужно.)
~~~
# host machine
mkdir /var/lib/lxc/okerr/rootfs/opt/okerr
~~~

Edit `/var/lib/lxc/okerr/config`, add mount option to config (replace path to okerr-dev repo): 
~~~
lxc.mount.entry = /home/USERNAME/repo/okerr-dev /var/lib/lxc/okerr/rootfs/opt/okerr none bind 0 0
~~~

Continue preparation:
~~~
# start and attach
lxc-start -n okerr
lxc-attach -n okerr

# VM
mkdir /opt/okerr
apt install git python3

~~~

## Шаг 3: Установка
Замените email в команде и запустите:
~~~
# inside VM
cd /opt/okerr

# All default values. user: okerr@example.com password: okerr_default_password
./okerr-install.py --local --email USER@EMAIL.COM

# Or explicitly
./okerr-install.py --fix --apache --rmq --email USER@EMAIL.COM --pass MyPass
~~~
Теперь можно расслабиться и откинуться на спинку кресла. Эта команда установит множество пакетов debian, модулей python, все настроет. На моем ноутбуке это занимает 11 минут.

## Шаг 4: Послеустановочная конфигурация
По умолчанию okerr настраивается чтобы обслуживать хост dev.okerr.com, так что вы можете установить его в /etc/hosts на вашей рабочей машине (чтобы он указывал на контейнет с okerr):
~~~
192.168.122.219 dev.okerr.com
~~~
Или установить/добавить другое имя хоста в файлах `/etc/okerr/local.d/local.conf` и `/etc/apache2/sites-available/okerr.conf` .

Убедитесь, что вы можете отправлять почту с виртуального хоста. Если нужно - настройке postfix для этого (по умолчанию он использует имя хоста 'okerr').


~~~
myhostname = okerr  #replace to your valid hostname

# inet_protocols = all
inet_protocols = ipv4
~~~

Так же вам нужно установить настройки FROM и SERVER_EMAIL в локальном конфиге (`/etc/okerr/okerr.conf`). Умолчания:
~~~
SERVER_EMAIL = 'noreply@okerr.com'
FROM = '"okerr robot" <noreply@okerr.com>'
~~~

## Использование
На этом этапе вы уже можете зайти на http://dev.okerr.com/ и получить полностью рабочую систему.