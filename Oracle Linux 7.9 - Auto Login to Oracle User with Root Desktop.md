# Oracle Linux 7.9 - Auto Login to Oracle User with Root Desktop

## Overview

This guide configures Oracle Linux 7.9 to:

- Automatically log in as the `oracle` user after boot.
- Automatically open a root terminal.
- Avoid enabling direct graphical login for the `root` account.
- Follow Oracle/Red Hat recommended security practices.

> **Tested On**
>
> - Oracle Linux 7.9
> - GNOME Desktop (GDM)
> - VirtualBox/VMware

---

# Why this approach?

Oracle Linux and Red Hat intentionally prevent automatic GUI login as the **root** user.

Instead of bypassing these security controls, this guide:

- Auto logs in as **oracle**
- Opens a **root terminal automatically**
- Provides nearly the same convenience while remaining much safer.

---

# Step 1 - Configure Automatic Login

Edit GDM configuration.

```bash
sudo vi /etc/gdm/custom.conf
```

Change to:

```ini
[daemon]
AutomaticLoginEnable=True
AutomaticLogin=oracle
```

Save the file.

---

# Step 2 - Allow oracle to use sudo

Install sudo if needed.

```bash
yum install sudo -y
```

Add oracle to wheel group.

```bash
usermod -aG wheel oracle
```

Verify:

```bash
id oracle
```

You should see:

```
wheel
```

---

# Step 3 - Allow passwordless sudo (Optional)

Edit sudoers safely.

```bash
visudo
```

Find:

```text
%wheel ALL=(ALL) ALL
```

Change to:

```text
%wheel ALL=(ALL) NOPASSWD: ALL
```

Save.

---

# Step 4 - Auto Start Root Terminal

Create the autostart directory.

```bash
mkdir -p /home/oracle/.config/autostart
```

Create a desktop file.

```bash
vi /home/oracle/.config/autostart/root-terminal.desktop
```

Paste:

```ini
[Desktop Entry]
Type=Application
Name=Root Terminal
Exec=gnome-terminal -- bash -c "sudo -i"
Terminal=false
X-GNOME-Autostart-enabled=true
```

Save.

---

# Step 5 - Set Ownership

```bash
chown -R oracle:oinstall /home/oracle/.config
```

---

# Step 6 - Reboot

```bash
reboot
```

---

# Result

After boot:

- Oracle Linux logs in automatically.
- GNOME Desktop loads.
- A root terminal opens automatically.

No manual login required.

---

# Verify

Current user:

```bash
whoami
```

Output:

```
oracle
```

Inside terminal:

```bash
sudo -i
```

Output:

```
root
```

---

# Rollback

Disable automatic login.

Edit

```bash
sudo vi /etc/gdm/custom.conf
```

Remove:

```ini
AutomaticLoginEnable=True
AutomaticLogin=oracle
```

Delete autostart.

```bash
rm -f /home/oracle/.config/autostart/root-terminal.desktop
```

Reboot.

---

# Notes

- Recommended for lab environments.
- Not recommended for production servers.
- Root GUI login remains disabled.
- Oracle Database can continue running under the `oracle` user while administrative tasks are performed in the root terminal.

---

# License

MIT
