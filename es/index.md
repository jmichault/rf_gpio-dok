---
lang: es
lang-niv: auto
lang-ref: indekso
layout: page
title: "_rf\\_gpio_"
---

# Presentación
 _rf\_gpio_   Simular   _RFLink_   con transmisor y receptor conectado directamente a los puertos   _GPIO_   de   _raspberry pi_.  


## Instalación:

```
cd /home/pi
git clone https://github.com/jmichault/rf_gpio.git
cd rf_gpio  
sudo cp -p rf_gpio.sh /etc/init.d  
sudo update-rc.d rf_gpio.sh defaults  
sudo service rf_gpio.sh start  
```

Conecte el receptor a   _gpio2 bcm27_   (  PIN 13  )    
 Conecte el transmisor a   _gpio0 bcm17_   (  PIN 11  )    

## Use con   _domoticz_ :
Agregar material de tipo  _«RFLink Gateway with LAN interface»_ 
 	 Dirección remota: 127.0.0.1   
	 Puerto: 10000    
 Si se reconoce el hardware, la integración con   _domoticz_   es simple: es suficiente para usar la detección automática  " en la pestaña  " interruptores ", o active La opción  " Permita 5 minutos "    

## Transceptores probados:
* Engranaje chino con reactor de reactor  ( Asin = B00Z9SZNP0, MX-05V + MX-FS-03V ), visto en Amazon a € 1.   


	El transmisor está bien, pero el receptor es un rango muy bajo (de aproximadamente 4 m), para usar solo para los códigos de aprendizaje.  
* WL101-341 + WL102-341, superherotedyne 433 MHz, visto en Aliexpress a 1 €.  


	 El receptor tiene una mejor sensibilidad que la anterior.    
	
NOTA: Para obtener buenos resultados, el receptor debe estar protegido de las plagas emitidas por  _raspberry-pi_. Para las pruebas, puede usar una capa de aluminio en una hoja de papel plegada por la mitad. 

## Materiales reconocidos:
ha sido probado con éxito:   
* TOMAR   _KYG_   (_  ASIN: B07DPVWW1  _ , Marcado   _Intertek_, Vistas de   _amazon_)(  Reconocido como   _«Impuls»_)    


* Termómetro-higiómetro   _Aneng_   (   Costo de bajo costo chino, con pantalla LCD, vista en   _aliexpress_.  )    


* Termómetro-higómetro   _Digoo RG-8B_   (   Costo de bajo costo chino, sin pantalla, vista en   _aliexpress_.  )    



Otros sensores están predefinidos en los sensores.ini, pero no fueron probados.  

## Para agregar un nuevo sensor:
El protocolo de transferencia de datos y el formato de datos deben ser identificados.  
### Opción 1:
Siga la salida de RF_gpio, soit en le lançant dans une fenêtre shell, soit en s'y connectant avec la commande «telnet 127.0.0.1 10000».  
  
  
Cuando el sensor envía datos, y se reconoce el protocolo de transferencia, verá algo que se parece a esto:   
20; 00; P0102, BITS = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865; binario = 011100110000100000, hex = 7300d8f20;  
  20; = Cualquier marco de emisiones de RFlink comienza así.  
    00; = Primer marco publicado.    
 P0102, BITS = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865 = RF  _gpio a identifié un protocole du type :  
  
  
	bit 0 = D0 D1  
	bit 1 = D0 D2  
	36 Bits de datos  
	Duración D0 = 520 μs  
	Duración D1 = 957 μs  
	Duración D2 = 1936 μs  
	 Duración síncrona DS = 3881 μs    
 DUUMA = 0111001100000000100000, hex = 7300d8f20; : Datos recibidos en binario y hexa.   

### Opcion 2:
Ejecutar en la corteza:   
./ANALYZI  
y presione el botón en el control remoto, o espere a que el sensor envíe datos. Si se reconoce el protocolo, vemos algo similar a eso:   
   73 Protocolo práctico:   " XXX; P0001, BITS = 36, D0 = 689, D1 = 1923, DS = 3890; ID: B1-B36  "    
 Datos binarios: 01110011000000110110001100100000    
 HEXA DATOS: 7300D8F20    


