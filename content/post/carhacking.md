+++
title = "Car Hacking Update"
description = ""
tags = [
    "Hacking",
]
date = "2023-11-17"
categories = [
    "Automotive",
]
+++

I've been talking shit about hacking cars recently, and now for the latest update.

Good news. I just wanted to share that I was able to install some new modules/ECUs, and the car didn't seem to mind. There were no security checks. The module I added is from the same manufacturer, but if someone has the time, they could build their own module and slip it into their car.

I had this idea to amp up the car with a combination of traction and stability control, useful for some specific off-road situations. So, first things first, I set out to get a copy of the workshop manual for the car - which contains detailed wiring diagrams of all components. Reading through the manual, I discovered that the *Electronic Brake Control Module* (EBCM) was in charge of the feature I was looking for.

I had two options, either and modify the firmware on my unit, or get a different model of EBCM.

> Note: Reverse engineering a firmware from scratch is time-consuming and needs mad skills...

The most reasonable option was to get a different model of EBCM that already had the specific feature I needed.

After further review of the workshop manual, I found the wiring diagram, turns out, this fancy new EBCM wasn't a solo act – it wanted a few comrades to join the party. A *Steering Angle Sensor* (SAS), a slick off button for the stability and traction duo, a fresh face on the block called the stop lamp relay, and some changes in the brakes wiring. Mainly to connect the stop lamp relay with a few new wires into the EBCM main control.

### Verification
Before purchasing the required components, I needed to make sure if my plan would actually work. I started to review the wires directly from my car, to my surprise, the control for the *Steering Angle Sensor* (SAS) and the off button were already chillin' in there. It seemed that the required components for the new feature should interact with each other, so there was only one missing piece of the puzzle: will the new components play nice with all the other control modules in the gang?

### Variant coding
I performed some additional research and stumbled upon something car manufacturers call *variant coding*, which is basically a code that tells all control modules which features they have enabled and which are disabled. This code is the boss, sitting pretty on the BCM — so without the ability to modify the variant code, the new EBCM wouldn’t work. Fortunately, there are open source tools out there that can help do exactly that.

I purchased all the required parts, then, it was a plug-and-play party – plugged them in and rewired everything according to the specifications in the workshop manual.

Snapshot below – that's the EBCM main plug, the epicenter of my rewiring shenanigans.

After turning the car on, as anticipated, it wasn't all smooth sailing. A bunch of diagnostic trouble codes (DTCs) flashing on the screen. The new EBCM was throwing a little tantrum because it was craving a variant code with the green light for the new feature.

It turned into several hours of trial and error, I'm in there, pulling off a Houdini with the car battery – disconnect, tweak the variant coding, reconnect, repeat. And finally, just when I was about to call it quits, the variant code for my exact car make, model, and wishlist of features emerged from the chaos.

Most of those pesky DTCs were history at this point, and all that was left to do was SAS calibration and a chat with the ECM. Made the ECM aware that the new module was available, (this was done with a decoder software).

Finally, I went out for a test drive and the newly added stability and traction feature tag team seemed to be doing their job. But, something was missing. There was supposed to be a light on the dashboard that would tell me each time the module was engaged, but I did not see that light popping up during the test drive.

Off came the dashboard. Lo and behold, after the grand revelation, it was clear as day – the LED for that indicator, along with a few resistors and transistors, were MIA. Fortunately, the circuit board had the space to solder in all those components.

> Note: This whole experiment was a bit of a time investment and a minor research, but notice that there were no security controls preventing my plans?

The same methods I used to perform these modifications could allow a criminal to embed a custom piece of hardware and control almost any feature on a vehicle, ranging from sliding in a custom piece of hardware and gain control over almost any feature on a vehicle – we're talking brakes, acceleration, windshield wipers, even the mic. It's as straightforward as assembling a Raspberry Pi, grabbing a CAN bus interface and some wireless mojo, then plugging it into a car's CAN bus.

Doing this would require physical access to the vehicle. BUT, connected cars are becoming more and more common these days and that could only make things easier for remote attackers in the future.

To sum it all up, here are some key takeaways from this personal project:

- The variant code tells the car's control modules, letting them in on which features are active and which ones are taking a snooze.
- It's like a computer party – multiple ones, each managing very specific tasks, that are interconnected through a local network within the car.
- Components from different models made by a manufacturer may work if they are properly configured and wired.
- Car control modules can be pretty trusting – they may trust whatever information is sent on the local network and may not perform any kind of validation of the packets or the authenticity of other control modules or devices connected to the CAN bus.
- Most importantly, anyone with enough knowledge and time can create new modules. This can be both good or bad, and with malicious intent, an attacker could implement a module to control your car remotely.

### Fin
Any device that can wirelessly provide access to the internal CAN bus of a vehicle is a potential new attack vector, making it critical to implement proper vehicle cybersecurity measures.

Modern vehicles are all equipped with numerous internal computers, a network-like central, and controls are pretty bare minimum. And with connected cars already in our daily lives, its time to develop and implement controls into every new model.
