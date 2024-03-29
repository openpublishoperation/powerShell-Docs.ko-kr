---
title: "Linux용 DSC nxUser 리소스"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 6477ae8575c83fc24150f9502515ff5b82bc8198
ms.openlocfilehash: 7813185313845b74e2a37dfa4ec6bb109f32f0eb

---

# Linux용 DSC nxUser 리소스

PowerShell DSC(필요한 상태 구성)의 **nxUser** 리소스에서는 Linux 노드에 있는 로컬 사용자를 관리하는 메커니즘을 제공합니다.

## 구문

```
nxUser <string> #ResourceName
{
    UserName = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ FullName = <string> ]
    [ Description = <string> ]
    [ Password = <string> ]
    [ Disabled = <bool> ]
    [ PasswordChangeRequired = <bool> ]
    [ HomeDirectory = <string> ]
    [ Mode = <string> ]
    [ GroupID = <string> ]
    [ DependsOn = <string[]> ]

}
```

## 속성

|  속성 |  특정 상태를 확인하려는 계정 이름을 나타냅니다. | 
|---|---|
| UserName| 파일 또는 디렉터리에 대한 상태를 확인하려는 위치를 지정합니다.| 
| Ensure| 계정이 존재하는지 여부를 지정합니다. 계정이 존재하도록 하려면 이 속성을 "Present"으로 설정하고, 계정이 존재하지 않도록 하려면 "Absent"으로 설정합니다.| 
| FullName| 사용자 계정에 사용할 전체 이름을 포함하는 문자열입니다.| 
| 설명| 사용자 계정에 대한 설명입니다.| 
| 암호| Linux 컴퓨터용으로 적절한 형태의 사용자 암호 해시입니다. 일반적으로 솔트된 SHA-256 또는 SHA-512 해시입니다. Debian 및 Ubuntu Linux에서 이 값은 mkpasswd 명령을 사용하여 생성할 수 있습니다. 다른 Linux 배포판의 경우 Python의 암호화 라이브러리의 암호화 방법을 사용하여 해시를 생성할 수 있습니다.| 
| 사용 안 함| 계정이 활성화되어 있는지 여부를 나타냅니다. 이 계정을 사용하지 않도록 하려면 이 속성을 **$true**로 설정하고, 사용하도록 하려면, **$false**로 설정합니다.| 
| PasswordChangeRequired| 사용자가 암호를 변경할 수 있는지 여부를 나타냅니다. 사용자가 암호를 변경할 수 없도록 하려면 이 속성을 **$true**로 설정하고, 사용자가 암호를 변경할 수 있도록 하려면 **$false**로 설정합니다. 기본값은 **$false**입니다. 이 속성은 사용자 계정이 이전에 존재하지 않아서 만들어지는 경우에만 평가됩니다.| 
| HomeDirectory| 사용자에 대한 홈 디렉터리입니다.| 
| GroupID| 사용자에 대한 주 그룹 ID입니다.| 
| DependsOn | 이 리소스를 구성하려면 먼저 다른 리소스의 구성을 실행해야 함을 나타냅니다. 예를 들어, 먼저 실행하려는 리소스 구성 스크립트 블록의 ID가 "ResourceName"이고 해당 형식이 "ResourceType"일 경우, 이 속성을 사용하기 위한 구문은 `DependsOn = "[ResourceType]ResourceName"`입니다.| 

## 예제

다음 예제에서는 사용자 "monuser"가 존재하고 "DBusers" 그룹의 구성원임을 확인합니다.

```
Import-DSCResource -Module nx 

Node $node {
nxUser UserExample{
   UserName = "monuser"
   Description = "Monitoring user"
   Password  =    '$6$fZAne/Qc$MZejMrOxDK0ogv9SLiBP5J5qZFBvXLnDu8HY1Oy7ycX.Y3C7mGPUfeQy3A82ev3zIabhDQnj2ayeuGn02CqE/0'
   Ensure = "Present"
   HomeDirectory = "/home/monuser"
}
 
nxGroup GroupExample{
   GroupName = "DBusers"
   Ensure = "Present"
   MembersToInclude = "monuser"
   DependsOn = "[nxUser]UserExample"            
}
}
```




<!--HONumber=Jun16_HO4-->


