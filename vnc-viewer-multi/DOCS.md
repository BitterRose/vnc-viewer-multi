# VNC Viewer Multi Proxy - Dokumentacja

## Opis

Ten addon tworzy osobne proxy dla każdego skonfigurowanego serwera VNC, umożliwiając bezpośredni dostęp do każdego serwera na osobnym porcie bez dodatkowego interfejsu graficznego. Addon używa zmodyfikowanej wersji noVNC z ulepszoną obsługą połączeń niezabezpieczonych.

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
    port: 5600
    proxy_port: 6082
    password: "raspberry"
  - name: "Windows PC"
    host: 192.168.0.100
    port: 5900
    proxy_port: 6083
    password: "windows123"
```

## Parametry konfiguracji

### `vnc_servers` (Wymagany)

Lista serwerów VNC. Każdy serwer wymaga następujących parametrów:

#### `name` (Wymagany)
- **Typ**: string
- **Opis**: Przyjazna nazwa serwera do identyfikacji w logach i interfejsie

#### `host` (Wymagany)  
- **Typ**: string
- **Opis**: Adres IP lub nazwa hosta serwera VNC
- **Przykłady**: `192.168.1.100`, `vnc-server.local`, `10.0.0.50`

#### `port` (Opcjonalny)
- **Typ**: integer (1-65535)
- **Domyślnie**: 5900
- **Opis**: Port na którym działa serwer VNC
- **Typowe porty**: 5900 (standardowy), 5901, 5902, 5600, itp.

#### `proxy_port` (Wymagany)
- **Typ**: integer (6080-6089)
- **Opis**: Port na którym będzie dostępny proxy dla tego serwera VNC
- **Uwaga**: Każdy serwer musi mieć unikalny proxy_port
- **Dostępne porty**: 6080, 6081, 6082, 6083, 6084, 6085, 6086, 6087, 6088, 6089

#### `password` (Opcjonalny)
- **Typ**: string  
- **Opis**: Hasło do serwera VNC
- **Uwaga**: Jeśli nie zostanie ustawione, będzie pytać o hasło podczas łączenia
- **Bezpieczeństwo**: Hasło może zawierać cyfry, litery i znaki specjalne

## Dostępne porty proxy

Addon rezerwuje porty **6080-6089** dla proxy serwerów VNC. Możesz skonfigurować maksymalnie **10 serwerów VNC** jednocześnie.

## Użytkowanie

Po skonfigurowaniu addon-a, każdy serwer VNC będzie dostępny pod trzema różnymi adresami:

### 1. **Auto-login** (Zalecany dla serwerów z hasłem)
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc_auto.html
```
- Automatyczne przekierowanie z hasłem
- Najwygodniejszy sposób dostępu
- Automatyczne łączenie po załadowaniu

### 2. **Lekki interfejs noVNC**
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc_lite.html
```

**Z hasłem:**
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc_lite.html?password=HASŁO&insecure=true
```

**Bez hasła (zostaniesz poproszony):**
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc_lite.html?insecure=true
```

### 3. **Pełny interfejs noVNC**
```
http://IP_HOME_ASSISTANT:PROXY_PORT/vnc.html
```

### Przykłady rzeczywistych adresów:
Jeśli Twój Home Assistant ma IP `192.168.1.100`:

- **Windows PC**: `http://192.168.1.100:6083/vnc_auto.html`
- **Raspberry Pi**: `http://192.168.1.100:6082/vnc_lite.html?password=raspberry&insecure=true`
- **Serwer**: `http://192.168.1.100:6081/vnc.html`

## Parametry URL (opcjonalne)

Możesz dostosować zachowanie noVNC przez parametry URL:

```
http://IP:PORT/vnc_lite.html?password=HASŁO&scale=true&cursor=true&insecure=true&quality=6
```

