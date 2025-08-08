## poc

### content_poc.sh
```bash
#!/bin/bash
chroot  ./  ./qemu-mips-static\
	-g  123  -L  ./lib  \
	./web_cste/cgi-bin/cstecgi.cgi  <  ./content_poc.json
```

### content_poc.json
```json
{
	
}
```

## Reproduce
```bash
./content_poc.sh
```
```bash
gdb-multiarch ./web_cste/cgi-bin/cstecgi.cgi
```

![enter image description here](https://i.mji.rip/2025/08/08/fd9383438353f1c37af25f915b56a5c3.png)

Set a breakpoint before the `strtol` call. The parameter `pcVar4` (register `a0`) will show as NULL.
![enter image description here](https://i.mji.rip/2025/08/08/e1e263f2236bc954753e41eab26ce018.png)

Tracing execution into `strtol` confirms the NULL pointer is dereferenced, causing the crash.
![enter image description here](https://i.mji.rip/2025/08/08/02f1b1ead85087e3c9d4c2622471822d.png)


