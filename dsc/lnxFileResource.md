---
title: "Linux용 DSC nxFile 리소스"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 6477ae8575c83fc24150f9502515ff5b82bc8198
ms.openlocfilehash: 2ba44df5dd6c91371cbbfe95d48184a4ff4a7738

---

# Linux용 DSC nxFile 리소스

PowerShell DSC(필요한 상태 구성)의 **nxFile** 리소스에서는 Linux 노드 상의 파일 및 디렉터리를 관리하는 메커니즘을 제공합니다.

## 구문

```
nxFile <string> #ResourceName
{
    DestinationPath = <string>
    [ SourcePath = <string> ]
    [ Ensure = <string> { Absent | Present }  ]
    [ Type = <string> { directory | file | link } ]
    [ Contents = <string> ]
    [ Checksum = <string> { ctime | mtime | md5 }  ]
    [ Recurse = <bool> ]
    [ Force = <bool> ]
    [ Links = <string> { follow | manage } ]
    [ Group = <string> ]
    [ Mode = <string> ]
    [ Owner = <string> ]
    [ DependsOn = <string[]> ]

}
```

## 속성

|  속성 |  설명 | 
|---|---|
| DestinationPath| 파일 또는 디렉터리에 대한 상태를 확인하려는 위치를 지정합니다.| 
| SourcePath| 파일 또는 폴더 리소스를 복사해 올 소스 경로를 지정합니다. 이 경로는 로컬 경로이거나 `http/https/ftp` URL일 수 있습니다. 원격 `http/https/ftp` URL은 **Type** 속성의 값이 file일 때에만 지원됩니다.| 
| Ensure| 해당 파일이 존재하는지를 확인할지 여부를 결정합니다. 해당 파일이 존재하도록 하려면 이 속성을 "Present"으로 설정합니다. 해당 파일이 존재하지 않도록 하려면 이 속성을 "Absent"으로 설정합니다. 기본값은 "Present"입니다.| 
| 유형| 구성되는 리소스가 디렉터리인지 아니면 파일인지를 지정합니다. 리소스가 디렉터리임을 나타내려면 이 속성을 "directory"로 설정합니다. 리소스가 파일을 나타내려면 이 속성을 "file"로 설정합니다. 기본값은 "file"입니다.| 
| 콘텐츠| 특정 문자열과 같이 파일의 내용을 지정합니다.| 
| 체크섬| 두 파일이 동일한 것인지 결정할 때 사용할 형식을 정의합니다. **Checksum**을 지정하지 않은 경우 비교에 파일 또는 디렉터리 이름만 사용됩니다. 값은 "ctime", "mtime" 또는 "md5"입니다.| 
| Recurse| 하위 디렉터리가 포함되어 있는지를 나타냅니다. 하위 디렉터리가 포함되도록 하려면 이 속성을 **$true**로 설정합니다. 기본값은 **$false**입니다. **참고**: **Type** 속성이 directory로 설정되어 있을 때에만 이 속성이 유효합니다.| 
| Force| 특정 파일 작업(예: 파일 덮어쓰기나 비어 있지 않은 디렉터리 삭제)을 수행하면 오류가 발생합니다. **Force** 속성을 사용하면 이러한 오류가 무시됩니다. 기본값은 **$false**입니다.| 
| 링크| 바로 가기 링크에 대해 원하는 동작을 지정합니다. 바로 가기 링크를 따르고 링크 대상에 대해 작업을 수행(예: 링크대신 파일 복사)하도록 하려면 이 속성을 "follow"로 설정합니다. 링크에 대해 작업을 수행(예: 링크 자체를 복사)하도록 하려면 이 속성을 "manage"로 설정합니다. 바로 가기 링크를 무시하려면 이 속성을 "ignore"로 설정합니다.| 
| 그룹| 파일 또는 디렉터리를 소유하는 **Group**의 이름입니다.| 
| 모드| 리소스에 대해 원하는 사용 권한을 8진수 또는 기호 표기법으로 지정합니다. (예: 777 또는 rwxrwxrwx). 기호 표기법을 사용하는 경우, 디렉터리나 파일을 나타내는 첫 번째 문자를 제공하지 마세요.| 
| DependsOn | 이 리소스를 구성하려면 먼저 다른 리소스의 구성을 실행해야 함을 나타냅니다. 예를 들어, 먼저 실행하려는 리소스 구성 스크립트 블록의 **ID**가 **ResourceName**이고 해당 형식이 **ResourceType**일 경우, 이 속성을 사용하기 위한 구문은 `DependsOn = "[ResourceType]ResourceName"`입니다.| 

