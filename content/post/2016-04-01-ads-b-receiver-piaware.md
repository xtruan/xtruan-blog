---
title: 'ADS-B Receiver with PiAware'
author: 'Struan Clark'
published: true
date: '2016-01-04'
publish_date: '2016-01-04 23:13'
categories:
    - Projects
    - Blog
tags:
    - projects
    - hardware
header_image: '0'
header_image_file: /blog-static/ads-b-receiver-piaware/ADS-B_MapHeader3.png
header_image_width: 900
header_image_height: 455
slug: ads-b-receiver-piaware
#image: /blog-static/ads-b-receiver-piaware/ADS-B_MapHeader3.png
---

Recently, I've been watching the excellent show <a href="https://en.wikipedia.org/wiki/Mayday_(TV_series)" target="_blank">Mayday</a> (or Air Disasters in the USA). The anatomy of how complex systems break down and fail has always fascinated me, and this show does an awesome job of presenting that information in an engaging fashion. 

<!--more-->

One of the recurring themes in the show is the fact that it's common for air traffic controllers to not have an accurate picture of where specific aircraft are in the sky.

#### Background

Right now (in most of the world at least) aircraft are tracked by radar. There are two types of radar used in air traffic control, primary and secondary. Primary radar is similar to the active sonar you see in submarine movies. Imagine this awesome scene below but with electromagnetic waves instead of sonar pings (and aircraft instead of Soviet torpedoes) and you get the general idea of how primary radar works: the sooner the signal bounces back, the closer the detected object is to the source.

<iframe  style="width:650px; height: 380px; border: none" src="https://www.youtube-nocookie.com/embed/4unk6siO-tI" frameborder="0" allowfullscreen></iframe>

Secondary radar works a little differently. Instead of simply reflecting a signal like with primary radar, the aircraft actually transmits its own signal back using a device called a transponder. There are multiple benefits to this system. The initial radio signal can be transmitted with a lower power since the aircraft has its own transmitter for the return signal. Because the aircraft is transmitting it can also send more information to the ground. The drawback of the system is that the aircraft is no longer a dumb chunk of flying metal; it must have a transponder.

So for those keeping score, the current system in place gives you range and bearing from the primary radar and <a href="https://en.wikipedia.org/wiki/Pressure_altitude" target="_blank">pressure altitude</a> which is transmitted using "Mode C" of a transponder operating as part of a secondary radar system. One additional thing to note is that these three pieces of information are also only available when the aircraft is in range of a powerful land-based transmitter.

ADS-B, or Automatic Dependent Surveillance - Broadcast, is meant to solve this problem. It works kind of like the transponder in secondary radar, except that it doesn't need to be interrogated by a ground station, and it provides a lot more information. An aircraft equipped with ADS-B transmits speed, heading, altitude, latitude, and longitude which it gets from the aircraft's own navigation system (usually using a high fidelity GPS unit) at regular intervals. Coupled with a network of ADS-B ground stations, this technology could eliminate the need for expensive radar facilities. There are also numerous other benefits. Air traffic controllers can have a much clearer picture of the sky, allowing for improvements in safety and efficiency (more accurate aircraft position knowledge allows aircraft to be placed closer to one another allowing for better utilization of air corridors and runways). Other aircraft can receive the signals increasing the pilots situational awareness. Remote airports can automatically turn on the lights when an aircraft gets near. In other words, the <em><a href="http://www.badumtss.net/" target="_blank">sky's the limit</a></em>...

#### Building the Receiver

The other major benefit of ADS-B is that building a ground station can be done on the cheap. Not only that, it can be put to good use by sending the information to the <a href="http://flightaware.com/" target="_blank">FlightAware</a> aircraft tracking service. In exchange for you feeding them information, they upgrade you to an Enterprise account which they normally charge $89.95 per MONTH for. Talk about a sweet deal since the cost of building a receiver is only a little more than that!

I decided I wanted to build my receiver in a waterproof enclosure so I could mount it outside. I also chose to use Power over Ethernet so I only had to run a single cable to my receiver.

#### Parts List

