<img width="417" height="592" alt="Screenshot 2026-07-12 080557" src="https://github.com/user-attachments/assets/69ff67ce-a798-4d1a-a0a9-aabbca77685c" />
<h3> Steps</h3>

<p><b>1. Spot the Bug</b></p>

<p>
The program echoes user input using <code>printf(your_input)</code> instead of
printing it safely. This creates a <b>format-string vulnerability</b>.
</p>

<p><b>2. Find Where the Flag Lives</b></p>

<p>
Static analysis shows that the flag is loaded into memory at the fixed address:
</p>

<pre>0x4040c0</pre>

<p><b>3. Find Your Input on the Stack</b></p>

<p>
Connect to the service and send:
</p>

<pre>AAAABBBB.%p.%p.%p.%p.%p.%p.%p.%p</pre>

<p>
Look for <code>0x4242424241414141</code> (the hexadecimal representation of
<code>AAAABBBB</code>). It appears as the <b>6th</b> argument, meaning the
controlled input starts at argument <b>#6</b>.
</p>

<p><b>4. Leak the Flag</b></p>

<p>
Place the flag address in argument <b>#7</b> and use the <code>%7$s</code>
format specifier to print it as a string.
</p>

<pre>
payload = b"%7$s".ljust(8, b"A") + p64(0x4040c0)
</pre>

<p>
Send the payload after the <code>&gt;</code> prompt.
</p>

<p><b>5. Get the Flag</b></p>

<p>
The service prints the flag:
</p>

<pre>OPUCC{n3v3r_l3t_pr1ntf_34t_y0ur_1nput}</pre>
