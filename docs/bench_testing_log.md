# My Bench Testing Diary

Here's my log of all the testing I've been doing on the bench. 

---

### Sept 25, 2026: Finally got the motors starting together!
Reflashed the board today to the proper `DAKEFPVF405` target. Before doing this, Motors 1 and 2 were starting way later than 3 and 4. I did a full ESC calibration sweep (2000 to 1000) using the Betaflight motor tab. Also got the FlySky receiver setup on the UART port. Now, all four motors start up beautifully at almost the exact same time (~1048µs). Big win for today!

### Testing the Radio Link
Plugged in the LiPo and tested the radio link. Everything is showing up perfectly in the receiver tab – no weird dropped frames or anything. It's super responsive.

### Setting up Switches
Mapped out my switches on the FlySky transmitter. I have AUX 1 set to arm the drone (safety first!), and I put ANGLE mode on the VRB knob so I can test self-leveling on the bench without it going crazy.

### Leveling the Drone
Just put it on my desk, leveled it out, and hit "Calibrate Accelerometer" in Betaflight. Looks perfectly flat on the 3D model now.

### Checking Motor Directions
I spun up each motor to make sure they were turning the right way for my quad-X setup. They all spin right, though Motor 4 felt a little stiff by hand. Might just be a tight screw, but I'll keep an eye on it. Oh, and I finally sketched out a quick diagram for the spin directions and saved it in the schematics folder so I don't forget.

### Tuning the Filters
The 500mm frame with big 10-inch props shakes quite a bit. I lowered the gyro and D-term filter multipliers to 0.8 just to smooth out the noise. This should help keep the ESCs from getting super hot while it tries to correct tiny vibrations.

### Tweaking the PIDs for this beast
These big props and old analog ESCs needed some custom PID tuning. I completely turned off Feedforward so it doesn't twitch on the bench. I also cut the P and D gains in half to stop it from over-correcting, and bumped the I-gain up to compensate. Seems a lot more stable for testing now!

### The Tether Setup
Getting ready for live power testing. Tied the drone down to a massive 15kg dumbbell. I am NOT letting this thing fly across the room again. 

### Investigating Weird Noises
I noticed the motors were creaking a lot at idle. Realized it was the low PWM frequency making the stators vibrate. Also figured out why Motor 4 felt weird earlier – when it's powered, the ESC basically locks up because of a shorted MOSFET acting like a brake. Super annoying, but at least I know what's broken now!

### The Crash
Yeah... I tested it untethered before figuring out the Motor 4 issue. Spun it up a bit, the PID loop went nuts trying to level it, and when I killed the throttle, Motor 4 braked instantly while the others kept spinning. Flipped right into a wall. Two props dead, one cracked. I'm taking a break until my new parts arrive!

### Sept 25, 2026: ESC Repair & Pre-Tuning Setup
**The Repair:**
Successfully swapped out the dead ESC for Motor 4. Instead of re-wiring the whole arm, I just desoldered the power/signal wires from the old board and soldered them straight to the new one.

**Results:**
- Motor 4 powers up beautifully.
- The weird electromagnetic braking (shorted MOSFET) is completely gone.
- Spun it up briefly and confirmed it's rotating in the right direction!

**Next Steps:**
Going to recalibrate all four ESCs together to ensure perfect synchronization, and then it's time to strap it to the anchor and get into the PID tuning!

---

### Sept 25, 2026: ESC Calibration & Anchor Prep
**Calibration Success:**
- Recalibrated all four ESCs simultaneously. The synchronization is spot on!
- Set the motor idle up to **7.0%** to ensure reliable, stutter-free startup and smooth commutation across the legacy hardware.

**Next Phase:**
Preparing the 15kg dumbbell anchor setup. It's time to run tethered live-power tuning tests to dial in the PIDs!
