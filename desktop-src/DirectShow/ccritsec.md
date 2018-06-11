---
Description: The CCritSec class provides a thread lock.
ms.assetid: ecc60afe-15d0-4780-8133-1dfc558c6325
title: CCritSec class
ms.technology: desktop
ms.prod: windows
ms.author: windowssdkdev
ms.topic: interface
ms.date: 05/31/2018
---

# CCritSec class

The **CCritSec** class provides a thread lock.

This class is a thin wrapper for a Windows **CRITICAL\_SECTION** object. You can lock and unlock the thread by calling the [**CCritSec::Lock**](ccritsec-lock.md) and [**CCritSec::Unlock**](ccritsec-unlock.md) methods. However, it is safer to use this class in conjunction with the [**CAutoLock**](cautolock.md) class. When the **CAutoLock** class goes out of scope, it automatically unlocks the **CCritSec** object. Moreover, it compiles to efficient inline code.



| Public Member Variables                            | Description                                              |
|----------------------------------------------------|----------------------------------------------------------|
| [**m\_currentOwner**](ccritsec-m-currentowner.md) | Thread identifier of the owning thread.                  |
| [**m\_lockCount**](ccritsec-m-lockcount.md)       | Number of outstanding locks on this object.              |
| [**m\_fTrace**](ccritsec-m-ftrace.md)             | Boolean value that specifies whether to trace this lock. |
| Public Methods                                     | Description                                              |
| [**CCritSec**](ccritsec-ccritsec.md)              | Constructor method.                                      |
| [**~CCritSec**](ccritsec--ccritsec.md)            | Destructor method.                                       |
| [**Lock**](ccritsec-lock.md)                      | Locks the critical section object.                       |
| [**Unlock**](ccritsec-unlock.md)                  | Unlocks the critical section object.                     |



 

## Requirements



|                    |                                                                                                                                                                                            |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Header<br/>  | <dl> <dt>Wxutil.h (include Streams.h)</dt> </dl>                                                                                    |
| Library<br/> | <dl> <dt>Strmbase.lib (retail builds); </dt> <dt>Strmbasd.lib (debug builds)</dt> </dl> |



## See also

<dl> <dt>

[Critical Section Objects](https://msdn.microsoft.com/library/windows/desktop/ms682530)
</dt> <dt>

[DirectShow Base Class Reference](base-class-reference.md)
</dt> </dl>

 

 



