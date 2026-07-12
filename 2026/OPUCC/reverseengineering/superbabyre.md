<img width="611" height="732" alt="image" src="https://github.com/user-attachments/assets/9c6e45dd-2e8c-4f42-9e19-7e4ffea9c65c" />
<h2>Question</h2>

<p>
The challenge provides a Linux ELF executable file. 
The objective is to find the real flag hidden inside the binary.
</p>

<h2>Solution</h2>

<p>
First, we identify the file type using the command:
</p>

<pre>
file superbabyre
</pre>

<p>
The result shows that it is an ELF 64-bit executable, which means it is a Linux program.
</p>

<h3>Step 1: Extract readable text</h3>

<p>
We use the <code>strings</code> command to find readable text inside the binary:
</p>

<pre>
strings superbabyre
</pre>

<p>
The output shows several possible flags:
</p>

<pre>
OPUCC{fake_flag}
OPUCC{not_this_one}
OPUCC{strings_maybe?}
OPUCC{y0u_s0lv3d_RE!}
</pre>

<p>
However, some of these are fake flags. 
We need to check which one is actually used by the program.
</p>

<h3>Step 2: Analyze the binary</h3>

<p>
Using a reverse engineering tool such as Ghidra, we open the executable and check the functions inside it.
</p>

<p>
The binary contains several functions:
</p>

<ul>
    <li>fake_flag_1</li>
    <li>fake_flag_2</li>
    <li>fake_flag_3</li>
    <li>real_flag</li>
    <li>main</li>
</ul>

<p>
The function names already give us a clue that the first three flags are only decoys.
</p>

<h3>Step 3: Check the real flag function</h3>

<p>
Inside the <code>real_flag</code> function, we can see that it prints:
</p>

<pre>
OPUCC{y0u_s0lv3d_RE!}
</pre>

<p>
This confirms that this is the actual flag.
</p>

<h2>Flag</h2>

<pre>
OPUCC{y0u_s0lv3d_RE!}
</pre>


We must analyze the program flow using reverse engineering tools like Ghidra to find the real flag.
</p>
