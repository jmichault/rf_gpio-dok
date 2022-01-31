---
lang: de
lang-niv: auto
lang-ref: indekso
layout: page
title: "_rf\\_gpio_"
---

# Präsentation
 _rf\_gpio_   Simulieren Sie   _RFLink_   mit Sender und Empfänger, der direkt an die   _GPIO_   -Anungen von   _raspberry pi_ angeschlossen ist.  


## Installation:

```
cd /home/pi
git clone https://github.com/jmichault/rf_gpio.git
cd rf_gpio  
sudo cp -p rf_gpio.sh /etc/init.d  
sudo update-rc.d rf_gpio.sh defaults  
sudo service rf_gpio.sh start  
```

Verbinden Sie den Empfänger an   _gpio2 bcm27_   (  Pin 13  )    
 den Sender an   _gpio0 bcm17_   (  PIN 11  )     Verbinden

## Verwendung mit   _domoticz_ :
Fügen Sie Material des Typs  _«RFLink Gateway with LAN interface»_ 
 	 Remote-Adresse hinzu: 127.0.0.1   
	 Anschluss: 10000    
 Wenn die Hardware erkannt wird, ist die Integration mit   _domoticz_   einfach: Es reicht aus, die  " Automatische Erkennung " auf der Registerkarte  " " zu verwenden oder zu aktivieren Die Option  " erlaubt 5 Minuten "    

## Transceivers getestet:
* Chinesische Ausrüstung mit Reaktorreaktor  ( ASIN = B00Z9SZNP0, MX-05V + MX-FS-03V ), angesehen in Amazon in EUR 1.   


	Der Sender ist in Ordnung, der Empfänger ist jedoch sehr niedrigvon etwa 4 m), nur für Lerncodes verwendet.  
* WL101-341 + WL102-341, Superheroterdyne 433 MHz, gesehen auf AliExpress mit 1 €.  


	 Der Empfänger hat eine bessere Sensibilität als der vorherige.    
	
Hinweis: Bei guten Ergebnissen muss der Empfänger vor den von  _raspberry-pi_ ausgestellten Schädlingen geschützt sein. Zum Testen können Sie einen Aluminiummantel in einem in der Hälfte gefalteten Blatt Papier verwenden. 

## Anerkannte Materialien:
erfolgreich getestet worden:   
* Nehmen Sie   _KYG_   (_  ASIN: B07DPVWW1  _ , markiert   _Intertek_, Ansichten von   _amazon_)( , die als   _«Impuls»_)     bestätigt werden


* Thermometer-Higometer   _Aneng_   (   chinesische kostengünstige Kosten, mit LCD-Bildschirm, angesehen auf   _aliexpress_.  )    


* Thermometer-Higometer   _Digoo RG-8B_   (   chinesische kostengünstige Kosten ohne Bildschirm, angesehen auf   _aliexpress_.  )    



Andere Sensoren sind in Sensors.ini vordefiniert, wurden jedoch nicht getestet.  

## So fügen Sie einen neuen Sensor hinzu:
Das Datenübertragungsprotokoll und das Datenformat müssen identifiziert werden.  
### Option 1:
Folgen Sie dem Austritt von RF_gpio, soit en le lançant dans une fenêtre shell, soit en s'y connectant avec la commande «telnet 127.0.0.1 10000».  
  
  
Wenn der Sensor Daten sendet und das Übertragungsprotokoll erkannt wird, sehen Sie etwas, das so aussieht:   
20; 00; P0102, Bits = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865; binär = 011100110000100000, hex = 7300d8f20;  
  20; = Jeder RFLink-Emissionsrahmen beginnt so.  
    00; = Erster Framework veröffentlicht.    
 P0102, Bits = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865 = RF  _gpio a identifié un protocole du type :  
  
  
	Bit 0 = D0 D1  
	Bit 1 = D0 D2  
	36 Datenbits  
	Dauer D0 = 520 μs  
	Dauer D1 = 957 μs  
	Dauer D2 = 1936 μs  
	 Synchrondauer DS = 3881 μs    
 Duuma = 0111001100000000100000, Hex = 7300D8F20; : Daten in Binär und Hexa erhalten.   

### Option 2:
Laufen in Rinde:  
./Analyzi  
und drücken Sie die Taste an der Fernbedienung oder warten Sie, bis der Sensor Daten sendet. Wenn das Protokoll erkannt wird, sehen wir etwas Ähnliches:   
   73 Praktisches Protokoll:   " XXX; P0001, Bits = 36, D0 = 689, D1 = 1923, DS = 3890; ID: B1-B36  "    
 Binäre Daten: 01110011000000110110001100100000    
 Hexa Daten: 7300D8F20    


Sie müssen jetzt binäre Daten analysieren, um die Bedeutung jedes Stücks zu identifizieren.  
Sie können dann eine Zeile in der Dentilos.ini-Datei hinzufügen. Jede Zeile besteht aus drei Elementen, die durch ein Semikolon getrennt sind:   
* Erstes Element: Der Name des Geräts. Achtung, wenn es sich um einen Schalter handelt, muss es Teil der Liste der von   _domoticz_ erkannten Elemente sein.    


