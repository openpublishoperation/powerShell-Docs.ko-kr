---
title: "서비스 관리"
ms.date: 2016-05-11
keywords: powershell,cmdlet
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 7a410e4d-514b-4813-ba0c-0d8cef88df31
translationtype: Human Translation
ms.sourcegitcommit: 03ac4b90d299b316194f1fa932e7dbf62d4b1c8e
ms.openlocfilehash: a9d6ece1df3b66090b2abf9d85019fee4db946b5

---

# 서비스 관리
다양한 서비스 작업을 위해 설계된 8개의 핵심 Service cmdlet이 있습니다. 이 설명서에서는 실행 중인 서비스의 상태를 표시하고 변경하는 방법에 대해서만 설명하지만 **Get\-Help \&#42;\-Service**를 사용하여 Service cmdlet의 목록을 보거나 **Get\-Help New\-Service**와 같은 **Get\-Help<Cmdlet\-Name>**을 사용하여 각 Service cmdlet에 대한 정보를 찾을 수도 있습니다.

## 서비스 가져오기
**Get\-Service** cmdlet을 사용하여 로컬 또는 원격 컴퓨터에 있는 서비스를 가져올 수 있습니다. **Get\-Process**와 마찬가지로 **Get\-Service** 명령을 매개 변수 없이 사용하면 모든 서비스가 반환됩니다. 이때 다음과 같이 별표를 와일드카드로 사용하여 이름을 기준으로 필터링할 수도 있습니다.

```
PS> Get-Service -Name se*
Status   Name               DisplayName
------   ----               -----------
Running  seclogon           Secondary Logon
Running  SENS               System Event Notification
Stopped  ServiceLayer       ServiceLayer
```

서비스의 실제 이름이 항상 분명한 것은 아니기 때문에 서비스를 표시 이름으로 찾아야 할 수도 있습니다. 이렇게 하려면 다음과 같이 와일드카드를 사용하거나 표시 이름 목록을 사용하여 특정 이름으로 찾으면 됩니다.

```
PS> Get-Service -DisplayName se*
Status   Name               DisplayName
------   ----               -----------
Running  lanmanserver       Server
Running  SamSs              Security Accounts Manager
Running  seclogon           Secondary Logon
Stopped  ServiceLayer       ServiceLayer
Running  wscsvc             Security Center
PS> Get-Service -DisplayName ServiceLayer,Server
Status   Name               DisplayName
------   ----               -----------
Running  lanmanserver       Server
Stopped  ServiceLayer       ServiceLayer
```

Get\-Service cmdlet의 ComputerName 매개 변수를 사용하여 원격 컴퓨터의 서비스를 가져올 수 있습니다. ComputerName 매개 변수는 여러 개의 값과 와일드카드 문자를 받아들이기 때문에 하나의 명령으로 여러 컴퓨터의 서비스를 가져올 수 있습니다. 예를 들어 다음 명령은 Server01 원격 컴퓨터의 서비스를 가져옵니다.

```
Get-Service -ComputerName Server01
```

## 필수 및 종속 서비스 가져오기
Get\-Service cmdlet에는 서비스 관리에 매우 유용한 두 개의 매개 변수가 있습니다. DependentServices 매개 변수는 이 서비스에 종속된 서비스를 가져옵니다. RequiredServices 매개 변수는 이 서비스가 종속된 서비스를 가져옵니다.

이러한 매개 변수는 Get\-Service가 반환하는 System.ServiceProcess.ServiceController 개체의 DependentServices 및 ServicesDependedOn(별칭\=RequiredServices) 속성 값을 표시할 뿐만 아니라 명령을 단순화하고 이 정보를 보다 간단하게 가져올 수 있도록 합니다.

다음 명령은 LanmanWorkstation 서비스에 필요한 서비스를 가져옵니다.

```
PS> Get-Service -Name LanmanWorkstation -RequiredServices
Status   Name               DisplayName
------   ----               -----------
Running  MRxSmb20           SMB 2.0 MiniRedirector
Running  bowser             Bowser
Running  MRxSmb10           SMB 1.x MiniRedirector
Running  NSI                Network Store Interface Service
```

다음 명령은 LanmanWorkstation 서비스를 필요로 하는 서비스를 가져옵니다.

```
PS> Get-Service -Name LanmanWorkstation -DependentServices
Status   Name               DisplayName
------   ----               -----------
Running  SessionEnv         Terminal Services Configuration
Running  Netlogon           Netlogon
Stopped  Browser            Computer Browser
Running  BITS               Background Intelligent Transfer Ser...
```

