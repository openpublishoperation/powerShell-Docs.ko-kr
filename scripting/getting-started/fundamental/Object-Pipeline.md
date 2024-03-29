---
title: "개체 파이프라인"
ms.date: 2016-05-11
keywords: powershell,cmdlet
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 523d8ae4-d743-47a4-b79a-806130ca688a
translationtype: Human Translation
ms.sourcegitcommit: 03ac4b90d299b316194f1fa932e7dbf62d4b1c8e
ms.openlocfilehash: 56aa32d40d8e5e61f5328d5a373ca5a1bd7ca6e3

---

# 개체 파이프라인
파이프라인은 일련의 파이프 세그먼트가 연결된 것처럼 동작합니다. 파이프라인을 따라 이동하는 항목은 각 세그먼트를 통과합니다. Windows PowerShell에서 파이프라인을 만들려면 여러 명령을 파이프 연산자 "|"로 연결합니다. 그러면 각 명령의 출력이 그 다음 명령의 입력으로 사용됩니다.

파이프라인은 명령줄 인터페이스에 사용되는 가장 중요한 개념이라고 할 수 있습니다. 파이프라인을 올바로 사용하면 복잡한 명령을 보다 쉽게 입력할 수 있을 뿐 아니라 명령의 작업 흐름도 보다 쉽게 확인할 수 있습니다. 또한 파이프라인은 각 항목에 대해 개별적으로 작동하기 때문에 파이프라인에 있는 항목 수에 따라 파이프라인을 수정하지 않아도 된다는 이점이 있습니다. 게다가 파이프라인에 있는 각 명령(파이프라인 요소)은 해당 출력을 파이프라인에 있는 다음 명령에 항목 단위로 전달하므로 복잡한 명령의 리소스 수요를 줄이고 출력을 즉시 볼 수 있게 해줍니다.

이 장에서는 Windows PowerShell 파이프라인과 가장 보편적으로 사용되는 셸 파이프라인의 차이점에 대해 설명한 다음 파이프라인 출력을 제어하고 파이프라인의 작동 방법을 확인하는 데 사용할 수 있는 몇 가지 기본 도구를 보여 줍니다.




<!--HONumber=Jun16_HO4-->


