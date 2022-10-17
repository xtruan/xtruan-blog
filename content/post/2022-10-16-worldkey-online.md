---
title: "WorldKey Simulator: Let's Put a 40-Year Old LaserDisc on the Web"
author: 'Struan Clark'
published: true
date: '2022-10-16'
publish_date: '2022-10-16 11:00'
categories:
    - Projects
    - Blog
tags:
    - projects
    - software
slug: worldkey-online
#image: /blog-static/worldkey-online/Worldkey10.webp
---

EPCOT Center. If you were lucky enough to visit as a kid you either loved it or hated it. I was firmly in the love camp, as a kid who was obsessed with computers and technology. Those in this camp seem to talk about, or rather, fondly reminisce about what used to be, and lament the state of the original pieces that are still hanging on. As a child of the 90's, I was lucky enough to experience the last gasps of Horizons and Journey into Imagination, as well as see the dinosaurs in the Energy pavilion. I still remember the 1994-2007 Spaceship Earth as one of the most inspirational touchstones of that time in my life. However during the earlier days of EPCOT Center, there was another, perhaps lesser known component that was just as innovative in own way. This technology was known as WorldKey.

<!--more-->

#### TL;DR

If you just can't wait to see the project in action, here's a direct link: [https://worldkey.magicalmiles.net](https://worldkey.magicalmiles.net).

#### Background on WorldKey

EPCOT Center opened in October of 1982. In 1982, the average person wasn't really exposed to what I'll call "interactive screens". Sure, there was television. And about 10% of people in the US had largely text-based home computers. But there wasn't a widespread penetration of the interactive screens we take for granted today. 40 years later, you're reading this blog post on some kind of interactive screen. It has a full graphical interface, and it likely has touch capability. Not to mention, the interactive screen you're reading this on is likely one of several you have in our home, and one of a dozen you interact with on a daily basis. Imagine having none of that innate context and understanding, and being presented with an interactive screen. Would you know how to use it? Would you have any desire to use it?

These questions were answered by thousands of guests who stepped of Spaceship Earth and into the original Earth Station post-show in the 1980's. A row of touch screen WorldKey consoles lined the wall of Earth Station and provided information about the park's attractions, restaurants, and shops. They also provided the ability to video-conference with a "WorldKey Attendant" who could answer questions and perform services such as making restaurant reservations.

![](/blog-static/worldkey-online/Worldkey10.webp)
_WorldKey consoles in Earth Station ([Source: Disney Fandom](https://disney.fandom.com/wiki/WorldKey))_

The WorldKey system was a collaboration between Walt Disney Imagineering which provided the content, Bell Laboratories which designed the WorldKey system, which they later tried to market elsewhere as Ariel, and Western Electric which provided the then-revolutionary fiber-optic cable which linked the guest consoles with the back-end system.

#### The Technology Behind WorldKey 

I find WorldKey to be a really interesting system on multiple levels. It married several technologies in an innovative way in order to provide a unique user experience. One of the most interesting of these is the conjunction of LaserDisc and computer graphics. I'm not going to do a deep dive into LaserDisc here, otherwise this post would be longer than it's already going to be, but here are the major pieces that make LaserDisc an important enabler for WorldKey:

1) It provides the ability to use pre-recorded video instead of computer-generated imagery. Computers were just not up to the task of generating graphics on-the-fly for a system like WorldKey, so engineers worked around this by essentially making an animated film and recording this as video to a LaserDisc.
2) It provides the ability to rapidly and accurately seek to specific portions of the video in order to play them back. This contrasts with a medium like video tape, which takes a non-trivial amount of time to scrub through.
3) Because LaserDisc is actually an analog video format, some cool features such as the ability to reference exact frame numbers and play content seamlessly in reverse were available. Based on my work building the WorldKey simulator, I think Disney almost certainly used both of these features.

With LaserDisc doing the heavy lifting from a graphics perspective, the computer portion can get away with just telling the LaserDisc player where on the disc the appropriate content is, handling user input, and providing text-based information where appropriate. This text-based information initially was used for content that had to be updated regularly, including features such as restaurant menus, musical act information, and an alphabetical index. However due to the expense of mastering an entirely new set of LaserDiscs as new attractions came online (each WorldKey terminal required its own dedicated LaserDisc media and LaserDisc player, not to mention the need for spares), this text-only mode was used to "fill in the gaps" instead of creating new graphics for these attractions.

These computers and LaserDisc players were centrally located in Computer Central, another fascinating location in the EPCOT Center of days past. This area contained all of the computer systems and media required to make the attractions and services of the park function. In addition, this is where the live attendants who were available on WorldKey sat. These attendants could be reached by switching WorldKey over to a closed-circuit television mode, where they were then able to answer questions and make restaurant reservations.

