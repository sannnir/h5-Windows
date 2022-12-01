# h5_Windows

Tehtävät ovat osa Tero Karvisen Palvelinten hallinta (Configuration Management Systems)- kurssia. Kurssitoteutus sekä tarkemmat tehtäväkuvaukset löytyvät [täältä](https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/). 
Tehtävän lähteet löytyvät lopusta.

Tehtävät on toteutettu Microsoft Windows 11 Home-käyttöjärjestelmällä (versio 10.0.22621). Lisäksi käytössä on ollut myös Virtual Boxiin (Versio 6.1) asennettu Ubuntun käyttöjärjestelmä (versio 22.04.1).
Tehtävät on tehty marraskuussa 2022 ja tehtävän tekemisessä on hyödynnetty luennolla tehtyjä muistiinpanoja. Luennon piti Tero Karvinen (24.11.2022) ja se oli kurssin viides luento, jonka aiheena oli "Vaihtoehtoiset järjestelmät", joista kävimme läpi Windowsia.

## a) Hello Window Salt! Tee Windowsille SLS-tiedostoon Salt-tila, joka tekee tiedoston nimeltä "suolaikkuna.txt".

Aloitetaan asentamalla Salt Minion Windowsille ([download install file](https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html)).

Kun tiedosto on tullut latauskansioon, avataan Windowsin PowerShell ”run as a administrator”:ina.

Siirrytään lataukset kansioon

     cd C:\Users\sanni\Downloads 

Tämän jälkeen avataan ladattu tiedosto, joka itseltäni löytyi nimellä 

    ./Salt-Minion-3004.2-1-Py3-AMD64.msi

Tehdään asennus.

<img width="469" alt="01_SALTwindowsille" src="https://user-images.githubusercontent.com/117899949/204536903-1870dbdf-3cf4-4529-8e39-97c4bc276622.png">

Testataan, että salt toimii ajamalla lokaalisti komento "echo hello":

    salt-call --local cmd.run ”echo hello”

<img width="443" alt="1 1" src="https://user-images.githubusercontent.com/117899949/204537047-ec99596a-354f-4f8a-83fa-69a1e5c78d1f.png">

Tämä ok. Luodaan levylle C ensin polku Salt-kansioon ja lopulta Salt-kansio.

<img width="353" alt="1" src="https://user-images.githubusercontent.com/117899949/204537144-17facf0e-aa44-4b22-a571-d57a7734d4ac.png">

Luodaan tekstitiedosto suolaikkuna.txt avaamalla notepad:

     notepad.exe suolaikkuna.txt

Tehdään mielikuvituksellinen tiedosto ja kirjoitetaan sinne ”Hello Wordl”.
Tallennetaan.

<img width="371" alt="2" src="https://user-images.githubusercontent.com/117899949/204537173-b7888c26-2fc3-4ad6-a516-fc25a210d880.png">

Luodaan sitten init.sls- tiedosto.

<img width="403" alt="3" src="https://user-images.githubusercontent.com/117899949/204537297-031abc0a-4ffb-4a6c-9cbc-46654fde4399.png">

Ajetaan tiedosto tämän jälkeen lokaalisti saltilla.
En ollut varma, kuinka tämä toimii Windowsilla, joten lähdin ensin kokeilemaan tapaa, jolla se toimii Linuxilla

     salt-call --local state.apply init

Erroria tulee, eli ei toimi.
Kokeillaan laittaa polku väliin:

      salt-call --file-root=C:\Users\sanni\srv\salt --local state.apply init

Nyt sentään homma menee läpi, mutta joku on väärin, sillä Result on False. Errorviesti kertoo, että ”salt is not an absolute path”.

<img width="527" alt="4" src="https://user-images.githubusercontent.com/117899949/204537368-8a66a214-9d74-458e-b341-6522ffad27b7.png">

Avataan init.sls ja tutkitaan tiedostoa.
Huomaan, että osoitepolku on jäänyt vähän vajaaksi. Korjataan siis polkua.

