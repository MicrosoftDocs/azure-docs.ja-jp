---
title: Azure Cosmos DB クエリ言語の SIN
description: Azure Cosmos DB の SQL システム関数 SIN について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b4450ea9dd50f8093fa9569d7f82fe124022c3a1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349465"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 式で指定されたラジアン単位の角度の三角関数サインを返します。  
  
## <a name="syntax"></a>構文
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定された角度の `SIN` を計算します。  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 結果セットは次のようになります。  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
