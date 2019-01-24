# HM-LC-Sw1-Pl-DN-R1_S26     [![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)     [![GitHub issues](https://img.shields.io/github/issues/der-pw/HM-LC-Sw1-Pl-DN-R1_S26.svg)](https://github.com/der-pw/HM-LC-Sw1-Pl-DN-R1_S26/issues)

## Projekt befindet sich noch in der Evaluierungsphase! Diese Seite dient momentan nur dem Informationsaustausch. Platine daher noch nicht nachbauen!

Umbau des [Sonoff S26 Zwischenstecker](https://www.itead.cc/sonoff-s26-wifi-smart-plug.html) zum HM-LC-Sw1-Pl-DN-R1_S26

Dies basiert auf der hervorragenden Arbeit von [pa-pa](https://github.com/pa-pa/AskSinPP),  [Jérôme](https://github.com/jp112sdl/Beispiel_AskSinPP), sowie meiner Inspirationsquelle [stan23](https://github.com/stan23) mit seinem Umbau OBI-Wifi-Stecker zu [HM-LC-Sw1-Pl-DN-R1_OBI](https://github.com/stan23/HM-LC-Sw1-Pl-DN-R1_OBI).

![Rückansicht HM-LC-Sw1-Pl-DN-R1_S26](https://github.com/der-pw/HM-LC-Sw1-Pl-DN-R1_S26/raw/master/img/PCB_3D_back.jpg "Rückansicht HM-LC-Sw1-Pl-DN-R1_S26")

### Bauteile

[Bestellliste](https://www.reichelt.de/my/1496083) bei Reichelt.

Bauteil                  | Bestellnummer   | Anzahl | Kommentar
------------------------ | --------------- | ------ | ---------
C1                       | X5R-G0603 10/6  |   1    | -
C2, C3, C4, C5           | X7R-G0603 100N  |   4    | -
R1                       | RND 0603 1 10K  |   1    | -
U1                       | ATMEGA 328P-AU  |   1    | -
Stiftleiste für CC1101   | SL 1X20G 2,00   |   1    | optional, hilfreich zum Anschließen des ISP
Buchsenleiste für CC1101 | MPE 156-1-032   |   1    | optional

Die Stiftleistenverbindung sollte nur zu Debugzwecken eingesetzt werden. Aus Platzgründen muss der CC1101 direkt auf die SMD-Pads der Paltine verlötet werden.

Bauteil | Bestellnummer            | Anzahl | Kommentar
------- | ------------------------ | ------ | ---------
CC1101  | CC1101 Funkmodul 868 MHz |   1    | z.B. *[eBay](https://www.ebay.de/itm/163171439732)

**Ich habe mit diesem Händler bereits mehrmalig gute Erfahrungen gemacht.*

~8,3 cm Draht als Antenne (auf die mitgelieferte Helixantenne sollte verzichtet werden.

### Bauanleitung

Zuerst den ATmega auflöten, die Markierung (kleiner Punkt) muss zur Beschriftung U1 zeigen.
Danach die Kondensatoren und den Widerstand auflöten.

Mit einem Multimeter messen ob kein Kurzschluss zwischen VCC und GND besteht (mehrere 10 K Widerstand sind okay).

Den ISP Programmieradapter an die Lötaugen für den CC1101 anschließen. Ein Adapterkabel vom 6-poligen ISP-Adapter auf eine 2 mm-Stiftleiste wird empfohlen und kann ggf. aus Stift- und Buchsenleiste hergestellt werden.

### Programmierung des ATMEGA
Ich verwende einen Arduino als ISP, daher beziehen sich die Beispiele darauf.

#### Fuses
Ext: 0xFF High: 0xD2 Low: 0xE2

`<Pfad zu> .\avrdude -C ..\etc\avrdude.conf -v -p m328p -P com11 -c stk500v1 -b 19200 -U lfuse:w:0xE2:m -U hfuse:w:0xD2:m -U efuse:w:0xFF:m`

#### Bootloader
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


[![Creative Commons Lizenzvertrag](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

Dieses Werk ist lizenziert unter einer [Creative Commons Namensnennung - Nicht-kommerziell - Weitergabe unter gleichen Bedingungen 4.0 International Lizenz](http://creativecommons.org/licenses/by-nc-sa/4.0/).
