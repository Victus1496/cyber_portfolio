# Raspberry Pi 5 Setup Write-Up (CanaKit Kit + Elecrow Pitower Case)

## Overview
I decided to set up a Raspberry Pi 5 (8GB) as a general-purpose Linux machine for learning, basic automation scripting, and future homelab projects. Started with the CanaKit starter kit (~$160 from Best Buy, in-store pickup) for immediate use, then upgraded to the Elecrow Pitower tower case (~$55 from Amazon) for NVMe SSD support, better cooling, OLED display, and a clean mini-tower aesthetic.

Goal: Boot from internal NVMe SSD (reused ROG Ally 2230 drive with extender), daily driver for Linux practice, and potential media server.

## Initial Setup (CanaKit Kit: Success)

- Unboxed and assembled the Turbine case (heatsink + low-noise fan). Easy 5 mins.
- Booted from pre-loaded 128GB microSD with Raspberry Pi OS. Worked perfectly out of the box.
- Connected to curved ultrawide monitor (HDMI), keyboard/mouse. Initial wizard completed.
- Fixed initial overscan ("out of bounds" desktop) by switching to X11 compositor in raspi-config.
- Updated system (`sudo apt update && sudo apt full-upgrade`).
- Installed basic apps (VS Code, Obsidian, Proton VPN, etc.) via apt/DEB/AppImage.
- External Sharge SSD enclosure (ROG Ally drive) detected fine via USB for storage/testing.
- Everything ran smoothly on microSD. Pi 5 felt snappy for browsing, coding, and learning terminal commands.

## Pitower Tower Case Upgrade & Issues Encountered

Ordered Elecrow Pitower bundle (tower case, PCIe adapter, RGB fans, OLED, safe shutdown, NVMe support).

### Assembly (30-60 mins):
- Followed included guide. Board mounting, ribbon cable, SSD on extender, fan/OLED wiring.
- Looks pro (RGB lighting, OLED shows stats). No obvious damage.

### Boot/SSD Detection Issues:
- After assembly, bootloader screen appeared but no NVMe/PCIe option in menu. SSD not detected.
- Tried reseating PCIe ribbon cable multiple times (orientation checked per guide. Printed side out/in).
- Updated firmware repeatedly (`sudo rpi-eeprom-update -a`).
- Added config.txt lines (`pcie_aspm=off`, `pcie_x1=1`, `dtparam=pcie0/nvme`). No change.
- External USB SSD boot worked, but internal NVMe never showed.

Conclusion: Likely subtle ribbon contact or enclosure PCIe quirk. Common in tower cases but frustrating.

### HDMI Black Screen Issue:
- When booting from microSD in Pitower, HDMI worked during bootloader but went black after OS load.
- Replugging HDMI, different cables/ports, monitor settings (PC Mode). No fix.
- SSH worked headlessly. Pi running fine.
- Tested on newer monitor: Worked perfectly. Confirmed older Samsung monitor (2015-era) incompatibility with Pi 5 HDMI signal in rerouted ports.
- Fixes tried (`hdmi_force_hotplug`, `hdmi_safe=1`, group/mode changes). Partial success on newer monitor but not old Samsung.

## Current Status & Decision

- Decided to skip internal SSD for now. Booting from microSD is reliable and fast enough for my use (learning Linux, scripting, light productivity).
- Pitower case stays (looks great, good cooling). Using external drives for storage.
- Older Samsung monitor usable for basic tasks with tweaks; newer monitor for full sessions.
- Overall, the Pi 5 is up and running as a daily driver. Great for terminal practice, VS Code syncing with laptop, and app testing. SSD/NVMe can be revisited later if needed.

## Next

Media servers (Jellyfin + Audiobookshelf) when ready. 🚀
