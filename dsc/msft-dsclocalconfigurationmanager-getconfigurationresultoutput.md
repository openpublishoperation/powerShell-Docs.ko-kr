---
title: "MSFT_DSCLocalConfigurationManager 클래스의 GetConfigurationResultOutput 메서드"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: c915ebd021ed20209bc491505d45cff2ac89f21d
ms.openlocfilehash: 8f13964dfbbe1cd827c58232a35d1cbacddeed1b

---

# MSFT_DSCLocalConfigurationManager 클래스의 GetConfigurationResultOutput 메서드

특정 작업에 연결된 구성 에이전트 출력을 검색합니다.

구문
------

```mof
uint32 GetConfigurationResultOutput(
  [in]  string                      jobId,
  [in]  uint8                       resumeOutputBookmark[],
  [out] MSFT_DSCConfigurationOutput output[]
);
```

매개 변수
----------

*jobId* \[in\]  
출력 데이터를 가져올 작업의 ID입니다.

*resumeOutputBookmark* \[in\]  
출력이 이전 책갈피의 연속이어야 함을 지정합니다.

*output* \[out\]  
지정한 작업의 출력입니다.

## 반환 값
------------

성공하면 0을 반환하고 그렇지 않으면 오류 코드를 반환합니다.

## 설명

정적 메서드입니다.

## 요구 사항
------------
>**MOF:** DscCore.mof

>**네임스페이스**: Root\Microsoft\Windows\DesiredStateConfiguration


## 참고 항목


[**MSFT_DSCLocalConfigurationManager**](msft-dsclocalconfigurationmanager.md)

 

 






<!--HONumber=Jun16_HO4-->


