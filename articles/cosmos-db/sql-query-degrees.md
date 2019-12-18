---
title: Azure Cosmos DB クエリ言語の DEGREES
description: ラジアン単位で指定された角度に対応する度単位の角度を返す Azure Cosmos DB の DEGREES SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f432f5ef57c8eccc0cf629f00d2231364785b9a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871331"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
 ラジアン単位で指定された角度に対応する度数単位の角度を返します。  
  
## <a name="syntax"></a>構文
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、PI/2 ラジアンの角度で度数を返します。  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 結果セットは次のようになります。  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
