---
title:  Pierwsze kroki z DMR na przykładzie Baofeng 1801
description: Pierwsze kroki w sieci SP-DMR dla posiadaczy taniego radiotelefonu Baofeng 1801
---

Baofeng 1801 to jeden z najtańszych obecnie radiotelefonów wspierających DMR, więc jest to dobry wybór na rozpoczęcie zabawy z transmisjami cyfrowymi zwłaszcza, jeśli ktoś nie jest do nich przekonany. Poza DMR jest oczywiście możliwość analogowej komunikacji w paśmie 2m i 70cm, więc nawet jeśli łączność cyfrowa nie przypadnie nam do gustu, to urządzenie się nie zmarnuje. Na jego plus przemawia również to, że sprawia wrażenie solidniej wykonanego niż mój poprzedni Baofeng UV-82.

Niestety, na początku DMR może przytłoczyć mnogością opcji. Postaram się ten temat trochę przybliżyć w dalszej części tego artykułu. Zakładam jednak, że czytelnicy są licencjonowanymi radioamatorami lub są zaznajomieni z podstawową radioamatorską nomenklaturą.

## Czym jest DMR?

DMR, czyli Digital Mobile Radio, jest standardem łączności cyfrowej. Dość dużo informacji na początek można znaleźć [na Wikipedii](https://pl.wikipedia.org/wiki/Digital_Mobile_Radio). Standard ten jest otwarty i został opracowany z myślą o łączności profesjonalnej, ale radioamatorzy odnaleźli się w nim jak ryba w wodzie.

Co daje w praktyce? Możliwość korzystania z radiotelefonu w podobny sposób jak z telefonu, można łączyć się albo bezpośrednio z innym radiotelefonem (i to nie tylko znajdującym się w bezpośrednim zasięgu), albo rozmawiać grupowo na dowolnie wybranej Talk Groupie. Można również przesyłać dane lub - tak! - krótkie wiadomości tekstowe.

Można się kłócić, że równie dobrze można użyć zwykłego telefonu, a taka zabawa odbiera całą radość z robienia łączności na odległość, ale przekonywanie kogokolwiek do czegokolwiek nie jest celem tego artykułu. Radioamatorstwo to szerokie hobby i miejsca wystarczy dla każdego.

## Mały słowniczek

Osoby zaczynające poznawać DMR od razu zauważą mnogość nowych terminów, które nie zawsze są dobrze wytłumaczone. Zacznijmy więc od małego słowniczka:
 * **DMR ID** - DMRowy odpowiednik numeru telefonu, indywidualny dla każdego radioamatora. DMR ID dla Polski zaczynają się od cyfr `260`, po którym następuje numer okręgu - w moim przypadku moim DMR ID jest `2606134`. DMR ID można uzyskać [rejestrując się na odpowiedniej stronie](https://register.ham-digital.org/), można je również [przeszukiwać na tej stronie](https://ham-digital.org/dmr-userreg.php) oraz pobrać [całą bazę DMR ID](https://ham-digital.org/status/).
 * **Talk Group** - po polsku *grupy rozmowne*. Numery DMR ID, z którymi można wykonywać łączności grupowe. Polecam zapoznać się z [artykułem na SP-DMR](http://www.sp-dmr.pl/brandmeister/grupy-rozmowne-konfiguracja/) oraz [wiki BrandMeistera](https://wiki.brandmeister.network/index.php/Poland), gdzie opisane są polskie grupy rozmowne.
 * **Private Call** - łączności 1-1. Niezalecane na przemiennikach, bo inni użytkownicy nie słyszą takich łączności - co oczywiście nie oznacza, że usłyszenie ich jest niemożliwe. Łączności takie zajmują przemiennik uniemożliwiając innym włączenie się do rozmowy, co jest generalnie nieeleganckie.
     - Private Call jest wykorzystywany również do wykonywania poleceń na przemienniku, jak łączenie z reflektorami. Kwestii reflektorów nie będę poruszał w tym artykule.
 * **BrandMeister** - Sieć DMR, do której również należy polska sieć SP-DMR. Łączy ze sobą przemienniki, hotspoty itp. [Strona sieci](https://brandmeister.network/), [Podgląd aktualnie prowadzonych łączności](https://hose.brandmeister.network/).
 * **Hotspot** - mały przemiennik do osobistego użytku, o niewielkiej mocy. Przydatne do testów (nie zajmuje się "dużego" przemiennika) oraz do ułatwienia łączności jeśli jesteśmy poza zasięgiem przemienników. Wymaga łączności z internetem aby uzyskać dostęp do sieci DMR i móc porozumiewać się z innymi hotspotami i przemiennikami.
 * **Code Plug** - Konfiguracja radia zrzucona do pliku. Często udostępniana dla ułatwienia wstępnej konfiguracji, ale nie jest potrzebna do rozpoczęcia pracy.
 * **Color Code**, **CC** - Funkcjonalność w praktyce trochę podobna do kodów CTCSS - radiotelefony potrafią porozumiewać się tylko z innymi stacjami, które mają ustawioną taką samą wartość CC. Dzięki temu różne sieci korzystające z tej samej częstotliwości mogą sobie nawzajem nie przeszkadzać - choć oczywiście jeśli dwie stacje z różnymi CC będą chciały jednocześnie korzystać z tego samego pasma, to pojawią się problemy z jakością dźwięku.
 * **Time Slot**, **TS** - Po polsku *Szczelina Czasowa*. DMR stosuje wielodostęp TDMA, i na każdej częstotliwości mogą odbywać się jednocześnie dwie łączności - na pierwszej i drugiej szczelinie czasowej. Na Wikipedii można znaleźć [artykuł wyjaśniający, czym jest TDMA](https://pl.wikipedia.org/wiki/TDMA).
     - Który TS wybrać? *To zależy.*
         + W przypadku łączności z hotspotem najprawdopodobniej zawsze będziemy korzystali z jednego TS. W przypadku mojego hotspota jest to TS2, ale w każdym może być inaczej.
         + W przypadku łączności przez przemiennik należy wybrać 1 lub 2 TS. Zgodnie z [opisem na stronie SP-DMR](http://www.sp-dmr.pl/brandmeister/grupy-rozmowne-konfiguracja/), do łączności w sieci należy używać slotu pierwszego, a do łączności w obrębie przemiennika - slotu drugiego.
         + Do łączności bezpośrednich (simpleks) preferowany jest TS2.

## Programowanie Baofeng 1801
Potrzebne będzie [narzędzie do programowania tego radiotelefonu](https://nc.fl9.eu/index.php/s/eiHitRmBLjtHdfE). Niestety, działa ono tylko pod Windowsem - na Linuksie Wine, którego użyłem nie było w stanie uruchomić tego cudu techniki. Windows pod VirtualBoksem działa wyśmienicie, pod warunkiem przekierowania portów USB - [instrukcję można znaleźć na StackOverflow](https://askubuntu.com/questions/25596/how-to-set-up-usb-for-virtualbox/25600#25600).

Mogą też być potrzebne sterowniki do kabla. Należy je zdobyć we własnym zakresie w zależności od posiadanego przewodu.

### 1. Podłączenie radia i pobranie ustawień

Tutaj nie ma niespodzianek. Podłączamy radio kablem do portu USB w komputerze i naciskamy przycisk read <img style="width: 18px" alt="Ikonka przycisku z tooltipem READ" src="https://zsyp.fl9.eu/blog/dmr/read.png" />.

 * Uwaga - radio powinno być ustawione na kanał, na którym nie odbywają się żadne łączności. 
 *  Warto w tym momencie zapisać zgrane ustawienia do pliku `.dat`, aby móc w razie czego do nich wrócić - program jest dość kapryśny i w moim przypadku np. raz przestawił język radia na chiński.

### 2. Podstawowe ustawienia radia

W bocznym drzewku wybieramy `Basic Configure` -> `General Setting`. Tutaj mamy tak naprawdę dwa ważne ustawienia:

 * Radio ID - Tutaj należy podać swój DMR ID
 * Private Call - Musi być zaznaczone, aby móc wykonywać połączenia 1-1.

### 3. Kontakty

Każdy użytkownik sieci DMR jest identyfikowany za pomocą DMR ID, jednak przydałby się sposób na to, żeby te numery mogły być przez radio odczytywane, a zamiast nich były wyświetlane znaki wywoławcze, nazwy grup rozmownych lub cokolwiek innego podamy.

W bocznym drzewku wybieramy `DMR` i klikamy dwukrotnie na `Digital Contact`. Można to rozwinąć, ale nas to obecnie nie interesuje - zaimportujemy kontakty z pliku.

Tutaj warto nadmienić dość poważne ograniczenie tego radia - można mieć co najwyżej 1024 kontakty. Widać więc od razu, że nie tylko nie damy rady zaprogramować wszystkich znanych DMR ID, ale nawet wszystkich polskich DMR ID. Będzie trzeba je trochę pofiltrować.

**Uwaga:** Dla ułatwienia gotową listę kontaktów możesz pobrać tutaj: [Lista kontaktów dla Baofeng 1801 w pliku CSV](https://zsyp.fl9.eu/blog/dmr/bao1801contacts.csv) - przygotowaną 13 lipca 2019. Ta lista może się dość szybko zdezaktualizować, więc polecam czytać dalej.

Na początek pobieramy listę wszystkich DMR ID znanych sieci BrandMeister - możemy to zrobić [na tej stronie](https://brandmeister.network/?page=contactsexport). Następnie przetwarzamy pobrane kontakty tym skryptem, który [umieściłem na GitHubie](https://gist.github.com/michalrud/bf9d2f2ab9cac3afed18dd3ccfdda712#file-brandmeister_to_1801-py).

Wymaga on pewnego dostrojenia do własnych potrzeb. Oto, co trzeba zmienić:

 * Jeśli nasz plik pobrany ze strony BrandMeistera ma inną nazwę niż `bm_ce.csv` (a pewnie ma, ale domyślna nazwa jest dziwna i ma dużo spacji), to musimy albo zmienić jego nazwę, albo dostosować `INPUT_FILENAME` w skrypcie.
 * w zmiennej `hardcoded` są wpisane kontakty, które będą dodane na początek listy, oprócz tych z eksportu. W eksporcie nie znajdują się np. grupy rozmowne, więc te trzeba dopisać tutaj samemu. Oprócz tego ja dopisałem sobie też kontakty, które nie przeszły przez gęste sito filtrujące - jeden zza granicy, i jeden, który dopiero zaczyna pracę z DMR. Te kontakty zostały jednak tutaj pominięte.
 * W trzeciej linijce od końca odbywa się filtrowanie listy kontaktów. Każdy będzie miał swoje wymagania co do kontaktów, dlatego też warto dostosować ten kod do swoich potrzeb. Ja stosuję dwa warunki, które pozwalają mi zmniejszyć listę tak, aby miała mniej niż magiczne 1024 pozycje:
     - DMR ID musi być z Polski - świadczy o tym `260` na początku DMR ID,
     - DMR ID musiał wykonać co najmniej 20 łączności.

### 4. Kanały

Czas na zaprogramowanie częstotliwości - tutaj już każdy, kto korzystał wcześniej z łączności analogowych powinien czuć się jak w domu. W przypadku częstotliwości DMR nie jest to co prawda takie proste, i pojawia się kilka nowych parametrów do ustawienia, ale nie ma się czego bać.

#### Ustawienie przemienników DMR:

 * **Mode**: Digital
 * **Name**: Dowolna nazwa
 * **RX Freq**, **Tx Freq**: Częstotliwości
 * **Admit Criteria**: Always
 * **Privacy**: Off
 * **Color Code**: 1 (dla innych przemienników wartość może być inna)
 * **Emergency System**: None
 * **Contact**: Domyślny kontakt na kanale, np. ulubiona grupa rozmowna
 * **Repeater Slot**: Szczelina czasowa
 * **Private Call Confirmed**: Zaznaczone
 * **Data Call Confirmed**: Zaznaczone

#### Import kanałów ze strony przemienniki.net

Na GitHubie z konwerterem kontaktów umieściłem również [skrypt pobierający i konwertujący listę przemienników ze strony przemienniki.net](https://gist.github.com/michalrud/bf9d2f2ab9cac3afed18dd3ccfdda712#file-przemienniki_to_1801-py). Pobiera on wszystkie przemienniki aktualnie działające w Polsce i wrzuca do pliku csv do wrzucenia na radio. Ten skrypt również można dostosować do swoich potrzeb:

 * W linii 22 wartość `HARDCODED` może być dostrojona, by dodać częstotliwości, których nie ma w bazie - np. ulubione częstotliwości directowe.
 * Wartości `COUNTRY` i `MODE` mogą być zmienione żeby dopasować ustawienia filtrowania częstotliwości.

Skrypt sprawdza rodzaj przemiennika, i jeśli przemiennik zdaje się wspierać DMR - jego tryb zostanie ustawiony na cyfrowy, a on sam zostanie dodany w dwóch kopiach - z ustawionymi obiema szczelinami czasowymi (TS1 i TS2), żeby nie trzeba było grzebać w menu i przełączać.

**Uwaga:** Wygląda na to, że na przemienniki.net nie ma wymienionych wszystkich przemienników DMRowych. Pełną listę można znaleźć na [liście przemienników na stronie SP-DMR](http://www.sp-dmr.pl/przemienniki-sp-dmr/).

#### Strefy (zones)

Na początku podczas eksperymentów z radiem zdziwiłem się, że jestem w stanie przełączać tylko pomiędzy pierwszymi 10 zaprogramowanymi kanałami. Okazuje się, że kanały są pogrupowane w *strefy* (ang. Zone), więc nie wszystkie są domyślnie dostępne. Jest to całkiem fajna funkcjonalność pozwalająca na stworzenie do 150 grup, każdej zawierającej do 32 kanałów. Dzięki temu można wygodnie pogrupować sobie kanały na cyfrowe, analogowe, directowe, lokalne, wyjazdowe... Muszę przyznać, że całkiem mi się ta opcja spodobała, choć oczywiście - jak chyba wszystko w tym radiotelefonie - nie jest ona wystarczająco dobrze przybliżona w instrukcji obsługi.

Strefy tworzy i modyfikuje się łatwo - w narzędziu programującym należy w drzewku wejść w `Zone` i kliknąć na jedną ze stref znajdujących się pod spodem. W otwartym okienku można dodawać, usuwać i sortować kanały w strefie, jak również można zmienić nazwę samej grupy.

Następnie w radiu należy wejść w *Menu* -> *Zone* i wybrać strefę dla aktualnego VFO, górnego albo dolnego.

### 5. Programowanie radia

Tutaj również nie ma niespodzianek. Wystarczy użyć przycisku Write <img style="width: 18px" alt="Ikonka przycisku z tooltipem WRITE" src="https://zsyp.fl9.eu/blog/dmr/write.png" />, a po chwili radio będzie zaprogramowane.

Ja próbując programować za pierwszym razem zawsze otrzymuję komunikat o błędzie połączenia - za drugim razem jednak zawsze działa bezbłędnie.

## Łączności bezpośrednie / direct

Łączności simpleksowe w DMR robi się niewiele trudniej niż łączności analogowe - wystarczy na obu radiotelefonach ustawić tę samą częstotliwość i upewnić się, że Color Code jest ustawiony na taką samą wartość. Ze względu na to, że w DMR trzeba mieć ustawionego adresata łączności, trzeba wybrać grupę rozmowną w której rozmawiamy - **w simpleksie zaleca się korzystanie z grupy 9**. Preferowane jest również **używanie TS2**.

## Podsumowanie

To już w sumie wszystko, co musiałem zrobić żeby rozpocząć pracę z DMRem. Jako, że są to moje pierwsze kroki w tej sieci, chętnie dowiem się, o czym zapomniałem i czego nie zrobiłem - jeśli masz uwagi, proszę o kontakt, a zaktualizuję artykuł:

 * Mailowy: SP6MR (czyli mój znak wywoławczy) na fl9.eu
 * Mastodon: [@sp6mr@mastodon.radio](https://mastodon.radio/@sp6mr)
 * No i oczywiście przez radio!

## Historia zmian

 * 16 lipca 2019:
     - Dokładniejszy opis szczelin czasowych,
     - Opis Private Call i adnotacja,
     - Skrypt konwertujący z przemienniki.net dodaje przemienniki DMR z oboma szczelinami czasowymi,
     - Link do listy przemienników na SP-DMR,
     - Opis łączności direktowych
 * 14 lipca 2019: Dodany skrypt pobierający częstotliwości przemienników i opis stref
 * 13 lipca 2019: Pierwsza wersja