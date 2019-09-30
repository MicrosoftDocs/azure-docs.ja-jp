---
title: Azure SQL Data Warehouse への予約割引の適用方法 | Microsoft Docs
description: コストを節約するために Azure SQL Data Warehouse に予約割引を適用する方法を説明します。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "60918402"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse への予約割引の適用方法

Azure SQL Data Warehouse の予約容量を購入した後で、そのリージョン内に存在するデータ ウェアハウスに予約割引が自動的に適用されます。 予約割引は、SQL Data Warehouse cDWU 測定ごとに発生した使用量に適用されます。 ストレージとネットワークは従量課金制で課金されます。

## <a name="reservation-discount-application"></a>適用される予約割引

SQL Data Warehouse の予約容量割引は、実行中のウェアハウスに 1 時間単位で適用されます。 ウェアハウスを 1 時間デプロイしないと、その 1 時間の予約容量は無駄になります。 持ち越すことはできません。

購入後は、購入した予約が、任意の時点でのウェアハウスの実行によって発生する SQL Data Warehouse 使用量と照合されます。 一部のウェアハウスをシャットダウンすると、予約割引はその他の一致するウェアハウスに自動的に適用されます。

1 時間ずっと実行しないウェアハウスについては、その時間の予約は他の一致するインスタンスに自動的に適用されます。

## <a name="discount-examples"></a>割引の例

次の例は、デプロイに応じて、SQL Data Warehouse の予約容量割引がどのように適用されるかを示しています。

- **例 1**:100 cDWU 予約容量を 5 ユニット購入します。 DW1500c の SQL Data Warehouse インスタンスを 1 時間実行します。 このケースでは、100 cDWU の 15 ユニット分の使用量が発生します。 予約割引は使用した 5 ユニットに適用されます。 使用した残りの 10 ユニット分の 100 cDWU 使用量については従量課金制で課金されます。

- **例 2**:100 cDWU 予約容量を 5 ユニット購入します。 DW100c の SQL Data Warehouse インスタンス 2 つを 1 時間実行します。 このケースでは、1 ユニットの 100 cDWU に対して 2 つの使用イベントが発生しています。 両方の使用イベントが予約容量割引を受けます。 残りの 100 cDWU 予約容量 3 ユニット分は、将来使うために持ち越されず、無駄になります。

- **例 3**:100 cDWU 予約容量を 1 ユニット購入します。 DW100c の SQL Data Warehouse インスタンス 2 つを実行します。 それぞれ 30 分間実行します。 このケースでは、両方の使用イベントが予約容量割引を受けます。 従量課金制で課金される使用分はありません。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

- ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [予約トランザクションの表示](billing-view-reservations.md)
- [API での予約トランザクションと使用率の取得](billing-reservation-apis.md)
- [予約を管理する](billing-manage-reserved-vm-instance.md)
