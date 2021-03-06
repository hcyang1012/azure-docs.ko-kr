---
title: 시냅스 SQL에서 쿼리 레이블 사용
description: 이 문서에는 Synapse SQL에서 쿼리 레이블을 사용하기 위한 필수 팁이 포함되어 있습니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430033"
---
# <a name="use-query-labels-in-synapse-sql"></a>시냅스 SQL에서 쿼리 레이블 사용
이 문서에는 Synapse SQL에서 쿼리 레이블을 사용하기 위한 필수 팁이 포함되어 있습니다.

> [!NOTE]
> SQL 주문형(미리 보기)은 레이블 지정 쿼리를 지원하지 않습니다.

## <a name="what-are-query-labels"></a>쿼리 레이블이란?
SQL 풀은 쿼리 레이블이라는 개념을 지원합니다. 좀더 깊이 들어가기 전에 한 가지 예를 살펴보겠습니다.

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

마지막 줄은 쿼리에 문자열 '내 쿼리 레이블' 태그를 추가합니다. 레이블은 DMV를 통해 쿼리가 가능하므로 이 태그는 특히 유용합니다. 레이블에 대한 쿼리는 문제 쿼리를 찾는 메커니즘을 제공하며 ELT 실행을 통해 진행 률을 식별하는 데 도움이 됩니다.

좋은 명명 규칙이 가장 유용합니다. 예를 들어 PROJECT, 프로시저, 문 또는 COMMENT로 레이블을 시작하면 소스 제어의 모든 코드 중에서 쿼리를 고유하게 식별합니다.

다음 쿼리는 동적 관리 보기를 사용하여 레이블별로 검색합니다.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 쿼리할 때 반드시 단어 레이블을 대괄호 또는 큰따옴표로 묶어야 합니다. 레이블은 예약어이므로 구분하지 않으면 오류가 발생합니다. 
> 
> 

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](develop-overview.md)를 참조하세요.


