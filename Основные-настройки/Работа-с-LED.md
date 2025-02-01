---
title: Работа с LED
description: 
published: true
date: 2025-02-01T09:47:31.654Z
tags: 
editor: markdown
dateCreated: 2025-01-13T07:07:25.849Z
---

## Навигация
Откройте браузер и введите адрес: http://routerich.lan
В разделе меню выберите **Система → Индикаторы**

Для быстрого перехода воспользуйтесь ссылкой: http://routerich.lan/cgi-bin/luci/admin/system/leds

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

Создайте файл скрипта и сделайте его исполняемым:
`touch /etc/led_off.sh & chmod +x /etc/led_off.sh`
Откройте файл в текстовом редакторе:
`nano /etc/led_off.sh`
И вставьте следующее содержимое:
```bash
#!/bin/sh

echo "none" > /sys/class/leds/blue:lan-1/trigger
echo "none" > /sys/class/leds/blue:lan-2/trigger
echo "none" > /sys/class/leds/blue:lan-3/trigger
echo "none" > /sys/class/leds/blue:wan/trigger
echo "none" > /sys/class/leds/red:wan/trigger
echo "none" > /sys/class/leds/blue:wlan-24/trigger
echo "none" > /sys/class/leds/red:wlan-50/trigger
echo "none" > /sys/class/leds/blue:power/trigger
echo "default-on" > /sys/class/leds/red:wan/trigger
```

