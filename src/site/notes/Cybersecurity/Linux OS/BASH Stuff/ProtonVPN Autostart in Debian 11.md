---
{"dg-publish":true,"permalink":"/cybersecurity/linux-os/bash-stuff/proton-vpn-autostart-in-debian-11/","tags":["ProtonVPN"]}
---


1. Go to GNOME tweaks and setup autostart for the ProtonVPN app.
2. Edit the created file in /home/\[username\]/autostart so that the exec line points to a script.
```bash
[Desktop Entry]
Name=Proton VPN
Exec=/home/[user]/.protonstartup.sh
Terminal=false
Type=Application
Icon=protonvpn-logo
StartupWMClass=Protonvpn
Comment=Proton VPN GUI client
Categories=Network;
Keywords=vpn;
```
3. The script should be:
```bash
# !bin/bash
protonvpn-cli ks --off
protonvpn-cli c -f
```

ProtonVPN should connect automatically on startup now.


