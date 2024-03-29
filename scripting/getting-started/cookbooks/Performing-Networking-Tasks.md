---
title: "네트워킹 작업 수행"
ms.date: 2016-05-11
keywords: powershell,cmdlet
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: a43cc55f-70c1-45c8-9467-eaad0d57e3b5
translationtype: Human Translation
ms.sourcegitcommit: 03ac4b90d299b316194f1fa932e7dbf62d4b1c8e
ms.openlocfilehash: 6d878b89a4cd49948cb465525e74e92db819c192

---

# 네트워킹 작업 수행
TCP\/IP는 가장 일반적으로 사용되는 네트워크 프로토콜이므로 대부분의 \- 간단한 네트워크 프로토콜 관리 작업은 TCP\/IP와 관련이 있습니다. 이 섹션에서는 Windows PowerShell 및 WMI를 사용하여 이러한 작업을 수행합니다.

### 컴퓨터의 IP 주소 표시
로컬 컴퓨터에서 사용 중인 모든 IP 주소를 가져오려면 다음 명령을 사용합니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Format-Table -Property IPAddress
```

이 명령의 출력 결과는 다음과 같이 값이 중괄호로 묶여 있기 때문에 일반적인 속성 목록과 다릅니다.

<pre>IP 주소
---------
{192.168.1.80} {192.168.148.1} {192.168.171.1} {0.0.0.0}</pre>

중괄호로 표시되는 이유를 이해하려면 Get\-Member cmdlet을 사용하여 **IPAddress** 속성을 검사합니다.

<pre>PS> Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Get-Member -Name IPAddress TypeName: System.Management.ManagementObject#root\cimv2\Win32_NetworkAdapter Configuration Name      MemberType Definition ----      ---------- ---------- IPAddress Property   System.String[] IPAddress {get;}</pre>

각 네트워크 어댑터의 IP주소 속성은 실제 배열입니다. 정의에 있는 중괄호는 **IPAddress**가 **System.String** 값이 아니라 **System.String** 값의 배열임을 나타냅니다.

### IP 구성 데이터 표시
각 네트워크 어댑터의 자세한 IP 구성 데이터를 표시하려면 다음 명령을 사용합니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName .
```

네트워크 어댑터 구성 개체의 기본 표시에는 매우 간단한 정보만 표시됩니다. 자세히 검사하고 문제를 해결하려면 Select\-Object 또는 형식 지정 cmdlet(예: Format\-List)을 사용하여 표시할 속성을 지정합니다.

최신 TCP\/IP 네트워크를 사용하는 경우와 같이 IPX 또는 WINS 속성이 필요 없는 경우 다음과 같이 Select\-Object의 ExcludeProperty 매개 변수를 사용하여 이름이 "WINS" 또는 "IPX:"로 시작하는 속성을 숨길 수 있습니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Select-Object -Property [a-z]* -ExcludeProperty IPX*,WINS*
```

이 명령은 DHCP, DNS, 라우팅 및 기타 보조 IP 구성 속성에 대한 세부 정보를 반환합니다.

### 컴퓨터에 대해 ping 수행
**Win32\_PingStatus**를 사용하여 컴퓨터에 대해 간단한 ping을 수행할 수 있습니다. 다음 명령은 ping을 수행하지만 긴 출력 결과를 반환합니다.

```
Get-WmiObject -Class Win32_PingStatus -Filter "Address='127.0.0.1'" -ComputerName .
```

이 출력을 보기 쉽게 요약하려면 다음 명령에 의해 생성되는 Address, ResponseTime 및 StatusCode 속성을 표시하면 됩니다. Format\-Table의 Autosize 매개 변수는 테이블 열 크기를 조정하여 Windows PowerShell에 올바로 표시되도록 합니다.

```
PS> Get-WmiObject -Class Win32_PingStatus -Filter "Address='127.0.0.1'" -ComputerName . | Format-Table -Property Address,ResponseTime,StatusCode -Autosize

