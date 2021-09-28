---
title: 'Building an Animatronic Dinosaur'
media_order: 'DSC00766_mouth_servo_1_sm.jpg,DSC00767_mouth_servo_2_sm.jpg,DSC00768_arm_servo_sm.jpg,DSC00770_power_sm.jpg,DSC00773_final_hardware_sm.jpg,IMG_9417_mec_manual_sm.jpg,IMG_9418_mec_box_components_sm.jpg,IMG_9420_mec_box_outside_sm.jpg,IMG_9421_meccasaur_brain_manual_sm.jpg,IMG_9422_mec_meccasaur_brain_manual_2_sm.jpg,IMG_9426_mec_motor_sm.jpg,IMG_9429_uno_prototype_sm.jpg,IMG_9430_soldering_sm.jpg,IMG_9432_headers_sm.jpg,IMG_9433_raspi_sm.jpg,IMG_9435_headers_cut_sm.jpg,IMG_9438_raspi_soldering_sm.jpg,IMG_9439_raspi_soldered_sm.jpg,IMG_9440_raspi_breadboard_sm.jpg,IMG_9441_raspi_hooked_up_sm.jpg,IMG_9442_raspi_boot_up_sm.jpg,IMG_9444_raspi_arduino_prototype_sm.jpg,IMG_9447_raspi_arduino_wired_up_sm.jpg,IMG_9452_arduino_size_compare_sm.jpg,IMG_9453_arduino_raspi_powered_ubec_sm.jpg,IMG_9456_servo_sm.jpg,IMG_9471_mouth_servo_sm.jpg,IMG_9472_all_wired_up_sm.jpg,IMG_9474_power_sm.jpg,DSC00759_full_dino_sm.jpg,DSC00760_full_dino_sm.jpg,DSC00762_meccasaur_full_power_sm.jpg,DSC00764_control_board_sm.jpg,DSC00765_posing_sm.jpg,controller_eagle.zip,controller_schem1.png,IMG_9453_arduino_raspi_powered_ubec_2_sm.jpg,SoftwareArch.PNG,Figment02.jpg'
published: true
date: '2019-01-06'
publish_date: '2019-01-06 20:05'
categories:
    - Projects
    - Blog
tags:
    - projects
    - hardware
    - software
    - robotics
header_image: '0'
slug: animatronic-dinosaur
---

I'm a huge fan of themed entertainment, especially the Disney parks. Theme park rides are some of the most interesting engineering marvels out there and the best part is - you can visit them! For me, the enjoyment of a trip to Walt Disney World comes as much from appreciating the technology and systems used to create rides as it does from the theming and story. A core part of many theme park rides are the animatronic characters that bring the experiences to life. I was recently gifted a Meccano Meccasaur - a toy with some limited "smart" functionality. I wanted to see how much I could augment this functionality if I added a new brain and some more points of articulation.

<!--more-->

#### Background

Animatronics (Disney calls them Audio-Animatronics) are robots which are animated and whose movements are synchronized with an audio track. Historically, the "attraction quality" animatronics are hydraulically actuated, but some of the newer ones are fully electronic. They run the gamut from simple back and forth motions like the children in Disney's "It's a Small World" to the incredible [Shaman of Songs](https://www.youtube.com/watch?v=bkvYbp31hM8) in Disney's Na'vi River Journey (seriously: this thing is incredible, the motions are incredibly fluid and it really looks alive).

