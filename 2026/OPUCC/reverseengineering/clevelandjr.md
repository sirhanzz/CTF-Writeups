<h2>Question</h2>

<p>The challenge description is shown below.</p>

<img src="https://github.com/user-attachments/assets/ef8a9b13-ece2-4790-8c9a-25859971674c"
     alt="Challenge Description"
     width="317">
<h2>Solution</h2>

<p>
This challenge provides an ELF executable that validates an input string.
Since running the program normally does not reveal the correct input,
the binary was analyzed using reverse engineering techniques.
</p>

<h3>Step 1: Inspect the Binary</h3>

<p>
The executable was opened using reverse engineering tools such as
<b>strings</b>, <b>Ghidra</b>, or <b>IDA</b>. Several interesting strings
were discovered:
</p>

<ul>
    <li>du bist gut genug</li>
    <li>gut genug</li>
    <li>hilfe</li>
    <li>help</li>
    <li>flag</li>
    <li>correct</li>
    <li>german only</li>
    <li>Cleaveland Jr says: <i>Sprich die falsche Sprache.</i></li>
</ul>

<p>
The sentence <b>"Sprich die falsche Sprache."</b> translates to
<b>"Speak the wrong language."</b>, suggesting that the program expects an
intentionally incorrect German phrase.
</p>

<h3>Step 2: Analyze the Functions</h3>

<p>
Several function names inside the binary provide additional hints:
</p>

<ul>
    <li>op_du</li>
    <li>op_bist</li>
    <li>op_gug</li>
    <li>op_genugg</li>
    <li>op_falsch</li>
    <li>handle_token</li>
</ul>

<p>
These functions indicate that the program parses individual words rather than
checking a single fixed string.
</p>

<h3>Step 3: Compare with Correct German</h3>

<p>
The correct German phrase is:
</p>

<pre>du bist gut genug</pre>

<p>
However, the binary contains the misspelled words:
</p>

<ul>
    <li><b>gut</b> → <b>gug</b></li>
    <li><b>genug</b> → <b>genugg</b></li>
</ul>

<p>
<p>
Another hidden message says:
</p>

<blockquote>
JA. So falsch ist richtig.
</blockquote>

<p>
This translates to:
</p>

<blockquote>
"Yes. Being wrong is correct."
</blockquote>

<p>
This confirms that the challenge intentionally expects incorrect German
instead of the proper phrase.
</p>

<h3>Step 4: Recover the Input</h3>

<p>
Combining all discovered tokens gives the expected input:
</p>

<pre>du bist gug genugg falsch</pre>




