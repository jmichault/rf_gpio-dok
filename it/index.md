---
lang: it
lang-niv: auto
lang-ref: indekso
layout: page
title: "_rf\\_gpio_."
---

# Presentazione
 _rf\_gpio_   Simula   _RFLink_   con trasmettitore e ricevitore collegato direttamente alle porte   _GPIO_   di   _raspberry pi_. . 


## Installazione:

```
cd /home/pi
git clone https://github.com/jmichault/rf_gpio.git
cd rf_gpio  
sudo cp -p rf_gpio.sh /etc/init.d  
sudo update-rc.d rf_gpio.sh defaults  
sudo service rf_gpio.sh start  
```

Collegare il ricevitore a   _gpio2 bcm27_   (  PIN 13  )    
 Collegare il trasmettitore a   _gpio0 bcm17_   (  PIN 11  )    

## Utilizzare con   _domoticz_ :
Aggiungi materiale di tipo  _«RFLink Gateway with LAN interface»_ 
 	 Indirizzo remoto: 127.0.0.1   
	 PORT: 10000    
 Se l'hardware è riconosciuto, l'integrazione con   _domoticz_   è semplice: è sufficiente utilizzare il rilevamento automatico  " " sugli interruttori  " ", o attivare L'opzione  " consente di 5 minuti "    

## Transceiver testati:
* Ingranaggio cinese con reattore reattore  ( ASIN = B00Z9SZNP0, MX-05V + MX-FS-03V ), visualizzato in Amazon a € 1. .  


	Il trasmettitore va bene, ma il ricevitore è molto basso (range di circa 4m), da utilizzare solo per i codici di apprendimento.  
* WL101-341 + WL102-341, Supererotedyne 433 MHz, visto su AliExpress a € 1.  


	 Il ricevitore ha una migliore sensibilità rispetto a quella precedente. .   
	
Nota: per buoni risultati, il ricevitore deve essere protetto da parassiti emessi da  _raspberry-pi_. Per i test, è possibile utilizzare un cappotto in alluminio in un foglio di carta piegato a metà. .

## Materiali riconosciuti:
stato testato con successo:   
* Take   _KYG_   (_  ASIN: B07DPVWW1  _ , contrassegnato   _Intertek_, Viste di   _amazon_)(  Riconosciuto come   _«Impuls»_)    


* Termometro-Higometro   _Aneng_   (   Cinese a basso costo, con schermo LCD, visualizzato su   _aliexpress_.  )    


* Termometro-higometro   _Digoo RG-8B_   (   Cinese a basso costo, senza schermo, visualizzato su   _aliexpress_.  )    



Altri sensori sono predefiniti in sensori.ini, ma non sono stati testati.  

## Per aggiungere un nuovo sensore:
Il protocollo di trasferimento dati e il formato dei dati devono essere identificati.  
### Opzione 1:
Seguire l'uscita di RF_gpio, soit en le lançant dans une fenêtre shell, soit en s'y connectant avec la commande «telnet 127.0.0.1 10000».  
  
  
Quando il sensore invia i dati, e il protocollo di trasferimento è riconosciuto, vedrai qualcosa che assomiglia a questo:   
20; 00; P0102, Bits = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865; Binary = 011100110000100000, HEX = 7300D8F20;  
  20; = Qualsiasi frame di emissioni rfluk inizia così.  
    00; = Primo framework pubblicato.    
 P0102, BITS = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865 = RF  _gpio a identifié un protocole du type :  
  
  
	Bit 0 = D0 D1  
	Bit 1 = D0 D2  
	36 Bits di dati  
	Durata D0 = 520 μs  
	Durata D1 = 957 μs  
	Durata D2 = 1936 μs  
	 Durata sincrona DS = 3881 μs    
 DUMA = 0111001100000000100000, HEX = 7300D8F20; : Dati ricevuti in Binary e Hexa. .  

### Opzione 2:
Corri in corteccia:   
./Analyzi  
e premere il pulsante sul telecomando o attendere che il sensore inviare i dati. Se il protocollo è riconosciuto, vediamo qualcosa di simile a quello:   
   73 Protocollo pratico:   " XXX; P0001, Bits = 36, D0 = 689, D1 = 1923, DS = 3890; ID: B1-B36  "    
 Dati binari: 0111001100000011011000100100000    
 Dati HEXA: 7300D8F20    


