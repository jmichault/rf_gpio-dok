---
lang: ms
lang-niv: auto
lang-ref: indekso
layout: page
title: Rf_gpio
---

# Persembahan
 _rf_GPO_ Membolehkan untuk mensimulasikan rflink dengan pemancar dan penerima yang disambungkan terus ke port _GPIO_ daripada _raspberry pi_.


## Pemasangan:

```
cd /home/pi
git clone https://github.com/jmichault/rf_gpio.git
cd rf_gpio  
sudo cp -p rf_gpio.sh /etc/init.d  
sudo update-rc.d rf_gpio.sh defaults  
sudo service rf_gpio.sh start  
```

Sambungkan penerima ke GPO2 BCM27  ( PIN 13 )   
Sambungkan pemancar ke GPO0 BCM17  ( PIN 11 )   

## Gunakan dengan Hosticz:
Tambah Bahan Jenis  _«RFLink Gateway with LAN interface»_ 
 	 Alamat Jauh: 127.0.0.1   
	 port: 10000    
 Jika perkakasan diiktiraf, integrasi dengan domomoticz adalah mudah: ia cukup untuk menggunakan butang pengesanan automatik  " " pada suis " ", atau diaktifkan pilihan  " membenarkan 5 minit "    

## Transciers diuji:
* Gear Cina dengan reaktor reaktor  ( asin = b00z9sznp0, mx-05v + mx-fs-03v ), dilihat di Amazon pada € 1.   


	Pemancar adalah baik, tetapi penerima sangat rendah (jarak kira-kira 4m), untuk digunakan hanya untuk kod pembelajaran.  
* WL101-341 + WL102-341, Superherotyne 433 MHz, dilihat di AliExpress pada € 1.  


	 penerima mempunyai sensitiviti yang lebih baik daripada yang sebelumnya.    
	
Nota: Untuk hasil yang baik, penerima mesti dilindungi dari perosak yang dikeluarkan oleh  _raspberry-pi_. Untuk ujian, anda boleh menggunakan lapisan aluminium dalam sekeping kertas yang dilipat separuh. 

## Bahan yang diiktiraf:
telah berjaya diuji:   
* Kyg (Asin: B07dpmpw1, bertanda persetubuhan, mata pandangan Amazon)(Diiktiraf sebagai "impuls")  


* Cina Cina Anneng  (  Termometer dengan skrin LCD, dilihat pada AliExpress. )   


* Digoooo RG-8B Higometer-Higometer Cost Cina, tanpa skrin, dilihat di AliExpress.)  



Sensor lain telah dipratentukan dalam sensors.ini, tetapi tidak diuji.  

## Untuk menambah sensor baru:
Protokol pemindahan data dan format data mesti dikenalpasti.  
### Pilihan 1:
Ikuti keluar dari RF_gpio, soit en le lançant dans une fenêtre shell, soit en s'y connectant avec la commande «telnet 127.0.0.1 10000».  
  
  
Apabila sensor menghantar data, dan protokol pemindahan diiktiraf, anda akan melihat sesuatu yang kelihatan seperti ini:   
20; 00; P0102, bit = 36, d0 = 529, D1 = 949, D2 = 1926, DS = 3865; binari = 011100110000100000, hex = 7300d8f20;  
  20; = Sebarang bingkai pelepasan RFLINK bermula seperti ini.  
    00; = Rangka pertama yang diterbitkan.    
 p0102, bit = 36, d0 = 529, d1 = 949, d2 = 1926, ds = 3865 = rf  _gpio a identifié un protocole du type :  
  
  
	bit 0 = d0 d1  
	bit 1 = d0 d2  
	36 Bit Data  
	Tempoh D0 = 520 μs  
	Tempoh D1 = 957 μs  
	Tempoh D2 = 1936 μs  
	 Tempoh segerak ds = 3881 μs    
 duuma = 0111001100000000100000, hex = 7300d8f20; : Data yang diterima dalam binari dan Hexa.   

### Pilihan 2:
Jalankan dalam kulit:   
./analyzi  
dan tekan butang pada alat kawalan jauh, atau tunggu sensor untuk menghantar data. Jika protokol diiktiraf, kita melihat sesuatu yang serupa dengan itu:   
   73 protokol praktikal:   " xxx; P0001, bit = 36, d0 = 689, D1 = 1923, DS = 3890; ID: B1-B36  "    
 Data Perduaan: 01110011000000110110001100100000    
 Hexa Data: 7300D8F20    


Anda kini perlu menganalisis data binari untuk mengenal pasti makna setiap bahagian.  
Anda kemudiannya boleh menambah garis dalam fail dentilos.ini, setiap baris terdiri daripada tiga elemen yang dipisahkan oleh titik koma:   
* Elemen pertama: nama peralatan. Perhatian, jika ia adalah suis, ia mestilah sebahagian daripada senarai unsur yang diiktiraf oleh penotikz.  


