---
title: Azure 予約の推奨事項
description: Azure 予約の推奨事項について説明します。
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928537"
---
# <a name="reservation-recommendations"></a>Reservation recommendations

Azure 予約インスタンス (RI) の購入に関する推奨事項は、Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations)、[Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)、Azure portal の予約購入エクスペリエンスを通じて提供されます。

次の手順では、推奨事項の計算方法を定義しています。

1. 推奨エンジンが、指定されたスコープにおけるリソースの時間単位の使用量を、過去 7 日、30 日、60 日にわたって評価します。
2. エンジンが、予約の有無にかかわらず、使用状況データに基づいてコストをシミュレートします。
3. コストはさまざまな数量に対してシミュレートされ、節約の額が最も大きくなる数量が推奨されます。
4. ご自分のリソースが定期的にシャットダウンされる場合、シミュレーションでは節約の額が特定されず、購入に関する推奨事項は提供されません。
5. 推奨事項の算出には、オンデマンド使用率に対する特別な割引が含まれます。

## <a name="recommendations-in-the-azure-portal"></a>Azure portal における推奨情報

予約購入に関する推奨事項は、購入エクスペリエンスの Azure portal にも記載されています。 推奨事項は **[推奨数量]** にも表示されています。 購入時に、Azure が推奨する数量によって最大限の節約が可能になります。 任意の数量を購入できますが、別の数量を購入した場合、節約は最適になりません。

その理由について、いくつかの例を見てみましょう。

選択した推奨事項に関する次の例の画像で、Azure が推奨する購入数量は 6 です。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="予約購入の推奨事項を示す例" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

推奨事項に関するその他の情報は、 **[See details]\(詳細の表示\)** を選択すると表示されます。 次の画像は、推奨事項の詳細を示しています。 推奨数量は、使用履歴に基づき、最も使用された場合を想定して計算されます。 使用状況が一貫していない場合、100% 使用された場合の推奨事項にならない可能性があります。 この例では、時間の経過と共に使用率が変動していることに注目してください。 予約のコスト、可能な節約、使用率が表示されます。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="予約購入の推奨事項の詳細を示す例" :::

このグラフと推定値は、推奨数量を増やすと変化します。 予約数量を増やすことによって、最終的に予約使用率が削減されるため、節約が減少します。 つまり、完全に使用されていない予約に対して支払いを行うことになります。

予約数量を減らすと、節約も減少します。 使用率が高くなりますが、予約が使用全体に適用されない期間が発生する場合があります。 予約数量を超える使用は、より高価な従量課金制リソースによって使用されます。 次の例の画像はこの点について示しています。 予約数量を 4 に手動で減らしました。 予約の使用量は増加していますが、従量課金制のコストがかかるため、全体的な節約は減少します。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="変更後の予約購入の推奨事項の詳細を示す例" :::

予約による節約効果を最大限に高めるには、できるだけ推奨事項に近い予約を購入してください。

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor における推奨事項

予約購入の推奨量は、Azure Advisor から入手できます。 以下の点に注意してください。

- Advisor で提供されるのは、単一サブスクリプションのスコープでの推奨事項のみです。 課金スコープ全体 (課金アカウントまたは課金プロファイル) の推奨事項を表示するには、次のようにします。
  -  Azure portal の **[予約]**  >  **[追加]** に移動し、推奨事項を表示する種類を選択します。
- 過去 30 日間の利用傾向を考慮した推奨事項を Advisor で利用できます。
- 推奨量と節約額は、3 年間の予約 (可能な場合) を対象としています。 サービスに対して 3 年間の予約が販売されていない場合、推奨量は 1 年間の予約価格を使用して計算されます。
- 推奨事項の算出には、オンデマンド使用率に対する特別な割引が含まれます。
- 共有スコープの予約を購入した場合、Advisor の予約購入に関する推奨事項が非表示になるまでに最大で 5 日間かかることがあります。

## <a name="other-expected-api-behavior"></a>API の予想されるその他の動作

- 7 日間のルックバック期間を使用する場合は、VM が 1 日以上シャットダウンすると、推奨事項を取得できないことがあります。

## <a name="next-steps"></a>次のステップ

- [Azure の予約割引が仮想マシンに適用されるしくみ](../manage/understand-vm-reservation-charges.md)について説明します。
