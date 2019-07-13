---
title:  Pierwsze kroki z DMR na przykładzie Baofeng 1801
description: Pierwsze kroki w sieci SP-DMR dla posiadaczy taniego radiotelefonu Baofeng 1801
---

Baofeng 1801 to jeden z najtańszych obecnie radiotelefonów wspierających DMR, więc jest to dobry wybór na rozpoczęcie zabawy z transmisjami cyfrowymi zwłaszcza, jeśli ktoś nie jest do nich przekonany. Poza DMR jest oczywiście możliwość analogowej komunikacji w paśmie 2m i 70cm, więc nawet jeśli łączność cyfrowa nie przypadnie nam do gustu, to urządzenie się nie zmarnuje. Na jego plus przemawia również to, że sprawia wrażenie solidniej wykonanego niż mój poprzedni Baofeng UV-82.

Niestety, na początku DMR może przytłoczyć mnogością opcji. Postaram się ten temat trochę przybliżyć w dalszej części tego artykułu.

## Czym jest DMR?

DMR, czyli Digital Mobile Radio, jest standardem łączności cyfrowej. Dość dużo informacji na początek można znaleźć [na Wikipedii](https://pl.wikipedia.org/wiki/Digital_Mobile_Radio). Standard ten jest otwarty i został opracowany z myślą o łączności profesjonalnej, ale radioamatorzy odnaleźli się w nim jak ryba w wodzie.

Co daje w praktyce? Możliwość korzystania z radiotelefonu w podobny sposób jak z telefonu, można łączyć się albo bezpośrednio z innym radiotelefonem (i to nie tylko znajdującym się w bezpośrednim zasięgu), albo rozmawiać grupowo na dowolnie wybranej Talk Groupie. Można również przesyłać dane lub - tak! - krótkie wiadomości tekstowe.

Można się kłócić, że równie dobrze można użyć zwykłego telefonu, a taka zabawa odbiera całą radość z robienia łączności na odległość, ale przekonywanie kogokolwiek do czegokolwiek nie jest celem tego artykułu. Radioamatorstwo to szerokie hobby i miejsca wystarczy dla każdego.

## Mały słowniczek

Osoby zaczynające poznawać DMR od razu zauważą mnogość nowych terminów, które nie zawsze są dobrze wytłumaczone. Zacznijmy więc od małego słowniczka:

**DMR ID** - DMRowy odpowiednik numeru telefonu, indywidualny dla każdego radioamatora. DMR ID dla Polski zaczynają się od cyfr `260`, po którym następuje numer okręgu - w moim przypadku moim DMR ID jest `2606134`. DMR ID można uzyskać [rejestrując się na odpowiedniej stronie](https://register.ham-digital.org/), można je również [przeszukiwać na tej stronie](https://ham-digital.org/dmr-userreg.php) oraz pobrać [całą bazę DMR ID](https://ham-digital.org/status/).

**Talk Group** - po polsku *grupy rozmowne*. Numery DMR ID, z którymi można wykonywać łączności grupowe. Polecam zapoznać się z [artykułem na SP-DMR](http://www.sp-dmr.pl/brandmeister/grupy-rozmowne-konfiguracja/) oraz [wiki BrandMeistera](https://wiki.brandmeister.network/index.php/Poland), gdzie opisane są polskie grupy rozmowne.

**BrandMeister** - Sieć DMR, do której również należy polska sieć SP-DMR. Łączy ze sobą przemienniki, hotspoty itp. [Strona sieci](https://brandmeister.network/), [Podgląd aktualnie prowadzonych łączności](https://hose.brandmeister.network/).

**Hotspot** - mały przemiennik do osobistego użytku, o niewielkiej mocy. Przydatne do testów (nie zajmuje się "dużego" przemiennika) oraz do ułatwienia łączności jeśli jesteśmy poza zasięgiem przemienników. Wymaga łączności z internetem aby uzyskać dostęp do sieci DMR i móc porozumiewać się z innymi hotspotami i przemiennikami.

**Code Plug** - Konfiguracja radia zrzucona do pliku. Często udostępniana dla ułatwienia wstępnej konfiguracji, ale nie jest potrzebna do rozpoczęcia pracy.

**Color Code**, **CC** - Funkcjonalność w praktyce trochę podobna do kodów CTCSS - radiotelefony potrafią porozumiewać się tylko z innymi stacjami, które mają ustawioną taką samą wartość CC. Dzięki temu różne sieci korzystające z tej samej częstotliwości mogą sobie nawzajem nie przeszkadzać - choć oczywiście jeśli dwie stacje z różnymi CC będą chciały jednocześnie korzystać z tego samego pasma, to pojawią się problemy z jakością dźwięku.

**Time Slot**, **TS** - Po polsku *Szczelina Czasowa*. DMR stosuje wielodostęp TDMA, i na każdej częstotliwości mogą odbywać się jednocześnie dwie łączności - na pierwszej i drugiej szczelinie czasowej. Na Wikipedii można znaleźć [artykuł wyjaśniający, czym jest TDMA](https://pl.wikipedia.org/wiki/TDMA).

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

Na początek pobieramy listę wszystkich DMR ID znanych sieci BrandMeister - możemy to zrobić [na tej stronie](https://brandmeister.network/?page=contactsexport). Następnie przetwarzamy pobrane kontakty tym skryptem:

```py
#!python
import csv

INPUT_FILENAME="bm_ce.csv"
OUTPUT_FILENAME="output.csv"
PRIVATE_CALL="Private Call"
GROUP_ALL="Group All"

hardcoded = [
    [0,"TG Poland","260",GROUP_ALL,"On","None"],
    [0,"TG SP6","2606",GROUP_ALL,"On","None"],
    [0,"TG DASR","26061",GROUP_ALL,"On","None"],
    [0,"SP Echo","260097",PRIVATE_CALL,"On","None"],
    [0,"TG WiresX","260080",GROUP_ALL,"On","None"],
    [0,"FusionPL","260042",GROUP_ALL,"On","None"],
    [0,"SP Test","260019",GROUP_ALL,"On","None"]
]

with open(INPUT_FILENAME, 'rb') as inputfile, open(OUTPUT_FILENAME, 'wb') as outputfile:
    reader = csv.reader(inputfile, delimiter=',', quotechar='"')
    writer = csv.writer(outputfile, delimiter=',')
    first = False
    counter = 1
    writer.writerow(["Number", "Name", "Call ID", "Type", "Ring Style", "Call Receive Tone"])
    for row in hardcoded:
        row[0] = counter
        writer.writerow(row)
        counter += 1
    for row in reader:
        if row[0].startswith("260") and int(row[4]) > 20:
            writer.writerow([counter, row[1], row[0], PRIVATE_CALL, "On", "None"])
            counter += 1
```

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
 * **Repeater Slot**: Szczelina czasowa.
 * **Private Call Confirmed**: Zaznaczone
 * **Data Call Confirmed**: Zaznaczone

### 5. Programowanie radia

Tutaj również nie ma niespodzianek. Wystarczy użyć przycisku Write <img style="width: 18px" alt="Ikonka przycisku z tooltipem WRITE" src="https://zsyp.fl9.eu/blog/dmr/write.png" />, a po chwili radio będzie zaprogramowane.

## Podsumowanie

To już w sumie wszystko, co musiałem zrobić żeby rozpocząć pracę z DMRem. Jako, że są to moje pierwsze kroki w tej sieci, chętnie dowiem się, o czym zapomniałem i czego nie zrobiłem - jeśli masz uwagi, proszę o kontakt, a zaktualizuję artykuł:

 * Mailowy: SP6MR (czyli mój znak wywoławczy) na fl9.eu
 * Mastodon: [@sp6mr@mastodon.radio](https://mastodon.radio/@sp6mr)
 * No i oczywiście przez radio!
