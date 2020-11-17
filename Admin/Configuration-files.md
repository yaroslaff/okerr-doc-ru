# Общее описание схемы конфигурации okerr
Почти все программы okerr, которые работают с базой данных (Okerr UI, okerr-process, okerr-smtpd, okerr-telebot, okerr-mqsender, но не okerr-poster и okerr-sensor) используют эту схему конфигурации. Все конфигурационные файлы - модули python (если не указано обратное).



Главный конфигурационный файл `okerr/settings.py` в каталоге okerr. (Все прочие файлы конфигурации подгружаются (импортируются) из этого файла). Далее, `okerr/settings_defaults.py` загружается, чтобы установить значения по умолчанию.


Наиболее важные директивы в нем:
- `MAIN_CONF_DIR='/etc/okerr'`, 
- `SITE_PRECONF_FILES = MAIN_CONF_DIR/okerr.conf`,
- `SITE_CONF_DIRS` - subdirectories of `MAIN_CONF_DIR` ending with '.d' (alphabetically sorted). Including symlinks to directories.
- `SITE_POSTCONF_FILES = MAIN_CONF_DIR/post.conf`
- `SITE_JSON_DIRS = '/etc/okerr/json/'` (files here are in JSON format)

Файлы, перечисленные выше не должны редактироваться пользователем/администратором. 

Далее импортируются:
- Файлы preconf (предварительной конфигурации)
- Все файлы '*.conf' из SITE_CONF_DIRS
- Файлы postconf (пост-конфигурация)

Любые конфигурационные значения, которые нужно изменить - [пере]устанавливаются в этих файлах.

JKEYS_TPL загражается из `SITE_JSON_DIRS/keys-template.json`.

## Условный импорт
Есть возможность импортировать файлы конфигурации по условиям. Когда okerr импортирует файл, он проверяет переменные вида: `__IF_VARIABLE = VALUE`. Если хотя бы одна такая проверка не выполняется - файл не импортируется.

Пример:
/etc/okerr/okerr.conf:
~~~
CLUSTER_NAME = 'MY'
~~~

/etc/okerr/local.d/local.conf:
~~~
__IF_CLUSTER_NAME = 'LOCAL'
~~~

/etc/okerr/local.d/my.conf:
~~~
__IF_CLUSTER_NAME = 'MY'
~~~

В этом примере local.conf не будет загружен, а my.conf будет.


## Отладка импорта
Переменная `IMPORT_VERBOSITY` (по-умолчанию: 0) устанавливается в `settings.conf` и определяется, насколько подробной должна быть процедура импорта. Новое значение для этой переменной можно установить в `MAIN_CONF_DIR/okerr.conf`. Файлы `settings.py`, `settings_default.py` and `MAIN_CONF_DIR/okerr.conf` обрабатываются до обработки этого файла, и будут всегда импортированы с `IMPORT_VERBOSITY=0`. Но все последующие файлы будут загружаться с новым значением.

При IMPORT_VERBOSITY=0, все символы из файла импортируются полностью тихо.

При IMPORT_VERBOSITY>=1, пишутся имена загружаемых (или отвергнутых) файлов конфигурации. Пример:
~~~
Load /etc/okerr/conf.d/admin.conf
Load /etc/okerr/conf.d/cluster_first.conf
Skip loading /etc/okerr/conf.d/cluster_local.conf ( __IF_CLUSTER_NAME = 'LOCAL' != 'FIRST')
Skip loading /etc/okerr/conf.d/cluster_test.conf ( __IF_CLUSTER_NAME = 'TEST' != 'FIRST')
Load /etc/okerr/conf.d/demo.conf
~~~

При IMPORT_VERBOSITY>=2, так же пишется каждый импортируемый символ:
~~~
Load /etc/okerr/conf.d/admin.conf
    ADMINS
    ADMINUSER
    LOGMAIL
    USUALUSER
Load /etc/okerr/conf.d/cluster_first.conf
    MACHINES
    SYNC_MAP
    UP_MAP
...
~~~

При IMPORT_VERBOSITY>=3 так же пишется и значение каждого символа (иногда это может быть длинным текстом):
~~~
Load /etc/okerr/local.d/local.conf
    ALLOWED_HOSTS = ['localhost', '127.0.0.1', 'localhost.okerr.com', 'dev.okerr.com']
    MYIP = '1.2.3.4'
    SITEURL = 'http://localhost/'
~~~




