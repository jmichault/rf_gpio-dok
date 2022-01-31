---
lang: pt
lang-niv: auto
lang-ref: indekso
layout: page
title: "_rf\\_gpio_"
---

# Apresentação
 _rf\_gpio_   Simular   _RFLink_   com transmissor e receptor conectado diretamente às portas   _GPIO_   de   _raspberry pi_.  


## Instalação:

```
cd /home/pi
git clone https://github.com/jmichault/rf_gpio.git
cd rf_gpio  
sudo cp -p rf_gpio.sh /etc/init.d  
sudo update-rc.d rf_gpio.sh defaults  
sudo service rf_gpio.sh start  
```

Conecte o receptor a   _gpio2 bcm27_   (  pino 13  )    
 conecte o transmissor a   _gpio0 bcm17_   (  pin 11  )    

## Use com   _domoticz_ :
Adicionar material do tipo  _«RFLink Gateway with LAN interface»_ 
 	 endereço remoto: 127.0.0.1   
	 Porta: 10000    
 Se o hardware for reconhecido, a integração com   _domoticz_   é simples: é suficiente para usar a detecção automática  " " na guia  " switches " ou ativar A opção  " Permitir 5 minutos "    

## Transceptores testados:
* Engrenagem chinesa com reator reator  ( asin = b00z9sznp0, MX-05V + MX-FS-03V ), visto na Amazon a € 1.   


	O transmissor é bom, mas o receptor é muito baixo (intervalo de cerca de 4m), para usar apenas para códigos de aprendizagem.  
* WL101-341 + WL102-341, super-heróxado 433 MHz, visto em AliExpress a € 1.  


	 O receptor tem uma sensibilidade melhor do que a anterior.    
	
Nota: Para bons resultados, o receptor deve ser protegido de pragas emitidas por  _raspberry-pi_. Para testes, você pode usar um revestimento de alumínio em uma folha de papel dobrada ao meio. 

## Materiais reconhecidos:
foi testado com sucesso:   
* Tome   _KYG_   (_  ASIN: B07DPVWW1  _ , marcado   _Intertek_, visualizações de   _amazon_)(  reconhecida como   _«Impuls»_)    


* Termômetro-higômetro   _Aneng_   (   baixo custo chinês, com tela LCD, visualizada em   _aliexpress_.  )    


* Termômetro-higômetro   _Digoo RG-8B_   (   baixo custo chinês, sem tela, visto em   _aliexpress_.  )    



Outros sensores são predefinidos em sensores.ini, mas não foram testados.  

## Para adicionar um novo sensor:
O protocolo de transferência de dados e o formato de dados devem ser identificados.  
### Opção 1:
Siga a saída de RF_gpio, soit en le lançant dans une fenêtre shell, soit en s'y connectant avec la commande «telnet 127.0.0.1 10000».  
  
  
Quando o sensor envia dados, e o protocolo de transferência é reconhecido, você verá algo que se parece com isso:   
20; 00; P0102, bits = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865; binário = 011100110000100000, hex = 7300d8f20;  
  20; = Qualquer quadro de emissões RFLink começa assim.  
    00; = Primeira estrutura publicada.    
 p0102, bits = 36, d0 = 529, d1 = 949, d2 = 1926, ds = 3865 = rf  _gpio a identifié un protocole du type :  
  
  
	Bit 0 = D0 D1  
	Bit 1 = D0 D2  
	36 bits de dados  
	Duração d0 = 520 μs  
	Duração d1 = 957 μs  
	Duração d2 = 1936 μs  
	 duração síncrona DS = 3881 μs    
 duuma = 0111001100000000100000, hex = 7300d8f20; : Dados recebidos em binário e hexá.   

### Opção 2:
Correr em casca:   
./analyzi  
e pressione o botão no controle remoto ou aguarde o sensor enviar dados. Se o protocolo é reconhecido, vemos algo semelhante à:   
   73 Protocolo Prático:   " XXX; P0001, bits = 36, d0 = 689, d1 = 1923, DS = 3890; ID: B1-B36  "    
 Dados binários: 011100110000110110001100100000    
 Hexa Dados: 7300D8F20    


