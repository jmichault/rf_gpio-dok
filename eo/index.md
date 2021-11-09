---
lang: eo
lang-niv: auto
lang-ref: indekso
layout: page
title: Rf_gpio
---

# Prezento
 _rf_GPIO_ ebligas simuli rflink kun dissendilo kaj ricevilo konektita rekte al la havenoj _GPIO_ de a _raspberry pi_.


## Instalado:

```
cd /home/pi
git clone https://github.com/jmichault/rf_gpio.git
cd rf_gpio  
sudo cp -p rf_gpio.sh /etc/init.d  
sudo update-rc.d rf_gpio.sh defaults  
sudo service rf_gpio.sh start  
```

Konekti la ricevilon al GPIO2 BCM27 (PIN 13)  
Konekti la dissendilon al GPIO0 BCM17 (PIN 11)  

## Uzo kun DOMOTICZ:
Aldoni materialon de tipo _«RFLink Gateway with LAN interface»_
	Remota Adreso: 127.0.0.1  
	 haveno: 10000   
Se la aparataro estas agnoskita, la integriĝo kun Domomoticz estas simpla: sufiĉas uzi la butonon "Aŭtomata Detekto" sur la langeto "Ŝaltiloj", aŭ aktivigi la opcion "Permesi 5 Minutoj"   

## Transceivers testis:
* Ĉina ilaro kun reaga ricevilo (asin = b00z9sznp0, MX-05V + MX-FS-03V), vidita en Amazono je 1 €.  

	La dissendilo estas bone, sed la ricevilo estas tre malalta (gamo de ĉirkaŭ 4m), por uzi nur por lernaj kodoj.  
* WL101-341 + WL102-341, Superheterodyne 433 MHz, vidita sur AliExpress je 1 €.  

	 La ricevilo havas pli bonan sentemon ol la antaŭa.   
	
Noto: Por bonaj rezultoj, la ricevilo devas esti protektita kontraŭ pestoj elsenditaj de _raspberry-pi_. Por testado, vi povas uzi aluminan tavoleton en folio de papero faldita duone.

## Materialoj rekonitaj:
estis sukcese testita:  
* Kyg (ASIN: B07DPmpw1, markita Intertk, vidpunktoj de Amazon)(agnoskita kiel "impulsoj")  

* Ĉina ĉina aneng ( termometro kun ekrano LCD, vidita sur AliExpress.)  

* DigOOOO RG-8B higrometer-higrómetro ĉina-kosto, sen ekrano, vidita sur AliExpress.)  


Aliaj sensiloj estas antaŭdifinitaj en sentiloj.ini, sed ne estis testitaj.  

## Por aldoni novan sensor:
La datuma transporto-protokolo kaj la datuma formato devas esti identigitaj.  
### Opcio 1:
Sekvu la eliron de RF_gpio, soit en le lançant dans une fenêtre shell, soit en s'y connectant avec la commande «telnet 127.0.0.1 10000».  
  
Kiam la sensor sendas datumojn, kaj la transiga protokolo estas agnoskita, vi vidos ion, kio aspektas tiel:  
20; 00; p0102, bitoj = 36, d0 = 529, d1 = 949, d2 = 1926, ds = 3865; binara = 01110011000000100000, hex = 7300d8f20;  
  20; = Ajna Framo pri RFLINK-Emisioj komenciĝas tiel.  
   00; = Unua kadro eldonita.   
   p0102, bitoj = 36, d0 = 529, d1 = 949, d2 = 1926, ds = 3865 = rf _gpio a identifié un protocole du type :  
  
	Bito 0 = D0 D1  
	bito 1 = d0 d2  
	36 datumoj bitoj  
	Daŭro D0 = 520 μs  
	Daŭro D1 = 957 μs  
	Daŭro d2 = 1936 μs  
	 sinkrona daŭro DS = 3881 μs   
  Duuma = 0111001100000000100000, hex = 7300D8F20; : Datumoj ricevitaj en duuma kaj hexa.  

### Opcio 2:
Kuru en ŝelo:  
./analizi  
kaj premu la butonon sur la fora kontrolo, aŭ atendu la sensilon por sendi datumojn. Se la protokolo estas agnoskita, ni vidas ion similan al tio:  
  73 Praktika Protokolo:  " XXX; P0001, Bits = 36, D0 = 689, D1 = 1923, DS = 3890; ID: B1-B36 "   
   Binary Datumoj: 0111001100000011011000111100100000   
   hexa datumo: 7300d8f20   


Vi nun bezonas analizi binarajn datumojn por identigi la signifon de ĉiu peco.  
Vi povas tiam aldoni linion en la dosieron Dentilos.ini, ĉiu linio konsistas el tri elementoj apartigitaj per punktokomo:  
* Unua elemento: nomo de la teamo. Atento, se ĝi estas ŝaltilo, ĝi devas esti parto de la listo de elementoj agnoskitaj de DomotiCz.  

