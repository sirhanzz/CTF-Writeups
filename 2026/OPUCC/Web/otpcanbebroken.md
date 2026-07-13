<img width="422" height="482" alt="Screenshot 2026-07-12 075215" src="https://github.com/user-attachments/assets/52e5b597-f360-4da9-9abd-e483f54e6651" />

### 1. Recon
Open `http://10.5.14.225:30012/`.

- View source → HTML comment lists test users: `j.doe`, `a.smith`, `b.johnson`
- Team section also shows `@j.doe` (Operations Manager)
- Go to **Login** → **Forgot password?**

Target: take over `j.doe` (manager — has the flag).

---

### 2. Request an OTP
`/forgot-password` → username `j.doe` → **Send OTP**.

You’re sent to `/verify-otp`. Message: **4-digit** code emailed (you never see the email).

---

### 3. Brute-force the OTP
Only `0000`–`9999` (10,000 codes). No useful rate limit.

Payload: numbers `0000`–`9999`. Look for:

```json
{"status":"success","redirect":"/reset-password"}
```

Keep the **new** `Set-Cookie: session=...` from that success response.

---

### 4. Reset the password
With that session cookie:

```text
POST /reset-password
password=YourNewPass123
```

You should be redirected to `/login`.

---

### 5. Login and grab the flag
Login: `j.doe` / your new password → `/dashboard`.

Flag is on the manager dashboard:

```text
OPUCC{k4mU_m3m4nG_p4dU}
```
