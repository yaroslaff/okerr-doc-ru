### Heartbeat
Простейший тип пассивного индикатора. Индикатор имеет статус, который ему передается с клиенской машины (Если клиент отправляет "OK", что может означать, например 'Сервис apache2 на сервере X в порядке', то индикатор будет в статусе OK. Если клиент передает статус ERR, то индикатор будет в статусе ERR).

Если клиент не передал никакого обновления статуса за определенной время (policy.period + patience), то индикатор автоматически переключается в состояние ERR.

Описание `patience` и `secret` смотрите в [общих свойствах](common).

