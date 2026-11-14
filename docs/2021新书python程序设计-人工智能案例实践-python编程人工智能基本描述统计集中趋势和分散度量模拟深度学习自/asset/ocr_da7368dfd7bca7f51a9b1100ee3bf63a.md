```
In [4]: import os
In [5]: os.remove('accounts.txt')
```

然后，使用rename函数将临时文件temp_file.txt重命名为
accounts.txt :

```
In [6]: os.rename('temp_file.txt', 'accounts.txt')
```