Ahora necesita analizar datos binarios para identificar el significado de cada pieza.  
A continuación, puede agregar una línea en el archivo Dentilos.ini, cada línea consta de tres elementos separados por un punto y coma:   
* Primer elemento: el nombre del equipo. Atención, si es un interruptor, debe ser parte de la lista de elementos reconocidos por   _domoticz_.    


* Segundo elemento: Protocolo. Copie lo que se muestra en RF_gpio ou analizo.  
    

    

	Ejemplo: P0102, BITS = 36, D0 = 561, D1 = 1899, D2 = 3845, DS = 9158  
		Medios: Protocolo P0102 (BIT 0 = D0 D1, BIT 1 = D0 D2), 36 bits por marco, Duración D0 = 561 μs, Duración D1 = 1899 μs, Duración D2 = 3845 μs, Duración Sincronización DS = 9158 μs  
	 	 Las piezas de protocolo, bits, D0 y D1 se utilizan para diferenciar dispositivos.   
* Tercer elemento: Descripción de los datos, cada campo se separa de otros por coma  


	Ejemplo para control remoto multicanal, ID: ID: B1-20, CMD: B21-21, SWILE: B22-24  
	 	 ID: B1-20 significa que el ID del control remoto está en bits 1 a 20  ( bit 1 = Primer bits entregado 
 	  	 CMD: B21-21 significa el El comando transmitido   (  ON / OFF  )   está ubicado en la OIT 21.    
 	  	 CAMBIO: B22-24 significa que el número del conector operado se encuentra en los bits 22 a 24.    
 	 Sé confirmado por   _domoticz_, el nombre del campo debe estar en la lista reconocida   (  ver Sensulos.txt  ). Sin embargo, podemos poner lo que queremos, simplemente el campo será ignorado por   _domoticz_.    
 	 Hay varias suites de bits, por ejemplo: CMD: B17-17: B15-15: B16-16 Concatena las bits 17 y 16 en ese orden.   
	El valor de ciertos bits se puede probar, por ejemplo: CST2: B43-48 = 1 confirmará que los bits 43 48 contienen el valor 1 (en la recepción, e influya en estas piezas en la distribución. , CST2: B43-48! 1 Confirme que los bits 438 no contienen el valor 1 (hexadecimal) en la recepción.  
	 Podemos declarar los campos codificados por BCD   (  Binario de codificación decimal  ) : PUT B en lugar de b. Ejemplo: TEMP: B12-15: B16-19: B20-23 declara un campo de temperatura cuyo primer número es para los bits 12-15, el segundo a los bits de 16 a 20 y el tercero en los bits 21 a 23.    
 	 El acabado de campo con  " -inv " es un campo especial que tomará el complemento inverso  ( a uno )  de su campo de homología en el show.   
	Puede asignar un valor a un campo que no se encuentra en los datos por "=". Ejemplo: cmd = en  
	 Se pueden hacer cálculos simples: la adición, la multiplicación y la resta son posibles   (  en este orden correcto  ), las constantes están en hexadecimal.    
 	 	 Ejemplo para convertir en décimas de datos de ° C que está en décimas de ° F + 900:   
	  	 TEMP: B17-28 + -4C4  *  5/9    (   SO: DATOS  _finale = (donnée_  Animal -1220  )   *   5/9   )    

En la recepción, todas las líneas que satisfacen la condición generarán una línea, si desea evitar positivos falsos, puede iniciar o eliminar las líneas que no coinciden con su equipo.  
Acerca de la pregunta, solo se utilizará la primera línea con el nombre correcto del equipo.  
	
		
Si el protocolo no se reconoce, puede usar para analizar para estudiarlo aumentando su verbo con la opción -v, -vv o -vv. Pero rf_gpio ne pourra pas le reconnaitre sans développement supplémentaire.  
  
  


## Protocolos compatibles:

Solo los protocolos con al menos las siguientes características tienen la oportunidad de ser reconocido:   
* Tiempo de pulsado siempre > 100 μs  


* No más de tres duraciones diferentes para representar los datos.  


* Cada pieza está codificada con dos pulsos, siempre de la misma manera.  


* Cada cuadro está rodeado por dos señales de sincronización del tiempo >2500 μs.  


* El marco no contiene más de 200 pulsos.  



En el programa, los códigos rotativos y los montos de confirmación no se administran.  
