# VNC Viewer Multi

![Addon Stage][stage-badge]
![Supports aarch64 Architecture][aarch64-badge]
![Supports amd64 Architecture][amd64-badge]
![Supports armhf Architecture][armhf-badge]
![Supports armv7 Architecture][armv7-badge]
![Supports i386 Architecture][i386-badge]

Advanced Home Assistant add-on enabling simultaneous access to multiple VNC servers. Each VNC server is accessible on a separate port with full noVNC interface. The add-on uses a modified version of noVNC with improved HTTP connection support.

## Features

- 🖥️ **Multiple VNC server support** - Up to 10 servers simultaneously (ports 6080-6089)
- 🌐 **Direct access** - Each server on its own port without additional interface
- 🔐 **Automatic passwords** - Password injection directly into noVNC interface
- 📱 **Modified noVNC** - Fork with HTTP connection fixes
- 🔄 **Automatic monitoring** - Proxy restart on failure
- ⚡ **High performance** - Direct proxy connections without additional layers
- 🔧 **Flexible ports** - Support for VNC ports 1-65535

## Installation

1. Add this repository to Home Assistant Add-on Store
2. Find "VNC Viewer Multi" and click Install
3. Configure VNC servers in Configuration tab
4. Start the add-on

## Example Configuration

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

## Server Access

After configuration, each server will be accessible at:

### Full noVNC interface:
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc.html
```

### Lite noVNC interface:
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc_lite.html
```

### Auto-login (for password-protected servers):
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc_auto.html
```

### Example URLs:
- Windows Desktop: `http://192.168.1.100:6080/vnc_auto.html` (auto-login)
- Linux Server: `http://192.168.1.100:6081/vnc_lite.html`
- MacBook: `http://192.168.1.100:6082/vnc_lite.html?password=mac456&insecure=true`

## Configuration Parameters

### `vnc_servers` (Required)

List of VNC servers to configure. Each server requires:

- **`name`** (string, required) - Friendly server name
- **`host`** (string, required) - VNC server IP or hostname
- **`port`** (integer, optional) - VNC server port (default: 5900)  
- **`proxy_port`** (integer, required) - Proxy port (6080-6089, must be unique)
- **`password`** (string, optional) - VNC password (if required)

## URL Parameters

You can customize noVNC behavior through URL parameters:

```
http://IP:PORT/vnc_lite.html?password=PASSWORD&insecure=true&scale=true&cursor=true
```

**Available parameters:**
- `password=PASSWORD` - Automatic login
- `insecure=true` - Allow HTTP connections (recommended)
- `scale=true` - Automatic screen scaling
- `cursor=true` - Show remote cursor
- `view_only=true` - View only (no control)
- `quality=0-9` - Compression quality (0=best quality, 9=fastest)

## Ports

The add-on uses ports **6080-6089** for VNC server proxies. Ensure these ports are not used by other services.

## noVNC Features

- **Auto-login** - vnc_auto.html redirects with password
- **Lite interface** - vnc_lite.html for basic functionality  
- **Full interface** - vnc.html with all options
- **URL parameters** - Customization through web address
- **HTTP support** - Modified noVNC without HTTPS requirement

## Troubleshooting

### Can't connect to proxy
1. Check add-on logs
2. Ensure port is not occupied
3. Check if firewall blocks the port
4. Check websockify logs at `/var/log/websockify_XXXX.log`

### Proxy restarts
1. Check VNC server availability
2. Check websockify logs for network errors  
3. Ensure host:port parameters are correct
4. Check network connection stability

### Password issues
1. Use `vnc_auto.html` for automatic login
2. Check if password contains problematic characters
3. Add `insecure=true` parameter to URL
4. Try without password to test connection

## Security

- Passwords are securely passed through URL parameters
- Proxies run locally within Home Assistant network
- Only configured ports are open
- Automatic monitoring detects connection issues

## Advanced Configuration Example

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

After this configuration you will have:
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