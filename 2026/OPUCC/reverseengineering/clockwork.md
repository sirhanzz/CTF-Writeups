<h2>Question</h2>

<p>The challenge description is shown below.</p>

<img width="317" height="425" alt="Challenge Description" src="https://github.com/user-attachments/assets/a123a6a7-f609-47bb-9dfe-cadf3a87a4b3" />

<h2>Solution</h2>

<p>
This challenge provides a 64-bit ELF executable that asks the user to enter a
key. Running the program only tells whether the input is correct or incorrect,
so I used <b>Ghidra</b> to analyze how the program checks the flag.
</p>

<h3>Step 1: Explore the Binary</h3>

<p>
After opening the binary in <b>Ghidra</b>, I noticed that it was not stripped,
so the function names were still available.
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
The <b>checkFlag()</b> function is responsible for verifying the user's input,
so I focused on analyzing it.
</p>

<h3>Step 2: Understand the Verification</h3>

<p>
Instead of comparing the input directly with the flag, the program transforms
each character before checking it against an encrypted array stored in
<b>gears[]</b>.
</p>

<p>
The verification uses three main functions:
</p>

<ul>
    <li><b>gear()</b> generates a pseudo-random byte.</li>
    <li><b>rotl8()</b> rotates a byte to the left.</li>
    <li><b>gears[]</b> stores the encrypted values.</li>
</ul>

<p>
The program also creates an internal state using the input length so that the
same sequence of pseudo-random bytes is generated each time.
</p>

<h3>Step 3: How the Flag Is Checked</h3>

<p>
Each character goes through the following steps:
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
If every transformed character matches the corresponding value in
<b>gears[]</b>, the program accepts the flag. Otherwise, it rejects the input.
</p>

<h3>Step 4: Recover the Flag</h3>

<p>
Since XOR and bit rotation can be reversed, I applied the reverse operations to
the encrypted values instead of guessing the flag.
</p>

<pre>
flag[i] = ROTR8(gears[i], g & 7) XOR g
</pre>

<p>
Repeating this for every byte revealed the original flag.
</p>

<h3>Recovered Flag</h3>

<pre>
OPUCC{c0gs_and_g3ars_keep_perf3ct_t1me_when_w0und_up_by_h4nds_67!!}
</pre>
