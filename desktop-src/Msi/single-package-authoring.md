---
Description: A dual-purpose package is a Windows Installer 5.0 package that has been authored to be capable of installing an application in either the per-user or per-machine installation context.
ms.assetid: b2da9fba-8fdf-48fa-a662-6b3eee1bfe87
title: Single Package Authoring
ms.technology: desktop
ms.prod: windows
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# Single Package Authoring

A dual-purpose package is a Windows Installer 5.0 package that has been authored to be capable of installing an application in either the per-user or per-machine [installation context](installation-context.md). Setup developers that use a dual-purpose package for their application can provide their users with a choice of installation context at installation time and can remove UAC credential prompts from per-user installations on Windows 7 or Windows Server 2008 R2. The development of a dual-purpose Windows Installer 5.0 package for installation on Windows 7 and Windows Server 2008 R2 is referred to as single package authoring.

You can begin to develop dual-purpose packages for Windows 7 and Windows Server 2008 R2 by using Windows Installer 5.0, the [**MSIINSTALLPERUSER**](msiinstallperuser.md) property, the [**ALLUSERS**](allusers.md) property, and the per-user capable known folders and registrations of the [Windows Shell](https://msdn.microsoft.com/windows/desktop/1d0d4ed7-9b85-4d70-bf1f-82567a1687fb). When Windows Installer 5.0 installs a dual-purpose package in the per-user context on Windows 7 or Windows Server 2008 R2, the installer directs file and registry entries to per-user locations and does not display UAC prompts for credentials. When Windows Installer 5.0 installs a dual-purpose package in the per-machine context, the installer directs files and registry entries to per-machine locations and prompts for UAC credentials to confirm that the user has sufficient privileges to install software for all users of the computer. Once Windows Installer 5.0 installs an application, it uses the same installation context for all subsequent updates, repairs, or removal of the application.

**[Windows Installer 4.5 or earlier](not-supported-in-windows-installer-4-5.md):** The [**MSIINSTALLPERUSER**](msiinstallperuser.md) property and per-user versions of folders referenced by the [**ProgramFilesFolder**](programfilesfolder.md), [**CommonFilesFolder**](commonfilesfolder.md), [**ProgramFiles64Folder**](programfiles64folder.md), and [**CommonFiles64Folder**](commonfiles64folder.md) properties are not supported. The **FOLDERID\_UserProgramFiles** and **FOLDERID\_UserProgramFilesCommon** folders are available beginning with Windows 7 and Windows Server 2008 R2. This means installations developed for Windows Installer 4.5 or earlier direct files and registry entries to FOLDERID\_ProgramFiles, FOLDERID\_ProgramFilesCommon, FOLDERID\_ProgramFilesX64, and FOLDERID\_ProgramFilesCommonX64. Because these are locations accessible to other users of the computer, Windows Vista and later systems require the display of UAC prompts for credentials.

When a user installs a dual-purpose package authored for Windows Installer 5.0 with Windows Installer 4.5 or earlier, the installer ignores the [**MSIINSTALLPERUSER**](msiinstallperuser.md) property. In this case, the installation can direct files and registry entries to locations accessible to other users and require the system to display UAC prompts for credentials. Windows Installer 5.0 can install a package that was developed for Windows Installer 4.5 or earlier, however, the installation directs files and registry entries to locations accessible to other users and requires the system to display UAC prompts for credentials.

## Development Guidelines

Adhere to the following single-package authoring guidelines to ensure that the package can be installed in either the per-user or per-machine context. Follow these guidelines to enable the user to choose at installation time a per-user or per-machine installation and to remove UAC prompts from per-user installations.

-   Per-user installation requires Windows Installer 5.0 on Windows 7 or Windows Server 2008 R2. You should inform the user that the package supports per-machine installation of the application on earlier versions of the system.
-   Initialize the values for the [**ALLUSERS**](allusers.md) and [**MSIINSTALLPERUSER**](msiinstallperuser.md) properties in the [Property Table](property-table.md) of your dual-purpose package. Use **ALLUSERS** value of 2 and a **MSIINSTALLPERUSER** value of 1 as the initial values. This specifies per-user installation as the default for the dual-purpose package.
-   Consider authoring a dialog box for the [user interface](user-interface.md) of your dual-purpose package that enables the user to choose the context at installation time. Author the controls on this custom dialog box to set the values of the [**ALLUSERS**](allusers.md) and [**MSIINSTALLPERUSER**](msiinstallperuser.md) properties. For the **ALLUSERS** value of 2, set **MSIINSTALLPERUSER** to a value of 1 to specify a per-user installation and set **MSIINSTALLPERUSER** to an empty string ("") to specify a per-machine installation. Users can also set the **ALLUSERS** and **MSIINSTALLPERUSER** on the command line if they install the package from the command line.
-   Validate the package using [Internal Consistency Evaluators - ICEs](internal-consistency-evaluators-ices.md). The package must be able to pass validation by [ICE105](ice-105.md) to be a valid dual-purpose package.
-   Use the [Registry Table](registry-table.md) and [RemoveRegistry Table](removeregistry-table.md) to redirect registry entries to the per-user parts of the registry during per-user installations. In a per-user installation, registry entries that have -1 in the Root column are redirected to **HKEY\_CURRENT\_USER**, and in a per-machine installation these are directed to **HKEY\_LOCAL\_MACHINE**. In a per-user installation, registry entries that have msidbRegistryRootClassesRoot (0) in the Root column are redirected to **HKCU**\\**Software**\\**Classes**, and in a per-machine installation, these are directed to **HKLM**\\**Software**\\**Classes**.
-   Use the [**ProgramFilesFolder**](programfilesfolder.md) property in the [Directory table](directory-table.md) of [32-bit Windows Installer Packages](32-bit-windows-installer-packages.md) to specify the locations of directories containing 32-bit components not shared across applications. When a user installs the dual-purpose package using the per-machine context, these components are saved in the Program Files folder on 32-bit versions of Windows and in the Program Files (x86) folder on 64-bit versions of the system. The components in these directories can be accessed by all users. When a user installs the dual-purpose package on Windows 7 or Windows Server 2008 R2 using the per-user context, these components are saved in the Programs folder of the current user (for example at %LocalAppData%\\Programs) and can be accessed only by that user.
-   Use the [**CommonFilesFolder**](commonfilesfolder.md) property in the [Directory table](directory-table.md) of [32-bit Windows Installer Packages](32-bit-windows-installer-packages.md) to specify the locations of directories containing 32-bit components shared across applications. When a user installs the dual-purpose package using the per-machine context, these components are saved in the Common Files folder and can be accessed by all users. When a user installs the dual-purpose package on Windows 7 or Windows Server 2008 R2 using the per-user context, these components are saved in the Common folder of the current user (for example at %LocalAppData%\\Programs\\Common) and can be accessed only by that user.
-   Use the [**ProgramFiles64Folder**](programfiles64folder.md) property in the [Directory table](directory-table.md) of [64-bit Windows Installer Packages](64-bit-windows-installer-packages.md) to specify the locations of directories containing 64-bit components not shared across applications. When a user installs the dual-purpose package using the per-machine context, these components are saved in the Program Files folder. The components in these directories can be accessed by all users. When a user installs the dual-purpose package on Windows 7 or Windows Server 2008 R2 using the per-user context, these components are saved in the Programs folder of the current user (for example at %LocalAppData%\\Programs) and can be accessed only by that user. For more information about authoring a package to install an application on 64-bit operating systems, see [Windows Installer on 64-bit Operating Systems](windows-installer-on-64-bit-operating-systems.md).
-   Use the [**CommonFiles64Folder**](commonfiles64folder.md) property in the [Directory table](directory-table.md) of [64-bit Windows Installer Packages](64-bit-windows-installer-packages.md) to specify the locations of directories containing 64-bit components shared across applications. When a user installs the dual-purpose package using the per-machine context, these components are saved in the Common Files folder and can be accessed by all users. When a user installs the dual-purpose package on Windows 7 or Windows Server 2008 R2 using the per-user context, these components are saved in the Common folder of the current user (for example at %LocalAppData%\\Programs\\Common) and can be accessed only by that user.
-   Use the [**ProgramFilesFolder**](programfilesfolder.md) and [**CommonFilesFolder**](commonfilesfolder.md) properties in the [Directory table](directory-table.md) of [64-bit Windows Installer Packages](64-bit-windows-installer-packages.md) to specify the location of directories containing 32-bit components. Use different names for the 32-bit and 64-bit versions of any components provided with the same name, or alternatively, save the versions in different folders. For example, add information to the Directory table to specify the location of the directory containing the 32-bit version as \[ProgramFilesFolder\]\\*ISV Name*\\*Application Name*\\x86 and the location of the directory containing the 64-bit version as \[Program64FilesFolder\]\\*ISV Name*\\*Application Name*\\x64. A per-machine installation then saves the 32-bit version in Program Files(x86)\\*ISV Name*\\*Application Name*\\x86 and saves the 64-bit version in Program Files\\*ISV Name*\\*Application Name*\\x64. A per-user installation saves the 32-bit version in %LocalAppData%\\Programs\\*ISV Name*\\*Application Name*\\x86 and installs the 64-bit version in %LocalAppData%\\Programs\\*ISV Name*\\*Application Name*\\x64.
-   Store per-user configuration data for the application under \\Users\\*username*\\AppData.
-   Store templates and files generated by the application in subfolders under \\Users\\*username*.
-   If your application uses shell extensions, you should use the per-user-capable [shell extensibility](http://go.microsoft.com/fwlink/p/?linkid=122576) points that are available beginning in Windows 7 or Windows Server 2008 R2.
-   Do not use custom actions in your package that require elevated privileges to run. The [CustomAction](customaction-table.md) table should contain no custom actions that have been marked to run with elevated privileges. For more information about elevated custom actions, see [Custom Action Security](custom-action-security.md).
-   Do not write in any global system folders. The [Directory](directory-table.md) table should not contain a reference to any of the following system folder properties.

    <dl>

[**AdminToolsFolder**](admintoolsfolder.md)  
    [**CommonAppDataFolder**](commonappdatafolder.md)  
    [**FontsFolder**](fontsfolder.md)  
    [**System16Folder**](system16folder.md)  
    [**System64Folder**](system64folder.md)  
    [**SystemFolder**](systemfolder.md)  
    [**TempFolder**](tempfolder.md)  
    [**WindowsFolder**](windowsfolder.md)  
    [**WindowsVolume**](windowsvolume.md)  
    </dl>

-   Do not install a common language runtime assembly into the global assembly cache (GAC.) For more information about installing assemblies to the global assembly cache, see [Adding Assemblies to a Package](adding-assemblies-to-a-package.md) and [Installation of Common Language Runtime Assemblies](installation-of-common-language-runtime-assemblies.md).
-   Do not install any ODBC data sources. Do not use the [ODBCDataSource](odbcdatasource-table.md) table to install a data source.
-   Do not install any services. Do not use the [ServiceInstall](serviceinstall-table.md) table to install a service.

## Example

An example of a dual-purpose package is provided in the [Windows SDK Components for Windows Installer Developers](platform-sdk-components-for-windows-installer-developers.md) as the file PUASample1.msi. If you have the current SDK, you have access to all the tools and data necessary to reproduce the sample installation package. For more information about this example, see [Single Package Authoring Example](single-package-authoring-example.md).

 

 


