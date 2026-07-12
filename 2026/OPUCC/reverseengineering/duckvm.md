<img width="317" height="426" alt="Screenshot 2026-07-12 082644" src="https://github.com/user-attachments/assets/22547a6d-7a8f-44e0-850b-6ebad3d17428" />
<h2>Solution</h2>

<h3>Step 1: Understand the Files</h3>

<p>
The challenge provides two files:
</p>

<ul>
    <li><b>brainduck.py</b> – the interpreter that runs the program.</li>
    <li><b>program.bd</b> – the BrainDuck program that checks the flag.</li>
</ul>

<p>
Run the program using:
</p>

<pre>python brainduck.py program.bd</pre>

<p>
The program asks you to enter a flag. If the flag is correct, it prints
<b>DUCK</b>; otherwise, it prints <b>ROTTEN</b>.
</p>

<h3>Step 2: Learn the Instructions</h3>

<p>
Before looking at <code>program.bd</code>, open <code>brainduck.py</code> to
understand what each instruction does. For example:
</p>

<ul>
    <li><b>EGG</b> – Read one input character.</li>
    <li><b>WADDLE</b> / <b>MIGRATE</b> – Move to the next or previous memory cell.</li>
    <li><b>BREAD</b> / <b>FLAP</b> – Add or subtract a value.</li>
    <li><b>SPLASH</b> – XOR the value.</li>
    <li><b>SWIM</b> / <b>PADDLE</b> – Rotate bits left or right.</li>
    <li><b>QUACK</b> – Compare with the expected value.</li>
    <li><b>DUCK</b> – Success.</li>
    <li><b>ROTTEN</b> – Failure.</li>
</ul>

<h3>Step 3: Find the Real Check</h3>

<p>
In <code>program.bd</code>, the program immediately jumps to the
<b>validate</b> section using:
</p>

<pre>FLY validate</pre>

<p>
This means the other sections are only decoys and can be ignored.
</p>

<h3>Step 4: Understand the Flag Check</h3>

<p>
Inside the <b>validate</b> section, each character is checked in the same way:
</p>

<pre>
EGG
(transform the character)
QUACK value
SCATTER fail
WADDLE
</pre>

<p>
The program reads one character, performs several operations on it, and
compares the result with a fixed value. If the comparison fails, the program
stops immediately.
</p>

<h3>Step 5: Reverse the Operations</h3>

<p>
Instead of guessing the flag, reverse the operations for each character.
The reverse operations are:
</p>

<ul>
    <li>Add → Subtract</li>
    <li>Subtract → Add</li>
    <li>XOR → XOR again</li>
    <li>Rotate Left → Rotate Right</li>
    <li>Rotate Right → Rotate Left</li>
</ul>

<p>
Repeating this process for every character reveals the original flag.
</p>

<h3>Recovered Flag</h3>

<pre>OPUCC{br41nduck_15_c0nfus1ng}</pre>

<h3>Verification</h3>

<p>
Run the program again and enter the recovered flag when prompted:
</p>

<pre>
Feed the duck a flag:
OPUCC{br41nduck_15_c0nfus1ng}
</pre>

<p>
If the flag is correct, the program displays:
</p>

<pre>Correct! The duck is enlightened.</pre>
