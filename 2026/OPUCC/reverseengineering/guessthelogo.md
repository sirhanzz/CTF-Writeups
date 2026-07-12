<img width="391" height="415" alt="Screenshot 2026-07-12 083128" src="https://github.com/user-attachments/assets/0ebdd0c6-e7a1-45a5-b5ae-9ee62d1ea998" />
<h2>Solution</h2>

<p>
The challenge provides a Roblox game link. At first, I joined the game and
completed the gameplay, but the flag obtained was rejected by the CTF platform.
This indicated that the in-game flag was only a decoy.
</p>

<h3>Step 1: Open the Game in Roblox Studio</h3>

<p>
Instead of focusing on the gameplay, I opened the game using
<b>Roblox Studio</b> to inspect its contents.
</p>

<h3>Step 2: Inspect the Source Code</h3>

<p>
Inside Roblox Studio, I explored the game's objects and scripts using the
<b>Explorer</b> and <b>Properties</b> panels. I checked the server scripts,
local scripts, and modules to look for hardcoded strings or hidden messages.
</p>

<h3>Step 3: Find the Hidden Flag</h3>

<p>
While reviewing the source code, I found that the real flag was stored inside
one of the scripts. The game intentionally displayed a fake flag during
gameplay to mislead players, while the actual flag was hidden in the source
code.
</p>
