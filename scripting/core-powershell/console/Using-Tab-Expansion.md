---
title: "탭 확장 사용"
ms.date: 2016-05-11
keywords: powershell,cmdlet
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: c8730471-bf6a-43b8-ab1d-f9ef5a74f04e
translationtype: Human Translation
ms.sourcegitcommit: 03ac4b90d299b316194f1fa932e7dbf62d4b1c8e
ms.openlocfilehash: 644dbbb51e98efda9735f0ff23489e936f6b28a2

---

# 탭 확장 사용
명령줄 셸은 종종 긴 파일 또는 명령의 이름을 자동으로 완성하는 기능을 제공하여 명령 입력 시간을 단축하고 힌트를 제공합니다. Windows PowerShell에서는 **Tab** 키를 눌러 파일 이름과 cmdlet 이름을 채울 수 있습니다.

> [!NOTE]
> 탭 확장은 내부 함수인 TabExpansion에 의해 제어됩니다. 이 함수는 수정 또는 재정의가 가능하므로 이 설명서에서는 기본 Windows PowerShell 구성의 동작에 대해 설명합니다.

사용 가능한 선택 항목으로 파일 이름이나 경로를 채우려면 이름의 일부를 입력한 다음 **Tab** 키를 누릅니다. 그러면 이름이 자동으로 일치하는 첫 번째 항목으로 확장됩니다. **Tab** 키를 반복해서 누르면 사용 가능한 모든 선택 항목이 번갈아 표시됩니다.

cmdlet 이름의 탭 확장은 약간 다릅니다. cmdlet 이름에 대해 탭 확장을 사용하려면 이름의 첫 번째 부분(즉, 동사) 전체와 하이픈을 차례로 입력합니다. 그러면 부분 일치를 위해 더 많은 이름을 채울 수 있습니다. 예를 들어 **get\-co**를 입력한 다음 **Tab** 키를 누르면 자동으로 **Get\-Command** cmdlet으로 확장되고(이때 문자의 대/소문자도 표준 형식으로 변경됨), **Tab** 키를 다시 누르면 일치하는 유일한 다른 cmdlet 이름인 **Get\-Content**로 이 cmdlet이 대체됩니다.

탭 확장은 동일한 줄에서 반복해서 사용할 수 있습니다. 예를 들어 다음을 입력하여 **Get\-Content** cmdlet의 이름에 대해 탭 확장을 사용할 수 있습니다.

```
PS> Get-Con<Tab>
```

**Tab** 키를 누르면 이 명령이 다음과 같이 확장됩니다.

```
PS> Get-Content
```

그러면 다음과 같이 Active Setup 로그 파일의 경로를 일부만 지정하고 다시 탭 확장을 사용할 수 있습니다.

```
PS> Get-Content c:\windows\acts<Tab>
```

**Tab** 키를 누르면 이 명령이 다음과 같이 확장됩니다.

```
PS> Get-Content C:\windows\actsetup.log
```

> [!NOTE]
> 탭 확장 프로세스의 유일한 제한 사항은 탭이 항상 단어를 완성하려는 시도로 해석된다는 것입니다. 따라서 명령 예제를 복사하여 Windows PowerShell 콘솔에 붙여 넣을 경우 명령 예제에 탭이 포함되지 않도록 해야 합니다. 명령 예제에 탭이 포함되어 있으면 예측할 수 없는 결과가 발생하고 대부분 의도한 대로 되지 않습니다.




<!--HONumber=Jun16_HO4-->


