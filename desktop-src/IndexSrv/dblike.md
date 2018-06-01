---
Description: DBLIKE
ms.assetid: cd015e0a-ecd3-4aa5-82c0-007fd293ebb9
title: DBLIKE
ms.technology: desktop
ms.prod: windows
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# DBLIKE

> [!Note]  
> Indexing Service is no longer supported as of Windows XP and is unavailable for use as of Windows 8. Instead, use [Windows Search](https://msdn.microsoft.com/windows/desktop/6da601c6-3742-40ad-99f2-8817f7f642b3) for client side search and [Microsoft Search Server Express]( http://go.microsoft.com/fwlink/p/?linkid=258445) for server side search.

 

The **DBLIKE** structure represents specific information required by the DBOP\_like operator.

``` syntax
typedef struct tagDBLIKE {
  LONG lWeight;      // weight on the dbop_like node
  GUID guidDialect;  // system to use for "likeness". E.g. Reg. Ex.
} DBLIKE;
```

### Remarks

For more information about the DBOP\_like operator, see [Operators for Scalars](operators-for-scalars.md).

 

 


