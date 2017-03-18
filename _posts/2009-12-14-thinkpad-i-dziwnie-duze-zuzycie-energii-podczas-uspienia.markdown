---
title:  ThinkPad i dziwnie duże zużycie energii podczas uśpienia
---

Unikałem do tej pory uśpienia na dłuższy czas, bo zawsze zżerało mi to całkiem pokaźną ilość energii - przez noc potrafiło uszczuplić stan naładowania nawet o 30%! Pogadałem jednak z ludźmi i u nich w laptopie uśpienie zabiera dość marginalną ilość energii. Usiadłem więc kiedyś, i po krótkim zastanowieniu znalazłem sprawcę - urządzenie w kieszonce UltraBay, obok którego świeci się dioda sygnalizująca zasilanie. Spróbujmy więc sprawić, aby laptop automatycznie odłączał zasilanie od napędu, gdy położymy go spać. Potrzebujemy pliku o nazwie `hal-system-power-suspend`, który w większości dystrybucji zawiera polecenia, jakie system ma wykonać przed uśnięciem. Szukamy go za pomocą narzędzia `locate`:

```
[michalek@michal-laptop scripts]$ locate hal-system-power-suspend
/usr/libexec/scripts/hal-system-power-suspend
/usr/libexec/scripts/hal-system-power-suspend-hybrid
/usr/libexec/scripts/linux/hal-system-power-suspend-hybrid-linux
/usr/libexec/scripts/linux/hal-system-power-suspend-linux
```

Ja wziąłem pierwszy z brzegu, tzn ten w `/usr/libexec/scripts`. Teraz, musimy tam wstawić kod odpowiedzialny za "wysuwanie" urządzenia w UltraBayu, a właściwie część odpowiedzialną za odłączanie zasilania. Wklejamy więc do środka, co następuje:

```bash
dock=$( /bin/grep -l ata_bay /sys/devices/platform/dock.?/type )
dock=${dock%%/type}
echo 1 > $dock/undock
```

Cały działający plik wygląda u mnie tak:

```bash
#!/bin/sh

. hal-functions

dock=$( /bin/grep -l ata_bay /sys/devices/platform/dock.?/type )
dock=${dock%%/type}
echo 1 > $dock/undock

hal_check_priv org.freedesktop.hal.power-management.suspend
hal_exec_backend
```

I nagle zużycie prądu podczas 7-godzinnego snu spada do jakiś 5%. Nadal za dużo? Daj znać w komentarzach, bo nie wiem, czy dalej kombinować z odłączaniem dziwnych elementów do snu. Ile Twój laptop zżera baterii podczas snu?
