---
title:  Notowanie na komputerze
description: Używanie vim+vimwiki do notowania na wykładach
---

Używasz komputera do notowania wykładów lub sporządzania notatek ze spotkań? Ten tekst może Cię zainteresować. Odkryłem ostatnio dość prosty sposób na to, aby za pomocą Vima naprawdę wygodnie tworzyć swoje wiki, wraz z odnośnikami pomiędzy stronami, formatowaniem, i eksportem do HTML. I jeszcze coś dla osób, który słysząc "vim" powoli sięgają po myszkę, aby zamknąć kartę: *To nie jest trudne!* Opowiem zaraz, od początku do końca, jak się z tego korzysta. Dodatkowo proponuję używać narzędzia gvim, w paczce dla windowsa dostępne domyslnie, w Linuksie zwykle dostępne w repozytoriach, które jest graficzną wersją vima, posiadającą menu, pasek narzędzi i generalnie ułatwiającą start.

## Instalacja

[Vimwiki](http://code.google.com/p/vimwiki/) jest dostępny jako tzw. vimball, ja jednak preferuję zwykłe rozpakowanie archiwum w moim `~/.vim`. Pobieramy archiwum `vimwiki-x.zip` ze [strony pobierania vimwik](http://code.google.com/p/vimwiki/downloads/list) i rozpakowujemy w `~/.vim`. I już po instalacji.

## Konfiguracja

Domyślna konfiguracja jest dobra i teoretycznie nie trzeba nic zmieniać, ja jednak chciałem, żeby pliki z wiki były zapisywane w moim katalogu Dropboksa. Otwieramy więc plik `~/.vimrc` i dodajemy następującą linię:

```
let g:vimwiki_list = [{'path': '~/Dropbox/wiki/', 'path_html': '~/Dropbox/Public/wiki/'}]
```

Co oznaczają poszczególne elementy?

 * **path** - ścieżka do plików wiki
 * **path_html** - ścieżka do katalogu, gdzie mają pojawić się wygenerowane pliki html

## Krótki kurs vimowania

Otwieramy `gvim`, upewniamy sie, że jesteśmy w trybie normalnym, a nie trybie wprowadzania. Powinniśmy widzieć <a href="http://zsyp.fl9.eu/blog/2010/10/vim-normal.png">coś takiego</a>. Dla porównania, vim w trybie wprowadzania wygląda <a href="http://zsyp.fl9.eu/blog/2010/10/vim-input.png">w ten sposób.</a> Różnica jest subtelna, ale ważna. W trybie normalnym wprowadzane są komendy, a w trybie wprowadzania piszemy tekst - najprawdopodobniej najwięcej czasu będziesz spędzał w trybue wprowadzania, ponieważ wtedy vim zachowuje się jak zwykły edytor tekstu. Oto, co powinieneś/powinnaś wiedzieć o zabawie w vimie:

 * Przechodzisz do trybu wpisywania naciskając klawisz `i` na klawiaturze. Małe `i`. Spowoduje to edycję tekstu od znaku, na którym obecnie znajduje się kursor. Zamiast `i` możesz wcisnąć `a`, a rozpoczniesz edycję za znakiem, na którym znajduje się kursor. Jeśli bardzo chcesz, to możesz nacisnąć duże litery - `I` rozpocznie edycję na początku linii, a `A` rozpocznie edycję na końcu linii, ale myślę, że na razie nie musisz zaprzątać sobie tym głowy.
 * Wychodzisz z trybu wprowadzania do trybu normalnego klawiszem `ESC`.
 * W trybie normalnym musisz znać dwie komendy: wyjście i zapisanie pliku. Komendę poprzedzasz dwukropkiem, czyli znakiem `:`. Tak więc, w trybie normalnym:
    - `:w` (jak <strong>w</strong>rite) zapisuje plik
    - `:q` (jak <strong>q</strong>uit) wychodzi z programu - jeśli chcemy wyjść bez zapisywania, należy wymusić to poprzez wykrzyknik, czyli wpisać `:q!`

Posiadając te informacje jesteśmy już w stanie wygodnie pisać w vimie. Polecić mogę jeszcze tylko <a href="http://jakilinux.org/programy/vim/">prostą sciągawkę z vima</a> i <a href="http://wiki.fl9.eu/stuff/config/vimrc">mój plik konfiguracyjny vimrc</a>, dzięki któremu można bez problemu używać myszki w gvimie, mieć podświetlanie składni itp.

## Zaczynamy notować

Będąc w trybie normalnym wpisz `\ww`, aby przejść do strony głównej swojego nowego wiki. Teraz naciśnij `i`, aby przejść do trybu wpisywania, i piszesz. Wypróbujmy więc formatowanie owego wiki - spróbuj przepisać to, co ja poniżej:

<a href="http://blog.fl9.eu/2010/10/23/notowanie-na-komputerze/vimwiki-syntax/" rel="attachment wp-att-1179"><img src="http://zsyp.fl9.eu/blog/2010/10/vimwiki-syntax.png" alt="" title="vimwiki-syntax" width="580" height="524" class="aligncenter size-full wp-image-1179" /></a><br />

Wynik tego kodu wygląda <a href="http://dl.dropbox.com/u/255644/wiki/syntax.html">w ten sposób</a>. Jeśli nie widzisz kolorowania składni, upewnij się, czy w pliku `.vimrc` masz <a href="http://code.google.com/p/vimwiki/wiki/Prerequisites">wszystkie wymagane ustawienia</a>. Kolejny raz polecam <a href="http://wiki.fl9.eu/stuff/config/vimrc">mój plik `.vimrc`</a>. Po skończonej edycji zapisz plik naciskając `ESC` i wpisując `:w`, potwierdzając polecenie klawiszem `Enter`.

Najważniejszą funkcją w wiki są odnośniki pomiędzy stronami. My tworzymy je wpisując `[[Tytuł strony]]`, albo po prostu wpisując coś przy użyciu <a href="http://pl.wikipedia.org/wiki/CamelCase">CamelCase</a>, czyli np `TytułStrony`. Zauważysz, że po wpisaniu takiego odnośnika zmieni kolor na czerwony. Oznacza to, że strona docelowa nie została jeszcze utworzona. Klawiszem `ESC` wyjdź z trybu wpisywania do trybu normalnego, najedź kursorem na odnośnik i naciśnij `Enter`. Bum, przeniosło Cię na nową, pustą stronę! Powpisuj coś i zapisz ją. Jak teraz wrócić do strony głównej wiki? Będąc w trybie normalnym naciśnij `Backspace`. Zauważysz pewnie, że kolor odnośnika zmienił się z czerwonego na inny, informując Cię, że dana strona istnieje.

## Eksport wiki do HTML

Chociaż eksport do HTML jest zbędny - wszystkie nasze informacje są zapisane w wygodnych do odczytu plikach tekstowych - czasem chcielibyśmy podzielić się naszymi notatkami z innymi. W tym celu możemy skorzystać z dwóch komend generujących pliki html:

 * `:VimwikiAll2HTML` - eksportuje wszystkie zapisane strony wiki do HTML
 * `:Vimwiki2HTML` - eksportuje obecną stronę do HTML (pamiętaj o zapisaniu zmian!)

Pliki te są zapisywane są w katalogu, który ustawiliśmy w `vimrc` pod hasłem `path_html`.

Mam nadzieję, że ten poradnik trochę przekona was do vima. Jest to bardzo wygodny edytor tekstu, i choć na początek wydaje się dość skomplikowany, to są to tylko pozory. Szczerze mówiąc, ten wpis został napisany w vimie, ponieważ jest to po prostu o wiele wygodniejsze, niż pisanie w edytorze wbudowanym w przeglądarkę. Nie opisałem tutaj wszystkich możliwości Vimwiki, a jedynie podstawy. Można, przykładowo, za jego pomocą prowadzić dziennik.