종속성을 가진 모든 서비스를 가져올 수도 있습니다. 다음 명령은 종속성을 가진 모든 서비스를 가져온 다음 Format\-Table cmdlet을 사용하여 컴퓨터에 있는 서비스의 Status, Name, RequiredServices 및 DependentServices 속성을 표시합니다.

```
Get-Service -Name * | where {$_.RequiredServices -or $_.DependentServices} | Format-Table -Property Status, Name, RequiredServices, DependentServices -auto
```

## 서비스 중지, 시작, 일시 중단 및 다시 시작
Service cmdlet은 모두 동일한 일반 형식으로 되어 있기 때문에 서비스를 일반 이름이나 표시 이름으로 지정할 수 있으며 목록과 와일드카드를 값으로 사용할 수 있습니다. 인쇄 스풀러를 중지하려면 다음 명령을 사용합니다.

```
Stop-Service -Name spooler
```

인쇄 스풀러를 중지한 후 다시 시작하려면 다음 명령을 사용합니다.

```
Start-Service -Name spooler
```

인쇄 스풀러를 일시 중단하려면 다음 명령을 사용합니다.

```
Suspend-Service -Name spooler
```

**Restart\-Service** cmdlet은 다른 Service cmdlet과 동일한 방식으로 작동하지만 이 설명서에서는 이 cmdlet에 대해 약간 더 복잡한 예제를 보여 줍니다. 가장 간단한 사용 시나리오에서는 다음과 같이 서비스 이름을 지정합니다.

```
PS> Restart-Service -Name spooler
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
PS>
```

그러면 인쇄 스풀러 시작에 대한 경고 메시지가 반복해서 나타나는데, 다소 시간이 걸리는 서비스 작업을 수행하면 Windows PowerShell이 계속 작업을 시도하고 있다는 내용의 메시지를 표시하기 때문입니다.

여러 서비스를 다시 시작하려면 다음과 같이 서비스 목록을 보고, 필터링한 다음 원하는 서비스를 다시 시작하면 됩니다.

```
PS> Get-Service | Where-Object -FilterScript {$_.CanStop} | Restart-Service
WARNING: Waiting for service 'Computer Browser (Browser)' to finish stopping...
WARNING: Waiting for service 'Computer Browser (Browser)' to finish stopping...
Restart-Service : Cannot stop service 'Logical Disk Manager (dmserver)' because
 it has dependent services. It can only be stopped if the Force flag is set.
At line:1 char:57
+ Get-Service | Where-Object -FilterScript {$_.CanStop} | Restart-Service <<<<
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
```

이러한 Service cmdlet에는 ComputerName 매개 변수가 없지만 Invoke\-Command cmdlet을 사용하여 원격 컴퓨터에서 실행할 수는 있습니다. 예를 들어 다음 명령은 Server01 원격 컴퓨터에서 스풀러 서비스를 다시 시작합니다.

```
Invoke-Command -ComputerName Server01 {Restart-Service Spooler}
```

## 서비스 속성 설정
Set\-Service cmdlet은 로컬 또는 원격 컴퓨터에서 서비스의 속성을 변경합니다. 서비스 상태는 속성이기 때문에 이 cmdlet을 사용하여 서비스를 시작, 중지 및 일시 중단할 수 있습니다. Set\-Service cmdlet에는 서비스 시작 유형을 변경할 수 있도록 해주는 StartupType 매개 변수도 있습니다.

Windows Vista 이상에서 Set\-Service를 사용하려면 "관리자 권한으로 실행" 옵션을 사용하여 Windows PowerShell을 엽니다.

자세한 내용은 [Set-Service [m2]](https://technet.microsoft.com/en-us/library/b71e29ed-372b-4e32-a4b7-5eb6216e56c3)를 참조하세요.

## 참고 항목
[Get-Service [m2]](https://technet.microsoft.com/en-us/library/0a09cb22-0a1c-4a79-9851-4e53075f9cf6)
[Set-Service [m2]](https://technet.microsoft.com/en-us/library/b71e29ed-372b-4e32-a4b7-5eb6216e56c3)
[Restart-Service [m2]](https://technet.microsoft.com/en-us/library/45acf50d-2277-4523-baf7-ce7ced977d0f)
[Suspend-Service [m2]](https://technet.microsoft.com/en-us/library/c8492b87-0e21-4faf-8054-3c83c2ec2826)




<!--HONumber=Jun16_HO4-->


