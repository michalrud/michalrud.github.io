---
title:  SDL Joystick
description: In English
---

*([polska wersja]({% link _posts/2010-12-13-sdl-joystick.markdown %}))*

SDL is a really nice library. It's multiplatform (and I *mean* it, it works almost everywhere, check it out) and easy to use. I had to write an app that uses a Joystick. At first I was thinking about doing it in a [Linux Way](http://archives.seul.org/linuxgames/Aug-1999/msg00107.html), but after a while looking around in the Internet I realized that SDL can not only make my work easier, but it will also make the code work everywhere (or at least in theory, I've never checked outside of Linux).

Code is posted below, but now I'll try do describe what's going on in there.

**Line 5** inits SDL. Instead of `SDL_INIT_EVERYTHING` we can use `SDL_INIT_JOYSTICK`, but events require a video subsystem, so I've decided to initialize everything.

In lines **8 and 9** we get number of joysticks available with `SDL_NumJoysticks()`, and then show the name of them using `SDL_JoystickName(i)`, where `i` is the number of joystick.

In **line 18** we enable the events pooling. What it's all about? It's rather simple. We can check the joystick *ghetto style*, with an infinite loop, updating the joystick state every iteration and doing what we want. This solution has some downsides:

 * You have to check events on the joystick by yourself
 * You have to manually update the Joystick status using `SDL_JoystickUpdate()`
 * We waste resources on doing nothing but checking if something happened

Much better solution is to use *events*. How does it work? We use an infinite loop as well, but in this case `SDL_WaitEvent()` our thread will wait until an event is received (allowing other apps in a multitasking environment do do their stuff), and then do something about it.

```cpp
#include <iostream>;
#include <SDL/SDL.h>;

int main(int argc, char **argv) {
    if (SDL_Init(SDL_INIT_EVERYTHING) == -1) {
      std::cerr < "Error while initialising SDL";
      return 1;
    }
    
    // Printing out names of joysticks that are available
    for (int i=0;i<SDL_NumJoysticks();i++) {
      std::cout < i < ": " < SDL_JoystickName(i) < std::endl;
    }
    
    // Let's open the joystick
    SDL_Joystick *joy = SDL_JoystickOpen(0);
    
    SDL_JoystickEventState(SDL_ENABLE);
    
    SDL_Event event;

    while (true) {
      SDL_WaitEvent(&amp;event);  // We wait for an event
      switch (event.type) {
        case SDL_JOYAXISMOTION:
          std::cout < "axis: " < event.jaxis.axis
            < " val: " < event.jaxis.value < std::endl;
        default:
          break;
      }
    }
    
    // Let's close the joystick
    if (SDL_JoystickOpened(0))
      SDL_JoystickClose(joy);
    return 0;
}
```