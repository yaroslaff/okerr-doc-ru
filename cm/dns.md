### DNS Resolving
Выполняет DNS запрос типа `type` для `host`.

Если результат запроса будет совпадать с `value`, статус будет OK, иначе ERR.

**Параметры**:
Если в `options` указано слово "dynamic" и результат запроса не совпадает с `value`, то статус останется OK, `value`
будет обновлено на новое значение, будет отправлено оповещение.

Если текущее значение `value` пустое и в `options` указано слово "init", то результат запроса будет сохранен в `value`.