## 추가 정보


Linux 및 Windows에서는 텍스트 파일에서 기본적으로 서로 다른 줄 바꿈 문자를 사용하며, 이로 인해 Linux 컴퓨터에서 __nxFile__을 사용하여 일부 파일을 구성할 때 예기치 않은 결과가 발생할 수 있습니다. 예기치 않은 줄 바꿈 문자로 인한 문제를 방지하면서 Linux 파일의 내용을 관리하는 방법에는 여러 가지가 있습니다.

1 단계: 원격 소스(예: http, https 또는 ftp)에서 파일 복사: 원하는 내용으로 Linux에서 파일을 만들고 구성할 노드에 액세스할 수 있는 웹 서버나 ftp 서버에 이 파일을 올립니다. __nxFile__ 리소스에 있는 __SourcePath__ 속성을 파일의 웹 또는 ftp URL로 정의합니다.

```
Import-DSCResource -Module nx
Node $Node
{
nxFile resolvConf
{
    SourcePath = "http://10.185.85.11/conf/resolv.conf"
    DestinationPath = "/etc/resolv.conf"
    Mode = "644"        
    Type = "file"
    
}
        
}
```


2 단계: Linux 줄 바꿈 문자를 사용하도록 __$OFS__ 속성을 설정한 후 [Get-Content](https://technet.microsoft.com/en-us/library/hh849787.aspx)로 PowerShell 스크립트에 있는 파일 내용을 읽습니다.


```
Import-DSCResource -Module nx
Node $Node
{
$OFS = "`n"
$Contents = Get-Content C:\temp\resolv.conf

nxFile resolvConf
{
    DestinationPath = "/etc/resolv.conf"
    Mode = "644"        
    Type = "file"
    Contents = "$Contents"
}

}
```


3 단계: PowerShell 함수를 사용하여 Windows 줄 바꿈을 Linux 줄 바꿈 문자로 바꿉니다.

```
Function LinuxString($inputStr){
    $outputStr = $inputStr.Replace("`r`n","`n")
    $ouputStr += "`n"
    Return $outputStr
}

Import-DSCResource -Module nx
Node $Node
{

$Contents = @'
search contoso.com
domain contoso.com
nameserver 10.185.85.11
'@

$Contents = LinuxString $Contents

nxFile resolvConf
{
    DestinationPath = "/etc/resolv.conf"
    Mode = "644"        
    Type = "file"
    Contents = $Contents
    
}
}
```

## 예제

다음 예제에서는 디렉터리 `/opt/mydir`이 존재하고, 지정된 내용의 파일이 이 디렉터리에 존재하도록 합니다.

```
Import-DSCResource -Module nx 

Node $node {
nxFile DirectoryExample
{
   Ensure = "Present"
   DestinationPath = "/opt/mydir"
   Type = "Directory"
}

nxFile FileExample
{
    Ensure = "Present"
    Destinationpath = "/opt/mydir/myfile"
    Contents=@"
#!/bin/bash`necho "hello world"`n
"@ 
    Mode = “755”
    DependsOn = "[nxFile]DirectoryExample"
} 
}
```




<!--HONumber=Jun16_HO4-->