Ora è necessario analizzare i dati binari per identificare il significato di ogni pezzo.  
È quindi possibile aggiungere una riga nel file Dentilos.ini, ogni riga è composta da tre elementi separati da un punto e virgola:   
* Primo elemento: il nome dell'attrezzatura. Attenzione, se è un interruttore, deve essere parte dell'elenco di elementi riconosciuti da   _domoticz_. .   


* Secondo elemento: protocollo. Copia ciò che viene mostrato da RF_gpio ou analizo.  
    

    

	Esempio: P0102, Bits = 36, D0 = 561, D1 = 1899, D2 = 3845, DS = 9158  
		Mezzi: Protocollo P0102 (Bit 0 = D0 D1, Bit 1 = D0 D2), 36 bit per fotogramma, Durata D0 = 561 μs, Durata D1 = 1899 μs, Durata D2 = 3845 μs, Durata Synchro DS = 9158 μs  
	 	 Il protocollo, i bit, le parti D0 e D1 vengono utilizzati per differenziare i dispositivi. .  
* Terzo elemento: descrizione dei dati, ogni campo si separa da altri da coma  


	Esempio per telecomando multicanale, ID: ID: B1-20, cmd: B21-21, SWILE: B22-24  
	 	 ID: B1-20 significa che l'ID del telecomando è in bit da 1 a 20  ( bit 1 = primo bit consegnato 
 	  	 cmd: B21-21 significa il Comando trasmesso   (  On / Off  )   si trova in ILO 21.    
 	  	 Modifica: B22-24 significa che il numero del jack azionato si trova ai bit da 22 a 24.    
 	 Essere riconosciuto da   _domoticz_, il nome del campo deve essere nell'elenco riconosciuto   (  vedere Sensulos.txt  ). Tuttavia, possiamo mettere ciò che vogliamo, semplicemente il campo sarà ignorato da   _domoticz_.    
 	 Ci sono diverse suite bit, ad esempio: cmd: B17-17: B15-15: B16-16 concatena i bit 17 e 16 in quell'ordine. .  
	Il valore di determinati bit può essere testato, ad esempio: CST2: B43-48 = 1 confermerà che i bit 43 48 contengono il valore 1 (nella ricezione e influenzare questi pezzi nella distribuzione , CST2: B43-48! 1 Confermare che i bit 438 non contengano il valore 1 (esadecimale) nella ricezione.  
	 Possiamo dichiarare campi codificati BCD   (  Codifica decimale Binary  ) : Posizionare B invece di b. Esempio: Temp: B12-15: B16-19: B20-23 dichiara un campo di temperatura il cui primo numero è ai bit 12-15, il secondo ai bit da 16 a 20 e al terzo al bit da 21 a 23.    
 	 Finitura campo con  " -inv " è un campo speciale che prenderà il complemento inverso  ( a uno )  del suo campo omosest nello spettacolo. .  
	È possibile assegnare un valore a un campo che non è stato trovato nei dati per "=". Esempio: cmd = on  
	 I calcoli semplici possono essere effettuati: aggiunta, moltiplicazione e sottrazione sono possibili   (  in questo ordine corretto  ), le costanti sono in esadecimali.    
 	 	 Esempio da convertire in decimi di dati di ° C in decima di ° F + 900:   
	  	 Temp: B17-28 + -4C4  *  5/9    (   SO: DATA  _finale = (donnée_  Animale -1220  )   *   5/9   )    

In ricevuta tutte le linee che soddisfano la condizione genererà una linea, se si desidera evitare false positivi, è possibile avviare o rimuovere le linee che non corrispondono alle apparecchiature.  
Informazioni sulla domanda, verrà utilizzata solo la prima riga con il nome corretto dell'apparecchiatura.  
	
		
Se il protocollo non è riconosciuto, è possibile utilizzare per analizzare per studiarlo aumentando il suo verbo con l'opzione -v, -vv o -vv. Ma RF_gpio ne pourra pas le reconnaitre sans développement supplémentaire.  
  
  


## Protocolli supportati:

Solo i protocolli con almeno le seguenti funzionalità hanno la possibilità di essere riconosciuti:   
* Tempo di impulso sempre > 100 μs  


* Non più di tre diverse durate per rappresentare i dati.  


* Ogni pezzo è codificato con due impulsi, sempre allo stesso modo.  


* Ogni fotogramma è circondato da due segnali di sincronizzazione del tempo >2500 μs.  


* La cornice non contiene più di 200 impulsi.  



Nello spettacolo, i codici rotanti e gli importi di conferma non vengono somministrati.  