The [Meccasaur](https://www.spinmaster.com/product_detail.php?pid=p20733) is a robotic dinosaur toy that's Meccano's entrant into the recent "smart toys" craze. Out of the box it comes with an embedded microcontroller they call the "Meccasaur Brain" as well as a motorized module to move the head and legs and a multicolor LED module for eyes. By my reckoning, the Meccasaur components seem to be dumbed-down versions of the ones that ship with Mecanno's other, more programmable "Meccanoid" robots. The Meccasaur Brain for instance seems to be a "hardwired to dinosaur" version of the more customizable "MeccaBrain" that powers the other robots.

#### Top Level Design

I designed the control system for this project based on my understanding of how actual show control systems are set up. The core of this design is the seperation of concerns between the overall show controller managing the cues and the subsystems triggering the individual motions. For the high-level show controller, I selected a Raspberry Pi. This runs a Python program which transmits the cues to the low-level system triggering the motions and also plays the audio track. For the low-level system, I selected an Arduino. This runs a C++ program which operates the servos and LED eyes. The Raspberry Pi communicates with the Arduino using the [I2C](https://en.wikipedia.org/wiki/I2C) (I-squared-C, or "Inter-Integrated Circuit") protocol. I2C is a serial protocol used for connecting up microcontroller style devices. I chose it because it's well supported by the Raspberry Pi and Arduino via libraries. It also only requires two wires making the circuit design simple. The last time I used it was with [my train project]({{< ref "2015-12-13-track-independent-train-mapping-and-identification" >}}), so I got a kick out of coming back to it again!

#### Parts List

I purchased a few new things to prepare for this project. I decided I wanted to go for a small footprint for the controllers so I got a Raspberry Pi Zero W (this is my 4th Raspberry Pi for those counting), a Mini Nano ATmega328P Arduino, and a five pack of cheap servo motors. I already had the UBEC from my [ADS-B receiver project]({{< ref "2016-04-01-ads-b-receiver-piaware" >}}). Below are the links for the products:

<ul>
 <li><a rel="nofollow" href="https://www.amazon.com/gp/product/B06XCYGP27/ref=oh_aui_detailpage_o02_s00?ie=UTF8&psc=1">Raspberry Pi Zero W</a> - Amazon ~$24</li>
 <li><a rel="nofollow" href="https://www.amazon.com/gp/product/B00NLAMS9C/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1">Mini Nano ATmega 328P Arduino</a> - Amazon ~$9</li>
 <li><a rel="nofollow" href="https://www.amazon.com/gp/product/B015H5AVZG/ref=oh_aui_detailpage_o09_s00?ie=UTF8&psc=1">5 Pack J-Deal Micro Servo</a> - Amazon ~$12</li>
 <li><a rel="nofollow" href="https://www.amazon.com/gp/product/B008ZNWOYY/ref=as_li_tl?ie=UTF8&psc=1">Hobbywing 5V/6V 3A Switch-mode UBEC</a> - Amazon ~$7</li>
</ul>

#### Hardware Design

The first step in prototyping the "hardware" was construction. I built the Meccasaur to the factory specs using the included instructions. They were pretty good and it was rewarding to go from a box of parts to a giant dinosaur.

![](/blog-static/animatronic-dinosaur/IMG_9418_mec_box_components_sm.jpg?lightbox&resize=700)<br>_This will soon be a dinosaur_

I designed a schematic for this project using [EAGLE](https://www.autodesk.com/products/eagle/overview). This illustrates how the Pi and the Arduino connect to one another and to the three servos (one for the mouth, and one for each arm). The small rectangular component on the bottom left is the LED and embedded controller for the Meccasaur's eyes; more on that later.

![](/blog-static/animatronic-dinosaur/controller_schem1.png?lightbox&resize=700)<br>_Project schematic_

I wanted to get spun up on the Arduino programming before the new parts I had ordered arrived so I started the project with a full sized Arduino Uno (actually a knockoff made by OSEPP) that I already had laying around. Since I didn't have any of the servos yet, I investigated what I could do with the existing Meccano hardware...

All Meccano peripherals use a serial protocol called MeccaBrain. The Meccasaur isn't officially branded as a MeccaBrain-compatible product, but I figured that since they offer a three wire multicolor LED as part of MeccaBrain and the LED that came with the Meccasaur is a three-wire multicolor LED that it was probably the same thing, or at least compatible. Meccano nicely provides [documentation and an Arduino library](http://www.meccano.com/meccanoid-opensource) to assist with interfacing with their products. I implemented this to talk to the LED, but from my testing it seemed unreliable at best. I had to send the MeccaBrain messages multiple times in order to get the LED to change colors, and even then sometimes it wouldn't change. With that experience in hand, I decided to not bother with the Meccasaur servos.

Servo motors are pretty cool. They're a combination of a motor and a sensor that measures the motor's position. This allows for it to accurately rotate to a specified angle. By attaching things to the motor, you can make real-world objects respond directly to coded instructions. They're also inexpensive, so are a great way to get started with a project like this one. Most servos use a three-wire configuration for power, ground, and data. Using the Servo library provided by Arduino, they're very easy to interface with.

![](/blog-static/animatronic-dinosaur/IMG_9456_servo_sm.jpg?lightbox&resize=500)<br>_J-Deal Micro Servo_

Once the J-Deal servos arrived I had to modify the Meccasaur to find a way to attach them. For the arms it was pretty easy, I removed them and just attached them by screwing them directly to the arm of the servo. For the mouth, I had to get a little more creative. I initially tried mounting the servo on the side of the jaw, to drive it directly, but [couldn't get it to work](https://www.youtube.com/watch?v=kom5TORWlLY). I then realized that the mouth naturally falls to an open position due to gravity. I ran some fishing line to the bottom jaw and attached it to a servo mounted behind the head. The servo lifts the jaw by pulling on the fishing line. It worked great and turned out to be a more aesthetically pleasing location for the servo to go anyway.

![](/blog-static/animatronic-dinosaur/DSC00768_arm_servo_sm.jpg?lightbox&resize=700)<br>_Arm mounted directly on servo_

![](/blog-static/animatronic-dinosaur/DSC00766_mouth_servo_1_sm.jpg?lightbox&resize=700)<br>_Servo connected to mouth with fishing line_

I ordered a Raspberry Pi Zero W without pins attached. I determined that if I attached a minimal amount of pins to the Pi I could make it fit on the same small breadboard as the Mini Arduino. Time to break out the soldering iron!

![](/blog-static/animatronic-dinosaur/IMG_9430_soldering_sm.jpg?lightbox&resize=700)<br>_My outdoor soldering station_

![](/blog-static/animatronic-dinosaur/IMG_9438_raspi_soldering_sm.jpg?lightbox&resize=700)<br>_First set of pins attached_

![](/blog-static/animatronic-dinosaur/IMG_9439_raspi_soldered_sm.jpg?lightbox&resize=700)<br>_All pins soldered_

![](/blog-static/animatronic-dinosaur/IMG_9440_raspi_breadboard_sm.jpg?lightbox&resize=700)<br>_Rasberry Pi attached to breadboard_

After attaching the pins to the Raspberry Pi and attaching it to the breadboard I flashed it with Raspbian and made sure it booted.

![](/blog-static/animatronic-dinosaur/IMG_9442_raspi_boot_up_sm.jpg?lightbox&resize=500)<br>_Boot up success!_

The final hardware step was to put it all together. I attached the Arduino to the breadboard along with the Pi and wired it up.

![](/blog-static/animatronic-dinosaur/DSC00773_final_hardware_sm.jpg?lightbox&resize=700)<br>_Hardware connected; red and black wires are power and ground and brown in the middle are I2C_

![](/blog-static/animatronic-dinosaur/IMG_9453_arduino_raspi_powered_ubec_2_sm.jpg?lightbox&resize=700)<br>_Plugged in and powered up_

With the hardware configured, it was time to write the software for both boards.

#### Software Design

I developed three programs to control the animatronic. `action_recorder`, `control`, and `actuator`.

![](/blog-static/animatronic-dinosaur/SoftwareArch.PNG?lightbox&resize=850)<br>_Software architecture_

`action_recorder` is a Python program that runs on the Raspberry Pi. It provides the user the ability to easily generate timing cues for the animatronic. I initially didn't plan on writing this program, just recording the cues manually. After attempting to synchronize some mouth movements manually with the audio track, I realized it would be much more efficient to write something that would allow for easily recording a "rough cut" of the motion which I could then fine tune later. The program is pretty simple: the user executes it at the same time as starting the audio track. As the audio plays, the user presses the space bar to annotate when the mouth should open. The specified cues are then timestamped and recorded to a CSV file which the `control` program can interpret. These cues aren't perfect, but it's a lot easier to fine tune them than generating them manually.

`control` is a Python program that also runs on the Raspberry Pi. It acts as the primary show control program for the animatronic. Cues are loaded from a CSV file into memory and then continually checked against a timer. Once the appropriate time for any given cue occurs, it is translated into a message that is transmitted over I2C to the `actuator` program running on the Arduino. I selected I2C as the protocol to connect these devices as it is an industry standard and has good support on both the Python/Raspberry Pi and Arduino sides. The SMBus Python library provides the I2C capability on the Raspberry Pi side.

`actuator` is a C++ program that runs on the Arduino. It converts the actual cue messages received over I2C from `control` into the actual electrical signals to drive the servos and LED. The I2C messages each contain three fields: a type field, an ID field, and a setting field. The type field indicates the device category, `L` for Arduino LED, `M` for Meccasaur LED, and `S` for servo. The ID field indicates a specific device number for a type. The setting field is contextually based on the device type and is used to actually configure the device state. For the Arduino LED, this is a simple on/off. For the Meccasaur LED, one of seven colors can be selected. Finally, and most importantly, the servo angle can be selected. This is used to control the arms and mouth. I was able to interface with I2C using the Wire library provided for Arduino and the servos with the Servo library. Servos work using PWM (pulse-width modulation) signals to indicate position. As such, they must be connected to PWM capable pins on the Arduino. I wrote my code to handle up to five servos, each attached to a PWM pin, even though I only ended up using three.

All the code for this project is available on GitHub, here: [https://github.com/xtruan/animatronic-control](https://github.com/xtruan/animatronic-control).

#### Testing

For testing I wanted to do something fun that would also lend itself reasonably well to animation. I was able to find the on-ride audio for Disney's [Journey into Imagination with Figment](https://en.wikipedia.org/wiki/Journey_into_Imagination_with_Figment) and figured that was a great candidate. EPCOT is my favorite park at Walt Disney World, and Figment is all about setting your imagination free so I figured he would be a good fit. Plus I could set my dinosaur's eyes to be yellow, so that was an added bonus!

The below video repeats the same section of animation twice. In the first section, I dubbed the audio over the video as the super cheap servos I used turned out to be... cheap. They weren't great at maintaining their position under load and as a result tended to vibrate around the position and vibrate LOUDLY. The second section uses a microphone and actually records the audio track to demonstrate the actual synchronization. However the servo noise is loud enough that it competes with the audio playback in that section.

<iframe style="width:650px; height: 380px; border: none" src="https://www.youtube-nocookie.com/embed/Xdeaxh2WyqI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


#### Final Thoughts

I had a great time with this project. I really think the control system I came up with is pretty representative of how a system like this operates in the real world. With that said, there's definitely a few things that I would change if I were to do it again:

* **Buy better servos** - The servos I bought were cheap, but they were barely up to the job for moving the arms. The arms were too heavy to be effectively driven by the servos directly. Next time I'd either find a better way to attach them, or use beefier servos.
* **Do more experimentation with the Meccasaur drive motor** - The Meccasaur came with a drive motor assembly that was powerful enough to move it around with. I should've done some more experimentation with it to see what was possible. The lack of documentation and general dumbed-down-ness for the Meccasaur was pretty disappointing, especially since it looks great.