# razer-stealth-late-2017-kblrefresh-linux
Fix things on the Razer Stealth (late 2017) with an Intel i7-8550U CPU on GNU/Linux

fixed
- heavy flicker and screen distortion
- PCIe Bus Error (no problems experienced however)
- touchpad jitter / jumpy cursor in the center of the touchpad (with Linux 4.15 RC6 it's a little reduced)

partially fixed:
- closing the lid causes the system to infinitely resume and suspend (suspend loop)
  - closing the lid for the first time with AC connected = no sleep (green light still lit)
  - closing a second time suspends the laptop

not fixed:
- lid closed, machine wakes up for some reason (like wake on lan or a problematic USB-C to Displayport adapter) and won't go to sleep again
- HDMI:
  - flickering and outages for a few seconds here and there
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

## install Kernel 4.15+
in combination with the boot parameter i915.edp_vswing=2 and kernel 4.15 the flicker is gone and I haven't seen one flicker since (after a week of work and after that).

Ubuntu users should use the latest mainline Kernel:
https://wiki.ubuntu.com/Kernel/MainlineBuilds

Fedora users should use the Rawhide Kernel:
https://fedoraproject.org/wiki/RawhideKernelNodebug

## install Kaby Lake firmware if needed (Ubuntu)
https://01.org/linuxgraphics/downloads/firmware

Download the newwest versions, extract them and copy the .bin files to /lib/firmware or run each install.sh script.

**In combination with kernel 4.15+ and the proper firmware files the flickering on the internal is gone.**

**HDMI output still flickers when the iGPU is under some load (video decoding, compositoring)**

I tried Ubuntu 17.10 and Fedora 27. All these steps apply to both and I guess to every GNU/Linux distribution.

For a company that once said it wants to fully support GNU/Linux an all it's devices the result is kind of disappointing. Especially because the mentioned problems also exist in previous Stealth and Blade machines (lid close, flickering screen and the jumpy touchpad).

I won't investigate this case any further.
