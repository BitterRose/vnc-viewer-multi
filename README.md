# VNC Multi Viewer - Home Assistant Add-on Repository

[![Add repository on my Home Assistant][repository-badge]][repository-url]
![Supports aarch64 Architecture][aarch64-badge]
![Supports amd64 Architecture][amd64-badge]
![Supports armhf Architecture][armhf-badge]
![Supports armv7 Architecture][armv7-badge]
![Supports i386 Architecture][i386-badge]

Repozytorium zawiera Home Assistant add-on umożliwiający dostęp do wielu serwerów VNC jednocześnie. Każdy serwer VNC jest dostępny na osobnym porcie bez potrzeby dodatkowego interfejsu graficznego.

## ✨ Funkcje

- 🖥️ **Obsługa wielu serwerów VNC** - Do 10 serwerów jednocześnie
- 🌐 **Bezpośredni dostęp** - Każdy serwer na osobnym porcie
- 🔐 **Automatyczne hasła** - Wstrzykiwanie haseł do interfejsu
- 📱 **Standardowy noVNC** - Pełna kompatybilność z noVNC
- 🔄 **Automatyczne monitorowanie** - Restartowanie proxy w razie awarii
- ⚡ **Wydajność** - Bezpośrednie połączenia proxy bez dodatkowych warstw

## 🚀 Szybki start

### 1. Dodaj repozytorium

[![Add repository on my Home Assistant][repository-badge]][repository-url]

Lub ręcznie:

1. Przejdź do **Settings** → **Add-ons** → **Add-on Store**
2. Kliknij **⋮** → **Repositories**
3. Dodaj: `https://github.com/TWOJA_NAZWA_UZYTKOWNIKA/ha-vnc-multi-addon`

### 2. Zainstaluj add-on

1. Znajdź **VNC Viewer Multi** w Add-on Store
2. Kliknij **Install**
3. Skonfiguruj serwery VNC w zakładce **Configuration**
4. Uruchom add-on

### 3. Przykład konfiguracji

```yaml
vnc_servers:
  - name: "Windows Desktop"
    host: 192.168.1.50
    port: 5900
    proxy_port: 6080
    password: "mojhaslo123"
    
  - name: "Linux Server"
    host: 192.168.1.51
    port: 5901
    proxy_port: 6081
    
  - name: "MacBook Pro"
    host: 192.168.1.52
    port: 5900
    proxy_port: 6082
    password: "mac456"
```

### 4. Dostęp do serwerów

Po konfiguracji każdy serwer będzie dostępny pod adresem:

- **Windows Desktop**: `http://192.168.1.100:6080/vnc.html`
- **Linux Server**: `http://192.168.1.100:6081/vnc.html`
- **MacBook Pro**: `http://192.168.1.100:6082/vnc.html`

## 📖 Dokumentacja

### Dostępne porty

Add-on rezerwuje porty **6080-6089** dla proxy serwerów VNC.

### Interfejsy noVNC

Dla każdego serwera dostępne są dwa interfejsy:

- **Pełny interfejs**: `http://IP:PORT/vnc.html`
- **Lekki interfejs**: `http://IP:PORT/vnc_lite.html`

### Parametry URL

Możesz dodać parametry dla automatycznej konfiguracji:

```
http://IP:PORT/vnc.html?autoconnect=true&resize=scale&quality=6
```

**Dostępne parametry:**
- `autoconnect=true` - Automatyczne łączenie
- `resize=scale|remote` - Tryb skalowania  
- `quality=0-9` - Jakość kompresji (0=najlepsza, 9=najszybsza)

## ⚙️ Konfiguracja

### Parametry serwera VNC

| Parametr | Typ | Wymagany | Domyślnie | Opis |
|----------|-----|----------|-----------|------|
| `name` | string | ✅ | - | Nazwa serwera (do identyfikacji) |
| `host` | string | ✅ | - | IP lub hostname serwera VNC |
| `port` | integer | ❌ | 5900 | Port serwera VNC |
| `proxy_port` | integer | ✅ | - | Port proxy (6080-6089) |
| `password` | string | ❌ | - | Hasło VNC (opcjonalne) |

