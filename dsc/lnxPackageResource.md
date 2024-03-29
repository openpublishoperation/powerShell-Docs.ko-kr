---
title: "Linux용 DSC nxPackage 리소스"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 6477ae8575c83fc24150f9502515ff5b82bc8198
ms.openlocfilehash: 31867cc7af96a3d8d527f5906d77bed5206940b4

---

# Linux용 DSC nxPackage 리소스

PowerShell DSC(필요한 상태 구성)의 **nxPackage** 리소스에서는 Linux 노드에 있는 패키지를 관리하는 메커니즘을 제공합니다.

## 구문

```
nxPackage <string> #ResourceName
{
    Name = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ PackageManager = <string> { Yum | Apt | Zypper } ]
    [ PackageGroup = <bool>]
    [ Arguments = <string> ]
    [ ReturnCode = <uint32> ]
    [ LogPath = <string> ]
    [ DependsOn = <string[]> ]
    
}
```

## 속성

|  속성 |  설명 | 
|---|---|
| 이름| 특정 상태를 확인하려는 패키지의 이름입니다.| 
| Ensure| 해당 패키지가 존재하는지를 확인할지 여부를 결정합니다. 해당 패키지가 존재하도록 하려면 이 속성을 "Present"으로 설정합니다. 해당 패키지가 존재하지 않도록 하려면 이 속성을 "Absent"으로 설정합니다. 기본값은 "Present"입니다.|  
| PackageManager| 지원되는 값은 "yum", "apt" 및 "zypper"입니다. 패키지를 설치할 때 사용할 패키지 관리자를 지정합니다. **FilePath**가 지정되면, 제공된 경로가 패키지를 설치하는 데 사용됩니다. 지정되지 않으면 패키지 관리자를 사용하여 미리 구성된 리포지토리에서 패키지를 설치하게 됩니다. **PackageManager**와 **FilePath**가 모두 제공되지 않으면, 시스템에 대한 기본 패키지 관리자가 사용됩니다.| 
| FilePath| 패키지가 있는 파일 경로| 
| PackageGroup| **$true**일 경우, **Name**은 **PackageManager**와 함께 사용할 패키지 그룹의 이름이 될 것으로 예상됩니다. **FilePath**를 제공하면 **PacakgeGroup**이 유효하지 않습니다.| 
| 인수| 제공된 그대로 패키지에 전달되는 인수 문자열입니다.| 
| ReturnCode| 예상된 반환 코드입니다. 실제 반환 코드가 여기에 제공된 예상 값과 일치하지 않는 경우 구성에서 오류를 반환하게 됩니다.| 
| DependsOn | 이 리소스를 구성하려면 먼저 다른 리소스의 구성을 실행해야 함을 나타냅니다. 예를 들어, 먼저 실행하려는 리소스 구성 스크립트 블록의 **ID**가 **ResourceName**이고 해당 형식이 **ResourceType**일 경우, 이 속성을 사용하기 위한 구문은 `DependsOn = "[ResourceType]ResourceName"`입니다.| 

## 예제

다음 예제에서는 "Yum" 패키지 관리자를 사용하여 "httpd"라는 패키지가 Linux 컴퓨터에 설치되어 있는지 확인합니다.

```
Import-DSCResource -Module nx 

Node $node {
nxPackage httpd
{
    Name = "httpd"
    Ensure = "Present"
    PackageManager = "Yum"
}
}
```




<!--HONumber=Jun16_HO4-->