Чтобы скрипт выполнялся при включении устройства, добавьте команду в **rc.local**:
- Перейдите в [**Система -> Автозапуск**](http://routerich.lan/cgi-bin/luci/admin/system/startup)
- Затем перейдите во вкладку "**Запуск пакетов и служб пользователя при включении устройства**"

Вставьте строку ```/etc/led_off.sh``` для выполнения скрипта перед ```exit 0```:
```bash
# Put your custom commands here that should be executed once
# the system init finished. By default this file does nothing.

/etc/led_off.sh

exit 0
```

Теперь после перезагрузки все индикаторы будут отключены.

### Включение всех индикаторов
> Используем протокол SSH или приложение "LUCI Терминал"
{.is-warning}

Если вы ранее отключили LED-индикаторы и теперь хотите включить их на постоянной основе, удалите соответствующую строку из **rc.local**.

- Перейдите в [**Система -> Автозапуск**](http://routerich.lan/cgi-bin/luci/admin/system/startup)
- Затем перейдите во вкладку "**Запуск пакетов и служб пользователя при включении устройства**"

Удалите строку содержащую ```/etc/led_off.sh```, что-бы вид был следующий:
```bash
# Put your custom commands here that should be executed once
# the system init finished. By default this file does nothing.

exit 0
```

Теперь после перезагрузки все индикаторы будут обратно включены.

---

**Для временного включения всех LED-индикаторов до следующей перезагрузки** выполните следующие команды:
```bash
echo "netdev" > /sys/class/leds/blue:lan-1/trigger
echo "1" > /sys/class/leds/blue:lan-1/link
echo "1" > /sys/class/leds/blue:lan-1/tx
echo "1" > /sys/class/leds/blue:lan-1/rx
echo "lan1" > /sys/class/leds/blue:lan-1/device_name
echo "netdev" > /sys/class/leds/blue:lan-2/trigger
echo "1" > /sys/class/leds/blue:lan-2/link
echo "1" > /sys/class/leds/blue:lan-2/tx
echo "1" > /sys/class/leds/blue:lan-2/rx
echo "lan2" > /sys/class/leds/blue:lan-2/device_name
echo "netdev" > /sys/class/leds/blue:lan-3/trigger
echo "1" > /sys/class/leds/blue:lan-3/link
echo "1" > /sys/class/leds/blue:lan-3/tx
echo "1" > /sys/class/leds/blue:lan-3/rx
echo "lan3" > /sys/class/leds/blue:lan-3/device_name
echo "netdev" > /sys/class/leds/blue:wan/trigger
echo "1" > /sys/class/leds/blue:wan/link
echo "1" > /sys/class/leds/blue:wan/tx
echo "1" > /sys/class/leds/blue:wan/rx
echo "wan"  > /sys/class/leds/blue:wan/device_name
echo "netdev" > /sys/class/leds/red:wan/trigger
echo "1" > /sys/class/leds/red:wan/link
echo "wan"  > /sys/class/leds/red:wan/device_name
echo "phy0tpt" > /sys/class/leds/blue:wlan-24/trigger
echo "phy1tpt" > /sys/class/leds/red:wlan-50/trigger
echo "default-on" > /sys/class/leds/blue:power/trigger
```

### Отключение по расписанию
Данный скрипт произведёт выключение индикации в 23:30, а включение в 7:00.

> Используем протокол SSH или приложение "LUCI Терминал"
{.is-warning}

Создайте файл скрипта и сделайте его исполняемым:
`touch /etc/ledcontrol.sh & chmod +x /etc/ledcontrol.sh`
Откройте файл в текстовом редакторе:
`nano /etc/ledcontrol.sh`

И вставьте следующее содержимое:
```bash
#!/bin/sh

case "$1" in
        on)
            echo "netdev" > /sys/class/leds/blue:lan-1/trigger
            echo "1" > /sys/class/leds/blue:lan-1/link
            echo "1" > /sys/class/leds/blue:lan-1/tx
            echo "1" > /sys/class/leds/blue:lan-1/rx
            echo "lan1" > /sys/class/leds/blue:lan-1/device_name
            echo "netdev" > /sys/class/leds/blue:lan-2/trigger
            echo "1" > /sys/class/leds/blue:lan-2/link
            echo "1" > /sys/class/leds/blue:lan-2/tx
            echo "1" > /sys/class/leds/blue:lan-2/rx
            echo "lan2" > /sys/class/leds/blue:lan-2/device_name
            echo "netdev" > /sys/class/leds/blue:lan-3/trigger
            echo "1" > /sys/class/leds/blue:lan-3/link
            echo "1" > /sys/class/leds/blue:lan-3/tx
            echo "1" > /sys/class/leds/blue:lan-3/rx
            echo "lan3" > /sys/class/leds/blue:lan-3/device_name
            echo "netdev" > /sys/class/leds/blue:wan/trigger
            echo "1" > /sys/class/leds/blue:wan/link
            echo "1" > /sys/class/leds/blue:wan/tx
            echo "1" > /sys/class/leds/blue:wan/rx
            echo "wan"  > /sys/class/leds/blue:wan/device_name
            echo "netdev" > /sys/class/leds/red:wan/trigger
            echo "1" > /sys/class/leds/red:wan/link
            echo "wan"  > /sys/class/leds/red:wan/device_name
            echo "phy0tpt" > /sys/class/leds/blue:wlan-24/trigger
            echo "phy1tpt" > /sys/class/leds/red:wlan-50/trigger
            echo "default-on" > /sys/class/leds/blue:power/trigger
            ;;

        off)
            echo "none" > /sys/class/leds/blue:lan-1/trigger
            echo "none" > /sys/class/leds/blue:lan-2/trigger
            echo "none" > /sys/class/leds/blue:lan-3/trigger
            echo "none" > /sys/class/leds/blue:wan/trigger
            echo "none" > /sys/class/leds/red:wan/trigger
            echo "none" > /sys/class/leds/blue:wlan-24/trigger
            echo "none" > /sys/class/leds/red:wlan-50/trigger
            echo "none" > /sys/class/leds/blue:power/trigger
            echo "default-on" > /sys/class/leds/red:wan/trigger
            ;;

        *)
            echo "Usage: $0 {on|off}"

            exit 1

esac
```

Для сохранения файла, используйте комбинацию <kbd>CTRL+S</kbd>, для закрытия текстового редактора используйте <kbd>CTRL+X</kbd>.

Затем в **планировщик заданий** расположенный в [**Система → Планировщик**](http://routerich.lan/cgi-bin/luci/admin/system/crontab), вставьте следующее содержимое:
```
30 23 * * * /etc/ledcontrol.sh off
00 7 * * * /etc/ledcontrol.sh on
```

И нажмите <kbd>СОХРАНИТЬ</kbd>

> За пример скрипта, спасибо [**NyXzOr**](https://4pda.to/forum/index.php?showuser=3288424)