Korjaan siihen C:\Users\sanni\srv\salt\suolaikkuna

<img width="652" alt="5" src="https://user-images.githubusercontent.com/117899949/204537419-d6e3cb98-72fb-4477-9da7-327dd9e11aee.png">

Nyt näytti menevän läpi.

## b) Ei vihkoa, ei kynää. Kerää Windows-koneen tekniset tiedot tekstitiedostoon.

Saltin mukana tulee käyttöliittymä, jolla saadaan tietoa taustasta olevasta järjestelmästä, kuten käyttöjärjestelmästä, verkkotunnuksesta, IP-osoitteesta, kernelistä, muistista ja monista muista omianaisuuksista. Tämä rajapinta on Salt grains. (Saltstack 2022.)

Testaan ensin grains.items komennon Windowsilla:

    salt-call --local grains.items

Tämä toimii ja antaa tietoja koneesta.
Sitten siirretään tiedot tekstitiedostoon.

    salt-call --local grains.items > tietoja.txt

kurkataan Salt-kansio ls

<img width="443" alt="6" src="https://user-images.githubusercontent.com/117899949/204537547-da8222b3-0558-45d8-9bfa-d8adc3e773da.png">

Sieltä löytyy. 
Testaan vielä, toimiiko Windowsilla cat tietoja.txt- komento

    cat tietoja.txt
    
Toimii! Eli tiedot on lisätty tekstitiedostoon.

## c) Kop kop. Onko TCP-portti auki vai kiinni? Näytä esimerkit portin kokeilusta Linuxilla ja Windowsilla. Näytä kummallakin käyttöjärjestelmällä ainakin yksi avoin ja yksi suljettu portti. (Kokeile tätä vain omaan koneeseesi. Vieraiden koneiden ja verkkojen porttiskannaaminen on kiellettyä. Yksittäisen portin testaavat komennot ovat suositeltavia, esim. nc, tnc) 

### Windowsilla: 

Tietokoneita ja tietoverkkoja käsitellessä ei voi välttyä sanalta ”portti”. Portit toimivat ns. valvontapisteinä saapuvalle ja lähtevälle liikenteelle, kun muodostetaan yhteyksiä muiden päätelaitteiden tai Internetin välille. (Ionos 2022.)

Windowsilla komento netstat -ano avaa porttitietoja. Status-otsikon alla tieto ”LISTENING” tarkoittaa, että palveluun on yhteys ko. portin kautta, kun puolestaan ” ESTABLISHED” tarkoittaa sitä, että portti on avoinna, mutta yhteyttä ei ole muodostettu. (Ionos 2022.)

     netstat -ano

Tällä saa kuitenkin listan kaikista avoinna olevista TCP/UDP-porteista.
Mikäli haluaa yksittäisen portin, voi käyttää komentoa

    test-netconnection <iposoite> -p <porttinumero>
    
    test-netconnection toimii myös lyhenteellä tnc
    
### Linuxilla:

Linuxilla saa seuraavalla komennolla listattua portit, joista näkyy ovatko ne avoinna vai kiinni.

    sudo ss -tulpn 



********************************************
LÄHTEET:

Ionos 2022. Port tests: Checking open ports with a port check. Luettavissa: https://www.ionos.com/digitalguide/server/security/checking-open-ports/. Luettu: 29.11.2022

Saltstack 2022. Grains. Luettavissa: https://docs.saltproject.io/en/latest/topics/grains/index.html. Luettu: 29.11.2022

Tero Karvinen 2022. Control Windows with Salt. Luettavissa: https://terokarvinen.com/2018/control-windows-with-salt/?fromSearch=windows%20salt. Luettu: 29.11.2022

Tero Karvinen 2022.Configuration Management Systems - Palvelinten Hallinta. Luettavissa: https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/. Luettu: 29.11.2022

Vivek Gite 2022. How to check open ports in Linux using the CLI. Luettavissa: https://www.cyberciti.biz/faq/how-to-check-open-ports-in-linux-using-the-cli/. Luettu: 29.11.2022