![](/blog-static/worldkey-online/EPCOT-Center-Computer-Central-WorldKey-Laser-Disc-Bank-From-the-Western-Electrics-WE-magazine-1st-qtr-1983.jpg)
_WorldKey LaserDisc bank in ComputerCentral ([Source: RetroWDW](https://www.retrowdw.com))_

#### Bringing WorldKey Back to Life

Thinking back, I may have used WorldKey one time in 1999, right before it was decommissioned. At this point the system had been moved out of Earth Station and was placed outside of the former Computer Central which was now a guest service location. Since the system is long gone from EPCOT Center, I began to think about how hard it would be to put a tribute version online.

I started by looking for some reference graphics on YouTube to see what the system actually looked like. In the early stages of this project, I actually envisioned trying to recreate some of the content and building a set of hyperlinked pages to show the spirit of what WorldKey was. During this process, I did find an interesting short documentary of the system and several shaky videos of people using various generations of the system, which was basically what I expected to find. However, I also found something I did not expect - someone had uploaded a direct rip of the actual 1982 version of the WorldKey LaserDisc.

Woah... this find changed the entire direction of my project. I decided, instead of trying to recreate the WorldKey graphics from scratch, to just do what the programmers in the 1980s did: rely on video content to do the graphical heavy-lifting, and to program a "LaserDisc player" to seek to the right spots, based on where a user touched.

#### Simulating a LaserDisc Player using HTML5 video and JavaScript

The video itself is 27 minutes and 8 seconds of content total at a resolution of 490x360 pixels in an MP4 container. All running at the NTSC framerate of 29.97 frames per second. Playing back the video from start to finish results in all the individual "scenes" just running directly into one another, with a few single-frame pieces that are intended to be static user interfaces.

I needed a way to accurately seek to, start, and stop the video at exact frames. I used a library aptly named [VideoFrame.js](https://github.com/allensarkisyan/VideoFrame). This, alongside a bit of JavaScript I whipped up, allowed me to play the video, see the current frame number, and jump forward, back, and to other arbitrary frames of my choosing. I also added some code so that whenever a user clicked on the video, I could capture the coordinates - similar to the original infrared touch screen on the real WorldKey.

![](/blog-static/worldkey-online/worldkey-video-player.png)
_Custom HTML5 Video Player for Navigating the LaserDisc_

#### Navigating Between Scenes Based on Actions

I knew from the beginning I would need a way to program the scenes in such a way that the video would play from a start frame until a stop frame and then wait for user input. I developed a schema for a big JSON object that stores all the metadata about every scene in the video. At the simplest level, this object is just two big lists - one list of scenes, and one list of possible actions. Each scene is defined with a name, a start frame, a stop frame, and some actions. Each action is defined with X and Y values which describe the area on the screen that triggers the action, and a "goto" which contains either the name of a scene or a specific frame to jump to when it is triggered. Actions can be triggered automatically when the scene they are a part of finishes playing. They can be defined explicitly on the scene object, or if they need to be reused, the scene can just refer to the name of an action in the list of all actions.

There are a few other special cases. Scenes can be set to automatically repeat themselves upon completion. This is only used on the initial "attract screen scene". Actions can also goto special locations that are annotated with a leading underscore. These gotos don't define the name of the scene itself, they act as a pointer to the name of a specific key attached to the current scene object that names a different scene. This allows for some pretty complex behavior as the actions can perform dynamic gotos based on the scene that they are attached to. To demonstrate the complexity, here is a single scene and associated actions captured in the JSON format.

```json
{
    "scenes": [    
        {
            "name": "epcot",
            "start": 3663,
            "end": 3664,
            "actions": [
                {   "name": "up"   },
                {   "name": "bit"   },
                { 
                    "goto": "services",
                    "x1": 274, "y1": 179,
                    "x2": 356, "y2": 252 
                },
                { 
                    "goto": "attractionsIntro",
                    "x1": 217, "y1": 52,
                    "x2": 301, "y2": 130 
                },
                { 
                    "goto": "dining",
                    "x1": 101, "y1": 76,
                    "x2": 192, "y2": 151 
                },
                {
                    "goto": "shopping",
                    "x1": 327, "y1": 79,
                    "x2": 412, "y2": 156 
                },
                { 
                    "goto": "music",
                    "x1": 161, "y1": 176,
                    "x2": 245, "y2": 251 
                }
            ],
            "_up": "home"
        }
    ],
    "actions": [
        {
            "name": "up",
            "x1": 128, "y1": 260,
            "x2": 188, "y2": 330,
            "goto": "_up"
        },
        {
            "name": "bit",
            "x1": 226, "y1": 260,
            "x2": 280, "y2": 330,
            "goto": "bit"
        }
    ]
}
```
_JSON for a single scene and associated actions_

#### Recording the Navigation Data

At this point I had a video player that could consume a JSON "script" telling it in what order to play the video. Now I just had to build said script. This was probably the most time consuming part of the project. Basically, I stepped through the video chunk by chunk and reverse engineered what was supposed to link to where. There are a few minute or two long clips on YouTube of people using the consoles, but there is nothing comprehensive out there. I did my best to reconstruct the script as faithfully as possible. I think I got it pretty close (barring a few places where I'm almost certain things are supposed to play in reverse, I discuss this below), and I wasn't left with any chunks of video content that were not used which was promising. This process gave me some appreciation for the design of the video itself. There are several ways to get to the same informational content, especially the longer video sections, so the WorldKey program feels like it has more content than it actually does - very clever in my opinion. This also resulted in me watching the video many times over; at this point I have the content pretty much memorized. This was such a fun trip as I never really got to play with the real consoles, but now I feel like I know them back to front.

#### Without Further Ado

You've made it this far, and by this point should have a general understanding of what makes up the project, so here it is! Presenting WorldKey Information Online - touch the screen to start!

<iframe style="height:550px;width:100%" src="https://worldkey.s3.us-east-2.amazonaws.com/index.html"></iframe>

You can also navigate to the WorldKey console directly by [clicking here](https://worldkey.magicalmiles.net). This is highly recommended if the frame above includes a scroll bar on your device.

#### Some of the Tricks I Used

The biggest trick I employed here was the reuse of actions. The way WorldKey is set up, most of the navigation screens use a "right", "left", and "up a level" button. Every screen except for the initial one also has access to jump to the "Bit" screen, which provides help and information on the consoles themselves (which I highly recommend watching - touch "Bit" then touch the Bell System logo). This saved a ton of time as I didn't have to capture the same X and Y coordinates multiple times for the same actions.

I also built my debug player with the ability to jump directly to any scene, or to skip 1, 10, 100, or 1000 frames back or forward. This allowed me to quickly scrub through some of the longer video portions to record the all important scene ending frames.

Finally, I kept the code as simple as possible. No fancy frameworks or Node.js deployments here. Just a hand-rolled JavaScript file and a big JSON object.

#### Known Bugs and Other Notes

I am aware of a few bugs in the finished product.

- The "touch sound" doesn't work on iOS devices. This is due to the way iOS handles sound when video content is playing. Maybe there's a workaround, but I haven't looked for it.
- The "sliding animation" is almost certainly wrong for the pavilion navigation under the EPCOT section of WorldKey. This is where I think the Disney Imagineers got fancy and had the LaserDisc play backwards when performing a "left" navigation, and play forward as normal when performing a "right" navigation. Since my player is only capable of playing forward, the slide happens backwards when navigating "left".
- All of the "dynamic content" screens that would have computer provided text content are blank. This is because I haven't built a simulated computer to generate that data. Maybe that's a next step for this project...
- The last bug is actually with the content itself. One of the menus for the Odyssey Restaurant suggests that there is a tour video describing the restaurant. However, it isn't on the LaserDisc and the other menus do not have the graphic. I'm very curious what would've happened if you were to touch that graphic on the real system. My bet is... nothing.

One thing I did not mention above is the ability to toggle the scanlines on and off. I have them off by default, because they gave me a headache after a while, but definitely turn them on at least for a minute or two to make the screen look more like a CRT.

#### What's Next

I'd really like to add a hardware component to this project. I briefly looked at some ancient touch-screen CRTs on eBay, but they were far out of my price range, and likely getting the touch functionality to work would be a nightmare. I think the best bet here is to find a thrift store CRT TV and to try and build some kind of simple touch interface. Maybe even using infrared like the real thing.

I'd also like to build in the computer smarts. I've already built an application to [pull theme park wait times](https://apps.garmin.com/en-US/apps/e9623de9-8a8e-4afc-9169-75883c8b1325) from the internet, I have to imagine that "filling in the blanks" for things like EPCOT musical performances, and even adding the wait times or other information for the new attractions that now inhabit the classic pavilions would be an interesting expansion of this project.

#### Final Thoughts

I had a really good time exploring this project. Hopefully it's given you some insight into an extinct system and let you see what it would be like to experience a small piece of EPCOT Center from 1982. Special mention to the uploader of [the LaserDisc to YouTube](https://www.youtube.com/watch?v=YbJR0ey3yjg). This kind of archival work is critical for the preservation of these antique systems, and without people doing this, projects like this would not be possible. If you found this project interesting, or want to discuss more, please feel free to reach out on Twitter [@xtruan](https://twitter.com/xtruan).

Also, this project was conducted as a hobby. It is not associated in any way with the Walt Disney Company or AT&T.
