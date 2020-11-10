# Zadania rekrutacyjne do działu elektroniki

Ubiegający się o miejsce: Mikołaj Czerniak

## STM32 / RPi software 

### Zadanie 1: STM32 + UART + Przerwania

* ramka z 4 bajtami -> prędkość do 1000 bramek/s -> maksymalna prędkość to 4kB/s = 32kb/s

* baudrate = 38400

UART-> STM32 -> DMA -> konwersja -> PWM

STM32 -> I2C -> EEPROM

Mam w domu akurat EEPROM na I2C , 128B ale kod będę robił tak że wszystkie kwestie związane z rozmiarem będę w #define

logi:

pierwsze dwa bajty pójdą na adres ostatniego zapisu/pomiaru

źródła:

* [UART with DMA](https://www.youtube.com/watch?v=urHGtvLYctk)
* 

### Zadanie 2: Czujnik wilgoci

* mikroklocek: STM32G031J6M tylko 8 nóżek, a nawet 2 zostaną nieużyte 
* to moje pierwsze PCB, wszystko robiłem wcześniej na tych płytkach prototypowych w kolorze brązowym
* kod:
  * UART
  * TIM1_CH1 -> PWM
  * ADC1_IN0 -> DMA do jednego pomiaru chyba nie ma sensu
  * do robienia pomiaru w odpowiednim czasie wykorzystam timer i globalne przerwanie od przekręcenia licznika
  * zegar na 16MHz, prescaller na 16000-1, a counter na 2000-1 i jest co 0.5Hz przerwanie, w którym zmierzę napięcie na tym czujniku oraz wyślę uartem stan wilgoci.
  * to 60% to wziąłem ze zdjęcia z dokumentacji metodą aproksymacji inżynierskiej połączonej z aproksymacją losową i wyszło mi 1.6V więc teraz muszę to przeliczyć ile to będzie dla 12 bitowej (4095) zmiennej względem 3.3V
  * co do dimingu diody:
    * jak przekroczy ten threshold to odpalam drugi timer i w jego przerwaniu zmieniam wypełnienie pwm
  * no a drugi pomysł to danie watchdoga, i pomiar adc robić w timerze po prostu, a watchgod srobi mi przerwanie od threshloda, 

### Zadanie 3: STM32 + CAN

brakło czasu, przepraszam. W sumie to siebie.

### Zadanie 4: Matryca LED 8x8

Wyświetlacz jest typu wspólna katoda.

linki:

* [król salamon](https://msalamon.pl/nigdy-wiecej-multipleksowania-na-gpio-max7219-w-akcji-cz-3/)
* [jakiś tam scheamt połaczeniowy](https://www.hackster.io/tony_s/smart-matrix-led-display-f150c6)
* [data scheat](https://www.sparkfun.com/datasheets/Components/General/COM-09622-MAX7219-MAX7221.pdf) <- też w sumie ważne
* [pinout do nucleo](https://www.snapeda.com/parts/NUCLEO-F401RE/STMicroelectronics/view-part/?welcome=home) <- wiem że do f4 ale pinologia niby ta sama
* [scheamat tego wyswietlacza](https://github.com/aniline/akn-kicad-libs)

[poradnik do gotowej libki](https://msalamon.pl/nigdy-wiecej-multipleksowania-na-gpio-max7219-w-akcji-cz-3/)

[poradnik robiony metodą bitbanging](https://controllerstech.com/led-dot-matrix-and-stm32/)

Nie mówię że jej nie potrafię użyć, czytać potrafię, ale no chciałem jakoś sam poczytać jak to w sumie działa, jak te rejestry trzeba nastawić przy inicjalizacji. Tamta libka jest bardzo dobra, ale no jest napisana przez profesjonalistę, są tam wstawki na rejestrach no i po prostu uznałem, że sam poczytam jak taki sygnał nadać.

Przydatny cytat:

> "R[0÷7], czyli wiersze – są to kolejne cyfry, które wpisywane są do [MAX7219](https://sklep.msalamon.pl/kategoria-produktu/wyswietlacze/led/?utm_source=blog&utm_medium=article&utm_campaign=max7219&utm_content=Text). C[0÷7] natomiast są to adekwatne bity znajdujące się w każdej cyfrze(wysyłanym bajcie). Reasumując. Chcąc ustawić pixel (0,0) musisz ustawić siódmy bit zerowej cyfry w MAX7219. Natomiast aby ustawić pixel np. (4,2) musisz zapalić BIT3 drugiej cyfry."
`Mateusz Salamon`

Jakoś nie wiem jak na tym zdjęciu kolejność mam obrać, znaczy wiem, zależy od połączenia, ale pierw kod, potem schemat.

<img src="https://msalamon.pl/wp-content/uploads/2019/04/MAX7219-Matrix-Type-0-1-300x300.png" alt="MAX7219-Matrix-Type-0-1-300x300.png (300×800)" style="zoom: 80%;" />



### Zadanie 5: Drgania styków - programowo

Dioda elektroluminescencyjna dioda (dioda LED) została podłączona do pinu PA5 na płytce ewaluacyjnej nucleo f103rb. No jest to ta dioda na płytce ale to chyba nie jest problem, no i tak w tym programie świeceni robię programowo a nie sprzętowo.

### ZADANIE 6: ESC

Z dokumentacji wyczytałem jakie te czasy mają być:

* szerokosć impulsu -> 0.0025s
* 

Czemu stm32 a nie raspberka
Ale każdy ma rację, trochę...







