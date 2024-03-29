---
title: "DSC 사용자 리소스"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 6477ae8575c83fc24150f9502515ff5b82bc8198
ms.openlocfilehash: 5c7878bdfc8a3f118b569a9e43be6c7e4333ad2c

---

#DSC 사용자 리소스#

 
>적용 대상: Windows PowerShell 4.0, Windows PowerShell 5.0


Windows PowerShell DSC(필요한 상태 구성)의 __사용자__ 리소스에서는 대상 노드에 있는 로컬 사용자 계정을 관리하는 메커니즘을 제공합니다.


##구문##

```
User [string] #ResourceName
{
    UserName = [string]
    [ Description = [string] ]
    [ Disabled = [bool] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ FullName = [string] ]
    [ Password = [PSCredential] ]
    [ PasswordChangeNotAllowed = [bool] ]
    [ PasswordChangeRequired = [bool] ]
    [ PasswordNeverExpires = [bool] ]
    [ DependsOn = [string[]] ]
}
```

## 속성
|  속성  |  설명   | 
|---|---| 
| UserName| 특정 상태를 확인하려는 계정 이름을 나타냅니다.| 
| 설명| 사용자 계정에 대해 사용할 설명을 나타냅니다.| 
| 사용 안 함| 계정이 사용되는지 여부를 나타냅니다. 이 계정을 사용하지 않도록 하려면 이 속성을 __$true__로 설정하고, 사용하도록 하려면, __$false__로 설정합니다.| 
| Ensure| 계정이 있는지 여부를 나타냅니다. 계정이 존재하도록 하려면 이 속성을 "Present"으로 설정하고, 계정이 존재하지 않도록 하려면 "Absent"으로 설정합니다.| 
| FullName| 사용자 계정에 대해 사용할 전체 이름으로 문자열을 나타냅니다.| 
| 암호| 이 계정에 사용할 암호를 나타냅니다. | 
| PasswordChangeNotAllowed| 사용자가 암호를 변경할 수 있는지 여부를 나타냅니다. 사용자가 암호를 변경할 수 없도록 하려면 이 속성을 __$true__로 설정하고, 사용자가 암호를 변경할 수 있도록 하려면 __$false__로 설정합니다. 기본값은 __$false__입니다.| 
| PasswordChangeRequired| 사용자가 다음 로그인 시 암호를 변경해야 하는지 여부를 나타냅니다. 사용자가 암호를 변경해야 하는 경우 이 속성을 __$true__로 설정합니다. 기본값은 __$true__입니다.| 
| PasswordNeverExpires| 암호가 만료될지 여부를 나타냅니다. 이 계정에 대한 암호가 절대로 만료되지 않도록 하려면, 이 속성을 __$true__로 설정하고, 암호가 만료될 것이라면 __$false__로 설정합니다. 기본값은 __$false__입니다.| 
| DependsOn | 이 리소스를 구성하려면 먼저 다른 리소스의 구성을 실행해야 함을 나타냅니다. 예를 들어, 먼저 실행하려는 리소스 구성 스크립트 블록의 ID가 __ResourceName__이고 해당 형식이 __ResourceType__일 경우, 이 속성을 사용하기 위한 구문은 `DependsOn = "[ResourceType]ResourceName"`입니다.| 

## 예제

```powershell
User UserExample
{
    Ensure = "Present"  # To ensure the user account does not exist, set Ensure to "Absent"
    UserName = "SomeName"
    Password = $passwordCred # This needs to be a credential object
    DependsOn = “[Group]GroupExample" # Configures GroupExample first
}
```




<!--HONumber=Jun16_HO4-->


