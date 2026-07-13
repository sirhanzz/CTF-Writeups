<img width="422" height="576" alt="Screenshot 2026-07-12 080817" src="https://github.com/user-attachments/assets/c35bdbee-d78f-42c6-bfe7-51239ea465a9" />

<h2>Solution</h2>

<p>
This challenge provides a note-taking program that allows users to create,
delete, edit, and view notes. The vulnerability is a <b>Use-After-Free (UAF)</b>
because deleting a note frees the memory but does not clear the stored pointer.
As a result, a deleted note can still be viewed or edited.
</p>

<h3>Step 1: Identify the Vulnerability</h3>

<p>
After creating and deleting a note, the same index can still be accessed using
the <b>View</b> or <b>Edit</b> options. This confirms the presence of a
Use-After-Free vulnerability.
</p>

<pre>
Create Note
     ↓
Delete Note
     ↓
View/Edit Still Works
</pre>

<h3>Step 2: Leak the libc Address</h3>

<p>
A large chunk (0x500 bytes) was allocated and freed. Since it is too large for
the tcache, it is placed into the <b>unsorted bin</b>. Viewing the freed chunk
reveals pointers into <b>main_arena</b>, allowing the libc base address to be
calculated.
</p>

<pre>
create(0, 0x500)
delete(0)
view(0)
</pre>

<h3>Step 3: Leak the Heap Address</h3>

<p>
Two small chunks were allocated and freed. Because glibc 2.35 uses
<b>safe-linking</b>, the pointers stored in the tcache are encoded. Reading the
freed chunks with the UAF vulnerability allows the heap address to be recovered.
</p>

<pre>
create(A)
create(B)
delete(A)
delete(B)
view(B)
view(A)
</pre>

<h3>Step 4: Poison the Tcache</h3>

<p>
Using the UAF, the forward pointer (<code>fd</code>) of a freed chunk was
modified so that the next allocation would return the address of
<b>atoi@GOT</b>.
</p>

<pre>
Freed Chunk
     ↓
atoi@GOT
</pre>

<h3>Step 5: Overwrite atoi()</h3>

<p>
After poisoning the tcache, two allocations were performed. The second
allocation returned the <code>atoi@GOT</code> entry, which was overwritten with
the address of <code>system()</code>.
</p>

<pre>
atoi() → system()
</pre>

<h3>Step 6: Get the Flag</h3>

<p>
Since every menu choice is normally processed using <code>atoi()</code>,
entering <code>/bin/sh</code> causes the program to execute
<code>system("/bin/sh")</code>, giving a shell. The flag can then be obtained
using:
</p>

<pre>
cat flag.txt
</pre>

