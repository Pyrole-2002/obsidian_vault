- It looks at a website and tells exactly what technologies were used to built it.
```bash
whatweb example.com
# -v for verbose
# -a is for aggression (-a 1 to -a 4)

whatweb 192.168.199.0/24 # subnet scan
whatweb -i targets.txt
```

- Some firewalls block requests coming from security tools by looking at the "User-Agent" header. By default, whatweb announces itself as `WhatWeb/0.5.5`. You can spoof this to look like a normal web browser.
```bash
whatweb -U "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36" example.com
```

