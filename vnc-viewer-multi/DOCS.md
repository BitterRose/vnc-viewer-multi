# VNC Viewer Multi Proxy - Documentation

## Overview

This add-on creates separate proxies for each configured VNC server, enabling direct access to each server on a separate port without additional graphical interface. The add-on uses a modified version of noVNC with improved support for insecure connections.

## Configuration

Example configuration with multiple VNC servers:

```yaml
vnc_servers:
  - name: "Office Computer"
    host: 192.168.0.23
    port: 5900
    proxy_port: 6080
    password: "mypassword123"
  - name: "Production Server"
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

## Configuration Parameters

### `vnc_servers` (Required)

List of VNC servers. Each server requires the following parameters:

#### `name` (Required)
- **Type**: string
- **Description**: Friendly server name for identification in logs and interface

#### `host` (Required)  
- **Type**: string
- **Description**: IP address or hostname of the VNC server
- **Examples**: `192.168.1.100`, `vnc-server.local`, `10.0.0.50`

#### `port` (Optional)
- **Type**: integer (1-65535)
- **Default**: 5900
- **Description**: Port on which the VNC server is running
- **Common ports**: 5900 (standard), 5901, 5902, 5600, etc.

#### `proxy_port` (Required)
- **Type**: integer (6080-6089)
- **Description**: Port on which the proxy for this VNC server will be available
- **Note**: Each server must have a unique proxy_port
- **Available ports**: 6080, 6081, 6082, 6083, 6084, 6085, 6086, 6087, 6088, 6089

#### `password` (Optional)
- **Type**: string  
- **Description**: VNC server password
- **Note**: If not set, will prompt for password during connection
- **Security**: Password can contain digits, letters, and special characters

## Available Proxy Ports

The add-on reserves ports **6080-6089** for VNC server proxies. You can configure a maximum of **10 VNC servers** simultaneously.

## Usage

After configuring the add-on, each VNC server will be accessible at three different addresses:

### 1. **Auto-login** (Recommended for password-protected servers)
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc_auto.html
```
- Automatic redirect with password
- Most convenient access method
- Automatic connection after loading

### 2. **Lite noVNC interface**
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc_lite.html
```

**With password:**
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc_lite.html?password=PASSWORD&insecure=true
```

**Without password (will prompt):**
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc_lite.html?insecure=true
```

### 3. **Full noVNC interface**
```
http://HOME_ASSISTANT_IP:PROXY_PORT/vnc.html
```

### Real address examples:
If your Home Assistant has IP `192.168.1.100`:

- **Windows PC**: `http://192.168.1.100:6083/vnc_auto.html`
- **Raspberry Pi**: `http://192.168.1.100:6082/vnc_lite.html?password=raspberry&insecure=true`
- **Server**: `http://192.168.1.100:6081/vnc.html`

## URL Parameters (Optional)

You can customize noVNC behavior through URL parameters:

```
http://IP:PORT/vnc_lite.html?password=PASSWORD&scale=true&cursor=true&insecure=true&quality=6
```

**Available parameters:**
- `password=PASSWORD` - Automatic login
- `insecure=true` - Allow insecure HTTP connections
- `scale=true` - Automatic screen scaling
- `cursor=true` - Show remote cursor
- `view_only=true` - View only (no control)
- `quality=0-9` - Compression quality (0=best quality, 9=fastest)

## Features

- ✅ **Multiple VNC server support** - Up to 10 servers simultaneously
- ✅ **Direct access** - Each server on its own port  
- ✅ **Automatic passwords** - Password injection into interface
- ✅ **Modified noVNC** - Improved HTTP connection support
- ✅ **Flexible VNC ports** - Support for ports 1-65535
- ✅ **Automatic monitoring** - Proxy restart on failure
- ✅ **Detailed logs** - Connection problem diagnostics

## Monitoring and Diagnostics

### Add-on logs
The add-on automatically:
- Monitors all proxies every 30 seconds
- Restarts proxies on failure
- Saves detailed websockify logs

### Log interpretation
The add-on shows information about available proxies and their URLs.

### Websockify log location
Detailed logs for each proxy:
```
/var/log/websockify_6080.log  # for proxy_port 6080
/var/log/websockify_6081.log  # for proxy_port 6081
```

## Troubleshooting

### Can't connect to proxy
1. **Check add-on logs** in Home Assistant
2. **Ensure** proxy port is not used by another service  
3. **Check firewall** on Home Assistant
4. **Check websockify logs** at `/var/log/websockify_XXXX.log`

### VNC connection problems
**Possible causes:**
- VNC server not running on target machine
- Firewall blocking VNC port
- Incorrect IP or port in configuration
- Network doesn't allow connections between machines

