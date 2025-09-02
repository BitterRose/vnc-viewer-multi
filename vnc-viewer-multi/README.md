# VNC Viewer Multi

![Addon Stage][stage-badge]
![Supports aarch64 Architecture][aarch64-badge]
![Supports amd64 Architecture][amd64-badge]
![Supports armhf Architecture][armhf-badge]
![Supports armv7 Architecture][armv7-badge]
![Supports i386 Architecture][i386-badge]

Zaawansowany add-on do Home Assistant umożliwiający jednoczesny dostęp do wielu serwerów VNC. Każdy serwer VNC jest dostępny na osobnym porcie z pełnym interfejsem noVNC. Addon wykorzystuje zmodyfikowaną wersję noVNC z ulepszoną obsługą połączeń HTTP.

## Funkcje

- 🖥️ **Obsługa wielu serwerów VNC** - Do 10 serwerów jednocześnie (porty 6080-6089)
- 🌐 **Bezpośredni dostęp** - Każdy serwer na własnym porcie bez dodatkowego interfejsu
- 🔐 **Automatyczne hasła** - Wstrzykiwanie haseł bezpośrednio do interfejsu noVNC
- 📱 **Zmodyfikowany noVNC** - Fork z poprawkami dla połączeń HTTP
- 🔄 **Automatyczne monitorowanie** - Restartowanie proxy w przypadku awarii
- ⚡ **Wysoka wydajność** - Bezpośrednie połączenia proxy bez dodatkowych warstw
- 🔧 **Elastyczne porty** - Obsługa portów VNC 1-65535

## Instalacja

1. Dodaj to repozytorium do Home Assistant Add-on Store
2. Znajdź "VNC Viewer Multi" i kliknij Install
3. Skonfiguruj serwery VNC w zakładce Configuration
4. Uruchom add-on

## Przykład konfiguracji

```yaml
vnc_servers:
  - name: "Windows Desktop"
    host: 192.168.1.50
    port: 5900
    proxy_port: 6080
    password: "windows123"
    
  - name: "Linux Server"
    host: 192.168.1.51
    port: 5901
    proxy_port: 6081
    
  - name: "MacBook"
    host: 192.168.1.52
    port: 5900
    proxy_port: 6082
    password: "mac456"
```

## Dostęp do serwerów

Po konfiguracji każdy serwer będzie dostępny pod adresem:

### Pełny interfejs noVNC:
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc.html
```

### Lekki interfejs noVNC:
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc_lite.html
```

### Auto-login (dla serwerów z hasłem):
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc_auto.html
```

### Przykłady URL:
- Windows Desktop: `http://192.168.1.100:6080/vnc_auto.html` (automatyczne logowanie)
- Linux Server: `http://192.168.1.100:6081/vnc_lite.html`
- MacBook: `http://192.168.1.100:6082/vnc_lite.html?password=mac456&insecure=true`

## Parametry konfiguracji

### `vnc_servers` (Wymagany)

Lista serwerów VNC do skonfigurowania. Każdy serwer wymaga:

- **`name`** (string, wymagany) - Przyjazna nazwa serwera
- **`host`** (string, wymagany) - IP lub hostname serwera VNC
- **`port`** (integer, opcjonalny) - Port serwera VNC (domyślnie: 5900)  
- **`proxy_port`** (integer, wymagany) - Port proxy (6080-6089, musi być unikalny)
- **`password`** (string, opcjonalny) - Hasło VNC (jeśli wymagane)

## Parametry URL

Możesz dodać parametry do URL dla automatycznej konfiguracji:

```
http://IP:PORT/vnc.html?autoconnect=true&resize=scale&quality=6
```

**Dostępne parametry:**
- `autoconnect=true` - Automatyczne łączenie po załadowaniu
- `resize=scale|remote` - Tryb skalowania ekranu
- `quality=0-9` - Jakość kompresji (0=najlepsza jakość, 9=najszybsza)

## Funkcje noVNC

- **Automatyczne logowanie** - vnc_auto.html przekierowuje z hasłem
- **Lekki interfejs** - vnc_lite.html dla podstawowych funkcji  
- **Pełny interfejs** - vnc.html z wszystkimi opcjami
- **Parametry URL** - dostosowywanie przez adres www
- **Obsługa HTTP** - zmodyfikowany noVNC bez wymogu HTTPS

## Porty

Add-on wykorzystuje porty **6080-6089** dla proxy serwerów VNC. Upewnij się, że te porty nie są używane przez inne usługi.

## Rozwiązywanie problemów

### Nie mogę się połączyć z proxy
1. Sprawdź logi add-on-a
2. Upewnij się, że port nie jest zajęty
3. Sprawdź czy firewall nie blokuje portu

### Proxy się restartuje
1. Sprawdź dostępność serwera VNC z Home Assistant
2. Sprawdź logi pod kątem błędów sieciowych  
3. Upewnij się, że parametry host:port są poprawne

### Problemy z wydajnością
1. Zmniejsz jakość kompresji (`?quality=9`)
2. Sprawdź obciążenie CPU Home Assistant
3. Użyj przewodowego połączenia sieciowego

## Bezpieczeństwo

- Hasła są bezpiecznie wstrzykiwane do interfejsu
- Proxy działa lokalnie w sieci Home Assistant
- Tylko skonfigurowane porty są otwarte
- Automatyczne monitorowanie wykrywa problemy

## Przykład zaawansowanej konfiguracji

```yaml
vnc_servers:
  - name: "Gaming PC"
    host: 192.168.1.100
    port: 5900
    proxy_port: 6080
    password: "gaming2023"
    
  - name: "Work Laptop"
    host: 192.168.1.101
    port: 5900
    proxy_port: 6081
    
  - name: "Home Server"
    host: 192.168.1.10
    port: 5901
    proxy_port: 6082
    password: "server_secure"
    
  - name: "Raspberry Pi"
    host: 192.168.1.200
    port: 5900
    proxy_port: 6083
```

Po tej konfiguracji będziesz mieć:
- Gaming PC: `http://192.168.1.5:6080/vnc_auto.html` (auto-login)
- Work Laptop: `http://192.168.1.5:6081/vnc_lite.html`  
- Home Server: `http://192.168.1.5:6082/vnc_auto.html` (auto-login)
- Raspberry Pi: `http://192.168.1.5:6083/vnc_lite.html`

[stage-badge]: https://img.shields.io/badge/Addon%20stage-stable-green.svg
[aarch64-badge]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-badge]: https://img.shields.io/badge/amd64-yes-green.svg
[armhf-badge]: https://img.shields.io/badge/armhf-yes-green.svg
[armv7-badge]: https://img.shields.io/badge/armv7-yes-green.svg
[i386-badge]: https://img.shields.io/badge/i386-yes-green.svg