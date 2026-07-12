<img width="327" height="496" alt="Screenshot 2026-07-12 082957" src="https://github.com/user-attachments/assets/89918d08-c0bc-4c7a-ad80-143534b89d5b" />
<h2>Steps</h2>

<ol>
  <li>
    <p>Download the challenge archive and extract it using the password:</p>
    <pre><code>infected</code></pre>
  </li>

  <li>
    <p>Open a terminal inside the extracted folder and identify the file types:</p>
    <pre><code>file *</code></pre>
    <p>This helps identify the executable file and the damaged or encrypted file.</p>
  </li>

  <li>
    <p>Check the executable for readable strings:</p>
    <pre><code>strings &lt;executable_name&gt;</code></pre>
    <p>The strings may reveal filenames, error messages, functions, or clues used by the program.</p>
  </li>

  <li>
    <p>Open the executable in a reverse-engineering tool such as <strong>Ghidra</strong>.</p>
  </li>

  <li>
    <p>Analyse the functions that open, read, and modify the victim file. The challenge hint provides these decimal values:</p>
    <pre><code>17
19</code></pre>
  </li>

  <li>
    <p>Convert the decimal values into hexadecimal:</p>
    <pre><code>17 = 0x11
19 = 0x13</code></pre>
  </li>

  <li>
    <p>Search for <code>0x11</code> and <code>0x13</code> inside Ghidra to locate the file-modification function.</p>
  </li>

  <li>
    <p>Trace how the program modifies each byte of the damaged file. Reverse the same operation to recover the original file.</p>
  </li>

  <li>
    <p>Open the recovered file. The recovered information points to <strong>Charlie Puth</strong>.</p>
  </li>

  <li>
    <p>Submit the final flag:</p>
    <pre><code>OPUCC{w3_4r3_ch4rl13_puthhhhhh}</code></pre>
  </li>
</ol>

<h3>Final Flag</h3>

<pre><code>OPUCC{w3_4r3_ch4rl13_puthhhhhh}</code></pre>
