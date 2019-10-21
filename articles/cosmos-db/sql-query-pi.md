---
title: Azure Cosmos DB クエリ言語の PI
description: Azure Cosmos DB での SQL システム関数 PI について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349654"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 円周率の定数値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`PI` の値を返します。  
  
```sql
SELECT PI() AS pi 
```  
  
 結果セットは次のようになります。  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>次の手順

- [数学関数 (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
