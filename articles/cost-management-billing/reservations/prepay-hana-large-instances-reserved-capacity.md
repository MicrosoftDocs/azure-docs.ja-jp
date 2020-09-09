---
title: Azure 予約を使用して SAP HANA Large Instances に保存する
description: HANA Large Instances 予約を購入する前に知っておくべきことと、その購入方法について説明します。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 44f7ce657ea9341779e15f6e4817e8fae1515e47
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685971"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Azure 予約を使用して SAP HANA Large Instances に保存する

Azure 予約を 1 年分または 3 年分事前に購入すると、SAP HANA Large Instances (HLI) のコストを節約することができます。 予約割引は、購入済みの予約インスタンスに対応するプロビジョニング済みの HLI の SKU に適用されます。 この記事は、予約を購入する前に知っておくべきことと、その購入方法を理解するのに役立ちます。

予約を購入することにより、HLI を 1 年間または 3 年間の使用が確定します。 HLI の予約容量の購入には、SKU にバンドルされているコンピューティングと NFS のストレージが含まれています。 予約には、オペレーティング システム、SAP、追加のストレージ コストなどのソフトウェア ライセンス コストは含まれません。 予約割引は、プロビジョニング済みの SAP HLI に自動的に適用されます。 予約期間が終了すると、プロビジョニング済みのリソースに従量課金制の料金が適用されます。

## <a name="purchase-considerations"></a>購入に関する考慮事項

予約容量の購入を行う前に、HLI の SKU をプロビジョニングする必要があります。 予約の支払いは、前払いまたは月払いで行います。 HLI の予約容量には、次の制限があります。

- 予約割引は、マイクロソフトエンタープライズ契約および Microsoft 顧客契約のサブスクリプションにのみ適用されます。 その他のサブスクリプションはサポートされていません。
- HLI の予約容量では、インスタンス サイズの柔軟性はサポートされていません。 予約は、SKU とそれを購入したリージョンにのみ適用されます。
- セルフサービスによるキャンセルと交換はサポートされていません。
- 予約容量のスコープは 1 つのスコープであるため、1 つのサブスクリプションとリソース グループに適用されます。 購入した容量を、別のサブスクリプションで使用できるように更新することはできません。
- HANA 予約容量に共有予約スコープを設定することはできません。 予約スコープを分割、マージ、または更新することはできません。
- 予約容量の API 呼び出しを使用して、一度に 1 つの HLI を購入できます。 追加の数量を購入するには、追加の API 呼び出しを行います。

予約容量は、Azure portal で購入することも、[REST API](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase) を使用して購入することもできます。

## <a name="buy-a-hana-large-instance-reservation"></a>HANA Large Instance 予約を購入する

次の情報を使用して、[予約注文 REST API](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase) で HLI 予約を購入します。

### <a name="get-the-reservation-order-and-price"></a>予約注文と価格を取得する

まず、[価格計算](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/calculate) API を使用して、プロビジョニング済みの HANA Large Instance SKU の予約注文と価格を取得します。

次の例では [armclient](https://github.com/projectkudu/ARMClient) を使用して、PowerShell で REST API 呼び出しを行っています。 予約注文、そして価格計算 API の要求と要求本文は次のようになります。

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

データ フィールドとその説明の詳細については、「[HLI 予約フィールド](#hli-reservation-fields)」を参照してください。

次の例のような応答が返されます。 `quoteId` に対して返された値に注目してください。

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>購入する

返された `quoteId` と、前の「[予約注文と価格を取得する](#get-the-reservation-order-and-price)」セクションで取得した `reservationOrderId` を使用して購入を行います。

次が要求の例です。

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

次が応答の例です。 注文が正常に行われた場合は、`provisioningState` が `creating` になるはずです。

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>購入状態の成功を確認する

予約注文の GET 要求を実行して、注文書の状態を確認します。 `provisioningState` は `Succeeded` になるはずです。

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

応答は次の例のようになります。

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>HLI 予約フィールド

次の情報は、さまざまな予約フィールドの意味について説明しています。

  **SKU** HLI の SKU 名。 `SAP_HANA_On_Azure_<SKUname>` のように表示されます。

  **Location** 使用可能な HLI のリージョン。 使用可能なリージョンについては、[SAP HANA on Azure (Large Instances) での SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus) に関する記事を参照してください。 場所の文字列形式を取得するには、[場所を取得する API 呼び出し](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations#locationlistresult)を使用します。

  **Reserved Resource type** `SapHana`

  **Subscription** 予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約のコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または Microsoft 顧客契約である必要があります。 年額コミットメント残高から料金が差し引かれるか(使用可能な場合)、超過料金として課金されます。

  **Scope** 予約のスコープは、1 つのスコープである必要があります。

  **Term** 1 年間または 3 年間。 `P1Y` または `P3Y` のように表示されます。

  **Quantity** 予約に対して購入しているインスタンス数。 購入する数量は、一度に 1 つの HLI です。 予約を追加する場合は、対応するフィールドで API 呼び出しを繰り返します。

## <a name="troubleshoot-errors"></a>エラーをトラブルシューティングする

予約購入を行うと、次の例のようなエラーが表示される場合があります。 原因として、HLI が購入用にプロビジョニングされていないことが考えられます。 その場合は、Microsoft アカウント チームに連絡して HLI をプロビジョニングしてから、予約購入をお試しください。

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>次のステップ

- [Postman と cURL で Azure REST API を呼び出す方法](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)について確認します。
- 使用可能な SKU の一覧とリージョンについては、[SAP HANA on Azure (Large Instances) での SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus) に関する記事を参照してください。