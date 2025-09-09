# RCP.API

## Общая информация

Доступные форматы запросов: `application/json`. Сервис работает в часовом поясе `UTC+3`.

## Авторизация

`POST`: <https://belorusneft.by/identity/connect/token>

Заголовки

* `Content-Type`: `application/x-www-form-urlencoded`

Параметры

* `grant_type`: `password`
* `client_id`: `rcp.web`
* `client_secret`: `secret`
* `scope`: `rcp.api`
* `username`: `<имя пользователя>`
* `password`: пароль пользователя

`<имя пользователя>` формируется по следующему шаблону: `emitentId.contractId`

* `emitentId` - номер эмитента карты
* `contractId` - номер договора


| emitentId | Description
|-----------|-----------------------------------------
|020        | РУП "Белоруснефть-Брестоблнефтепродукт"
|160        | РУП "Белоруснефть-Витебскоблнефтепродукт"
|370        | РУП "Белоруснефть-Гомельоблнефтепродукт"
|520        | РУП "Белоруснефть-Гроднооблнефтепродукт"
|530        | ОАО "Лиданефтепродукт"
|600        | РУП "Белоруснефть-Минскоблнефтепродукт"
|650        | РУП "Белоруснефть-Минскавтозаправка"
|720        | ОАО "Пуховичинефтепродукт"
|800        | РУП "Белоруснефть-Могилевоблнефтепродукт"
|340        | ПУ "Связьинформсервис"


Структура ответа

| Fields          | Type      | Description
|-----------------|-----------|------------------------------
| `access_token`  | `string`  | Токен
| `expires_in`    | `integer` | Активность токена в секундах
| `token_type`    | `string`  | Тип токена

Пример успешного ответа

```json
{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
    "expires_in": 3600,
    "token_type": "Bearer"
}
```

HTTP статусы ответа

| Code | Description
|------|----------------------
| 200  | OK
| 400  | Bad request
| 500  | Internal Server Error

## Свойства топливных карт

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/cards>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура ответа

| Key                            | Type       | Discription
|--------------------------------|------------|-------------
| `contrCode`                    | `integer`  | Номер договора
| `cardCode`                     | `integer`  | Номер топливной карты
| `monthNorm`                    | `integer`  | Месячная норма отпуска топлива
| `dayNorm`                      | `integer`  | Дневная норма отпуска топлива в литрах
| `dayNormAmount`                | `decimal`  | Дневная норма в рублях
| `oilGroupSet`                  | `array`    | Массив разрешенных нефтепродуктов
| `transitFl`                    | `boolean`  | флаг разрешения отпуска по карте на транзитных АЗС (АЗС не принадлежащих ПОНу, с которым заключен договор)
| `goodsFl`                      | `boolean`  | Флаг отпуска товаров (`false` - запрещено, `true` - разрешено)
| `transitGoodsFl`               | `boolean`  | Флаг разрешения отпуска товаров на транзитных АЗС (false - запрещено, true - разрешено)
| `status`                       | `enum`     | Статус топливной карты, `0` - работа по карте запрещена, `1` - работа по карте разрешена
| `actionDate`                   | `date`     | Cрок действия топливной карты
| `division`                     | `integer`  | номер подразделения
| `driver`                       | `string`   | ФИО водителя
| `carNum`                       | `string`   | Государственный регистрационный знак автомобиля
| `priority`                     | `integer`  | Приоритет карты при распределении денежных средств, когда последних не хватает на полную суточную норму договора. Сначала деньги распределяются на карты с большим приоритетом
| `dosePermitted`                | `integer`  | Разрешенная доза в литрах
| `dosePermittedAmount`          | `decimal`  | Разрешенная доза в деньгах
| `kapschCard`                   | `boolean`  | `true` - топливная карта является картой счета для BELTOLL, `false` - не является
| `kapschContract`               | `boolean`  | `true` - топливная карта является картой договора для BELTOLL, `false` - не является
| `gasRemoteFill`                | `boolean`  | Флаг удаленной заправки газом. `true` - разрешена, `false` - запрещена
| `gasBottleVolume`              | `integer`  | Объем газового баллона
| `gasBottleExaminationDate`     | `datetime` | Дата освидетельствования баллона
| `gasBottleNextExaminationDate` | `datetime` | Дата следующего освидетельствования баллона
| `gasRulesAccepted`             | `boolean`  | Флаг ознакомления с правилами
| `pinCode`                      | `integer`  | Пинкод карты
| `roadTollsFl`                  | `boolean`  | Флаг оплаты дорог

Кодировка группы нефтепродуктов ключа `oilGroupSet`
| Бит | Группа
|-----|--------
| 0   | ДТ
| 1   | ДТЗ класс 2
| 2   | АИ-92
| 3   | АИ-95
| 4   | АИ-98
| 5   | АИ-100
| 6   | Керосин
| 7   | Газ
| 8   | Другие группы

Например, разрешаем ДТ - 0-ой бит поля `oilGroupSet` = 1, и т.д.

Пример успешного ответа

```json
[
    {
        "contrCode": 3701234,
        "cardCode": 370012345,
        "monthNorm": 65535,
        "dayNorm": 2500,
        "dayNormAmount": 4450.00,
        "oilGroupSet": [
            {
                "code": 1,
                "name": "ДТ"
            }
        ],
      "transitFl": true,
      "goodsFl": true,
      "transitGoodsFl": true,
      "status": 0,
      "actionDate": "2025-09-08T05:32:15.191Z",
      "division": 0,
      "driver": "string",
      "carNum": "string",
      "priority": 0,
      "dosePermitted": 0,
      "dosePermittedAmount": 0,
      "kapschCard": true,
      "kapschContract": true,
      "gasRemoteFill": true,
      "gasBottleVolume": 0,
      "gasBottleExaminationDate": "2025-09-08T05:32:15.191Z",
      "gasBottleNextExaminationDate": "2025-09-08T05:32:15.191Z",
      "gasRulesAccepted": true,
      "cardType": 0,
      "pinCode": "string",
      "vehicleParams": [
        {
          "cardCode": 0,
          "vehicleId": 0,
          "number": "string",
          "model": "string",
          "gasBottleVolume": 0,
          "gasBottleExaminationDate": "2025-09-08T05:32:15.191Z",
          "gasBottleNextExaminationDate": "2025-09-08T05:32:15.191Z",
          "viewInReportFl": true
        }
      ],
      "roadTollsFl": true
    }
]
```

HTTP статусы ответа

| Code | Description
|------|----------------------
| 200  | OK
| 400  | Bad request
| 500  | Internal Server Error

## Измение свойств карт

`PUT`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/cards>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                                            | Type       | Required | Discription
|------------------------------------------------|------------|:--------:|------------
| `contrCode`                                    | `integer`  |   Yes    | Номер договора
| `cardCode`                                     | `integer`  |   Yes    | Номер топливной карты
| `monthNorm`                                    | `integer`  |   Yes    | Месячная норма отпуска топлива
| `dayNorm`                                      | `integer`  |   Yes    | Дневная норма отпуска топлива в литрах
| `dayNormAmount`                                | `decimal`  |   Yes    | Дневная норма в рублях
| `oilGroupSet`                                  | `integer`  |   Yes    | Набор групп нефтепродуктов (см. примечание)
| `oilGroupSet`                                  | `array`    |   Yes    | Массив разрешенных нефтепродуктов
| `oilGroupSet`/`сode`                           | `integer`  |   Yes    | Код группы нефтепродуктов
| `oilGroupSet`/`name`                           | `string`   |   Yes    | Наименование группы нефтепродуктов
| `transitFl`                                    | `boolean`  |   Yes    | флаг разрешения отпуска по топливной карте на транзитных АЗС (АЗС не принадлежащих ПОНу, с которым заключен договор)
| `goodsFl`                                      | `boolean`  |   Yes    | Флаг отпуска товаров (`false` - запрещено, `true` - разрешено)
| `transitGoodsFl`                               | `boolean`  |   Yes    | Флаг разрешения отпуска товаров на транзитных АЗС (`false` - запрещено, `true` - разрешено)
| `status`                                       | `enum`     |   Yes    | Статус топливной карты, `0` - работа по топливноой карте запрещена, `1` - работа по карте разрешена
| `actionDate`                                   | `date`     |   Yes    | Cрок действия топливной карты
| `division`                                     | `integer`  |   Yes    | Номер подразделения
| `driver`                                       | `string`   |   Yes    | ФИО водителя
| `carNum`                                       | `string`   |   Yes    | Государственный регистрационный знак автомобиля
| `priority`                                     | `integer`  |   Yes    | Приоритет топливной карты при распределении денежных средств, когда последних не хватает на полную суточную норму договора. Сначала деньги распределяются на карты с большим приоритетом
| `dosePermitted`                                | `integer`  |   Yes    | Разрешенная доза в литрах
| `dosePermittedAmount`                          | `decimal`  |   Yes    | Разрешенная доза в деньгах
| `kapschCard`                                   | `boolean`  |   Yes    | `true` - топливная карта карта является картой счета для BELTOLL, `false` - не является
| `kapschContract`                               | `boolean`  |   Yes    | `true` - топливная карта является картой договора для BELTOLL, `false` - не является
| `user`                                         | `string`   |   Yes    | Логин пользователя, который сделал изменения в картах
| `gasRemoteFill`                                | `boolean`  |   Yes    | Флаг удаленной заправки газом. `true` - разрешена, `false` - запрещена
| `gasBottleVolume`                              | `integer`  |   Yes    | Объем газового баллона
| `gasBottleExaminationDate`                     | `datetime` |   Yes    | Дата освидетельствования баллона
| `gasBottleNextExaminationDate`                 | `datetime` |   Yes    | Дата следующего освидетельствования баллона
| `gasRulesAccepted`                             | `boolean`  |   Yes    | Флаг ознакомления с правилами
| `cardType`                                     | `integer`  |    No    | Тип карты
| `pinCode`                                      | `string`   |    No    | Пинкод карты
| `vehicleParams`                                | `array`    |    No    | Параметры автомобилей
| `vehicleParams`/`cardCode`                     | `integer`  |    No    | Номер карты
| `vehicleParams`/`vehicleId`                    | `long`     |   Yes    | Номер автомобиля
| `vehicleParams`/`number`                       | `string`   |    No    | Номер автомобиля
| `vehicleParams`/`model`                        | `string`   |    No    | Водитель
| `vehicleParams`/`gasBottleVolume`              | `integer`  |    No    | объем газового баллона
| `vehicleParams`/`gasBottleExaminationDate`     | `date`     |    No    | дата освидетельствования баллона
| `vehicleParams`/`gasBottleNextExaminationDate` | `date`     |    No    | дата последующего освидетельствования баллона
| `vehicleParams`/`viewInReportFl`               | `boolen`   |   Yes    | отражать в отчетах номер авто

