---
title: "Windows PowerShell 4.0 필요한 상태 구성 LCM(로컬 구성 관리자)"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 6477ae8575c83fc24150f9502515ff5b82bc8198
ms.openlocfilehash: 25195166f4d9dd668427d6bb5d748ef61273cdee

---

# Windows PowerShell 4.0 필요한 상태 구성 LCM(로컬 구성 관리자)

>적용 대상: Windows PowerShell 4.0, Windows PowerShell 5.0

로컬 구성 관리자는 Windows PowerShell DSC(필요한 상태 구성) 엔진으로서, 모든 대상 노드에서 실행되며 DSC 구성 스크립트에 포함된 구성 리소스를 호출하는 일을 담당합니다. 이 항목에서는 로컬 구성 관리자의 속성을 나열하고 대상 노드에서 로컬 구성 관리자 설정을 수정할 수 있는 방법을 설명합니다.

## 로컬 구성 관리자 속성
다음은 설정하거나 검색할 수 있는 로컬 구성 관리자 속성 목록입니다.
 
* **AllowModuleOverwrite**: 구성 서버에서 다운로드한 새 구성이 대상 노드에 있는 이전 구성을 덮어쓰도록 허용되는지 여부를 제어합니다. 가능한 값은 True와 False입니다.
* **CertificateID**: 구성에 액세스하기 위한 자격 증명을 보호하는 데 사용되는 인증서의 GUID입니다. 자세한 내용은 [Want to secure credentials in Windows PowerShell Desired State Configuration?(Windows PowerShell 필요한 상태 구성의 자격 증명 보호가 필요하세요?)](http://blogs.msdn.com/b/powershell/archive/2014/01/31/want-to-secure-credentials-in-windows-powershell-desired-state-configuration.aspx)을 참조하세요.
* **ConfigurationID**: "끌어오기" 서버로 설정된 서버에서 특정 구성 파일을 가져오는 데 사용되는 GUID를 나타냅니다. 이 GUID는 올바른 구성 파일에 액세스하도록 해줍니다.
* **ConfigurationMode**: 로컬 구성 관리자가 실제로 구성을 대상 노드에 적용하는 방법을 지정합니다. 이 속성에 가능한 값은 다음과 같습니다.
    - **ApplyOnly**: 이 옵션을 사용하면 새 구성을 대상 노드에 직접 전송하여("밀어넣기") 새로 만들기 구성이 감지되지 않은 경우 또는 "끌어오기" 서버를 구성했고, "끌어오기" 서버에서 확인하면 DSC에서 새 구성을 검색하는 경우, DSC가 구성을 적용하는 작업만을 수행합니다. 대상 노드의 구성이 변경되지 않는 경우 아무 작업도 수행되지 않습니다.
    - **ApplyAndMonitor**: 이 옵션(기본값)을 사용하면 대상 노드에 직접 전송했든지 아니면 "끌어오기" 서버에서 검색되었든지 관계없이 DSC에서 모든 새 구성을 적용합니다. 그 후 대상 노드의 구성이 구성 파일과 달라지면, DSC에서 로그 불일치를 보고합니다. DSC 로깅에 대한 자세한 내용은 [Using Event Logs to Diagnose Errors in Desired State Configuration(이벤트 로그를 사용하여 필요한 상태 구성에 있는 오류 진단)](http://blogs.msdn.com/b/powershell/archive/2014/01/03/using-event-logs-to-diagnose-errors-in-desired-state-configuration.aspx)을 참조하세요.
    - **ApplyAndAutoCorrect**: 이 옵션을 사용하면, 작업자가 대상 노드에 직접 전송했든지, 아니면 "끌어오기" 서버에서 검색되었든지 관계없이 DSC에서 모든 새 구성을 적용합니다. 그 후 대상 노드의 구성이 구성 파일과 달라지면, DSC에서 로그 불일치를 보고한 다음, 구성 파일에 따라 가져올 대상 노드 구성에 대한 조정을 시도합니다.
* **ConfigurationModeFrequencyMins**: DSC의 백그라운드 응용 프로그램이 대상 노드에서 현재 구성에 대한 구현을 시도하는 빈도(분 단위)를 나타냅니다. 기본값은 15입니다. 이 값은 RefreshMode와 함께 설정할 수 있습니다. RefreshMode를 PULL로 설정하면 대상 노드에서는 RefreshFrequencyMins로 설정한 간격으로 구성 서버에 연결하여 현재 구성을 다운로드합니다. RefreshMode 값에 관계없이 ConfigurationModeFrequencyMins로 설정한 간격으로, 대상 노드에 다운로드한 최신 구성을 일관성 엔진이 적용합니다. RefreshFrequencyMins는 ConfigurationModeFrequencyMins의 정수 배로 설정해야 합니다.
* **Credential**: 구성 서버 연결과 같이, 원격 리소스에 액세스하는 데 필요한 자격 증명(Get-Credential과 마찬가지로)을 나타냅니다.
* **DownloadManagerCustomData**: 다운로드 관리자에 고유한 사용자 지정 데이터를 포함하는 배열을 나타냅니다.
* **DownloadManagerName**: 구성 및 모듈 다운로드 관리자의 이름을 나타냅니다.
* **RebootNodeIfNeeded**: 대상 노드에서의 특정 구성 변경 내용은 대상 노드를 다시 시작해야 적용될 수 있습니다. 이 속성의 값이 **True**면, 구성이 완전히 적용되자마자 더 이상의 경고 없이 노드가 다시 시작됩니다. **False**(기본값)일 경우에는, 구성은 완료되지만 노드는 수동으로 다시 시작해야 변경 내용이 적용됩니다.
* **RefreshFrequencyMins**: "끌어오기" 서버를 설정하면 사용됩니다. 로컬 구성 관리자가 현재 구성을 다운로드하기 위해 "끌어오기" 서버에 연결하는 빈도(분)를 나타냅니다. 이 값은 ConfigurationModeFrequencyMins와 함께 설정할 수 있습니다. RefreshMode를 PULL로 설정하면 대상 노드에서는 RefreshFrequencyMins로 설정한 간격으로 "끌어오기" 서버에 연결하여 현재 구성을 다운로드합니다. 그러면 ConfigurationModeFrequencyMins로 설정한 간격으로, 대상 노드에 다운로드한 최신 구성을 일관성 엔진이 적용합니다. RefreshFrequencyMins가 ConfigurationModeFrequencyMins의 정수 배로 설정되어 있지 않으면 시스템에서 반올림합니다. 기본값은 30입니다.
* **RefreshMode**: 가능한 값은 **Push**(기본값)와 **Pull**입니다. "밀어넣기" 구성에서는 임의의 클라이언트 컴퓨터를 사용하여 각 대상 노드에 구성 파일을 배치해야 합니다. "끌어오기" 모드에서는 연결할 로컬 구성 관리자에 대한 "끌어오기" 서버를 설정하고 구성 파일에 액세스해야 합니다.

### 로컬 구성 관리자 설정을 업데이트하는 예제

다음 예제에서 보듯이, 구성 스크립트의 노드 블록 내에 **LocalConfigurationManager** 블록을 포함하여 대상 노드의 로컬 구성 관리자 설정을 업데이트할 수 있습니다.

```powershell
Configuration ExampleConfig
{
    Node “Server001”
    {
        LocalConfigurationManager
        {
            ConfigurationID = "646e48cb-3082-4a12-9fd9-f71b9a562d4e"
            ConfigurationModeFrequencyMins = 45
            ConfigurationMode = "ApplyAndAutocorrect"
            RefreshMode = "Pull"
            RefreshFrequencyMins = 90
            DownloadManagerName = "WebDownloadManager"
            DownloadManagerCustomData = (@{ServerUrl="https://$PullServer/psdscpullserver.svc"})
            CertificateID = "71AA68562316FE3F73536F1096B85D66289ED60E"
            Credential = $cred
            RebootNodeIfNeeded = $true
            AllowModuleOverwrite = $false
        }
# One or more resource blocks can be added here
    }
}

# The following line invokes the configuration and creates a file called Server001.meta.mof at the specified path
ExampleConfig -OutputPath "c:\users\public\dsc"  
```

이전 예제의 스크립트를 실행하면 원하는 설정을 지정하고 저장하는 MOF 파일이 생성됩니다. 설정을 적용하기 위해 다음 예제에서 보듯이 **Set-DscLocalConfigurationManager** cmdlet을 사용할 수 있습니다.

```powershell
Set-DscLocalConfigurationManager -Path "c:\users\public\dsc"
```

> **참고**: **Path** 매개 변수의 경우, 앞의 예제에서 구성을 호출할 때 **OutputPath**에 대해 지정한 것과 동일한 경로를 지정해야 합니다.

현재 로컬 구성 관리자 설정을 확인하기 위해 **Get-DscLocalConfigurationManager** cmdlet을 사용할 수 있습니다. 매개 변수 없이 이 cmdlet을 호출하는 경우, 기본적으로, 작업자가 실행하는 노드에 대한 로컬 구성 관리자 설정을 가져오게 됩니다. 다른 노드를 지정하려면 이 cmdlet에 **CimSession** 매개 변수를 사용합니다.




<!--HONumber=Jun16_HO4-->


