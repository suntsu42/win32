---
Description: Sets the logon password pair for establishing credentials for scopes with universal naming convention (UNC) paths.
ms.assetid: 106ae11f-babd-4ea1-bc53-da14f91be6dd
title: IScopeAdm::SetLogonInfo method
ms.technology: desktop
ms.prod: windows
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# IScopeAdm::SetLogonInfo method

\[Indexing Service is no longer supported as of Windows XP and is unavailable for use as of Windows 8. Instead, use [Windows Search](https://msdn.microsoft.com/windows/desktop/6da601c6-3742-40ad-99f2-8817f7f642b3) for client side search and [Microsoft Search Server Express]( http://go.microsoft.com/fwlink/p/?linkid=258445) for server side search.\]

Sets the logon password pair for establishing credentials for scopes with universal naming convention (UNC) paths.

## Syntax


```C++
HRESULT SetLogonInfo(
  [in] BSTR bstrLogon,
  [in] BSTR bstrPassword
);
```



## Parameters

<dl> <dt>

*bstrLogon* \[in\]
</dt> <dd>

The logon name used to log on to the scope's UNC path.

</dd> <dt>

*bstrPassword* \[in\]
</dt> <dd>

The logon password, which must be a valid string. A null password is represented by a null string.

</dd> </dl>

## Return value

If this method succeeds, it returns **S\_OK**. Otherwise, it returns an **HRESULT** error code.

## Remarks

Any failures cause an error object to be created.

## Requirements



|                                     |                                                            |
|-------------------------------------|------------------------------------------------------------|
| Minimum supported client<br/> | Windows 2000 Professional \[desktop apps only\]<br/> |
| Minimum supported server<br/> | Windows 2000 Server \[desktop apps only\]<br/>       |
| End of client support<br/>    | Windows 7<br/>                                       |
| End of server support<br/>    | Windows Server 2008 R2<br/>                          |



## See also

<dl> <dt>

[**IScopeAdm**](iscopeadm.md)
</dt> </dl>

 

 