### Wymagania

- **Home Assistant OS** lub **Supervised**
- **Serwery VNC** dostępne w sieci
- **Porty 6080-6089** dostępne na hoście HA

## 🔧 Rozwiązywanie problemów

### Nie mogę się połączyć z serwerem VNC

1. ✅ Sprawdź czy serwer VNC działa
2. ✅ Upewnij się, że IP i port są poprawne  
3. ✅ Sprawdź firewall na serwerze VNC
4. ✅ Sprawdź logi add-on-a w HA

### Proxy się restartuje

1. ✅ Sprawdź dostępność serwera VNC z HA
2. ✅ Sprawdź logi pod kątem błędów sieciowych
3. ✅ Upewnij się, że porty nie kolidują

### Problemy z wydajnością

1. ✅ Użyj parametru `?quality=9` dla szybszego połączenia
2. ✅ Sprawdź obciążenie CPU Home Assistant
3. ✅ Rozważ przewodowe połączenie sieciowe

## 📝 Przykłady użycia

### Domowe biuro

```yaml
vnc_servers:
  - name: "PC Gaming"
    host: 192.168.1.100
    proxy_port: 6080
    password: "gaming123"
    
  - name: "Laptop Work"  
    host: 192.168.1.101
    proxy_port: 6081
```

### Serwery produkcyjne

```yaml
vnc_servers:
  - name: "Web Server"
    host: 10.0.0.10
    port: 5901
    proxy_port: 6080
    
  - name: "Database Server"
    host: 10.0.0.11  
    port: 5901
    proxy_port: 6081
    password: "db_secure_123"
    
  - name: "Monitoring Server"
    host: 10.0.0.12
    proxy_port: 6082
```

## 🛡️ Bezpieczeństwo

- 🔐 **Hasła** są wstrzykiwane bezpiecznie do interfejsu
- 🌐 **Proxy lokalne** - ruch nie opuszcza sieci domowej
- 🚪 **Kontrola portów** - tylko skonfigurowane porty są otwarte
- 📊 **Monitorowanie** - automatyczne wykrywanie problemów

## 🤝 Wsparcie

- 📚 **Dokumentacja**: [DOCS.md](vnc-viewer-multi/DOCS.md)
- 🐛 **Problemy**: [GitHub Issues](https://github.com/TWOJA_NAZWA_UZYTKOWNIKA/ha-vnc-multi-addon/issues)
- 💬 **Dyskusje**: [GitHub Discussions](https://github.com/TWOJA_NAZWA_UZYTKOWNIKA/ha-vnc-multi-addon/discussions)

## 📄 Licencja

Ten projekt jest licencjonowany na licencji Apache 2.0 - zobacz plik [LICENSE](LICENSE) dla szczegółów.

## 🙏 Podziękowania

- [noVNC](https://github.com/novnc/noVNC) - Web VNC client
- [websockify](https://github.com/novnc/websockify) - WebSocket proxy
- [Home Assistant](https://www.home-assistant.io/) - Platforma automatyzacji domu

---

⭐ **Jeśli ten add-on Ci pomógł, zostaw gwiazdkę na GitHub!**

[repository-badge]: https://img.shields.io/badge/Add-repository-41BDF5?logo=home-assistant&style=for-the-badge
[repository-url]: https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A//github.com/TWOJA_NAZWA_UZYTKOWNIKA/ha-vnc-multi-addon

[aarch64-badge]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-badge]: https://img.shields.io/badge/amd64-yes-green.svg
[armhf-badge]: https://img.shields.io/badge/armhf-yes-green.svg
[armv7-badge]: https://img.shields.io/badge/armv7-yes-green.svg
[i386-badge]: https://img.shields.io/badge/i386-yes-green.svg