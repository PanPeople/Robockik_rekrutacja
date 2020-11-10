# Zadania rekrutacyjne do działu elektroniki

# `do poddziału: STM32/RPi software`

**Ubiegający się o miejsce: Mikołaj Czerniak, EKA, 2k19**

Uznałem że zostawię notatki, bo wtedy widać że nie było to dla mnie jedno posiedzenie przy kawie i napisane wszystkiego z palca. Część właściwa znajduje się w sekcji: `Odpowiedź/Wypowiedź:`

## STM32 / RPi software 

### Zadanie 1: STM32 + UART + Przerwania

#### `drobne notatki:`

>* ramka z 4 bajtami -> prędkość do 1000 bramek/s -> maksymalna prędkość to 4kB/s = 32kb/s
>
>* baudrate = 38400
>
>UART-> STM32 -> DMA -> konwersja -> PWM
>
>STM32 -> I2C -> EEPROM
>
>Mam w domu akurat EEPROM na I2C , 128B ale kod będę robił tak że wszystkie kwestie związane z rozmiarem będę w #define
>
>logi:
>
>pierwsze dwa bajty pójdą na adres ostatniego zapisu/pomiaru
>
>źródła:
>
>* [UART with DMA](https://www.youtube.com/watch?v=urHGtvLYctk)

#### `Odpowiedź/Wypowiedź:`

komunikacja z RPi odbywa się za pomocą UART-a z STM32

prędkość baudrate określiłem na "standardowe"  3840

wartości z Rx_UARTA trafiają do DMA, skąd w pętli while przeprowadzam konwersję, pod sygnał dla serwa (jest to PWM, oraz chciałem zobaczyć "efekt" swojej pracy). Gdyby zaszła taka potrzeba można było robić konwersje na trochę innym zakresie np. dla ESC, a programiście od RPi byłoby łatwej podawać sygnał w formacie np. od 0 - 255 , niż wartości poniżej zera.

Niestety w kwestii logów. Nie udało mi się tego dokończyć, jest jakiś szkielet tego jak ma to wyglądać.

Na pewno chciałem na pierwszych dwóch bajtach zapisywać adres na jaki został wykonany zapis.

Przez to po zaniku zasilania będę mógł sczytać ostatnio użyty adres i przeprowadzać zapis na następnych komórkach.

#### `link do kodu:`

[Robocik_1](https://github.com/PanPeople/Robockik_rekrutacja/tree/master/Robocik_1)

### Zadanie 2: Czujnik wilgoci

drobne notatki:

>  * mikroprocesor: STM32G031J6M tylko 8 nóżek, a nawet 2 zostaną nieużyte 
> * to moje pierwsze PCB, wszystko robiłem wcześniej na tych płytkach prototypowych w kolorze brązowym
> * kod:
>   * UART
>   * TIM1_CH1 -> PWM
>   * ADC1_IN0 -> DMA do jednego pomiaru chyba nie ma sensu
>   * do robienia pomiaru w odpowiednim czasie wykorzystam timer i globalne przerwanie od przekręcenia licznika
>   * zegar na 16MHz, prescaller na 16000-1, a counter na 2000-1 i jest co 0.5Hz przerwanie, w którym zmierzę napięcie na tym czujniku oraz wyślę uartem stan wilgoci.
>   * to 60% to wziąłem ze zdjęcia z dokumentacji metodą aproksymacji inżynierskiej połączonej z aproksymacją losową i wyszło mi 1.6V więc teraz muszę to przeliczyć ile to będzie dla 12 bitowej (4095) zmiennej względem 3.3V
>   * co do dimingu diody:
>     * jak przekroczy ten threshold to odpalam drugi timer i w jego przerwaniu zmieniam wypełnienie pwm
>   * no a drugi pomysł to danie watchdoga, i pomiar adc robić w timerze po prostu, a watchgod srobi mi przerwanie od threshloda, 

#### `Odpowiedź/Wypowiedź:`

**KOD:**
Przygotowałem 3 timery:

> do komunikacji
> do PWM dla diody
> dla rozjaśniania didy (zmiany wypełnień co określone przerwanie)

Komunikacja z urządzeniem hostem odbywa się za pomocą UART-a.

Wykorzystałem przerwanie od przepełnienia/ obrotu licznika w timerze, wtedy szczytuję wartość z ADC robię konwersję na napięcie. Następnie wyliczam wilgotność ze wzoru w postaci **y = ax +b**. W tle załączyłem też watchdoga aby sprawdzał czy nie przekroczono 1.6V. Wtedy uruchomione jest przerwanie i zostaje wyświetlona wartość o przecieku.

<img src=".\HIH-5031-0001.PNG" alt="HIH-5031-0001.PNG" style="zoom: 50%;" />

**PŁYTKA/URZĄDZENIE:**
Wykorzystałem mały mikrokontroler od ST, który ostatnio rzucił mi się w oczy: STM32G031J6M

Jest on w obudowie SOIC8

Płytkę wykonałem w programie KiCad, w Internecie znalazłem biblioteki potrzebnych komponentów.

<img src=".\schemat_zad_2.png" alt="" style="zoom: 40%;" />

<img src=".\pcb_view_zad_2.png" alt="" style="zoom: 40%;" />

<img src=".\pcb_zad_2.png" alt="" style="zoom: 40%;" />

Niestety jest to moje pierwsze pcb, więc na pewno pachnie (lub jak kto woli "śmierdzi") amatorszczyzną, ja jestem zadowolony w każdym razie.

#### `link do kodu:`

[Robocik_2](https://github.com/PanPeople/Robockik_rekrutacja/tree/master/Robocik_2)

### Zadanie 3: STM32 + CAN

#### `Odpowiedź/Wypowiedź:`

Niestety nie wypowiem się absolutnie nic w kwestii tego zadania, wiem tylko że wydawało się ona najbardziej skomplikowane

### Zadanie 4: Matryca LED 8x8

drobne notatki:

> Wyświetlacz jest typu wspólna katoda.
>
>linki:
>
>* [król salamon](https://msalamon.pl/nigdy-wiecej-multipleksowania-na-gpio-max7219-w-akcji-cz-3/)
>* [jakiś tam scheamt połaczeniowy](https://www.hackster.io/tony_s/smart-matrix-led-display-f150c6)
>* [data scheat](https://www.sparkfun.com/datasheets/Components/General/COM-09622-MAX7219-MAX7221.pdf) <- też w sumie ważne
>* [pinout do nucleo](https://www.snapeda.com/parts/NUCLEO-F401RE/STMicroelectronics/view-part/?welcome=home) <- wiem że do f4 ale pinologia niby ta sama
>* [scheamat tego wyswietlacza](https://github.com/aniline/akn-kicad-libs)
>
>* [poradnik do gotowej libki](https://msalamon.pl/nigdy-wiecej-multipleksowania-na-gpio-max7219-w-akcji-cz-3/)
>
>* [ poradnik robiony metodą bitbanging](https://controllerstech.com/led-dot-matrix-and-stm32/)

>Nie mówię że jej nie potrafię użyć, czytać potrafię, ale no chciałem jakoś sam poczytać jak to w sumie działa, jak te rejestry trzeba nastawić przy inicjalizacji. Tamta libka jest bardzo dobra, ale no jest napisana przez profesjonalistę, są tam wstawki na rejestrach no i po prostu uznałem, że sam poczytam jak taki sygnał nadać.

>Przydatny cytat:

>> "R[0÷7], czyli wiersze – są to kolejne cyfry, które wpisywane są do [MAX7219](https://sklep.msalamon.pl/kategoria-produktu/wyswietlacze/led/?utm_source=blog&utm_medium=article&utm_campaign=max7219&utm_content=Text). C[0÷7] natomiast są to adekwatne bity znajdujące się w każdej cyfrze(wysyłanym bajcie). Reasumując. Chcąc ustawić pixel (0,0) musisz ustawić siódmy bit zerowej cyfry w MAX7219. Natomiast aby ustawić pixel np. (4,2) musisz zapalić BIT3 drugiej cyfry."
>> `Mateusz Salamon`

>Jakoś nie wiem jak na tym zdjęciu kolejność mam obrać, znaczy wiem, zależy od połączenia, ale pierw kod, potem schemat.



#### `Odpowiedź/Wypowiedź:`

Nie udało mi się dokończyć schematy połączeniowego, mam tylko pobrane schematy użytych komponentów.

<img src=".\matrix_M.png" alt="" style="zoom: 50%;" />

<img src=".\matrix_C.png" alt="" style="zoom: 50%;" />

<img src=".\rejestry_zad_4.png" alt="" style="zoom: 50%;" />

**KOD:**

Zadanie było najbardziej czasochłonne dla mnie jeśli chodzi o kwestie kodu.

Komunikacja odbywa się za pomocą SPI w trybie Master Transmit Only. Linię CS steruję samodzielnie.
Komunikacja polega na ustawianiu odowiednich rejestów poprzez wysłanie odpowiednich wartości po SPI.

Pierwsze kilka komend to konfiguracja samego wyświetlacza.

Następnie wysyła się adres interesującej nas liczby(gdybyśmy korzystali z MAX7219 do obsługi 8 segmentowego licznika), a następnie diodę którą chcemy włączyć. W praktyce więc wybieramy adres od 1 do 8 dla Y oraz 1 do 8 dla X

#### `link do kodu:`

[Robocik_4](https://github.com/PanPeople/Robockik_rekrutacja/tree/master/Robocik_4)

### Zadanie 5: Drgania styków - programowo

Dioda dioda elektroluminescencyjna (inaczej dioda LED) została podłączona do pinu PA5 na płytce ewaluacyjnej nucleo f103rb. No jest to ta dioda na płytce ale to chyba nie jest problem.

#### `Odpowiedź/Wypowiedź:`

Wykorzystałem NUCLEO-F103rb

Wykorzystałem LED na moim nucelo. Która jest podłączona do wyjścia PA5.

Przycisk wyprowadziłem na wyjście PA0

Do interakcji/ detekcji przycisku wykorzystałem Input Capture. Dzięki temu mogę ustawić minimalny i maxymalny czas wciśnięcia oraz mogę zastosować filtr softwarowy na detekcję zbocza opadającego.

Każdy pomiar input capture, który zmieścił się w narzuconym zakresie (czas trwania wciśnięcia) powoduje inkrementacje "flag", która w połączeniu ze switchem pozwala na przechodzenie pomiędzy kolejnymi trybami świecenie i nie świecenia .

Dodatkowo doczytałem że, do debouncingu mógłbym wykorzystać dodatkowo filtr RC ze wzorem: $f =\frac{1}{2\Pi \cdot R \cdot C }$

Więc można by wykorzystać np. warości  10$k\Omega$ oraz 10$\mu F$ 

#### `link do kodu:`

[Robocik_5](https://github.com/PanPeople/Robockik_rekrutacja/tree/master/Robocik_5)

### ZADANIE 6: ESC

drobne notatki:

> Z dokumentacji wyczytałem jakie te czasy mają być:

> * szerokosć impulsu -> 0.0025s

>  Czemu stm32 a nie raspberka

#### `Odpowiedź/Wypowiedź:` 

Program , który napisałem, stopuje esc a następnie przyśpiesza w jednym i w drugim kierunku.

<img src=".\esc_timing.png" alt="" style="zoom: 50%;" />

Do otrzymywania danych do sterowania można wykorzystać program z zadania 1.

Co do odwróconego sygnału, uznałem że chodzi o logiczną negację, ale pewny nie byłem.

Dlatego w kodzie dodałem drugą funkcje inicjalizującą timer, gdzie ustawiam tryb na PWM_MODE_2

Mógłbym zrobić to w samym Cube, ale postanowiłem zajrzeć to funkcji inicjującej i sam zrobić podmiankę.

#### `link do kodu:`

[Robocik_6](https://github.com/PanPeople/Robockik_rekrutacja/tree/master/Robocik_6)

### Sekcja "O Tobie"

Witam, jestem Mikołaj. Chciałbym się ubiegać o stanowisko na dział Elektroniki do poddziału software. No generalnie lubię robić też inne rzeczy, modelowanie, druk 3d, elektronika użytkowa.  Ale teraz zacząłem bawić się więcej w software bo w sumie bez niego nic nie zrobię przy projektach.
Ale generalnie to lubię elektronikę, niestety jestem człekiem po liceum więc wiedza jest zdobywana metodą nieuporządkowanej losowości.
Ale mimo to udał mi się zrobić w swojej małej "karierze" kilka ciekawych rzeczy:

<img src=".\zdjecia\zdjecie (1).jpg" alt="" style="zoom: 30%;" />

> w pełni przenośna konsola Nintendo Wii, nie jest to emulator a pełnoprawna przycięta płyta główna od Wii oraz elegancki ekran LCD na złączu VGA

<img src=".\zdjecia\zdjecie (1).png" alt="" style="zoom: 30%;" />

	>Manipulator na serwach

<img src=".\zdjecia\zdjecie (2).jpg" alt="" style="zoom: 20%;" />

> moja Anet A6, po wielu modyfikacjach, w tym czujnik auto poziomowania oparty o diodę i fotokomórkę

<img src=".\zdjecia\zdjecie (4).jpg" alt="" style="zoom: 60%;" />

> mój głośnik bluetooth, z fajnych bajerów to brak pętli uziemienia więc nie ma pierdzenia i pisków



No i lubię dobre gry, fabuła na pierwszym miejscu np. Life is Stange. Lubię MCU, ale najlepszy super bohater to Batman.
