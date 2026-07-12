<h2>Question</h2>

<p>The challenge description is shown below.</p>

<img width="317" height="425" alt="Challenge Description" src="https://github.com/user-attachments/assets/a123a6a7-f609-47bb-9dfe-cadf3a87a4b3" />

<h2>Solution</h2>

<p>
This challenge provides a 64-bit ELF executable that asks the user to enter a key.
When I ran the program, it only displayed whether the input was correct or incorrect,
so there was no obvious way to obtain the flag directly. Because of this, I analyzed
the binary with Ghidra to understand how the validation process works.
</p>

<h3>Step 1: Exploring the Binary</h3>

<p>
I first opened the executable in <b>Ghidra</b>. The binary was not stripped,
so function names were still available, which made it much easier to understand
its structure.
</p>

<pre>
main
checkFlag
gear
gears
state
rotl8
clockwork.c
</pre>

<p>
Among these functions, <b>checkFlag()</b> stood out because its name clearly
suggested that it was responsible for verifying the user's input. I focused my
analysis on this function.
</p>

<h3>Step 2: Understanding the Verification Process</h3>

<p>
Inside <b>checkFlag()</b>, I found that the program does not compare the input
directly with the flag. Instead, it transforms every character before comparing
the result with an encrypted array stored in the binary.
</p>

<p>
The verification relies on three important components:
</p>

<ul>
    <li><b>gear()</b> generates a pseudo-random byte.</li>
    <li><b>rotl8()</b> performs an 8-bit left rotation.</li>
    <li><b>gears[]</b> stores the encrypted values used for comparison.</li>
</ul>

<p>
Before processing the input, the program also initializes an internal state using
the length of the entered string. Since the correct flag length is fixed, this
produces the same sequence of pseudo-random bytes every time the program runs with
an input of the correct length.
</p>

<h3>Step 3: How Each Character Is Checked</h3>

<p>
Each character of the input goes through a few transformations before it is
compared with the corresponding value in <b>gears[]</b>.
</p>

<pre>
state = strlen(flag) ^ 0x1337C0DE;

for each character:
    g = gear()
    x = flag[i] XOR g
    x = ROTL8(x, g & 7)

    compare x with gears[i]
</pre>

<p>
In other words, the program first XORs each character with a pseudo-random byte.
The result is then rotated to the left by a number of bits determined by that
same byte. Finally, the transformed value is compared against the encrypted data.
If any comparison fails, the program immediately rejects the input.
</p>

<h3>Step 4: Recovering the Flag</h3>

<p>
After understanding the algorithm, recovering the flag became much easier because
both XOR and bit rotation can be reversed. Instead of trying to guess the flag,
I simply reversed the same operations performed by the program.
</p>

<p>
For each byte stored in <b>gears[]</b>, I generated the same pseudo-random value,
rotated the encrypted byte back to the right, and then applied XOR again to obtain
the original character.
</p>

<pre>
flag[i] = ROTR8(gears[i], g & 7) XOR g
</pre>

<p>
Repeating this process for every byte revealed the complete flag.
</p>

<h3>Recovered Flag</h3>

<pre>
OPUCC{c0gs_and_g3ars_keep_perf3ct_t1me_when_w0und_up_by_h4nds_67!!}
</pre>
