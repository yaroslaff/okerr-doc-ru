# Ручная установка

Это более сложный путь чтобы установить базовую часть okerr и предназначан только для опытных администраторов и программистов okerr, кто хочет работать с okerr "под капотом". Так же, это скорее подсказки и чек-листы, а не полное руководство и может быть немного устаревшим.

Другими словами: лучше используйте рекомендуемую [Автоматическую установку](Install) :)

## Install required debian packages
~~~shell
sudo apt install python3-dev python3-venv dialog gcc libmariadb-dev-compat libadns1-dev libffi-dev libssl-dev libsasl2-modules redis-server redis-tools libadns1-dev mariadb-server rsyslog postfix cron rabbitmq-server
~~~

## Configure redis socket
edit `/etc/redis/redis.conf` append:
~~~
unixsocket /var/run/redis/redis.sock
unixsocketperm 770
~~~
`sudo systemctl restart redis`

`sudo usermod -a -G redis USER`

Relogin now, or make sure you will work as user root (not secure) or user with group `redis`.

## Configure RabbitMQ
edit `/etc/rabbitmq/rabbitmq.config`
~~~
[
    {rabbit, [
            {tcp_listeners, []},
            {ssl_listeners, [{'0.0.0.0', 5671}]},
            {ssl_options, [
                {cacertfile,           "/etc/okerr/ssl/ca.pem"},
                {certfile,             "/etc/okerr/ssl/rabbitmq.pem"},
                {verify,               verify_peer},
                {fail_if_no_peer_cert, true}
                ]
            }
        ]
    }
].
~~~
`systemctl restart rabbitmq-server`

create vhost and user:
~~~
sudo rabbitmqctl add_vhost okerr
sudo rabbitmqctl add_user okerr okerr_default_password
sudo rabbitmqctl set_permissions -p okerr okerr ".*" ".*" ".*"
~~~


## Create db user
`mysql -u root`

~~~sql
CREATE DATABASE okerr CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE USER 'okerr'@'localhost' IDENTIFIED BY 'okerrpass';
GRANT ALL ON okerr.* TO 'okerr'@'localhost';
~~~

## Create and activate venv
~~~shell
sudo mkdir /opt/venv/okerr
sudo chown USER /opt/venv/okerr
python3 -m venv /opt/venv/okerr/
. /opt/venv/okerr/bin/activate
~~~

## Install python packages
~~~shell
pip3 install -r requirements.txt
~~~

## Create okerr certificates
~~~shell
sudo mkdir -p /etc/okerr/ssl
cd ca
sudo ./mkcert.sh ca
sudo ./mkcert.sh client
sudo ./mkcert.sh client rabbitmq
~~~

## Initialize database
~~~
./manage.py migrate
./manage.py dbadmin --reinit --really

./manage.py profile --create USER@EXAMPLE.COM --pass PASSWORD --textid okerr
./manage.py group --assign Admin --user USER@EXAMPLE.COM --infinite
~~~

## Create local config (optional)
~~~
sudo mkdir /etc/okerr/conf.d
sudo vim /etc/okerr/conf.d/local.conf
~~~
enable DEBUG in `local.conf`:
~~~
DEBUG=True
~~~

## Start mqsender (task manager)
~~~
./mqsender.py -v 
~~~

## Start sensor
~~~
sensor.py -v
~~~

## Start Okerr UI development UI
~~~
./manage.py runserver
~~~
