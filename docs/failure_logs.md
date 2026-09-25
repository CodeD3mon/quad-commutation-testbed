# What Broke & How I Fixed It

This is where I'm keeping track of all the weird issues I ran into during the upgrade, so I don't go crazy trying to remember how I fixed them!

### Issue 1: Two motors just wouldn't spin
- **What happened:** Motors 1 and 2 worked fine, but 3 and 4 were completely dead.
- **The fix:** Turned out there was a timer collision because the STM32F405 was trying to run DSHOT. I just switched it back to legacy analog PWM (400Hz) and they woke up!

### Issue 2: Radio stopped talking to the drone
- **What happened:** After a firmware flash, I completely lost my FlySky IBUS connection.
- **The fix:** I accidentally wiped the UART settings. I just had to go back into Betaflight and reassign UART2 to Serial RX.

### Issue 3: Flashed the wrong firmware (oops)
- **What happened:** I accidentally flashed the `SPEEDYBEEF405V3` target instead of `DAKEFPVF405`. 
- **The fix:** Re-flashed it with the right target, recalibrated all the ESCs together (2000µs to 1000µs), and now all four motors start up perfectly at the same time. 

### Issue 4: Motors were making a weird creaking sound
- **What happened:** When armed at idle, all the motors were making this weird groaning/creaking noise.
- **The fix:** The low PWM frequency (400Hz) was causing some stator chatter. I raised the idle throttle just a tiny bit (to 1065) and bumped the PWM rate to 480Hz. It runs super smooth now!

### Issue 5: The cursed Motor 4 (Shorted ESC)
- **What happened:** Motor 4 would spin freely when off, but as soon as I plugged the battery in and dropped throttle, it felt like it had the brakes slammed on. 
- **The fix:** Confirmed it was a shorted MOSFET in the ESC that was acting like an electromagnetic brake. 

### Issue 6: The Bench Incident (RIP Propellers)
- **What happened:** I was testing on the bench (not tied down) in ANGLE mode. I gave it a tiny bit of throttle, the PID loop freaked out (windup), and when I dropped the throttle, Airmode kept trying to balance the drone. Because Motor 4's ESC was braking hard, it pitched violently, flew into the wall, and destroyed my propellers.
- **The fix:** Lesson learned: **ALWAYS tie it down to the 15kg dumbbell anchor when testing with props on!**

### Next Steps: Repairing the crash damage!
Alright, so I just got my hands on the new propellers and a replacement ESC. Here is my plan for today:
1. Instead of taking the whole drone apart and re-routing wires, I'm just going to desolder the power and signal wires from the old ESC board. Then, I'll take those pre-routed wires and solder them directly onto the new ESC board (after removing the factory wires it came with). Much easier!
2. Calibrate all the ESCs together again.
3. Test the new ESC to make sure it runs smooth and doesn't have that weird electromagnetic braking or any current leaks.
4. Add the new props, strap this thing down to my anchor, and get ready for some tuning tests!
