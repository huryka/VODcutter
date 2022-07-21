# VODcutter
A MaxMSP patcher that uses image masking via Jitter to determine start and endpoints of a fighting game VOD. It will export a batch file that will cut your VODs. Standalone .exe available. You need ffmpeg essentials: https://www.gyan.dev/ffmpeg/builds/

Early demonstration video:
https://twitter.com/bootyjazz/status/1486813442640121862

This tool started out as a practice in AUDIO modeling, not image masking. I wanted to see if I could match the audio from the "GO!" and "GAME!" announcement in a Super Smash Bros. match in order to find the start and endpoints of a potential VOD (video-on-demand). This, along with Max's iterative nature, could automate VODcutting immensely.

After some savagery with `peakamp~` and `slide`, I realized: On your average stream, there are usually other sounds playing at the same time that would derail the modeling completely. This program couldn't account for a streamer talking over her stream, or external music playing in the foreground or background. In addition, SSBU's background music was usually quiet enough to be filtered out by the program, but "usually" meant "not all the time" and that was unacceptable. So, the use case for this program was limited to practically ONLY sound-only videos. Never mind if you had mods installed that changed the announcer's voice.

While I was agonizing over this, I somehow completed some cutting logic (they were two seprate ideas - find//cut), and hacked together a GUI.

May as well figure out a way to finish it. Enter image masking.

Unless you have mods installed, you will see a GO! and a GAME! - hence referred to as GG - drawn over everything, every single match, without fail. So I hacked out a couple image masks, singling out GG's text in the middle of the screen. After applying the GG image mask with `jit.op @absdiff` (absolute difference seemed to give the most usable data) I used the `jit.3m` object to extract some RGB data from each masked frame of the imported video that comes through. A larger coefficient means a larger correlation to GG. This is notated by the rolling graphs beside the video preview.

Since GG stays on screen for a second or so, we can smooth the data out with `slide` to make sure we don't catch any potential red herrings, e.g. bright flashes, colorful final smashes.

Since Jitter has video export capabilities, I tried to keep it "in the box" and hacked together some Jitter routines to cut a video. However, this proved way too inefficient, as MaxMSP is not really made for non-linear editing of video files (on that note, another weakness of this program is that it only works in real-time). In the end, I outsourced the cutting to FFMPEG, which you do have to download, but it's light and has a great feature set. After a few nested `sprintf` objects combining the data I derived, I have a .bat file that I can export via the `text` object. From this point, I can use Max's file pathing (combined with its aforementioned iterative nature) to find and cut an entire folder using one batch file.

An audio modeling practice turned image masking tool!
