# Проект для автоматизации работы резервуара с водой
![sc](https://badgen.net/github/release/ErilovNikita/water-barell-hass)

Проект создан для автоматизаци контроля уровня воды в бочке, а так же для автоматического наполнения ее без участия человека.

## Оглавление
- [Аппаратная часть](#Аппаратная-часть)
    - [Материалы](#Материалы)
    - [Сборка](#Сборка)
- [Программая часть](#Программая-часть)
    - [Функциионал](#Функциионал)
    - [Прошивка](#Прошивка)
    - [Настройка Home Assistant](#Настройка-Home-Assistant)
- [Дополнительные скриншоты](#Дополнительные-скриншоты)

## Аппаратная часть
### Материалы
- Микроконтроллер NodeMCU v3 x 1
- Ультазвуковой дальномер HC-SR04 x 1
- 4-х пиновые разьемы для удобства x 2
- Зажим болтовой 2 пин х 1
- Светодиод зеленый х 1
- Резистор 1кОм х 1
- Резистор 2кОм х 1
- Резистор 10кОм х 1
- Транзистор 2n2222 x 1
- Диод LL4148 x 1
- Россыпь проводов

### Сборка 
Схема подключения NodeMCU
![Схема подключения NodeMCU ](/scheme/scheme3.png "Схема подключения")

Схема подключения ESP-01
![Схема подключения ESP-01 ](/scheme/scheme2.png "Схема подключения")

Для удобства я нарисовал всю схему по поключению и разработал печатную плату на известном сайте
Для более удобной сборки можно воспользоваться grabber файлом из данного проекта ESP-01 👉 [Gerber_water-barrel-hass.zip](/scheme/Gerber_water-barrel-hass.zip)

Если вы будете заказывать плату на базе ESP-01 из репозитория вы получите что-то наподобии этого
![Готовая плата ESP-01](/scheme/scheme1.png "Готовая плата")


## Программная часть

### Функциионал
- Есть управление минимального уровня воды
- Автоматическое управление насосами
- Контроль за уровнем воды
- Карточка для lovelace

### Прошивка
Программаная часть устройства строиться на базе прошивки [ESPHome](https://esphome.io)

Исходный код прошивки можно найти тут 👉 [water-barrel.yaml](/esphome/water-barrel.yaml)

Добавляем новый проект в ESPHome и прошиваем новое устройство

### Настройка Home Assistant
Нам необходимо добавить package с автоматизациями и дополнительными обьектами 
1. Скачиваем пакет [water-barrel-package.yaml](/homeassistant/water-barrel-package.yaml)
1. Добавляем его в папку */config/packages/*
1. Обязательно необходимо заменить переменные в коде на свои, для этого смотрим таблицу ниже 

    | Переменная в коде        | Описание            |
    |--------------------------|---------------------|
    | switch.nasosy            | Выключатель насосов **(Если используется внешнее реле. В случае с использованием схемы на nodeMCU менять не нужно!)** |
    | sensor.ultrasonic_sensor | Ультразвуковой дальномер, имя обьекта из прошивки esp |


Для визуализиции данных необходимо вывести обьекты на lovelace, пример карточки можно увидеть чуть ниже

![](/screenshots/screen1.png)
``` yaml
type: vertical-stack
cards:
  - type: gauge
    entity: sensor.template_barrel_water_level
    name: Уровень воды
    needle: true
    severity:
      green: 80
      yellow: 20
      red: 0
  - type: entities
    entities:
      - entity: sensor.template_barrel
      - entity: switch.nasosy
      - entity: sensor.template_barrel_water_level
      - entity: input_number.barrel_max_level
      - entity: input_number.barrel_min_level
      - entity: input_boolean.barrel_auto_level
  - hours_to_show: 24
    graph: line
    type: sensor
    entity: sensor.template_barrel_water_level
    detail: 1
```

## Дополнительные скриншоты
![](/screenshots/screen3.png)
