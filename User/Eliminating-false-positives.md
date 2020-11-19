
# Оповещения и ложные срабатывания
Основное предназначение okerr в оповещениях (алертах). Вы работаете с okerr чтобы настроить его и забыть. Затем, однажды вы получите алерт, который вас спасет. Он предупредит вас о наступающей проблеме (сертификат скоро протухнет) или аномалии (логи растут очень быстро / продажи в магазине сильно упали, нужно проверить) или наступившей проблеме (вебсайт пишет "PHP Error")

Каждое оповещение от okerr (в идеале) требует от вас каких-то действий (проверить или исправить проблему). Если вы не реагируете на оповещение - оно не было вам нужно. Такие оповещения являются "ложными срабатываниями" и не должны случаться. Если вы будете часто получать и игнорировать ложные оповещения ("Волки! Волки!") в один из ней вы можете так же проигнорировать по-настоящему важное оповещение.

В Okerr есть несколько способов, как сократить количество ложных оповещений (возможно даже до нуля). Каждый более уместен в своем случае.

# Методы сокращения ложных срабатываний

## Устранить проблему
**Пример**: У вас настроено оповещение, при заполнении диска свыше 80%. Обычное заполнение диска в интервале 70-82%. Иногда, когда сервер делает какую-то тяжелую задачу (например, создает дамп базы данных) потребление превышает 80% границу и вы получаете оповещение.

Оцените вариант покупки диска большего размера или почистите этот. Если это возможно - то это лучшее решение.

## Настройка параметров
Можно изменить максимальное значение в индикаторе с 80% до 85% или даже 90%. Это решит проблему с ложными оповещениями, но немного повысит риски.

## Подпишитесь только на индикаторы, которые вам важны
Если в проекте работают несколько человек, возможно, что вам важны только некоторые индикаторы (не все). Вы можете убрать галочку "Рассылать оповещения" в профиле (если включено - это включает оповещения от всех индикаторов), но на странице идикатора включите галочку "Подписаться" в пользовательских настройках. После этого вы будете получать оповещения только от индикаторов, на которые вы подписались.

## Режим настройки
Если вы настраиваете индикатор или ресурс, за которым следит индикатор (например, чистите диск), по ходу вашей работы индикатор может несколько раз поменять состояние и разошлет ложные оповещения другим членам команды. (Им они не нужны, ведь вы уже решаете эту проблему). Правильный путь в этом случае - установить "режим настройки" для индикатора. Чтобы это сделать, нажмите кнопку "..." на странице индикатора чтобы открыть дополнительные кнопки, и на включите режим настройки.

В режиме настройки оповещения не создаются. Так же, другие участники команды будут видеть, что индикатор находится в режиме настройки, кто его включил и когда. Они будут знать, что вы работаете.

## Тихий режим
Тихие индикаторы не создают оповещений. Если вы установите эту галочку, индикатор больше не будет отправлять оповещений, ни ложных, ни настоящих. Используйте с осторожностью.

## Проверки с указанием географического расположения сенсора
Для активных проверок (таких как sslcert, http statuc, ping) вы можете указать сенсор по стране/городу/имени. Если тесты из Франции не очень надежны, вы можете тестировать из Германии, к примеру.


## Расписание перепроверок
Активные проверки иногда дают ошибку из-за редких и коротких сетевых проблем. Это случается редко, но если у вас несколько сотен индикаторов, то хотя бы один из них будет давать ложное срабатывание по этой проблеме раз в несколько дней. Вместо того чтобы вручную перепроверять каждое такое срабатывание, не лучше ли поручить это okerr?

Настройте такое расписание перепроверок: "30s 5min". Теперь, если проверка будет неудачной, она повторится через 30 секунд. И если снова неудачна - еще через 5 минут. Только если все расписание перепроверок выполнено, и проблема по прежнему есть - то установится состояние ERR и будет отправлено оповещение. Если вы не хотите получать оповещения о кратковременных проблемах - вы не будете.

Каждая следующая перепроверка выполняется другим сенсором (если это возможно), не тем, который провалил предыдущую проверку.


## Логические индикаторы
Это очень мощная функция okerr. Статус логического индикатора определяется логическим выражением, основанным на текущем состоянии структуры данных проекта (она доступна со странички логического индикатора). Вы можете условно разделить индикаторы на индикаторы "нижнего уровня" (и им установить тихий режим) и создать логический индикатор ("верхнего уровня") который за ними следит.

