---
title: "DSC 보고서 서버 사용"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 59793e1701740dc783439cf1408c6efabd53cbcf
ms.openlocfilehash: d541f37723d77cf0b52012bae143dc7d64efd65d

---

# DSC 보고서 서버 사용

> 적용 대상: Windows PowerShell 5.0

>**참고:** 이 항목에 설명된 보고서 서버는 PowerShell 4.0에서 사용할 수 없습니다.

노드의 LCM(로컬 구성 관리자)은 해당 구성 상태에 대한 보고서를 끌어오기 서버에 보내도록 구성할 수 있습니다. 이렇게 하면 해당 데이터를 검색하도록 쿼리할 수 있습니다. 노드는 구성을 확인하고 적용할 때마다 보고서를 보고서 서버에 보냅니다. 이러한 보고서는 서버의 데이터베이스에 저장되며, 보고 웹 서비스를 호출하여 검색할 수 있습니다. 각 보고서에는 적용된 구성, 성공 여부, 사용된 리소스, 발생한 모든 오류, 시작 및 완료 시간 등의 정보가 포함됩니다.

## 보고서를 보내도록 노드 구성

노드의 LCM 구성에서 **ReportServerWeb** 블록을 사용하여 서버에 보고서를 보내도록 노드에게 지시하세요(LCM 구성에 대해서는 [로컬 구성 관리자 구성](metaConfig.md) 참조). 노드가 보내는 보고서를 받는 서버는 웹 끌어오기 서버로 설정되어 있어야 합니다(SMB 공유에는 보고서를 보낼 수 없음). 끌어오기 서버 설정에 대한 내용은 [DSC 웹 끌어오기 서버 설정](pullServer.md)을 참조합니다. 보고서 서버는 노드가 구성을 끌어오고 리소스를 가져오는 서비스와 동일한 서비스일 수도 있고 다른 서비스일 수도 있습니다.
 
**ReportServerWeb** 블록에서는 끌어오기 서비스의 URL과 서버에 알려진 등록 키를 지정합니다.
 
다음 구성은 한 서비스에서 구성을 끌어오고 다른 서버의 서비스에 보고서를 보내도록 노드를 구성합니다. 
 
```powershell
[DSCLocalConfigurationManager()]
configuration ReportClientConfig
{
    Node localhost
    {
        Settings
        {
            RefreshMode          = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded   = $true
        }

        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL          = 'https://CONTOSO-PULL:8080/PSDSCPullServer.svc'
            RegistrationKey    = 'bbb9778f-43f2-47de-b61e-a0daff474c6d'
            ConfigurationNames = @('ClientConfig')
        }

        ReportServerWeb CONTOSO-ReportSrv
        {
            ServerURL               = 'http://CONTOSO-REPORT:8080/PSDSCReportServer.svc'
            RegistrationKey         = 'ba39daaa-96c5-4f2f-9149-f95c46460faa'
            AllowUnsecureConnection = $true
        }
    }
}
ReportClientConfig
```

다음 구성에서는 구성, 리소스 및 보고에 단일 서버를 사용하도록 노드를 구성합니다.

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfig
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }

        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = 'fbc6ef09-ad98-4aad-a062-92b0e0327562'
        }
        
        

        ReportServerWeb CONTOSO-ReportSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
        }
    }
}
PullClientConfig
```

>**참고:** 끌어오기 서버를 설정할 때 웹 서비스 이름을 원하는 대로 지정할 수 있지만 **ServerURL** 속성은 서비스 이름과 일치해야 합니다.

## 보고서 데이터 가져오기

끌어오기 서버에 전송된 보고서는 서버의 데이터베이스에 입력됩니다. 보고서는 웹 서비스 호출을 통해 사용할 수 있습니다. 특정 노드에 대한 보고서를 검색하려면, 보고서 웹 서비스에 다음 형식으로 HTTP 요청을 보냅니다. `http://CONTOSO-REPORT:8080/PSDSCReportServer.svc/Nodes(AgentID = MyNodeAgentId)/Reports` 여기서 `MyNodeAgentId`는 보고서를 가져올 노드의 에이전트 ID입니다. 해당 노드에서 [Get-DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn407378.aspx)를 호출하여 노드에 대한 에이전트 ID를 가져올 수 있습니다.

보고서는 JSON 개체의 배열로 반환됩니다.

다음 스크립트는 스크립트가 실행되는 노드에 대한 보고서를 반환합니다.

```powershell
function GetReport
{
    param($AgentId = "$((glcm).AgentId)", $serviceURL = "http://CONTOSO-REPORT:8080/PSDSCReportServer.svc")
    $requestUri = "$serviceURL/Nodes(AgentId= '$AgentId')/Reports"
    $request = Invoke-WebRequest -Uri $requestUri  -ContentType "application/json;odata=minimalmetadata;streaming=true;charset=utf-8" `
               -UseBasicParsing -Headers @{Accept = "application/json";ProtocolVersion = "2.0"} `
               -ErrorAction SilentlyContinue -ErrorVariable ev
    $object = ConvertFrom-Json $request.content
    return $object.value
}
```
    
