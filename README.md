# VNC Multi Viewer - Home Assistant Add-on Repository

[![Add repository on my Home Assistant][repository-badge]][repository-url]
![Supports aarch64 Architecture][aarch64-badge]
![Supports amd64 Architecture][amd64-badge]
![Supports armhf Architecture][armhf-badge]
![Supports armv7 Architecture][armv7-badge]
![Supports i386 Architecture][i386-badge]

Home Assistant add-on repository enabling simultaneous access to multiple VNC servers. Each VNC server is accessible on a separate port without requiring an additional graphical interface. The add-on uses a modified version of noVNC with improved HTTP connection support.

## ✨ Features

- 🖥️ **Multiple VNC server support** - Up to 10 servers simultaneously
- 🌐 **Direct access** - Each server on a separate port
- 🔐 **Automatic passwords** - Password injection into interface
- 📱 **Modified noVNC** - Fork with HTTP connection improvements
- 🔄 **Automatic monitoring** - Proxy restart on failure
- ⚡ **Performance** - Direct proxy connections without additional layers

## 🚀 Quick Start

### 1. Add Repository

[![Add repository on my Home Assistant][repository-badge]][repository-url]

Or manually:

1. Go to **Settings** → **Add-ons** → **Add-on Store**
2. Click **⋮** → **Repositories**
3. Add: `https://github.com/BitterRose/vnc-viewer-multi`

### 2. Install Add-on

1. Find **VNC Viewer Multi** in Add-on Store
2. Click **Install**
3. Configure VNC servers in **Configuration** tab
4. Start the add-on

### 3. Example Configuration

```yaml
vnc_servers:
  - name: "Windows Desktop"
    host: 192.168.1.50
    port: 5900
    proxy_port: 6080
    password: "mypassword123"
    
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

### 4. Server Access

After configuration, each server will be accessible at:

- **Windows Desktop**: `http://192.168.1.100:6080/vnc_auto.html` ← **Auto-login**
- **Linux Server**: `http://192.168.1.100:6081/vnc_lite.html`
- **MacBook Pro**: `http://192.168.1.100:6082/vnc_auto.html` ← **Auto-login**

## 📖 Documentation

### Available Ports

The add-on reserves ports **6080-6089** for VNC server proxies.

### noVNC Interfaces

Three interfaces are available for each server:

- **Auto-login**: `http://IP:PORT/vnc_auto.html` 
  - Automatic login with password
  - **Recommended for password-protected servers** - most convenient
  - Automatic connection without clicking
  
- **Lite**: `http://IP:PORT/vnc_lite.html` 
  - Minimalist interface
  - **Recommended for servers without passwords** or when you need login control
  - Faster and more efficient
  
- **Full**: `http://IP:PORT/vnc.html` 
  - All noVNC features
  - **For advanced users** needing full functionality

### URL Parameters

You can customize noVNC behavior through URL parameters:

```
http://IP:PORT/vnc_lite.html?password=PASSWORD&insecure=true&scale=true&quality=6
```

**Available parameters:**
- `password=PASSWORD` - Automatic login
- `insecure=true` - Allow HTTP connections  
- `scale=true` - Automatic scaling
- `quality=0-9` - Compression quality (0=best quality, 9=fastest)
- `cursor=true` - Show remote cursor
- `view_only=true` - View only without control

## ⚙️ Configuration

### VNC Server Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | string | ✅ | - | Server name (for identification) |
| `host` | string | ✅ | - | VNC server IP or hostname |
| `port` | integer | ❌ | 5900 | VNC server port |
| `proxy_port` | integer | ✅ | - | Proxy port (6080-6089) |
| `password` | string | ❌ | - | VNC password (optional) |

### Requirements

- **Home Assistant OS** or **Supervised**
- **VNC servers** available on network
- **Ports 6080-6089** available on HA host

## 🔧 Troubleshooting

### Can't connect to VNC server

1. ✅ Check add-on logs in HA  
2. ✅ Ensure VNC server is running on target machine
3. ✅ Check firewall on VNC server and Home Assistant
4. ✅ Check websockify logs at `/var/log/websockify_XXXX.log`

