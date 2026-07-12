<img width="317" height="426" alt="Screenshot 2026-07-12 082644" src="https://github.com/user-attachments/assets/22547a6d-7a8f-44e0-850b-6ebad3d17428" />


<h2>Solution</h2>

<p>
First, identify the file type using:
</p>

<pre>
file duckvm
</pre>

<p>
The output shows that it is a 64-bit Linux ELF executable.
</p>

<h3>Step 1: Extract readable strings</h3>

<p>
Use the <code>strings</code> command to inspect the binary:
</p>

<pre>
strings duckvm
</pre>

<p>
Several flags are displayed:
</p>

<pre>
OPUCC{fake_vm_flag}
OPUCC{wrong_duck_wrong_flag}
OPUCC{strings_will_not_save_you}
</pre>

<p>
These are fake flags because the binary is designed to mislead the player.
</p>

<h3>Step 2: Analyze the program</h3>

<p>
Open the binary in Ghidra and inspect the <code>main()</code> function.
Instead of comparing the flag directly, the program initializes a custom virtual machine (DuckVM) and executes its bytecode.
</p>

<p>
The VM loads the user input, performs several operations such as rotations, addition, subtraction, and XOR, then compares the result with expected values.
Each character of the flag is checked individually.
</p>

<h3>Step 3: Reverse the VM</h3>

<p>
By reversing the VM instructions and applying the inverse operations to each comparison, the original input can be reconstructed one character at a time.
</p>

<h3>Step 4: Recover the flag</h3>

<p>
After reversing all character transformations, the recovered flag is:
</p>

<pre>
OPUCC{cust0m_1s_4nn0y1ng}
</pre>

<h2>Flag</h2>

<pre>
OPUCC{cust0m_1s_4nn0y1ng}
</pre>

<p>
This challenge demonstrates that custom virtual machines are often used to hide the real flag verification logic. Instead of trusting the strings output, we must analyze the VM and reverse its bytecode to recover the correct flag.
</p>
