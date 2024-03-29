---
title: "DSC 리소스 디버그"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 83ca45d507e39b77751ac7feb6a7b65ae2834280
ms.openlocfilehash: e1922008a92f00c9ddab28598735839c25219d24

---

# DSC 리소스 디버그

> 적용 대상: Windows PowerShell 5.0

PowerShell 5.0에서는 구성이 적용됨에 따라 DSC 리소스를 디버그할 수 있도록 해주는 새로운 기능이 DSC(필요한 상태 구성)에 도입되었습니다.

## DSC 디버그 사용
리소스를 디버그할 수 있으려면 먼저, [Enable-DscDebug](https://technet.microsoft.com/en-us/library/mt517870.aspx) cmdlet을 호출하여 디버그할 수 있도록 해야 합니다. 이 cmdlet은 필수 매개 변수인 **BreakAll**을 사용합니다. 

[Get-DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn407378.aspx)를 호출한 결과를 보면 디버그할 수 있게 되어 있는지 확인할 수 있습니다. 다음의 PowerShell 출력은 디버그 사용의 결과를 보여 줍니다.


```powershell
PS C:\DebugTest> $LCM = Get-DscLocalConfigurationManager

PS C:\DebugTest> $LCM.DebugMode
NONE

PS C:\DebugTest> Enable-DscDebug -BreakAll

PS C:\DebugTest> $LCM = Get-DscLocalConfigurationManager

PS C:\DebugTest> $LCM.DebugMode
ForceModuleImport
ResourceScriptBreakAll

PS C:\DebugTest>
```


## 디버그를 사용하도록 설정된 구성 시작
DSC 리소스를 디버그하려면 해당 리소스를 호출하는 구성을 시작합니다. 예를 들어 [WindowsFeature](windowsfeatureResource.md) 리소스를 호출하여 "WindowsPowerShellWebAccess" 기능이 설치되어 있는지 확인하는 간단한 구성을 살펴보겠습니다.

```powershell
Configuration PSWebAccess
    {
    Import-DscResource -ModuleName 'PsDesiredStateConfiguration'
    Node localhost
        {
        WindowsFeature PSWA
            {
            Name = 'WindowsPowerShellWebAccess'
            Ensure = 'Present'
            }
        }
    }
PSWebAccess
```
구성을 컴파일한 후에는 [Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx)을 호출하여 구성을 시작합니다. 이 구성은 LCM(로컬 구성 관리자)이 구성의 첫 번째 리소스를 호출하면 중지됩니다. `-Verbose` 및 `-Wait` 매개 변수를 사용하는 경우, 출력에 디버그를 시작하려면 입력해야 하는 줄이 표시됩니다.

```powershell
PS C:\DebugTest> Start-DscConfiguration .\PSWebAccess -Wait -Verbose
VERBOSE: Perform operation 'Invoke CimMethod' with following parameters, ''methodName' = SendConfigurationApply,'className' = MSFT_DSCLocalConfiguration
Manager,'namespaceName' = root/Microsoft/Windows/DesiredStateConfiguration'.
VERBOSE: An LCM method call arrived from computer TEST-SRV with user sid S-1-5-21-2127521184-1604012920-1887927527-108583.
VERBOSE: An LCM method call arrived from computer TEST-SRV with user sid S-1-5-21-2127521184-1604012920-1887927527-108583.
VERBOSE: [TEST-SRV]: LCM:  [ Start  Set      ]
WARNING: [TEST-SRV]:                            [DSCEngine] Warning LCM is in Debug 'ResourceScriptBreakAll' mode.  Resource script processing will 
be stopped to wait for PowerShell script debugger to attach.
VERBOSE: [TEST-SRV]:                            [DSCEngine] Importing the module C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\PSDesiredStateCo
nfiguration\DscResources\MSFT_RoleResource\MSFT_RoleResource.psm1 in force mode.
VERBOSE: [TEST-SRV]: LCM:  [ Start  Resource ]  [[WindowsFeature]PSWA]
VERBOSE: [TEST-SRV]: LCM:  [ Start  Test     ]  [[WindowsFeature]PSWA]
VERBOSE: [TEST-SRV]:                            [[WindowsFeature]PSWA] Importing the module MSFT_RoleResource in force mode.
WARNING: [TEST-SRV]:                            [[WindowsFeature]PSWA] Resource is waiting for PowerShell script debugger to attach.  Use the follow
ing commands to begin debugging this resource script:
Enter-PSSession -ComputerName TEST-SRV -Credential <credentials>
Enter-PSHostProcess -Id 9000 -AppDomainName DscPsPluginWkr_AppDomain
Debug-Runspace -Id 9
```
이 시점에서 LCM은 리소스를 호출하고 첫 번째 중단점으로 이동했습니다. 출력의 마지막 세 줄에는 프로세스에 연결하여 리소스 스크립트의 디버그를 시작하는 방법이 나옵니다.

## 리소스 스크립트 디버그

PowerShell ISE의 새 인스턴스를 시작합니다. 콘솔 창에서 `Start-DscConfiguration` 출력으로부터 나온 결과의 마지막 세 줄을 명령으로 입력하여 `<credentials>`를 올바른 사용자 자격 증명으로 바꿉니다. 이제 다음과 유사한 프롬프트가 표시됩니다.

```powershell
[TEST-SRV]: [DBG]: [Process:9000]: [RemoteHost]: PS C:\DebugTest>>
```

리소스 스크립트가 스크립트 창에 열리고 디버거가 **Test-TargetResource** 함수(클래스 기반 리소스의 **Test()** 메서드)의 첫째 줄에서 중지됩니다.
이제 ISE에서 디버그 명령을 사용하여 리소스 스크립트 단계별 실행, 변수 값 확인, 호출 스택 보기 등을 할 수 있습니다. PowerShell ISE에서 디버그하는 방법에 대한 자세한 내용은 [How to Debug Scripts in Windows PowerShell ISE(Windows PowerShell ISE에서 스크립트를 디버그하는 방법)](https://technet.microsoft.com/en-us/library/dd819480.aspx)를 참조하세요. 리소스 스크립트 (또는 클래스)의 모든 줄은 중단점으로 설정된다는 점을 기억하세요.

## DSC 디버그 사용 안 함

[Enable-DscDebug](https://technet.microsoft.com/en-us/library/mt517870.aspx)를 호출한 후 [Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx)을 호출하면 항상 구성으로 인해 디버거가 중단됩니다. 구성이 정상적으로 실행되도록 하려면 [Disable-DscDebug](https://technet.microsoft.com/en-us/library/mt517872.aspx) cmdlet을 호출하여 디버깅을 사용하지 않도록 설정해야 합니다.

>**참고:** 재부팅해도 LCM의 디버그 상태가 변경되지 않습니다. 디버깅을 사용하도록 설정하는 경우 재부팅 후 구성을 시작하면 여전히 디버거가 중단됩니다.


## 참고 항목
- [MOF를 사용하여 사용자 지정 DSC 리소스 작성](authoringResourceMOF.md) 
- [PowerShell 클래스를 사용하여 사용자 지정 DSC 리소스 작성](authoringResourceClass.md)




<!--HONumber=Jul16_HO5-->