**Пример 1** - Подавление кратковременных ошибок (моргание). Загрузка (Load Average) сервера обычно низкая, и у вас есть индикатор `srv1:la1` c maxlim=1.0, но иногда (например, когда вы компилируете большие проекты) он может подниматься до 5. Но затем в течение ближайших 20 минут возвращается снова к обычному значению, не требуется ничего "чинить". В таком случае, можно перевести этот индикатор в тихий режим. Он по прежнему будет переходить в состояние ERR при высокой загрузке, но уже не будет отправлять алерт. Затем, вы можете настроить логический индикатор чтобы отправлять алерт если наш индикатор загрузки в состоянии ERR более 20 минут (значение expr установить в `i['srv1:la']['errage']<1200`). Теперь, если Load Average сервера будет подниматься на короткое время - вы не будет получать ненужные оповещения, но если он будет держаться таким долго, вы получите алерт от логического индикатора и проверите ситуацию.

Этот способ похож на сокращение алертов и перепроверки. Возможно, какой-то из них вам окажется более подходящим.


**Пример 2**: Проблема по расписанию. У вас несколько серверов и сисадминам иногда нужно отключать некоторые сервисы или целые сервера. Вы выделили два "безопасных часа" с 3:00 до 5:00, даунтайм в этот период наименее болезненный и сисадмины могут делать все что им нужно в это время. Вы не хотите получать алерты о даунтайме в это время. Вы можете так же сделать эти индикаторы тихими, установить им, например, метку night, но сделать логический индикатор, который оповестит, если любой из них будет красным в любое время кроме разрешенного (expr: `tags['ERR:night']==0 or  hhmm>0300 and hhmm<0500`)


**Пример 3**: N из M. У вас M идентичных серверов, которые разделяют нагрузку. Так как любой из них заменяет другой, и временный даунтайм одного-двух серверов не является проблемой, вы можете создать логический индикатор, который будет регистрировать ошибку только если работают менее N серверов.  Например, всего 5 серверов и три из них  работают - OK, вы не получаете оповещения. Но если в работе осталось только 2 - это уже слишком мало, вы получите алерт.

Реализация: если все индикаторы у нас имеют одинаковый префикс (например, индикаторы cluster:1 .. cluster:5), то используем выражение `prefix['OK:cluster']>=3`, если же индикаторы с разными префиксами, то присваиваем наблюдаемым индикаторам одинаковую метку (например, 'cluster'), и используем выражение `tags['OK:cluster']>=3`.


## Сокращение оповещений
Это еще одна удобная и мощная функция okerr, эффективная защита для ситуация из примеров 1 и 2 выше, но часто гораздо более легкая и удобная. В политике можно указать настройку сокращения алертов, к примеру: "0s 00:30-02:00 5m 14:00-14:30 30m"

Это означает, что время сокращения алертов по умолчанию будет 0 секунд (никакого сокращения), в период 00:30 - 02:00 задержка алертов для сокращения будет 5 минут, а в период 14:00 - 14:30 - 30 минут.

Если индикатор переключится в ERR в 01:00, оповещение будет создано, но будет задержано на 5 минут (до 01:05). Если в 01:04 произойдет обратное переключение (ERR->OK), то оповещения об обоих переключениях будут сокращены (удалены), не будут отправлены. Но если обратного переключения за указанное время не произойдет, то задержанный алерт будет отправлен в 01:05.

Вы можете использовать короткие периоды сокращения оповещений почти для всех индикаторов все время, и использовать долгие периоды в то время, когда даунтам допускается.


## Просто оставить как есть

Если вы первый раз получили ложное срабатывание от индикатора - возможно, это и не нужно исправлять сейчас. Возможно, это не ложный, а правильный алерт? А если и не правильный, но случается раз очень редко - то можно оставить как есть. Например, [17-18 июля 2020 большая часть интернет-ресурсов была недоступна в результате проблем у компании CloudFlare](https://techcrunch.com/2020/07/17/cloudflare-dns-goes-down-taking-a-large-piece-of-the-internet-with-it/) (которая предоставляет популярный DNS сервер 1.1.1.1). Такие события происходят очень редко, и не страшно, если мониторинг оповещает о них (это даже правильно и ценно - заранее ведь не известно, где именно проблема, устранится ли она сама и когда).

С другой стороны, если вы получаете оповещения от okerr и не читаете их (потому что привыкли, что они часто ложные), или читаете очень бегло - наверное, это ложные срабатывания, и нужно найти решение этой проблемы.