---
title: Azure 코스모스 DB 쿼리 언어의 CONCAT
description: Azure Cosmos DB의 CONCAT SQL 시스템 함수가 두 개 이상의 문자열 값을 연결한 결과인 문자열을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302613"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure 코스모스 DB)
 둘 이상의 문자열 값을 연결한 결과인 문자열을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   다른 값에 연결 하는 문자열 식입니다. 함수에는 `CONCAT` 두 개 이상의 *str_expr* 인수가 필요합니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 지정된 값의 연결된 문자열을 반환합니다.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
