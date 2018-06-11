---
Description: Windows Management Instrumentation (WMI) can create the sink to receive asynchronous callbacks for a client application in a separate process.
audience: developer
author: REDMOND\\markl
manager: REDMOND\\markl
ms.assetid: 3d3111ac-7d83-48d6-94e4-36cb46a506fa
ms.prod: windows-server-dev
ms.technology: windows-management-instrumentation
ms.tgt_platform: multiple
title: Lowering the Security for a Sink in a Separate Process
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# Lowering the Security for a Sink in a Separate Process

Windows Management Instrumentation (WMI) can create the sink to receive asynchronous callbacks for a client application in a separate process. The separate process is Unsecapp.exe. Use the [**IWbemUnsecuredApartment**](/windows/desktop/api/Wbemcli/nn-wbemcli-iwbemunsecuredapartment) interface. **IWbemUnsecuredApartment** allows you to control whether Unsecapp.exe authenticates callbacks to the sink. For more information, see [Setting Security on an Asynchronous Call](setting-security-on-an-asynchronous-call.md).

You can then lower the security on that process and WMI can access the sink without restriction. To assist with this technique WMI provides the Unsecapp.exe process to function as the separate process. You can host Unsecapp.exe with a call to the [**IUnsecuredApartment**](/windows/desktop/api/Wbemcli/nn-wbemcli-iunsecuredapartment) interface.

The [**IUnsecuredApartment**](/windows/desktop/api/Wbemcli/nn-wbemcli-iunsecuredapartment) interface allows a client application to create a separate dedicated process running Unsecapp.exe for hosting a [**IWbemObjectSink**](iwbemobjectsink.md) implementation. The dedicated process can call [**CoInitializeSecurity**](https://msdn.microsoft.com/windows/desktop/e0933741-6b75-4ce1-aa63-6240e4a7130f) to grant WMI access to the dedicated process without compromising the security of the main process. After initialization, the dedicated process acts as an intermediary between the main process and WMI.

The following procedure describes how to perform an asynchronous call with [**IUnsecuredApartment**](/windows/desktop/api/Wbemcli/nn-wbemcli-iunsecuredapartment).

**To perform an asynchronous call with IUnsecuredApartment**

1.  Create a dedicated process with a call to [**CoCreateInstance**](https://msdn.microsoft.com/windows/desktop/7295a55b-12c7-4ed0-a7a4-9ecee16afdec).

    The following code example calls [**CoCreateInstance**](https://msdn.microsoft.com/windows/desktop/7295a55b-12c7-4ed0-a7a4-9ecee16afdec) to create a dedicated process.

    ```C++
    IUnsecuredApartment* pUnsecApp = NULL;

    CoCreateInstance(CLSID_UnsecuredApartment, NULL, 
      CLSCTX_LOCAL_SERVER, IID_IUnsecuredApartment, 
      (void**)&amp;pUnsecApp);
    ```

    

2.  Instantiate the sink object.

    The following code example creates a new sink object.

    ```C++
    CMySink* pSink = new CMySink;
    pSink->AddRef();
    ```

    

3.  Create a stub for the sink.

    A stub is a wrapper function produced from the sink.

    The following code example calls [**CreateObjectStub**](/windows/desktop/api/Wbemcli/nf-wbemcli-iunsecuredapartment-createobjectstub) to create a stub for the sink.

    ```C++
    IUnknown* pStubUnk = NULL; 
    pUnsecApp->CreateObjectStub(pSink, &amp;pStubUnk);
    ```

    

4.  Call [**QueryInterface**](https://msdn.microsoft.com/windows/desktop/54d5ff80-18db-43f2-b636-f93ac053146d) for the wrapper, and request a pointer to the [**IWbemObjectSink**](iwbemobjectsink.md) interface.

    The following code example calls [**QueryInterface**](https://msdn.microsoft.com/windows/desktop/54d5ff80-18db-43f2-b636-f93ac053146d) and requests a pointer to the [**IWbemObjectSink**](iwbemobjectsink.md) interface.

    ```C++
    IWbemObjectSink* pStubSink = NULL;
    pStubUnk->QueryInterface(IID_IWbemObjectSink, (void **)&amp;pStubSink); pStubUnk->Release();
    ```

    

5.  Release the sink object pointer.

    You can release the object pointer because the stub now owns the pointer.

    The following code example releases the sink object pointer.

    ```C++
    pSink->Release();
    ```

    

6.  Use the stub in any asynchronous call.

    When finished with the call, release the local reference count.

    The following code example uses the stub in an asynchronous call.

    ```C++
    // pServices is an IWbemServices* object
    pServices->CreateInstanceEnumAsync(strClassName, 0, NULL, pStubSink);
    ```

    

    At times you may have to cancel an asynchronous call after you make the call. If you need to cancel the call, cancel the call with the same pointer that originally made the call.

    The following code example shows how to cancel an asynchronous call.

    ```C++
    pServices->CancelAsyncCall(pStubSink);
    ```

    

7.  Release the local reference count when you are done using the asynchronous call.

    Make sure to release the *pStubSink* pointer only after you confirm that the asynchronous call does not need to be canceled. Further, do not release *pStubSink* after WMI releases the *pSink* sink pointer. Releasing *pStubSink* after *pSink* creates a circular reference count in which both the sink and the stub stay in memory forever. Instead, a possible location to release the pointer is in the [**IWbemObjectSink::SetStatus**](/windows/desktop/api/Wbemcli/nf-wbemcli-iwbemobjectsink-setstatus) call, made by WMI to report that the original asynchronous call is complete.

8.  When finished, uninitialize COM with a call to [**Release()**](https://msdn.microsoft.com/windows/desktop/4b494c6f-f0ee-4c35-ae45-ed956f40dc7a).

    The following code example shows how to call [**Release()**](https://msdn.microsoft.com/windows/desktop/4b494c6f-f0ee-4c35-ae45-ed956f40dc7a) on the *pUnsecApp* pointer.

    ```C++
    pUnsecApp->Release();
    ```

    

    The code examples in this topic require the following reference and \#include statement to correctly compile.

    ```C++
    #include <wbemidl.h>
    #pragma comment(lib, "wbemuuid.lib")
    ```

    

For more information about the [**CoInitializeSecurity**](https://msdn.microsoft.com/windows/desktop/e0933741-6b75-4ce1-aa63-6240e4a7130f) function and parameters, see the [COM](https://msdn.microsoft.com/windows/desktop/b21a6b08-c17c-4fcc-bc60-39037bc9902f) documentation in the Platform Software Development Kit (SDK).

 

 


