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