Agora você precisa analisar dados binários para identificar o significado de cada peça.  
Você pode adicionar uma linha no arquivo Dentilos.ini, cada linha consiste em três elementos separados por um ponto-e-vírgula:   
* Primeiro elemento: o nome do equipamento. Atenção, se é um interruptor, deve fazer parte da lista de elementos reconhecidos pelo   _domoticz_.    


* Segundo elemento: protocolo. Copie o que é mostrado por RF_gpio ou analizo.  
    

    

	Exemplo: P0102, bits = 36, D0 = 561, D1 = 1899, D2 = 3845, DS = 9158  
		significa: protocolo P0102 (Bit 0 = d0 d1, bit 1 = d0 d2), 36 bits por quadro, duração d0 = 561 μs, duração d1 = 1899 μs, duração d2 = 3845 μs, sincronia duração ds = 9158 μs  
	 	 O protocolo, bits, d0 e peças D1 são usados para diferenciar dispositivos.   
* Terceiro elemento: descrição de dados, cada campo separa de outros por coma  


	Exemplo para controle remoto multicanal, ID: ID: B1-20, CMD: B21-21, Swille: B22-24  
	 	 id: b1-20 significa que o ID do controle remoto está em bits 1 a 20  ( bit 1 = primeiro bit entregue 
 	  	 cmd: b21-21 significa o Comando transmitido   (  ON / OFF  )   está localizado na OIT 21.    
 	  	 Alteração: B22-24 significa que o número da tomada operada está localizado nos bits 22 a 24.    
 	 Ser confirmado por   _domoticz_, o nome do campo deve estar na lista reconhecida   (  ver sensóis.txt  ). No entanto, podemos colocar o que queremos, simplesmente o campo será ignorado por   _domoticz_.    
 	 Existem várias suítes, por exemplo: CMD: B17-17: B15-15: B16-16 Concatenar os bits 17 e 16 nessa ordem.   
	O valor de certos bits pode ser testado, por exemplo: cst2: B43-48 = 1 confirmará que os bits 43 48 contêm o valor 1 (na recepção e influenciam essas peças na distribuição , CST2: B43-48! 1 Confirme se os bits 438 não contêm o valor 1 (hexadecimal) na recepção.  
	 podemos declarar campos codificados com BCD   (  codificação decimal  ) : colocar B em vez de b. Exemplo: Temp: B12-15: B16-19: B20-23 declara um campo de temperatura cujo primeiro número é para os bits 12-15, o segundo para os bits 16 a 20 e o terceiro nos bits 21 a 23.    
 	 Acabamento de campo com  " -inv " é um campo especial que levará o complemento inverso  ( para um )  de seu campo homonyst no show.   
	Você pode atribuir um valor a um campo que não é encontrado nos dados por "=". Exemplo: cmd = on  
	 Cálculos simples podem ser feitos: adição, multiplicação e subtração são possíveis   (  Nesta ordem correta  ), as constantes estão em hexadecimal.    
 	 	 exemplo para converter em décimos de dados ° C que está em décimos de ° F + 900:   
	  	 Temp: B17-28 + -4C4  *  5/9    (   Assim: Dados  _finale = (donnée_  animal -1220  )   *   5/9   )    

Em recebimento todas as linhas que satisfazem a condição gerarão uma linha, se você quiser evitar falsos positivos, você pode iniciar ou remover as linhas que não correspondem ao seu equipamento.  
Sobre pergunta, apenas a primeira linha com o nome correto do equipamento será usada.  
	
		
Se o protocolo não for reconhecido, você poderá usar para analisar para estudá-lo aumentando seu verbo com a opção -V, -vv ou -vv. Mas rf_gpio ne pourra pas le reconnaitre sans développement supplémentaire.  
  
  


## Protocolos suportados:

Apenas protocolos com pelo menos os seguintes recursos têm a chance de ser reconhecido:   
* Tempo de pulsado sempre > 100 μs  


* Não mais do que três durações diferentes para representar os dados.  


* Cada peça é codificada com dois pulsos, sempre da mesma maneira.  


* Cada quadro é cercado por dois sinais de sincronização de tempo >2500 μs.  


* O quadro não contém mais de 200 pulsos.  



No show, os códigos rotativos e os montantes de confirmação não são administrados.  
