---
author:
- |
  Artur Michalski, Ignacy Sujecki, Mateusz Tabor,\
  Dawid Maksymowski, Damian Wyszołmirski
title: |
  ![image](MINI logo long.png){width="\\textwidth"}\
  System rezerwacji pokoi hotelowych

  ------------------------------------------------------------------------

  \
  IO PROJEKT - Finalna Specyfikacja
---

# Wprowadzenie

Celem projektu jest stworzenie systemu do rezerwacji pokoi w hotelach.
Aplikacja pozwala obsłudze hotelu na udostępnienie oferty w systemie, a
użytkownikowi na zarezerwowanie pokoju z oferty w określonym oknie
czasowym. Obsługa hotelu dzieli się na pracowników z różnym poziomem
dostępu do zasobów aplikacji. Manager hotelu pełni rolę administratora
modułu hotelowego. Serwer aplikacji przetrzymuje kopie wszystkich ofert,
więc po złożeniu rezerwacji i sprawdzeniu kieruje prośbę do systemu
hotelowego, gdzie hotel jeszcze raz potwierdza dostępność.

## Glosariusz

Poniżej znajdują się definicje kluczowych elementów naszego systemu.
Warto wśród nich zwrócić uwagę na rozróżnienie Pokoju oraz Oferty.
Podział taki został zastosowany, aby nie powielać tych samych informacji
wśród różnych pokoi, będących w istocie takimi samymi z punktu widzenia
Klienta.

-   **Pokój**\
    Reprezentuje fizyczny pokój obecny w hotelu, w którym się znajduje.
    Jest konkretnym reprezentantem Oferty (patrz: następny punkt). Dane
    dotyczące pokoi są przechowywane w bazie danych Hotelu.
    Najwygodniej, gdy jego ID jest tożsame z fizycznym numerem pokoju,
    jednak -- jako, że informacje o tych obiektach są przechowywane w
    lokalnej bazie hotelu -- identyfikacja leży całkowicie w gestii
    zarządcy tej bazy.

-   **Oferta**\
    Reprezentuje pokoje o takich samych parametrach, które są
    nierozróżnialne z punktu widzenia Klienta rezerwującego pokój.
    Oferta ma określoną liczbę gości (dorosłych oraz dzieci), posiada
    opis i (opcjonalnie) zdjęcia. Ponieważ oferta jest tworzona lokalnie
    w module hotelowym, jest rozróżnialna przez ID hotelu, w którym się
    znajduje oraz jej lokalne ID.

-   **Rezerwacja**\
    Reprezentuje wynajem pokoju przez klienta w określonym odcinku
    czasowym. Klient dokonuje rezerwacji pokoju poprzez wybranie Oferty.
    To, który Pokój zostanie przypisany do Rezerwacji jest decyzją
    hotelu. ID rezerwacji jest przypisywane przez hotel i unikatowe w
    obrębie modułu hotelowego.

-   **Opinia**\
    Po pobycie w danym hotelu w ciągu 30 dni klient ma możliwość
    wystawienia opinii, czyli krótkiego opisu swoich przeżyć w hotelu
    wraz z oceną w skali od 1 do 10. Opinia jest przetrzymywana przez
    serwer i dołączana do wysyłanych ofert hotelu którego dotyczy. ID
    opinii jest przypisywane przez serwer więc jest unikalne w całym
    systemie.

-   **Klient**\
    Użytkownik Aplikacji klienckiej, który może w systemie się
    zarejestrować oraz zalogować. Tylko za pośrednictwem konta
    Klienckiego możliwe jest przeglądanie Ofert, Hoteli oraz złożenie
    rezerwacji. ID klienta jest przypisywane przez serwer więc jest
    unikalne w całym systemie.

-   **Hotel**\
    Reprezentuje fizyczną jednostkę hotelu rzeczywistego świata.
    Dodawanie Ofert i reklamowanie się do potencjalnych klientów za
    pośrednictwem Systemu jest możliwe wyłącznie za pośrednictwem
    takiego konta.

-   **Pracownik**\
    Reprezentuje fizyczną osobę pracującą w hotelu. Posiada uprawnienia
    i w zależności od nich może edytować i pobierać dane z systemu
    hotelowego, które mogą usprawniać jego pracę.

# Podział na moduły

System składa się z 3 modułów niezależnych od siebie. Mogą one (choć nie
muszą) być uruchomione i działać na oddzielnych maszynach. Moduły te to:

-   Aplikacja Kliencka

-   System Hotelowy

-   Moduł Serwerowy

## Aplikacja Kliencka

