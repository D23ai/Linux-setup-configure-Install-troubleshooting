# Oracle Linux 7.9 – Root GUI Auto Login

Configure Oracle Linux 7.9 GNOME (GDM) to automatically log in as the **root** user.

> **Test Environment**
>
> - Oracle Linux 7.9
> - GNOME Desktop (GDM)
> - VirtualBox / VMware
> - Lab Environment Only

---

# ⚠️ Recommended Approach (Still Gives You a Root Desktop)

Instead of logging into the desktop as **root**, configure the **oracle** user to automatically log in and automatically start a root terminal.

This approach:

- Follows Oracle/Red Hat best practices
- Avoids modifying GDM security policies
- Still provides full root access using `sudo -i`

---

# If You Really Want the Root GUI Desktop

> **Warning**
>
> This method is intended **only for personal lab or virtual machine environments**.
>
> It is **NOT recommended** for production servers.

---

# Step 1 – Set the Root Password

```bash
passwd root
```

---

# Step 2 – Permit Root Login in GDM

Edit:

```bash
vi /etc/pam.d/gdm-password
```

Locate:

```text
auth required pam_succeed_if.so user != root quiet
```

Comment it:

```text
#auth required pam_succeed_if.so user != root quiet
```

> **Note**
>
> Some Oracle Linux versions do not contain this line.

---

# Step 3 – Configure Automatic Login

Edit:

```bash
vi /etc/gdm/custom.conf
```

Configure:

```ini
[daemon]
AutomaticLoginEnable=True
AutomaticLogin=root
```

---

# Step 4 – Ensure Root Is Allowed

Edit:

```bash
vi /etc/securetty
```

Verify the following entries exist:

```text
tty1
tty2
tty3
tty4
tty5
tty6
```

---

# Step 5 – Reboot

```bash
reboot
```

---

# Verification

Verify the root account is enabled:

```bash
passwd -S root
```

Verify GDM configuration:

```bash
cat /etc/gdm/custom.conf
```

Verify PAM configuration:

```bash
grep root /etc/pam.d/gdm-password
```

---

# Troubleshooting

If automatic login still fails:

```bash
journalctl -b -u gdm --no-pager
```

Check for:

- PAM authentication errors
- GDM policy restrictions
- Permission issues

---

# Security Notice

Automatic graphical login as **root** should only be used for:

- Personal Lab
- Virtual Machines
- Testing

It is **NOT recommended** for:

- Production Servers
- Shared Systems
- Internet-facing Servers

---



---

⭐ If this guide helped you, consider giving the repository a star.