<ul>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B00T2U7R7I/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00T2U7R7I&amp;linkId=MSVMCW3CXPVYWYLH">Raspberry Pi 2 Model B Project Board</a> - Amazon ~$40</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B00MQLB1N6/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B00MQLB1N6&amp;linkId=FQPDYXUFJJCWRX75">Case for Raspberry Pi 2 Model B</a> - Amazon ~$9</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B00P2UOU72/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00P2UOU72&amp;linkId=JJ3MF5SKI76QPBPK">NooElec NESDR Mini 2 SDR</a> - Amazon ~$23</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B010GBQXK8/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B010GBQXK8&amp;linkId=LIX6OL7I6QQLCA3O">ADS-B 1090MHz Band-pass SMA Filter</a> - Amazon ~$20</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B00WZL6WPO/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00WZL6WPO&amp;linkId=QT65UTZLU2RDYG6W">1090MHz ADS-B Antenna</a> - Amazon ~$45</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B008ZNWOYY/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B008ZNWOYY&amp;linkId=HRNETHXCDNZZI5JX">Hobbywing 5V/6V 3A Switch-mode UBEC</a> - Amazon ~$8</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B0192JDWW2/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B0192JDWW2&amp;linkId=JJTI2BDCD6A5AT2V">Power over Ethernet (PoE) injector</a> - I actually had one of these laying around, I linked a similar looking one on Amazon ~$12</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B00CP1129K/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00CP1129K&amp;linkId=XSF2A44SZXU5PM4D">SMA male to MCX male RF coax cable</a> - Amazon ~$6</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B00TX46QR0/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00TX46QR0&amp;linkId=DHE3TALMNOWV7PV7">Type N male to SMA female RF coax cable</a> - Amazon ~$8</li>
<li><a rel="nofollow" href="http://www.walmart.com/ip/Outdoor-Products-Watertight-Box-Small/26674162" target="_blank">Watertight box</a> - Walmart ~$7</li>
<li><a rel="nofollow" href="http://www.amazon.com/gp/product/B00303FYKA/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00303FYKA&amp;linkId=YFDGPVNI3XYZVMRG">Weatherproof cord connector</a> - Amazon ~$18 for 10</li>
<li>A few Ethernet cables of various lengths - I had these. A great place to get cables of all types for cheap is <a rel="nofollow" href="http://www.monoprice.com/" target="_blank">Monoprice</a>.</li>
<li>A USB extension cable - You may not need this, I needed it so I could fit everything in the watertight box.</li>
<li>A microSD card that's at least 8 GB - I used a 32 GB one because I had it already. Make sure you have a way to connect this to a computer.</li>
<li>An RJ45 keystone jack - You probably won't need this if you have an RJ45 crimping tool. I had one and thought it would be easier to use it to make the PoE splitter, now having done it I'm not so sure.</li>
<li>Velcro tape - I had this too, very useful for getting things to stay in place.</li>
<li>Soldering kit - This is only required for connecting a few wires. You could probably get away with using those <a rel="nofollow" href="http://www.homedepot.com/p/Unbranded-Wire-Connector-Assortment-25-Pack-775128/202894260" target="_blank">twisty things from Home Depot</a> if you're uncomfortable soldering.</li>
</ul>

#### Putting It All Together

First, some pictures of the goods. From top to bottom on the cardboard are the NooElec radio, the Raspberry Pi, the ADS-B filter, the Hobbywing UBEC, and the PoE injector. On the right is the ADS-B antenna.

![Most of the major components](/blog-static/ads-b-receiver-piaware/ADS-B_AllMajorComponents_1500.jpg?lightbox&resize=475)
_Most of the major components_

The brain of this project is a brand new Raspberry Pi 2 Model B board. I actually had an old "vintage" Raspberry Pi 1 Model B laying around so I decided to take a comparison shot. The new one has twice as many USB slots, and has ditched the composite video connector. Not to mention that it's 6x faster.

![Old (top) and new (bottom)](/blog-static/ads-b-receiver-piaware/ADS-B_2PisVertical_1500.jpg?lightbox&resize=400)
_Old (top) and new (bottom)_

The first thing I did was flash the PiAware system image to the microSD card. On Windows, you need to download the <a href="http://piaware.flightcdn.com/piaware-sd-card-2.1-3.img.zip" target="_blank">PiAware on Raspbian Linux 2.1-3 ZIP file</a>, as well as the <a href="https://flightaware.com/adsb/piaware/files/Win32DiskImager-0.9.5-binary.zip" target="_blank">Win32DiskImager utility ZIP file</a>.