Address   ResponseTime StatusCode
-------   ------------ ----------
127.0.0.1            0          0
A status code of 0 indicates a successful ping.
```

배열을 사용하여 하나의 명령으로 여러 컴퓨터를 ping할 수 있습니다. 여러 개의 주소가 있기 때문에 다음과 같이 **ForEach\-Object**를 사용하여 각 주소를 따로 ping합니다.

```
"127.0.0.1","localhost","research.microsoft.com" | ForEach-Object -Process {Get-WmiObject -Class Win32_PingStatus -Filter ("Address='" + $_ + "'") -ComputerName .} | Select-Object -Property Address,ResponseTime,StatusCode
```

같은 명령 형식을 사용하여 네트워크 번호 192.168.1.0을 사용하는 개인 네트워크, 표준 클래스 C 서브넷 마스크(255.255.255.0) 등 서브넷에 있는 모든 컴퓨터를 ping할 수 있습니다. 192.168.1.1에서 192.168.1.254 범위에 있는 주소만 유효한 로컬 주소입니다. 0은 항상 네트워크 번호로 예약되어 있고 255는 서브넷 브로드캐스트 주소입니다.

Windows PowerShell에서 1에서 254까지의 번호 배열을 나타내려면 **1..254** 문을 사용합니다. 배열을 생성하고 ping 문의 부분 주소에 값을 추가하여 서브넷 ping을 완료할 수 있습니다.

```
1..254| ForEach-Object -Process {Get-WmiObject -Class Win32_PingStatus -Filter ("Address='192.168.1." + $_ + "'") -ComputerName .} | Select-Object -Property Address,ResponseTime,StatusCode
```

주소 범위를 생성하는 방법을 다른 작업에 적용할 수도 있습니다. 예를 들어 다음과 같이 전체 주소 집합을 생성할 수 있습니다.

`$ips = 1..254 | ForEach-Object -Process {"192.168.1." + $_}`

### 네트워크 어댑터 속성 검색
이 사용 설명서 앞부분에서는 **Win32\_NetworkAdapterConfiguration**을 사용하여 일반적인 구성 속성을 검색할 수 있다고 설명했습니다. TCP\/IP 정보에는 그대로 적용되지 않을 수 있지만 MAC 주소와 어댑터 유형 같은 네트워크 어댑터 정보는 컴퓨터의 상태를 파악하는 데 유용할 수 있습니다. 이 정보의 요약을 보려면 다음 명령을 사용합니다.

```
Get-WmiObject -Class Win32_NetworkAdapter -ComputerName .
```

### 네트워크 어댑터의 DNS 도메인 할당
자동 이름 확인에 사용할 DNS 도메인을 할당하려면 **Win32\_NetworkAdapterConfiguration SetDNSDomain** 메서드를 사용합니다. 각 네트워크 어댑터 구성에 대해 개별적으로 DNS 도메인을 할당하기 때문에 다음과 같이 **ForEach\-Object** 문을 사용하여 각 어댑터에 도메인을 할당해야 합니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=true -ComputerName . | ForEach-Object -Process { $_. SetDNSDomain("fabrikam.com") }
```

TCP\/IP만 사용하는 네트워크에 있는 컴퓨터의 몇 가지 네트워크 어댑터 구성이 실제 TCP\/IP 어댑터가 아니므로 필터링 문인 "IPEnabled\=true"가 필요합니다. 즉, 이 구성은 모든 어댑터에 대한 RAS, PPTP, QoS 및 기타 서비스를 지원하는 일반 소프트웨어 요소이기 때문에 자체 주소를 가지고 있지 않습니다.

**Get\-WmiObject** 필터를 사용하는 대신 **Where\-Object** cmdlet을 사용하여 명령을 필터링할 수 있습니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName . | Where-Object -FilterScript {$_.IPEnabled} | ForEach-Object -Process {$_.SetDNSDomain("fabrikam.com")}
```

### DHCP 구성 작업 수행
DHCP 세부 정보는 DNS 구성과 같이 네트워크 어댑터 집합을 사용하여 수정합니다. WMI에서는 여러 가지 고유한 작업을 수행할 수 있는데, 이 설명서에서는 몇 가지 일반적인 작업을 단계별로 수행하는 방법을 보여 줍니다.

#### DHCP 사용 가능 어댑터 확인
컴퓨터에서 DHCP 사용 가능 어댑터를 찾으려면 다음 명령을 사용합니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "DHCPEnabled=true" -ComputerName .
```

IP 구성에 문제가 있는 어댑터를 제외하려면 IP 사용 어댑터만 검색하면 됩니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled=true and DHCPEnabled=true" -ComputerName .
```

#### DHCP 속성 검색
어댑터의 DHCP 관련 속성은 일반적으로 "DHCP"로 시작되므로 Format\-Table의 Property 매개 변수를 사용하여 해당 속성만 표시할 수 있습니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "DHCPEnabled=true" -ComputerName . | Format-Table -Property DHCP*
```

#### 각 어댑터에서 DHCP 사용
모든 어댑터에서 DHCP를 사용하도록 설정하려면 다음 명령을 사용합니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=true -ComputerName . | ForEach-Object -Process {$_.EnableDHCP()}
```

**Filter** 문 "IPEnabled\=true 및 DHCPEnabled\=false"를 사용하여 이미 설정된 곳에서는 DHCP를 설정하지 않도록 할 수 있습니다. 하지만 이 단계를 생략해도 오류가 발생하지는 않습니다.

#### 특정 어댑터에서 DHCP 임대 해제 및 갱신
**Win32\_NetworkAdapterConfiguration** 클래스에는 **ReleaseDHCPLease** 및 **RenewDHCPLease** 메서드가 있습니다. 두 메서드의 사용 방법은 동일합니다. 일반적으로 이러한 메서드는 특정 서브넷에 있는 어댑터 주소를 임대 해제 또는 갱신만 하면 되는 경우에 사용합니다. 서브넷에서 어댑터를 필터링하는 가장 쉬운 방법은 이 서브넷의 게이트웨이를 사용하는 어댑터 구성만 선택하는 것입니다. 예를 들어 다음 명령은 192.168.1.254에서 DHCP를 임대하는 로컬 컴퓨터에 있는 어댑터의 모든 DHCP를 임대 해제합니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled=true and DHCPEnabled=true" -ComputerName . | Where-Object -FilterScript {$_.DHCPServer -contains "192.168.1.254"} | ForEach-Object -Process {$_.ReleaseDHCPLease()}
```

