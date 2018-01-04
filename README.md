# razer-stealth-late-2017-kblrefresh-linux
Fix things on the Razer Stealth (late 2017) with an Intel i7-8550U CPU on GNU/Linux

fixed
- heavy flicker and screen distortion
- closing the lid causes the system to infinitely resume and suspend (suspend loop)
- PCIe Bus Error

partially fixed
- touchpad jitter / jumpy cursor in the center of the touchpad (with Linux 4.15 RC6 it's reduced)

not fixed:
- HDMI:
  - No 4k@60Hz via HDMI port (DisplayPort over USB-C works fine)
  Workaround: find an USB-C-adapter with (Mini)Displayport that supports 4k@60Hz and passthrough charging (there are one or two)
  
When I find more issues I'll document them here.

## Kernel Boot Parameters
**pci=nomsi i915.edp_vswing=2 i915.enable_rc6=1 button.lid_init_state=open**

**pci=nomsi** "fixes" the following error I got all the time (altough I didn't experience any problems):
```
computername kernel: pcieport 0000:00:1c.0: PCIe Bus Error: severity=Corrected, type=Data Link Layer, id=00e0(Transmitter ID)
computername kernel: pcieport 0000:00:1c.0:   device [8086:9d12] error status/mask=00001000/00002000
computername kernel: pcieport 0000:00:1c.0:    [12] Replay Timer Timeout
```

**i915.edp_vswing=2** in combination with Linux 4.15 fixes the flickering for the internal display.

**i915.enable_rc6=1** works fine and I didn't experience any issue with it (additional power saving states for the iGPU)

**button.lid_init_state=open** "fixes" the suspend-resume-loop when closing the lid. Essentially it tells the kernel the initial lid-state like it says. The lid state otherwise is unknown to the kernel. Theoretically it could be a problem if the computer is woken up while the lid is closed (wake on lan or a timed suspension).
When I have the computer connected to a "chenyang usb-c USB 3.1 Typ C to Mini DisplayPort DP & USB 2.0 OTG" (incl. charging capabilities) it wasn't able to suspend longer than 20 to 30 minutes or so. I couldn't see any entries in the journal. ~~Perhaps it has something to do with the the PCIe error message. I will try with the **pci=nomsi** parameter in the next few days and lets see if the error is corrected.~~

## install Kernel 4.15+ (RC6 at least)
in combination with the boot parameter i915.edp_vswing=2 and kernel 4.15 the flicker is gone and I haven't seen one flicker since (after a week of work and after that).

4.15 RC6 reduces the touchpad jittering (in Xorg it's practicable but not on Wayland because of the higher sensivity with HiDPI displays)

## install Kaby Lake firmware
https://01.org/linuxgraphics/downloads/firmware

**In combination with kernel 4.15+ and the proper firmware files the flickering on the internal and HDMI is gone.**

I tried Ubuntu 17.10 and Fedora 27. All these steps apply to both and I guess to every GNU/Linux distribution.
