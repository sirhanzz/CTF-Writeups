<img width="327" height="451" alt="Screenshot 2026-07-12 084137" src="https://github.com/user-attachments/assets/d73eee89-2ed4-4a72-86be-fbd55027df7e" />
<h1>TABLE FOR TWO</h1>

<h2>Steps</h2>

<h3>Step 1 Decode the Mission Brief</h3>

<p>The challenge provides a message written using hyphenated numbers.</p>

<p>Decode the numbers using <strong>A1Z26</strong>, where:</p>

<pre>1 = A
2 = B
3 = C
...
26 = Z</pre>

<p>The decoded message is:</p>

<pre>THE PERRIN PAGES WILL HELP YOU FIND THE PASSWORD
BUT DONT BE DUPED
CUT DOWN THE WOODS
THEY BE ERDOS</pre>

<h3>Step 2 Identify the Number Sequences</h3>

<p>The relevant Perrin numbers are:</p>

<pre>0, 2, 3, 5, 7, 10, 12, 17, 22, 29, 39, 51, 68, 90, 119, 158, 209, 277</pre>

<p>The relevant Erdős–Woods numbers are:</p>

<pre>16, 22, 34, 36, 46, 56</pre>

<p>The number <strong>22</strong> appears in both sequences. Based on the clue “Don't be duped,” it must still be removed.</p>

<h3>Step 3 Examine the PDF</h3>

<p>Open <strong>Challenge.pdf</strong> and go to page 5.</p>

<p>The page contains a table named <strong>Host Printer Binder Audit</strong>. Each binder sheet number has a corner stamp.</p>

<pre>Sheet 2   → 3
Sheet 3   → 9
Sheet 5   → 3
Sheet 7   → 0
Sheet 10  → 3
Sheet 12  → 6
Sheet 16  → X
Sheet 17  → 3
Sheet 22  → F
Sheet 29  → 5
Sheet 34  → A
Sheet 36  → D
Sheet 39  → 3
Sheet 46  → B
Sheet 51  → 4
Sheet 56  → E
Sheet 68  → 3
Sheet 90  → 4
Sheet 119 → 3
Sheet 158 → 6
Sheet 209 → 3
Sheet 277 → 7</pre>

<p>The PDF footer also contains a password-protected Pastebin link:</p>

<pre>https://pastebin.com/viH4AecZ</pre>

<h3>Step 4 Filter the Binder Rows</h3>

<p>Keep the binder sheet numbers that are Perrin numbers.</p>

<p>Remove the Erdős–Woods numbers:</p>

<pre>16, 22, 34, 36, 46, 56</pre>

<p>The remaining corner stamps are:</p>

<pre>3 9 3 0 3 6 3 5 3 4 3 4 3 6 3 7</pre>

<p>Combine them into one hexadecimal string:</p>

<pre>3930363534343637</pre>

<h3>Step 5 Convert Hexadecimal to ASCII</h3>

<p>Split the hexadecimal string into pairs:</p>

<pre>39 30 36 35 34 34 36 37</pre>

<p>The password is:</p>

<pre>90654467</pre>

<p>The conversion can also be confirmed using Python:</p>

<pre>hex_value = "3930363534343637"
password = bytes.fromhex(hex_value).decode()
print(password)</pre>

<p>Output:</p>

<pre>90654467</pre>

<h3>Step 6 Unlock the Pastebin</h3>

<p>Open the Pastebin link:</p>

<pre>https://pastebin.com/viH4AecZ</pre>

<p>Enter the following password:</p>

<pre>90654467</pre>

<h3>Step 7 Retrieve the Flag</h3>

<p>After unlocking the Pastebin, the flag is displayed.</p>

<h2>Final Flag</h2>

<pre>OPUCC{y0ur_w1ll_b3_4_crypt0_m4st4}</pre>