DHCP 임대를 갱신하려면 다음과 같이 **ReleaseDHCPLease** 메서드 대신 **RenewDHCPLease** 메서드를 호출하기만 하면 됩니다.

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled=true and DHCPEnabled=true" -ComputerName . | Where-Object -FilterScript {$_.DHCPServer -contains "192.168.1.254"} | ForEach-Object -Process {$_.ReleaseDHCPLease()}
```

> [!NOTE]
> 원격 컴퓨터에서 이 메서드를 사용하면 임대 해제 또는 갱신된 어댑터를 통해 원격 시스템에 연결하는 경우 이 시스템에 액세스할 수 없습니다.

#### 모든 어댑터에서 DHCP 임대 해제 및 갱신
**Win32\_NetworkAdapterConfiguration** 메서드, **ReleaseDHCPLeaseAll** 및 **RenewDHCPLeaseAll**을 사용하여 모든 어댑터에서 DHCP 주소를 한꺼번에 임대 해제 또는 갱신할 수 있습니다. 그러나 특정 어댑터 대신 WMI 클래스에서 한꺼번에 임대 해제하고 갱신하므로 이 명령을 특정 어댑터가 아니라 WMI 클래스에 적용해야 합니다.

WMI 클래스를 모두 표시하고 원하는 클래스만 이름으로 선택하여 클래스 인스턴스 대신 WMI 클래스에 대한 참조를 사용할 수 있습니다. 예를 들어 다음 명령은 Win32\_NetworkAdapterConfiguration 클래스를 반환합니다.

```
Get-WmiObject -List | Where-Object -FilterScript {$_.Name -eq "Win32_NetworkAdapterConfiguration"}
```

전체 명령을 클래스로 처리한 다음 이 클래스에서 **ReleaseDHCPAdapterLease** 메서드를 호출할 수 있습니다. 다음 명령에서는 **Get\-WmiObject** 및 **Where\-Object** 파이프라인 요소가 괄호로 묶여 있기 때문에 Windows PowerShell에서 먼저 평가됩니다.

```
( Get-WmiObject -List | Where-Object -FilterScript {$_.Name -eq "Win32_NetworkAdapterConfiguration"} ).ReleaseDHCPLeaseAll()
```

다음과 같이 동일한 명령 형식을 사용하여 **RenewDHCPLeaseAll** 메서드를 호출할 수 있습니다.

```
( Get-WmiObject -List | Where-Object -FilterScript {$_.Name -eq "Win32_NetworkAdapterConfiguration"} ).RenewDHCPLeaseAll()
```

### 네트워크 공유 만들기
네트워크 공유를 만들려면 다음과 같이 **Win32\_Share Create** 메서드를 사용합니다.

```
(Get-WmiObject -List -ComputerName . | Where-Object -FilterScript {$_.Name -eq "Win32_Share"}).Create("C:\temp","TempShare",0,25,"test share of the temp folder")
```

Windows PowerShell에서 **net share**를 사용하여 공유를 만들 수도 있습니다.

```
net share tempshare=c:\temp /users:25 /remark:"test share of the temp folder"
```

### 네트워크 공유 제거
**Win32\_Share**와 함께 네트워크 공유를 제거할 수 있지만 **Win32\_Share** 클래스 대신 제거할 특정 공유를 검색해야 하므로 제거 프로세스는 공유 만들기와 약간 다릅니다. 다음 문은 "TempShare" 공유를 삭제합니다.

```
(Get-WmiObject -Class Win32_Share -ComputerName . -Filter "Name='TempShare'").Delete()
```

다음과 같이 **Net share**를 사용할 수도 있습니다.

```
PS> net share tempshare /delete
tempshare was deleted successfully.
```

### 액세스 가능한 Windows 네트워크 드라이브 연결
**New\-PSDrive** cmdlet은 Windows PowerShell 드라이브를 만들지만 이런 방식으로 만든 드라이브는 Windows PowerShell에서만 사용할 수 있습니다. 새 네트워크 드라이브를 만들려면 **WScript.Network** COM 개체를 사용하면 됩니다. 다음 명령은 \\\\FPS01\\users 공유를 로컬 드라이브 B:에 매핑합니다.

```
(New-Object -ComObject WScript.Network).MapNetworkDrive("B:", "\\FPS01\users")
```

다음과 같이 **net use** 명령을 사용할 수도 있습니다.

```
net use B: \\FPS01\users
```

**WScript.Network** 또는 net use를 사용하여 매핑된 드라이브는 Windows PowerShell에서 즉시 사용할 수 있습니다.




<!--HONumber=Jun16_HO4-->