Plug in your microSD card, and extract the IMG file from the PiAware SD Card ZIP. Then, open up Win32DiskImager. Browse to the location of the IMG file and select it, then select the drive letter of your microSD card. Click write and Win32DiskImager will do the rest.

![Preparing to write](/blog-static/ads-b-receiver-piaware/ADS-B_DiskImage.png?lightbox)
_Preparing to write_

![Writing image](/blog-static/ads-b-receiver-piaware/ADS-B_DiskImage2.png?lightbox)
_Writing image_

Most of the hardware part of this project is "plug and play". The most time consuming part is assembling the PoE components. Ethernet cables have a few unused wires when they're being used in 10/100 mode, and we can deliver power for the Raspberry Pi over them. A PoE injector takes energy from a wall socket and transfers it onto these unused lines in its output socket, while also passing through data coming in through its input socket. On the other end of the cable, the UBEC takes this voltage and efficiently steps it down to the 5V required to power the Raspberry Pi. UBECs are actually intended to be hooked up to a battery, the idea being that as the battery discharges and the voltage drops, constant power is still being fed to whatever the battery is powering. In fact, the UBEC is basically just a glorified switching regulator. I thought about using a cheap linear regulator instead, but decided to spring for the UBEC because it's a lot more efficient, especially for a device that's going to be operating 24/7.

Lets talk about which wires in your Ethernet cable are doing what. There are eight wires in an Ethernet cable. They are green, green/white, orange, orange/white, blue, blue/white, brown, and brown/white. In 10/100 mode only the green, green/white, orange, and orange/white are used for data. This leaves the others free to carry power. We'll be tying the blue and blue/white wires together to +DC and the brown and brown/white to -DC.

![Ethernet cable pinout](/blog-static/ads-b-receiver-piaware/RJ-45_TIA-568B_Right.png?lightbox)
_Ethernet cable pinout_

I wasn't sure what voltage my PoE injector was putting out, so I hooked it up to an Ethernet cable and measured the voltage between pin 5 and 7 with a multimeter. It came out to about 15V which is perfect since the UBEC we have works over a range of 5.5V to 26V. The one I linked in the parts list should supply 12V.

![Testing the PoE injector](/blog-static/ads-b-receiver-piaware/ADS-B_PoETesting_1500.jpg?lightbox&resize=600)
_Testing the PoE injector_

After this test, I wired up my RJ45 keystone jack to an Ethernet cable with one end cut off. Two things to note: First, make sure this cable is long enough to reach from the PoE injector to the Raspberry Pi. You don't want it to be too short! Second, slip a weatherproof connector over the Ethernet cable with the metal piece of the connector facing the cut off end of the cable. An RJ45 connector is just barely too big to fit through the weatherproof connector, so if you forget to do this you'll have to cut the cable and rewire one of the ends. I speak from experience. The RJ45 keystone jacks are great because they don't require any tools to wire up, you just line up your wires with the correct slots and press down on the plastic shields. The jack contacts slice through the insulation so the wires don't even have to be stripped. I split out the blue, blue/white, brown, and brown/white wires and wired the rest into the jack. Since I have an RJ45 crimp tool if I were to do this again I think I would've foregone the keystone jack and just crimped a connector onto the green, green/white, orange and orange/white wires.

![Left side wired up](/blog-static/ads-b-receiver-piaware/ADS-B_EthernetWired4678_1500.jpg?lightbox&resize=400)
_Left side wired up_

![Right side wired up](/blog-static/ads-b-receiver-piaware/ADS-B_EthernetWired2135_1440.jpg?lightbox&resize=400)
_Right side wired up_

The next step is to hook up and test the UBEC. Connect the red wire to blue and blue/white and the black wire to brown and brown/white. Note that there's a jumper on the UBEC for selecting 5V or 6V output. Mine came set to 5V, but it's worth checking that it's there on yours as well. I turned on the power and measured the output. Right around 5V!

![Testing the UBEC](/blog-static/ads-b-receiver-piaware/ADS-B_PowerTest_1500.jpg?lightbox&resize=600)
_Testing the UBEC_

Now that the power system is ready, it's time to clean it up. I recommend soldering the wires together to get a clean connection, but you can also use screw on wire connectors. Soldering is really a valuable skill and isn't that hard once you have a little bit of practice. Of course it helps to have a dedicated soldering work station with a lighted magnifier, a high quality iron, and a professional ventilation system... but failing that you can do what I did. Cover a baking sheet in aluminum foil and do all your soldering on the stove. Turn on the stove hood to suck away any nasty fumes.

