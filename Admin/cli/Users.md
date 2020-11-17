# Управление пользователями

##  Создать пользователя и зачислить в группу (установить тариф) одной командой:
```shell
(okerr) okerr@okerr:~$ ./manage.py profile --create test@test.com --pass MyPassword --group Bellatrix --infinite
create user test@test.com
No such user. good!
create it.
created user test@test.com pass MyPassword
assign to group Bellatrix
```
Вы можете использовать `--days <NNN>` вместо `--infinite`. Используется `./manage.py group --list` чтобы увидеть все имеющиеся группы (тарифные планы).

## Создание пользователя (без добавления в группы)
```shell
./manage.py profile --list

./manage.py profile --create test@test.com --pass mypassword
```
Но не забудьте добавить его затем в [Группы](Groups).

## Удаление пользователей
~~~shell
./manage.py profile --delete --user user@example.com --really
~~~