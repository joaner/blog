---
title: 误删线上的表
id: 294
categories:
  - php
date: 2016-03-08 14:49:26
tags:
---

刚才在从线上表里删除产品时，不小心删除了整个表。也就是`where`条件没写上。

因为是分几次删除，所以先把上次删除的命令调出来，然后修改后面的ID参数。

```
delete from product where id ;
```

结果刚删掉老的参数，同事问我问题，我手就自然落在键盘上，触发了`Enter`键。

幸好昨天晚上还备份过，所以用了两三分钟，将产品数据恢复过来了，不过服务因此也中断了几分钟。

这是最严重的错误，以后绝对不能用SQL直接删除数据。