## 보고서 데이터 보기

변수를 **GetReport** 함수의 결과로 설정하는 경우, 반환되는 배열의 요소에서 개별 필드를 볼 수 있습니다.

```powershell
$reports = GetReport
$reports[1]


JobId                : 019dfbe5-f99f-11e5-80c6-001dd8b8065c
OperationType        : Consistency
RefreshMode          : Pull
Status               : Success
ReportFormatVersion  : 2.0
ConfigurationVersion : 2.0.0
StartTime            : 04/03/2016 06:21:43
EndTime              : 04/03/2016 06:22:04
RebootRequested      : False
Errors               : {}
StatusData           : {{"StartDate":"2016-04-03T06:21:43.7220000-07:00","IPV6Addresses":["2001:4898:d8:f2f2:852b:b255:b071:283b","fe80::852b:b255:b071
                       :283b%12","::2000:0:0:0","::1","::2000:0:0:0"],"DurationInSeconds":"21","JobID":"{019DFBE5-F99F-11E5-80C6-001DD8B8065C}","Curren
                       tChecksum":"A7797571CB9C3AF4D74C39A0FDA11DAF33273349E1182385528FFC1E47151F7F","MetaData":"Author: configAuthor; Name: 
                       Sample_ArchiveFirewall; Version: 2.0.0; GenerationDate: 04/01/2016 15:23:30; GenerationHost: CONTOSO-PullSrv;","RebootRequested":"False
                       ","Status":"Success","IPV4Addresses":["10.240.179.151","127.0.0.1"],"LCMVersion":"2.0","ResourcesNotInDesiredState":[{"SourceInf
                       o":"C:\\ReportTest\\Sample_xFirewall_AddFirewallRule.ps1::23::9::xFirewall","ModuleName":"xNetworking","DurationInSeconds":"8.785",
                       "InstanceName":"Firewall","StartDate":"2016-04-03T06:21:56.4650000-07:00","ResourceName":"xFirewall","ModuleVersion":"2.7.0.0","
                       RebootRequested":"False","ResourceId":"[xFirewall]Firewall","ConfigurationName":"Sample_ArchiveFirewall","InDesiredState":"False
                       "}],"NumberOfResources":"2","Type":"Consistency","HostName":"CONTOSO-PULLCLI","ResourcesInDesiredState":[{"SourceInfo":"C:\\ReportTest\\Sample_xFirewall_AddFirewallRule.ps1::16::9::Archive","ModuleName":"PSDesiredStateConfiguration","DurationInSeconds":"1.848",
                       "InstanceName":"ArchiveExample","StartDate":"2016-04-03T06:21:56.4650000-07:00","ResourceName":"Archive","ModuleVersion":"1.1","
                       RebootRequested":"False","ResourceId":"[Archive]ArchiveExample","ConfigurationName":"Sample_ArchiveFirewall","InDesiredState":"T
                       rue"}],"MACAddresses":["00-1D-D8-B8-06-5C","00-00-00-00-00-00-00-E0"],"MetaConfiguration":{"AgentId":"52DA826D-00DE-4166-8ACB-73F2B46A7E00",
                       "ConfigurationDownloadManagers":[{"SourceInfo":"C:\\ReportTest\\LCMConfig.ps1::14::9::ConfigurationRepositoryWeb","A
                       llowUnsecureConnection":"True","ServerURL":"http://CONTOSO-PullSrv:8080/PSDSCPullServer.svc","RegistrationKey":"","ResourceId":"[Config
                       urationRepositoryWeb]CONTOSO-PullSrv","ConfigurationNames":["ClientConfig"]}],"ActionAfterReboot":"ContinueConfiguration","LCMCo
                       mpatibleVersions":["1.0","2.0"],"LCMState":"Idle","ResourceModuleManagers":[],"ReportManagers":[{"AllowUnsecureConnection":"True
                       ","RegistrationKey":"","ServerURL":"http://CONTOSO-PullSrv:8080/PSDSCPullServer.svc","ResourceId":"[ReportServerWeb]CONTOSO-PullSrv","S
                       ourceInfo":"C:\\ReportTest\\LCMConfig.ps1::24::9::ReportServerWeb"}],"StatusRetentionTimeInDays":"10","LCMVersion":"2.0","Config
                       urationMode":"ApplyAndMonitor","RefreshFrequencyMins":"30","RebootNodeIfNeeded":"True","RefreshMode":"Pull","DebugMode":["NONE"]
                       ,"LCMStateDetail":"","AllowModuleOverwrite":"False","ConfigurationModeFrequencyMins":"15"},"Locale":"en-US","Mode":"Pull"}}
AdditionalData       : {}
```

기본적으로 보고서는 **JobID**를 기준으로 정렬되어 있습니다. 가장 최근 보고서를 보려면 **StartTime** 속성을 기준으로 보고서를 내림차순으로 정렬한 다음 배열의 첫 번째 요소를 가져올 수 있습니다.

```powershell
$reportsByStartTime = $reports | Sort-Object {$_."StartTime" -as [DateTime] } -Descending
$reportMostRecent = $reportsByStartTime[0]
```