**Dostępne parametry:**
- `password=HASŁO` - Automatyczne logowanie
- `insecure=true` - Zezwala na niezabezpieczone połączenia HTTP
- `scale=true` - Automatyczne skalowanie ekranu
- `cursor=true` - Pokazuj kursor zdalny
- `view_only=true` - Tylko podgląd (bez kontroli)
- `quality=0-9` - Jakość kompresji (0=najlepsza jakość, 9=najszybsza)

## Funkcje

- ✅ **Obsługa wielu serwerów VNC** - Do 10 serwerów jednocześnie
- ✅ **Bezpośredni dostęp** - Każdy serwer na własnym porcie  
- ✅ **Automatyczne hasła** - Wstrzykiwanie haseł do interfejsu
- ✅ **Zmodyfikowany noVNC** - Ulepszona obsługa połączeń HTTP
- ✅ **Elastyczne porty VNC** - Obsługa portów 1-65535
- ✅ **Automatyczne monitorowanie** - Restartowanie proxy w przypadku awarii
- ✅ **Testy połączenia** - Automatyczne sprawdzanie dostępności serwerów
- ✅ **Szczegółowe logi** - Diagnostyka problemów z połączeniem

## Monitorowanie i diagnostyka

### Logi addon-a
Addon automatycznie:
- Monitoruje wszystkie proxy co 30 sekund
- Restartuje proxy w przypadku awarii
- Zapisuje szczegółowe logi websockify

### Interpretacja logów
Addon pokazuje informacje o dostępnych proxy i ich adresach URL.

### Lokalizacja logów websockify
Szczegółowe logi dla każdego proxy:
```
/var/log/websockify_6080.log  # dla proxy_port 6080
/var/log/websockify_6081.log  # dla proxy_port 6081
```

## Rozwiązywanie problemów

### Nie mogę się połączyć z proxy
1. **Sprawdź logi addon-a** w Home Assistant
2. **Upewnij się**, że port proxy nie jest używany przez inną usługę  
3. **Sprawdź firewall** Home Assistant
4. **Sprawdź logi websockify** w `/var/log/websockify_XXXX.log`

### Problemy z połączeniem VNC
**Możliwe przyczyny:**
- Serwer VNC nie działa na docelowej maszynie
- Firewall blokuje port VNC
- Nieprawidłowy IP lub port w konfiguracji
- Sieć nie pozwala na połączenia między maszynami

**Jak sprawdzić (Windows PowerShell):**
```powershell
Test-NetConnection -ComputerName 192.168.1.100 -Port 5900
```

**Jak sprawdzić (Linux/Mac):**
```bash
nc -z 192.168.1.100 5900
# lub
telnet 192.168.1.100 5900
```

### Proxy się restartuje
1. **Sprawdź dostępność serwera VNC** z Home Assistant
2. **Sprawdź logi websockify** - addon pokaże ostatnie 5 linii przy restarcie
3. **Upewnij się**, że parametry host:port są poprawne w konfiguracji
4. **Sprawdź stabilność sieci** między HA a serwerem VNC

