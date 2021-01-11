---
title: Azure Cache for Redis に予約割引が適用されるしくみについて | Microsoft Docs
description: Azure Cache for Redis インスタンスに予約割引が適用されるしくみについて説明します。
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 10fedaf5680976ae93984c9b2c607882f650019d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213261"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Azure Cache for Redis インスタンスに予約割引が適用されるしくみについて

Azure Cache for Redis の予約容量を購入すると、予約の属性や数量に合致するキャッシュ インスタンスに対して予約割引が自動的に適用されます。 予約購入分は、ご利用の Azure Cache for Redis の計算コストにのみ充当されます。 ストレージおよびネットワークについては、通常料金が適用されます。 予約容量は、[Premium レベル](/https://docs.microsoft.com/azure/azure-cache-for-redis/quickstart-create-redis)のキャッシュでのみ使用できます。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、***使用しないと失われます***。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

## <a name="discount-applied-to-azure-cache-for-redis"></a>Azure Cache for Redis に適用される割引

Azure Cache for Redis の予約容量割引は、利用中のキャッシュに 1 時間単位で適用されます。 購入した予約は、実行中のキャッシュによる計算の使用量と照合されます。 実行時間が 1 時間に満たないキャッシュの場合、その予約は予約の属性に一致する他のキャッシュに自動的に適用されます。 この割引は、同時に実行されているキャッシュに適用されます。 予約の属性に一致するキャッシュのうち、実行時間が 1 時間を超えるキャッシュがない場合は、その時間について予約割引の特典を完全に活用することができません。

次の例は、購入したキャッシュ数と実行する時間に応じて、Azure Cache for Redis の予約容量割引がどのように適用されるかを示しています。

* **例 1**:6 GB キャッシュ用の Azure Cache for Redis の予約容量を購入するとします。 予約の残りの属性と一致する 13 GB のキャッシュを実行している場合、Azure Cache for Redis の計算使用量の 7 GB に対して従量課金制の料金が請求され、1 時間分の 6 GB のキャッシュの計算使用量に対して予約割引が適用されます。

以降の例では、購入する Azure Cache for Redis の予約容量は、26 GB のキャッシュ用であり、残りの予約の属性は実行中のキャッシュと一致するものとします。

* **例 2**:2 つの 13 GB のキャッシュを 1 時間実行するとします。 26 GB の予約割引が両方のキャッシュの計算使用量に適用されます。

* **例 3**:1 つの 26 GB のキャッシュを午後 1 時から午後 1 時 30 分まで実行するとします。 別の 26 GB のキャッシュを午後 1 時 30 分から午後 2 時まで実行するとします。 いずれも予約割引が適用されます。

* **例 4**:1 つの 26 GB のキャッシュを午後 1 時から午後 1 時 45 分まで実行するとします。 別の 26 GB のキャッシュを午後 1 時 30 分から午後 2 時まで実行するとします。 15 分間の重復分には、従量課金制の料金が適用されます。 残りの時間の計算使用量には、予約割引が適用されます。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](/azure/billing/billing-understand-reserved-instance-usage-ea)に関するページを参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ
ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
