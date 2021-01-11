---
title: 予約割引の概要 - Azure Database for PostgreSQL 単一サーバー
description: Azure Database for PostgreSQL 単一サーバーに対する予約割引の適用方法について学習します。
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 51b2f4964c01efbfc82008134d47f09648a772ff
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681976"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーに対する予約割引の適用方法

Azure Database for PostgreSQL 単一サーバーの予約容量を購入すると、予約の属性や数量に合致する PostgreSQL 単一サーバーに対して予約割引が自動的に適用されます。 予約の対象となるのは、Azure Database for PostgreSQL 単一サーバーのコンピューティング コストのみです。 ストレージおよびネットワークについては、通常料金が適用されます。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、***使用しないと失われます***。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。</br>

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーに適用される割引

Azure Database for PostgreSQL 単一サーバーの予約容量の割引は、実行中の PostgreSQL 単一サーバーに対して時間単位で適用されます。 購入した予約は、実行中の Azure Database for PostgreSQL 単一サーバーで生成されるコンピューティング使用量と一致します。 PostgreSQL 単一サーバーの実行時間が 1 時間に満たない場合、その予約は、予約の属性に一致する他の Azure Database for PostgreSQL 単一サーバーに自動的に適用されます。 この割引は、同時に実行されている Azure Database for PostgreSQL 単一サーバーに適用できます。 予約の属性に一致し、まる 1 時間実行される PostgreSQL 単一サーバーがない場合は、その時間について予約割引の特典を完全に活用することができません。

次の例は、購入したコア数と実行する時間に応じて、Azure Database for PostgreSQL 単一サーバーの予約容量割引がどのように適用されるかを示しています。

* **例 1**:8 仮想コア用の Azure Database for PostgreSQL 単一サーバーの予約容量を購入するとします。 予約の残りの属性と一致する 16 仮想コアの Azure Database for PostgreSQL 単一サーバーを実行している場合、PostgreSQL 単一サーバーのコンピューティング使用量の 8 仮想コアに対して従量課金制の料金が請求され、1 時間分の 8 仮想コアの PostgreSQL 単一サーバーのコンピューティング使用量に対して予約割引が適用されます。</br>

以降の例では、購入する Azure Database for PostgreSQL 単一サーバーの予約容量は、16 仮想コアの Azure Database for PostgreSQL 単一サーバー用であり、残りの予約属性は実行中の PostgreSQL 単一サーバーと一致するものとします。

* **例 2**:8 仮想コアを持つ 2 つの Azure Database for PostgreSQL 単一サーバーを 1 時間実行します。 16 仮想コアの予約割引は、8 仮想コア Azure Database for PostgreSQL 単一サーバーの両方のコンピューティング使用量に適用されます。

* **例 3**:一方の 16 仮想コア Azure Database for PostgreSQL 単一サーバーを、午後 1 時から午後 1 時 30 分まで実行します。 もう一方の 16 仮想コア Azure Database for PostgreSQL 単一サーバーを、午後 1 時 30 分から午後 2 時まで実行します。 いずれも予約割引が適用されます。

* **例 4**:一方の 16 仮想コア Azure Database for PostgreSQL 単一サーバーを、午後 1 時から午後 1 時 45 分まで実行します。 もう一方の 16 仮想コア Azure Database for PostgreSQL 単一サーバーを、午後 1 時 30 分から午後 2 時まで実行します。 15 分間の重復分には、従量課金制の料金が適用されます。 残りの時間の計算使用量には、予約割引が適用されます。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
