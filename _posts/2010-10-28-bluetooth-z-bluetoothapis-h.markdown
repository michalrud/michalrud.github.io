---
title:  Bluetooth z BluetoothAPIs.h
---

Nie ma tutaj wiele do gadania, obsługa Bluetooth z poziomu Windowsa jest *dość nieprzyjemna* dla osób, które wcześniej niewiele miały wspólnego z programowaniem dla tego systemu - w tym i dla mnie. Jeśli więc mamy dowolność w wyborze wykorzystywanej przez nas technologii, polecam [rodzinę .net](http://32feet.codeplex.com/) albo [Javę](http://bluecove.org/) - .net wypróbowałem i było łatwo i przyjemnie, ale Java kusi lepszą przenośnością.

Mój kod oparłem na [przykładowym kodzie znalezionym w internecie](http://www.dreamincode.net/code/snippet2051.htm). Dopisałem do niego co nieco, utknąłem przy próbie wysyłania czegokolwiek - gniazdko WinSock, które próbowałem tworzyć, było błędne, a ja nie miałem czasu prowadzić dochodzenia, czemu tak się dzieje ;) Jeśli wiesz, chętnie usłyszę w komentarzach i poprawię. Na razie jednak podzielę się tym, co mam.


Kod znajduje się na samym dole, podświetliłem tam linie, o których mówię. Na początku definiujemy kilka struktur, które są potrzebne do działania windowsowym funkcjom. Na ich temat nie ma się co rozpisywać, ponieważ wszystko znajdziemy w [dokumentacji Microsoftu](http://msdn.microsoft.com/en-us/library/aa362930(v=VS.85).aspx) (mała rada - jeśli zechce wam się sciągać ich przykładowy kod, to przestrzegam - i tak niewiele pomaga, przynajmniej mi nie pomógł).

Linia **62** - funkcja `BluetoothFindFirstRadio` robi dokładnie to, co mówi jej nazwa - znajduje pierwszy adapter w systemie i zwraca obiekt `HBLUETOOTH_RADIO_FIND`, będący czymś w rodzaju identyfikatora wyszukiwania adapterów. Jeśli nie ma żadnego adaptera w systemie, to funkcja zwraca `null`, co później sprawdzamy. W pętli zaczynającej się od linii **71** dodajemy znaleziony adapter do wektora adapterów. Do działania pętli wykorzystujemy fakt, że `BluetoothFindNextRadio` zwraca fałsz, jeśli nie znajdzie więcej adapterów, a jeśli znajdzie, to zwraca prawdę, a element, na który wskazuje drugi argument, zostaje wypełniony danymi o znalezionym adapterze - tak samo, jak w przypadku funkcji znajdującej pierwszy adapter.

W linii **91** ustawiamy w strukturze zawierającej konfigurację skanowania otoczenia to, którym adapterem chcemy skanować. Dalej sposób wyszukiwania urządzeń w pobliżu odbywa się w sposób analogiczny, co wyszukiwanie adapterów.

W linii **124** parujemy się z urządzeniem. Funkcja ta jest o tyle miła, że wyświetla Windowsowe okienko parowania, pytające o PIN i co tylko - tak więc nic poza tym nie musimy robić. Po zakończeniu tej funkcji telefon i komputer będzie sparowany… albo i nie. W każdym bądź razie, próbowaliśmy.

W linii **127** tworzymy nowe gniazdo WinSock, przystosowane do komunikacji poprzez Bluetooth. I tutaj zaczynają się schody - następujące po tym sprawdzanie poprawności socketa zawsze przerywa pracę programu. *Tutaj właśnie leży główny błąd w moim kodzie* Kilka linijek niżej widzimy ustawianie adresu urządzenia docelowego i próbę wysyłania danych w tamtym kierunku, jednak nie miałem okazji sprawdzić, na ile to jest poprawne.

Linie **152** i **155** zamykają wyszukiwanie adapterów i urządzeń w pobliżu. Nie zapominajmy pozostawić po sobie porządku ;) Nie jestem pewien, czy nie powinno się zamykać wyszukiwania *zaraz po dokonaniu owego przeszukiwania* - spróbujcie, jeśli nie spowoduje to błędu, to to byłby lepszy sposób. Ja nie spróbuję, bo jestem zbyt leniwy, żeby odpalać teraz Windowsa i sprawdzać.

```cpp
#include "stdafx.h"

#include <Ws2bth.h>
#include <BluetoothAPIs.h>
#include <Bthsdpdef.h>
#include <stdio.h>
#include <WinSock2.h>
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>
#include <bthdef.h>

#include <vector>

#pragma comment(lib, "Irprops.lib")

BLUETOOTH_FIND_RADIO_PARAMS m_bt_find_radio = {
  sizeof(BLUETOOTH_FIND_RADIO_PARAMS)
};

BLUETOOTH_RADIO_INFO m_bt_info = {
  sizeof(BLUETOOTH_RADIO_INFO),
  0,
};

BLUETOOTH_DEVICE_SEARCH_PARAMS m_search_params = {
  sizeof(BLUETOOTH_DEVICE_SEARCH_PARAMS),
  1,
  0,
  1,
  1,
  1,
  15,
  NULL
};

BLUETOOTH_DEVICE_INFO m_device_info = {
  sizeof(BLUETOOTH_DEVICE_INFO),
  0,
};

HANDLE m_radio = NULL;
HBLUETOOTH_RADIO_FIND m_bt = NULL;
HBLUETOOTH_DEVICE_FIND m_bt_dev = NULL;

// lista znalezionych adapterów
std::vector<HANDLE> radios;

// lista znalezionych urządzeń
std::vector<BLUETOOTH_DEVICE_INFO> devices;

int wmain(int argc, wchar_t **args) {
    unsigned int j=MAXINT; // numer urządzenia, które będziemy parować
    BLUETOOTH_DEVICE_INFO wybrane; // wybrane urządzenie, z którym chcemy się łączyć
    SOCKET sck;
    SOCKADDR_BTH sadr;
    char inc[20];

    wprintf(L"Szukanie adapterow Bluetooth...\n");

    // znajdujemy pierwszy adapter bluetooth
    m_bt = BluetoothFindFirstRadio(&amp;m_bt_find_radio, &amp;m_radio);

    // jeśli m_bt to null, to system nie znalazł żadnych adapterów Bluetooth.
    if (m_bt == NULL) {
        wprintf(L"Nie znaleziono adapterow Bluetooth");
        return 1;
    }

    // dodajemy znaleziony adapter do listy
    do {
        // dodajemy ten adapter do listy znalezionych adapterów
        radios.push_back(m_radio);
        // jeśli znaleźliśmy kolejny adapter, to również dodajmy go do listy
    } while (BluetoothFindNextRadio(&amp;m_bt_find_radio, &amp;m_radio));

    // Przestajemy szukać adapterów Bluetoot
    
    // wyświetlmy listę
    for (unsigned int i=0;i<radios.size();i++) {
        BluetoothGetRadioInfo(m_radio,&amp;m_bt_info);
        wprintf(L"Adapter nr. %d:\r\n", i);
        wprintf(L"\tNazwa:\t\t%s\r\n", m_bt_info.szName);
        wprintf(L"\tMAC\t\t%02x:%02x:%02x:%02x:%02x:%02x\r\n", m_bt_info.address.rgBytes[1], m_bt_info.address.rgBytes[0], m_bt_info.address.rgBytes[2], m_bt_info.address.rgBytes[3], m_bt_info.address.rgBytes[4], m_bt_info.address.rgBytes[5]);
        wprintf(L"\tKlasa:\t\t0x%08x\r\n", m_bt_info.ulClassofDevice);
        wprintf(L"\tProducent:\t0x%04x\r\n", m_bt_info.manufacturer);
    }

    // zakładamy na razie, że wybrany został zerowy adapter
    // ustawmy więc w opcjach wyszukiwania ten właśnie adapter
    m_search_params.hRadio = radios[0];

    // zaczynamy szukać urządzeń dostępnych poprzez bluetooth
    wprintf(L"\nSzukam urzadzen widocznych poprzez Bluetooth...\n\n");
    m_bt_dev = BluetoothFindFirstDevice(&amp;m_search_params, &amp;m_device_info);

    //BluetoothSelectDevices(*m_search_params);

    do {
        devices.push_back(m_device_info);
    } while(BluetoothFindNextDevice(m_bt_dev,&amp;m_device_info));

    // wyświetlmy te urządzenia
    for (unsigned int i=0;i<devices.size();i++) {
        m_device_info = devices[i];
        wprintf(L"Urzadzenie %d:\r\n", i);
        wprintf(L"\tNazwa:\t\t%s\r\n", m_device_info.szName);
        wprintf(L"\tMAC:\t\t%02x:%02x:%02x:%02x:%02x:%02x\r\n", m_device_info.Address.rgBytes[1], m_device_info.Address.rgBytes[0], m_device_info.Address.rgBytes[2], m_device_info.Address.rgBytes[3], m_device_info.Address.rgBytes[4], m_device_info.Address.rgBytes[5]);
        wprintf(L"\tKlasa:\t\t0x%08x\r\n", m_device_info.ulClassofDevice);
        wprintf(L"\tPolaczony:\t%s\r\n", m_device_info.fConnected ? L"tak" : L"nie");
        wprintf(L"\tParowany:\t%s\r\n", m_device_info.fAuthenticated ? L"tak" : L"nie");
        wprintf(L"\tPamietany:\t%s\r\n", m_device_info.fRemembered ? L"tak" : L"nie");
    }

    // wybierzmy urządzenie, z którym będziemy się parować
    do {
        printf("Z ktorym urzadzeniem parowac? (nr) ");
        scanf_s("%u",&amp;j); // pobieramy numer urządzenia do parowania
    } while (j > devices.size());

    wybrane = devices[j];

    // parujmy urządzenia
    BluetoothAuthenticateDevice(NULL,radios[0],&amp;wybrane,NULL,MITMProtectionNotRequired);

    // łączymy się z wybranym urządzeniem
    sck = socket(AF_BTH,SOCK_STREAM,BTHPROTO_RFCOMM);

    if (sck == INVALID_SOCKET) {
        printf("Blad przy tworzeniu gniazda");
        BluetoothFindDeviceClose(m_bt_dev);
        BluetoothFindRadioClose(m_bt);
        system("pause");
        return 2;
    }

    sadr.addressFamily = AF_BTH;
    sadr.btAddr = wybrane.Address.ullLong;

    // dokonujemy połączenia
    connect(sck,(SOCKADDR *) &amp;sadr,sizeof(sadr));

    send(sck,OBEX_CONNECT,sizeof(OBEX_CONNECT),0);
    recv(sck,&amp;(inc[0]),25,0);

    printf("%s",inc);

    // zamykamy gnizado
    closesocket(sck);

    // Przestajemy szukać urządzeń Bluetooth
    BluetoothFindDeviceClose(m_bt_dev);

    // Przestajemy szukać adapterów Bluetooth
    BluetoothFindRadioClose(m_bt);

    return 0;
}
```
