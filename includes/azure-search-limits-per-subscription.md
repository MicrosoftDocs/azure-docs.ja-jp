---
title: インクルード ファイル
description: インクルード ファイル
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "67181554"
---
サブスクリプション内に複数のサービスを作成することができます。 作成したサービスはそれぞれ、一定のレベルでプロビジョニングできます。 各レベルにはサービス数に上限が設けられています。 たとえば、Basic レベルのサービスであれば 12 個まで作成できますが、同じサブスクリプションの枠内で S1 レベルのサービスをさらにもう 12 個まで作成できます。 レベルの詳細については、「[Azure Search 用の SKU または価格レベルの選択](../articles/search/search-sku-tier.md)」を参照してください。

サービス数の上限は、依頼により引き上げが可能です。 同一のサブスクリプションで通常よりも多数のサービスが必要な場合には、Azure Support にお問い合わせください。

| リソース            | Free<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| 最大サービス数    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| 検索単位の最大スケール (SU)<sup>2</sup> |該当なし |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Free は、専用リソースではなく、共有リソースに基づいています。 スケールアップは、共有リソースではサポートされていません。

<sup>2</sup> Search ユニットは、*レプリカ*または*パーティション*のいずれかとして割り当てられている請求単位です。 ストレージ、インデックス作成、およびクエリ操作については、両方のリソースが必要になります。 SU の計算の詳細については、[クエリとインデックス作成のワークロードに応じたリソース レベルのスケーリング](../articles/search/search-capacity-planning.md)に関するページを参照してください。 