* Elemen Kedua: Protokol. Salin apa yang ditunjukkan oleh RF_gpio ou analizo.  
    

    

	Contoh: P0102, bit = 36, D0 = 561, D1 = 1899, D2 = 3845, DS = 9158  
		bermakna: protokol P0102 (bit 0 = d0 d1, bit 1 = d0 d2), 36 bit dengan bingkai, tempoh d0 = 561 μS, tempoh D1 = 1899 μs, tempoh D2 = 3845 μs, tempoh Synchro DS = 9158 μs  
	 	 protokol, bit, bahagian D0 dan D1 digunakan untuk membezakan peranti.   
* Elemen Ketiga: Penerangan Data, Setiap medan memisahkan dari orang lain oleh koma  


	Contoh untuk kawalan jauh pelbagai saluran, ID: ID: B1-20, CMD: B21-21, SBLE: B22-24  
	 	 ID: B1-20 bermakna bahawa ID alat kawalan jauh berada dalam bit 1 hingga 20  ( bit 1 = bit pertama dihantar 
 	  	 cmd: B21-21 bermaksud Perintah yang dihantar   (  ON / OFF  )   terletak di Ilo 21.    
 	  	 Perubahan: B22-24 bermaksud bahawa bilangan Jack yang dikendalikan terletak di Bits 22 hingga 24.    
 	 Diakui oleh Domomoticz, nama lapangan mestilah berada di dalam senarai yang terkenal  ( See Sensilos.txt ). Walau bagaimanapun, kita boleh meletakkan apa yang kita mahu, hanya bidang yang akan diabaikan oleh Domomoticz.   
	Terdapat beberapa suite kecil, sebagai contoh: CMD: B17-17: B15-15: B16-16 Concatenate bit 17 dan 16 dalam perintah itu.  
	Nilai bit tertentu boleh diuji, sebagai contoh: CST2: B43-48 = 1 akan mengesahkan bahawa bit 43 48 mengandungi nilai 1 (dalam penerimaan, dan mempengaruhi kepingan-kepingan ini dalam pengedaran , CST2: B43-48! 1 Sahkan bahawa bit 438 tidak mengandungi nilai 1 (heksadesimal) dalam penerimaan tetamu.  
	 kita boleh mengisytiharkan medan berkod BCD   (  pengekodan perpuluhan binari  ) : letakkan B bukannya b. Contoh: Temp: B12-15: B16-19: B20-23 mengisytiharkan medan suhu yang nombor pertama adalah kepada bit 12-15, yang kedua kepada bit 16 hingga 20 dan yang ketiga di bit 21 hingga 23.    
 	 Finishing Finishing dengan  " -Inv " adalah medan khas yang akan mengambil songsang  ( melengkapkan kepada satu medan homemonya dalam persembahan.   
	Anda boleh menetapkan nilai ke medan yang tidak dijumpai dalam data per "=". Contoh: CMD = pada  
	 Pengiraan mudah boleh dibuat: Penambahan, pendaraban dan penolakan adalah mungkin   (  Dalam urutan yang betul ini  ), pemalar berada dalam heksadesimal.    
 	 	 Contoh untuk menukar dalam sepuluh data ° C yang berada dalam kesepuluh ° F + 900:   
	  	 temp: b17-28 + -4C4  *  5/9    (   Jadi: data  _finale = (donnée_  haiwan -1220  )   *   5/9   )    

Dalam resit semua baris yang memenuhi syarat akan menjana garis, jika anda ingin mengelakkan positif palsu, anda boleh memulakan atau mengeluarkan garisan yang tidak sepadan dengan peralatan anda.  
Mengenai soalan, hanya baris pertama dengan nama peralatan yang betul akan digunakan.  
	
		
Jika protokol tidak diiktiraf, anda boleh gunakan untuk menganalisis untuk mengkaji dengan meningkatkan kata kerja dengan pilihan -v, -vv atau -vv. Tetapi rf_gpio ne pourra pas le reconnaitre sans développement supplémentaire.  
  
  


## Protokol yang disokong:

Hanya protokol dengan sekurang-kurangnya ciri-ciri berikut mempunyai peluang untuk diiktiraf:   
* Masa berdenyut sentiasa > 100 μs  


* Tidak lebih daripada tiga tempoh yang berbeza untuk mewakili data.  


* Setiap bahagian dikodkan dengan dua denyutan, selalu dengan cara yang sama.  


* Setiap bingkai dikelilingi oleh dua isyarat penyegerakan masa >2500 μs.  


* Bingkai tidak mengandungi lebih daripada 200 denyutan.  



Dalam pertunjukan, kod berputar dan jumlah pengesahan tidak diberikan.  