### Proxy restarts

1. ✅ Check VNC server availability from Home Assistant
2. ✅ Check logs for network errors
3. ✅ Ensure host:port parameters are correct

### Performance issues

1. ✅ Use `?quality=9` parameter for fastest connection
2. ✅ Check Home Assistant CPU load
3. ✅ Consider wired network connection
4. ✅ Use `vnc_lite.html` instead of full interface

## 📋 Recommended Usage

### 🏆 **Best Experience:**
1. **For password-protected servers**: Use `vnc_auto.html` 
   ```
   http://192.168.1.100:6080/vnc_auto.html
   ```
   
2. **For servers without passwords**: Use `vnc_lite.html`
   ```
   http://192.168.1.100:6080/vnc_lite.html?insecure=true
   ```

3. **For advanced features**: Use full interface
   ```
   http://192.168.1.100:6080/vnc.html
   ```

### ⚡ **Performance Tips:**
- `vnc_lite.html` is faster than full interface
- `vnc_auto.html` connects automatically without clicking
- Add `?quality=9` for fastest connection
- Use `?scale=true` for automatic fitting

## 📝 Configuration Examples

### Home Office

```yaml
vnc_servers:
  - name: "Gaming PC"
    host: 192.168.1.100
    proxy_port: 6080
    password: "gaming123"
    
  - name: "Work Laptop"  
    host: 192.168.1.101
    proxy_port: 6081
```

**Access:**
- Gaming PC: `http://HA_IP:6080/vnc_auto.html` (auto-login)
- Work Laptop: `http://HA_IP:6081/vnc_lite.html` (no password)

### Production Servers

```yaml
vnc_servers:
  - name: "Web Server"
    host: 10.0.0.10
    port: 5901
    proxy_port: 6080
    password: "webserver123"
    
  - name: "Database Server"
    host: 10.0.0.11  
    port: 5901
    proxy_port: 6081
    password: "db_secure_123"
    
  - name: "Monitoring Server"
    host: 10.0.0.12
    proxy_port: 6082
```

**Access:**
- Web Server: `http://HA_IP:6080/vnc_auto.html` (auto-login)
- Database: `http://HA_IP:6081/vnc_auto.html` (auto-login)  
- Monitoring: `http://HA_IP:6082/vnc_lite.html` (no password)

## 🛡️ Security

- 🔐 **Passwords** are securely passed through URL parameters
- 🌐 **Local proxies** - traffic doesn't leave home network
- 🚪 **Port control** - only configured ports are open
- 📊 **Monitoring** - automatic proxy problem detection
- 🛡️ **Modified noVNC** - security fixes for HTTP connections

## 🤝 Support

- 📚 **Documentation**: [DOCS.md](vnc-viewer-multi/DOCS.md)
- 🐛 **Issues**: [GitHub Issues](https://github.com/BitterRose/vnc-viewer-multi/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/BitterRose/vnc-viewer-multi/discussions)

## 📄 License

This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [noVNC](https://github.com/novnc/noVNC) - Web VNC client
- [noVNC-BitterRose](https://github.com/BitterRose/noVNC-BitterRose) - Modified noVNC version
- [websockify](https://github.com/novnc/websockify) - WebSocket proxy
- [Home Assistant](https://www.home-assistant.io/) - Home automation platform

---

⭐ **If this add-on helped you, leave a star on GitHub!**

💡 **Tip:** For the best experience, use `vnc_auto.html` - it will automatically log in and connect to the VNC server!

[repository-badge]: https://img.shields.io/badge/Add-repository-41BDF5?logo=home-assistant&style=for-the-badge
[repository-url]: https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A//github.com/BitterRose/vnc-viewer-multi

[aarch64-badge]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-badge]: https://img.shields.io/badge/amd64-yes-green.svg
[armhf-badge]: https://img.shields.io/badge/armhf-yes-green.svg
[armv7-badge]: https://img.shields.io/badge/armv7-yes-green.svg
[i386-badge]: https://img.shields.io/badge/i386-yes-green.svg