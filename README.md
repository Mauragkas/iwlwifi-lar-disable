# iwlwifi-lar-disable (Linux 7.x Port)

This repository contains:
1. **The ported LAR-disable & NO-IR bypass patch** updated for Linux kernel **7.x** (compatible with 7.2.x+).
2. **Arch Linux PKGBUILD & DKMS packaging** for easy installation and automatic module rebuilding on kernel updates.

---

## What this does
Intel Wi-Fi cards (such as AX200, AX210, etc.) enforce Location Aware Regulatory (LAR) in their Linux driver (`iwlwifi`), which places `(no IR)` (No Initiate Radiation) restrictions on 5 GHz channels in AP / hotspot mode.

This patch:
- Introduces the module parameter `lar_disable` (`options iwlwifi lar_disable=1`).
- Bypasses LAR validation checks in `iwl-nvm-parse.c` and `mvm/nvm.c`.
- Clears the kernel `NO_IR` (`IEEE80211_CHAN_NO_IR` and `NL80211_RRF_NO_IR`) flags on channels when `lar_disable=1` is specified.

---

## Installation via DKMS on Arch Linux

### 1. Install prerequisites
```bash
sudo pacman -S dkms linux-headers wireless-regdb
```

### 2. Manual DKMS Installation
```bash
sudo mkdir -p /usr/src/iwlwifi-lar-disable-7.2.9
# Extract your kernel's drivers/net/wireless/intel/iwlwifi into /usr/src/iwlwifi-lar-disable-7.2.9
# Apply patches/iwlwifi-7.2-lar-disable-no-ir.patch
sudo dkms add iwlwifi-lar-disable/7.2.9
sudo dkms build iwlwifi-lar-disable/7.2.9
sudo dkms install iwlwifi-lar-disable/7.2.9
```

### 3. Enable the parameter
Create `/etc/modprobe.d/iwlwifi.conf`:
```ini
options iwlwifi lar_disable=1
```

Reload the module or reboot:
```bash
sudo modprobe -r iwlmvm iwlwifi && sudo modprobe iwlwifi
```

### 4. Verify
```bash
cat /sys/module/iwlwifi/parameters/lar_disable
# Expected: Y
```
