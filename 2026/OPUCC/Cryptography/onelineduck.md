<img width="326" height="402" alt="Screenshot 2026-07-12 083957" src="https://github.com/user-attachments/assets/9f31da02-93db-4dc6-bc17-0658304f6047" />
<h2>Solution</h2>

<p>
The provided Python script verifies the flag by evaluating a polynomial instead of
comparing the input directly. The flag must first satisfy the following format:
</p>

<pre><code>OPUCC{[a-z0-9_?]{15}}</code></pre>

<p>
The checker then computes 24 values using the equation:
</p>

<pre><code>
sum(x[j] * pow(i + 2, j, MOD) for j in range(len(x))) % MOD
</code></pre>

<p>
where <code>x[j]</code> is the ASCII value of each character in the flag.
This is equivalent to evaluating a polynomial whose coefficients are the bytes of
the flag at 24 different points (2 to 25).
</p>

<p>
Since the flag contains only 22 characters (including the prefix <code>OPUCC{</code>
and the closing brace <code>}</code>), but the program provides 24 polynomial
evaluations, the system has enough information to uniquely recover every byte of
the flag. Instead of brute forcing every possible flag, the challenge can be solved
by constructing the corresponding system of linear equations and solving it using
modular linear algebra (for example, with SageMath).
</p>

<p>
After solving the equations, the recovered flag is:
</p>

<pre><code>OPUCC{1s_1t_0n3_l1n3?}</code></pre>

<h2>Flag</h2>

<pre><code>OPUCC{1s_1t_0n3_l1n3?}</code></pre>
