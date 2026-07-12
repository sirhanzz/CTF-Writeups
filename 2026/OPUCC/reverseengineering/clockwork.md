<h2>Question</h2>

<p>The challenge description is shown below.</p>

<img width="317" height="425" alt="Challenge Description" src="https://github.com/user-attachments/assets/a123a6a7-f609-47bb-9dfe-cadf3a87a4b3" />

<h2>Solution</h2>

<p>
This challenge gives us a 64-bit ELF executable that asks for a key.
Running the program normally does not reveal the correct flag because it
only tells us whether our input is correct or not. Therefore, the only
way to solve the challenge is by reverse engineering the binary and
understanding how the flag is being verified.
</p>

<h3>Step 1: Looking at the Binary</h3>

<p>
The first thing I did was inspect the binary using tools such as
<b>strings</b>, <b>Ghidra</b>, and <b>IDA</b>. Since the binary was not
stripped, many function names and symbols were still available, making
the analysis much easier.
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
From these names, it was obvious that the flag verification was handled
inside the <b>checkFlag()</b> function, so that became the main focus of
the analysis.
</p>

<h3>Step 2: Understanding the Flag Check</h3>

<p>
After opening <b>checkFlag()</b> in Ghidra, I found that the program does
not compare the user's input directly with the flag. Instead, it processes
each character one by one before checking it against a stored array.
</p>

<p>
Three important parts are involved in this process:
</p>

<ul>
    <li><b>gear()</b> generates a pseudo-random byte.</li>
    <li><b>rotl8()</b> performs an 8-bit left rotation.</li>
    <li><b>gears[]</b> stores the encrypted bytes that the transformed input must match.</li>
</ul>

<p>
Before checking the characters, the program also initializes an internal
state using the length of the input. This ensures that every time the
correct-length input is provided, the same sequence of pseudo-random bytes
is generated.
</p>

<h3>Step 3: How the Validation Works</h3>

<p>
For each character entered by the user, the program performs several
operations before comparing it with the encrypted values stored in
<b>gears[]</b>.
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
In simple terms, each character is first XORed with a pseudo-random byte,
then rotated to the left by a value based on that byte. The result is
compared with the corresponding value inside the encrypted array.
If even one byte is different, the program rejects the input.
</p>

<h3>Step 4: Reversing the Algorithm</h3>

<p>
The good thing about this challenge is that both XOR and bit rotation are
reversible operations. Once the validation process was understood, it was
possible to reverse every step.
</p>

<p>
For each encrypted byte in <b>gears[]</b>, I first generated the same
pseudo-random byte using <b>gear()</b>, then rotated the encrypted value
back to the right before applying XOR again.
</p>

<pre>
flag[i] = ROTR8(gears[i], g & 7) XOR g
</pre>

<p>
Repeating this process for every byte successfully recovered the original
flag.
</p>

<h3>Recovered Flag</h3>

<pre>
OPUCC{c0gs_and_g3ars_keep_perf3ct_t1me_when_w0und_up_by_h4nds_67!!}
</pre>