Кодировка группы нефтепродуктов в поле `oilGroupSet`
| Бит | Группа
|-----|--------
| 0   | ДТ
| 1   | ДТЗ класс 2
| 2   | АИ-92
| 3   | АИ-95
| 4   | АИ-98
| 5   | АИ-100
| 6   | Керосин
| 7   | Газ
| 8   | Другие группы

Например, разрешаем ДТ - 0-ой бит ключа `oilGroupSet` = 1, и т.д.

Пример запроса

```json
[
    {
        "contrCode": 6800606,
        "cardCode": 650183695,
        "monthNorm": 6169,
        "dayNorm": 199,
        "dayNormAmount": 350,
        "oilGroupSet": [
            {
                "code": 1,
                "name": "ДТ"
            }
        ],
      "transitFl": true,
      "goodsFl": true,
      "transitGoodsFl": true,
      "status": 0,
      "actionDate": "2025-09-08T05:33:58.226Z",
      "division": 0,
      "driver": "string",
      "carNum": "string",
      "priority": 0,
      "dosePermitted": 0,
      "dosePermittedAmount": 0,
      "kapschCard": true,
      "kapschContract": true,
      "gasRemoteFill": true,
      "gasBottleVolume": 0,
      "gasBottleExaminationDate": "2025-09-08T05:33:58.226Z",
      "gasBottleNextExaminationDate": "2025-09-08T05:33:58.226Z",
      "gasRulesAccepted": true,
      "cardType": 0,
      "pinCode": "string",
      "vehicleParams": [
        {
          "cardCode": 0,
          "vehicleId": 0,
          "number": "string",
          "model": "string",
          "gasBottleVolume": 0,
          "gasBottleExaminationDate": "2025-09-08T05:33:58.226Z",
          "gasBottleNextExaminationDate": "2025-09-08T05:33:58.226Z",
          "viewInReportFl": true
        }
      ],
      "roadTollsFl": true
    }
]
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

## Отчеты

### Баланс по договору

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/balance>

Заголовки  

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура ответа

| Key                         | Type      | Discription
|-----------------------------|-----------|------------
| `loanFlag`                  | `boolean` | Признак наличия коммерческого займа `false` - нет, `true` - есть
| `contractIssuerId`          | `integer` | Код эмитента
| `clientName`                | `string`  | Наименование предприятия
| `contractNumber`            | `integer` | Номер договора
| `summa`                     | `decimal` | Остаток суммы на договоре
| `date`                      | `date`    | Время последнего пересчета остатка
| `unn`                       | `integer` | УНН
| `effectPrice`               | `decimal` | Сумма средств, при которой все разрешенные карты могут заправиться на 100% дневной нормы
| `phone`                     | `string`  | Номер телефона
| `internetChangeCard`        | `boolean` | Флаг разрешения менять свойства топливных карт в кабинете `false` - нельзя, `true` - можно
| `paymentPlatonFlag`         | `boolean` | Флаг разрешения осуществлять платежи в системе Платон `false` - нельзя, `true` - можно
| `minimumBalanceNoticeFlag`  | `boolean` | Признак уведомления о минимальном балансе
| `minimumBalanceSumm`        | `double`  | Сумма минимального баланса
| `mDMPartnerCode`            | `string`  | Код МДМ партнера
| `virtualCardCost`           | `decimal` | Стоимость виртуальной карты
| `expressFlag`               | `integer` | Признак использования экспресс-договора. `0` - не используется, `1` - используется
| `isElectronicCheck`         | `boolean` | Cогласие на получение электронных чеков
| `noReminderElectronicCheck` | `boolean` | флаг напоминания вывода уведомлений о согласии на электронные чеки
| `gasRemoteFill`             | `boolean` | флаг наличия доп. соглашения на самостоятельную заправку газом
| `statusCode`                | `integer` | Код состояния договора
| `isGasEquipment`            | `boolean` | Флаг наличия газобаллонного оборудования. `true` - есть, `false` - нет
| `isBudgetary`               | `boolean` | Признак бюджетой организации. `true` - да, `false` - нет
| `isEditFlagElectronicCheck` | `boolean` | Флаг изменения флага согласия на электронные чеки. `true` - да, `false` - нет
| `emitentName`               | `string`  | Наименование эмитента
| `emitentEmail`              | `string`  | Email эмитента

Пример успешного ответа

```json
{
    "loanFlag": true,
    "contractIssuerId": 370,
    "clientName": "Унитарное предприятие",
    "contractNumber": 3700263,
    "summa": 0.00,
    "date": "0001-01-01T00:00:00",
    "unn": null,
    "effectPrice": 9299.20,
    "phone": "1122334455",
    "internetChangeCard": true,
    "paymentPlatonFlag": true,
    "minimumBalanceNoticeFlag": true,
    "minimumBalanceSumm`": 4.23,
    "mDMPartnerCode": "234",
    "virtualCardCost": 3.45,
    "expressFlag": 1,
    "isElectronicCheck": 1, 
    "noReminderElectronicCheck": true,
    "gasRemoteFill": true,
    "statusCode": 1,
    "isGasEquipment": true,
    "isBudgetary": true,
    "isEditFlagElectronicCheck": true,
    "emitentName": "string",
    "emitentEmail": "string"
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### Баланс по договору без подсчета сумм

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/balanceLight>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура ответа

| Key                         | Type      | Discription
|-----------------------------|-----------|------------
| `loanFlag`                  | `boolean` | Флаг наличия коммерческого займа `false` - нет, `true` - есть
| `contractIssuerId`          | `integer` | Код эмитента
| `clientName`                | `string`  | Наименование предприятия
| `contractNumber`            | `integer` | Номер договора
| `summa`                     | `decimal` | Остаток суммы на договоре
| `date`                      | `date`    | Время последнего пересчета остатка
| `unn`                       | `integer` | УНН
| `effectPrice`               | `decimal` | Сумма средств, при которой все разрешенные карты могут заправиться на 100% дневной нормы
| `phone`                     | `string`  | Номер телефона
| `internetChangeCard`        | `boolean` | Флаг разрешения менять свойства карт в кабинете `false` - нельзя, `true` - можно
| `paymentPlatonFlag`         | `boolean` | Флаг разрешения осуществлять платежи в системе Платон `false` - нельзя, `true` - можно
| `minimumBalanceNoticeFlag`  | `boolean` | Признак уведомления о минимальном балансе
| `minimumBalanceSumm`        | `double` | Сумма минимального баланса
| `mDMPartnerCode`            | `string` | Код МДМ партнера
| `virtualCardCost`           | `decimal` | Стоимость виртуальной карты
| `expressFlag`               | `int` | Признак использования экспресс-договора. `0` - не используется, `1` - используется
| `isElectronicCheck`         | `boolean` | Cогласие на получение электронных чеков
| `noReminderElectronicCheck` | `boolean` | флаг напоминания вывода уведомлений о согласии на электронные чеки
| `gasRemoteFill`             | `boolean` | флаг наличия доп. соглашения на самостоятельную заправку газом
| `statusCode`                | `integer` | Код состояния договора
| `isGasEquipment`            | `boolean` | Флаг наличия газобаллонного оборудования. `true` - есть, `false` - нет
| `isBudgetary`               | `boolean` | Признак бюджетой организации. `true` - да, `false` - нет
| `isEditFlagElectronicCheck` | `boolean` | Флаг изменения флага согласия на электронные чеки. `true` - есть, `false` - нет
| `emitentName`               | `string`  | Наименование эмитента
| `emitentEmail`              | `string`  | Email эмитента

Пример успешного ответа

```json
{
    "loanFlag": true,
    "contractIssuerId": 370,
    "clientName": "Унитарное предприятие",
    "contractNumber": 3700263,
    "summa": 0.00,
    "date": "0001-01-01T00:00:00",
    "unn": null,
    "effectPrice": 9299.20,
    "phone": "1122334455",
    "internetChangeCard": true,
    "paymentPlatonFlag": true,
    "minimumBalanceNoticeFlag": true,
    "minimumBalanceSumm`": 4.23,
    "mDMPartnerCode": "234",
    "virtualCardCost": 3.45,
    "expressFlag": 1,
    "isElectronicCheck": 1, 
    "noReminderElectronicCheck": true,
    "gasRemoteFill": true,
    "statusCode": 1,
    "isGasEquipment": true,
  "isBudgetary": true,
  "isEditFlagElectronicCheck": true,
  "emitentName": "string",
  "emitentEmail": "string"
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### Пооперационный отчет

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/operational>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                | Type      | Required | Description
|--------------------|-----------|:--------:|------------
| `startDate`        | `date`    |   Yes    | Дата начала периода в формате `MM-DD-YYYY`
| `endDate`          | `date`    |   Yes    | Дата конца периода в формате `MM-DD-YYYY`
| `cardNumber`       | `integer` |   Yes    | Номер топливной карты. Если значение меньше либо равно нулю, тогда отчет по всем топливным картам договора
| `subDivisnNumber`  | `integer` |   Yes    | Номер подразделения. Если меньше нуля, тогда по всем подразделениям договора
| `flChoice`         | `integer` |   Yes    | Опция выбора информации: `1` - топливо, `2` - оплата дорог, `4` - иные товары (услуги), `3` - топливо и оплата дорог, `5` - топливо и иные товары (услуги), `6` - оплата дорог и иные товары (услуги), `7` - топливо, оплата дорог и иные товары (услуги)
| `AzsCode`          | `integer` |   Yes    | Номер АЗС. Значение `-1` - любой номер
| `EmtCodeFrm`       | `integer` |   Yes    | Код эмитента. Значение `-1` - любой эмитент
| `contractId`       | `integer` |   Yes    | Номер договора
| `contractIssuerId` | `integer` |   Yes    | ID эмитента договора
| `discountCode`     | `integer` |   Yes    | Код скидки
| `author`           | `string`  |    No    | Автор изменений

Пример запроса

```json
{
  "startDate": "2025-09-08T06:47:06.839Z",
  "endDate": "2025-09-08T06:47:06.839Z",
  "contractId": 0,
  "contractIssuerId": 0,
  "flChoice": 0,
  "cardNumber": 0,
  "subDivisnNumber": 0,
  "discountCode": 0,
  "author": "string",
  "emtCodeFrm": 0,
  "azsCode": 0
}
```

Структура ответа

| Key                          | Type     | Description
|------------------------------|----------|------------
| `cardList`                   | `array`    | Массив топливных карт
| `cardNumber`                 | `integer`  | Номер топливной карты
| `issueRows`                  | `array`    | Информация по платежу
| `subdivision`                | `integer`  | Номер подразделения
| `dateTimeIssue`              | `datetime` | Дата и время отпуска
| `productType`                | `integer`  | Тип ТМЦУ (`1` - нефтепродукты, `2` – газ, `3` - товар, `4` - услуги, `5` - дорожные сборы)
| `productCode`                | `integer`  | Код ТМЦУ
| `productName`                | `string`   | Наименование ТМЦУ
| `productQuantity`            | `decimal`  | Количество ТМЦУ
| `productMeasure`             | `string`   | Единица измерения
| `productUnitPrice`           | `decimal`  | Цена единицы с НДС со скидкой
| `productCost`                | `decimal`  | Стоимость с НДС со скидкой
| `vatAmount`                  | `decimal`  | Сумма НДС
| `discountWithVAT`            | `decimal`  | Скидка с НДС
| `percentageForServices`      | `decimal`  | Процент за услуги
| `costServiceWithVAT`         | `decimal`  | Стоимость услуг с НДС
| `costTotalWithVAT`           | `decimal`  | Стоимость всего с НДС
| `vatAmountTotal`             | `decimal`  | Всего сумма НДС
| `ownerName`                  | `decimal`  | Наименование владельца АЗС
| `azsNumber`                  | `integer`  | Номер АЗС
| `trkNumber`                  | `integer`  | Номер ТРК/номер секции
| `operCode`                   | `integer`  | Код операции (`2` - продажа, `5` - возврат) (не используется)
| `carNum`                     | `string`   | Государственный регистрационный знак автомобиля
| `driverName`                 | `string`   | ФИО водителя
| `docNumber`                  | `integer`  | Номер чека
| `emtCodeFrm`                 | `integer`  | Kод эмитента-владельца ТО
| `total`                      | `object`   | Блок итогов по отчету
| `total`/`subdivision`        | `integer`  | Номер подразделения
| `total`/`dateTimeIssue`      | `datetime` | Дата и время отпуска
| `total`/`productType`        | `integer`  | Тип ТМЦУ (`1` - нефтепродукты, `2` – газ, `3` - товар, `4` - услуги, `5` - дорожные сборы)
| `total`/`productCode`        | `integer`  | Код ТМЦУ
| `total`/`productName`        | `string`   | Наименование ТМЦУ
| `total`/`productQuantity`    | `integer`  | Код ТМЦУ
| `total`/`productCost`        | `decimal`  | Стоимость с НДС со скидкой
| `total`/`vatAmount`          | `decimal`  | Сумма НДС
| `total`/`discountWithVAT`    | `decimal`  | Скидка с НДС
| `total`/`costServiceWithVAT` | `decimal`  | Стоимость услуг с НДС
| `total`/`costServiceWithVAT` | `decimal`  | Стоимость услуг с НДС
| `total`/`costTotalWithVAT`   | `decimal`  | Стоимость всего с НДС
| `total`/`vatAmountTotal`     | `decimal`  | Всего сумма НДС
| `total`/`ownerName`          | `string`   | Наименование владельца АЗС
| `total`/`azsNumber`          | `integer`  | Номер АЗС
| `total`/`trkNumber`          | `integer`  | Номер ТРК/номер секции
| `total`/`operCode`           | `integer`  | Код операции (`2` - продажа, `5` - возврат) (не используется)
| `total`/`carNum`             | `string`   | Государственный регистрационный знак автомобиля
| `total`/`driverName`         | `string`   | ФИО водителя
| `total`/`docNumber`          | `integer`  | Номер чека
| `total`/`emtCodeFrm`         | `integer`  | Kод эмитента-владельца ТО

Пример успешного ответа

```json
{
    "cardList": [
        {
            "cardNumber": "112233445566",
            "issueRows": [
                {
                    "subdivision": 0,
                    "dateTimeIssue": "2019-12-17T23:59:00",
                    "productType": 5,
                    "productCode": 3,
                    "productName": "Залоговая стоимость",
                    "productQuantity": 1.000000,
                    "productMeasure": "шт.",
                    "productUnitPrice": 117.34,
                    "productCost": 117.34,
                    "vatAmount": 0.00,
                    "discountWithVAT": 0.00,
                    "percentageForServices": 0.50,
                    "costServiceWithVAT": 0.59,
                    "costTotalWithVAT": 117.93,
                    "vatAmountTotal": 0.10,
                    "ownerName": "Белавто",
                    "azsNumber": 9800,
                    "trkNumber": 0,
                    "operCode": 2,
                    "carNum": "",
                    "driverName": "",
                    "docNumber": 1234,
                    "emtCodeFrm": 370
                }
            ],
    "total": {
        "subdivision": 0,
        "dateTimeIssue": "0001-01-01T00:00:00",
        "productType": 0,
        "productCode": 0,
        "productName": null,        
        "productQuantity": 14.000000,
        "productMeasure": null,
        "productUnitPrice": 0.0,
        "productCost": 1640.63,
        "vatAmount": 0.00,
        "discountWithVAT": 0.00,
        "percentageForServices": 0.0,
        "costServiceWithVAT": 8.22,
        "costTotalWithVAT": 1648.85,
        "vatAmountTotal": 1.39,
        "ownerName": null,
        "azsNumber": 0,
        "trkNumber": 0,
        "operCode": 0,
        "carNum": null,
        "driverName": null,
        "docNumber": 0,
        "emtCodeFrm": 0
    }}]
}
```

HTTP статусы ответа

| Code | Description |
|------|-------------|
| 200  | OK          |
| 400  | Bad request |
| 401  | Unauthorized |
| 500  | Internal Server Error |

### Пооперационный отчет суммарный по типам ТМЦУ

`POST` <https://ssl.beloil.by/rcp/i/api/v3/Contract/operationalSum>  

Заголовки

* `Authorization`: `Bearer <токен>`  
* `Content-Type`: `application/json`  

Структура запроса

| Key                | Type      | Required | Description
|--------------------|-----------|:--------:|------------
| `startDate`        | `date`    |   Yes    | Дата начала периода в формате `MM-DD-YYYY`
| `endDate`          | `date`    |   Yes    | Дата конца периода в формате `MM-DD-YYYY`
| `contractId`       | `integer` |   Yes    | Номер договора
| `contractIssuerId` | `integer` |   Yes    | ID эмитента договора
| `cardNumber`       | `integer` |   Yes    | Номер топливной карты. Если значение меньше либо равно нулю, тогда отчет по всем картам договора
| `subDivisnNumber`  | `integer` |   Yes    | Номер подразделения. Если значение меньше нуля, тогда отчет по всем подразделениям договора
| `flChoice`         | `integer` |   Yes    | Параметр `FlChoice` - флаг выбора информации: `1` - топливо, `2` - оплата дорог, `4` - иные товары (услуги), `3` - топливо и оплата дорог, `5` - топливо и иные товары (услуги), `6` -  оплата дорог и иные товары (услуги), `7` - топливо, оплата дорог и иные товары (услуги)
| `author`           | `string`  |    No    | Автор изменений 
| `emtCodeFrm`       | `integer` |    No    | Код эмитента, если меньше нуля, то по всем подразделениям
| `azsCode`          | `integer` |    No    | Номер азс, если меньше нуля, то по всем подразделениям

Пример запроса

```json
{
 "startDate": "11-01-2019",
 "endDate": "11-30-2020",
 "cardNumber": 0,
 "subDivisnNumber": -1,
 "flChoice": 2
}
```

Структура ответа

| Key                                                        | Type      | Description
|------------------------------------------------------------|-----------|------------
| `subdivision`                                              | `array`   | Массив подразделений
| `divisionNumber`                                           | `integer` | Номер подразделения
| `cardList`                                                 | `array`   | Массив сведений по топливным картам
| `cardNumber`                                               | `integer` | Номер топливной карты
| `issueRows`                                                | `array`   | Информация по платежу
| `issueRows`/`subdivision`                                  | `integer` | Номер подразделения
| `productCode`                                              | `integer` | Код ТМЦУ
| `productType`                                              | `integer` | Тип ТМЦУ (`1` - нефтепродукты, `2` – газ, `3` - товар, `4` - услуги, `5` - дорожные сборы)
| `productName`                                              | `string`  | Наименование ТМЦУ
| `productQuantity`                                          | `decimal` | Количество ТМЦУ
| `productUnitPrice`                                         | `decimal` | Цена единицы с НДС со скидкой
| `productCost`                                              | `decimal` | Стоимость с НДС со скидкой
| `vatAmount`                                                | `decimal` | Сумма НДС
| `discountWithVAT`                                          | `decimal` | Скидка с НДС
| `percentageForServices`                                    | `decimal` | Процент за услуги
| `costServiceWithVAT`                                       | `decimal` | Стоимость услуг с НДС
| `costTotalWithVAT`                                         | `decimal` | Стоимость всего с НДС
| `vatAmountTotal`                                           | `decimal` | Всего сумма НДС
| `cardList`/`total`                                         | `object`  | Итоги по топливной карте
| `cardList`/`total`/`subdivision`                           | `integer` | Номер подразделения
| `cardList`/`total`/`productCode`                           | `integer` | Код товара
| `cardList`/`total`/`productType`                           | `integer` | Тип товара
| `cardList`/`total`/`productName`                           | `string`  | Наименование товара
| `cardList`/`total`/`productQuantity`                       | `decimal` | Всего по топливной карте количество ТМЦУ
| `cardList`/`total`/`productUnitPrice`                      | `decimal` | Цена единицы товара с НДС со скидкой
| `cardList`/`total`/`productCost`                           | `decimal` | Стоимость товара с НДС со скидкой
| `cardList`/`total`/`vatAmount`                             | `decimal` | Всего по топливной карте сумма НДС
| `cardList`/`total`/`discountWithVAT`                       | `decimal` | Всего по топливной карте cкидка с НДС
| `cardList`/`total`/`percentageForServices`                 | `decimal` | Процент за услуги
| `cardList`/`total`/`costServiceWithVAT`                    | `decimal` | Всего по топливной карте стоимость услуг с НДС
| `cardList`/`total`/`costTotalWithVAT`                      | `decimal` | Всего по топливной карте cтоимость всего с НДС
| `cardList`/`total`/`vatAmountTotal`                        | `decimal` | Всего по топливной карте сумма НДС
| `cardList`/`oilGas`                                        | `object`  | Итог по топливной карте/нефть и газ
| `cardList`/`oilGas`/`totalOilGas`                          | `decimal` | Итог по топливной карте/нефть и газ/cумма
| `cardList`/`oilGas`/`totalOilGasQuantity`                  | `decimal` | Итог по топливной карте/нефть и газ/количество
| `cardList`/`oilGas`/`totalWithVATWithDiscountOilGas`       | `decimal` | Итог по топливной карте/нефть и газ/стоимость с НДС со скидкой
| `cardList`/`oilGas`/`totalDiscountWithVATOilGas`           | `decimal` | Итог по топливной карте/нефть и газ/всего скидка с НДС
| `cardList`/`oilGas`/`totalVATOilGas`                       | `decimal` | Итог по топливной карте/нефть и газ/НДС
| `cardList`/`oilGas`/`totalTotalVATOilGas`                  | `decimal` | Итог по топливной карте/нефть и газ/всего НДС
| `cardList`/`oilGas`/`totalWithVATOilGas`                   | `decimal` | Итог по топливной карте/нефть и газ/стоимость с НДС
| `cardList`/`product`                                       | `object`  | Итог по топливной карте - товары
| `cardList`/`product`/`totalProduct`                        | `decimal` | Итог по топливной карте/товары/сумма
| `cardList`/`product`/`totalProductQuantity`                | `decimal` | Итог по топливной карте/товары/количество
| `cardList`/`product`/`totalWithVATWithDiscountProduct`     | `decimal` | Итог по топливной карте/товары/стоимость с НДС со скидкой
| `cardList`/`product`/`totalDiscountWithVATOilProduct`      | `decimal` | Итог по топливной карте/товары/всего скидка с НДС
| `cardList`/`product`/`totalVATProduct`                     | `decimal` | Итог по топливной карте/товары/сумма НДС
| `cardList`/`product`/`totalTotalVATProduct`                | `decimal` | Итог по топливной карте/товары/всего НДС
| `cardList`/`product`/`totalWithVATProduct`                 | `decimal` | Итог по топливной карте/товары/стоимость с НДС
| `cardList`/`service`                                       | `object`  | итог по топливной карте - услуги
| `cardList`/`service`/`totalService`                        | `decimal` | Итог по топливной карте/услуги/сумма
| `cardList`/`service`/`totalServiceQuantity`                | `decimal` | Итог по топливной карте/услуги/количество
| `cardList`/`service`/`totalWithVATWithDiscountService`     | `decimal` | Итог по топливной карте/услуги/стоимость с НДС со скидкой
| `cardList`/`service`/`totalDiscountWithVATOilService`      | `decimal` | Итог по топливной карте/услуги/всего скидка с НДС
| `cardList`/`service`/`totalVATService`                     | `decimal` | Итог по топливной карте/услуги/сумма НДС
| `cardList`/`service`/`totalTotalVATService`                | `decimal` | Итог по топливной карте/услуги/всего НДС
| `cardList`/`service`/`totalWithVATService`                 | `decimal` | Итог по топливной карте/услуги/стоимость с НДС
| `cardList`/`road`                                          | `object`  | Итог по топливной карте - оплата дорог
| `cardList`/`road`/`totalRoad`                              | `decimal` | Итог по топливной карте/оплата дорог/сумма
| `cardList`/`road`/`totalRoadQuantity`                      | `decimal` | Итог по топливной карте/оплата дорог/количество
| `cardList`/`road`/`totalWithVATWithDiscountRoad`           | `decimal` | Итог по топливной карте/оплата дорог/стоимость с НДС со скидкой
| `cardList`/`road`/`totalDiscountWithVATOilRoad`            | `decimal` | Итог по топливной карте/оплата дорог/всего скидка с НДС
| `cardList`/`road`/`totalVATRoad`                           | `decimal` | Итог по топливной карте/оплата дорог/сумма НДС
| `cardList`/`road`/`totalTotalVATRoad`                      | `decimal` | Итог по топливной карте/оплата дорог/всего НДС
| `cardList`/`road`/`totalWithVATRoad`                       | `decimal` | Итог по топливной карте/оплата дорог/стоимость с НДС
| `cardList`/`totalQuantity`                                 | `decimal` | Итог по топливной карте - количество товара
| `cardList`/`totalQuantity`                                 | `decimal` | Итог по топливной карте - количество товара
| `divisionSummaryOilGas`                                    | `object`  | Итог по подразделению нефть и газ
| `divisionSummaryOilGas`/`totalOilGas`                      | `decimal` | Итог по подразделению нефть и газ/сумма
| `divisionSummaryOilGas`/`totalOilGasQuantity`              | `decimal` | Итог по подразделению нефть и газ/количество
| `divisionSummaryOilGas`/`totalWithVATWithDiscountOilGas`   | `decimal` | Итог по подразделению нефть и газ/стоимость с НДС со скидкой
| `divisionSummaryOilGas`/`totalDiscountWithVATOilGas`       | `decimal` | Итог по подразделению нефть и газ/всего скидка с НДС
| `divisionSummaryOilGas`/`totalVATOilGas`                   | `decimal` | Итог по подразделению нефть и газ/сумма НДС
| `divisionSummaryOilGas`/`totalTotalVATOilGas`              | `deciaml` | Итог по подразделению нефть и газ/всего НДС
| `divisionSummaryOilGas`/`totalWithVATOilGas`               | `decimal` | Итог по подразделению нефть и газ/стоимость с НДС
| `divisionSummaryProduct`                                   | `object`  | Итог по подразделению товары
| `divisionSummaryProduct`/`totalProduct`                    | `decimal` | Итог по подразделению товары/сумма
| `divisionSummaryProduct`/`totalProductQuantity`            | `decimal` | Итог по подразделению товары/количество
| `divisionSummaryProduct`/`totalWithVATWithDiscountProduct` | `decimal` | Итог по подразделению товары/стоимость с НДС со скидкой
| `divisionSummaryProduct`/`totalDiscountWithVATProduct`     | `decimal` | Итог по подразделению товары/всего скидка с НДС
| `divisionSummaryProduct`/`totalWithVATProduct`             | `deciaml` | Итог по подразделению товары/стоимость с НДС
| `divisionSummaryProduct`/`totalVATProduct`                 | `decimal` | Итог по подразделению товары/сумма НДС
| `divisionSummaryProduct`/`totalTotalVATProduct`            | `decimal` | Итог по подразделению товары/всего НДС
| `divisionSummaryService`                                   | `object`  | Итог по подразделению услуги
| `divisionSummaryService`/`totalService`                    | `decimal` | Итог по подразделению услуги/
| `divisionSummaryService`/`totalServiceQuantity`            | `decimal` | Итог по подразделению услуги/
| `divisionSummaryService`/`totalVATService`                 | `decimal` | Итог по подразделению услуги/
| `divisionSummaryService`/`totalWithVATWithDiscountService` | `decimal` | Итог по подразделению услуги/
| `divisionSummaryService`/`totalDiscountWithVATService`     | `decimal` | Итог по подразделению услуги/
| `divisionSummaryService`/`totalWithVATService`             | `decimal` | Итог по подразделению услуги/
| `divisionSummaryService`/`totalTotalVATService`            | `decimal` | Итог по подразделению услуги/
| `divisionSummaryRoad`                                      | `object`  | Итог по подразделению оплата дорог
| `divisionSummaryRoad`/`totalRoad`                          | `decimal` | Итог по подразделению оплата дорог/сумма
| `divisionSummaryRoad`/`totalRoadQuantity`                  | `decimal` | Итог по подразделению оплата дорог/количество
| `divisionSummaryRoad`/`totalWithVATWithDiscountRoad`       | `decimal` | Итог по подразделению оплата дорог/стоимость с НДС со скидкой
| `divisionSummaryRoad`/`totalDiscountWithVATRoad`           | `decimal` | Итог по подразделению оплата дорог/всего скидка с НДС
| `divisionSummaryRoad`/`totalVATRoad`                       | `decimal` | Итог по подразделению оплата дорог/стоимость с НДС
| `divisionSummaryRoad`/`totalWithVATRoad`                   | `decimal` | Итог по подразделению оплата дорог/сумма НДС
| `divisionSummaryRoad`/`totalTotalVATRoad`                  | `decimal` | Итог по подразделению оплата дорог/всего НДС
| `total`                                                    | `object`  | Итог по отчету

Пример успешного ответа

```json
{
    "subDivisions": [
        {
            "divisionNumber": 1,
            "cardList": [
                {
                    "cardNumber": "112233445566",
                    "issueRows": [
                        {
                            "subdivision": 0,
                            "productCode": 20,
                            "productType": 1,
                            "productName": "ДТЗ Евро5",
                            "productQuantity": 1780.000000,
                            "productUnitPrice": 1.68,
                            "productCost": 2990.40,
                            "vatAmount": 498.40,
                            "discountWithVAT": 0.00,
                            "percentageForServices": 0.00,
                            "costServiceWithVAT": 0.00,
                            "costTotalWithVAT": 2990.40,
                            "vatAmountTotal": 498.40
                        }
                    ],
                    "total": {
                        "subdivision": 0,
                        "productCode": 0,
                        "productType": 0,
                        "productName": "",
                        "productQuantity": 34.210000,
                        "productUnitPrice": 0.0,
                        "productCost": 70.70,
                        "vatAmount": 5.89,
                        "discountWithVAT": 2.32,
                        "percentageForServices": 0.0,
                        "costServiceWithVAT": 0.37,
                        "costTotalWithVAT": 71.07,
                        "vatAmountTotal": 5.99
                    },
                    "oilGas": {
                        "totalOilGas": 12846.13,
                        "totalOilGasQuantity": 7748.850000,
                        "totalWithVATWithDiscountOilGas": 12846.13,
                        "totalDiscountWithVATOilGas": 0.00,
                        "totalVATOilGas": 2141.12,
                        "totalTotalVATOilGas": 2141.12,
                        "totalWithVATOilGas": 12846.13
                    },
                    "product": {
                        "totalProduct": 0.0,
                        "totalProductQuantity": 0.0,
                        "totalWithVATWithDiscountProduct": 0.0,
                        "totalDiscountWithVATProduct": 0.0,
                        "totalWithVATProduct": 0.0,
                        "totalVATProduct": 0.0,
                        "totalTotalVATProduct": 0.0
                    },
                    "service": {
                        "totalService": 0.0,
                        "totalServiceQuantity": 0.0,
                        "totalVATService": 0.0,
                        "totalWithVATWithDiscountService": 0.0,
                        "totalDiscountWithVATService": 0.0,
                        "totalWithVATService": 0.0,
                        "totalTotalVATService": 0.0
                    },
                    "road": {
                        "totalRoad": 0.0,
                        "totalRoadQuantity": 0.0,
                        "totalWithVATWithDiscountRoad": 0.0,
                        "totalDiscountWithVATRoad": 0.0,
                        "totalVATRoad": 0.0,
                        "totalWithVATRoad": 0.0,
                        "totalTotalVATRoad": 0.0
                    },
                    "totalQuantity": 7748.850000
                }
            ],
            "divisionSummaryOilGas": {
                "totalOilGas": 50579.27,
                "totalOilGasQuantity": 0.0,
                "totalWithVATWithDiscountOilGas": 50579.27,
                "totalDiscountWithVATOilGas": 0.00,
                "totalVATOilGas": 8430.32,
                "totalTotalVATOilGas": 8430.32,
                "totalWithVATOilGas": 50579.27
            },
            "divisionSummaryProduct": {
                "totalProduct": 149.25,
                "totalProductQuantity": 0.0,
                "totalWithVATWithDiscountProduct": 149.25,
                "totalDiscountWithVATProduct": 5.60,
                "totalWithVATProduct": 149.25,
                "totalVATProduct": 24.88,
                "totalTotalVATProduct": 24.88
            },
            "divisionSummaryService": {
                "totalService": 0.0,
                "totalServiceQuantity": 0.0,
                "totalVATService": 0.0,
                "totalWithVATWithDiscountService": 0.0,
                "totalDiscountWithVATService": 0.0,
                "totalWithVATService": 0.0,
                "totalTotalVATService": 0.0
            },
            "divisionSummaryRoad": {
                "totalRoad": 0.0,
                "totalRoadQuantity": 0.0,
                "totalWithVATWithDiscountRoad": 0.0,
                "totalDiscountWithVATRoad": 0.0,
                "totalVATRoad": 0.0,
                "totalWithVATRoad": 0.0,
                "totalTotalVATRoad": 0.0
            },
            "total": {
                "subdivision": 0,
                "productCode": 0,
                "productType": 0,
                "productName": null,
                "productQuantity": 30727.440000,
                "productUnitPrice": 0.0,
                "productCost": 50728.52,
                "vatAmount": 8455.20,
                "discountWithVAT": 5.60,
                "percentageForServices": 0.0,
                "costServiceWithVAT": 0.00,
                "costTotalWithVAT": 50728.52,
                "vatAmountTotal": 8455.20
            }
        }
    ],
    "total": {
        "subdivision": 0,
        "productCode": 0,
        "productType": 0,
        "productName": null,
        "productQuantity": 30727.440000,
        "productUnitPrice": 0.0,
        "productCost": 50728.52,
        "vatAmount": 8455.20,
        "discountWithVAT": 5.60,
        "percentageForServices": 0.0,
        "costServiceWithVAT": 0.00,
        "costTotalWithVAT": 50728.52,
        "vatAmountTotal": 8455.20
    }
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### Сверка расчетов

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/recon>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key         | Type   | Required | Description
|-------------|--------|:--------:|------------
| `startDate` | `date` |   Yes    | Дата начала периода в формате `MM-DD-YYYY`
| `endDate`   | `date` |   Yes    | Дата конца периода в формате `MM-DD-YYYY`
| `contractId`       | `integer` |   Yes    | Номер договора
| `contractIssuerId` | `integer` |   Yes    | ID эмитента договора
| `cardNumber`       | `integer` |   Yes    | Номер топливной карты. Если значение меньше либо равно нулю, тогда отчет по всем картам договора
| `subDivisnNumber`  | `integer` |   Yes    | Номер подразделения. Если значение меньше нуля, тогда отчет по всем подразделениям договора
| `flChoice`         | `integer` |   Yes    | Параметр `FlChoice` - флаг выбора информации: `1` - топливо, `2` - оплата дорог, `4` - иные товары (услуги), `3` - топливо и оплата дорог, `5` - топливо и иные товары (услуги), `6` -  оплата дорог и иные товары (услуги), `7` - топливо, оплата дорог и иные товары (услуги)
| `author`           | `string`  |    No    | Автор изменений 
| `emtCodeFrm`       | `integer` |    No    | Код эмитента, если меньше нуля, то по всем подразделениям
| `azsCode`          | `integer` |    No    | Номер азс, если меньше нуля, то по всем подразделениям

Пример запроса

```json
{
  "startDate": "2025-09-08T07:05:28.924Z",
  "endDate": "2025-09-08T07:05:28.924Z",
  "contractId": 0,
  "contractIssuerId": 0,
  "flChoice": 0,
  "cardNumber": 0,
  "subDivisnNumber": 0,
  "discountCode": 0,
  "author": "string",
  "emtCodeFrm": 0,
  "azsCode": 0
}
```

Структура ответа

| Key                        | Type      | Description
|----------------------------|-----------|------------
| `balanceBegin`             | `decimal` | Сальдо на начало периода
| `totalPaidByMonth`         | `deciaml` | Итого произведено оплат на сумму за месяц
| `goodsReleased`            | `decimal` | Отпущено товаров и оказано услуг на сумму
| `fuel`                     | `decimal` | Сумма по топливу
| `roads`                    | `decimal` | Сумма по оплате дорог
| `relatedProducts`          | `decimal` | Сумма по сопутствующим товарам и услугам
| `commission`               | `decimal` | Сумма за организационные услуги (комиссия)
| `sumCard`                  | `decimal` | Стоимость выданных электронных карт
| `commercialLoan`           | `decimal` | Сумма коммерческого заема
| `balanceEnd`               | `decimal` | Сальдо на конец периода
| `paidItems`                | `array`   | Массив сведений о произведенных выплатах
| `paidItems`/`date`         | `date`    | Дата операции
| `paidItems`/`pay`          | `decimal` | Произведенные выплаты
| `paidItems`/`accruedAll`   | `decimal` | Начислено всего
| `paidItems`/`oil`          | `decimal` | в том числе нефтепродукты
| `paidItems`/`roads`        | `decimal` | в том числе дорожные сборы
| `paidItems`/`relatedGoods` | `decimal` | в том числе иные сопутствующие товары и услуги
| `paidItems`/`management`   | `decimal` | в том числе организационные услуги
| `paidItems`/`sumCard`      | `decimal` | в том числе стоимость выданных электронных карт
| `paidItems`/`percent`      | `decimal` | Проценты по коммерческому займу

Пример успешного ответа

```json
{
    "balanceBegin": 139.65,
    "totalPaidByMonth": 447.63,
    "goodsReleased": 2751.77,
    "fuel": 2751.77,
    "roads": 0.00,
    "relatedProducts": 0.00,
    "commission": 0.00,
    "sumCard": 0.00,
    "commercialLoan": 0.0,
    "balanceEnd": -2164.49,
    "paidItems": [
        {
            "date": "2017-12-01T00:00:00",
            "pay": -52.37,
            "accruedAll": 1162.03,
            "oil": 1162.03,
            "roads": 0.00,
            "relatedGoods": 0.00,
            "management": 0.00,
            "sumCard": 0.00,
            "percent": 0.00
        }
    ]
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error


### Цены нефтепродуктов

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/oilPrices>

Заголовки

* `Authorization`: `Bearer <токен>`

Структура ответа

| Key           | Type      | Description
|---------------|-----------|------------
| `oilCode`     | `integer` | Код нефтепродукта
| `smallName`   | `string`  | Краткое название нефтепродукта
| `name`        | `string`  | Полное название нефтепродукта
| `price`       | `decimal` | Цена нефтепродукта

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 401  | Unauthorized
| 500  | Internal Server Error

### Список карт

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/cardlist>

Заголовки

* `Authorization`: `Bearer <токен>`

Структура ответа

| Key        | Type      | Description
|------------|-----------|------------
| `cardCode` | `integer` | Номер карты 
| `driver`   | `string`  | Водитель
| `division` | `string`  | Подразделение

Пример успешного ответа

```json
{
  "list": [
    {
      "cardCode": 0,
      "driver": "string",
      "division": 0
    }
  ]
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 401  | Unauthorized
| 500  | Internal Server Error

### Получить справочник нефтепродуктов

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/oil>

Заголовки

* `Authorization`: `Bearer <токен>`

Структура ответа

| Key            | Type      | Description
|----------------|-----------|------------
| `oilCode`      | `integer` | Код нефтепродукта
| `smallName`    | `string`  | Краткое название нефтепродукта
| `actualFlag`   | `integer` | Флаг атуильности
| `сodeOilGroup` | `integer` | Код группы нефтепродукта
| `nameOilGroup` | `string`  | Наименование группы нефтепродукта

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 401  | Unauthorized
| 500  | Internal Server Error

### Получить список групп нефтепродуктов

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/OilGroup>

Заголовки

* `Authorization`: `Bearer <токен>`

Структура ответа

| Key    | Type      | Description
|--------|-----------|------------
| `code` | `integer` | Код группы
| `name` | `string`  | Наименование группы

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 401  | Unauthorized
| 500  | Internal Server Error

### Коммерческий заем

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/commercial>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key         | Type   | Required | Description
|-------------|--------|:--------:|------------
| `startDate` | `date` |   Yes    | Дата начала периода в формате `MM-DD-YYYY`
| `endDate`   | `date` |   Yes    | Дата конца периода в формате `MM-DD-YYYY`
| `contractId`       | `integer` |   Yes    | Номер договора
| `contractIssuerId` | `integer` |   Yes    | ID эмитента договора
| `cardNumber`       | `integer` |   Yes    | Номер топливной карты. Если значение меньше либо равно нулю, тогда отчет по всем картам договора
| `subDivisnNumber`  | `integer` |   Yes    | Номер подразделения. Если значение меньше нуля, тогда отчет по всем подразделениям договора
| `flChoice`         | `integer` |   Yes    | Параметр `FlChoice` - флаг выбора информации: `1` - топливо, `2` - оплата дорог, `4` - иные товары (услуги), `3` - топливо и оплата дорог, `5` - топливо и иные товары (услуги), `6` -  оплата дорог и иные товары (услуги), `7` - топливо, оплата дорог и иные товары (услуги)
| `author`           | `string`  |    No    | Автор изменений 
| `emtCodeFrm`       | `integer` |    No    | Код эмитента, если меньше нуля, то по всем подразделениям
| `azsCode`          | `integer` |    No    | Номер азс, если меньше нуля, то по всем подразделениям

Пример запроса

```json
{
  "startDate": "2025-09-08T07:05:28.924Z",
  "endDate": "2025-09-08T07:05:28.924Z",
  "contractId": 0,
  "contractIssuerId": 0,
  "flChoice": 0,
  "cardNumber": 0,
  "subDivisnNumber": 0,
  "discountCode": 0,
  "author": "string",
  "emtCodeFrm": 0,
  "azsCode": 0
}
```

Структура ответа

| Key          | Type      | Description
|--------------|-----------|------------
| `monthDate`  | `date`    | Месяц (1 число)
| `beginRest`  | `decimal`    | Сальдо на начало периода
| `debetTurn`  | `decimal` | Начислено КЗ
| `creditTurn` | `decimal` | Оплачено по КЗ
| `endRest`    | `decimal` | Сальдо на конец месяца

Пример успешного ответа

```json
{
  "monthDate": "2025-09-08T07:14:55.479Z",
  "beginRest": 0,
  "debetTurn": 0,
  "creditTurn": 0,
  "endRest": 0
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### Получить условия договора

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/terms>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура ответа

| Key                            | Type      | Description
|--------------------------------|-----------|------------
| `contractNumber`               | `int`     | Номер договора
| `contractDate`                 | `date`    | Дата заключения договора
| `emailFlag`                    | `boolean` | Флаг формирования электронной почты
| `emailReport`                  | `string`  | Адрес для отправки отчета с ЭЦП
| `paymentMethod`                | `string`  | Способ оплаты 
| `expiredDay`                   | `int`     | Отсрочка погашения задолженности (кол-во дней)
| `discountFlag`                 | `boolean` | Флан расчета скидки
| `discount`                     | `int`     | Код скидки
| `loan`                         | `string`  | Коммерческий заем
| `loanTerm`                     | `string`  | Срок коммерческого займа
| `loanSize`                     | `string`  | Размер коммерческого займа
| `loanCost`                     | `string`  | Стоимость коммерческого займа
| `providerName`                 | `string`  | Наименование поставщика
| `providerUNP`                  | `string`  | УНП поставщика
| `providerLegalAddress`         | `string`  | Юридический адрес поставщика
| `providerPostAddress`          | `string`  | Почтовый адрес поставщика
| `providerSite`                 | `string`  | Сайт поставщика
| `providerEmail`                | `string`  | Адрес электронной почты поставщика
| `providerPhone`                | `string`  | Телефон поставщика
| `providerAccount`              | `string`  | Расчетный счет поставщика
| `providerBankName`             | `string`  | Наименование банка поставщика
| `providerBankCode`             | `string`  | Код банка поставщика
| `providerBankAddress`          | `string`  | Адрес банка поставщика
| `customerName`                 | `string`  | Наименование покупателя
| `customerUNP`                  | `string`  | УНП покупателя
| `customerLegalAddress`         | `string`  | Юридический адрес покупателя
| `customerPostAddress`          | `string`  | Почтовый адрес покупателя
| `contrDiscountList`            | `string`  | Детальная информация по скидке
| `contrDiscountList`/`vsCode`   | `string`  | Код нефтепродукта
| `contrDiscountList`/`limit`    | `decimal` | Предел
| `contrDiscountList`/`disValue` | `decimal` | Значение скидки
| `contrDiscountList`/`disType`  | `string`  | Описание типа скидки
| `contrDiscountList`/`oilSet`   | `string`  | Описание набора нефтепродуктов

Пример успешного ответа

```json
{
  "contractNumber": 0,
  "contractDate": "2025-09-08T07:25:41.786Z",
  "emailFlag": true,
  "emailReport": "string",
  "paymentMethod": "string",
  "payCurrency": "string",
  "expiredDay": 0,
  "discountFlag": true,
  "discount": 0,
  "loan": "string",
  "loanTerm": "string",
  "loanSize": "string",
  "loanCost": 0,
  "providerName": "string",
  "providerUNP": "string",
  "providerLegalAddress": "string",
  "providerPostAddress": "string",
  "providerSite": "string",
  "providerEmail": "string",
  "providerPhone": "string",
  "providerAccount": "string",
  "providerBankName": "string",
  "providerBankCode": "string",
  "providerBankAddress": "string",
  "customerName": "string",
  "customerUNP": "string",
  "customerLegalAddress": "string",
  "customerPostAddress": "string",
  "contrDiscountList": [
    {
      "vsCode": "string",
      "limit": 0,
      "disValue": 0,
      "disType": "string",
      "oilSet": "string"
    }
  ]
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### Получить транспортные средства договора

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/contractVehicles>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура ответа

| Key                                            | Type      | Description
|------------------------------------------------|-----------|------------
| `сontrCode`                                    | `integer` | Номер карты
| `number`                                       | `string`  | Номер автомобиля
| `model`                                        | `string`  | Водитель
| `gasBottleVolume`                              | `integer` | Объем газового баллона
| `gasBottleExaminationDate`                     | `date`    | Дата освидетельствования баллона
| `gassBottleNextExaminationDate`                | `date`    | Дата последующего освидетельствования баллона
| `active`                                       | `boolean` | Флаг разрешения заправки авто по карте
| `vehicleParams`                                | `array`   | Параметры автомобиля
| `vehicleParams`/`cardCode`                     | `string`  | Номер карты
| `vehicleParams`/`vehicleId`                    | `decimal` | Номер автомобиля
| `vehicleParams`/`number`                       | `decimal` | Номер автомобиля
| `vehicleParams`/`model`                        | `string`  | Водитель
| `vehicleParams`/`gasBottleVolume`              | `integer` | Объем газового баллона
| `vehicleParams`/`gasBottleExaminationDate`     | `date`    | Дата освидетельствования баллона
| `vehicleParams`/`gasBottleNextExaminationDate` | `date`    | Дата последующего освидетельствования баллона
| `vehicleParams`/`viewInReportFl`               | `boolean` | Отражать в отчетах номер авто

Пример успешного ответа

```json
[
  {
    "id": 0,
    "contrCode": 0,
    "number": "string",
    "model": "string",
    "gasBottleVolume": 0,
    "gasBottleExaminationDate": "2025-09-08T08:23:41.452Z",
    "gasBottleNextExaminationDate": "2025-09-08T08:23:41.452Z",
    "active": true,
    "vehicleParams": [
      {
        "cardCode": 0,
        "vehicleId": 0,
        "number": "string",
        "model": "string",
        "gasBottleVolume": 0,
        "gasBottleExaminationDate": "2025-09-08T08:23:41.452Z",
        "gasBottleNextExaminationDate": "2025-09-08T08:23:41.452Z",
        "viewInReportFl": true
      }
    ]
  }
]
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

## Отправка измененных настроек авто

`PUT`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/contractVehicles>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                            | Type      | Required | Discription
|--------------------------------|-----------|:--------:|------------
| `id`                           | `long`    |   Yes    | Номер карты
| `contrCode`                    | `integer` |   Yes    | Номер карты
| `number`                       | `string`  |   Yes    | Номер автомобиля
| `model`                        | `string`  |   Yes    | Водитель
| `gasBottleVolume`              | `integer` |    No    | Объем газового баллона
| `gasBottleExaminationDate`     | `date`    |    No    | Дата освидетельствования баллона
| `gasBottleNextExaminationDate` | `date`    |    No    | Дата последующего освидетельствования баллона
| `active`                       | `boolen`  |   Yes    | Флаг разрешения заправки авто по карте
| `vehicleParams`                | `array`   |   Yes    | Параметры автомобилей
| `vehicleParams`/`cardCode`                     | `string`  | Номер карты
| `vehicleParams`/`vehicleId`                    | `decimal` | Номер автомобиля
| `vehicleParams`/`number`                       | `decimal` | Номер автомобиля
| `vehicleParams`/`model`                        | `string`  | Водитель
| `vehicleParams`/`gasBottleVolume`              | `integer` | Объем газового баллона
| `vehicleParams`/`gasBottleExaminationDate`     | `date`    | Дата освидетельствования баллона
| `vehicleParams`/`gasBottleNextExaminationDate` | `date`    | Дата последующего освидетельствования баллона
| `vehicleParams`/`viewInReportFl`               | `boolean` | Отражать в отчетах номер авто

Пример запроса

```json
[
  {
    "id": 0,
    "contrCode": 0,
    "number": "string",
    "model": "string",
    "gasBottleVolume": 0,
    "gasBottleExaminationDate": "2025-09-08T08:44:57.189Z",
    "gasBottleNextExaminationDate": "2025-09-08T08:44:57.189Z",
    "active": true,
    "vehicleParams": [
      {
        "cardCode": 0,
        "vehicleId": 0,
        "number": "string",
        "model": "string",
        "gasBottleVolume": 0,
        "gasBottleExaminationDate": "2025-09-08T08:44:57.189Z",
        "gasBottleNextExaminationDate": "2025-09-08T08:44:57.189Z",
        "viewInReportFl": true
      }
    ]
  }
]
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

## Добавление настроек авто

`PUT`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/createVehicle>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                            | Type      | Required | Discription
|--------------------------------|-----------|:--------:|------------
| `id`                           | `long`    |   Yes    | Номер карты
| `contrCode`                    | `integer` |   Yes    | Номер карты
| `number`                       | `string`  |   Yes    | Номер автомобиля
| `model`                        | `string`  |   Yes    | Водитель
| `gasBottleVolume`              | `integer` |    No    | Объем газового баллона
| `gasBottleExaminationDate`     | `date`    |    No    | Дата освидетельствования баллона
| `gasBottleNextExaminationDate` | `date`    |    No    | Дата последующего освидетельствования баллона
| `active`                       | `boolen`  |   Yes    | Флаг разрешения заправки авто по карте
| `vehicleParams`                | `array`   |   Yes    | Параметры автомобилей
| `vehicleParams`/`cardCode`                     | `string`  | Номер карты
| `vehicleParams`/`vehicleId`                    | `decimal` | Номер автомобиля
| `vehicleParams`/`number`                       | `decimal` | Номер автомобиля
| `vehicleParams`/`model`                        | `string`  | Водитель
| `vehicleParams`/`gasBottleVolume`              | `integer` | Объем газового баллона
| `vehicleParams`/`gasBottleExaminationDate`     | `date`    | Дата освидетельствования баллона
| `vehicleParams`/`gasBottleNextExaminationDate` | `date`    | Дата последующего освидетельствования баллона
| `vehicleParams`/`viewInReportFl`               | `boolean` | Отражать в отчетах номер авто

Пример запроса

```json
[
  {
    "id": 0,
    "contrCode": 0,
    "number": "string",
    "model": "string",
    "gasBottleVolume": 0,
    "gasBottleExaminationDate": "2025-09-08T08:44:57.189Z",
    "gasBottleNextExaminationDate": "2025-09-08T08:44:57.189Z",
    "active": true,
    "vehicleParams": [
      {
        "cardCode": 0,
        "vehicleId": 0,
        "number": "string",
        "model": "string",
        "gasBottleVolume": 0,
        "gasBottleExaminationDate": "2025-09-08T08:44:57.189Z",
        "gasBottleNextExaminationDate": "2025-09-08T08:44:57.189Z",
        "viewInReportFl": true
      }
    ]
  }
]
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

## Добавление настроек авто

`PUT`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/deleteVehicle>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                            | Type      | Required | Discription
|--------------------------------|-----------|:--------:|------------
| `id`                           | `long`    |   Yes    | Номер карты
| `contrCode`                    | `integer` |   Yes    | Номер карты
| `number`                       | `string`  |   Yes    | Номер автомобиля
| `model`                        | `string`  |   Yes    | Водитель
| `gasBottleVolume`              | `integer` |    No    | Объем газового баллона
| `gasBottleExaminationDate`     | `date`    |    No    | Дата освидетельствования баллона
| `gasBottleNextExaminationDate` | `date`    |    No    | Дата последующего освидетельствования баллона
| `active`                       | `boolen`  |   Yes    | Флаг разрешения заправки авто по карте
| `vehicleParams`                | `array`   |   Yes    | Параметры автомобилей
| `vehicleParams`/`cardCode`                     | `string`  | Номер карты
| `vehicleParams`/`vehicleId`                    | `decimal` | Номер автомобиля
| `vehicleParams`/`number`                       | `decimal` | Номер автомобиля
| `vehicleParams`/`model`                        | `string`  | Водитель
| `vehicleParams`/`gasBottleVolume`              | `integer` | Объем газового баллона
| `vehicleParams`/`gasBottleExaminationDate`     | `date`    | Дата освидетельствования баллона
| `vehicleParams`/`gasBottleNextExaminationDate` | `date`    | Дата последующего освидетельствования баллона
| `vehicleParams`/`viewInReportFl`               | `boolean` | Отражать в отчетах номер авто

Пример запроса

```json
[
  {
    "id": 0,
    "contrCode": 0,
    "number": "string",
    "model": "string",
    "gasBottleVolume": 0,
    "gasBottleExaminationDate": "2025-09-08T08:44:57.189Z",
    "gasBottleNextExaminationDate": "2025-09-08T08:44:57.189Z",
    "active": false,
    "vehicleParams": [
      {
        "cardCode": 0,
        "vehicleId": 0,
        "number": "string",
        "model": "string",
        "gasBottleVolume": 0,
        "gasBottleExaminationDate": "2025-09-08T08:44:57.189Z",
        "gasBottleNextExaminationDate": "2025-09-08T08:44:57.189Z",
        "viewInReportFl": true
      }
    ]
  }
]
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### Получить информацию о баннере

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/banners>

Заголовки

* `Authorization`: `Bearer <токен>`

Структура ответа

| Key             | Type      | Description
|-----------------|-----------|------------
| `doseForPeriod` | `decimal` | Доза за период
| `benefit`       | `decimal` | Выгода
| `summForPeriod` | `decimal` | Сумма за период
| `periodBegin`   | `date`    | Начало периода
| `periodEnd`     | `date`    | Конец периода

```json
[
{
"doseForPeriod": 0,
"benefit": 0,
"summForPeriod": 0,
"periodBegin": "2025-09-08T10:47:45.097Z",
"periodEnd": "2025-09-08T10:47:45.097Z"
}
]
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

## Получить информацию о баннере

`PUT`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/banners>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                | Type      | Required | Discription
|--------------------|-----------|:--------:|------------
| `contractId`       | `integer` |    No    | Номер договора
| `contractIssuerId` | `integer` |   Yes    | ID эмитента договора
| `viewDate`         | `date`    |    No    | Дата отображения

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### AcceptanceCertificate document

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Acceptance>

Заголовки

* `Authorization`: `Bearer <токен>`

Параметры запроса

| Key        | Type     |Discription
|------------|----------|------------
| `date`     | `date`   | Дата
| `filename` | `string` | Filename

Структура ответа

byte[]

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### AcceptanceCertificate. Get filename list.

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/AcceptanceList>

Заголовки

* `Authorization`: `Bearer <токен>`

Параметры запроса

| Key        | Type     |Discription
|------------|----------|------------
| `date`     | `date`   | Дата

Структура ответа

ArrayList

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 400  | Bad request
| 401  | Unauthorized
| 500  | Internal Server Error

### Получить опции настроек по умолчанию

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Catalog/options>

Заголовки

* `Authorization`: `Bearer <токен>`

Структура ответа

| Key       | Type      | Description
|-----------|-----------|------------
| `percent` | `decimal` | Процент

```json
{
"percent": 5
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 401  | Unauthorized
| 500  | Internal Server Error

### Получить информацию о валюте по умолчанию

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Catalog/currency>

Заголовки

* `Authorization`: `Bearer <токен>`

Структура ответа

| Key          | Type      | Description
|--------------|-----------|------------
| `code`       | `integer` | Код валюты
| `letterCode` | `string`  | Буквенный код валюты
| `name`       | `string`  | Наименование валюты
| `symbol`     | `string`  | Символьный код валюты

```json
{
  "code": 840,
  "letterCode": "USD",
  "name": "Доллар США",
  "symbol": "USD"
}
```

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 401  | Unauthorized
| 500  | Internal Server Error

### Акт сверки

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/CheckAccount>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                | Type      | Required | Description
|--------------------|-----------|:--------:|------------
| `startDate`        | `date`    |   Yes    | Дата начала периода в формате `MM-DD-YYYY`
| `endDate`          | `date`    |   Yes    | Дата конца периода в формате `MM-DD-YYYY`

Пример запроса

```json
{
  "startDate": "2025-09-08T06:47:06.839Z",
  "endDate": "2025-09-09T06:47:06.839Z"
}
```

Структура ответа

| Key                                       | Type     | Description
|-------------------------------------------|----------|------------
| `periodBegin`                             | `date`   | Дата начала периода 
| `periodEnd`                               | `date`   | Дата окончания периода
| `memberCode`                              | `long`   | Код эмитента
| `memberName`                              | `string` | Наименование эмитента
| `memberUnp`                               | `string` | УНП эмитента
| `contractCode`                            | `long`   | Номер договора
| `contractBeginDate`                       | `date`   | Дата старта договора
| `contractName`                            | `string` | Наименование договора
| `сontractUnp`                             | `string` | УНП договора
| `checkAccountTableRowData`                | `array`  | Счета договора
| `checkAccountTableRowData`/`documentDate` | `string` | Дата документа
| `checkAccountTableRowData`/`description`  | `string` | Наименование документа
| `checkAccountTableRowData`/`debet`        | `double` | Дебет
| `checkAccountTableRowData`/`credit`       | `double` | Кредит

Пример успешного ответа

```json
{
  "periodBegin": "2025-09-08T11:02:28.062Z",
  "periodEnd": "2025-09-08T11:02:28.062Z",
  "memberCode": 0,
  "memberName": "string",
  "memberUnp": "string",
  "contractCode": 0,
  "contractBeginDate": "2025-09-08T11:02:28.062Z",
  "contractName": "string",
  "contractUnp": "string",
  "checkAccountTableRowData": [
    {
      "documentDate": "string",
      "description": "string",
      "debet": 0,
      "credit": 0
    }
  ]
}
```

HTTP статусы ответа

| Code | Description |
|------|-------------|
| 200  | OK          |
| 400  | Bad request |
| 401  | Unauthorized |
| 500  | Internal Server Error |

### Получить шаблон договора

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/ContractTemplate>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key     | Type     | Required | Description
|---------|----------|:--------:|------------
| `name`  | `string` |   Yes    | Наименование
| `value` | `string` |   Yes    | Значение

Пример запроса

```json
[
  {
    "name": "string",
    "value": "string"
  }
]
```

Структура ответа

| Key            | Type      | Description
|----------------|-----------|------------
| `templateType` | `integer` | Тип шаблона
| `fileName`     | `string`  | Имя файла
| `text`         | `string`  | Текст шаблона
| `binaryData`   | `bytes`   | Шаблон

Пример успешного ответа

```json
{
  "templateType": 0,
  "fileName": "string",
  "text": "string",
  "binaryData": "string"
}
```

HTTP статусы ответа

| Code | Description |
|------|-------------|
| 200  | OK          |
| 400  | Bad request |
| 401  | Unauthorized |
| 500  | Internal Server Error |

### Акт сверки. Процедура обновления флага.

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/ElectronicCheck>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                         | Type      | Required | Description
|-----------------------------|-----------|:--------:|------------
| `isElectronicCheck`         | `integer` |   Yes    | Флаг о согласии на электронные чеки
| `noReminderElectronicCheck` | `integer` |   Yes    | Флаг напоминания

Пример запроса

```json
{
  "isElectronicCheck": 0,
  "noReminderElectronicCheck": 0
}
```

Структура ответа

int

HTTP статусы ответа

| Code | Description |
|------|-------------|
| 200  | OK          |
| 400  | Bad request |
| 401  | Unauthorized |
| 500  | Internal Server Error |

### Сохранение настроек оповещения о минимальной сумме на счете.

`PUT`: <https://ssl.beloil.by/rcp/i/api/v3/Contract/NoticeInfo>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key                        | Type      | Required | Description
|----------------------------|-----------|:--------:|------------
| `emitent`                  | `integer` |   Yes    | Номер эмитента
| `contrCode`                | `integer` |   Yes    | Номер договора
| `minimumBalanceNoticeFlag` | `bool`    |   Yes    | Флаг отправки уведомления о достижении минимального баланса
| `minimumBalanceSumm`       | `decimal` |   Yes    | Сумма минимального баланса

Пример запроса

```json
{
  "emitent": 0,
  "contrCode": 0,
  "minimumBalanceNoticeFlag": true,
  "minimumBalanceSumm": 0
}
```

Структура ответа

int

HTTP статусы ответа

| Code | Description |
|------|-------------|
| 200  | OK          |
| 400  | Bad request |
| 401  | Unauthorized |
| 500  | Internal Server Error |

### Движение денежных средств по договору

`POST`: <https://ssl.beloil.by/rcp/i/api/v3/PaymentReport>

Заголовки

* `Authorization`: `Bearer <токен>`
* `Content-Type`: `application/json`

Структура запроса

| Key         | Type   | Required | Description
|-------------|--------|:--------:|------------
| `startDate` | `date` |   Yes    | Дата начала периода в формате `MM-DD-YYYY`
| `endDate`   | `date` |   Yes    | Дата конца периода в формате `MM-DD-YYYY`
| `contractId`       | `integer` |   Yes    | Номер договора
| `contractIssuerId` | `integer` |   Yes    | ID эмитента договора
| `cardNumber`       | `integer` |   Yes    | Номер топливной карты. Если значение меньше либо равно нулю, тогда отчет по всем картам договора
| `subDivisnNumber`  | `integer` |   Yes    | Номер подразделения. Если значение меньше нуля, тогда отчет по всем подразделениям договора
| `flChoice`         | `integer` |   Yes    | Параметр `FlChoice` - флаг выбора информации: `1` - топливо, `2` - оплата дорог, `4` - иные товары (услуги), `3` - топливо и оплата дорог, `5` - топливо и иные товары (услуги), `6` -  оплата дорог и иные товары (услуги), `7` - топливо, оплата дорог и иные товары (услуги)
| `author`           | `string`  |    No    | Автор изменений 
| `emtCodeFrm`       | `integer` |    No    | Код эмитента, если меньше нуля, то по всем подразделениям
| `azsCode`          | `integer` |    No    | Номер азс, если меньше нуля, то по всем подразделениям

Пример запроса

```json
{
  "startDate": "2025-09-08T07:05:28.924Z",
  "endDate": "2025-09-08T07:05:28.924Z",
  "contractId": 0,
  "contractIssuerId": 0,
  "flChoice": 0,
  "cardNumber": 0,
  "subDivisnNumber": 0,
  "discountCode": 0,
  "author": "string",
  "emtCodeFrm": 0,
  "azsCode": 0
}
```

Структура ответа

| Key            | Type      | Description
|----------------|-----------|------------
| `PayDate` | `integer` | Дата платежа
| `PayDocument`     | `string`  | Номер документа выписки 
| `PaySum`         | `string`  | Сумма платежа

Пример успешного ответа

```json
[
  {
    "payDate": "2025-09-08T12:01:31.099Z",
    "payDocument": "string",
    "paySum": 0
  }
]
```

HTTP статусы ответа

| Code | Description |
|------|-------------|
| 200  | OK          |
| 400  | Bad request |
| 401  | Unauthorized |
| 500  | Internal Server Error |

### Получение файлов отчетов. Параметры:
### doctype - тип документа. 1 - НТУ-АЗС; 2 - Количество и стоимость отпущенный нефлепродуктов, товаров и услуг   
### date - дата отчета в формате ггггмм

`GET`: <https://ssl.beloil.by/rcp/i/api/v3/Report>

Заголовки

* `Authorization`: `Bearer <токен>`

Параметры запроса

| Key       | Type      |Discription
|-----------|-----------|------------
| `doctype` | `integer` | Тип документа
| `date`    | `string`  | Дата

Структура ответа

byte[]

HTTP статусы ответа

| Code | Description
|------|------------
| 200  | OK
| 401  | Unauthorized
| 500  | Internal Server Error
