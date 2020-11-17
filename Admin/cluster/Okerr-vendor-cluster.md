Так мы используем okerr  в нашем 'большом' кластере. Скорее всего, вам не требуется читать эту страницу, но если вам интересно...

## Install
~~~shell
export SENSOR_NAME=name@location.cc
export RMQ_HOST=rmq.okerr.com
export RMQ_PASS=...
export OKERR_SECRET=...
export OKERR_URL=...

sudo -E ./okerr-install.py --fix --apache --home ~xenon/repo/okerr-dev/ --cluster TEST \
 --confd ~xenon/repo/okerr-private/etc/okerr/conf.d ~xenon/repo/okerr-private/etc/okerr/ssl \
 --host $HOSTNAME localhost.okerr.com cat.okerr.com cur.okerr.com cp.okerr.com

~~~

## Uninstall (wipe)
~~~shell
#!/bin/sh

echo .. systemd 
systemctl stop okerr
rm /etc/systemd/system/okerr*service
systemctl daemon-reload

echo .. user
userdel okerr

echo .. /etc/okerr
rm -r /etc/okerr

echo .. apache
a2dissite okerr
rm /etc/apache2/sites-available/okerr.conf
systemctl reload apache2

echo .. db
mysqladmin drop okerr

echo .. venv
rm -r /opt/venv/okerr/

~~~