Ten moduł wyposażony jest w interfejs graficzny pozwalający na
wyświetlanie ofert wraz ze zdjęciami i list dostępnych ofert do wyboru
oraz w wyszukiwarkę. Pozwala również klientowi zarezerwować pokój(przez
wybór oferty i kliknięcie \"rezerwuj\"), anulować rezerwacje, a także
zostawiać opinię dotyczącą odbytego pobytu. Moduł ten porozumiewa się
tylko z modułem serwerowym. W systemie jest dowolna liczba instancji
modułów Aplikacji Klienckiej.

## Moduł Hotelowy

Moduł zwiera interfejs graficzny pozwalający na wyświetlanie informacji
oraz modyfikowanie i dostęp do danych hotelowych związanych z dostępnymi
ofertami jak i rezerwacjami, pokojami hotelowymi czy użytkownikami
systemu hotelowego (obsługa hotelowa). Moduł hotelowy zawiera system
uprawnień użytkowników jak i konto administracyjne służące do
zarządzania systemem hotelowym. Moduł ten komunikuje się tylko z
serwerem. W systemie może być dowolna liczba instancji modułów
hotelowych.

## Moduł Serwerowy

System ten posiada interfejs konsolowy. Pośredniczy on w komunikacji
pomiędzy hotelami i klientami, oraz przechowuje dane o ofertach
udostępnianych użytkownikom oraz o ocenach jakie użytkownicy wystawili
poszczególnym hotelom. Serwer powinien mieć tyle informacji, aby bez
komunikacji z poszczególnymi hotelami móc przedstawić klientowi oferty
spełniające jego zapytanie. Wymagana jest zatem synchronizacja
dostępności ofert serwera z systemami hotelowymi wystawiającymi te
oferty w celu stwierdzenia dostępności oferty w imieniu danego hotelu. W
systemie jest tylko jeden moduł serwerowy.\

# Diagramy PU i User stories {#userStories}

## User stories

::: {.center}
:::

Powyższa tabela przedstawia przewidywane przez nasz zespół historie
użytkowników naszego systemu. Na jej podstawie można w relatywnie prosty
sposób określić granice działania systemu jak i jego funkcjonalności.
Szczególnie użyteczne w tym celu są określone dla każdego user stories
flagi oznaczające priorytety wyróżnionych przez nas funkcjonalności.
Zastosowany model MoSCoW w sposób przejrzysty prezentuje, które user
stories uważamy za krytyczne dla prawidłowego funkcjonowania systemu
(**M**ust **H**ave/**S**hould **H**ave), a których implementację
rozważamy (**C**ould **H**ave) czy wykluczamy, bądź uznaliśmy za mało
istotną (**W**on't **H**ave).\
Tabela uwzględnia podział na zdefiniowane przez nas we wcześniejszych
sekcjach moduły. Klient definiuje jakie interakcje przewidujemy pomiędzy
aplikacją kliencką a wspomnianym klientem. Są to między innymi:

-   Wyszukiwanie hoteli po określonych kryteriach - proces znajdowania
    pobytu rozpoczyna się od wyszukania hotelu, który swoją ofertą
    zadowala potrzeby klienta. Użytecznym narzędziem jest zestaw filtrów
    zawężających listę hoteli

-   Wyszukiwanie pokoi po określonych kryteriach - udostępniamy
    funkcjonalność pozwalającą na filtrację pokoi w obrębie jednego
    hotelu, aby znaleźć pokój zadowalający nasze wymagania

-   Rezerwacja pokoju - po znalezieniu hotelu jak i oferty spełniającej
    oczekiwania klienta umożliwiamy bezpośrednio z poziomu aplikacji
    dokonanie rezerwacji

-   Dokonanie płatności za zarezerwowany pokój - bezpośrednio po
    dokonaniu rezerwacji klient powinien opłacić swój pobyt. Umożliwiamy
    realizację tego zadania bezpośrednio z poziomu aplikacji

-   Anulowanie rezerwacji - w przypadku jakichkolwiek trudności po
    stronie klienta uniemożliwiających zrealizowanie pobytu udostępniamy
    funkcjonalność anulowania rezerwacji bez potrzeby bezpośredniego
    kontaktu z obsługą hotelu

-   Kontakt z hotelem - w przypadku jakichkolwiek wątpliwości
    dotyczących oferty umożliwiamy bezpośredni kontakt z poziomu
    aplikacji z obsługą hotelu

-   Pozostawienie opinii wraz z oceną - po zakończonym pobycie
    umożliwiamy wystawienie ofercie opinii jak i punktowej oceny. Ocena
    może stanowić kryterium wyszukiwania, więc pomaga innym klientom w
    znalezieniu wymarzonej oferty

Kolejne wiersze tabeli poświęcone są obsłudze hotelowej i jej
interakcjom z systemem hotelowym. Przewidujemy w ramach hotelowego
personelu różny dostęp do systemu. Przejdźmy do omówienia użytkownika
najbardziej uprzywilejowanego w systemie jakim jest manager. W
realizowaniu powierzonych zadań wspierać go mają następujące
funkcjonalności:

-   umieszczanie nowych ofert - manager w każdym momencie działania
    systemu ma możliwość tworzenia i umieszczania na serwerze nowych
    ofert tak, aby dotrzeć do jak najszerszego grona klientów

-   edytowanie ofert - udostępniamy możliwość edycji oferty bez potrzeby
    usuwania ich z serwera i ponownego wgrywania. W ten sposób
    odzwierciedlanie bieżącego stanu oferty jak i wolnych terminów nie
    wymaga dodatkowych nakładów niż to konieczne

-   usunięcie ofert - w przypadku jakichkolwiek problemów z ofertą, bądź
    wygaśnięciu terminu oferty manager ma możliwość usunięcia oferty z
    serwera

-   wprowadzanie promocji do ofert - funkcjonalność szczególnie
    przydatna w okresie świątecznym, bądź też przy tworzeniu ofert
    grupowych. Manager w sposób dynamiczny może zmieniać cenę ofert
    wprowadzając szeroko pojęte promocje czy też rabaty

-   dodawanie zdjęć - system wspiera także dodawanie do oferty zdjęć, co
    czyni ją dużo bardziej atrakcyjną dla klienta jak i odzwierciedla
    realny wygląd oferty

-   usunięcie rezerwacji z oferty - w przypadku gdy manager zostanie
    poinformowany o niemożliwości zrealizowania rezerwacji przez klienta
    ma możliwość anulowania rezerwacji nie zmieniając przy tym stanu
    oferty

-   kontakt z klientem - to funkcjonalność przewidziana do
    bezpośredniego kontaktu pomiędzy obsługą hotelu, a klientem. Możemy
    w ten sposób aktywnie informować klienta o statusie jego rezerwacji
    czy atrakcjach przewidzianych na pobyt

-   zakładanie kont pracownikom - w ramach działaniu systemu
    przewidujemy również narzędzia wspomagające w pracy pozostałą część
    personelu. Manager ma możliwość zarządzania dostępem do systemu dla
    każdego z pracowników osobna lub skorzystać z przygotowanych
    uprzednio profili odzwierciedlających role

-   grupowanie pokoi - to kolejna funkcjonalność wspierająca interakcję
    z pozostałą częścią personelu. Udostępniamy narzędzie pozwalające na
    przypisywanie pokoi do konkretnych grup takich jak na przykład: do
    posprzątania, gotowe, goście wymagający specjalnego traktowania co
    znacząco zoptymalizuje wykonywanie przez personel zadań

Za esencjonalną funkcjonalność naszego systemu nie uważamy wyłącznie
wspieranie działań managera, ale także ułatwienie organizacji pracy
całego personelu. Przejdziemy teraz do zwięzłego omówienia narzędzi dla
obsługi hotelowej:

-   rezerwacja pokoi

-   anulowanie rezerwacji na życzenie klienta

-   monitorowanie stanu pokoi

-   informacja o liczbie gości jak i ich wieku

-   informacja o rodzaju wyżywienia czy innych pakietach

Aby opisana wyżej komunikacja i interakcja z systemem odbywała się w
sposób ciągły konieczne są następujące funkcjonalności modułu
hotelowego:

-   wymiana informacji z serwerem - absolutnie newralgiczna
    funkcjonalność zapewniająca płynną i nieprzerwaną wymianę informacji
    pomiędzy systemem hotelowym, a serwerem. Wysoka wydajność zostanie
    osiągnięta dzięki zastosowanym rozwiązaniom opartych na lokalnej i
    serwerowej bazie danych

-   pozostałe funkcjonalności wynikające z realizacji opisanych wyżej
    interakcji z Hotelem, a docelowo serwerem. Są to między innymi:
    rezerwacja pokoi, anulowanie rezerwacji, edycja kalendarza
    dostępności ofert czy kontakt z klientem

Przybliżone zostały interakcje pomiędzy klientem, a aplikacją kliencką a
także pomiędzy system hotelowym, a obsługą hotelu. Przejdziemy teraz do
omówienia kulis działania wspomnianych funkcjonalności, a mianowicie do
serwera stanowiącego filar całego systemu. Do jego zadań należy przede
wszystkim zapewnienie niezachwianej komunikacji pozostałych modułów, a
część z koniecznych w tym celu funkcjonalności została opisana poniżej:

-   wysyłanie zapytań o wolne pokoje - serwer w części przypadków jest w
    stanie samodzielnie odpowiedzieć na zapytania klientów, jednakże w
    celu weryfikacji przesyła również żądanie do konkretnego systemu
    hotelowego

-   znajomość lokalizacji i udogodnień hoteli - przed przekierowaniem
    klienta do wybranego modułu hotelowego następuje wstępna filtracja
    obiektów poprzez zdefiniowane przez klienta kryteria. Takie
    rozwiązanie pozwala na wyświetlanie wyłącznie hoteli spełniających
    oczekiwania klienta

-   znajomość opinii i średnich cen hoteli - to kolejne parametry mające
    na celu precyzyjniejsze wskazanie ofert przeznaczonych dla
    konkretnego klienta

-   znajomość rodzai dostępnych pokoi każdego hotelu - informacja ta
    pozwala już na wstępie odrzucić zapytanie czy rezerwację złożoną
    przez klienta w przypadku gdy oczekiwane usługi nie mogą być przez
    dany hotel świadczone

## Diagramy PU

### Aplikacja kliencka

![image](checkpoint1/Use cases - klient.png){width="\\linewidth"} W
obrębie aplikacji klienckiej dowolny klient będzie mógł wyszukać
interesujący go hotel lub hotele wysyłając zapytanie z podanymi przez
niego kryteriami do serwera, zarezerwować interesujący go pokój
(równocześnie opłacając daną rezerwację) lub zarządzać swoimi
rezerwacjami - anulować je lub pozyskać dodatkowe informacje poprzez
kontakt z hotelem, lub też zostawić opinię o danym hotelu po zakończonym
pobycie.

### Hotel

![image](checkpoint1/Use cases - hotel.png){width="\\linewidth"} Moduł
hotelowy wspomaga operowanie danym hotelem jego obsłudze poprzez system
hotelowy. W obrębie systemu obsługa hotelu ma dostęp do listy obecnie
wynajętych pokoi i łącznej liczby osób w celu np. wyznaczenia
odpowiedniej pory posprzątania danych pokoi. Recepcjoniści, oprócz
posiadania tych samych uprawnień co obsługa, mogą dokonywać rezerwacji
na rzecz klienta lub je anulować, jeśli zaistnieje taka potrzeba. Z
kolei dla managera danego hotelu, który ma najwyższe uprawnienia,
zarezerwowane są możliwości dodania ofert do hotelu, (a zarazem ich
edycji/aktualizacji np. umieszczenie zdjęć, opisu czy też ustalenie
promocji), jak również bezpośrednia komunikacja z klientem.

### Serwer

![image](checkpoint1/Use cases - serwer.png){width="\\linewidth"} W
module serwera przewidywane są funkcjonalności synchronizacji
(udostępnienia) danych o lokalizacji i dostępności danego hotelu między
serwerem a systemem hotelowym, przekazania informacji o rezerwacji
jakiegoś pokoju (w tym również danych o kliencie, który dany pokój
postanowił zarezerwować) lub odpytania o dostępność pokoi w ramach ofert
danego hotelu. Ostatnie zapytanie może zostać rozszerzone o atrybuty
wszystkich dostępnych ofert danego hotelu. Są to funkcje niezbędne do
prawidłowej wymiany informacji między systemem hotelowym a serwerem, a
zarazem ich efektywnej pracy.

## Przykładowe przypadki użycia (use cases)

### Wyszukanie pokoju przez klienta

W przypadku wyszukiwania przez klienta interesujących go ofert musi on
najpierw wybrać hotele oraz parametry pokoi, następnie wysłać zapytanie
z danymi parametrami do serwera. Ten następnie na podstawie własnych,
lokalnych danych dotyczących wszystkich modułów hotelowych przeszukuje
oferty i wysyła listę, która następnie zostaje pokazana klientowi. Jeśli
w dowolnym momencie interakcji wystąpią problemy z połączeniem,
użytkownikowi zostanie wyświetlona odpowiednia informacja o braku
połączenia. W przypadku gdy któraś instancja systemu hotelowego nie jest
osiągalna przez serwer agregujący, klientowi zostanie wyświetlona
odpowiednia informacja.

### Dodanie nowej oferty pokoju do systemu przez managera hotelu

W sytuacji, kiedy wymagane jest dodanie nowej oferty pokoju, manager
hotelu wypełnia odpowiedni formularz z parametrami pokoju. Jeśli któreś
pole nie zostało wypełnione poprawnie (np. pola wymagane są puste lub
wprowadzone dane są nieprawidłowe), aplikacja zwraca błąd i nie dodaje
oferty, w przeciwnym wypadku system hotelowy dodaje ofertę do swojej
bazy danych zgodnie z wypełnionym formularzem. W późniejszym czasie baza
danych hotelu zostanie zsynchronizowana z serwerem i dane oferty pojawią
się w bazie danych serwera agregującego.

### Rezerwacja pokoju przez klienta

Kiedy klient wybrał interesującą go ofertę rezerwacji, wysyła informację
o próbie rezerwacji pokoju. Jeżeli serwer stwierdzi, że oferta
rezerwacji pokoju nie jest dostępna, to zwracana jest informacja z
błędem i nie jest wykonywana rezerwacja. W przeciwnym przypadku serwer
przekierowuje prośbę utworzenia rezerwacji do hotelu, gdzie dostępność
rezerwacji jest ponownie sprawdzana. Jeśli oferta jest dostępna w
wybranym przedziale czasowym tworzona jest lokalna rezerwacja po stronie
hotelu i odsyłany jest identyfikator płatności za tą ofertę. Klient musi
opłacić swoją rezerwację, po czym odsyła do serwera informację o
zakończonym procesie płatności. Po potwierdzeniu ukończonej płatności
przez hotel, wpis o rezerwacji jest tworzony na serwerze i zwracana jest
klientowi informacja o zakończeniu procesu tworzenia rezerwacji. W
przypadku niepowodzenia płatności (np. pieniądze nie zostały
zaksięgowane na koncie hotelu) serwer informuje klienta o konieczności
kontaktu z hotelem w celu wyjaśnienia tej sytuacji. Jeśli natomiast
hotel stwierdzi brak dostępności oferty wysyłana jest informacja o
błędzie serwerowi, co świadczy o desynchronizacji danych między hotelem
i serwerem. Odsyłany jest wówczas klientowi błąd mówiący o braku
dostępności oferty w wybranym okresie czasowym.

# Diagramy Klas

We wszystkich trzech modułach Systemu zdecydowaliśmy się zastosować
rozdział części odpowiadającej za połączenie między modułami (klasy
nazwane `*Connection`) od części odpowiadającej za wysokopoziomowe akcje
(czyli metody zawarte w klasach `*Manager`). Dzięki takiemu krokowi
rozdzielamy obsługę błędów związanych z połączeniem od walidacji
formularzy i błędów związanych z poprawnością wprowadzonych danych.
Klasa `*Connection` pracuje na danych, które są poprawne. W
odpowiedzialności klasy `*Manager` znajduje się dbanie o poprawną
komunikację -- w związku z tym również wywoływanie odpowiednich metod
klasy `*Connection`.\
Ponadto w modułach serwerowym oraz hotelowym -- które dysponują swoimi
lokalnymi bazami danych -- wyróżniono klasę `DataManager`, będącą
**wyłącznym** pośrednikiem w pobieraniu informacji z tej bazy. Z tego
względu `DataManager` został oznaczony na diagramie jako klasa silnie
agregująca obiekty tych typów, które może zwrócić -- niezależnie od
faktycznej technologii, która została użyta do implementacji tej części
(np. SQL czy ORM).

## Aplikacja Kliencka

::: {.center}
![image](checkpoint1/IO Klasy-Client_App_Module.png)
:::

### ClientManager

Klasa ta zawiera implementacje metod, które są wywoływane z poziomu UI
użytkownika. Zawiera odpowiednie metody służące do autentykacji oraz
dane o użytkowniku.

-   Login\
    Funkcja wywoływana w czasie logowania się użytkownika do systemu.
    Przekazywane argumenty to w kolejności login i hasło.\
    Zwraca boola czy operacja się powiodła i w przypadku powodzenia
    pobiera informacje o zalogowanym kliencie.

-   GetOffers\
    Funkcja pobiera informacje o ofertach w systemie zgodnie z
    kryteriami filtracji użytkownika w postaci obiektu
    OfferSearchOptions przekazanego do funkcji.

-   GetHotels\
    Funkcja pobiera informacje o hotelach w systemie zgodnie z
    kryteriami filtracji użytkownika w postaci obiektu
    HotelSearchOptions przekazanego do funkcji.

-   MakeReservation\
    Funkcja próbuje dodać do systemu rezerwację o parametrach do niej
    przekazanych.\
    Zwraca informację czy operacja się powiodła.

-   CancelReservation\
    Funkcja usuwa z systemu rezerwację przekazaną jako parametr.\
    Zwraca informację czy operacja się powiodła.

-   RemoveRoomReview\
    Funkcja usuwa z systemu opinię przekazaną jako parametr.\
    Zwraca informację czy operacja się powiodła.

-   UpdateReview\
    Funkcja aktualizuje już istniejącą Review w systemie (zmiana oceny
    lub opisu).\
    Zwraca informację czy operacja się powiodła.

-   SendReview\
    Funkcja dodaje do systemu nową opinię dotyczącą Oferty której Id
    zostało przekazane jako parametr. Zwraca informację czy operacja się
    powiodła.

-   GetMyReviews\
    Funkcja zwraca listę opinii utworzonych przez zalogowanego klienta.

-   GetMyReservations\
    Funkcja zwraca listę rezerwacji złożonych przez zalogowanego
    klienta.

### HotelInfo

Klasa przetrzymuje informacje o hotelu.

### HotelSearchOptions

Klasa trzyma w sobie wymagania jakie muszą spełniać hotele wyszukiwane
przez klienta.\
Używana tylko przy wyszukiwaniu.

### Offer

Klasa przetrzymująca wszystkie informacje o ofercie.

### OfferSearchOptions

Klasa trzyma w sobie wymagania, które musi spełniać oferta wyszukiwana
przez klienta.\
Używana tylko przy wyszukiwaniu.

### ReservationInfo

Klasa trzyma informacje o rezerwacji bez identyfikatora rezerwacji i
ewentualnej opinii.

### ClientReservaton

Klasa trzyma wszystkie informacje o rezerwacji klienta wraz z opcjonalną
opinią.

### ReviewInfo

Klasa zawierająca informacje o recenzji bez jej identyfikatora.

### ClientReview

Klasa przetrzymująca wszystkie informacje o opinii.

## Moduł hotelowy

::: {.center}
![image](checkpoint1/IO Klasy-Hotel_Module.png)
:::

### OfferInfo

Klasa przechowuje informacje opisujące daną ofertę bez informacji o jej
identyfikatorze oraz stanu dotyczącego aktywności.

### Offer

Klasa przechowuje informacje dotyczące różnych rodzajów ofert, jakie
proponuje właściciel hotelu swoim potencjalnym klientom. Ponadto
przechowywana jest informacja o tym czy oferta jest aktywna oraz jej
identyfikator.

### Room

`Pokój` jest klasą, która reprezentuje fizyczny pokój obecny w budynku
hotelowym. Jego identyfikacja leży całkowicie w gestii zarządcy
hotelowego. Ma odwołanie do `Offer`, w ramach której jest przedstawiany
na stronie.

### Client

Reprezentuje klienta, którego konto istnieje w systemie rezerwacji
pokoi.

### Reservation

Przechowuje informacje dotyczące rezerwacji klienckiej razem z ID pokoju
oraz ID oferty. Ponadto przechowywany jest identyfikator płatności
rezerwacji w celu umożliwienia anulowania rezerwacji i zwrotu pieniędzy.

### ReservationInfo

Przechowuje szczegóły dotyczące pojedynczej, potwierdzonej rezerwacji,
czyli daty od kiedy do kiedy ma ona trwać, oraz liczbę gości w jej
ramach.

### HotelEmployee

Reprezentuje członka personelu hotelowego, który posiada dostęp do
części danych w systemie. Zakres dostępu oraz ograniczenia zdefiniowane
są przez pole **Permissions**, które zawiera zbiór zdefiniowanych przez
managera stałych typu enum.

### HotelEmployeeSecrets

Reprezentuje dane stanowiące podstawę do autentykacji pracownika
hotelowego (dane z tabeli zawierającej obiekty tego typu są używane
bezpośrednio przy logowaniu się przez personel do systemu). Ze względów
bezpieczeństwa informacje o loginach i hasłach pracowników zawarte są w
osobnej tabeli do której dostęp jest stosownie chroniony.

### Payments

Reprezentuje dane dotyczące rezerwacji, które nie zostały jeszcze
opłacone. W momencie poprawnego opłacenia rezerwacji usuwane są rekordy
z tej tabeli.

### DataManager

Klasa pośrednicząca w wydobywaniu informacji z bazy danych. W tym celu
używa jej HotelManager. Posiada jedną metodę --
**ParseQueryString(string)**, która przyjmuje kwerendę do sparsowania w
języku bazy, np SQL. Ze względu na to, że pobranie danych możliwe jest
wyłącznie za pośrednictwem tej metody, **DataManager** agreguje obiekty
wszystkich opisanych wyżej klas.

### HotelInfo {#HotelInfo}

Przechowuje informacje o danym hotelu, takie jak lokalizacja, nazwa czy
jego opis.

### ServerConnectionIncoming

Klasa stanowiąca pomost w komunikacji między hotelem oraz serwerem dla
wiadomości inicjowanych przez serwer. Głównym zadaniem tej klasy jest
więc interpretacja żądań formułowanych przez serwer, ich przetwarzanie i
zwrócenie stosownych informacji za pomocą metody **SendMessage** czy też
dalsza komunikacja z modułem serwera. Metody:

-   Login\
    Metoda wywoływana w czasie nawiązywania połączenia ze stosownym
    gniazdem sieciowym po stronie serwera. Do autentykacji używany jest
    nadany hotelowi authenticationKey. Zwraca boola czy operacja się
    powiodła.

-   SendMessage\
    Podstawowy sposób wysyłania wiadomości do serwera. Wiadomość jest
    przekazywana do metody jako parametr typu string. Metoda ta ma na
    celu zagwarantowanie poprawnego przesyłu danych (np. ponawianie prób
    wysyłania w przypadku chwilowego przerwania połączenia). Zwracany
    jest typ bool, który mówi o udanym transferze danych przez gniazdo
    sieciowe.

-   ParseServerMessage\
    W tej metodzie analizowana jest wiadomość wysłana przez serwer.
    Poprzez interpretację otrzymanego kodu operacyjnego następuję
    rozpoznanie rodzaju żądania i jego stosowna obsługa w oparciu o
    przesłane parametry.

### ServerConnectionOutgoing

Klasa stanowiąca pomost w komunikacji między hotelem oraz serwerem dla
wiadomości inicjowanych przez hotel. Metody wewnątrz tej klasy są
kluczowe dla poprawnej synchronizacji danych pomiędzy modułami
hotel-serwer. Ponadto występują metody implementujące procesy biznesowe
i związane z nimi procesy komunikacji. W prywatnym polu ProcessList
przechowywane są aktualnie przetwarzane procesy biznesowe wraz z kodem
operacyjnym ostatniej wysłanej wiadomości i jej treścią (informacje te
są potrzebne do rozróżnienia tych samych procesów biznesowych oraz
aktualnego kontekstu danego procesu biznesowego i oczekiwanych kodów
operacyjnych w wiadomościach zwrotnych, a także dalszej obsługi
wychodzącego żądania po otrzymaniu pozytywnej odpowiedzi od serwera).
Każda metoda związana z procesem biznesowym wywołuje odpowiednie metody
klasy HotelManager i w zależności od sukcesu lub rodzaju błędu
otrzymanego od HotelManager (np. w postaci wyjątku określonego typu)
tworzy żądania i odpowiedzi o odpowiednich kodach operacyjnych
jednocześnie implementując cały ciąg wiadomości (i związanych z nimi
kodami operacyjnymi) oraz obsługę błędów związaną z danym procesem
biznesowym. Metody:

-   Login\
    Metoda wywoływana w czasie nawiązywania połączenia ze stosownym
    gniazdem sieciowym po stronie serwera. Do autentykacji używany jest
    nadany hotelowi authenticationKey. Zwraca boola czy operacja się
    powiodła.

-   ParseServerMessage\
    W tej metodzie analizowana jest odpowiedź serwera związana z danym
    ID kontekstu procesu. ID kontekstu procesu jest szukane w prywatnym
    polu ProcessList w celu zidentyfikowania ostatniej wysłanej
    wiadomości i odtworzenia kontekstu procesu biznesowego. W zależności
    od otrzymanej odpowiedzi proces może się zakończyć lub mogą zostać
    ponownie wywołane odpowiednie metody z klasy HotelManager, utworzona
    nowa wiadomość (żądanie) i kontynuacja procesu biznesowego.

-   SendMessage\
    Podstawowy sposób wysyłania wiadomości do serwera. Wiadomość jest
    przekazywana do metody jako parametr typu string. Metoda ta ma na
    celu zagwarantowanie poprawnego przesyłu danych (np. ponawianie prób
    wysyłania w przypadku chwilowego przerwania połączenia). Zwracany
    jest typ bool, który mówi o udanym transferze danych przez gniazdo
    sieciowe.

-   AddOffer\
    Metoda dodaje do serwerowej i hotelowej bazy danych nową ofertę
    stworzoną przez managera konta hotelowego (w przypadku sukcesu).\
    Zwraca wartość bool określającą, czy operacja się powiodła oraz ID,
    jakie przyjęła oferta po stronie serwera.

-   RemoveOffer\
    Metoda usuwa z serwerowej oraz hotelowej bazy danych ofertę
    przekazaną jako argument (w przypadku sukcesu).\
    Zwraca wartość bool określającą, czy operacja się powiodła.

-   ActivateOffer, DeactivateOffer\
    Metoda aktualizuje dostępność oferty przekazanej jako argument
    metody. Administrator hotelu może ofertę dowolnie zdezaktualizować
    lub zaktualizować ponownie, manipulując w ten sposób wachlarzem
    propozycji dla swoich potencjalnych klientów (więcej nt. stanów
    klasy Offer patrz: [5.2](#offerStateDiagram){reference-type="ref"
    reference="offerStateDiagram"}).\
    Zwraca wartość bool określającą, czy operacja się powiodła.

### HotelManager

Centralna klasa modułu. Przechowuje wysokopoziomowe metody niezbędne do
interakcji z bazą danych i implementacji wszystkich procesów
biznesowych. Ponadto przechowywane są informacje o hotelu oraz
referencje do klas ServerConnectionIncoming oraz
ServerConnectionOutgoing. Metody:

-   AddOffer\
    Metoda dodaje do lokalnej bazy danych nową ofertę stworzoną przez
    managera konta hotelowego.\
    Zwraca wartość bool określającą, czy operacja się powiodła.

-   RemoveOffer\
    Metoda usuwa z lokalnej bazy danych ofertę o ID przekazanym jako
    argument.\
    Zwraca wartość bool określającą, czy operacja się powiodła.

-   ActivateOffer, DeactivateOffer\
    Metody aktualizują dostępność oferty. Administrator hotelu może
    ofertę dowolnie zdezaktualizować lub zaaktualizować ponownie,
    manipulując w ten sposób wachlarzem propozycji dla swoich
    potencjalnych klientów (więcej nt. stanów klasy Offer patrz:
    [5.2](#offerStateDiagram){reference-type="ref"
    reference="offerStateDiagram"}).\
    Zwracają wartość bool określającą, czy operacja się powiodła.

-   CheckReservationAvailability\
    Metoda zwraca wartość bool opisującą dostępność oferty o ID w
    określonym czasie zawartym w obiekcie ReservationInfo przekazanym
    jako argument.

-   RemoveRoom\
    Usuwa pokój o zadanym ID.\
    Zwraca wartość bool określającą, czy operacja się powiodła.

-   CreateReservation\
    Odpowiedzialnością metody jest stworzenie rezerwacji. Zwraca wartość
    bool określającą, czy operacja się powiodła.

-   CreateLocalReservation\
    Odpowiedzialnością metody jest stworzenie rezerwacji anonimowej
    przez obsługę hotelową. Metoda sprawdza uprawnienia użytkownika czy
    może on tworzyć nowe rezerwacje. Zwraca wartość bool określającą,
    czy operacja się powiodła oraz stworzoną rezerwację.

-   RemoveReservation\
    Odpowiedzialnością metody jest usunięcie rezerwacji. Pierwsza,
    wersja metody obsługuje zapytania otrzymane od serwera. Druga wersja
    jest dostępna dla członków personelu, sprawdza uprawnienia do
    wykonania tej akcji.\
    Zwraca wartość bool określającą, czy operacja się powiodła.

-   GetEmployee\
    Zwraca członka personelu na podstawie podanych danych logowania.
    Dane logowania są porównywane z zawartością tabeli
    HotelEmployeeSecrets.\
    W razie nieudanego logowania zwraca null.

-   ValidateOffer Metoda, która ma na celu sprawdzenie czy utworzona,
    bądź zmodyfikowana oferta jest zgodna z wewnętrznymi regułami
    związanymi z walidacją danych.

-   GetUnavailabilityIntervals Metoda, która przyjmuje jako argument ID
    oferty oraz zwraca przedziały czasowe, w których oferta jest
    niedostępna.

## Moduł Serwerowy

::: {.center}
![image](checkpoint1/IO Klasy-Server_Module.png)
:::

### Client

Klasa trzyma informacje o kliencie. Przedstawia jeden rekord z bazy
danych.

### ClientConnection

Klasa ta to interfejs sieciowy pomiędzy aplikacją kliencką i serwerem.
Klasa w metodzie `ParseClientRESTRequest` implementuje procesy biznesowe
związane z REST API, z których korzystają klienci. W zależności od
żądania wywoływane są odpowiednie metody klasy ServerManager i zwracane
są klientowi dane lub błąd (co wynika np. ze zwrócenia błędu przez
odpowiednią metodę klasy ServerManager, bądź wyrzuceniem określonego
typu wyjątku). Klasa ta może również przekazać żądanie do konkretnego
hotelu wykorzystując metodę `GetHotelConnection` w ramach dalszej
realizacji określonego procesu biznesowego (np. tworzenie rezerwacji)
jednocześnie oczekując na odpowiedź od hotelu.

### DataManager {#serverModuleDataManager}

Klasa będąca interfejsem bazy danych. W serwerze jest tylko jedna
instancja tej klasy, która jest używana bezpośrednio przez klasę
ServerManager.

### HotelInfo {#hotelinfo-1}

Klasa trzymająca informacje o hotelach korzystających z serwisu.

### HotelSearchOptions

Klasa służy do trzymania kryteriów wyszukiwania po hotelach.

### OfferSearchOptions

Klasa służy do trzymania kryteriów wyszukiwania po ofertach.

### ReservationInfo

Klasa zawiera informacje o pojedynczej rezerwacji bez identyfikatora tej
rezerwacji.

### ClientReservation

Klasa dziedziczy po ReservationInfo i ponadto łączy rezerwacje z
klientem i opinią.

### OfferInfo

Klasa trzymająca informacje opisowe o ofercie bez informacji o
identyfikatorze oferty.

### Offer

Klasa trzymająca wszystkie informacje o ofercie oraz dodatkowo
informacje o przedziałach czasowych, w których oferta jest niedostępna.
Informacja ta jest otrzymywana na bieżąco od hotelu w ramach procesu
synchronizacji danych. ID oferty jest unikatowe w obrębie jednego hotelu
(nie globalnie).

### ReviewInfo

Zawiera informacje o pojedynczej opinii bez informacji o identyfikatorze
opinii bądź przynależności opinii do konkretnego użytkownika.

### ClientReview

Zawiera wszystkie informacje o pojedynczej opinii.

### Payments

Zawiera informacje o płatnościach związanych z rezerwacjami, które nie
zostały jeszcze opłacone. Klient może w dowolnym momencie pobrać
informacje o nieuiszczonych płatnościach. W momencie udanej opłaty za
rezerwacje usuwane są odpowiednie rekordy z tej tabeli.

### HotelConnectionIncoming

Klasa stanowiąca pomost w komunikacji między hotelem oraz serwerem dla
wiadomości inicjowanych przez hotel. Głównym zadaniem tej klasy jest
więc interpretacja żądań formułowanych przez hotel, ich przetwarzanie i
zwrócenie stosownych informacji za pomocą metody **SendMessage** czy też
dalsza komunikacja z modułem serwera.

-   SendMessage\
    Podstawowy sposób wysyłania wiadomości do hotelu. Wiadomość jest
    przekazywana do metody jako parametr typu string. Metoda ta ma na
    celu zagwarantowanie poprawnego przesyłu danych (np. ponawianie prób
    wysyłania w przypadku chwilowego przerwania połączenia). Zwracany
    jest typ bool, który mówi o udanym transferze danych przez gniazdo
    sieciowe.

-   ParseServerMessage\
    W tej metodzie analizowana jest wiadomość wysłana przez hotel.
    Poprzez interpretację otrzymanego kodu operacyjnego następuję
    rozpoznanie rodzaju żądania i jego stosowna obsługa w oparciu o
    przesłane parametry.

### HotelConnectionOutgoing

Klasa reprezentująca procesy biznesowe, dla których żądania wysyłane są
z serwera do hotelu. Każda instancja tej klasy zawiera informacje o
hotelu, w ramach którego utrzymywane jest połączenie sieciowe (pole
Hotel). Ponadto zawarte jest prywatne pole ProcessList, w którym zawarte
są informacje o wszystkich bieżąco realizowanych procesach biznesowych
wraz z ich ID kontekstu, kodem ostatnio wysłanej wiadomości oraz danymi
związanymi z ostatnio wysłaną wiadomością. Metody:

-   ParseHotelMessage\
    W tej metodzie analizowana jest odpowiedź hotelu związana z danym ID
    kontekstu procesu. ID procesu jest wiązane z ID procesu zapisanym w
    polu ProcessList. W zależności od otrzymanej odpowiedzi proces może
    się zakończyć lub mogą zostać ponownie wywołane odpowiednie metody z
    klasy ServerManager, utworzona nowa wiadomość (żądanie) i
    kontynuacja procesu biznesowego.

-   SendMessage\
    Podstawowy sposób wysyłania wiadomości do hotelu. Wiadomość jest
    przekazywana do metody jako parametr typu string. Metoda ta ma na
    celu zagwarantowanie poprawnego przesyłu danych (np. ponawianie prób
    wysyłania w przypadku chwilowego przerwania połączenia). Zwracany
    jest typ bool, który mówi o udanym transferze danych przez gniazdo
    sieciowe.

-   MakeReservation\
    Metoda, która tworzy proces związany z utworzeniem nowej rezerwacji.
    Wysyłane jest odpowiednie żądanie do hotelu oraz odkładane jest na
    listę ProcessList ID nowego procesu związanego z utworzeniem nowej
    rezerwacji. W przypadku sukcesu tworzony jest wpis w tabeli Payments
    o nowo utworzonej rezerwacji, która nie jest opłacona przez klienta.

-   CancelReservation\
    Metoda, która tworzy proces związany z anulowaniem nowej rezerwacji.
    Wysyłane jest odpowiednie żądanie do hotelu oraz odkładane jest na
    listę ProcessList ID nowego procesu związanego z utworzeniem nowej
    rezerwacji. W przypadku sukcesu usuwany jest lokalny wpis o
    rezerwacji klienta.

-   ConfirmPayment\
    Metoda, która tworzy proces związany z potwierdzeniem opłaty
    rezerwacji. Wysyłane jest odpowiednie żądanie do hotelu oraz
    odkładane jest na listę ProcessList ID nowego procesu związanego z
    utworzeniem nowej rezerwacji. W przypadku sukcesu usuwany jest wpis
    o nieuiszczonej opłacie za rezerwację z tabeli Payments oraz
    tworzony jest wpis o nowej rezerwacji w tabeli ClientReservations.

-   Synchronize\
    Metoda, która tworzy proces związany z synchronizacją danych
    dotyczących dostępności oferty. Wysyłane jest odpowiednie żądanie do
    hotelu oraz odkładane jest na listę ProcessList ID nowego procesu
    związanego z utworzeniem nowej rezerwacji. W przypadku sukcesu
    aktualizowane są dane o dostępności określonej oferty za pomocą
    metody UpdateOfferUnavailability klasy ServerManager.

### ServerManager

Klasa zawierająca wysokopoziomowe metody dostępu do bazy danych związane
z określonymi procesami biznesowymi. Agreguje w sobie i udostępnia
wszelkie aktywne połączenia z hotelami. Posiada również wskazanie na
**DataManagera** ([4.3.3](#serverModuleDataManager){reference-type="ref"
reference="serverModuleDataManager"}). W przypadku błędów wykonania
metod zwracane mogą być błędy lub wyrzucane wyjątki, które powinny być
łapane w celu określenia typu błędu. Metody:

-   CreateReview, UpdateReview, RemoveReview\
    Metody odpowiedzialne za manipulowanie opiniami znajdującymi się w
    bazie danych.\
    Zwracają wartość bool określającą, czy operacja się powiodła.

-   GetReviews\
    Zwraca wszystkie opinie przypisane do podanej w argumencie oferty.

-   AddOffer, EditOffer, DeleteOffer\
    Metody odpowiedzialne za manipulowanie ofertami znajdującymi się w
    bazie danych.\
    Zwracają wartość bool określającą, czy operacja się powiodła.

-   GetOffers\
    Zwraca wszystkie oferty spełniające podane w argumencie typu
    OfferSearchOptions kryteria w odniesieniu do hotelu określonego
    identyfikatorem przekazanym jako argument metody.

-   GetHotels\
    Zwraca wszystkie hotele spełniające podane w argumencie typu
    HotelSearchOptions kryteria.

-   GetAllClientReservations\
    Zwraca wszystkie rezerwacje klienta podanego w argumencie.

-   GetHotelConnection\
    Zwraca instancję klasy HotelConnectionOutgoing. Zwracana jest klasa
    reprezentująca połączenie z hotelem o identyfikatorze przekazanym
    jako parametr metody GetHotelConnection.

-   TryMakeReservation\
    Metoda próbuje stworzyć rezerwację w systemie.\
    Zwraca instancję klasy ClientReservation i string reprezentujący
    identyfikator płatności za nowo utworzoną rezerwację (w przypadku
    sukcesu).

-   CancelReservation\
    Metoda usuwa rezerwację z sytemu.\
    Zwraca wartość bool określającą, czy operacja się powiodła.

-   ConfirmPayment\
    Metoda mająca na celu potwierdzenie ukończenia procesu płatności
    przez klienta wywołując odpowiednią metodę klasy
    SeverConnectionOutgoing.

-   AddNewClient\
    Dodanie nowo zarejestrowanego użytkownika do systemu.

-   RemoveClient\
    Usunięcie z systemu użytkownika, który się wyrejestrował.

-   UpdateOfferUnavailability\
    Aktualizuje dane związane z przedziałami czasowymi niedostępności
    oferty w oparciu o nowo otrzymane dane z procesu synchronizacji.

# Diagramy stanu

W tej sekcji omówione zostały stany kluczowych obiektów systemu
rezerwacji pokoi hotelowych. Poniżej zamieszczone zostały diagramy UML
oraz ich szczegółowe opisy.

## Pokój hotelowy

![image](checkpoint1/RoomStateDiagram.png){width="\\linewidth"}\
\
Z każdą ofertą hotelową jest związany co najmniej jeden pokój, którego
opis jest zawarty w reprezentującej go ofercie. Po utworzeniu nowego
pokoju i dodaniu go do puli pokoi związanych z daną ofertą, pokój
otrzymuje swój własny unikatowy identyfikator i przechodzi w stan
\"wolny\" oczekując na rezerwację kliencką.\
W momencie rozpoczęcia rezerwacji pokój hotelowy przechodzi w stan
\"zajęty\" - oznacza to fizyczny pobyt klienta w pokoju. Pokoje w stanie
\"zajętym\" są wyszczególnione dla obsługi hotelowej - informacja ta
może być wykorzystywana przez obsługę hotelową w celu określenia
potrzeby realizacji usług takich jak regularne sprzątanie pokoju.\
W momencie zakończenia pobytu klienta w pokoju hotelowym i upływie jego
rezerwacji pokój jest ponownie uwzględniany przy żądaniach rezerwacji
obejmujących przedział czasowy zrealizowanej rezerwacji. Informacja o
zrealizowanej rezerwacji jest usuwana w systemie hotelowym a pokój jest
oznaczany jako \"wolny\" na okres zrealizowanej rezerwacji.\
Cykl życia obiektu pokoju hotelowego kończy się w momencie jego
usunięcia z puli pokoi związanych z daną ofertą pod warunkiem, że pokój
znajduje się w stanie \"wolny\" oraz nie są przewidziane żadne jego
rezerwacje.

## Oferta pokoju {#offerStateDiagram}

::: {.center}
![image](checkpoint1/OfferStateDiagram.png)
:::

Informacje o ofercie hotelowej jak i jej dostępność są przechowywane
zarówno na serwerze jak i systemie hotelowym. Po utworzeniu nowej oferty
w systemie hotelowym informacja o poprawnym wykonaniu tej akcji musi
zostać przekazana i potwierdzona przez serwer, więc początkowym stanem
nowej oferty jest \"oferta aktywna niezsynchronizowana\". Po poprawnej
synchronizacji oferta przechodzi w stan \"oferty aktywnej
zsynchronizowanej\".\
W przypadku edycji oferty (zmiany informacji takich jak opis pokoi, cena
itp.) oferta ponownie przechodzi w stan \"oferty aktywnej
niezsynchronizowanej\" oczekującej na wykonanie żądanych przez hotel
akcji na serwerze oraz potwierdzenie ich wykonania lub informacji o
błędzie.\
Oferta aktywna zsynchronizowana może zostać zdezaktywowana, dzięki czemu
opcja tworzenia nowych rezerwacji jest tymczasowo niedostępna dla
klientów. Oferta ta przechodzi w stan \"oferty nieaktywnej
niezsynchronizowanej\" oczekującej na synchronizację tej akcji między
modułem serwerowym i modułem hotelowym. Po pomyślnej dezaktywacji oferty
przechodzi ona w stan \"oferty nieaktywnej zsynchronizowanej\"
oczekującej na ponowną aktywację lub jej edycję przez hotel. W przypadku
edycji oferta przechodzi ponownie w stan \"oferty nieaktywnej
niezsynchronizowanej\" oczekując na zakończenie procesu synchronizacji
serwera i hotelu, natomiast z przypadku aktywacji oferty jej stan
zmienia się na \"ofertę aktywną niezsynchronizowaną\" oczekującą na
potwierdzenie reaktywacji oferty i synchronizacji obu modułów.\
Oferty znajdujące się w stanie \"aktywnym zsynchronizowanym\" mogą
zostać przesłane do aplikacji klienckiej w celu ich wyświetlenia. Razem
z wysyłanymi ofertami mogą również zostać opcjonalnie wysłane recenzje
tych ofert. Oferty te są wówczas w stanie \"wysłane do klienta\" a po
zakończeniu ich wyświetlania obiekty reprezentujące oferty są niszczone
lub zachowywane po stronie aplikacji klienckiej w celu cache'ingu.\
Oferty znajdujące się w stanie \"zsynchronizowanym\" mogą zostać
permanentnie usunięte na żądanie systemu hotelowego. Oferta wówczas
przechodzi w stan \"do usunięcia niezsynchronizowana\", podczas którego
serwer wykonuje odpowiednie akcje związane z usunięciem oferty
(oznaczenie oferty do usunięcia oraz zablokowanie możliwości tworzenia
rezerwacji w ramach tej oferty) oraz informuje hotel o powodzeniu lub
błędzie. W przypadku powodzenia oferta przechodzi w stan \"do usunięcia
zsynchronizowana\" po czym obiekty reprezentujące tą ofertę są niszczone
zarówno po stronie serwera jak i systemu hotelowego.

## Rezerwacja pokoju

::: {.center}
![image](checkpoint1/ReservationStateDiagram.png)
:::

Rezerwacja pokoju hotelowego tworzona jest przez klienta w oparciu o
informacje o hotelach i udostępnionych przez nie ofert jak i dostępnych
terminach rezerwacji w ramach tych ofert za pomocą metody
`MakeReservation`. Utworzony obiekt zawierający informacje o żądanej
przez klienta rezerwacji jest w stanie \"rezerwacji utworzonej przez
klienta\".\
Po poprawnym utworzeniu obiektu reprezentującego nową rezerwację
przesyłany jest on do serwera, przechodząc w stan \"rezerwacji
przekazanej do serwera\". Serwer w oparciu o własną lokalną bazę danych
zawierającą dane związane z dostępnością ofert w określonych
przedziałach czasowych (aktualizowaną na bieżąco w oparciu o komunikaty
hoteli informujące o zmianie dostępności ofert) odpowiednio odrzuca
żądanie rezerwacji przechodząc do stanu \"rezerwacji niemożliwej\"
informując klienta o niedostępności oferty i ostatecznie niszcząc obiekt
reprezentujący rezerwację po stronie serwera.\
W przypadku dostępności oferty w wybranym przez klienta terminie w
oparciu o informacje zawarte w lokalnej bazie danych serwera żądanie
rezerwacji przechodzi w stan \"rezerwacji przekazywanej do hotelu\" w
celu dodatkowej weryfikacji dostępności terminu rezerwacji po stronie
systemu hotelowego. W przypadku niedostępności żadnego pokoju na
określony w ramach nowej rezerwacji czas, rezerwacja przechodzi w stan
\"rezerwacji niemożliwej\" informując klienta o błędzie oraz serwer o
błędzie synchronizacji lokalnej bazy danych serwera przetrzymującej
informacje o dostępności oferty z bazą danych hotelu.\
W przypadku potwierdzenia dostępności pokoju na podany przez klienta
okres, oferta przechodzi do stanu \"rezerwacji potwierdzonej przez
hotel\", podczas którego generowana jest metoda opłacenia rezerwacji
przez klienta poprzez wywołanie metody `TryMakePayment`. Obiekt
rezerwacji przechodzi wówczas do stanu \"rezerwacji oczekującej na
płatność\", podczas której przeprowadzany jest proces płatności i
walidacja tego procesu. W przypadku niepowodzenia rezerwacja przechodzi
w stan \"rezerwacji nieopłaconej\". Klient może wówczas ponowić próbę
płatności za rezerwację co skutkuje ponownym przejściem rezerwacji w
stan \"rezerwacji oczekującej na płatność\".\
Możliwa jest również jawna rezygnacja klienta z rezerwacji w przypadku
gdy rezerwacja jest w stanie \"rezerwacji nieopłaconej\" w skutek czego
obiekt rezerwacji przechodzi do stanu \"rezerwacji anulowanej przez
klienta\" i przeprowadzane są odpowiednie akcje anulowania rezerwacji po
stronie klienta (`CancelReservation`). Po wywołaniu akcji
`CancelReservation` po stronie serwera obiekt rezerwacji jest niszczony.
W przypadku braku jawnej decyzji klienta o anulowaniu rezygnacji w
momencie gdy jest ona w automatycznie anulowana po upływie 1 dnia. Po
wywołaniu akcji `CancelReservation` obiekt rezerwacji jest niszczony.\
W przypadku braku decyzji o anulowaniu rezerwacji oraz zakończenia
procesu płatności z powodzeniem rezerwacja przechodzi w stan
\"rezerwacji opłaconej\". Po przetworzeniu zapłaty system hotelowy
tworzy obiekt rezerwacji w bazie danych za pomocą metody
`CreateReservation` na skutek czego rezerwacja przechodzi do stanu
\"rezerwacji niezrealizowanej\". W przypadku anulowania rezerwacji przez
klienta przyznany przez system hotelowy pokój jest zwalniany i ponownie
uwzględniany w kolejnych żądaniach rezerwacji. Anulowana rezerwacja
przechodzi wówczas do stanu \"rezerwacji anulowanej przez klienta\".\
Po upływie czasu rezerwacji obiekt przechodzi w stan \"rezerwacji
zrealizowanej\", natomiast zarezerwowany pokój jest ponownie
uwzględniany w nowych żądaniach rezerwacji. Przed upływem 30 dni klient
może wystawić opinię oferty, w ramach zrealizowanej rezerwacji pokoju
hotelowego. Zapisywana jest wówczas recenzja klienta, natomiast obiekt
rezerwacji przechodzi wówczas do stanu \"rezerwacji ocenionej\".

# Diagramy aktywności i sekwencji

Poniżej prezentujemy diagramy sekwencji przedstawiające przebieg
komunikacji pomiędzy modułami przy realizacji najistotniejszych naszym
zdaniem funkcjonalności systemu. Uwzględnione są również błędy czy
alternatywne przebiegi komunikacji. Na diagramach wyróżnieni są
następujący aktorzy: system hotelowy (moduł hotelowy), serwer (moduł
serwera), aplikacja kliencka/ClientApp (moduł klienta). W celu
zwiększenia czytelności diagramów ignorowane są błędy wynikające z
utraty czy braku połączenia.

## Oferta

Jedną z najważniejszych funkcjonalności naszej aplikacji jest możliwość
dynamicznego zarządzania ofertami przez managera hotelu (zarządcy modułu
hotelowego). Dla każdej oferty można, więc przeprowadzić następujące
operacje:

-   Dodawanie nowej oferty do systemu

-   Usuwanie już istniejącej oferty - jeśli oferta z jakichkolwiek
    powodów przestanie być aktualna (zakończenie okresu promocji,
    wyczerpanie liczby dostępnych ofert) udostępniamy możliwość trwałego
    usunięcia jej z systemu, tak aby przestała być widoczna dla klientów

-   Edytowanie oferty - w przypadku zaistnienia konieczności modyfikacji
    już istniejącej oferty manager ma możliwość zmiany dowolnego
    parametru oferty. W tym celu posługuje się dobrze mu znanym
    formularzem dodawania nowej oferty z naniesioną bieżącą postacią
    oferty.

Przebieg komunikacji dla każdej z tych operacji prezentujemy poniżej.

### Dodawanie oferty
MY PART

<img src="Aktywnosc/IO_Aktywności-Dodawanie oferty.png">
<img src="Sekwencje/Offer_Add.png">

Dodawanie oferty to operacja między Systemem Hotelowym, a Serwerem.
System Hotelowy wysyła po walidacji lokalnej żądanie do serwera wraz z
wszystkimi informacjami o ofercie. Serwer po otrzymaniu żądania waliduje
otrzymane dane po czym odsyła serwerowi czy operacja się powiodła, wtedy
odsyła potwierdzenie, czy nastąpił jakiś błąd, wtedy odsyła informacje o
błędzie. Jeśli system hotelowy nie może wysłać komunikatu ponawia próbę
po pewnym czasie. Powtarza to 5 razy co sekundę, po czym zarzuca
wykonywanie aktywności. W przypadku otrzymania potwierdzenia System
Hotelowy kończy operacje dodaniem do swojej bazy danych oferty. W
przypadku niepowodzenia oferta nie zostaje dodana do bazy danych i
proces się kończy.

### Usuwanie oferty

<img src="Aktywnosc/IO_Aktywności-Usuwanie oferty.png">
<img src="Sekwencje/Offer_Delete.png">

Usuwanie oferty odbywa się w następujący sposób System hotelowy wysyła
żądanie, a Serwer odsyła informacje o powodzeniu operacji lub o błędzie.
Jeśli system hotelowy nie może wysłać komunikatu ponawia próbę po pewnym
czasie. Powtarza to 5 razy co sekundę, po czym zarzuca wykonywanie
aktywności.

### Edytowanie oferty

<img src="Aktywnosc/IO_Aktywności-Usuwanie oferty.png">
<img src="Sekwencje/Offer_Edit.png">

Edycja oferty zaczyna się od wypełnienia formularza zmian przez
użytkownika Systemu Hotelowego wewnątrz niej. Zmiany są następnie
wstępnie walidowane. W przypadku nieudanej walidacji użytkownik jest z
powrotem odsyłany do formularza. W przypadku udanej walidacji System
Hotelowy wysyła żądanie wprowadzenia zmian do Serwera który jeszcze raz
waliduje otrzymane dane. Jeśli to się nie powiedzie odsyła błąd i proces
się kończy. W przypadku przejścia walidacji pomyślnie system uaktualnia
dane i odsyła informacje o powodzeniu operacji po czym System Hotelowy
uaktualnia swoje dane. Jeśli system hotelowy nie może wysłać komunikatu
ponawia próbę po pewnym czasie. Powtarza to 5 razy co sekundę, po czym
zarzuca wykonywanie aktywności.

### Wyszukiwanie oferty

<img src="Aktywnosc/IO_Aktywności-Wyszukiwanie hoteli i ofert.png">
<img src="Sekwencje/Offer_Search.png">

Wyszukiwanie oferty w systemie jest 2 etapowe. Pierwszy etap to
uzupełnienie danych wyszukiwania hotelu. Wypełniony formularz jest
przesyłany do serwera. Tam odbywa się jego walidacja. W przypadku
nieprawidłowości w formularzu do użytkownika zostaje przesłany błąd w
postaci kodu 400 wraz z informacją o błędnie wypełnionym polu. Jeśli
formularz został wypełniony poprawnie zwracana jest lista hoteli. W
przypadku gdy lista jest niepusta możemy wybrać jeden z hoteli, aby
poznać szczegółowe informacje na jego temat i mieć dostęp do
udostępnionych przez niego ofert.\
Drugi etap to wyszukiwanie ofert spośród tych udostępnionych przez
wybrany we wcześniejszych krokach hotel. Wyszukiwanie to przebiega
analogicznie do wyszukiwania hoteli. Użytkownik ma możliwość
ograniczenia listy ofert poprzez wypełnienie danych do wyszukiwania
ofert. Formularz jest przesyłany do serwera gdzie odbywa się jego
walidacja. W przypadku nieprawidłowości do użytkownika zostaje przesłany
błąd w postaci kodu 400 wraz z informacją o błędnie wypełnionym polu.
Jeśli formularz został wypełniony poprawnie zwracana jest lista ofert. W
przypadku gdy lista jest niepusta możemy wybrać jedną z ofert, aby
poznać jej szczegóły i przejść do dalszej interakcji.\
Jeśli system hotelowy nie może wysłać komunikatu ponawia próbę po pewnym
czasie. Powtarza to 5 razy co sekundę, po czym zarzuca wykonywanie
aktywności.\
Walidacja formularza i ewentualnie zwracane błędy w postaci kodów 400
nie zostały naniesione na diagram w celu zachowania jego czytelności.

## Rezerwacja

Podstawą systemu jest możliwość składania rezerwacji przez klientów. W
poniższej podsekcji zobaczymy jak wygląda z grubsza komunikacja między
modułami podczas tworzenia i anulowania rezerwacji przez klienta.

### Tworzenie rezerwacji {#reservation_diagram}

<img src="Aktywnosc/IO_Aktywności-Tworzenie rezerwacji.png">

<img src="Sekwencje/Reservation_Create.png">

Proces tworzenia rezerwacji zaczyna się po wybraniu przez użytkownika
aplikacji klienckiej hotelu oraz oferty, w ramach której ma być
utworzona nowa rezerwacja. Prośba o utworzenie nowej rezerwacji jest
przesyłana do serwera, który sprawdza czy oferta jest dostępna w oparciu
o własne dane uzyskane z hotelu. W przypadku braku wolnego terminu
zwracany jest błąd i kończony jest proces tworzenia rezerwacji. Jeśli
jednak z lokalnych danych wynika możliwość utworzenia rezerwacji,
przesyłane jest żądanie do hotelu o wstępne utworzenie rezerwacji. Hotel
w oparciu o swoje dane ponownie sprawdza dostępność oferty. W przypadku
gdy oferta nie jest dostępna w wyznaczonym czasie zawracany jest błąd,
który oznacza desynchronizację między danymi hotelu a serwera
opasującymi dostępność oferty. W efekcie serwer odsyła użytkownikowi
informację o nieudanej rezerwacji oraz natychmiastowo wykonuje proces
związany z synchronizacją danych. W przypadku gdy hotel będzie mógł
przyporządkować odpowiedni pokój na podany okres czasowy, tworzy on
lokalny wpis w bazie danych związany z tą rezerwacją oraz wpis dotyczący
płatności za tą rezerwację, która musi zostać opłacona przez klienta, a
następnie wysyłana do klienta przez serwer. Klient może anulować
rezerwację oraz wysłać do serwera odpowiedni komunikat, który następnie
jest przesyłany do hotelu. Hotel usuwa wówczas utworzony wpis rezerwacji
i zwraca odpowiednią informację serwerowi, która jest propagowana do
klienta. Jeśli klient opłaci w czasie daną rezerwację, wysyłany jest
komunikat do serwera o zakończeniu procesu płatności, który jest
następnie przekazywany do hotelu. Hotel ponownie sprawdza czy płatność
związana z konkretnym identyfikatorem płatności została zakończona
sukcesem. W przypadku niepowodzenia zwracana jest wiadomość do serwera
oznaczająca konieczność kontaktu z hotelem w celu potwierdzenia
płatności. Jest to sytuacja szczególna, która może być zależna od
zewnętrznego dostawcy usług płatności i błędami w tym systemie płatności
lub brakiem odpowiedniej synchronizacji (ten przypadek szczególny został
opisany przy ([8.5.3](#payments)). Jeśli płatność zostanie potwierdzona przez
hotel, jest odsyłana odpowiedź o sukcesie do serwera, w wyniku czego
tworzony jest wpis o rezerwacji klienckiej po stronie serwera i odsyłana
odpowiednia odpowiedź stanowiąca o sukcesie całego procesu rezerwacji.

### Anulowanie rezerwacji

<img src="Aktywnosc/IO_Aktywności-Usuwanie rezerwacji.png">
<img src="Sekwencje/Reservation_Cancel.png">

Po wybraniu swojej rezerwacji klient ma możliwość anulowania jej.
Aplikacja Kliencka wysyła wtedy żądanie usunięcia rezerwacji do Serwera
który przekazuje ją odpowiedniemu hotelowi. Hotel usuwa ze swojej bazy
danych rezerwacje i przesyła potwierdzenie do Serwera który również
usuwa rezerwację z swojej bazy danych i przesyła potwierdzenie do
klienta. W przypadku wystąpienia błędu na którymkolwiek z tych etapów
przesyłany jest błąd w stronę klienta i żadne zmiany w bazie danych nie
są robione. Jeśli któryś z modułów nie może wysłać komunikatu ponawia
próbę po pewnym czasie. Powtarza to 5 razy co sekundę, po czym zarzuca
wykonywanie aktywności.

### Tworzenie rezerwacji lokalnie

<img src="Aktywnosc/IO_Aktywności-Local reservation.png">

Istnieje również możliwość że klient przyjdzie do hotelu bez rezerwacji.
System hotelowy ma możliwość właśnie na taką ewentualność. System
hotelowy może zarezerwować pokój w imieniu klienta Po takiej rezerwacji
nie może zostać strwożona opinia gdyż serwer nie wie o istnieniu
takowej. Id klienta w bazie danych systemu hotelowego jest IdUser
hotelu. Hotel nie przetrzymuje wtedy żadnych informacji o kliencie, ale
za to klient nie musi trwożyć nowego konta. System Hotelowy próbuje
synchronizować się z serwerem i gdy nie ma żadnych przeciwności(czytaj
np. nie ma rezerwacji które były na serwerze na dany okres, a system
hotelowy o nich nie wiedział) dodaje rezerwację do lokalnej bazy danych.

## Opinia

W celu umożliwienia oceny danej oferty klienci(użytkownicy aplikacji
klienckiej) mają możliwość dodawania swoich opinii do ofert z których
ostatnio skorzystali. Poniżej przedstawiamy proces dodawania takiej
opini do systemu.

### Dodawanie opinii

<img src="Aktywnosc/IO_Aktywności-Dodawanie oceny.png">
<img src="Sekwencje/Opinion_Add.png">

Klient może dodać opinie do wybranej przez siebie rezerwacji którą już
odbył. W tym celu wypełnia formularz w Aplikacji Klienckiej, który jest
walidowany (w przypadku niepowodzenie odsyłany jest z powrotem do
formularza). Po przejściu przez walidację żądanie wysyłane jest do
Serwera który ponownie je waliduje i odsyła informacje czy operacja się
powiodła czy nie.

## Synchronizacja

<img src="Sekwencje/Synchronization_Server.png.png">
<img src="Sekwencje/Synchronization_Hotel.png"> 

W dowolnym momencie dane między hotelem a serwerem dotyczące dostępności
ofert mogą się zdesynchronizować. Może to wynikać np. z błędów
systemowych/sprzętowych po stronie serwera powodujących utratę danych,
przeorganizowanie przyporządkowań pokoi do rezerwacji czy utworzenia
nowej, anonimowej rezerwacji w hotelu niezależnej od systemu. W celu
zsynchronizowania danych, zarówno serwer jak i hotel mogą rozpocząć
procedurę synchronizacji danych w odniesieniu do konkretnej oferty
hotelowej. Przesyłane są wówczas dane zawierające przedziały czasowe
niedostępności ofert odpowiednio wyznaczone przez hotel.

# Hotel-Serwer

Do przesyłania wiadomości używane są trwałe połączenia TCP. Wynika to z
faktu, że komunikacja między modułami hotelowymi a modułem serwerowym
może się rozpocząć zarówno po stronie hotelu jak i serwera. Format
przesyłanych wiadomości jest następujący:

1.  Unsigned integer (enum) oznaczający kontekst wiadomości (kod
    operacyjny)

2.  Unsigned integer oznaczający id kontekstu (procesu) pozwalający
    odróżnić wiadomość od innych o takim samym kodzie operacyjnym (w
    przypadku gdy realizowanych jest kilka takich samych procesów
    biznesowych jak tworzenie rezerwacji, istnieje konieczność
    odróżnienia, który ciąg wiadomości odnosi się do którego procesu)

3.  Unsigned integer oznaczający liczbę bajtów przesyłanych danych

4.  Ciąg bajtów danych zawierający zserializowany obiekt JSON

Standard unsigned integerów to ISO/IEC 9899. Standardem zapisu JSONa
jest UTF-8. Pozwoli nam to zachować polskie znaki wewnątrz Opinii
klientów i wewnątrz Ofert wystawianych przez Hotele. W odniesieniu do
jednego połączenia TCP między hotelem oraz serwerem procesy biznesowe i
wiadomości są podzielone na 2 grupy - procesy, których żądania związane
z rozpoczęciem są zawsze żądaniami wychodzącymi oraz procesy, których
żądania związane z rozpoczęciem tego procesu są zawsze żądaniami
przychodzącymi. Każdy z tych procesów jest związany z oddzielnym
gniazdem sieciowym realizującym połączenie TCP (te same adresy IP, ale
różne porty). Każda z tych grup może być przetwarzana przez oddzielny
proces na danym module (zatem w ramach jednego połączenia wyróżniamy 4
procesy: 2 procesy po stronie serwera przetwarzające procesy związane z
żądaniami wychodzącymi (serwera) i przychodzącymi (hotelu) oraz 2
procesy po stronie hotelu przetwarzające procesy związane z żądaniami
przychodzącymi (serwera) i wychodzącymi (hotelu)). Procesy te w obrębie
jednego modułu są od siebie niezależne, wykorzystując własne gniazda
sieciowe oraz mogą przetwarzać wiele procesów jednocześnie
przyporządkowując odpowiednie id kontekstu procesu w wysyłanych
wiadomościach. W przypadku źle sformatowanej wiadomości nie robimy nic.

Poniżej zostały przedstawione wszystkie kody operacyjne wraz z ich
prawdziwymi wartościami.

            kod operacyjny            numer   wysyłany przez
  ---------------------------------- ------- ----------------
         HOTEL_LOGIN_REQUEST            1         Hotel
     HOTEL_LOGIN_RESPONSE_SUCCESS       2         Serwer
     HOTEL_LOGIN_RESPONSE_FAILURE       3         Serwer
          HOTEL_SYNC_REQUEST            4         Hotel
     HOTEL_SYNC_RESPONSE_SUCCESS        5         Serwer
         SERWER_SYNC_REQUEST            6         Serwer
     SERWER_SYNC_RESPONSE_SUCCESS       7         Hotel
          RESERVATION_CREATE            8         Serwer
           RESERVATION_GET              9         Serwer
       RESERVATION_GET_RESPONSE        10         Hotel
          OFFER_UNAVALAIBLE            11         Hotel
             PAYMENT_INFO              12         Hotel
           PAYMENT_SUCCESS             13         Serwer
   PAYMENT_SUCCESS_RESPONSE_SUCCESS    14         Hotel
   PAYMENT_SUCCESS_RESPONSE_FAILURE    15         Hotel
              ID_UNKNOWN               16         Hotel
          RESERVATION_DELETE           17         Serwer
      RESERVATION_DELETE_SUCCESS       18         Hotel
      RESERVATION_DELETE_FAILURE       19         Hotel
          OFFER_ADD_REQUEST            20         Hotel
          OFFER_ADD_SUCCESS            21         Serwer
          OFFER_ADD_FAILURE            22         Serwer
         OFFER_DELETE_REQUEST          23         Hotel
         OFFER_DELETE_SUCCESS          24         Serwer
         OFFER_DELETE_FAILURE          25         Serwer
          OFFER_EDIT_REQUEST           26         Hotel
          OFFER_EDIT_SUCCESS           27         Serwer
          OFFER_EDIT_FAILURE           28         Serwer

## Logowanie i uwierzytelnienie hotelu

### `HOTEL_LOGIN_REQUEST`

<img src="Hotel login + synchronizacja/hotel_login_request.png">
W celu możliwości zarządzania ofertami hotelowymi i tworzenia rezerwacji
system hotelowy musi nawiązać trwałe połączenie TCP z serwerem. Po
nawiązaniu połączenia na znany adres IP i numer portu serwera pierwszym
komunikatem wysyłanym przez hotel musi być wiadomość o kodzie
operacyjnym `HOTEL_LOGIN_REQUEST` oraz zserializowanym obiektem JSON
zgodnym ze schematem załączonym powyżej. W obiekcie tym znajduje się
właściwość "authKey" będąca kluczem autentykacyjnym hotelu. Unikatowe
klucze autentykacyjne są uprzednio nadawane przez administratora serwera
każdemu hotelowi korzystającemu z serwisu. W związku z faktem, że
połączenie składa się z 2 gniazd sieciowych przetwarzających różne grupy
procesów biznesowych powyższa wiadomość musi być przesłana do każdego
gniazda oddzielnie po nawiązaniu połączenia.

Do kodów "akceptowalnych" należą:

-   `HOTEL_LOGIN_RESPONSE_SUCCESS` - wiadomość mówiąca o udanym
    zalogowaniu. Utworzone zostaje stałe łącze TCP między hotelem a
    serwerem, przez które mogą być wysyłane nowe żądania. Wiadomość ta
    składa się jedynie z kodu operacyjnego - nie jest przesyłany
    zserializowany obiekt JSON.

-   `HOTEL_LOGIN_RESPONSE_FAILURE`

### `HOTEL_LOGIN_RESPONSE_FAILURE`

<img src="Hotel login + synchronizacja/hotel_login_response_failure.png">
Wiadomość wysyłana w przypadku niepowodzenia procesu logowania hotelu do
serwisu. Zawiera ona obiekt JSON z właściwością "authError" zawierającą
szczegółowy opis błędu autentykacji.

## Synchronizacja

Informacje o rezerwacji są przechowywane zarówno w bazie danych modułu
hotelowego jak i modułu serwera. Gdy klient wyszukuje oferty możliwych
do rezerwacji w określonym zakresie czasowym serwer znajduje
odpowiadające oferty w oparciu o własne dane dotyczące przedziałów
czasowych, w których dana oferta jest niedostępna. Dane te są na bieżąco
aktualizowane z hotelami podczas procesu tworzenia i anulowania
rezerwacji jak i w ramach możliwego mechanizmu periodycznej
synchronizacji obu modułów. W przypadku gdy zostanie utworzona nowa
rezerwacja anonimowa (na miejscu w hotelu) bądź system hotelowy
przeorganizuje dopasowanie pokoi do rezerwacji tworząc lub zamykając tym
samym "okna" dostępności danej oferty potrzebna jest synchronizacja
danych dostępności danej oferty z serwerem. Ponadto, system
synchronizacji jest częścią procesu anulowania jak i tworzenia
rezerwacji po stronie serwera. Serwer może w nieoczekiwanym przypadku
mieć nieaktualne dane związane z dostępnością oferty w momencie wysłania
wiadomości do hotelu o utworzeniu nowej rezerwacji. W efekcie zostanie
zwrócony błąd o niedostępności oferty, przez co serwer zmuszony będzie
do niezwłocznego wysłania żądania o synchronizacje danych.

### `HOTEL_SYNC_REQUEST`

<img src="Hotel login + synchronizacja/hotel_sync_request.png">
Wiadomość ta jest żądaniem synchronizacji danych dotyczących dostępności
oferty wysyłanym przez hotel. Żądanie to może się wiązać z anonimową
rezerwacją lub przeorganizowaniem przyporządkowania pokoi do rezerwacji
hotelu. Wysyłana jest wówczas do serwera powyższa wiadomość zawierająca
tablicę przedziałów czasowych, podczas których nie jest możliwe
wykonanie rezerwacji (we właściwości "unavailableTimeIntervals").

Do kodów "akceptowalnych" należą:

-   `HOTEL_SYNC_RESPONSE_SUCCESS` - wiadomość zwrotna oznaczająca udaną
    synchronizację danych po stronie serwera. Wiadomość ta nie zawiera
    zserializowanego obiektu JSON.

### `SERVER_SYNC_REQUEST`

<img src="Hotel login + synchronizacja/server_sync_request.png">
Wiadomość ta wiąże się z żądaniem serwera o synchronizację danych z
hotelem. Może ono wystąpić w mechanizmie periodycznej synchronizacji
danych w celu zachowania spójności danych w obu modułach, w przypadku
anulowania rezerwacji lub podczas procesu tworzenia nowej rezerwacji
klienckiej po otrzymaniu komunikatu `OFFER_UNAVAILABLE` oznaczającego
niedostępność oferty. Wiadomość ta ma na celu potwierdzenia lub
aktualizację dostępności danej oferty z hotelem.

Do kodów "akceptowalnych" należą:

-   `SERVER_SYNC_RESPONSE_SUCCESS` - wiadomość zwrotna zawierające dane
    potrzebne do synchronizacji dostępności oferty po stronie serwera.
    Zawartość wiadomości jest identyczna jak zawartość wiadomości o
    kodzie operacyjnym `HOTEL_SYNC_REQUEST`

## Zarządzanie ofertami

### `OFFER_ADD_REQUEST`

<img src="Oferta-Hotel-Serwer/Offer_Add_JSON1.png">

<img src="Oferta-Hotel-Serwer/Offer_Add_JSON2.png">
**Dodawanie nowej oferty**\
Proces dodawania nowej oferty zaczyna się od wypełnienia odpowiedniego
formularza. Następnie dokonywana jest wstępna walidacja formularza po
stronie systemu hotelowego. Jeżeli nie wykryto żadnych błędów, do
serwera zostaje przesłany kod operacyjny: `OFFER_ADD_REQUEST` wraz z
zserializowanym JSONem zawierającym kolejne pola formularza i informację
o ID hotelu od którego pochodzi żądanie. Są to więc kolejno:

-   CostPerChild - koszt skorzystania z oferty dla dziecka. Powinien być
    większy od 0. Przyjęta precyzja to 0.01. Parametr ten nie jest
    wymagany.

-   CostPerAdult - koszt skorzystania z oferty dla osoby dorosłej.
    Powinien być większy od 0. Przyjęta precyzja to 0.01. Parametr ten
    nie jest wymagany.

-   MaxGuests - maksymalna liczba osób dla której przeznaczona jest
    oferta. Minimalna wartość to 1. Parametr ten nie jest wymagany.

-   Description - szczegółowy opis oferty. Parametr ten nie jest
    wymagany.

-   Pictures - zdjęcia związane z dodawaną ofertą. Parametr ten nie jest
    wymagany.

Wszystkie parametry dla których wartość nie została zdefiniowana są
ukrywane w widoku oferty. Manager hotelu ma możliwość późniejszej edycji
oferty i wprowadzenie dokładnych wartości.

### `OFFER_ADD_SUCCESS`

<img src="Oferta-Hotel-Serwer/Offer_Add_Success.png">
Po otrzymaniu JSONa z informacjami o ofercie serwer dokonuje ponownej
walidacji wszystkich parametrów. Jeśli oferta została uzupełniona
poprawnie serwer dodaje ją do swojej lokalnej bazy danych i odsyła do
hotelu informację o powodzeniu w postaci kodu operacyjnego:
`OFFER_ADD_SUCCESS` i informacji o ID nowo dodanej oferty. Moduł
hotelowy następnie dodaję do lokalnej bazy danych ofertę ze wskazanym
przez serwer OfferID. Takie rozwiązani pozwala na zachowanie spójności
pomiędzy numerami identyfikacyjnymi ofert po stronie serwera i hotelu.

### `OFFER_ADD_FAILURE`

<img src="Oferta-Hotel-Serwer/Offer_Error.png">
W przypadku błędów w formularzu serwer przesyła kod operacyjny:
`OFFER_ADD_FAILURE` wraz z JSONem zawierającym jedynie informację na
czym polegał błąd. Otrzymany JSON powinien być więc zgodny z powyższym
schematem. Przykładowe błędy to między innymi:

-   Brak HotelID lub jego nieprawidłowa wartość

-   Błędne wypełnienie jednego lub więcej pól formularza - niespełnienie
    warunków walidacji

### `OFFER_DELETE_REQUEST`

**Usuwanie oferty**\
<img src="Oferta-Hotel-Serwer/Offer_DeleteJSON.png">
Manager hotelu wskazuje ofertę przeznaczoną do usunięcia. System
hotelowy następnie przesyła kod operacyjny `OFFER_DELETE_REQUEST` wraz z
zserializowanym JSONem zawierającym ID usuwanej oferty.

### `OFFER_DELETE_SUCCESS`

Po otrzymaniu JSONa serwer podejmuje próbę usunięcia ze swojej lokalnej
bazy danych oferty o wskazanym ID. Jeśli operacja ta powiodła się, do
systemu hotelowego odsyłana jest informacja o powodzeniu w postaci kodu
operacyjnego: `OFFER_DELETE_SUCCESS`.

### `OFFER_DELETE_FAILURE`

<img src="Oferta-Hotel-Serwer/Offer_Error.png">
Jeśli otrzymany JSON zawiera nieprawidłowe ID, serwer przekona się o tym przy
próbie znalezienia zadanego rekordu o zadanym ID. System hotelowy
zostanie poinformowany o zaistniałym błędzie poprzez przesłanie
następującego kodu operacyjnego: `OFFER_DELETE_FAILURE` wraz ze
stosownym komunikatem. W przypadku wystąpienia błędów po stronie
serwera, system hotelowy zostanie poinformowany o zaistniałej sytuacji
analogicznie kodem `OFFER_DELETE_FAILURE` wraz z JSONem zawierającym
jedynie informację na czym polegał błąd. Niezależnie od natury błędu
otrzymany JSON powinien być więc zgodny z powyższym schematem.

### `OFFER_EDIT_REQUEST`

**Edytowanie istniejącej oferty**\

<img src="Oferta-Hotel-Serwer/Offer_Edit_JSON1.png">
<img src="Oferta-Hotel-Serwer/Offer_Edit_JSON2.png">

Manager ma możliwość edycji już istniejącej oferty. W tym celu wybiera
ofertę i przechodzi do jej edycji poprzez formularz znany mu dobrze z
dodawania nowej oferty. Po zakończeniu edycji oferta jest ponownie
walidowana po stronie systemu hotelowego, po czym do serwera zostaje
przesłany kod operacyjny: `EDIT_OFFER_REQUEST` wraz z zserialiozowanym
JSONem zawierającym pola które zostały poddane modyfikacji. Schemat
wiadomości jest więc analogiczny do tego dla dodawania nowej oferty.
Wzbogacony jest jedynie o dodatkowe wymagane pole OfferID pozwalające na
identyfikację modyfikowanej oferty.

### `OFFER_EDIT_SUCCESS`

Po otrzymaniu JSONa z informacjami o ofercie serwer dokonuje ponownej
walidacji wszystkich parametrów. Jeśli oferta została zedytowana
poprawnie serwer uaktualnia odpowiedni wpis w swojej lokalnej bazie
danych i odsyła do systemu hotelowego informację o powodzeniu w postaci
kodu operacyjnego: `OFFER_EDIT_SUCCESS`.

### `OFFER_EDIT_FAILURE`

<img src="Oferta-Hotel-Serwer/Offer_Error.png">
W przypadku błędów w formularzu serwer przesyła kod operacyjny:
`OFFER_EDIT_FAILURE` wraz z JSONem zawierającym jedynie informację na
czym polegał błąd.

## Zarządzanie rezerwacjami

### `RESERVATION_CREATE` {#reservation_info}

Komunikat przesyła szczegółowe informacje dot. rezerwacji. Serwer wysyła
tę wiadomość natychmiast po prośbie klienta stworzenia tejże rezerwacji.

Pola tego obiektu są analogiczne do odpowiadającej mu klasy
`ReservationInfo`, rozszerzone o `ClientID` klienta powiązanego z
rezerwacją.

<img src="Rezerwacje/ReservationInfoSchema.jpg">

Oczekiwane odpowiedzi:

-   `OFFER_UNAVAILABLE`\
    Oferta jest niedostępna w wybranym okresie wg danych po stronie
    hotelu. Hotel sugeruje, że potrzebna jest synchronizacja.

-   `PAYMENT_INFO`\
    Serwer po otrzymaniu `PAYMENT_INFO` zapisuje otrzymane informacje
    tymczasowo w lokalnej bazie danych. Oprócz otrzymanych danych serwer
    przetrzymuje w danym wierszu również informację o ID hotelu, od
    którego je otrzymał. W ten sposób może jednoznacznie zidentyfikować
    rezerwację, w ramach której została utworzona dana płatność (innymi
    słowy, para \[HotelID, ReservationID\] jest tutaj kluczem głównym).
    Informacje te są potrzebne głównie dla późniejszego wykorzystania
    przez klienta przy płatności (patrz: /payments).

    <img src="Rezerwacje/paymentInfo.jpg">

### `PAYMENT_SUCCESS`

Komunikat wysyłany do hotelu po otrzymaniu od klienta informacji o
zakończonym procesie płatności.

<img src="Rezerwacje/reservationIDSchema.jpg">

Oczekiwane odpowiedzi:

-   `PAYMENT_SUCCESS_RESPONSE_SUCCES`\
    Płatność dotarła do hotelu. Hotel w odpowiedzi przesyła obiekt z 2
    właściwościami: **ID rezerwacji** oraz **ID klienta**. Serwer
    zapisuje te informacje w swojej bazie.

-   `PAYMENT_SUCCESS_RESPONSE_FAILURE`\
    Płatność nie dotarła do hotelu. Wiadomość zawiera string w opisanym
    powodem błędu.

-   `ID_UNKNOWN`\
    Nieznane ID rezerwacji

### `RESERVATION_DELETE`

Klient może zrezygnować ze swojej rezerwacji w dowolnym momencie. Zaraz
po otrzymaniu przez serwer takiej prośby, przekazuje ją do hotelu
niniejszym komunikatem. Wewnątrz wiadomości znajduje się ID rezerwacji,
której dotyczy. Struktura jest więc identyczna jak w
([7.4.2](#payment_success).

Oczekiwane odpowiedzi:

-   `RESERVATION_DELETE_SUCCESS`\
    Usunięcie powiodło się.

-   `RESERVATION_DELETE_FAILURE`\
    Usunięcie nie powiodło się.

-   `ID_UNKNOWN`\
    Nieznane ID rezerwacji.

### `RESERVATION_GET`

Zapytanie o szczegóły konkretnej rezerwacji - na przykład w celu
przekazania tych informacji klientowi. Struktura wiadomości identyczna
jak w ([7.4.2](#payment_success)).

Oczekiwane odpowiedzi:

-   `RESERVATION_GET_RESPONSE`\
    Szczegółowe info. dot. rezerwacji. Struktura wiadomości identyczna
    jak w komunikacie `RESERVATION_CREATE `(patrz:
    [7.4.1](#reservation_info)).

-   `ID_UNKNOWN`\
    Nieznane ID rezerwacji.

# Klient-Serwer

Komunikacja pomiędzy klientem (modułem aplikacji klienckiej), a serwerem
odbywa się przy użyciu połączeń HTTP i REST API. Poniżej opisane są
wszystkie endpoint'y oraz związane z nimi żądania i odpowiedzi HTTP
zamodelowane w RAML.

## Autentykacja i autoryzacja

Poniżej opisana jest przykładowa implementacja schematu autentykacji dla
klientów realizowana przez serwer. Część ta jest jedynie przykładem
realizacjitego procesu - inną możliwością jest użycie zewnętrznych usług
autentykacyjnych. Wówczas cały proces autentykacji byłby realizowany
oddzielnie, natomiast serwer przechowywałby jedynie dane klientów bez
ich sekretów. Wówczas na podstawie zewnętrznego dostawcy token'ów JWT
(np. Azure B2C) na podstawie claim'ów zawartych w tym tokenie możliwe
byłoby przydzielenie własnego niestandardowego token'a (opisanego
poniżej) identyfikującego klienta z rekordem w bazie danych klienta.\
Wszystkie endpointy serwera (poza endpointem związanym z logowaniem)
zabezpieczone są przez schemat autentykacji opierający się na tworzeniu
tokenów autentykacyjnych dla każdego klienta w momencie gdy dostarczone
zostaną poprawne dane logowania. W każdym zapytaniu klienta powinien być
dołączony nagłówek "x-session-token", którego wartością jest otrzymany
przez klienta token autentykacyjny. Token ten jest zawsze tworzony po
stronie serwera i odpowiednio szyfrowany w celu uniemożliwienia jego
modyfikacji bądź podrobienia. Generowany token ma format JSON i jego
przykładowa zawartość jest podana na zdjęciu poniżej. Token
"clientSessionToken" jest obiektem JSON zawierającym właściwość, której
wartością jest ciąg znaków będący zaszyfrowanym tokenem JWT, natomiast
"serverSessionToken" powstaje poprzez rozszyfrowanie przez serwer tego
ciągu znaków. Każdy token zawiera właściwość "id", której wartość
jednoznacznie identyfikuje klienta w bazie danych serwera.
![image](Client login + authentication/authentication_tokens.png){width="\\linewidth"}
Poniżej znajduje się dokładny opis schematu autentykacji w języku RAML
oraz zwracane kody błędu związane z niepowodzeniem procesu
uwierzytelnienia klienta. Każda wiadomość związana z błędem zawiera
dokładny opis zawierający czytelne dla człowieka szczegóły dotyczące
tego błędu.
![image](Client login + authentication/authentication_scheme.png){width="\\linewidth"}
![image](Client login + authentication/authentication_error.png){width="\\linewidth"}

## Logowanie klienta i pobieranie danych o kliencie

Z logowaniem klienta jest związana rejestracja konta. Proces rejestracji
nie jest przedstawiony w specyfikacji, gdyż może być on zrealizowany w
dowolny sposób. Przykładową implementacją może być tabela sekretów
klienta w module serwerowym na podstawie której serwer przeprowadza
proces autentykacji, bądź proces rejestracji może być częścią usług
zewnętrznej, w skład której wchodzi proces autentykacji i zarządzanie
sekretami klientów (np. serwis Azure B2C). Poniżej przedstawiona została
przykładowa implementacja logowania klientów w przypadku implementacji
procesu autentykacji przeprowadzanej przez serwer w oparciu o lokalną
tablicę sekretów w bazie danych. Porównywane są wówczas przesłane przez
klienta login i hasło z danymi przechowywanymi w takiej tabeli i
zwracany jest odpowiednio zaszyfrowany token przechowujący ID klienta.\
Do zarządzania danymi związanymi z kontem klienta oraz logowania do
serwisu służą odpowiednio endpointy: `/Client` oraz `/Client/login`.
Endpoint `/Client` jest zabezpieczony wyżej zdefiniowanym schematem
autentykacji, natomiast endpoint służący do logowania nie wymaga
przesyłania nagłówka `x-session-token`.

### `/Client`

![image](Client login + authentication/client_info_type.png){width="\\linewidth"}
![image](Client login + authentication/client_info.png){width="\\linewidth"}
Endpoint ten definiuje 2 metody HTTP: `GET` oraz `PATCH`. Metoda `GET`
pobiera informacje o aktualnie zalogowanym użytkowniku, natomiast metoda
`PATCH` udostępnia możliwość zmiany danych użytkownika takich jak e-mail
lub nazwa użytkownika. W przypadku niepowodzenia metody `PATCH` wysyłany
jest obiekt JSON z właściwością \"errorDescription\" opisującą rodzaj
błędu.

### `/Client/login`

![image](Client login + authentication/client_secrets.png){width="\\linewidth"}
![image](Client login + authentication/client_login.png){width="\\linewidth"}
Endpoint ten nie jest zabezpieczony przez schemat autentykacji - nie
jest wymagane dołączanie tokenu do nagłówka "x-session-token". Endpoint
służący do logowania się użytkowników do systemu za pomocą ustalonego
przy rejestracji loginu i hasła. Wysłane przez klienta dane logowania
jako metoda POST sprawdzane są następnie przez serwer. W przypadku
sukcesu tworzony jest "serverSessionToken" zawierający "id" logującego
się klienta i szyfrowany a następnie zwracany w ciele odpowiedzi HTTP
serwera. W celu dalszej autentykacji klienta token ten jest dołączany do
kolejnych żądań HTTP w nagłówku "x-session-token". W przypadku
niepowodzenia serwer zwraca odpowiedni kod błędu oraz dokładny opis
błędu w ciele odpowiedzi HTTP. Powyżej znajdują się szczegółowe opisy
typów danych oraz żądań i odpowiedzi HTTP w języku RAML.

## Wyszukiwanie hoteli

![image](Oferta+Hotel-Raml/pageable.png){width="\\linewidth"}

Opisane w tej i kolejnej sekcji endpointy `/Hotel` i
`/Hotel/{HotelID}/Offer` korzystają z pagingu. Rozwiązanie to zwiększa
czytelność zwracanych list ograniczając liczbę wyników do ilości
zdefiniowanej przez parametr: limit. Przeglądanie kolejnych stron odbywa
się przez modyfikacje parametru: offset.

### `/Hotel`

::: {.center}
![image](Oferta+Hotel-Raml/Hotel_Raml.png)
:::

![image](Oferta+Hotel-Raml/HotelInfoPreview_Raml.png){width="\\linewidth"}
Endpoint służy do przeglądania hoteli współpracujących z systemem.
Dostęp jest stosownie chroniony przez customSecurityToken. W celu
zwiększenia przejrzystości zastosowano paging ograniczający ilość
rekordów znajdujących się na aktualnie przeglądanej stronie. Ponadto
użytkownik ma możliwość wyszukiwania wymarzonego hotelu po zestawie
filtrów takich jak lokalizacja, czy nazwa hotelu. Wynikiem udanego
wyszukiwania hoteli jest kod 200 wraz z listą zawierającą obiekty typu
HotelInfoPreview. Każdy z elementów listy składa się wyłącznie z
kluczowych informacji na temat hotelu co poprawia czytelność wyników
wyszukiwania. Są to odpowiednio opis hotelu i jego lokalizacja.
Naciśnięcie na jeden z elementów listy powoduje przeniesienie do
enpoint'u `/Hotel/{HotelID}` gdzie uzyskujemy dostęp do dalszych
interakcji z wybranym przez nas hotelem.\
W przypadku niepowodzenia zwracany jest błąd o kodzie 400 wraz ze
stosownym opisem.

### `/Hotel/{HotelID}`

![image](Oferta+Hotel-Raml/HotelID_Raml.png){width="\\linewidth"}
![image](Oferta+Hotel-Raml/HotelInfo_Raml.png){width="\\linewidth"} Po
wybraniu z listy konkretnego hotelu mamy dostęp do większej ilości
informacji w ramach przygotowanego przez hotel opisu. O powodzeniu
jesteśmy również informowani przez kod 200. Uzyskujemy także dostęp do
dalszych działań związanych z wybranym przez nas hotelem. W przypadku
wybrania ID nieistniejącego hotelu użytkownik jest informowany o błędzie
przez kod 404 i stosowny komentarz.

## Wyszukiwanie ofert

### `/Hotel/{HotelID}/Offer`

![image](Oferta+Hotel-Raml/Offer_e_Raml.png){width="\\linewidth"}
![image](Oferta+Hotel-Raml/OfferPreview_Raml.png){width="\\linewidth"}
Zadaniem tego endpointu jest prezentacja ofert należących do wybranego
przez użytkownika we wcześniejszych krokach hotelu. Analogicznie jak w
przypadku opisanego wyżej endpointu `/Hotel` w celu podniesienia
przejrzystości zastosowano paging i wyświetlane są jedynie
najistotniejsze informacje dotyczące prezentowanych ofert. Między
innymi: koszt skorzystania z oferty dla dziecka i osoby dorosłej, a
także maksymalna liczba gości, która może skorzystać z oferty.
Użytkownik aplikacji przy wyszukiwaniu wymarzonej oferty ponownie może
skorzystać z zestawu filtrów. Poprawne wyszukiwanie ofert kończy się,
więc zwróceniem kodu 200 wraz z listą obiektów typu OfferPreview
przedstawionych na powyższym schemacie. O niepoprawnym użyciu filtrów
czy błędzie innej postaci informuje kod 400 wraz ze stosownym
komentarzem.

### `/Hotel/{HotelID}/Offer/{OfferID}`

![image](Oferta+Hotel-Raml/OfferID_Raml.png){width="\\linewidth"}

::: {.center}
![image](Oferta+Hotel-Raml/Offer_Raml.png)
:::

Po wybraniu z listy konkretnej oferty możemy poznać jej szczegóły takie
jak opis, zdjęcia czy opinie innych użytkowników aplikacji, którzy
skorzystali już z tej oferty. Przykładowy obiekt Offer zwracany w
przypadku poprawnego OfferID został przedstawiony na powyższej grafice
ilustrującej cały endpoint. W przypadku wskazania oferty o
nieprawidłowym OfferID jesteśmy informowani o błędzie przez kod 404 wraz
ze stosownym komentarzem.

## Zarządzanie rezerwacjami

Ze szczególną uwagą warto przyjrzeć się komunikatom wymienianym pod
adresem `/Reservations/` oraz `/Payments/`. Ze względu na to, że
potencjalna strona z listą rezerwacji użytkownika stanowi jego \"centrum
dowodzenia wszechświatem\" i za jej pośrednictwem user wykonuje wiele
akcji, warto dobrze zrozumieć wymianę komunikatów odbywającą się w tym
miejscu. Pewne nieoczywiste rozwiązania zastosowane są m.in. przy
płatnościach.

### `/Reservations`

Pod tym adresem można otrzymać wszystkie rezerwacje klienta wraz z
ewentualnymi przypisanymi do nich opiniami. `HotelID` i `ReservationID`
reprezentują wspólnie jedną rezerwację. Są niejako złożonym kluczem
głównym dla rezerwacji. Jeżeli dana rezerwacja nie posiada wystawionej
opinii, właściwość `ReviewID` nie jest zawarta w przesłanych danych
(patrz przykład).

![image](Rezerwacje/reservationType.jpg){width="\\linewidth"}

![image](Rezerwacje/reservationsGET.jpg){width="\\linewidth"} Należy
zauważyć, że nie jest to pełna lista informacji gotowa do wyświetlenia,
a raczej lista powiązanych ze sobą ID. Aby otrzymać taką listę, należy
odwołać się do adresu `/Reservations/{HotelID}/{ReservationID}`
reprezentującego konkretną rezerwację - należy to wykonać dla *każdej*
otrzymanej rezerwacji.

Przesłanie prośby o dodanie nowej rezerwacji odbywa się przez metodę
`POST`. Wysyła się obiekt `ReservationInfo` zawierający wszystkie
niezbędne informacje.

![image](Rezerwacje/reservationInfoType.jpg){width="\\linewidth"}

![image](Rezerwacje/reservationsPOST.jpg)

### `/Reservations/{HotelID}/{ReservationID}`

Pod tym endpointem można znaleźć szczegółowe informacje dotyczące
konkretnej rezerwacji `ReservationID` w hotelu `HotelID` bądź ją usunąć.

![image](Rezerwacje/reservationEndpoint.jpg){width="\\linewidth"}

### `/Payments` {#payments}

Powyżej nie znajdują się jednak *wszystkie* informacje dotyczące
rekordu. Identyfikatory płatności przypisane do rejestracji są
przechowywane pod osobnym adresem URI. Serwer przechowuje te informacje
jedynie przez krótką chwilę, natomiast dłużej są przetrzymywane w Hotelu
(przynajmniej do zakończenia rezerwacji). W momencie, kiedy serwer
otrzymuje komunikat `PAYMENT_INFO` (patrz
([7.4.1](#reservation_info){reference-type="ref"
reference="reservation_info"}) oraz spójrz na
([6.2.1](#reservation_diagram){reference-type="ref"
reference="reservation_diagram"})), zapisuje te informacje u siebie. Aby
klient mógł opłacić zamówienie, musi dostać te informacje - w związku z
tym przy listowaniu rezerwacji należy również się odwołać do
`/Payments`. Kiedy klient szczęśliwie zakończy opłatę, informuje o tym
serwer, który informuje hotel, po czym serwer usuwa informację o tej
płatności. Hotel może przechowywać identyfikator płatności (i tak też
robi, w razie ewentualnych zwrotów) przez dłuższy czas.\
Trudna sytuacja może pojawić się w przypadku, kiedy płatność nie
zostanie potwierdzona przez hotel. Wówczas stan rzeczy jest następujący:

-   Hotel stworzył płatność i oznaczył ją jako nieudaną (znacznik bool
    isError). Przechowuje u siebie dane zarówno o płatności jak i
    rezerwacji.

-   Płatność nie została zakończona, więc serwer nie zapisał sobie tej
    rezerwacji. Posiada natomiast wpis dot. płatności (z flagą isError),
    który pozostawił (ponieważ dostał odpowiedź\
    `PAYMENT_SUCCESS_RESPONSE_FAILURE`).

-   Aplikacja kliencka wyświetliła komunikat z sugestią kontaktu z
    hotelem. Po przeładowaniu interfejsu komunikatu nie ma.

Aby uchronić się przed tym, że użytkownik straci jakiekolwiek informacje
o tej rezerwacji (oraz pieniądze) -- serwer powinien dla każdej
płatności oznaczonej flagą `isError`, poprosić hotel o te dane wysyłając
`RESERVATION_GET`.

![image](Rezerwacje/paymentsGET.jpg){width="\\linewidth"}

![image](Rezerwacje/paymentsDELETE.jpg)

**Uwaga!** Dla uproszczenia projektu, aby nie trzeba było wprowadzać
czwartego modułu zajmującego się płatnościami, proces w systemie jest
symulowany w bardzo prosty sposób: zawsze się udaje. W aktualnym
inkremencie rozwoju nie przewidujemy zwracanego błędu 409 metody
`/Payments DELETE` - poza specjalnie zaaranżowanym przypadkiem testowym.

## Zarządzanie opiniami

Endpointy poniżej służą użytkownikowi do zarządzania swoimi opiniami na
temat rezerwacji które odbył. Oba są zabezpieczone w sposób
przedstawiony na początku tego rozdziału.

::: {.center}
![image](Review+id/return_id.png) ![image](Review+id/review.png)
:::

Powyżej widzimy szczegółowy opis typów używanych przez opisane poniżej
Endpointy w języku RAML. typ review służy do przesyłani informacji o
Opinii i jest odpowiednikiem klasy Review w systemie. Natomiast
id_return służy do przesyłania id świeżo dodanej opinii do klienta.

### `/Review`

![image](Review+id/post.png){width="\\linewidth"}
![image](Review+id/get[].png){width="\\linewidth"}

Endpoint służy do pobierania wszystkich Opinii klienta oraz dodawaniu
nowych. Metoda POST nie znajduje się w `/Review/{id}`, gdyż nie znamy
jeszcze id Opinii. Zostanie ona przyznana przez serwer i odesłana do
klienta za pomocą id_return. Aplikacja kliencka zapisuje po uzyskaniu id
zapisuje nową Opinię razem z innymi. Metoda GET jest tutaj używana zaraz
po logowani Klienta w celu pobrania jego opinii.

### `/Review/{id}`

::: {.center}
![image](Review+id/get.png){width="\\linewidth"}
![image](Review+id/put.png){width="\\linewidth"}
![image](Review+id/delete.png){width="\\linewidth"}
:::

Endpoint służy do zarządzania pojedynczą Opinią.

Metoda GET służy pobieraniu pojedynczej Opinii z Serwera.

Metod PUT służy do edytowani istniejących w systemie Opinii. Zastępuje
ona dane w Serwerze danymi przesłanymi z Aplikacji Klienckiej.

Metoda DELETE natomiast usuwa z systemu Opinię o danym id.

# Scenariusze testowe

Scenariusze przedstawiają proponowane testy do przeprowadzania na
systemie. Podają one na początku dane wejściowe, następnie przebieg
wykonywania zadania oraz ewentualne scenariusze alternatywne (w razie
błędów). Scenariusze są przygotowane w ten sposób aby można było łatwo
przetestować poprawność zaimplementowanego systemu pod kątem komunikacji
- poniższe scenariusze pokrywają jej całość.

## Dodawanie nowej oferty

Manager hotelu dodaje nową ofertę. Moduły pomiędzy którymi odbywa się
komunikacja: Hotel, Serwer. Hotel w ramach którego manager dokonuje
operacji ma następujące ID:

-   HotelID: 1

Formularz został wypełniony następującymi danymi:

-   Cost per child: 80

-   Cost per adult: 120

-   Max guests: 3

-   Description: Very very very good offer

Następuje wymiana wiadomości:

-   Hotel $\rightarrow$ Serwer `OFFER_ADD_REQUEST`\
    Do modułu serwerowego przesłany zostaje zserializowany obiekt
    oferty. Oferta jest walidowana, a następnie dodawana do lokalnej
    bazy danych serwera. Powiedzmy, że oferta zostaje dodana do
    odpowiedniej tabeli z następującym ID:

    -   OfferID: 3

-   Hotel $\leftarrow$ Serwer `OFFER_ADD_SUCCESS`\
    W odpowiedzi odsyłany jest OfferID pod jakim oferta została dodana.
    Moduł hotelowy następnie dodaje ofertę pod tym samym ID do swojej
    lokalnej bazy danych.

Wynikiem pomyślnego zakończenia operacji jest dodanie do bazy danych
serwera i hotelu w stosownych tabelach następującego wpisu:

-   OfferID: 3

-   isActive: true

-   costPerChild: 80

-   costPerAdult: 120

-   maxGuests: 3

-   description: Very very very good offer

Dla serwera wpis ten jest dodatkowo rozszerzony o następujące pole:

-   HotelID: 1

W przypadku gdy:

-   Dojdzie do utraty połączenia przy przesyłaniu oferty przez moduł
    hotelowy do serwera.

-   Walidacja parametrów oferty po stronie serwera nie zakończy się
    powodzeniem.

-   Dojdzie do utraty połączenia przy przesyłaniu odpowiedzi serwera.

-   Przesyłane wiadomości są niezgodne z przyjętym formatem (kody
    operacyjne, treść wiadomości).

operacja kończy się niepowodzeniem i nowa oferta nie jest dodawana do
systemu. Jeśli oferta została już dodana po stronie serwera, z bazy
danych usuwany jest dodany rekord. W przypadku błędów przy walidacji do
modułu hotelowego odsyłana jest odpowiedź o kodzie `OFFER_ADD_FAILURE`
wraz z informacją, które pole zostało błędnie wypełnione.

## Edycja istniejącej oferty

Manager hotelu dokonuje edycji istniejącej już w systemie oferty. Moduły
pomiędzy którymi odbywa się komunikacja: Hotel, Serwer. Hotel w ramach
którego manager dokonuje operacji ma następujące ID:

-   HotelID: 1

Wybrana przez niego oferta ma następujące ID:

-   OfferID: 3

Formularz został wypełniony następującymi danymi:

-   Cost per child: 70

-   Cost per adult: 110

-   Max guests: 4

-   Description: Not so very very very good offer

Następuje wymiana wiadomości:

-   Hotel $\rightarrow$ Serwer `OFFER_EDIT_REQUEST`\
    Do modułu serwerowego przesłany zostaje zserializowany obiekt
    oferty. Oferta jest walidowana, a następnie uaktualniany jest
    stosowny wpis w bazie danych serwera.

-   Hotel $\leftarrow$ Serwer `OFFER_EDIT_SUCCESS`\
    Po otrzymaniu potwierdzenia moduł hotelowy uaktualnia wpis z daną
    ofertą w swojej lokalnej bazie danych

Wynikiem pomyślnego zakończenia operacji jest uaktualnienie wpisu
zawierającego informacje o wskazanej ofercie dla baz danych serwera i
hotelu:

-   OfferID: 3

-   isActive: true

-   costPerChild: 70

-   costPerAdult: 110

-   maxGuests: 4

-   description: Not so very very very good offer

Dla serwera wpis ten jest dodatkowo rozszerzony o następujące pole:

-   HotelID: 1

W przypadku gdy:

-   Dojdzie do utraty połączenia przy przesyłaniu oferty przez moduł
    hotelowy do serwera.

-   Walidacja parametrów oferty po stronie serwera nie zakończy się
    powodzeniem.

-   Dojdzie do utraty połączenia przy przesyłaniu odpowiedzi serwera.

-   Przesyłane wiadomości są niezgodne z przyjętym formatem (kody
    operacyjne, treść wiadomości).

operacja kończy się niepowodzeniem i oferta nie ulega modyfikacji. W
przypadku błędów przy walidacji do modułu hotelowego odsyłana jest
odpowiedź o kodzie `OFFER_EDIT_FAILURE` wraz z informacją, które pole
zostało błędnie wypełnione.

## Usuwanie istniejącej oferty

Manager hotelu usuwa z systemu ofertę. Moduły pomiędzy którymi odbywa
się komunikacja: Hotel, Serwer. Hotel w ramach którego manager dokonuje
operacji ma następujące ID:

-   HotelID: 1

Wybrana przez niego oferta ma następujące ID:

-   OfferID: 3

Następuje wymiana wiadomości:

-   Hotel $\rightarrow$ Serwer `OFFER_DELETE_REQUEST`\
    Do modułu serwerowego przesłane zostaje OfferID=3. Ze stosownej
    tabeli usuwany jest wpis zawierający żądane OfferID.

-   Hotel $\leftarrow$ Serwer `OFFER_DELETE_SUCCESS`\
    Po otrzymaniu potwierdzenia moduł hotelowy również usuwa stosowny
    wpis ze swojej lokalnej bazy danych.

Operacja zakończona powodzeniem usunie z systemu ofertę o wskazanym
OfferID (w tym przypadku OfferID=3). W przypadku gdy:

-   Dojdzie do utraty połączenia przy przesyłaniu ID oferty przez moduł
    hotelowy do serwera.

-   Dojdzie do utraty połączenia przy przesyłaniu odpowiedzi serwera.

-   Przesyłane wiadomości są niezgodne z przyjętym formatem (kody
    operacyjne, treść wiadomości).

-   Oferta o wskazanym ID nie istnieje w systemie.

operacja kończy się niepowodzeniem i stan ofert nie ulega zmianie (żadna
z ofert nie jest usuwana). W przypadku wskazania oferty o nieistniejącym
ID przesyłana jest odpowiedź `OFFER_DELETE_FAILURE`.

## Wyszukiwanie ofert

Klient wyszukuje oferty. Moduły pomiędzy którymi odbywa się komunikacja:
Client, Serwer. Następuje wymiana wiadomości:

-   Client $\rightarrow$ Serwer `/Hotel GET`\
    Formularz z HotelSearchOptions został wypełniony w następujący
    sposób:

    -   longitude: 52.25

    -   latitude: 21.0

    -   radius: 0.5

-   Client $\leftarrow$ Serwer `HTTP 200`\
    W wyniku wyszukiwania zostały zwrócone hotele o następujących ID:

    -   HotelID: 1

    -   HotelID: 2

-   Client $\rightarrow$ Serwer `/Hotel/1 GET`\
    Klient decyduje się na skorzystanie z usług oferowanych przez hotel
    o ID równym 1.

-   Client $\leftarrow$ Serwer `HTTP 200`\
    Serwer zwraca informacje o wybranym hotelu wraz z możliwością
    przeglądania jego ofert.

-   Client $\rightarrow$ Serwer `/Hotel/1/Offer GET`\
    Formularz z OfferSearchOptions został wypełniony w następujący
    sposób:

    -   FromTime: 2077-07-07

    -   ToTime: 2077-07-17

    -   MaxGuests: 2

    -   CostMin: 0

    -   CostMax: 70

-   Client $\leftarrow$ Serwer `HTTP 200`\
    W wyniku wyszukiwania zostały zwrócone oferty o następujących ID:

    -   OfferID: 1

    -   OfferID: 3

-   Client $\rightarrow$ Serwer `/Hotel/1/Offer/3 GET`\
    Klient decyduję się na wybór oferty o ID=3.

-   Client $\leftarrow$ Serwer `HTTP 200`\
    Serwer zwraca informacje o wybranej ofercie.

Operacja zakończona powodzeniem powinna kolejno zwracać klientowi
następujące widoki:

-   Lista hoteli spełniających kryteria.

-   Szczegółowe informacje o wybranym hotelu.

-   Lista ofert spełniających kryteria.

-   Szczegółowe informacje o wybranej ofercie.

W przypadku gdy:

-   Nie został znaleziony żaden hotel, bądź oferta o wskazanych
    parametrach

wyszukiwanie kończy się odpowiednio przy znajdowaniu ofert czy hoteli.
Taka sytuacja kończy się kodem 200 i zwróconą pustą listą hoteli, bądź
ofert co nie jest uważane za błąd.\
W przypadku gdy:

-   Dojdzie do utraty połączenia w trakcie wyszukiwania ofert, czy
    hoteli

proces wyszukiwania ofert zostaje przerwany o czym użytkownik jest
informowany w postaci stosownego błędu.\
W przypadku gdy:

-   Formularz HotelSearchOptions, bądź OfferSearchOptions nie został
    wypełniony poprawnie

użytkownik jest informowany o błędzie w postaci kodu 400. Ma możliwość
wznowienia wyszukiwania od momentu zakończonego błędem.

## Synchronizacja / dodawanie lokalnej Rezerwacji

W hotelu dodano nową rezerwacje lokalnie. W tym celu system hotelowy
dodaje rezerwację używając swojego UserID i przeprowadza synchronizację
jeśli na serwerze zostanie wykryta rezerwacja kolidująca z wprowadzaną
to wprowadzana zostaje cofnięta, a wykryta zostaje wprowadzona do
systemu i operacja kończy się niepowodzeniem. Z punktu widzenia całego
systemu nie różni się to niczym od przeprowadzenia synchronizacji.

1.  Hotel $\rightarrow$ Serwer `Hotel_SYNC_REQUEST`\
    Rozpoczęcie synchronizacji.

2.  Serwer $\rightarrow$ Hotel `HOTEL_SYNC_RESPONSE_SUCCESS`\
    Poprawnie przeprowadzono synchronizację

W wypadku gdy:

-   Offer IsActive = false

-   Nie ma przypisanych pokoi do Offer

, to funkcja `AddLocalReservation` nie dodaje rezerwacji do systemu i
zwraca false.

## Dodawanie Opinii

Klient dodaje opinię do rezerwacji. W tym celu ściąga swoje rezerwacje i
wybiera dla jednej z nich opcję dodania Opinii. Następnie wypełnia
formularz i przesyła go do serwera. Serwer następnie odsyła info o id
nadane Opinii. Z punktu widzenia systemu sytuacja wygląda następująco:

1.  Client $\rightarrow$ Serwer `/Reservations GET`\
    Pobieranie własnych rezerwacji.

2.  Client $\rightarrow$ Serwer `/Review POST`\
    Klient wysyła formularz i dostaje zwrot z id Opinii.

W wypadku gdy:

-   Istniała już opinia do danej rezerwacji(w takim wypadku radzimy
    zamienić przycisk przy rezerwacji z dodaj Opinię na Edytuj Opinię)

-   Klient nie odbył rezerwacji lub nie ma rezerwacji na podaną ofertę(w
    takim wypadku radzimy aby przycisk umożliwiający dodanie Opinii się
    nie wyświetlał)

-   Klient utracił połączenie z serwerem na dłuższy okres czasu

operacja kończy się niepowodzeniem i nie wprowadza zmian do systemu.

## Usuwanie Opinii

Klient usuwa opinię; może to zrobić na 2 sposoby: wyświetlić swoje
rezerwację i stąd mieć możliwość usunięcia Opinii przypisanej do danej
rezerwacji lub wyświetlić wszystkie swoje Opinie i stąd usunąć jedną
wybraną. Pierwszy sposób z punktu widzenia systemu wygląda następująco:

1.  Client $\rightarrow$ Serwer `/Reservations GET`\
    Pobieranie własnych rezerwacji.

2.  Client $\rightarrow$ Serwer `/Review/{id} DELETE`\
    Klient wysyła prośbę o usunięcie Opinii o danym id.

Drugi natomiast:

1.  Client $\rightarrow$ Serwer `/Review GET`\
    Pobieranie własnych Opinii.

2.  Client $\rightarrow$ Serwer `/Review/{id} DELETE`\
    Klient wysyła prośbę o usunięcie Opinii o danym id.

Operacja zakończona powodzeniem usunie z systemu daną opinię.

W wypadku gdy:

-   Istniała już opinia do danej rezerwacji

-   Klient nie odbył rezerwacji lub nie ma rezerwacji na podaną ofertę

-   Klient utracił połączenie z serwerem na dłuższy okres czasu

, operacja kończy się niepowodzeniem i nie wprowadza zmian do systemu.

## Edycja Opinii

Klient edytując opinię może to zrobić na 2 sposoby. Wyświetlić swoje
rezerwację i stąd mieć możliwość edycji Opinii przypisanej do danej
rezerwacji lub wyświetlić wszystkie swoje Opinie i stąd edytować jedną
wybraną. Pierwszy sposób z punktu widzenia systemu wygląda następująco:

1.  Client $\rightarrow$ Serwer `/Reservations GET`\
    Pobieranie własnych rezerwacji.

2.  Client $\rightarrow$ Serwer `/Review/{id} PUT`\
    Klient wysyła prośbę o nadpisanie Opinii o danym id.

Drugi natomiast:

1.  Client $\rightarrow$ Serwer `/Review GET`\
    Pobieranie własnych Opinii.

2.  Client $\rightarrow$ Serwer `/Review/{id} PUT`\
    Klient wysyła prośbę o nadpisanie Opinii o danym id.

Operacja zakończona powodzeniem nadpisze z systemu daną opinię.

W wypadku gdy:

-   Nie istnieje taka Opinia

-   Opinia istnieje ale nie należy do danego Klienta

-   Klient utracił połączenie z serwerem na dłuższy okres czasu

-   Istnieje taka Opinia ale nie do tej samej rezerwacji lub oferty

, operacja kończy się niepowodzeniem i nie wprowadza zmian do systemu.

## Rezerwacja pokoju przez klienta

Klient planuje z rodziną nocleg w *Warszawie* w dniach *2022-02-02* do
*2022-02-22*. Po znalezieniu i wybraniu swojej wymarzonej oferty,
wypełnia formularz:

-   FromTime: 2022-02-02

-   ToTime: 2022-02-22

-   Number of children: 2

-   Number of adults: 2

i przesyła go do serwera. Po chwili otrzymuje szczegóły dot. płatności,
finalizuje ją i cieszy się z potwierdzonej rezerwacji.

-   Klient najpierw popełnia pomyłkę i w polu \"Liczba dzieci\" wpisuje
    20 zamiast 2. Otrzymuje błąd 400. Nie zachodzą żadne zmiany w
    danych.\
    Jeżeli walidacja UI uniemożliwia wprowadzenie takiej liczby, można
    zasymulować ten przypadek ręcznym odniesieniem się do
    `/Reservations POST`.

-   Tym razem klient wypełnił formularz poprawnie. Nastąpiła wymiana
    wiadomości:

    -   Client $\rightarrow$ Serwer `/Reservations POST`

    -   Serwer $\rightarrow$ Hotel `RESERVATION_CREATE`

    -   Serwer $\leftarrow$ Hotel `PAYMENT_INFO`\
        Serwer zanotował dane dot. płatności.

    -   Client $\leftarrow$ Serwer `HTTP 200`

    -   Klient został przekierowany na stronę ze swoimi rezerwacjami.\
        Client $\rightarrow$ Serwer `/Reservations GET`\
        Client $\rightarrow$ Serwer
        `/Reservations/{HotelID}/{ReservationID} GET` dla każdej z
        uzyskanych rezerwacji.\
        Client $\rightarrow$ Serwer `/Payments GET`. Klient zauważa
        jedną aktywną płatność, którą od razu realizuje:\
        Client $\rightarrow$ Serwer `/Payments DELETE`

    -   Serwer $\rightarrow$ Hotel `PAYMENT_SUCCESS`

    -   Serwer $\leftarrow$ Hotel `PAYMENT_SUCCESS_RESPONSE_SUCCESS`\
        Serwer usunął dane dot. płatności.

    -   Client $\leftarrow$ Serwer `HTTP 200` (odpowiedź do
        `/Payments DELETE`)

-   Przy podglądzie własnych rezerwacji, klient widzi właśnie dokonaną
    rezerwację pokoju z danymi, które są takie same jak te wprowadzone w
    formularzu.

-   Rezerwację widzi również hotel. Ma łatwy podgląd danych klienta,
    pokoju do którego zostanie skierowany oraz oferty, w ramach której
    złożono rezerwację wraz z przedziałem czasowym z nią związanym.

## Anulowanie Rezerwacji

Klient ma na swoim koncie 3 rezerwacje:

1.  rezerwacja zakończona kilka dni temu - istnieje możliwość oceny
    pobytu,

2.  rezerwacja w trakcie,

3.  rezerwacja z przyszłą datą pobytu, (już opłacona).

Spośród nich tylko jedną (ostatnią) można anulować.

-   Próba ręcznego anulowania rezerwacji przeszłej bądź będącej w
    trakcie, przez odwołanie się do endpointu
    `/Reservations/{HotelID}/{ReservationID} DELETE` zwraca 400. Bazy
    danych pozostają nienaruszone.

-   Anulowanie przyszłej rezerwacji kończy się sukcesem. Wymiana
    komunikatów:

    -   Client $\rightarrow$ Serwer
        `/Reservations/{HotelID}/{ReservationID} DELETE`

    -   Serwer $\rightarrow$ Hotel `RESERVATION_DELETE`

    -   Serwer $\leftarrow$ Hotel `RESERVATION_DELETE_SUCCESS`

    -   Client $\leftarrow$ Serwer `HTTP 200`

-   Lista rezerwacji klienta zostaje zaktualizowana.

-   Hotel również widzi aktualną listę rezerwacji.

-   Jeśli zaszła taka potrzeba, dane (np. te dotyczące (nie)dostępności
    oferty) po stronie serwera zostały zaktualizowane odpowiednio do
    zmian - Serwer oraz hotel pozostają zsynchronizowane.

## Płatność

Po zakończonej płatności za rezerwację, klient przekazuje taką
informację do systemu. Na potrzeby testów, Hotel na tę wiadomość
powinien zareagować negatywnie (należy to zmienić w kodzie, np. w
odpowiednim miejscu `return true;` na `return false;`). W tym momencie
klient otrzymuje informację o tym, że płatność nie została przyjęta
poprawnie. Sugeruje się kontakt z hotelem w celu uzgodnienia wykonania
płatności, np. na miejscu albo zostawia się inicjatywę związaną z
rozwiązaniem zaistniałej sytuacji hotelowi (szczegóły takiej sytuacji:
([8.5.3](#payments){reference-type="ref" reference="payments"})).
Nastąpiła wymiana komunikatów:

-   Client $\rightarrow$ Serwer `/Payments GET`

-   Client $\leftarrow$ Serwer `HTTP 200`

-   Client $\rightarrow$ Serwer `/Payments DELETE`

-   Serwer $\rightarrow$ Hotel `PAYMENT_SUCCESS`

-   Serwer $\leftarrow$ Hotel `PAYMENT_SUCCESS_RESPONSE_FAILURE`

-   Client $\leftarrow$ Serwer `HTTP 409`

-   Przy kolejnym wypisaniu rezerwacji widnieje wiadomość o tym, że
    płatność jest problematyczna.

# Wymagania Technologiczne

## Serwer

-   System - Windows 10 64bit lub Linux Pop!\_OS 20.04 LST lub
    ekwiwalentny

-   Procesor - Inter Core i5-3570K

-   RAM - 8GB

-   Miejsce na dysku - 80 GB

-   Język C\# - .Net Core 3.1.11 lub nowszy

## Aplikacja Kliencka

-   System - Windows 10 64bitowy

-   Procesor - Inter Core i5-3570K

-   RAM - 8GB

-   Karta graficzna - NVIDIA GeForce GTX 780 or AMD Radeon RX 470

-   DirectX - Wersja 12

-   Miejsce na dysku - 70 GB

-   Język C\# - .Net Framework 4.8 lub nowszy

## System hotelowy

-   System - Windows 10 64bitowy

-   Procesor - Inter Core i5-3570K

-   RAM - 8GB

-   Karta graficzna - NVIDIA GeForce GTX 780 or AMD Radeon RX 470

-   DirectX - Wersja 12

-   Miejsce na dysku - 70 GB

-   Język C\# - .Net Framework 4.8 lub nowszy
