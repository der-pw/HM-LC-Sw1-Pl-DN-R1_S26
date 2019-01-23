# HM-LC-Sw1-Pl-DN-R1_S26     [![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)     [![GitHub issues](https://img.shields.io/github/issues/der-pw/HM-LC-Sw1-Pl-DN-R1_S26.svg)](https://github.com/der-pw/HM-LC-Sw1-Pl-DN-R1_S26/issues)

## Projekt befindet sich noch in der Evaluierungsphase! Diese Seite dient momentan nur dem Informationsaustausch. Platine daher noch nicht nachbauen!

Umbau des [Sonoff S26 Zwischenstecker](https://www.itead.cc/sonoff-s26-wifi-smart-plug.html) zum HM-LC-Sw1-Pl-DN-R1_S26

Dies basiert auf der hervorragenden Arbeit von [pa-pa](https://github.com/pa-pa/AskSinPP),  [Jérôme](https://github.com/jp112sdl/Beispiel_AskSinPP), sowie der Inspirationsquelle [stan23](https://github.com/stan23) mit seinem Umbau [OBI-Wifi-Stecker](https://github.com/stan23/HM-LC-Sw1-Pl-DN-R1_OBI) zu HM-LC-Sw1-Pl-DN-R1_OBI.

![Rückansicht HM-LC-Sw1-Pl-DN-R1_S26](https://github.com/der-pw/HM-LC-Sw1-Pl-DN-R1_S26/raw/master/img/PCB_3D_back.jpg "Rückansicht HM-LC-Sw1-Pl-DN-R1_S26")

## Programmierung des ATMEGA
Ich verwende einen Arduino als ISP, daher beziehen sich die Beispiele darauf.

### Fuses
Ext: 0xFF High: 0xD2 Low: 0xE2

`<Pfad zu> .\avrdude -C ..\etc\avrdude.conf -v -p m328p -P com11 -c stk500v1 -b 19200 -U lfuse:w:0xE2:m -U hfuse:w:0xD2:m -U efuse:w:0xFF:m`

### Bootloader
Hier kann aus der Arduino IDE, der Standard-Bootloader für ATmega328P (3,3 V, 8 MHz) genommen werden.

`<Pfad zu> .\avrdude -C ..\etc\avrdude.conf -v -p m328p -P com11 -c stk500v1 -b 19200 -U flash:w:..\..\..\arduino\avr\bootloaders\atmega\ATmegaBOOT_168_atmega328_pro_8MHz.hex`

### Details zum S26
 
 Anders als beim Sonoff Basic oder Sonoff S20, befindet sich der ESP8266 auf einer zweiten Platine, die in einem Slot mit der Hauptplatine verlötet ist.
 Es ist also möglich, das WiFi-Modul durch eine selbst entwickelte Platine auszutauschen um den S26 auf Basis von AskSin++ zu betreiben.
 
 #### Pinbelegung und Vergleich
 
Eine Übersicht über die Belegung der einzelnen Anschlüsse.
 
 Bez. PCB | WiFi-Mod  | Funktion    | ATMEGA 
----------|-----------|-------------|--------
 J1       | V         | 3V3         | 3V3
 J2       | G         | GND         | GND
 J3       | GPIO 12   | Relais      | GPIO 8
 J4       | GPIO 0    | Button      | GPIO 4
 J5       | GPIO 13   | Status LED  | GPIO 5
 J10      | RX        | ser. RX     | NC
 
- Die blaue Status-LED schaltet bei LOW-Signal an J5 ein. Damit sie also nur bei Aktion leuchtet (blinkt) muss das Ausgangssignal am GPIO5 des ATMEGA invertiert werden. Asksinn++ bietet mit `sdev.led().invert(true);` die Möglichkeit, dies per Software zu lösen.
Die rote Indikator-LED für ON/OFF schaltet zusammen mit dem Relais.

- Dem Relais an J5 ist ein S8550 Transistor in SMD-Bauweise vorgeschaltet. Der GPIO des ATMEGA wird bei angezogenem Relais mit ca. 5mA belastet

- Der Button verbindet J4 mit GND. Es ist also möglich, über den internen Pullupwiderstand, den Tastendruck auszuwerten.

#### S26 Hauptplatine
![Ansicht S26 Hauptplatine](https://github.com/der-pw/HM-LC-Sw1-Pl-DN-R1_S26/raw/master/img/S26_main.jpg "Ansicht S26 Hauptplatine")

#### Vorderansicht S26 WiFi-Modul 
![Ansicht S26 WiFi-Modul Vorderseite](https://raw.githubusercontent.com/der-pw/HM-LC-Sw1-Pl-DN-R1_S26/master/img/S26_wifi_front.jpg "Ansicht S26 WiFi-Modul Vorderseite")

#### Rückansicht S26 WiFi-Modul 
![Ansicht S26 WiFi-Modul Rückseite](https://raw.githubusercontent.com/der-pw/HM-LC-Sw1-Pl-DN-R1_S26/master/img/S26_wifi_back.jpg "Ansicht S26 WiFi-Modul Rückseite")
