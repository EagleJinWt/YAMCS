
# Yet Another MultiColor Solution (For klipper 3D Printers)

**Status:** WIP / Early Concept  
**Goal:** Get the most out of existing parts without reinventing the wheel  

---

## Overview  

This project explores a *low-cost*, *hackable*, and *modular* approach to multi-color 3D printing — without the need for expensive AMS systems or complex servo mechanisms.  

Instead of building a completely new multi-material system, the idea is to combine existing, reliable components and let **Klipper** handle the orchestration.  

Think of it as:  
> “Four extruders + one *smart buffer* = a functional multi-color system.”  

---

## Core Components  

| Component | Description |
|------------|-------------|
| **Mainboard** | You don't need this if you board have 4 or more motor slot empty. But having them on a seperate board make things easier to matain. I chosed the Mellow Fly's Micro4, that's exactly what you need. |
| **Stepper Driver** | A4988 will work, but a TMC2209/2208 is recomended for easier configuration. Note: If you are using a board have integraded drivers, for e.g. the Micro4 I used, as the name suggests, drivers are integraded, don't buy extra drivers. |
| **Extruders** | Standard **BMG-style extruders** — one per filament channel |
| **Buffer Unit** | A **Mellow LLL buffer** (A reletively cheap one I found) |
| **Sensors** |  - One **filament sensor** per channel (Insert detection, for trigging the load process)<br> - One **filament sensor** on the toolhead (detection & timing). <br>*You don't need motion detection, regular ones with a microswitch will do it* |
| **Filament Cutter** | Optional, Recommended — Install for hight success rate during color switching. It is not necessary, tip-forming can work after good tuning. Check [Filamatrix](https://github.com/sorted01/Filametrix) if you are using StealthBurner |
| **MOSFETs (x2)** | Used to emulate button presses on the buffer’s control PCB. Anything like a **BSS138** should work, you don't need those heavy load one |
| **4-in-1-out-adapter** | The Bambulab one for P1 serires works best for me, in theory you can use any one that works, even the printed ones |

*No extra mounting hardware needed! It's all off-the-shelf parts so they mount as is!*

Although with that being said, you might still need some extra screws, nuts, etc. Here is some you might want to keep some stock on:

| Name | Description |
|------------|-------------|
| **M3 T-nut** | For mounting the extruder & buffer. Some BMG units might not come with them. Though you should have some left when building the machine |
| **Heat Shrinking Tube** | Before the PCB design came out, or you just don't feel like spending more on a PCB, a heat shrinking tube will help a lot on insulating the MOSFET |

---

## Concept  

One day, I was thinking about adding a buffer to my 3D printer. Then it hit me: four extruders plus one buffer basically equals a perfect multi-color system. It’s simpler and more stable than the servo-based approach, and it solves all those annoying synchronization headaches. Honestly, you don’t even need me to say it—just look at what manufacturers like Bambulab, Creality, and others are doing, and you’ll see they’re basically using the same idea.

But in the DIY 3D printing community, as far as I know, the only one doing something like this is Box Turtle. The problem is, their setup is way too complicated: it uses a custom mainboard to drive those N20 motors and a ton of aluminum extrusion for the structure, which drives the cost way up. Of course, there’s a reason for that—they’re carrying four spools of filament on top.

My approach is different: let a dedicated filament storage box do the heavy lifting (I will include the one I used. It simple, but make sure to check the README in the stl folder.), and then focus on the electronics and mechanics of the multi-color system itself.

You might be thinking, “Wait, isn’t this basically the MMU/ERCF idea?” True, but their designs are still way too complex—they use lead screws, linear rods, and even require servos. So you can think of my approach as a Box Turtle that runs on a regular Klipper board (no special brushed motor driver needed) and uses an off-the-shelf buffer—but without Box part.

---

## Buffer Klipper Intigration (Probalbly he most technical part of this project)
The Mellow LLL Filament buffer uses it's own controller, and cannot talk to klipper directly. However, it have two buttons that handles the manual "Feed" and "Retract" feature. We can use this feature to integrate it into Klipper. Since klipper/MCU-in-general can't control whether a pin can conduct with another pin, we can't simulate buttons on MCU. But we can use two mosfets, and it will perfectly simulate a "Button". Basically, a mosfet have 3 pins, a Gate(G), a Drain(D), and a Source(S). When a voltage higher than a certain threshold is applied between G and S, the D and S will conduct, exactly how buttons works, just in a controlled way. I might release a PCB for that, since leave the mosfet hanging in the air is not the best solution.

### Simplified Flow  
1. Identify the two physical buttons on the buffer PCB (`feed` / `retract`).  
2. Desolder or parallel-connect each to a MOSFET acting as a digital switch.  
3. Control the MOSFET gates using Klipper output pins (e.g. via `SET_PIN`).  
4. Klipper now “presses” the buttons for you — fully integrated.  

This keeps the buffer’s internal logic intact while giving Klipper total control.  

---

## Advantages  

- **Cost-effective:** Reuses existing extruders and components.  
- **Flexible:** Works with any number of filament channels (limited only by your setup).  
- **Klipper-native:** All logic and sequencing can live inside your Klipper macros, no ```/extra/whatever.py``` needed. 
- **Open idea:** Modular and adaptable — just build what you need.  

---

## Current Status  

This project is currently **in the concept/WIP stage**.  
Prototyping is ongoing, and the design may evolve.  

If you decide to try it out or improve upon the concept, please **open an Issue** or **submit a PR** — I’d love to see your version of it!  

---

## Philosophy  

> *“Don’t reinvent the wheel — make it spin smarter.”*  

This project aims to leverage existing, battle-tested parts in new ways rather than designing everything from scratch.  

If you believe in clever hacks and low-cost innovation, you’re in the right place. 

---

## Future Plans  

- Add wiring diagrams and example Klipper macros  
- Share example toolhead sensor integration  
- Document color change timing optimization  
- Test real-world performance on ABS and PLA  

---

## Related link

[ClockWork 2 filament sensor](https://www.printables.com/model/292186-stealthburner-clockwork-2-filament-sensor)

[Mellow LLL Filament Buffer Item Page](https://es.aliexpress.com/item/1005007265643359.html)

[Spool holder & rewinder](placeholder.com)

---

**License:** MIT  
**Contributors:** You (hopefully!)  
