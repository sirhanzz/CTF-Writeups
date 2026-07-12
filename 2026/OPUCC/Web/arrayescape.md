<img width="422" height="522" alt="Screenshot 2026-07-12 075310" src="https://github.com/user-attachments/assets/707fc760-3f73-4358-90e9-e0a7d5d7ac7a" />


<h3>Step 1: Fake an Admin Token</h3>
<p>
To read the files, you need to be an "admin." The server won't make you one, but it accidentally left its secret password (the JWT secret) inside the website's source code. We can decode this secret key and use it to forge our own "admin" token.
</p>

<h3>Step 2: Trick the "No Hackers" Filter (Array Bypass)</h3>
<p>
The server tries to block people from snooping around by rejecting any file request that contains <code>..</code> (which means "go back a folder"). However, if we send two files at the same time in the URL (like <code>?file=a&file=b</code>), the server treats it as a list (an array). The security filter is only designed to check single text strings, so our list sneaks right past the <code>..</code> block.
</p>

<h3>Step 3: Trick the Name Filter (Newline Bypass)</h3>
<p>
Next, the server checks to make sure the file name only uses normal letters and numbers. Because we sent a list, the server squishes it into a single word with a comma (e.g., <code>a,b</code>). The comma would normally get blocked! But, if we inject a hidden "new line" code (<code>%0A</code>) into our list, the server's filter gets confused and only checks the safe parts of our text, completely missing the comma.
</p>

<h3>Step 4: Grab the Flag</h3>
<p>
Now that the filters are broken, we can use <code>../../secrets/flag.txt</code> to tell the server to step backward out of its normal folders and into the secret folder. We send this file request along with our forged admin token.
</p>

<h3>Recovered Flag</h3>
<pre>OPUCC{arr4y_byp4ss_p4th_tr4v}</pre>
