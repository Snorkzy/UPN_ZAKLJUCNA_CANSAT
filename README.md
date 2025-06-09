# UPN_ZAKLJUCNA_CANSAT
Na začetku naprava za zbiranje okoljskih in lokacijskih podatkov samodejno inicializira vse senzorje: temperaturni in vlažnostni senzor DHT11, barometrični senzor DPS310, GPS modul in SD kartico za shranjevanje podatkov. Na serijskem monitorju se ob zagonu izpiše, ali je bila SD kartica uspešno zaznana.

Naprava deluje samodejno in v rednih časovnih intervalih (vsako sekundo) zajema meritve. GPS modul neprestano prejema lokacijske podatke – koordinate, višino, hitrost in število satelitov. Hkrati se iz senzorja DHT11 pridobita trenutna temperatura zraka in relativna vlažnost. Barometrični senzor DPS310 izmeri dodatno temperaturo in zračni tlak, iz katerega se izračuna nadmorska višina po barometrični formuli.

Vsi zbrani podatki se nato zložijo v strukturiran tekstovni niz, ločen s podpičji (;). Če GPS trenutno ne zagotavlja veljavnih podatkov, se v zapis dodajo ničelne vrednosti, da se ohrani konsistentnost formata.

Ko je niz pripravljen:

-Se ta zapiše na SD kartico v datoteko data.txt.

-Se pošlje brezžično prek APC220 modula, kar omogoča oddaljeno spremljanje podatkov.

-Se izpiše na serijski monitor, da lahko uporabnik sproti spremlja delovanje sistema.

Celoten postopek se ponovi vsakih 1000 ms (1 sekunda), brez potrebe po interakciji uporabnika. Naprava je torej samodejna vremenska in lokacijska postaja, ki podatke neprestano beleži, pošilja in shranjuje.

Če pride do težave s SD kartico ali odpiranjem datoteke, se to izpiše na serijski monitor kot napaka.


## Povezava komponent

| Komponenta            | Arduino pin         |
|----------------------|---------------------|
| DHT11 Data           | 8                   |
| GPS RX / TX          | 4 (RX), 3 (TX)      |
| APC220 RX / TX       | 6 (RX), 7 (TX)      |
| SD kartica CS        | 10                  |
| DPS310 (I2C)         | A4 (SDA), A5 (SCL)  |
| Napajanje            | 5V / GND            |


##Slika vezja
![Opis slike]



# KOSOVNICA (Potreben material)

| #  | Element               | Količina | Opombe                             |
|----|-----------------------|----------|----------------------------------|
| 1  | Arduino Uno           | 1        | Mikrokontrolerska plošča          |
| 2  | DHT11 senzor          | 1        | Temperatura in relativna vlažnost |
| 3  | DPS310 senzor         | 1        | Barometrični tlak in temperatura  |
| 4  | GPS modul (npr. Neo-6M) | 1      | Lokacijski modul                  |
| 5  | APC220 brezžični modul| 1        | Brezžični prenos podatkov         |
| 6  | SD kartica + modul    | 1        | Za shranjevanje podatkov          |
| 7  | Žice za povezavo (jumperji) | cca 15 | Moški-moški in moški-ženski       |
| 8  | Napajalni kabli        | 1        | Za napajanje celotne naprave      |


## Koda

Koda je v datoteki `koda.ino`.


## 🧪 Knjižnice

Preveri, da imaš nameščene naslednje knjižnice (prek Library Managerja):

- `DHT sensor library` (Adafruit)
- `Adafruit DPS310`
- `TinyGPSPlus`
- `Adafruit Unified Sensor`
- `SdFat` (za SD modul)
- `SoftwareSerial`



## Uporaba

- Naloži kodo na Arduino.
- Vklopi napajanje.
- Poveži se na serijski monitor (9600 baud), da vidiš izpis.
- Podatki se bodo zapisovali na SD kartico in pošiljali prek APC220.

## Napake in diagnostika

- Če SD kartica ni zaznana ali ne uspe odpreti datoteke, se to izpiše na serijski monitor.
- GPS lahko vrne ničelne vrednosti, če nima signala.



## 🚀 Možne nadgradnje

- Uporaba RTC modula za točen čas zapisa
- OLED zaslon za prikaz podatkov
- Shramba v CSV formatu
- Zvočno opozorilo ob izgubi GPS signala

