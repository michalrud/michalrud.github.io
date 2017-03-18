---
title: "BASH: Znajdowanie wszystkich dowiązań twardych do pliku w podanym drzewie katalogów"
---

Pytanie: Jak znaleźć wszystkie dowiązania twarde do tego samego pliku w podanym drzewie katalogów i coś z nimi zrobić (policzyć, zamienić na dowiązania symboliczne, usunąć, cokolwiek)? Należy użyć polecenia <code>find</code>. Pojawiają się jednak problemy:

 * Można w `find` użyć `-links +1`, aby znaleźć wszystkie pliki, których licznik dowiązań twardych ma wartość większą niż 1 (wiedząc, że każdy plik w \*niksie to tak naprawdę jest dowiązanie twarde do jakiegoś *inode* szukamy takich plików, przy których tych dowiązań twardych jest więcej niż jedno), ale to zwróci również pliki, do których dowiązania znajdują się **poza** wskazanym drzewem katalogów.
 * Można również znaleźć wszystkie takie pliki, a następnie wewnątrz pętli `while` wywołać kolejny raz `find` z parametrem `-samefile`. Jednak to powoduje przeszukanie całego drzewa katalogów wraz z każdą iteracją pętli, a więc jeśli mamy duże drzewo, to czas operacji może być naprawdę **długi**.

Rozwiązanie? Trzeba znaleźć wszystkie pliki regularne (`-type f`) do których istnieje ponad jedno dowiązanie twarde (`-links +1`). Wszystko dobrze, ale normalne wywołanie `find` w ten sposób zwróci nam wszystkei takie pliki w dość losowej kolejności. Do osiągnięcia naszego celu potrzebowaliśmy, aby były mniej-więcej posortowane - aby wszystkie dowiązania do jednego pliku były obok siebie. Jak to osiągnąć? `find` potrafi wypluwać dane nie tylko w prosty sposób, poprzez `-print`, ale również trochę bardziej potężny przy pomocy `-printf`. Wiemy również, że każde dowiązanie symboliczne wskazujące na ten sam plik musi mieć ustawiony ten sam numer *inode*. Tak więc, korzystając z `-printf "%i %p\n"` wypiszemy sobie wszystkie dowiązania wraz z ich numerami inode na przedzie, a następnie przy pomocy `sort` posortujemy tak, aby wszystkie dowiązania do jednego *inode* znalazły się obok siebie.

## Kod prezentuje się mniej więcej następująco:

```bash
#!/bin/bash
find $1 -type f -links +1 -printf &quot;%i %p\n&quot; | sort | while read a
do
    # w zmiennej $a mamy obecnie przetwarzaną linię
    inode=`echo $a | cut -d' ' -f1`
    path=`echo $a | cut -d' ' -f2`
done
```

Teraz, jeśli obecny `$inode` różni się od tego, który znajdował się w poprzedniej iteracji pętli, to możemy być pewni, że już więcej na dowiązanie do poprzedniego pliku nie natrafimy. Tak więc wystarczy coś wykombinować, np. zmienne globalne przechowujące ostatnio znaleziony inode, i wszystko gra.

## Dlaczego `while` a nie `for`?

Różnica jest prosta, ale ważna. W przypadku takiej pętli:

```bash
for i in *
do
    # coś robimy
done
```

cała zawartość `*` ląduje w wierszu poleceń. To się może wydawać drobnostką, ale jeśli jest tego **bardzo** dużo, to może się posypać - w końcu maksymalna długość polecenia w linii komend jest ograniczona. Spójrzmy teraz na konstrukcję z `while`:

```bash
find $1 -print | while read zmienna
do
    # coś robimy
done
```

Wygląda trochę bardziej skomplikowanie, ale zaleta jest znaczna. Mianowicie, tutaj wynik działania polecenia `find` nie ląduje w linii poleceń, ale idzie za pośrednictwem potoku do polecenia `while`, które przekazuje każdą otrzymaną w ten sposób linię do polecenia `read`. Wiemy, że potok może mieć praktycznie nieskończoną długość, więc tutaj problemu takiego, jak w przypadku `for` nie ma.

## Zobacz też:

 * [Strona podręcznika dla `find`](http://manpages.debian.net/cgi-bin/man.cgi?query=FIND&sektion=1&apropos=0&manpath=Debian+6.0+squeeze&locale=pl)
 * [Strona podręcznika dla `cut`](http://manpages.debian.net/cgi-bin/man.cgi?query=cut&apropos=0&sektion=1&manpath=Debian+6.0+squeeze&format=html&locale=pl)
 * [Strona podręcznika dla `sort`](http://manpages.debian.net/cgi-bin/man.cgi?query=sort&apropos=0&sektion=1&manpath=Debian+6.0+squeeze&format=html&locale=pl)