![Professional soldering workstation](/blog-static/ads-b-receiver-piaware/ADS-B_SolderingStoveStation2_1500.jpg?lightbox&resize=600)
_Professional soldering workstation_

I clamped the wires together on a soldering stand and went to town. I think it turned out pretty good!

![Clamping the wires in place](/blog-static/ads-b-receiver-piaware/ADS-B_SolderingClamped_1500.jpg?lightbox&resize=400)
_Clamping the wires in place_

![Soldering complete](/blog-static/ads-b-receiver-piaware/ADS-B_SolderingComplete_1500.jpg?lightbox&resize=400)
_Soldering complete_

After the soldering job was done I wrapped up the UBEC and the RJ45 keystone jack in electrical tape and stuck a piece of Velcro on so I could attach it somewhere later.

![All wrapped up](/blog-static/ads-b-receiver-piaware/ADS-B_UBECPackagedVelcro_1500.jpg?lightbox&resize=600)
_All wrapped up and Velcro stuck on_

Now that the hardest part is over, it's time to go back to the Raspberry Pi. Unplug the microSD card from your computer (it should be imaged by now) and plug it in to the Raspberry Pi. Then put the Raspberry Pi into the case. Now it's time for the next test. Plug the output connector on the UBEC into the Raspberry Pi's GPIO connector so it covers pins 2, 4, and 6 (P.S. there's a great Raspberry Pi pinout reference <a href="http://pinout.xyz/" target="_blank">here</a>). Turn it on and after a few seconds some lights should come on!

![Raspberry Pi test](/blog-static/ads-b-receiver-piaware/ADS-B_PowerTestPackaged_1500.jpg?lightbox&resize=600)
_Raspberry Pi test_

