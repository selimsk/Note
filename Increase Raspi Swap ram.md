I see the issue. You are currently using **zram**, which is different from a standard Swap file. 

**zram** compresses data *inside* your existing 1GB of RAM. It's fast, but on a 1GB machine, it's not enough of a safety net for your goals. To get a true **2GB Swap**, we should add a file-based swap on your storage (SD card or SSD) to supplement the zram.

Since your hostname is `homecasaos`, you are likely on a Debian/Ubuntu-based system. Follow these steps to add the 2GB file-based swap alongside your zram:

---

### Step 1: Create the 2GB Swap File
We will create a physical file on your disk to act as emergency memory.
```bash
sudo fallocate -l 2G /swapfile
```
*(If you get an error saying "fallocate failed", use this instead: `sudo dd if=/dev/zero of=/swapfile bs=1M count=2048`)*

### Step 2: Secure the File
You must set the correct permissions so only the system can access it.
```bash
sudo chmod 600 /swapfile
```

### Step 3: Format and Enable
Tell the system this file is for Swap.
```bash
sudo mkswap /swapfile
sudo swapon /swapfile
```

### Step 4: Make it Permanent
To ensure this stays active after you reboot, add it to your filesystem table:
```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

---

### How to Verify (The Result)
Now, run `sudo swapon --show` again. You should see **two** entries:
1.  **/dev/zram0** (Your fast, compressed RAM swap).
2.  **/swapfile** (Your new 2GB emergency disk swap).


### Why this works better for you:
The system will prioritize **zram** first (because it's faster). If you run out of both RAM and zram, it will overflow into the **/swapfile**. This prevents the complete system freeze you experienced earlier while keeping the speed of zram for smaller tasks.

**Are both showing up now when you run `sudo swapon --show`?** If yes, we can move on to installing **Pi-hole**.
