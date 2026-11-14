# 02 · Web Exploitation

[← Back to index](README.md)

Placeholders: `<IP>` = target.

---

## Burp Suite basics

### Intercept and modify requests
Burp sits between the browser and the server, so you can see and change cookies, parameters and headers before they are sent.
1. Point the browser to the proxy `127.0.0.1:8080` (or use Burp's built-in browser).
2. **Proxy → Intercept → Intercept on**, then load the page.
3. Edit the request and click **Forward**.

### Replay a request many times
Send the request to **Repeater** (`Ctrl+R`) to modify and resend it without going through the browser each time. Ideal for testing payloads one by one.

---

## There is a login form

### Try default credentials first
Many lab services and appliances keep their factory credentials.
- `admin:admin`, `admin:password`, `root:root`
- Search `<product name> default credentials` for the specific software.

### Bypass the login with SQL injection
If the backend builds the query by concatenating input, e.g.
`SELECT * FROM users WHERE username='<user>' AND password='<pass>'`,
the input can change the logic of the query.
```text
admin'-- -
admin' OR '1'='1
' OR 1=1-- -
```
- `admin'-- -`: closes the string and comments out the rest, so the password check disappears. Most reliable when the user `admin` exists.
- `--` needs a space after it in MySQL; the trailing `-` keeps that space from being trimmed. `#` also works in MySQL (`%23` in a URL).
- `admin' OR '1'='1`: `AND` is evaluated before `OR`, so it becomes `username='admin' OR ('1'='1' AND password='...')` → only works if `admin` exists.
- `' OR 1=1-- -`: no username needed; usually logs you in as the first user in the table.
- None of these work if the app fetches the user first and compares the password hash in code.

### SQL injection beyond the login (UNION, error-based, blind, sqlmap)
> **TODO:** detecting the injection point, `ORDER BY` / `UNION SELECT` column counting, dumping tables, `sqlmap -r request.txt`.

---

## The URL or request contains IDs

### IDOR (Insecure Direct Object Reference)
The server trusts the ID sent by the client without checking that it belongs to you.
```text
/profile?user_id=1   →   /profile?user_id=2
```
- Look for IDs in URLs, POST bodies, cookies and API paths (`/api/users/1`).
- Try neighbouring values, `0`, and other users' IDs.

---

## The app uses cookies or tokens for sessions or roles

### Cookie with readable / Base64 values
If the role or username is stored client-side, you can decode it, change it and send it back.
```bash
echo 'dXNlcg==' | base64 -d      # decode → user
echo -n 'admin' | base64          # encode → YWRtaW4=
```
- `-n`: no trailing newline; without it the newline gets encoded too and the value is wrong.
- Also check plain values like `role=user` → `role=admin`.
- Edit cookies in Burp or in the browser DevTools (**Storage / Application → Cookies**).

### JWT (JSON Web Tokens)
> **TODO:** decoding header/payload, `alg: none`, weak HMAC secret cracking, key confusion.

---

## Other vulnerabilities

### XSS (Cross-Site Scripting)
> **TODO:** reflected vs stored, basic payloads, stealing cookies in labs.

### LFI / RFI (File inclusion)
> **TODO:** path traversal, PHP wrappers, log poisoning, remote inclusion.

### SSTI (Server-Side Template Injection)
> **TODO:** detection payloads per template engine (Jinja2, Twig…), RCE.

### Command injection
> **TODO:** separators (`;`, `|`, `&&`, `$()`), blind detection, filter bypasses.
