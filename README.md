# Postman
Тестовое задание для сайта https://partner.agentapp.ru/ по созданию договора страхования.
Для этого потребуется получить токен, создать водителя, владельца, страхователя, автомобиль, объединить их в объект страхования, создать договор.

## 0. Создаем коллекцию в Postman, например AgentApp
В variables коллекции создаем переменную host: partner.agentapp.ru и api_version: v1

## 1. Получаем токен
Отпправляем POST запрос на https://{{host}}/{{api_version}}/users/obtain-token
с тестовыми username и password:
```json
{
    "username":"qa@qa.qa",
    "password":"111"
}
```
Полученный в ответе токен записываем в переменные коллекции с помощью скрипта JS (вкладка Tests, скрипт выполнится после запроса):
```javascript
var jsonData = JSON.parse(responseBody);
pm.collectionVariables.set("tokenn", jsonData.token);
```

## 2. Создаем водителя
POST на https://{{host}}/{{api_version}}/insured_objects/drivers
В хедерах прокидываем полученный токен в формате Authorization: Token {{tokenn}}
В body передаем информацию о водителе (свои данные для конфиденциальности я немного изменил):
```json
{
  "first_name": "Иван",
  "last_name": "Гудырев",
  "patronymic": "Евгеньевич",
  "birth_date": "1987-09-13",
  "driving_experience_started": "2009-06-25",
  "driver_licenses": [
    {
      "credential_type": "DRIVER_LICENSE",
      "number": "619609",
      "series": "7039",
      "issue_date": "2009-06-25"
    }
  ]
}
```
Из ответа получаем id_driver:
```javascript
var a = JSON.parse(responseBody);
pm.collectionVariables.set("id_driver", a.id);
```

## 3. Создаем собственника
POST на https://{{host}}/{{api_version}}/insured_objects/owners/natural_persons
В хедерах прокидываем полученный токен в формате Authorization: Token {{tokenn}}
Body - информация о собственнике:
```json
{
  "last_name": "Гурбановская",
  "first_name": "Леся",
  "patronymic": "Богдановна",
  "birth_date": "1976-06-15",
  "credential": [
    {
      "credential_type": "RUSSIAN_INTERNAL_PASSPORT",
      "issue_date": "2017-03-08",
      "issue_point": "УФМС РОССИИ ПО ТОМСКОЙ ОБЛАСТИ",
      "issue_point_code": "700-002",
      "number": "865497",
      "series": "6919"
    }
  ],
  "address": [
    {
      "address_query": "г Томск, ул Говорова, д 62 к 30",
      "address_type": "LEGAL_ADDRESS",
      "region_kladr_id": "7000000000000",
      "city_kladr_id": "7000000100000"
    },
    {
      "address_query": "г Томск, ул Говорова, д 62 к 30",
      "address_type": "ACTUAL_ADDRESS",
      "region_kladr_id": "7000000000000",
      "city_kladr_id": "7000000100000"
    }
  ]
}
```
Из ответа получаем id_owner:
```javascript
var b = JSON.parse(responseBody);
pm.collectionVariables.set("id_owner", b.person);
```

## 4. Создаем страхователя
POST на https://{{host}}/{{api_version}}/insured_objects/insurants/natural_persons
В хедерах прокидываем полученный токен в формате Authorization: Token {{tokenn}}
Body - информация о страхователе:
```json
{
  "last_name": "Гурбановская",
  "first_name": "Леся",
  "patronymic": "Богдановна",
  "birth_date": "1976-06-15",
  "credential": [
    {
      "credential_type": "RUSSIAN_INTERNAL_PASSPORT",
      "issue_date": "2019-06-27",
      "issue_point": "УФМС РОССИИ ПО ТОМСКОЙ ОБЛАСТИ",
      "issue_point_code": "700-002",
      "number": "865497",
      "series": "6919"
    }
  ],
  "address": [
    {
      "address_query": "г Томск, ул Говорова, д 62 к 30",
      "address_type": "LEGAL_ADDRESS",
      "region_kladr_id": "7000000000000",
      "city_kladr_id": "7000000100000"
    },
    {
      "address_query": "г Томск, ул Говорова, д 62 к 30",
      "address_type": "ACTUAL_ADDRESS",
      "region_kladr_id": "7000000000000",
      "city_kladr_id": "7000000100000"
    }
  ],
   "contact": [
        {
            "contact_type": "PHONE",
            "data": "+79992061450"
        },
        {
            "contact_type": "EMAIL",
            "data": "natahodyr@yandex.ru"
        }
   ]
}
```
Из ответа получаем id_insurant:
```javascript
var c = JSON.parse(responseBody);
pm.collectionVariables.set("id_insurant", c.person);
```

## 5. Создаем автомобиль
POST на https://{{host}}/{{api_version}}/insured_objects/cars
В хедерах прокидываем полученный токен в формате Authorization: Token {{tokenn}}
Body - информация об автомобиле:
```json
{
  "car_model_id": 261020578,
  "engine_power": 110,
  "vin_number": "SJNFCAE11U1230880",
  "number_plate": "P617EC70",
  "manufacturing_year": 2007,
  "max_mass": null,
  "has_trailer": false,
  "credential": [
    {
      "credential_type": "VEHICLE_REGISTRATION",
      "issue_date": "2010-11-01",
      "number": "204326",
      "series": "70PM"
    }
  ]
}
```
Из ответа получаем id_vehicle:
```javascript
var d = JSON.parse(responseBody);
pm.collectionVariables.set("id_vehicle", d.id);
```

## 6. Создаем объект страхования - объединяем наши сущности
POST на https://{{host}}/{{api_version}}/insured_objects/
В хедерах прокидываем полученный токен в формате Authorization: Token {{tokenn}}
Body - информация об автомобиле:




