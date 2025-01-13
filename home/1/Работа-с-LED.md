---
title: Работа с LED
description: 
published: true
date: 2025-01-13T07:07:25.849Z
tags: 
editor: markdown
dateCreated: 2025-01-13T07:07:25.849Z
---

## Навигация
Откройте браузер и введите адрес: http://routerich.lan.
В разделе меню выберите **Система → Индикаторы**

Для быстрого перехода воспользуйтесь ссылкой: http://routerich.lan/cgi-bin/luci/admin/system/leds.

## Работа с LED:
На странице "LED индикации" представлен список всех доступных индикаторов, которые можно настроить. Каждый из них может отображать состояние определённого устройства или интерфейса.

**Структура таблицы:**
- **Название**
Название профиля индикации.
- **Имя LED**
Сопоставление LED индикатора и сетевого интерфейса. Пример: `blue:lan-1`:
**blue** - имя LED индикатора.
**lan-1** - имя связанного сетевого интерфейса.
- **Триггер**
Программный механизм, задающий условия или события, при которых LED индикатор включается, выключается или меняет свое состояние.

### Настройка LED
Для настройки нажмите кнопку <kbd>ИЗМЕНИТЬ</kbd> напротив нужного профиля, или добавьте новый нажав <kbd>ДОБАВИТЬ ДЕЙСТВИЕ LED</kbd>.

**Основные поля всплывающего окна:**
**Название:** оставьте значение по умолчанию или укажите собственное имя профиля.
**Имя LED:** выберите соответствующий LED индикатор для его настройки.
**Триггер:** задайте нужный триггер для управления состоянием индикатора.

Нажмите <kbd>СОХРАНИТЬ</kbd>, затем нажмите <kbd>ПРИМЕНИТЬ</kbd> для записи настроек в устройство.

### Доступные триггеры
**Всегда включен (kernel: default-on)** - индикатор постоянно горит
**Интервал сердцебиения (kernel: heartbeat)** - индикатор мигает с фиксированной частотой, имитируя биение сердца
**Активность сетевого устройства (kernel: netdev)** - индикатор мигает при передаче или получении данных на заданном интерфейсе
**Всегда выключен (kernel: none)** - индикатор постоянно отключён
**Произвольный интервал мигания (kernel: timer)** - индикатор мигает с настраиваемой частотой

## Спискок доступных LED
### AX3000
Список LED для роутера **Routerich AX3000**
| Имя LED:       | Отвечает за:    |
|----------------|-----------------|
| `blue:lan-1`   | Синий LAN1      |
| `blue:lan-2`   | Синий LAN2      |
| `blue:lan-3`   | Синий LAN3      |
| `blue:mesh`    | Синий MESH      |
| `blue:power`   | Синий POWER     |
| `blue:wan`     | Синий WAN       |
| `red:wan`      | Красный WAN     |
| `blue:wlan-24` | Синий Wi-Fi     |
| `red:wlan-50`  | Красный Wi-Fi   |
| `mt76-phy0`    | Диапазон 2.4Ghz |
| `mt76-phy1`    | Диапазон 5Ghz   |


## Скрипты
### Выключение всех индикаторов
> Используем протокол SSH или приложение "LUCI Терминал"
{.is-warning}

Необходимо ввести последовательно следующие команды:
```bash
uci set system.@led[0].trigger='none'
uci set system.@led[1].trigger='none'
uci set system.@led[2].trigger='none'
uci set system.@led[3].trigger='none'
uci set system.@led[4].trigger='default-on'
uci set system.@led[5].trigger='none'
uci set system.@led[6].trigger='none'
uci commit
service led restart
```

После чего, все индикаторы будут отключены.

### Включение всех индикаторов
> Используем протокол SSH или приложение "LUCI Терминал"
{.is-warning}

Необходимо ввести последовательно следующие команды:
```bash
uci set system.@led[0].trigger='netdev'
uci set system.@led[1].trigger='netdev'
uci set system.@led[2].trigger='netdev'
uci set system.@led[3].trigger='netdev'
uci set system.@led[4].trigger='netdev'
uci set system.@led[5].trigger='phy0tpt'
uci set system.@led[6].trigger='phy1tpt'
uci commit
service led restart
```
После чего, все индикаторы будут обратно включены.

### Отключение по расписанию
Данный скрипт произведёт выключение индикации в 23:30, а включение в 7:00.

> Используем протокол SSH или приложение "LUCI Терминал"
{.is-warning}

Cоздаем файл по пути **/etc/ledcontrol.sh** комадной:
`touch /etc/ledcontrol.sh`
Открываем файл текстовым редактором:
`nano /etc/ledcontrol.sh`
И вставляем содержимое таблицы ниже:
```bash
#!/bin/sh

case "$1" in
        on)
            uci set system.@led[0].trigger='netdev'
            uci set system.@led[1].trigger='netdev'
            uci set system.@led[2].trigger='netdev'
            uci set system.@led[3].trigger='netdev'
            uci set system.@led[4].trigger='netdev'
            uci set system.@led[5].trigger='phy0tpt'
            uci set system.@led[6].trigger='phy1tpt'
            uci commit
            service led restart
            ;;

        off)
            uci set system.@led[0].trigger='none'
            uci set system.@led[1].trigger='none'
            uci set system.@led[2].trigger='none'
            uci set system.@led[3].trigger='none'
            uci set system.@led[4].trigger='default-on'
            uci set system.@led[5].trigger='none'
            uci set system.@led[6].trigger='none'
            uci commit
            service led restart
            ;;

        *)
            echo "Usage: $0 {on|off}"

            exit 1
            
esac
```

Для сохранения файла, используйте комбинацию <kbd>CTRL+S</kbd>, для закрытия текстового редактора используйте <kbd>CTRL+X</kbd>.

Затем в **планировщик заданий** расположенный в [**Система → Планировщик**](http://routerich.lan/cgi-bin/luci/admin/system/crontab),
До строки `exit 0`, нужно добавить следующие содержимое:
```
30 23 * * * /etc/ledcontrol.sh off
00 7 * * * /etc/ledcontrol.sh on

exit 0
```

> За адаптацию скрипта, спасибо [**NyXzOr**](https://4pda.to/forum/index.php?showuser=3288424)