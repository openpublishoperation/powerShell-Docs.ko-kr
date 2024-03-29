---
title: "Linux nxArchive 리소스용 DSC"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 6477ae8575c83fc24150f9502515ff5b82bc8198
ms.openlocfilehash: 2edbc1d11dfc7c84369430688a8b0d773277e864

---

# Linux nxArchive 리소스용 DSC

PowerShell DSC(필요한 상태 구성)의 **nxArchive** 리소스는 Linux 노드 상의 특정 경로에서 보관(.tar, .zip) 파일의 압축을 푸는 메커니즘을 제공합니다.

## 구문

```
nxArchive <string> #ResourceName
{
    SourcePath = <string>
    DestinationPath = <string>
    [ Checksum = <string> { ctime | mtime | md5 }  ]
    [ Force = <bool> ]
    [ DependsOn = <string[]> ]
    [ Ensure = <string> { Absent | Present }  ]
}
```

## 속성

|  속성 |  설명 | 
|---|---|
| SourcePath| 보관 파일의 원본 경로를 지정합니다. .tar, .zip 또는 .tar.gz 파일이어야 합니다. | 
| DestinationPath| 보관 파일 내용을 추출해 놓을 위치를 지정합니다.| 
| 체크섬| 소스 보관 파일이 업데이트되었는지 결정할 때 사용할 형식을 정의합니다. 값은 "ctime", "mtime" 또는 "md5"입니다. 기본값은 "md5"입니다.| 
| Force| 특정 파일 작업(예: 파일 덮어쓰기나 비어 있지 않은 디렉터리 삭제)을 수행하면 오류가 발생합니다. **Force** 속성을 사용하면 이러한 오류가 무시됩니다. 기본값은 **$false**입니다.| 
| DependsOn | 이 리소스를 구성하려면 먼저 다른 리소스의 구성을 실행해야 함을 나타냅니다. 예를 들어, 먼저 실행하려는 리소스 구성 스크립트 블록의 **ID**가 **ResourceName**이고 해당 형식이 **ResourceType**일 경우, 이 속성을 사용하기 위한 구문은 `DependsOn = "[ResourceType]ResourceName"`입니다.| 
| Ensure| 보관 파일의 내용이 **Destination**에 있는지 확인할지 여부를 결정합니다. 내용이 있도록 하려면 이 속성을 "Present"으로 설정합니다. 내용이 없도록 하려면 이 속성을 "Absent"으로 설정합니다. 기본값은 "Present"입니다.| 

## 예제

다음 예제에서는 **nxArchive** 리소스를 사용하여 `website.tar`이라는 보관 파일의 내용이 존재하고 지정된 대상에 압축이 풀리도록 하는 방법을 보여줍니다.

```
Import-DSCResource -Module nx 

nxFile SyncArchiveFromWeb
{
   Ensure = "Present"
   SourcePath = “http://release.contoso.com/releases/website.tar”
   DestinationPath = "/usr/release/staging/website.tar"
   Type = "File"
   Checksum = “mtime”
}

nxArchive SyncWebDir
{
   SourcePath = “/usr/release/staging/website.tar”
   DestinationPath = “/usr/local/apache2/htdocs/”
   Force = $false
   DependsOn = "[nxFile]SyncArchiveFromWeb"
} 
```




<!--HONumber=Jun16_HO4-->


