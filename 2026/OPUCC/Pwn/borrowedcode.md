<img width="420" height="575" alt="Screenshot 2026-07-12 080731" src="https://github.com/user-attachments/assets/b8b54565-6a08-407a-a946-a9dfeeefef69" />
<h2>Solution</h2>

<p>
First, check the binary protection using:
</p>

<pre>
checksec --file borrow
</pre>

<p>
The results show that NX is enabled, PIE is disabled, and there is no stack canary. This means shellcode cannot be executed directly, so a ret2libc attack is required.
</p>

<h3>Step 1: Find the buffer overflow</h3>

<p>
Open the binary in Ghidra or GDB and inspect the vulnerable function. The program reads 512 bytes into a 64-byte buffer, allowing the return address to be overwritten.
</p>

<pre>
char buf[64];
read(0, buf, 512);
</pre>

<p>
The offset to overwrite the return address is 72 bytes.
</p>

<h3>Step 2: Leak the libc address</h3>

<p>
Using the <code>pop rdi; ret</code> gadget, create a ROP chain that calls <code>puts()</code> on its Global Offset Table (GOT) entry. This leaks the real libc address of <code>puts()</code>.
</p>

<p>
Using the leaked address, calculate the libc base address:
</p>

<pre>
libc_base = leaked_puts - puts_offset
</pre>

<h3>Step 3: Execute system("/bin/sh")</h3>

<p>
After obtaining the libc base address, build a second ROP chain that calls <code>system("/bin/sh")</code>.
</p>

<pre>
padding
ret
pop rdi
"/bin/sh"
system
</pre>

<p>
A shell is spawned, allowing us to read the flag.
</p>

<h2>Flag</h2>

<pre>
OPUCC{r3t2l1bc_b0rr0w_th3_c0d3_y0u_n33d}
</pre>

<p>
This challenge demonstrates the use of a ret2libc attack. Instead of executing shellcode, the exploit reuses functions already loaded in libc to gain code execution and retrieve the flag.
</p>
