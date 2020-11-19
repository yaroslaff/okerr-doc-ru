
# Общие свойства различных проверок

Разные типы проверок могут иметь схожие параметры

## Options

Параметр **options** (обычно пустой) используется в проверках HTTP и может содержать `ssl_noverify` и `addr=a.b.c.d`. 

Если указана опция `ssl_noverify` - проверка будет успешной даже если сертификат не пройдет проверку (например, просрочен или self-signed).

Если указана опция `addr` (addr=a.b.c.d), запрос будет выполнен на адрес a.b.c.d, вне зависимости от того, какой хост указан в `URL`.

## Patience и Secret

Пассивные индикаторы имеют параметры Patience и Secret, которые (если установлены) переопределяют соответствующие значения из [Политики](../User/Policies).