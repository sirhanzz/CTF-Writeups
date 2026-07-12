
<h2>Question</h2>

<p>The challenge description is shown below.</p>

<img src="https://github.com/user-attachments/assets/ef8a9b13-ece2-4790-8c9a-25859971674c"
     alt="Challenge Description"
     width="317">

<h3>Python Source Code</h3>

<pre><code>import re

# One-Line Duck
# Give me the flag and I'll quack if it's correct.

MOD = 147573952589676412927
NUM_CONSTRAINTS = 24
TARGETS = [
402712235,
1641913624228,
642033056552607,
67062383355890354,
3015890087404562875,
75621100142205097440,
54439766132355649047,
67501390084162708256,
142541596138675302568,
138738849737622044560,
94176950107903794750,
80251394051784004419,
72379374205401762317,
48774126549622494084,
39926213304781495935,
13537376142590932677,
97280306823067613871,
10483375554361384424,
58328316716263228943,
125554086513498291907,
82615529945542353023,
126943729914407991685,
39708635104512346187,
100672686568625571656
]

x = input("flag: ").encode()

assert re.fullmatch(rb"OPUCC\{[a-z0-9_?]{15}\}", x) and [
    sum(x[j] * pow(i + 2, j, MOD) for j in range(len(x))) % MOD
    for i in range(NUM_CONSTRAINTS)
] == TARGETS

print("Correct duck!")
</code></pre>

<h2>Solution</h2>

<p>
The challenge provides the complete Python flag checker. Instead of comparing the
input directly with the correct flag, it evaluates a mathematical expression.
</p>

<p>
First, the regular expression verifies that the input matches the required flag
format:
</p>

<pre><code>OPUCC{[a-z0-9_?]{15}}</code></pre>

<p>
This means the flag starts with <code>OPUCC{</code>, ends with <code>}</code>, and
contains exactly 15 lowercase letters, digits, underscores (<code>_</code>), or
question marks (<code>?</code>) in between.
</p>

<p>
The main verification is performed using the following expression:
</p>

<pre><code>sum(x[j] * pow(i + 2, j, MOD) for j in range(len(x))) % MOD</code></pre>

<p>
Each character of the flag is converted into its ASCII value. These ASCII values
are treated as the coefficients of a polynomial. The program evaluates this
polynomial at 24 different points (2 through 25) and compares the results with the
24 values stored in <code>TARGETS</code>.
</p>

<p>
Since there are only 22 characters in the entire flag but 24 equations, the system
contains enough information to uniquely determine every character. Rather than
brute-forcing every possible flag, the equations can be solved using modular linear
algebra (for example, with SageMath or modular Gaussian elimination) to recover the
original bytes of the flag.
</p>

<p>
After solving the equations, the recovered flag is:
</p>

<pre><code>OPUCC{1s_1t_0n3_l1n3?}</code></pre>

<h2>Flag</h2>

<pre><code>OPUCC{1s_1t_0n3_l1n3?}</code></pre>


