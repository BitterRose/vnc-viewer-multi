# VNC Viewer Multi Proxy - Dokumentacja

## Opis

Ten addon tworzy osobne proxy dla każdego skonfigurowanego serwera VNC, umożliwiając bezpośredni dostęp do każdego serwera na osobnym porcie bez dodatkowego interfejsu graficznego.

## Konfiguracja

Przykład konfiguracji z wieloma serwerami VNC:

```yaml
vnc_servers:
  - name: "Komputer biurowy"
    host: 192.168.0.23
    port: 5900
    proxy_port: 6080
    password: "mojhaslo123"
  - name: "Serwer produkcyjny"
    host: 10.0.0.15
    port: 5901
    proxy_port: 6081
  - name: "Raspberry Pi"
    host: 192.168.0.45
    port: 5900
    proxy_port: 6082
    password: "raspberry"
```

## Parametry konfiguracji

### `vnc_servers` (Wymagany)

Lista serwerów VNC. Każdy serwer powinien mieć następujące parametry:

#### `name` (Wymagany)
- **Typ**: string
- **Opis**: Nazwa serwera do identyfikacji w logach

#### `host` (Wymagany)  
- **Typ**: string
- **Opis**: Adres IP lub nazwa hosta serwera VNC

#### `port` (Opcjonalny)
- **Typ**: integer (5900-65535)
- **Domyślnie**: 5900
- **Opis**: Port na którym działa serwer VNC

#### `proxy_port` (Wymagany)
- **Typ**: integer (6080-6089)
- **Opis**: Port na którym będzie dostępny proxy dla tego serwera VNC
- **Uwaga**: Każdy serwer musi mieć unikalny proxy_port

#### `password` (Opcjonalny)
- **Typ**: string  
- **Opis**: Hasło do serwera VNC. Jeśli nie zostanie ustawione, będzie pytać o hasło podczas łączenia

## Dostępne porty proxy

Addon rezerwuje porty 6080-6089 dla proxy serwerów VNC. Możesz skonfigurować maksymalnie 10 serwerów VNC.

## Użytkowanie

Po skonfigurowaniu addon-u, każdy serwer VNC będzie dostępny bezpośrednio pod adresem:

### Pełny interfejs noVNC:
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc.html
```

### Lekki interfejs noVNC:
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc_lite.html
```

### Przykłady:
Jeśli Twój Home Assistant ma IP `192.168.1.100` i skonfigurowałeś serwer z `proxy_port: 6081`:

- **Pełny interfejs**: `http://192.168.1.100:6081/vnc.html`
- **Lekki interfejs**: `http://192.168.1.100:6081/vnc_lite.html`

## Parametry URL (opcjonalne)

Możesz dodać parametry do URL-a dla automatycznej konfiguracji:

```
http://IP:PORT/vnc.html?autoconnect=true&resize=scale&quality=6
```

Dostępne parametry:
- `autoconnect=true` - Automatyczne łączenie po załadowaniu
- `resize=scale|remote` - Tryb skalowania
- `quality=0-9` - Jakość kompresji (0=najlepsza jakość, 9=najszybsza)

## Funkcje

- **Bezpośredni dostęp**: Każdy serwer VNC ma własny URL
- **Standardowy noVNC**: Pełna kompatybilność ze standardowym interfejsem noVNC
- **Automatyczne hasła**: Hasła są automatycznie wstrzykiwane do interfejsu
- **Monitoring**: Automatyczne restartowanie proxy w przypadku awarii
- **Skalowanie**: Automatyczne skalowanie ekranu

## Monitorowanie

Addon automatycznie monitoruje wszystkie proxy i restartuje je w przypadku awarii. Wszystkie zdarzenia są rejestrowane w logach addon-a.

## Rozwiązywanie problemów

### Nie mogę się połączyć z proxy
1. Sprawdź logi addon-a w Home Assistant
2. Upewnij się, że port nie jest używany przez inną usługę
3. Sprawdź czy firewall nie blokuje portu

### Proxy się restartuje
1. Sprawdź czy serwer VNC jest dostępny z Home Assistant
2. Sprawdź logi pod kątem błędów sieciowych
3. Upewnij się, że parametry host:port są poprawne

### Problemy z wydajnością
1. Zmniejsz jakość kompresji dodając `?quality=9` do URL
2. Sprawdź obciążenie CPU Home Assistant
3. Rozważ użycie przewodowego połączenia sieciowego

## Przykład kompletnej konfiguracji

```yaml
vnc_servers:
  - name: "Windows Desktop"
    host: 192.168.1.50
    port: 5900
    proxy_port: 6080
    password: "windows123"
    
  - name: "Linux Server"
    host: 192.168.1.51
    port: 5900
    proxy_port: 6081
    
  - name: "macOS iMac"
    host: 192.168.1.52
    port: 5900
    proxy_port: 6082
    password: "mac456"
```

Po tej konfiguracji będziesz mieć dostęp do:
- Windows Desktop: `http://192.168.1.100:6080/vnc.html`
- Linux Server: `http://192.168.1.100:6081/vnc.html`
- macOS iMac: `http://192.168.1.100:6082/vnc.html`