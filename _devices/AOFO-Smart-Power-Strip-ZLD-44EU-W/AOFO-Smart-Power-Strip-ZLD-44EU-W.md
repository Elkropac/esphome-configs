---
title: AOFO Smart Power Strip ZLD-44EU-W
date-published: 2020-08-19
type: plug
standard: eu
---

1. TOC
{:toc}

## Device Information

Wifi Smart Power Strip 4 EU Outlets Plug with 4 USB Charging Ports.

Max power: 2400W

Max USB power: 4A

Chip: TYWE2S

(https://www.aliexpress.com/item/32939654903.html)

## Product images

![alt text](/20200816_135301.jpg "AOFO Smart Power Strip ZLD-44EU-W inside 1")
![alt text](/20200819_112354.jpg "AOFO Smart Power Strip ZLD-44EU-W inside 2")

## Flashing

As tuya_convert did not work (new PSK format), it is needed to flash it by hand. It has 6 screws with tri-angle heads. Once inside, you have to remove PCB with sockets to get to other side. Then you solder wires to pins on chip.

![alt text](/20200819_115941.jpg "while flashing")

## GPIO Pinout

| Pin     | Function                           |
|---------|------------------------------------|
| GPIO01  | Power LED (inverted)               |
| GPIO03  | Push Button                        |
| GPIO04  | Relay Socket #2                    |
| GPIO05  | Relay Socket #1                    |
| GPIO12  | Relay Socket #3                    |
| GPIO13  | Relay Socket #4                    |
| GPIO14  | Relay USB ports (inverted)         |

## Basic Configuration

```yaml
substitutions:
  dev_name: aofo_socket_001
  sensor_wifi_signal_update_interval: 600s
  sensor_uptime_update_interval: 600s

esphome:
  name: ${dev_name}
  platform: ESP8266
  board: esp01_1m
  #power on USB ports on boot
  on_boot:
    then:
      - switch.turn_on:
          id: relay_05
wifi:
  ssid: "ssid"
  password: "password"

# Enable logging
logger:
  level: DEBUG
  
# Enable Home Assistant API
api:
  password: 'apipassword'

ota:
  password: 'otapassword'

binary_sensor:
  - platform: gpio
    pin:
      number: GPIO03
      mode: INPUT_PULLUP
      inverted: True
    name: "${dev_name}_button"
    #toggle all sockets
    on_press:
      - switch.toggle: relay_01
      - switch.toggle: relay_02
      - switch.toggle: relay_03
      - switch.toggle: relay_04

switch:
  - platform: gpio
    name: "${dev_name}_relay_01"
    pin: GPIO05
    id: relay_01
  - platform: gpio
    name: "${dev_name}_relay_02"
    pin: GPIO04
    id: relay_02
  - platform: gpio
    name: "${dev_name}_relay_03"
    pin: GPIO12
    id: relay_03
  - platform: gpio
    name: "${dev_name}_relay_04"
    pin: GPIO13
    id: relay_04
  - platform: gpio
    name: "${dev_name}_relay_05"
    pin: GPIO14
    id: relay_05
    inverted: True
  - platform: shutdown
    name: "${dev_name}_shutdown"
  - platform: restart
    name: "${dev_name}_restart"

status_led:
  pin:
    number: GPIO01
    inverted: yes

sensor:
  - platform: wifi_signal
    name: "${dev_name}_wifi_signal"
    update_interval: "${sensor_wifi_signal_update_interval}"
  - platform: uptime
    name: "${dev_name}_uptime"
    update_interval: "${sensor_uptime_update_interval}"

text_sensor:
  - platform: version
    name: "${dev_name}_version"
```
