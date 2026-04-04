To keep your network simple and save a tiny bit of system overhead (which helps on your 1GB RAM setup), disabling IPv6 is a common move. On a Raspberry Pi, the most reliable way to do this "permanently" is through the kernel command line.

### Step 1: Edit the Boot Command Line
The Raspberry Pi reads its startup instructions from a file called `cmdline.txt`. 

1. Open the file:
   ```bash
   sudo nano /boot/firmware/cmdline.txt
   ```
   *(Note: If you are on an older version of Raspberry Pi OS, the path might be `/boot/cmdline.txt`)*.

2. You will see a single long line of text. **Do not create a new line.** Use your arrow keys to go to the very end of that line, add a space, and paste this:
   ```text
   ipv6.disable=1
   ```

3. Press `Ctrl+O`, `Enter`, then `Ctrl+X` to save and exit.

---

### Step 2: Update System Configurations (Sysctl)
To make sure the networking stack itself doesn't try to initialize IPv6, add these rules to the system configuration:

1. Open the sysctl file:
   ```bash
   sudo nano /etc/sysctl.conf
   ```

2. Scroll to the bottom and paste these three lines:
   ```text
   net.ipv6.conf.all.disable_ipv6 = 1
   net.ipv6.conf.default.disable_ipv6 = 1
   net.ipv6.conf.lo.disable_ipv6 = 1
   ```

3. Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

---

### Step 3: Apply and Reboot
For these changes to take full effect (especially the kernel command line), you must reboot the Pi:

```bash
sudo reboot
```

---

### Step 4: Verify it’s Gone
After the Pi restarts, run this command to check your IP addresses:
```bash
ip addr show | grep inet6
```
**If the command returns nothing, IPv6 is successfully disabled.**



### Why this helps your Pi-hole setup:
When you install **Pi-hole** next, it will ask if you want to use IPv4 and IPv6. Since you've disabled it at the system level, you can just select **IPv4**, which makes your DNS logs much cleaner and easier to read.
