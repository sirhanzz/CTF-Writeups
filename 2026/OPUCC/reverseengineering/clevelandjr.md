<h2>Question</h2>

<p>The challenge description is shown below.</p>

<img src="https://github.com/user-attachments/assets/ef8a9b13-ece2-4790-8c9a-25859971674c"
     alt="Challenge Description"
     width="317">

<h2>Solution</h2>

<p>
This challenge provides a 64-bit ELF executable that asks the user to enter a
key. Running the program normally only displays a message asking for the
correct input, so the binary needs to be analyzed to understand how the key is
validated.
</p>

<h3>Step 1: Inspect the Binary</h3>

<p>
The binary was opened in <b>Ghidra</b> to inspect its functions and strings.
Several interesting strings can be found, including:
</p>

<pre>
du bist gut genug
hilfe
help
flag
correct
german only
Sprich die falsche Sprache.
</pre>

<p>
The sentence <b>"Sprich die falsche Sprache."</b> translates to
<b>"Speak the wrong language."</b>. This is the first hint that the program
does not expect proper German, but instead expects an intentionally incorrect
version of the phrase.
</p>

<h3>Step 2: Analyze the Validation Logic</h3>

<p>
Looking at the functions inside the binary reveals several names that match
German words:
</p>

<pre>
op_du
op_bist
op_gug
op_genugg
op_falsch
handle_token
</pre>

<p>
Instead of comparing the user's input directly with a fixed string, the program
splits the input into individual words and checks each token one by one.
</p>

<p>
The expected words are not the correct German phrase
<b>"du bist gut genug"</b>. Instead, the binary uses intentionally misspelled
words such as <b>gug</b> and <b>genugg</b>, matching the challenge hint about
using the "wrong language".
</p>

<h3>Step 3: Test Different Inputs</h3>

<p>
Using the clues from the strings, several combinations of the phrase were
tested. Although words like <b>falsch</b> appear inside the binary, they are
only used as part of the program's dialogue and are <b>not</b> part of the
flag.
</p>

<p>
After following the validation logic and testing the expected input, the
program accepts a modified version of the phrase that also includes leetspeak
characters.
</p>

<h3>Recovered Flag</h3>

<pre>OPUCC{du_b1st_gug_g3nuggggg}</pre>