### Problemy z hasłem
1. **Użyj vnc_auto.html** dla automatycznego logowania
2. **Sprawdź czy hasło** nie zawiera problemowych znaków (`, ", ', &)
3. **Spróbuj bez hasła** - może serwer VNC ma inne ustawienia
4. **Sprawdź konfigurację VNC** na docelowej maszynie

### Problemy z wydajnością
1. **Zmniejsz jakość kompresji** dodając `?quality=9` do URL
2. **Sprawdź obciążenie CPU** Home Assistant
3. **Użyj przewodowego połączenia** zamiast Wi-Fi
4. **Ogranicz liczbę jednoczesnych połączeń**

### "noVNC requires a secure context (TLS)" 
Ten addon używa zmodyfikowanego noVNC który rozwiązuje ten problem:
1. **Używaj parametru insecure=true** w URL
2. **vnc_auto.html już zawiera** ten parametr
3. **Ostrzeżenie jest pominięte** w logach przeglądarki

## Konfiguracja serwerów VNC

### Windows
- **TightVNC**, **UltraVNC**, **RealVNC**
- Standardowy port: **5900**
- Sprawdź firewall: **Windows Defender Firewall**

### Linux  
- **x11vnc**, **TigerVNC**, **RealVNC**
- Standardowy port: **5900**
- Sprawdź: `sudo netstat -tlnp | grep :5900`

### macOS
- **Screen Sharing** (wbudowany)
- Standardowy port: **5900**  
- Włącz: **System Preferences** → **Sharing** → **Screen Sharing**

### Raspberry Pi
- **RealVNC** (domyślnie w Raspberry Pi OS)
- Standardowy port: **5900**
- Włącz: `sudo raspi-config` → **Interfacing Options** → **VNC**

## Bezpieczeństwo

### Zalecenia
- 🔐 **Używaj haseł VNC** dla wszystkich serwerów
- 🌐 **Ogranicz dostęp** do sieci lokalnej  
- 🔒 **Nie eksponuj portów** 6080-6089 na internet
- 📊 **Monitoruj logi** pod kątem nieautoryzowanych prób dostępu

### Informacje o bezpieczeństwie
- Hasła są przekazywane bezpiecznie przez parametry URL
- Proxy działa lokalnie w kontenerze Home Assistant
- Ruch VNC nie opuszcza sieci lokalnej
- Automatyczne monitorowanie wykrywa problemy z połączeniami

## Przykład zaawansowanej konfiguracji

```yaml
vnc_servers:
  # Serwer Windows z RealVNC
  - name: "Windows Desktop"
    host: 192.168.1.100  
    port: 5900
    proxy_port: 6080
    password: "secure123"
    
  # Linux serwer z niestandardowym portem
  - name: "Ubuntu Server"
    host: 192.168.1.101
    port: 5901
    proxy_port: 6081
    password: "linux_vnc"
    
  # Raspberry Pi z domyślnymi ustawieniami
  - name: "Raspberry Pi 4"
    host: 192.168.1.200
    port: 5900  
    proxy_port: 6082
    password: "raspberry"
    
  # Maszyna wirtualna VMware
  - name: "VM Test Environment"  
    host: 192.168.1.150
    port: 5902
    proxy_port: 6083
    
  # macOS z Screen Sharing
  - name: "MacBook Pro"
    host: 192.168.1.120
    port: 5900
    proxy_port: 6084
    password: "mac_screen"
```

### Dostęp po konfiguracji:
- **Windows Desktop**: `http://192.168.1.5:6080/vnc_auto.html`
- **Ubuntu Server**: `http://192.168.1.5:6081/vnc_auto.html`
- **Raspberry Pi**: `http://192.168.1.5:6082/vnc_auto.html`
- **VM Test**: `http://192.168.1.5:6083/vnc_lite.html` (bez hasła)
- **MacBook Pro**: `http://192.168.1.5:6084/vnc_auto.html`

## FAQ

### Q: Czy mogę używać więcej niż 10 serwerów VNC?
**A:** Nie, addon obsługuje maksymalnie 10 serwerów (porty 6080-6089). To ograniczenie można zmienić modyfikując kod.

### Q: Czy addon działa z VNC przez internet?
**A:** Tak, ale **nie jest zalecane** ze względów bezpieczeństwa. Używaj VPN lub tuneli SSH.

### Q: Dlaczego widzę ostrzeżenie o TLS?
**A:** Addon używa zmodyfikowanego noVNC który pomija to ostrzeżenie. Używaj parametru `insecure=true`.

### Q: Czy mogę zmienić domyślne porty 6080-6089?
**A:** Obecnie nie, ale możesz zmodyfikować kod w `config.yaml` i skrypcie uruchomieniowym.

### Q: Jak zrestartować konkretny proxy?
**A:** Proxy są automatycznie restartowane przy problemach. Możesz też zrestartować cały addon.

---

**💡 Wskazówka:** Dla najlepszego doświadczenia użyj `vnc_auto.html` - automatycznie zaloguje się i połączy z serwerem VNC!