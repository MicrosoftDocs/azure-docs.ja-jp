---
title: "Azure Machine Learning データ準備で可能なフィルター式の例 | Microsoft Doc"
description: "このドキュメントでは、Azure ML データ準備で可能なフィルター式の例を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: a80e43f84b518a7c9ce609fbebe34c531e1ab187
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-filter-expressions-python"></a>フィルター式のサンプル (Python) 
この付録を読む前に、[Python 機能拡張の概要](data-prep-python-extensibility-overview.md)に関する記事を参照してください

## <a name="filter-with-equivalence-test"></a>等価テストを使用したフィルター
(数値) Col2 の値が 4 より大きな行だけがフィルターを通ります 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>複数列を使用したフィルター 
Col1 が値「Good」を含んでおり、Col2 が (数値) 値 1 を含んでいる行だけがフィルターを通ります 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>null に対するテスト フィルター
Col1 に null 値がある行だけがフィルターを通ります 
```python
    pd.isnull(row["Col1"])
```
