---
title: "개체 구조 보기(Get-Member)"
ms.date: 2016-05-11
keywords: powershell,cmdlet
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: a1819ed2-2ef3-453a-b2b0-f3589c550481
translationtype: Human Translation
ms.sourcegitcommit: 03ac4b90d299b316194f1fa932e7dbf62d4b1c8e
ms.openlocfilehash: 8cd074bc4314aeb8d50fa31d74d9a1b2d2a37469

---

# 개체 구조 보기(Get-Member)
Windows PowerShell에서 개체는 중요한 역할을 하므로 임의의 개체 유형에 사용할 여러 개의 기본 명령이 설계되어 있습니다. 이러한 명령 중 가장 중요한 명령은 **Get\-Member**입니다.

명령이 반환하는 개체를 분석하는 가장 간단한 방법은 명령의 출력을 **Get\-Member** cmdlet에 파이프하는 것입니다. **Get\-Member** cmdlet은 개체 유형의 정식 이름과 해당 멤버의 전체 목록을 보여 줍니다. 이때 매우 많은 요소가 반환될 수도 있습니다. 예를 들어 프로세스 개체에는 100개 이상의 멤버가 있습니다.

프로세스 개체의 멤버를 모두 표시하고 출력을 페이징하여 해당 내용을 모두 표시하려면 다음과 같이 입력합니다.

```
PS> Get-Process | Get-Member | Out-Host -Paging
```

이 명령을 실행하면 다음과 같은 내용이 출력됩니다.

```
TypeName: System.Diagnostics.Process

Name                           MemberType     Definition
----                           ----------     ----------
Handles                        AliasProperty  Handles = Handlecount
Name                           AliasProperty  Name = ProcessName
NPM                            AliasProperty  NPM = NonpagedSystemMemorySize
PM                             AliasProperty  PM = PagedMemorySize
VM                             AliasProperty  VM = VirtualMemorySize
WS                             AliasProperty  WS = WorkingSet
add_Disposed                   Method         System.Void add_Disposed(Event...
...
```

이 긴 정보 목록은 원하는 요소만 표시되도록 필터링하여 보다 간단하게 만들 수 있습니다. **Get\-Member** 명령을 사용하면 속성 멤버만 표시할 수 있습니다. 속성에는 여러 가지 유형이 있는데, **Get\-MemberMemberType** 매개 변수를 값 **Properties**로 설정하면 이 cmdlet이 모든 유형의 속성을 표시합니다. 결과 목록은 다음과 같이 아직도 매우 길지만 읽기가 조금 더 쉬워집니다.

```
PS> Get-Process | Get-Member -MemberType Properties

   TypeName: System.Diagnostics.Process

Name                       MemberType     Definition
----                       ----------     ----------
Handles                    AliasProperty  Handles = Handlecount
Name                       AliasProperty  Name = ProcessName
...
ExitCode                   Property       System.Int32 ExitCode {get;}
...
Handle                     Property       System.IntPtr Handle {get;}
...
CPU                        ScriptProperty System.Object CPU {get=$this.Total...
...
Path                       ScriptProperty System.Object Path {get=$this.Main...
...
```

> [!NOTE]
> MemberType에 사용할 수 있는 값으로는 AliasProperty, CodeProperty, Property, NoteProperty, ScriptProperty, Properties, PropertySet, Method, CodeMethod, ScriptMethod, Methods, ParameterizedProperty, MemberSet 및 All이 있습니다.

프로세스에는 60개 이상의 속성이 있습니다. Windows PowerShell이 종종 잘 알려진 개체의 속성을 일부만 표시하는 이유는 속성을 모두 표시하면 관리할 수 없는 정보의 양이 많아지기 때문입니다.

> [!NOTE]
> Windows PowerShell은 이름이 .format.ps1xml로 끝나는 XML 파일에 저장된 정보를 사용하여 개체의 표시 방법을 결정합니다. 예를 들어 .NET System.Diagnostics.Process 개체인 프로세스 개체의 서식 데이터는 PowerShellCore.format.ps1xml에 저장됩니다.

Windows PowerShell이 기본적으로 표시하는 속성이 아닌 다른 속성을 보려면 출력 데이터의 형식을 사용자가 직접 지정해야 합니다. 이렇게 하려면 Format cmdlet을 사용하면 됩니다.




<!--HONumber=Jun16_HO4-->