* Dua elemento: Protokolo. Kopiu tion, kio estas montrita de RF_gpio ou analizo.  
    

	Ekzemplo: p0102, bitoj = 36, d0 = 561, d1 = 1899, d2 = 3845, ds = 9158  
		signifas: Protokolo P0102 (Bito 0 = D0 D1, BIT 1 = D0 D2), 36 bitoj por kadro, daŭro d0 = 561 μs, daŭro d1 = 1899 μs, daŭro d2 = 3845 μs, daŭro Synchro DS = 9158 μs  
		La protokolo, bitoj, D0 kaj D1-partoj estas uzataj por diferencigi aparatojn.  
* Tria Elemento: Data Priskribo, ĉiu kampo apartiĝas de aliaj per komo  

	Ekzemplo por multi-kanalo Remote Kontrolo, ID: ID: B1-20, CMD: B21-21, SWILE: B22-24  
		ID: B1-20 signifas, ke la ID de la fora kontrolo estas en bitoj 1 ĝis 20 (bito 1 = unua bito transdonita
	 	 Cmd: B21-21 signifas, ke la komando transdonita  ( ON / OFF )  situas en Ilo 21.   
	 	 ŝanĝi: B22-24 signifas, ke la nombro de la funkciigita Jack situas ĉe la bitoj 22 ĝis 24.   
	Esti agnoskita de Domomoticz, la nomo de la kampo devas esti en la rekonita listo (vidu sentilos.txt). Ni tamen povas meti tion, kion ni volas, simple la kampo estos ignorita de Domomoticz.  
	Ekzistas pluraj bita suitoj, ekzemple: CMD: B17-17: B15-15: B16-16 Concatenate la bitoj 17 kaj 16 en tiu ordo.  
	La valoro de certaj bitoj povas esti testita, ekzemple: CST2: B43-48 = 1 konfirmos, ke la bitoj 43 48 enhavas la valoron 1 (deksesuma) en ricevo, kaj Influas ĉi tiujn pecojn en dissendo, CST2: B43-48! 1 konfirmos, ke la bitoj 43 48 ne enhavas la valoron 1 (deksesuma) en ricevo.  
	 Ni povas deklari BCD-koditajn kampojn  ( Dekuma kodita duuma ) : metu b anstataŭ b. Ekzemplo: Temp: B12-15: B16-19: B20-23 deklaras temperaturan kampon kies unua cifero estas al la bitoj 12-15, la dua al la bitoj 16 ĝis 20 kaj la tria ĉe la bitoj 21 ĝis 23.   
	Kampo finanta kun "-inv" estas speciala kampo kiu prenos la inversan (komplemento al unu) de ĝia homonima kampo en la spektaklo.  
	Vi povas atribui valoron al kampo, kiu ne troviĝas en la datumoj per "=". Ekzemplo: cmd = sur  
	 Simplaj kalkuloj povas esti faritaj: aldono, multipliko kaj subtraho estas eblaj  ( en ĉi tiu ĝusta ordo ), la konstantoj estas en deksesuma.   
		Ekzemplo por konverti en dekonojn de ° C datumo kiu estas en dekonoj de ° F + 900:  
	 	 TEMP: B17-28 + -4C4 * 5/9   (  SO: DATA _finale = (donnée_ BESTO -1220 )  *  5/9  )   

En ricevo ĉiuj linioj kiuj kontentigas la kondiĉon generos linion, se vi volas eviti falsajn pozitivaĵojn, vi povas komenci aŭ forigi la liniojn, kiuj ne kongruas kun via ekipaĵo.  
Pri demando, nur la unua linio kun la ĝusta nomo de ekipaĵo estos uzata.  
	
		
Se la protokolo ne estas rekonita, vi povas uzi Analizi por studi ĝin per pliigo de ĝia verboseco kun la opcio -v, -vv aŭ -vv. Sed RF_gpio ne pourra pas le reconnaitre sans développement supplémentaire.  
  


## Apogitaj protokoloj:

Nur protokoloj kun almenaŭ la sekvaj karakterizaĵoj havas ŝancon esti rekonitaj:  
* Tempo de pulsado ĉiam > 100 μs  

* Ne pli ol tri malsamaj daŭroj por reprezenti la datumojn.  

* Ĉiu peco estas kodita kun du pulsbatoj, ĉiam sammaniere.  

* Ĉiu kadro estas ĉirkaŭita de du sinkronigaj signaloj de tempo >2500 μs.  

* Kadro ne enhavas pli ol 200 pulsojn.  


En la spektaklo, la rotaciaj kodoj kaj la sumoj de konfirmo ne estas administritaj.  