Everything is ready to go at this stage so now it's a case of packaging it up. I used this legit little box I got at Walmart. I actually bought an extra one because I can think of a million uses for it. Although I was disappointed to learn it didn't come with the pictured $10... :(

![Box closed](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxClosed_1500.jpg?lightbox&resize=300)
_Box closed_

![Box opened](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxOpen_1500.jpg?lightbox&resize=500)
_Box opened_

I also can't say enough good things about Velcro tape. I have a roll that consists of two lengths of tape, one has hooks and the other has loops. The back of both tapes have a super-strong 3M adhesive on them. This stuff is awesome for holding all the parts together, but is also easy to remove if you need to adjust anything.

![Raspberry Pi with Velcro attached](/blog-static/ads-b-receiver-piaware/ADS-B_RaspberryPiCaseVelcro_1500.jpg?lightbox&resize=600)
_Raspberry Pi with Velcro attached_

It was a tight fit to get everything in. Too tight in fact. When the NooElec radio is plugged into a USB port on the Raspberry Pi, it's too long to fit in the box. Argh! I solved this with a USB extension cable that's way too long. I had to roll it up and use it to put the radio on top of the Raspberry Pi. I'd recommend getting a really short extension cable (or a bigger box). I know I used to have a USB wireless adapter that came with a USB extension cable that was about four inches long, but I couldn't find it unfortunately.

![Everything packed in](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxPacked_1500.jpg?lightbox&resize=600)
_Everything packed in_

I'd recommending experimenting with getting everything to fit before drilling the hole for the weatherproof connector. Then drill the hole with the box empty so you don't destroy any of the components. I only used one of the connectors for both the Ethernet cable and the antenna coax cable. I'd recommend giving them each their own connector since they come in a ten pack. I also sealed up the connector to waterproof it more. I used hot glue which probably isn't ideal but should work "good enough".

![Lots of connectors!](/blog-static/ads-b-receiver-piaware/ADS-B_WeatherproofConnectors_1500.jpg?lightbox&resize=400)
_Lots of connectors!_

![Sealed up](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxPackedClosedSideView_1500.jpg?lightbox&resize=400)
_Sealed up_

Here are a few more pictures of everything in the box.

![Left side close-up](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxPackedZoomLeft_1500.jpg?lightbox&resize=400)
_Left side close-up_

![Right side close-up](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxPackedZoomRight_1500.jpg?lightbox&resize=400)
_Right side close-up_

![Closed box close-up](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxPackedClosed_1500.jpg?lightbox&resize=400)
_Closed box close-up_

![Open box close-up](/blog-static/ads-b-receiver-piaware/ADS-B_WaterproofBoxPackedZoomAll_1500.jpg?lightbox&resize=400)
_Open box close-up_

The ideal place to mount the receiver is somewhere with a 360 degree view of the sky and as high as you can get. The roof of a structure is about as good as you can get. My receiver is mounted on a window on the top floor of my apartment building which is four floors. Not ideal, but I'm getting pretty good returns nonetheless.

Once you have the receiver mounted to your liking, run a cable from the input of the PoE injector to your router and turn it on. Now log into your router and look for it under the list of clients. It should show up as "piaware".

![Router client list](/blog-static/ads-b-receiver-piaware/ADS-B_Router.png?lightbox&resize=1000)
_Router client list_

By default, the username and password are not in a secure configuration. You should log in and change them via SSH. On Windows, the easiest way to SSH is by using <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a>. I'm using <a href="http://cygwin.com/install.html" target="_blank">Cygwin</a> which is a little more effort to set up, but is a lot more powerful. After installing your client of choice, sign in to PiAware running on the the Raspberry Pi using the IP address from the router client list, the username <code>pi</code>, and the password <code>flightaware</code>. Once you are logged in (you will see <code>pi@piaware ~ $</code> at the beginning of the line) type <code>passwd</code> and press enter. PiAware will now prompt for a new password. Note: when typing the password, nothing will show up. This is a security feature in case someone is standing behind you. Once you enter the new password twice and get no errors it's changed, you can close the window.

![Setting the password](/blog-static/ads-b-receiver-piaware/ADS-B_CommandPrompt.png?lightbox&resize=800)
_Setting the password_

Now, who's ready to see some planes? Open a web browser and type in the IP address from before, followed by <code>:8080</code>. My PiAware IP was <code>10.0.0.25</code> so I typed <code>10.0.0.25:8080</code>. Assuming everything worked, you should see any nearby aircraft transmitting ADS-B! In my case, this was just a few, they're highlighted in green.

![ADS-B transmitting aircraft](/blog-static/ads-b-receiver-piaware/ADS-B_Map2.png?lightbox&resize=1000)
_ADS-B transmitting aircraft_

There's a way to see a lot more aircraft. First, <a href="https://flightaware.com/adsb/piaware/claim" target="_blank">claim your receiver with FlightAware</a> to get the benefits of the Enterprise account. After you have claimed your receiver, you should configure its exact location in your profile. I did this by holding my phone running a navigation app next to the receiver and writing down the GPS latitude, longitude, and altitude. Once you've set this up (don't worry, FlightAware will not show your exact location on a map) you should start receiving multilateration (MLAT) reports, they're highlighted in blue.

![ADS-B and MLAT](/blog-static/ads-b-receiver-piaware/ADS-B_Map3.png?lightbox&resize=1000)
_ADS-B and MLAT_

Multilateration works by using the existing secondary radar system. Remember above we talked about how transponders work? When a transponder replies to interrogation, if there are enough receivers nearby, the exact time the signal reaches each receiver can be recorded and this information calculated into position data. Since our ADS-B receiver works on the same frequency that transponders reply on (1090 MHz), and FlightAware knows our exact position, we can participate in multilateration. Cool, right?

#### Resources and References

This is a list of sites I found useful while doing this project and writing the post.

<ul>
<li><a href="https://flightaware.com/adsb/piaware/build" target="_blank">https://flightaware.com/adsb/piaware/build</a></li>
<li><a href="https://en.wikipedia.org/wiki/Automatic_dependent_surveillance_–_broadcast" target="_blank">https://en.wikipedia.org/wiki/Automatic_dependent_surveillance_–_broadcast</a></li>
<li><a href="https://en.wikipedia.org/wiki/Secondary_surveillance_radar" target="_blank">https://en.wikipedia.org/wiki/Secondary_surveillance_radar</a></li>
<li><a href="http://www.multilateration.com/surveillance/multilateration.html" target="_blank">http://www.multilateration.com/surveillance/multilateration.html</a></li>
<li><a href="https://en.wikipedia.org/wiki/Power_over_Ethernet" target="_blank">https://en.wikipedia.org/wiki/Power_over_Ethernet</a></li>
<li><a href="https://pinout.xyz/" target="_blank">https://pinout.xyz/</a></li>
</ul>