**How to check (Windows PowerShell):**
```powershell
Test-NetConnection -ComputerName 192.168.1.100 -Port 5900
```

**How to check (Linux/Mac):**
```bash
nc -z 192.168.1.100 5900
# or
telnet 192.168.1.100 5900
```

### Proxy restarts
1. ✅ Check VNC server availability from HA
2. ✅ Check logs for network errors
3. ✅ Ensure host:port parameters are correct in configuration
4. ✅ Check network stability between HA and VNC server

### Password problems
1. ✅ Use `vnc_auto.html` for automatic login
2. ✅ Check if password doesn't contain problematic characters (`, ", ', &)
3. ✅ Try without password - maybe VNC server has different settings
4. ✅ Check VNC configuration on target machine

### Performance issues
1. ✅ Reduce compression quality by adding `?quality=9` to URL
2. ✅ Check Home Assistant CPU load
3. ✅ Use wired connection instead of Wi-Fi
4. ✅ Limit number of simultaneous connections

### "noVNC requires a secure context (TLS)" 
This add-on uses modified noVNC that solves this problem:
1. **Use insecure=true parameter** in URL
2. **vnc_auto.html already contains** this parameter
3. **Warning is skipped** in browser logs

## VNC Server Configuration

### Windows
- **TightVNC**, **UltraVNC**, **RealVNC**
- Standard port: **5900**
- Check firewall: **Windows Defender Firewall**

### Linux  
- **x11vnc**, **TigerVNC**, **RealVNC**
- Standard port: **5900**
- Check: `sudo netstat -tlnp | grep :5900`

### macOS
- **Screen Sharing** (built-in)
- Standard port: **5900**  
- Enable: **System Preferences** → **Sharing** → **Screen Sharing**

### Raspberry Pi
- **RealVNC** (default in Raspberry Pi OS)
- Standard port: **5900**
- Enable: `sudo raspi-config` → **Interfacing Options** → **VNC**

## Security

### Recommendations
- 🔐 **Use VNC passwords** for all servers
- 🌐 **Restrict access** to local network  
- 🔒 **Don't expose ports** 6080-6089 to internet
- 📊 **Monitor logs** for unauthorized access attempts

### Security information
- Passwords are securely passed through URL parameters
- Proxy runs locally in Home Assistant container
- VNC traffic doesn't leave local network
- Automatic monitoring detects connection problems

## Advanced Configuration Example

```yaml
vnc_servers:
  # Windows server with RealVNC
  - name: "Windows Desktop"
    host: 192.168.1.100  
    port: 5900
    proxy_port: 6080
    password: "secure123"
    
  # Linux server with custom port
  - name: "Ubuntu Server"
    host: 192.168.1.101
    port: 5901
    proxy_port: 6081
    password: "linux_vnc"
    
  # Raspberry Pi with default settings
  - name: "Raspberry Pi 4"
    host: 192.168.1.200
    port: 5900  
    proxy_port: 6082
    password: "raspberry"
    
  # VMware virtual machine
  - name: "VM Test Environment"  
    host: 192.168.1.150
    port: 5902
    proxy_port: 6083
    
  # macOS with Screen Sharing
  - name: "MacBook Pro"
    host: 192.168.1.120
    port: 5900
    proxy_port: 6084
    password: "mac_screen"
```

### Access after configuration:
- **Windows Desktop**: `http://192.168.1.5:6080/vnc_auto.html`
- **Ubuntu Server**: `http://192.168.1.5:6081/vnc_auto.html`
- **Raspberry Pi**: `http://192.168.1.5:6082/vnc_auto.html`
- **VM Test**: `http://192.168.1.5:6083/vnc_lite.html` (no password)
- **MacBook Pro**: `http://192.168.1.5:6084/vnc_auto.html`

## FAQ

### Q: Can I use more than 10 VNC servers?
**A:** No, the add-on supports a maximum of 10 servers (ports 6080-6089). This limitation can be changed by modifying the code.

### Q: Does the add-on work with VNC over internet?
**A:** Yes, but **not recommended** for security reasons. Use VPN or SSH tunnels.

### Q: Why do I see a TLS warning?
**A:** The add-on uses modified noVNC that skips this warning. Use the `insecure=true` parameter.

### Q: Can I change the default ports 6080-6089?
**A:** Currently no, but you can modify the code in `config.yaml` and startup script.

### Q: How do I restart a specific proxy?
**A:** Proxies are automatically restarted on problems. You can also restart the entire add-on.

---

**💡 Tip:** For the best experience, use `vnc_auto.html` - it will automatically log in and connect to the VNC server!