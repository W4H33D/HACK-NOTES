# Authentication Flaws Checklist

## Password-Based Login

- Check for Username and Password brute-forcing
- Check for subtle changes in responses i.e Status Code, Error message, Response Time, etc.
- Check for flawed brute forcing protection to bypass IP Blocking and Account locking to enumerate usernames and passwords.
- Try brute forcing with rate limiting i.e try with some delays or bypassing the captcha.
- Check for HTTP basic authentication without HSTS or exploitable using other vulnerabilities like CSRF or XSS

## Vulnerabilities in multi-factor authentication

- Skipping for 2FA i.e user is in a login state after reloading.
- Check for 2FA bypass with another user cookie i.e go through the 2FA process with other user's cookies.
- Brute-forcing 2FA verification codes and try to bypass any protection mentioned above to guess code.

## Vulnerabilities in other authentication mechanisms

- Try to reverse engineering the "Remember me" Cookie.
- If the Resetting user password functionality exits try to reset another user's password and get its password.
- Check if the website Sending passwords by email in a secure manner that protects it from MitM attacks and after the first login immediately changing the method is forced or not.
- Check for guessable parameters in Reset password URLs and they correctly check for the token is stored in the server.
- Check if the Reset tokens send by email are vulnerable to Reset Poisoning attacks 
- Changing passwords and usernames can be tested with the same techniques for password-based logins to enumerate or for password brute force.