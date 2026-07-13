<img width="420" height="962" alt="Screenshot 2026-07-12 080243" src="https://github.com/user-attachments/assets/94df75ac-ca37-473f-9181-33f04ccd8189" />

<h3> Steps</h3>

<p><b>1. Recon the Website</b></p>

<p>
Visit the target website and identify the technologies in use. Checking
<code>robots.txt</code> and Joomla files reveals that the site is running
<b>Joomla 6.1.2</b> and exposes several interesting directories such as
<code>/administrator/</code> and <code>/secure-vault/</code>.
</p>

<p><b>2. Identify the Vulnerable Extension</b></p>

<p>
Inspect the installed Joomla components and find that the website is using
<b>JCE Editor 2.9.99</b>. This version is vulnerable to
<b>CVE-2026-48907</b>, which allows an unauthenticated file upload leading to
remote code execution.
</p>

<p><b>3. Upload a PHP Payload</b></p>

<p>
Obtain the CSRF token from the homepage, then upload a PHP payload through the
vulnerable profile import feature. Accessing the uploaded file inside the
<code>/tmp/</code> directory confirms that PHP code execution is possible.
</p>

<pre>
Upload PHP file
      ↓
Access /tmp/payload.xml.php
      ↓
Code Executes
</pre>

<p><b>4. Read the Joomla Configuration</b></p>

<p>
Use the uploaded PHP payload to read the
<code>configuration.php</code> file. This reveals the MySQL database
credentials used by Joomla.
</p>

<pre>
cat /var/www/html/configuration.php
</pre>

<p><b>5. Access the Database</b></p>

<p>
Using the recovered credentials, connect to the MySQL database, list the
available tables, and locate the table containing the flag.
</p>

<pre>
SHOW TABLES;
SELECT * FROM jos_flag;
</pre>

<p><b>6. Get the Flag</b></p>

<p>
The <code>jos_flag</code> table contains the challenge flag.
</p>

<pre>OPUCC{s3cr3t_4g3nt_f0und}</pre>
