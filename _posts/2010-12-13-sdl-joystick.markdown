---
title:  SDL Joystick
description: Po polsku
---

*([in english]({% link _posts/2010-12-13-sdl-joystick-en.markdown %}))*

SDL jest dość przyjemną biblioteką. Jest wieloplatformowa (i to *naprawdę* wieloplatformowa, [obsługuje](http://gpf.dcemu.co.uk/ndsSDL.shtml) [mnóstwo](http://koti.mbnet.fi/mertama/sdl.html) [dziwnych](http://code.google.com/p/iphone-sdl-1-3/) [platform](http://jiggawatt.org/badc0de/android/index.html) jak i te normalniejsze, oszczędzając programiście wiele pracy) i prosta w obsłudze. Za zadanie miałem napisać obsługę joystika - wypisanie listy dostępnych urządzeń tego typu w systemie i obsłużenie joystika. Na początku myślałem o zrobieniu tego [w typowo linuksowy sposób](http://archives.seul.org/linuxgames/Aug-1999/msg00107.html), jednak po chwili w internecie okazało się, że SDL nie tylko potrafi ułatwić mi pracę, ale jeszcze sprawi, że kod będzie (przynajmniej w założeniu, nie testowałem poza Linuksem) działał wszędzie.

Kod znajduje się poniżej, a tutaj ja postaram się opisać po kolei, co się dzieje.

**Linia 5** inicjalizuje SDL. Można zamiast `SDL_INIT_EVERYTHING` użyć `SDL_INIT_JOYSTICK`, jednak obsługa zdarzeń wymaga inicjalizacji podsystemu wideo, a więc postanowiłem inicjalizować wszystko.

W liniach **8 i 9** pobieramy liczbę joystików w systemie za pomocą funkcji `SDL_NumJoysticks()`, a następnie wyświetlamy nazwę każdego za pomocą funkcji `SDL_JoystickName(i)`, gdzie parametrem jest numer joystika. Linii tej nie podświetlałem, bo w sumie nie ma tam nic ciekawego.


W **linii 18** włączamy tryb zdarzeń w obsłudze joystika. O co chodzi? Sprawa jest w sumie prosta. Normalnie można joystick obsłużyć *na chama*, wywołując nieskończoną pętlę, w każdej iteracji aktualizując stan joystika i robiąc z tym co trzeba. Takie rozwiązanie ma jednak kilka wad:

 * Trzeba samemu sprawdzać, czy coś się wydarzyło na joystiku
 * Trzeba samodzielnie odświeżać stan joystika za pomocą funkcji `SDL_JoystickUpdate()`
 * Marnujemy zasoby komputera, nie robiąc nic poza sprawdzaniem, czy coś nowego się nie wydarzyło

O wiele lepszym rozwiązaniem jest system oparty na *Zdarzeniach* (ang. *Event*). Jak to działa? Również możemy wykorzystać nieskończoną pętlę (chociaż możemy też sprawdzać, czy mamy jeszcze jakieś zdarzenie do obsłużenia, to nie będziemy się tym tutaj zajmować). Róznica polega jednak na tym, że w tym wypadku wywołanie funkcji `SDL_WaitEvent()` powoduje uśpienie programu do momentu otrzymania zdarzenia. Po otrzymaniu zdarzenia (którego źródłem może być nie tylko joystick, ale i np. klawiatura, ale my włączyliśmy jedynie joystick) za pomocą switcha obsługujemy zdarzenie - w tym wypadku jedynie ruch osi joystika. W podobny sposób można obsłużyć [przyciski na joysticku](http://www.libsdl.org/docs/html/sdljoybuttonevent.html), jednak jako że urządzenie, którym dysponuję przycisków nie posiada, to tego nie zaimplementowałem.

```cpp
#include <iostream>;
#include <SDL/SDL.h>;

int main(int argc, char **argv) {
    if (SDL_Init(SDL_INIT_EVERYTHING) == -1) {
      std::cerr << "SDL nie inicjalizuje się";
      return 1;
    }
    
    // Wypisujemy nazwy wszystkich joystików w systemie
    for (int i=0;i<SDL_NumJoysticks();i++) {
      std::cout << i << ": " << SDL_JoystickName(i) << std::endl;
    }
    
    // Otwieramy joystick
    SDL_Joystick *joy = SDL_JoystickOpen(0);
    
    SDL_JoystickEventState(SDL_ENABLE);
    
    SDL_Event event;

    while (true) {
      SDL_WaitEvent(&amp;event);  // Czekamy na zdarzenie od joystika
      switch (event.type) {
        case SDL_JOYAXISMOTION:  // Ruch osią joystika
          std::cout << "axis: " << event.jaxis.axis
            << " val: " << event.jaxis.value << std::endl;
        default:
          break;
      }
    }
    
    // Zamykamy joystick
    if (SDL_JoystickOpened(0))
      SDL_JoystickClose(joy);
    return 0;
}
```

Jak widać, sprawa jest bardzo prosta - pozostaje jedynie dorobienie praktycznej funkcjonalności do naszego programu. Więcej informacji o obsłudze joystika można znaleźć w [dokumentacji SDL](http://www.libsdl.org/docs/html/joystick.html), tam też znajdują się przykładowe kody źródłowe. Oczywiście, obsługę zdarzeń najlepiej byłoby wykonać na osobnym wątku, aby nasza aplikacja nie usypiała się całkowicie co chwilę, ale to również wybiega poza ramy tego, co chciałem tutaj przedstawić.