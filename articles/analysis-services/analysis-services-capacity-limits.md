---
title: Azure Analysis Services のリソースとオブジェクトの制限 | Microsoft Docs
description: Azure Analysis Services のリソースとオブジェクトの制限について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 40a5b68a12724f2574af19bb10c276c54c5afba0
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997602"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services のリソースとオブジェクトの制限

この記事では、リソースとモデル オブジェクトの制限について説明します。

## <a name="tier-limits"></a>レベルの制限

Developer、Basic、Standard の各レベルの QPU とメモリの制限については、[「Azure Analysis Services の価格」ページ](https://azure.microsoft.com/pricing/details/analysis-services/)を参照してください。

## <a name="object-limits"></a>オブジェクト制限

この制限は理論上のものです。 数字が小さくなると、パフォーマンスが低下します。

|Object|最大サイズ/数|  
|------------|----------------------------|  
|インスタンスのデータベース|16,000|  
|データベース内のテーブルと列の合計数|16,000|  
|テーブル内の行|無制限<br /><br /> **警告:** テーブル内の 1 つの列には 1,999,999,997 を超える個別値を設定することができないという制限があります。|  
|テーブル内の階層|15,999|  
|階層内のレベル|15,999|  
|リレーションシップ|8,000|  
|すべてのテーブルのキー列|15,999|  
|テーブル内のメジャー|2^31-1 = 2,147,483,647|  
|クエリによって返されるセル|2^31-1 = 2,147,483,647|  
|ソース クエリのレコード サイズ|64 K|  
|オブジェクト名の長さ|512 文字|  


