# How This Build Came Together

Hey guys, this is a quick overview of how my 500mm quadcopter project evolved and why I chose the hardware I did. I mainly use this as a testbed for learning about embedded systems and control theory.

---

## From 8-bit to 32-bit
I originally started this project using an old APM 2.8 flight controller. It was cool, but honestly, the 8-bit processor was just too slow to handle modern PID filtering. Also, running 1400KV motors on a big 500mm frame with 10-inch props was a terrible idea—it got super hot and flew horribly. 

So, I upgraded to what I call "Generation 2":
* **Frame:** 500mm Quadcopter (like a TBS Discovery clone)
* **Brain:** DakeFPV F405 (Running an STM32F405 chip)
* **Motors:** 1000KV brushless motors (much better suited for 10-inch props)
* **ESCs:** My old reliable 30A analog ESCs
* **Radio:** FlySky FS-i6 and FS-iA6B receiver

---

## The Upgrading Headaches
Moving to a modern STM32 board wasn't exactly plug-and-play. I ran into two huge roadblocks:
1. **Half the motors wouldn't spin:** Motors 1 and 2 worked, but 3 and 4 were dead. 
2. **Radio silence:** My receiver just stopped talking to the board completely after I flashed it.

### Figuring out what went wrong
**The Motor Issue:** Turns out, modern firmware assumes you are using fancy digital DSHOT ESCs. When the board tried to output DSHOT, it created a timer conflict on the STM32 chip, completely killing the signals for motors 3 and 4. 

**The Power Issue:** My old ESCs all have built-in 5V regulators. When I plugged all four of them into the flight controller, they started fighting each other and the FC's own regulator. It created a ton of ground noise!

---

## How I Fixed It

Here's how I got it all working smoothly:
1. **Fixing the Power:** I grabbed my soldering iron and actually disconnected the +5V wire on all four ESC plugs. Now they only share a signal and ground wire with the flight controller, which completely fixed the noise issues.
2. **Dumbing down the signals:** I went into the Betaflight CLI and forced the board to talk to the ESCs using legacy analog PWM (400Hz) instead of DSHOT.
3. **Getting the radio back:** I just had to re-map my receiver to the correct UART port in the configurator.
4. **Syncing the ESCs:** I ran a full PWM calibration sweep from 2000 to 1000, so all four ESCs know exactly when to start spinning.

---

## Why Betaflight instead of ArduPilot?
Honestly, ArduPilot is amazing for autonomous flying, but it's super annoying when you just want to test things on the bench. It constantly complains if you don't have a GPS lock or the compass isn't perfect. 

I switched to Betaflight specifically because it lets me manually spin the motors using the Motor tab. I need this to test my motor commutation and check how hot things are getting without jumping through hoops to arm the drone. I actually stripped out a lot of the extra telemetry and digital features from the firmware just to keep it super lightweight and compatible with my old analog ESCs!
