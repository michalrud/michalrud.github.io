---
title:  PXE w Fedorze, czyli stawiamy serwerek
---

Czasem musimy postawić system na maszynce, która nie ma napędu CD/DVD, bądź my nie mamy przy sobie zapisywalnego krążka. Możemy wówczas kombinować z pendrajwem, jednak ta opcja ma swoje wady - po pierwsze, musimy owy sprzęt posiadać, po drugie, jest to opcja powolna - popularne, tanie pendrajwy są powolne, no i starsze komputery mogą posiadać jedynie USB 1.1, co strasznie boli. Ale jest jeszcze jedna opcja - uruchomienie komputera przez sieć. Jak osobiście się o tym przekonałem, jest to procedura dość łatwa do osiągnięcia i przyjemna w wykorzystaniu - dość niezawodna, a prędkości osiągane są na tyle przyzwoite, żeby umożliwić komfortową instalację. Opiszę, jak wszystko to działa na Fedorze 12, jednak i na innych wersjach/dystrybucjach powinno być podobnie. Instalował będę xubuntu 9.10. No więc, bez zbędnego smęcenia - zaczynamy.

## Zdobywamy narzędzia

Potrzebny nam będzie serwer DHCP i tftp. Instalujemy więc odpowiednie pakiety, pod Fedorką noszące nazwy `tftp-server dhcp`

## Przygotowujemy miejsce pracy

Najpierw skonfigurujmy serwer DHCP. W pliku `/etc/dhcp/dhcpd.conf` wstawiamy coś na kształt tego:

```
allow booting;
allow bootp;
ddns-update-style interim;
ignore client-updates;

subnet 192.168.1.0 netmask 255.255.255.0 {
      option subnet-mask 255.255.255.0;
      option broadcast-address 192.168.1.255;
      range dynamic-bootp 192.168.1.200 192.168.1.240;
      next-server 192.168.1.10;
      filename "pxelinux.0";
}
```

Oczywiście, `subnet` i `netmask` ustawiamy odpowiednio do naszej sieci. Dane te można łatwo wyciągnąć graficznie z NetworkManagera, klikając prawym przyciskiem na ikonce i wybierając Informacje o połączeniu. Oczywiście, można też użyć ifconfiga. `range dynamic-bootp` ustala zakres adresów, jakie serwer przydzieli, a `next-server` należy ustawić na adres IP komputera, na którym uruchamiamy serwer tftp.

Kiedy już mamy DHCP skonfigurowane, przychodzi pora na konfigurację TFTP. Plik konfiguracyjny znajdziemy pod `/etc/xinet.d/tftp`. W zasadzie domyślna konfiguracja jest w porządku. W razie czego, oto mój plik konfiguracyjny:

```
service tftp
{
        disable = no
        socket_type             = dgram
        protocol                = udp
        wait                    = yes
        user                    = root
        server                  = /usr/sbin/in.tftpd
        server_args             = -s /var/lib/tftpboot
        per_source              = 11
        cps                     = 100 2
        flags                   = IPv4
}
```

## Wrzucanie plików tam, gdzie trzeba

Tak więc, bierzesz plik .iso z instalką systemu/livecd/czymkolwiek i rozpakowujesz jego zawartość. Można to zrobić na wiele sposobów, ja jestem leniem i użyłem opcji "Rozpakuj tutaj" w menu kontekstowym Gnome. Tylko uwaga, mogą tam się znaleźć ukryte pliki, którymi również się zajmujemy, jak np. katalog `.disk` na płytce z *ubuntu. Zawartość tego kopiujemy do katalogu `/var/lib/tftpboot`, a następnie z katalogu `install/netboot` przerzucamy katalog `ubuntu-installer` do katalogu `/var/lib/tftpboot`. Wewnątrz `ubuntu-installer/architektura_kompa` znajdziemy ciekawe pliczki, jak `vmlinuz`, `initrd.gz` i `pxelinux.0` (ten ostatni również jest w `netboot`, jednak tam ma podejrzanie zerowy rozmiar, z braku chęci nie testowałem, jak to się wszystko zachowa). Kopiujemy je do katalogu `/var/lib/tftpboot`, i to w sumie powinno być tyle, o ile nie używamy...

## SELinux i TFTP

Jeśli mamy SELinuksa załączonego, to bez tego kroku będzie z nami krucho - żaden plik nie będzie chciał się przesłać, wywalając Forbiddena. W internecie polecają wyłączenie SELinuksa całkowicie, ale jeśli w niczym poza tym nam nie przeszkadza, to nie ma sensu go wywalać - ponowne nadanie kontekstu plikom wystarczy. Będąc w katalogu `/var/lib` wydajemy polecenie

```bash
restorecon -Rv tftpboot/
```


## Uruchamiamy wehikuł

Teraz musimy odpalić serwer DHCP i TFTP. Robimy to poleceniami:

```bash
$ service dhcpd start
$ service xinetd start
$ chkconfig tftp on
```

Jeśli podczas odpalania dhcp pojawił się błąd, to znaczy, że popełniliśmy gdzieś błąd w jego pliku konfiguracyjnym - sprawdź, czy aby na pewno wprowadzone tam wartości odpowiadają tym, z jakim jest skonfigurowane aktywne połączenie sieciowe. Jeśli wszystko poszło dobrze, odpalamy kompa, na którym będziemy przeprowadzać instalację, w BIOSie ustawiamy odpowiedni priorytet dla urządzeń startowych (tak, aby PXE było wyżej niż dysk) i wszystko powinno śmigać.

## Źródła

Informacje, z jakich korzystałem znajdują się w [dokumentacji Fedory](http://docs.fedoraproject.org/install-guide/f7/en_US/ap-pxe-server.html) i [ich liście mailingowej](http://www.redhat.com/archives/fedora-selinux-list/2007-May/msg00022.html).

## Post Scriptum

Kilka dodatkowych porad:

 * Jeśli nie macie katalogu netboot na płytce, to wszystkie potrzebne pliki znajdziecie [na serwerach Ubuntu](http://archive.ubuntu.com/ubuntu/dists/jaunty/main/installer-i386/current/images/netboot/), najwygodniej pobrać spakowane archiwum netbook.tar.gz i rozpakować w tftpboot.
 * Aby w trakcie instalacji instalator był w stanie korzystać z internetu, musimy wyłączyć serwer DHCP na drugim komputerze i pozwolić działać temu na routerze. Można to bezpiecznie zrobić, jak system już się uruchomi.
