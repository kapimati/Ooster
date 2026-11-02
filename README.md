[README.md](https://github.com/user-attachments/files/34276654/README.md)
# Ooster

Ooster to eksperymentalna, niezależna sesja Wayland dla Arch Linux: KWin jako compositor, Rust + Qt/QML jako własna powłoka.
Nie jest to pełny zamiennik dojrzałego środowiska graficznego — część funkcji (zasobnik, wskaźniki aktywnych okien, powiadomienia i globalne menu aplikacji) pozostaje na liście rozwoju.

## Funkcje

- Górny pasek z zegarem, menu sesji i przyciskiem „Aplikacje”.
- Launchpad z listą aplikacji z plików `.desktop`, wyszukiwaniem po nazwie, uruchamianiem i obsługą Escape/Enter.
- Dock z ikonami aplikacji z plików `.desktop`, podpowiedziami i animacją powiększania.
- Menu sesji: uśpienie, restart, wyłączenie i wylogowanie.
- Panele Wayland przez `layer-shell-qt`; awaryjny tryb zwykłych okien, gdy warstwa layer-shell nie jest dostępna.
- Uruchamianie aplikacji bez powłoki systemowej; parser `Exec` obsługuje podstawowe cytowanie i usuwa standardowe kody pól plików/URI.
- Filtrowanie ukrytych aplikacji, wpisów terminalowych i nieobecnych `TryExec`; preferowana nazwa polska, gdy jest zdefiniowana.

## Instalacja na Arch Linux

W katalogu projektu:

```bash
sudo pacman -S --needed base-devel rustup qt6-base qt6-declarative qt6-wayland kwin layer-shell-qt plasma-integration breeze-icons dbus
rustup default stable
makepkg -si
```

Wyloguj się i wybierz **Ooster** z menu sesji ekranu logowania. Zachowaj GNOME jako sesję zapasową.

## Testowanie

W działającej sesji Wayland można uruchomić testowo:

```bash
cargo test --locked
cargo run
```

`cargo run` uruchamia powłokę obok istniejącego pulpitu; nie testuje pełnej sesji KWin. Nie należy używać poleceń z menu zasilania do testów, jeśli nie chcesz uśpić lub wyłączyć komputera.

## Znane ograniczenia

- Lista ulubionych jest obecnie wbudowana w kod; brakujące aplikacje są dobierane z katalogów `.desktop`.
- Nie ma jeszcze konfiguratora ulubionych, zasobnika StatusNotifier, wskaźników aktywnych okien ani systemu powiadomień. Launchpad obecnie skanuje główne katalogi `applications` (bez zagnieżdżonych podkatalogów).
- Kompatybilność layer-shell zależy od wersji Qt/KWin i wymaga sprawdzenia w docelowej sesji.
- Zmiany źródeł trzeba skompilować na docelowym Arch Linux; środowisko przygotowania archiwum nie ma toolchaina Rust ani Qt, więc tutaj nie można potwierdzić kompilacji.


## Poprawka v0.1.0-5

- Naprawiono przekazywanie sygnału `launcherClicked` z paska do okna sesji.
- Kliknięcie „Aplikacje” otwiera teraz Launchpad i odświeża listę aplikacji.
- Dodano logowanie prób uruchomienia aplikacji i błędów `Exec` do standardowego wyjścia sesji.

Po przebudowaniu pakietu sprawdź logi sesji, jeśli aplikacja nadal nie startuje.


## Diagnostyka kliknięć i uruchamiania aplikacji (wydanie 0.1.0-6)

- Komponenty okien są ładowane z URL rozwiązanym względem `main.qml`, w trybie preferującym synchroniczną kompilację.
- Logi rozróżniają kliknięcie przycisku paska, otwarcie Launchpada i próbę uruchomienia programu.
- Backend zwraca status powodzenia `launch`, a Launchpad pokazuje komunikat, jeśli proces nie wystartuje.
- `TryExec` z argumentami sprawdza tylko nazwę programu, zamiast błędnie traktować całe pole jako ścieżkę.

Po uruchomieniu sesji diagnostykę można odczytać z logów użytkownika (`journalctl --user -b`).