**StatusData** 속성은 다양한 속성이 있는 개체입니다. 대부분의 보고 데이터가 이 속성에 있습니다. 최근 보고서에 대한 **StatusData** 속성의 개별 필드를 살펴보겠습니다.

```powershell
$statusData = $reportMostRecent.StatusData | ConvertFrom-Json
$statusData

StartDate                  : 2016-04-04T11:21:41.2990000-07:00
IPV6Addresses              : {2001:4898:d8:f2f2:852b:b255:b071:283b, fe80::852b:b255:b071:283b%12, ::2000:0:0:0, ::1...}
DurationInSeconds          : 25
JobID                      : {135D230E-FA92-11E5-80C6-001DD8B8065C}
CurrentChecksum            : A7797571CB9C3AF4D74C39A0FDA11DAF33273349E1182385528FFC1E47151F7F
MetaData                   : Author: configAuthor; Name: Sample_ArchiveFirewall; Version: 2.0.0; GenerationDate: 04/01/2016 15:23:30; GenerationHost: 
                             CONTOSO-PullSrv;
RebootRequested            : False
Status                     : Success
IPV4Addresses              : {10.240.179.151, 127.0.0.1}
LCMVersion                 : 2.0
ResourcesNotInDesiredState : {@{SourceInfo=C:\ReportTest\Sample_xFirewall_AddFirewallRule.ps1::23::9::xFirewall; ModuleName=xNetworking; 
                             DurationInSeconds=10.725; InstanceName=Firewall; StartDate=2016-04-04T11:21:55.7200000-07:00; ResourceName=xFirewall; 
                             ModuleVersion=2.7.0.0; RebootRequested=False; ResourceId=[xFirewall]Firewall; ConfigurationName=Sample_ArchiveFirewall; 
                             InDesiredState=False}}
NumberOfResources          : 2
Type                       : Consistency
HostName                   : CONTOSO-PULLCLI
ResourcesInDesiredState    : {@{SourceInfo=C:\ReportTest\Sample_xFirewall_AddFirewallRule.ps1::16::9::Archive; ModuleName=PSDesiredStateConfiguration; 
                             DurationInSeconds=2.672; InstanceName=ArchiveExample; StartDate=2016-04-04T11:21:55.7200000-07:00; ResourceName=Archive; 
                             ModuleVersion=1.1; RebootRequested=False; ResourceId=[Archive]ArchiveExample; ConfigurationName=Sample_ArchiveFirewall; 
                             InDesiredState=True}}
MACAddresses               : {00-1D-D8-B8-06-5C, 00-00-00-00-00-00-00-E0}
MetaConfiguration          : @{AgentId=52DA826D-00DE-4166-8ACB-73F2B46A7E00; ConfigurationDownloadManagers=System.Object[]; 
                             ActionAfterReboot=ContinueConfiguration; LCMCompatibleVersions=System.Object[]; LCMState=Idle; 
                             ResourceModuleManagers=System.Object[]; ReportManagers=System.Object[]; StatusRetentionTimeInDays=10; LCMVersion=2.0; 
                             ConfigurationMode=ApplyAndMonitor; RefreshFrequencyMins=30; RebootNodeIfNeeded=True; RefreshMode=Pull; 
                             DebugMode=System.Object[]; LCMStateDetail=; AllowModuleOverwrite=False; ConfigurationModeFrequencyMins=15}
Locale                     : en-US
Mode                       : Pull
```

무엇보다도, 이 속성은 가장 최근 구성에서 두 개의 리소스를 호출했으며, 그중에서 하나는 원하는 상태였고 다른 하나는 원하는 상태가 아니었음을 보여 줍니다. **ResourcesNotInDesiredState** 속성만 포함된 보다 읽기 쉬운 출력을 가져올 수 있습니다.

```powershell
$statusData.ResourcesInDesiredState

SourceInfo        : C:\ReportTest\Sample_xFirewall_AddFirewallRule.ps1::16::9::Archive
ModuleName        : PSDesiredStateConfiguration
DurationInSeconds : 2.672
InstanceName      : ArchiveExample
StartDate         : 2016-04-04T11:21:55.7200000-07:00
ResourceName      : Archive
ModuleVersion     : 1.1
RebootRequested   : False
ResourceId        : [Archive]ArchiveExample
ConfigurationName : Sample_ArchiveFirewall
InDesiredState    : True
```

이러한 예제는 보고서 데이터로 수행할 수 있는 작업에 대해 알 수 있도록 하기 위한 것입니다. PowerShell에서의 JSON 사용에 대한 소개 내용을 알려면 [Playing with JSON and PowerShell(JSON 및 PowerShell 사용)](https://blogs.technet.microsoft.com/heyscriptingguy/2015/10/08/playing-with-json-and-powershell/)을 참조하세요.

## 참고 항목
- [로컬 구성 관리자 구성](metaConfig.md)
- [DSC 웹 끌어오기 서버 설정](pullServer.md)
- [구성 이름을 사용하여 끌어오기 클라이언트 설정](pullClientConfigNames.md)




<!--HONumber=Jun16_HO4-->


