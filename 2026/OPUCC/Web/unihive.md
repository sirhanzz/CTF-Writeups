<img width="412" height="536" alt="Screenshot 2026-07-12 075508" src="https://github.com/user-attachments/assets/5cf8a860-11ca-4d65-9719-14a669be65ee" />


<h1>Solution</h1>

<h2>Step 1: Map the app</h2>

| Page | What It Does |
| --- | --- |
| index.php | Login  |
| register.php | Create account |
| reset.php | Password reset via security answer |
| feed.php | Campus feed |
| profile.php?id=N | User profiles |
| admin.php | Admin settings |

<h2>Step 2: Register and look around</h2>

1. Create any account on register.php.
2. You'll land on feed.php.
3. Note your profile link e.g. profile.php?id=17
4. Try admin.php

<h2>Step 3: Find the vulnerability</h2>

1. Change the profile.php?id="" until you get admin account
2. Copy the security answer

<h2>Step 4: Reset the admin password</h2>

1. Open reset password page.
2. Enter username admin.
3. Enter the security answer copied earlier.
4. Set a new password.
5. Submit.

<h2>Step 5: Log in as admin and get the flag</h2>

1. Open admin.php.
2. Get the flag in the yellow box.
