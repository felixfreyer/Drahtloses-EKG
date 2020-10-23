# Drahtloses EKG
Aug 19th, 2014

![](https://github.com/felixfreyer/Drahtloses-EKG/raw/main/abb0.png)

Ziel dieses Projektes ist es an einem zu untersuchenden Patienten ein Elektrokardiogramm (EKG) aufzunehmen, dieses zu übertragen und auf einem PC zu visualisieren. Ausgangspunkt des Projektes war das EKG-Frontend ADS1293 von Texas Instruments.

### Einleitung
Zunächst werden die Grundlagen eines Elektrokardiogramms erläutert. Unter einem EKG versteht man die Darstellung beziehungsweise die Aufzeichnung der Summe aller elektrischen Aktivitäten aller Herzmuskelfasern. Jeder Kontraktion des Herzens geht eine elektrische Erregung voraus, diese breitet sich ausgehend vom Sinusknoten über die übrigen Herzmuskelzellen aus. Die elektrischen Spannungsänderungen können über die Körperoberfläche gemessen werden und in einem Zeitverlauf aufgezeichnet werden. Dazu ist eine Umsetzung von einer Ionenleitung, bedingt durch extrazelluläre Flüssigkeit im Körperinnenren, auf eine Elektronenleitung nötig. Diese Umsetzung erfolgt mit Hilfe von Elektroden, die dem Patienten auf die Körperoberfläche geklebt werden. In Abbildung 1 ist der typische Verlauf eines EKG schematisch dargestellt.

![](https://github.com/felixfreyer/Drahtloses-EKG/raw/main/abb1.png)
<br />
**Abbildung 1: Typischer Verlauf eines EKG**

Läuft die zu messende Potentialwelle auf die Elektrode zu, wird sie mit einer positiven Polarität gemessen, entfernt sie sich von der Elektrode, so misst man eine negative Polarität. Das EKG-Signal liegt in dem Frequenzbereich von 0,5 Hz bis 100 Hz und besitzt typischerweise eine maximale Amplitude von etwa 1 mV. Für die Messwertaufnahme wird in diesem Projekt eine drei Punkt Ableitung nach Einthoven verwendet, dabei wird die elektrische Potentialänderung zwischen drei Extremitäten gemessen. Die drei Elektroden für die Messwert-aufnahme werden auf dem rechten Arm (RA), dem linken Arm (LA) und dem linken Bein (LL) aufgebracht. Die vierte Elektrode auf dem rechten Bein (RL), auch driven right leg genannt, dient der Störgrössenminimierung. Dabei wird der für das EKG irrelevante Gleichtaktanteil des Messsignals ausgekoppelt, invertiert und auf den Patienten zurückgeführt, so lässt sich der störende Einfluss des Gleichtaktanteils bei der Messwert-aufnahme minimieren. Nachdem nun die Grundlagen des Elektrokardiogramms kurz erläutert wurden, werden im Folgenden die verschiedenen Anwendungsszenarien des Projektes beschrieben.

### Anwendungsszenarien
Das Gerät soll in der Lage sein ein Elektrokardiogramm über die 3-Punkt Ableitung aufzunehmen, dabei ist ein kabelloser Betrieb vorgesehen, das heisst, dass das Gerät über einen integrierten Akku versorgt wird. Die Messwerte werden über vier Elektroden, welche auf dem Patienten aufgebracht werde, erfasst. Der Akku kann bei Bedarf mit Hilfe eines Mini-USB-Kabels über einen PC aufgeladen werden. Des Weiteren sollen die erfassten Daten an einen PC übertragen werden, wo sie anschliessend auf dem Bildschirm grafisch dargestellt werden sollen. Das Gerät soll weiterhin über einen Ein- und Ausschalter verfügen und den Zustand des Akkus über eine Led anzeigen.

### Technische Realisierung
Das nachstehende Blockdiagramm zeigt die Zusammenhänge der einzelnen Komponenten auf. Das zentrale Element bilden der MSPCC430 und das EKG-Frontend ADS1293. Des Weiteren stellen der Battery Charger, der Buck-Boost Converter und der LiPo Akku die Spannungsversorgung des Systems sicher. Die restliche Peripherie dient der Signalübertragung und der Signalumsetzung, wie beispielsweise der FTDI-Chip der die Signale von UART nach USB umsetzt. Zusätzlich ist für zukünftige Anwendungen eine Funkschnittstelle vorgesehen, sodass die Messwerte auch drahtlos übertragen werden können.

![](https://github.com/felixfreyer/Drahtloses-EKG/raw/main/abb2.png)
<br />
**Abbildung 2: Blockdiagramm**

### Layout
Für das Layout wurde die Freeware Version des Layout-Programms EAGLE verwendet.

Die Platinengrösse wurde so entworfen, dass die Platine später in das dafür vorgesehene Gehäuse passt. Dann wurden USB und D-Sub-Stecker an die Platinenseiten der Gehäuseöffnungen platziert. Der ADS-1293-Chip wurde in die Nähe des D-Sub-Steckers platziert und der Mikrocontroller in der Nähe der SMD-Antenne. Die Ladeschaltung und der Schaltregler befinden sich am oberen Rand der Platine. Bei jedem Bauteil wurde darauf geachtet, dass die Abblockkondensatoren möglichst nah am Bauteil sitzen. Siehe nachfolgende Abbildung.

![](https://github.com/felixfreyer/Drahtloses-EKG/raw/main/abb3.png)
<br />
**Abbildung 3: Layout**

Im endgültigen Layout wurden alle freien Flächen durch Massepotentialflächen ersetzt und in zufälligen Abständen wurden Vias zwischen Top und Bottom gesetzt, um Massekopplungen zu verhindern.

###Aufbau
In dieser Version geht die USB-Schnittstelle über den UART-Chip direkt mit 5V an den Mikrocontroller. Da, dieser jedoch nur mit maximal 3,3V betrieben werden darf, wurde ein provisorischer Pegelwandler zwischengeschaltet, der in Schaltung und Layout nicht vorhanden ist.

![](https://github.com/felixfreyer/Drahtloses-EKG/raw/main/abb4.png)
<br />
**Abbildung 4: Fertige Platine mit Pegelwandler**

### Mikrocontroller Firmware
Die Hauptfunktionen der Mikrocontroller-Firmware sind die Datenerfassung sowie die Kommunikation mit LabVIEW. Die Kommunikation geschieht über die USB-Schnittstelle wodurch EKG und PC miteinander verbunden sind. Der verwendete Mikrocontroller besitzt jedoch keine Hardware-USB-Schnittstelle sondern einen USB-RS232-Umsetzer der eine UART Kommunikation via USB ermöglicht. Aufgabe der Firmware ist also per UART Daten / Kommandos zu empfangen, diese zu interpretieren und darauf zu antworten.

**Aufbau der Firmware**

Die Firmware besteht aus mehreren Modulen.

TI_ADS1293_register_settings.h - ADS1293 Registereinstellungen

TI_ADS1293.h - ADS1293 Registeradressen

TI_MSP430_hardware_board.h - ADS1293 Pinbelegung

TI_MSP430.h - ADS1293 SPI-Pinbelegung

main.c - Hauptschleife für Datenerfassung /-Verarbeitung

Die Module mit dem Präfix TI_ADS1293 sowie TI_MSP430 wurden aus dem Beispiel-Code ‘MSP430/ADS1293 Interface Code Library v1.0’ entnommen und an die verwendete Hardware angepasst. Das Modul main.c enthält die Hauptschleife der Firmware und dient zur Erfassung des EKG Signals per SPI-Schnittstelle sowie zur Kommunikation mit dem PC.

### Softwaretest
Im Softwaretest wurde zunächst die korrekte Messwertaufnahme des ADS1293 EKG-Frontends überprüft. Dazu wurde die Datenflusssteuerung mittels Software-Handshake entfernt, sodass die aufgenommenen Abtastwerte einfach per UART an den PC weitergeleitet werden konnten. Am PC wurden die empfangenen Messwerte über ein serielles Terminalprogramm eingelesen und gespeichert. Die gespeicherten Messwerte konnten nun mit einem von Steffen zur Verfügung gestellten Matlab Skript ausgewertet und dargestellt werden:

![](https://github.com/felixfreyer/Drahtloses-EKG/raw/main/abb5.png)
<br />
**Abbildung 5: SW Test mit Matlab**

Durch diesen Softwaretest wurde die korrekte Funktion der Firmware bestätigt, welche als Grundvorraussetzung für die weitere LabVIEW-Programmierung diente.

### Empfängerseite
Die an den PC übertragenden Daten werden mit Hilfe des Programms LabVIEW verarbeitet und visualisiert. Das Programm eignet sich dafür besonders, weil es für Anwendungen in der Messtechnik und Regelungstechnik konzipiert wurde. Zunächst wird an dieser Stelle das Userinterface beschrieben. Das Userinterface besteht aus vier Bedienelementen und zwei Anzeigeelemente. Mit Hilfe der Bedienelemente werden drei wichtigsten Konfigurationen für die Kommunikation mit dem EKG vorgenommen. Mit dem Bedienelement Sample Rate / ms kann die Abtastrate des EKGs eingestellt werden. Mit dieser Eingabe wird die Iterationszeit eines Schleifendurchlaufs bestimmt. Die Abtastrate ergibt sich zu 1 / Sample Rate. Das Bedienelement Baud Rate / Baud stellt die Baudrate für die Kommunikation zwischen LabVIEW und dem Mikrocontroller des EKGs ein. Der Standardwert für dieses Projekt beträgt 115200 Baud. Wichtig bei der Einstellung der Baudrate ist es, dass die Baudrate von LabVIEW mit der Baudrate des Mikrocontrollers übereinstimmt, da ansonsten keine oder nicht die richtigen Messwerte übertragen werden. Der COM Port des PCs an dem das EKG angeschlossen ist kann über das Bedienelement COM Port ausgewählt werden. Das letzte Bedienelement ist der Stop-Button. Dieser hält das LabVIEW Programm an und beendet die Kommunikation zwischen PC und EKG. Neben den vier Bedienelementen beinhaltet das Userinterface zwei Anzeigeelemente, die der Darstellung des EKG-Verlaufs dienen. Dabei handelt es sich um zwei Grafen, welche jeweils einen Kanal des EKGs ausgeben.

In Abbildung 6 ist das Userinterface dargestellt. Auf der linken Seite befinden sich die vier Bedienelemente, rechts daneben die beiden Anzeigeelemente.

![](https://github.com/felixfreyer/Drahtloses-EKG/raw/main/abb6.png)
<br />
**Abbildung 6: Labview Userinterface**