* Zweites Element: Protokoll. Kopieren Sie, was von RF_gpio ou analizo.  
  gezeigt wird  

    

	Beispiel: P0102, Bits = 36, D0 = 561, D1 = 1899, D2 = 3845, DS = 9158  
		Mittel: Protokoll P0102 (Bit 0 = D0 D1, Bit 1 = D0 D2), 36 Bits nach Frame, Dauer D0 = 561 μs, Dauer D1 = 1899 μs, Dauer D2 = 3845 μs, Dauer Synchro ds = 9158 μs  
	 	 Die Protokoll-, Bits, D0- und D1-Teile dienen der Unterscheidung von Geräten.   
* Drittes Element: Datenbeschreibung, jedes Feld trennt sich von anderen mit COMA  


	Beispiel für Multi-Channel-Fernbedienung, ID: ID: B1-20, CMD: B21-21, SWILE: B22-24  
	 	 ID: B1-20 bedeutet, dass die ID der Fernbedienung in den Bits 1 bis 20  ( Bit 1 = erster Bit geliefert wird 
 	  	 CMD: B21-21 bedeutet das Befehl, übertragenen Befehl   (  Ein / Aus  )   befindet sich in ILO 21.    
 	  	 Änderung: B22-24 bedeutet, dass sich die Anzahl der betätigten Buchse an den Bits 22 bis 24 befindet.   (  > 	 Von   _domoticz_ anerkannt werden, muss der Feldname in der erkannten Liste   (  siehe Sensulos.txt  ) sein. Wir können jedoch ein, was wir wollen, einfach das Feld wird von   _domoticz_ ignoriert.    
 	 Es gibt mehrere Bit-Suiten, zum Beispiel: CMD: B17-17: B15-15: B16-16 Verkettung der Bits 17 und 16 in dieser Reihenfolge.   
	Der Wert bestimmter Bits kann zum Beispiel getestet werden: CST2: B43-48 = 1 bestätigt, dass die Bits 43 48 den Wert 1 (in der Rezeption enthalten und diese Teile in der Verteilung beeinflussen , CST2: B43-48! 1 Vergewissern Sie sich, dass die Bits 438 nicht den Wert 1enthalten  
	 Wir können BCD-codierte Felder deklarieren   (  Dezimalcodierung Binärer  ) : Setzen Sie B statt b. Beispiel: TEMP: B12-15: B16-19: B20-23 deklariert ein Temperaturfeld, dessen erste Zahl an den Bits 12-15 ist, wobei der zweite an den Bits 16 bis 20 und das dritte an den Bits 21 bis 23 ist.    
 	 Feldveredelung mit  " -Inv " ist ein spezielles Feld, das die inverse )  ergänzende Ergänzung zu einem )  seinem Homonyst-Feld in der Show dauert.   
	Sie können einem Feld einen Wert zuweisen, der in den Daten pro "="gefunden wird. Beispiel: cmd = auf  
	 einfache Berechnungen können vorgenommen werden: Zugabe, Multiplikation und Subtraktion sind in dieser korrekten Reihenfolge  ), die Konstanten sind in Hexadezimal möglich.    
 	 	 Beispiel, um in Zehntel von ° C Daten zu konvertieren, die sich in Zehntel ° F + 900:  befinden  
	  	 TEMP: B17-28 + -4C4  *  5/9    (   SO: Daten  _finale = (donnée_  Tier -1220  )   *   5/9   )    

In Erhalt aller Zeilen, die den Zustand erfüllen, erzeugt die Bedingung eine Zeile, wenn Sie falsche Positive vermeiden möchten, können Sie die Zeilen starten oder entfernen, die nicht mit Ihrer Ausrüstung entsprechen.  
In der Frage wird nur die erste Zeile mit dem richtigen Namen der Geräte verwendet.  
	
		
Wenn das Protokoll nicht erkannt wird, können Sie dazu verwenden, dass Sie analysieren, um ihn zu studieren, indem das Verb mit der Option -V, -VV oder -VV erhöht wird. Aber rf_gpio ne pourra pas le reconnaitre sans développement supplémentaire.  
  
  


## Unterstützte Protokolle:

Nur Protokolle mit mindestens den folgenden Funktionen haben die Möglichkeit, anerkannt zu werden:  
* Zeit der gepulsten immer > 100 μs  


* Nicht mehr als drei verschiedene Dauern, um die Daten darzustellen.  


* Jedes Stück ist mit zwei Impulsen, immer auf dieselbe Weise, codiert.  


* Jeder Rahmen ist von zwei Synchronisationssignalen der Zeit >2500 μs umgeben.  


* Der Rahmen enthält nicht mehr als 200 Impulse.  



In der Show werden die Drehcodes und die Bestätigungsbeträge nicht verabreicht.  
