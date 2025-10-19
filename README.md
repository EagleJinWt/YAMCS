# Project: Yet Another MultiColor Solution (For klipper 3D Printers)
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
| **Main Control** | Klipper-based mainboard (any board with spare outputs will do) |
| **Extruders** | Standard **BMG-style extruders** — one per filament |
| **Buffer Unit** | A **Mellow LLL buffer**, originally a standalone system with its own controller & Motor |
| **Sensors** | - One **filament sensor** per channel (entry)<br>- One **filament sensor** on the toolhead (detection & timing) |
| **Toolhead Cutter** | Optional — for cleaner color transitions |
| **MOSFETs (x2)** | Used to emulate button presses on the buffer’s control PCB |

---

## Concept  

One day, I was thinking about adding a buffer to my 3D printer. Then it hit me: four extruders plus one buffer basically equals a perfect multi-color system. It’s simpler and more stable than the servo-based approach, and it solves all those annoying synchronization headaches. Honestly, you don’t even need me to say it—just look at what manufacturers like Bambulab, Creality, and others are doing, and you’ll see they’re basically using the same idea.

But in the DIY 3D printing community, as far as I know, the only one doing something like this is Box Turtle. The problem is, their setup is way too complicated: it uses a custom mainboard to drive those N20 motors and a ton of aluminum extrusion for the structure, which drives the cost way up. Of course, there’s a reason for that—they’re carrying four spools of filament on top.

My approach is different: let a dedicated filament storage box do the heavy lifting (I’ve included the design I use in the repository; you just need a standard off-the-shelf storage box, pick the right size, drill a few holes, print the parts, and mount them), and then focus on the electronics and mechanics of the multi-color system itself.

You might be thinking, “Wait, isn’t this basically the MMU/ERCF idea?” True, but their designs are still way too complex—they use lead screws, linear rods, and even require servos. So you can think of my approach as a Box Turtle that runs on a regular Klipper board (no special brushed motor driver needed) and uses an off-the-shelf buffer—but without Box part.

##Buffer Klipper Intigration (Probalbly he most technical part of this project)
The Mellow LLL Filament buffer uses it's own controller, and cannot talk to klipper directly. However, it have two buttons that handles the manual "Feed" and "Retract" feature. We can use this feature to integrate it into Klipper. Since klipper/MCU-in-general can't control whether a pin can conduct with another pin, we can't simulate buttons on MCU. But we can use two mosfets, and it will perfectly simulate a "Button". Basically, a mosfet have 3 pins, a Gain(G), a Drain(D), and a Source(S). When a voltage higher than a certain threshold is applied between G and S, the D and S will conduct, exactly how buttons works, just in a controlled way.

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

**License:** MIT  
**Contributors:** You (hopefully!)  
