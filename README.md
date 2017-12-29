# razer-stealth-late-2017-kblrefresh-linux
Fix things on the Razer Stealth (late 2017) with an Intel i7-8550U CPU on GNU/Linux

fixed
- heavy flicker and screen distortion
- closing the lid causes the system to infinitely resume and suspend (suspend loop)

not fixed:
- touchpad jitter / jumpy cursor in the center of the touchpad (newest libinput and kernel haven't resolved this issue)

*I have not experienced any other issue.*

## Kernel Boot Parameters
**i915.edp_vswing=2 i915.enable_rc6=1 button.lid_init_state=open**

i915.edp_vswing in combination with Linux 4.15 fixes the flickering. No other solution I read about earlier Razer notebooks worked for me. I still had this vswing-fix in mind because it also worked on an XPS 15 9550 at a time.

i915.enable_rc6 works fine and I didn't experience any issue with it (additional power saving states for the iGPU)

button.lid_init_state "fixes" the suspend-resume-loop when closing the lid. Essentially it tells the kernel the initial lid-state like it says. The lid state otherwise is unknown to the kernel. Theoretically it could be a problem if the computer is woken up while the lid is closed (wake on lan or a timed suspension) - I haven't tested this cases.
When AC is connected and you close the lid the very first time you have to do it twice -once-. After that it suspends and resumes flawelessly. I tested nearly every use cases, plugged cables, AC, unplugged them, closed and re-opened the lid - it works fine.

## install Kernel 4.15+
in combination with the boot parameter i915.edp_vswing and kernel 4.15 the flicker is gone and I haven't seen one flicker since (after 2-3 normal work days and after that).

I tried Ubuntu 17.10 and Fedora 27. All these steps apply to both and I guess to every GNU/Linux distribution.
