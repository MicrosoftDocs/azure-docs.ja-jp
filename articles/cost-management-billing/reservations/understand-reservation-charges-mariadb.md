---
title: 予約割引の概要 - Azure Database for MariaDB
description: Azure Database for MariaDB サーバーに対する予約割引の適用方法について学習します
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be2dd4235883601b0b13c5783e4c7c7c106c20e9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682027"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Azure Database for MariaDB に対する予約割引の適用方法

Azure Database for MariaDB の予約容量を購入すると、予約の属性や数量に合致する MariaDB サーバーに対して予約割引が自動的に適用されます。 予約の対象となるのは、Azure Database for MariaDB のコンピューティング コストのみです。 ストレージおよびネットワークについては、通常料金が適用されます。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、***使用しないと失われます***。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Azure Database for MariaDB に適用される割引

Azure Database for MariaDB の予約容量割引は、実行中の MariaDB サーバーに対して時間単位で適用されます。 購入した予約は、実行中の Azure Database for MariaDB サーバーで生成されるコンピューティング使用量と一致します。 MariaDB サーバーの実行時間が 1 時間に満たない場合、その予約は、予約の属性に一致する他の Azure Database for MariaDB に自動的に適用されます。 この割引は、同時に実行されている Azure Database for MariaDB サーバーに適用できます。 予約の属性に一致し、まる 1 時間実行される MariaDB サーバーがない場合は、その時間について予約割引の特典を完全に活用することができません。

次の例は、購入したコア数と実行する時間に応じて、Azure Database for MariaDB の予約容量割引がどのように適用されるかを示しています。

* **例 1**:8 仮想コア用の Azure Database for MariaDB の予約容量を購入するとします。 予約の残りの属性と一致する 16 仮想コアの Azure Database for MariaDB サーバーを実行している場合、MariaDB サーバーのコンピューティング使用量の 8 仮想コアに対して従量課金制の料金が請求され、1 時間分の 8 仮想コアの MariaDB サーバーのコンピューティング使用量に対して予約割引が適用されます。

以降の例では、購入する Azure Database for MariaDB の予約容量は、16 仮想コアの Azure Database for MariaDB 用であり、残りの予約属性は実行中の MariaDB サーバーと一致するものとします。

* **例 2**:8 仮想コアを持つ 2 つの Azure Database for MariaDB サーバーを 1 時間実行します。 16 仮想コアの予約割引は、8 仮想コア Azure Database for MariaDB サーバーの両方のコンピューティング使用量に適用されます。

* **例 3**:1 つの 16 仮想コア Azure Database for MariaDB サーバーを、午後 1 時から午後 1 時 30 分まで実行します。 別の 16 仮想コア Azure Database for MariaDB サーバーを、午後 1 時 30 分から午後 2 時まで実行します。 いずれも予約割引が適用されます。

* **例 4**:1 つの 16 仮想コア Azure Database for MariaDB サーバーを、午後 1 時から午後 1 時 45 分まで実行します。 別の 16 仮想コア Azure Database for MariaDB サーバーを、午後 1 時 30 分から午後 2 時まで実行します。 15 分間の重復分には、従量課金制の料金が適用されます。 残りの時間の計算使用量には、予約割引が適用されます。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
