---
title: Azure Enterprise REST API
description: この記事では、Azure エンタープライズ登録で使用する REST API について説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ea53ad91dcb411c43b367bfb43bc9dcea3d2bba7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841423"
---
# <a name="azure-enterprise-rest-apis"></a>Azure Enterprise REST API

この記事では、Azure エンタープライズ登録で使用する REST API について説明します。 また、REST API に関する一般的な問題の解決方法についても説明します。

## <a name="consumption-and-usage-apis"></a>Consumption API と Usage API

Microsoft Enterprise Azure のお客様は、REST API を使用して使用状況と課金情報を取得できます。 ロール所有者 (エンタープライズ管理者、部署管理者、アカウント所有者) は、Azure EA Portal からキーを生成することによって、API へのアクセスを有効にする必要があります。 その後、登録番号とキーを提供されたユーザーは、API を使用してデータにアクセスできます。

### <a name="available-apis"></a>利用可能な API

**Balance and Summary** - [Balance and Summary API](billing-enterprise-api-balance-summary.md) では、残高、新規購入、Azure Marketplace サービス料金、精算、および超過料金に関する情報の月次概要が提供されます。 詳細については、「[Reporting APIs for Enterprise customers - Balance and Summary (Enterprise のお客様向けの Reporting API - Balance and Summary)](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)」を参照してください。

**Usage Details** - [Usage Detail API](billing-enterprise-api-usage-detail.md) では、登録別の毎日の消費量の内訳と見積料金が提供されます。 この結果には、インスタンス、測定、および部署に関する情報も含まれています。 この API には、請求期間、または指定した開始日と終了日でクエリを実行できます。 詳細については、「[Reporting APIs for Enterprise customers - Usage Details (Enterprise のお客様向けの Reporting API - Usage Details)](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)」を参照してください。

**Marketplace Store Charge** - [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md) では、指定された請求期間または開始日から終了日までの使用量に基づく Marketplace 料金の日別内訳が返されます。 詳細については、「[Reporting APIs for Enterprise customers - Marketplace Store Charge (Enterprise のお客様向けの Reporting API - Marketplace Store Charge)](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)」を参照してください。

**Price sheet** - [Price Sheet API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) では、登録と請求期間に対する各測定に適用される課金率が提供されます。 詳細については、「[Reporting APIs for Enterprise customers - Price Sheet (Enterprise のお客様向けの Reporting API - Price Sheet)](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)」を参照してください。

**Billing Periods** - [Billing Periods API](billing-enterprise-api-billing-periods.md) では、登録の消費量データが記載された請求期間の一覧が逆時系列順に返されます。 各期間には、BalanceSummary、UsageDetails、Marktplace Charges および PriceSheet という 4 セットのデータの API ルートを示すプロパティが含まれています。 詳細については、「[Reporting APIs for Enterprise customers - Billing Periods (Enterprise のお客様向けの Reporting API - Billing Periods)](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)」を参照してください。

### <a name="enable-api-data-access"></a>API データ アクセスを有効にする

ロール所有者は、Azure EA Portal で、次の手順を実行できます。 **[レポート]**  >  **[使用状況のダウンロード]**  >  **[API アクセス キー]** の順に移動します。 ここでは、次のことができます。

- プライマリ アクセス キーとセカンダリ アクセス キーを生成する。
- アクセス キーを無効にする。
- アクセス キーの開始日と終了日を表示する。

### <a name="generate-or-retrieve-the-api-key"></a>API キーを生成または取得する

1. エンタープライズ管理者としてサインインします。
2. 左側のナビゲーション ウィンドウで **[レポート]** をクリックし、 **[使用状況のダウンロード]** タブをクリックします。
3. **[API アクセス キー]** をクリックします。
4. **[登録アクセス キー]** で、キーの生成アイコンを選択して、プライマリ キーまたはセカンダリ キーを生成します。
5. **[キーの展開]** を選択して、生成された API アクセス キー全体を表示します。
6. **[コピー]** を選択して API アクセス キーを取得し、すぐに使用できるようにします。

![[API アクセス キー] ページの表示例](./media/billing-ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

登録のエンタープライズ管理者ではないユーザーに API アクセス キーを付与するには、次の手順を実行します。

1. 左側のナビゲーション ウィンドウで、 **[管理]** をクリックします。
2. **[DA ビューの請求額]** (部署管理者ビューの請求額) の横にある鉛筆アイコンをクリックします。
3. **[有効]** を選択し、 **[保存]** をクリックします。
4. **[AO ビューの請求額]** (アカウント所有者ビューの請求額) の横にある鉛筆アイコンをクリックします。
5. **[有効]** を選択し、 **[保存]** をクリックします。

![DA および AO ビューの請求額の表示例](./media/billing-ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) 前の手順によって、API アクセス キーの保持者が、使用状況レポートのコスト情報と価格情報にアクセスできるようになります。

### <a name="pass-keys-in-the-api"></a>API にキーを渡す

認証と承認をそれぞれ呼び出すために API キーを渡します。 次のプロパティを HTTP ヘッダーに渡します。

| 要求ヘッダー キー | 値 |
| --- | --- |
| Authorization | 次の形式で値を指定します: **bearer {API\_KEY}**
例: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

次の API 用の Swagger エンドポイントは、[Enterprise Reporting v3 APIs](https://consumption.azure.com/swagger/ui/index) で入手できます。 Swagger は API の検査に役立ちます。 Swagger を使用して、[AutoRest](https://github.com/Azure/AutoRest) または [Swagger CodeGen](http://swagger.io/swagger-codegen/) を使用したクライアント SDK を生成します。 2014 年 5 月 1 日以降に利用可能なデータは、API を通じて入手できます。

### <a name="api-response-codes"></a>API 応答コード

API を使用しているときに、応答の状態コードが表示されます。 次の表ではこれらについて説明します。

| 応答の状態コード | Message | 説明 |
| --- | --- | --- |
| 200 | OK | エラーなし |
| 401 | 権限がありません | API キーが検出されない、正しくない、有効期限が切れている、など |
| 404 | 使用不可 | レポートのエンドポイントが見つからない |
| 400 | 正しくない要求 | 無効なパラメーター - 日付範囲、EA 番号など。 |
| 500 | サーバー エラー | 要求の処理中に予期しないエラーが発生した |

### <a name="usage-and-billing-data-update-frequency"></a>使用状況データと課金データの更新頻度

使用状況データ ファイルと課金データ ファイルは、現在の請求月に対して 24 時間ごとに更新されます。 ただし、最大で 3 日間のデータ待機時間が発生する可能性があります。 たとえば、月曜日に使用量が発生した場合、データ ファイルには木曜日までデータが表示されないことがあります。

### <a name="test-enrollment-for-development"></a>開発用のテスト登録

Azure エンタープライズ登録のないパートナーまたは開発者が API にアクセスする場合は、テスト登録を使用できます。 登録名は _EnrollmentNumber 100_ です。2018 年 6 月までの使用情報を検索してテストできます。 次に、次のキーを使用して API を呼び出し、サンプル データを確認できます。

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImpoeXA2UU9DWlZmY1pmdmhDVGR1OFdxeTJ5byJ9.eyJFbnJvbGxtZW50TnVtYmVyIjoiMTAwIiwiSWQiOiI1ZTc2ZmNiMy0xN2I4LTQ5ZDItYjdkOC0zMDU0YjUwOWY0MWYiLCJSZXBvcnRWaWV3IjoiU3lzdGVtIiwiUGFydG5lcklkIjoiIiwiRGVwYXJ0bWVudElkIjoiIiwiQWNjb3VudElkIjoiIiwiaXNzIjoiZWEubWljcm9zb2Z0YXp1cmUuY29tIiwiYXVkIjoiY2xpZW50LmVhLm1pY3Jvc29mdGF6dXJlLmNvbSIsImV4cCI6MTU4NjM5MDA2OSwibmJmIjoxNTcwNTc4ODY5fQ.lENR5pCBph6iZCVexUlN1b-j7StaILCyBewVHoILD-_fn8S2o2bHY1qUseGOkBwNlaFQfk2OZIo-jQYvnf3eP3UNrNVTCINT0APbc1RqgwSjZSxugVVHH9jnSzEjONkJaSKmi4tlidk6zkF1-uY-TPJkKxYN_9ar7BgLshF9JGXk7t8OZhxSCxDZc-smntu6ORFDl4gRZZVBKXhqOGjOAdYX5tPiGDF2Bxb68RSzh9Xyr5PXxKLx5yivZzUdo0-GFHo13V9w6a5VQM4R1w4_ro8jF8WAo3mpGZ_ovx_U5IY6zMNmi_AoA1mUyvTGotgcu94RragutoJRxAGHbNJZ0Q
```

### <a name="azure-service-catalog"></a>Azure サービス カタログ

すべての Azure サービスは、Azure Storage Blob の CSV 形式のカタログに記載されています。 このカタログは、お使いのシステムのすべての Azure サービスのキュレーション カタログを作成する必要がある場合に便利です。 現在のカタログは、[https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv) にあります。

### <a name="csv-data-file-details"></a>CSV データ ファイルの詳細

ここでは、API レポートのプロパティについて説明します。

#### <a name="usage-summary"></a>使用状況の概要

JSON 形式は CSV レポートから生成されます。 そのため、形式は概要の CSV 形式と同じになります。 列名が使用されるため、JSON 概要データを使用するときはデータ テーブルに逆シリアル化する必要があります。

| CSV の列名 | JSON の列名 | JSON の新しい列 | Comment (コメント) |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| アカウント名 | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| サブスクリプション名 | SubscriptionName | SubscriptionName |   |
| Date | Date | Date | サービス カタログ レポートが実行された日付を示します。 形式は、タイム スタンプのない日付文字列です。 |
| 月 | 月 | 月 |   |
| 日 | 日 | 日 |   |
| 年 | 年 | 年 |   |
| Product | BillableItemName | Product |   |
| 測定 ID | ResourceGUID | MeterId |   |
| 測定カテゴリ | Service | MeterCategory | サービスを検索する際に役立ちます。 複数の ServiceType を持つサービスに関連します。 例: Virtual Machines。 |
| 測定サブカテゴリ | ServiceType | MeterSubCategory | サービスの 2 番目の詳細レベルを提供します。 例: A1 VM (Windows 以外)。  |
| 測定リージョン | ServiceRegion | MeterRegion | サービスに必要な 3 番目の詳細レベル。 ResourceGUID のリージョン コンテキストを検索する際に役立ちます。 |
| 測定名 | ServiceResource | MeterName | サービスの名前。 |
| Consumed | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| リソースの場所 | ServiceSubRegion | ResourceLocation |   |
| 使用サービス | ServiceInfo | ConsumedService |   |
| インスタンス ID | コンポーネント | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo: | AdditionalInfo: | AdditionalInfo: |   |
| Tags | Tags | Tags |   |
| Store Service Identifier   | OrderNumber | StoreServiceIdentifier   |   |
| Department Name | DepartmentName | DepartmentName |   |
| コスト センター | CostCenter | CostCenter |   |
| 計算単位 | UnitOfMeasure | UnitOfMeasure | サンプル値: 時間、GB、イベント、プッシュ、ユニット、ユニット時間、MB、日割りユニット |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Azure Marketplace レポート

| CSV の列名 | JSON の列名 | JSON の新しい列 |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| アカウント名 | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| サブスクリプション名 | SubscriptionName |  SubscriptionName |
| Date | BillingCycle |  日付 (日付文字列のみ。 タイム スタンプなし)
| 月 | 月 |  月 |
| 日 | 日 |  日 |
| 年 | 年 |  年 |
| 測定 ID | MeterResourceId |  MeterId |
| 発行元の名前 | PublisherFriendlyName |  発行元 |
| プラン名 | OfferFriendlyName |  OfferName |
| Plan Name | PlanFriendlyName |  PlanName |
| Consumed | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| 計算単位 | UnitOfMeasure | UnitOfMeasure |
| インスタンス ID | InstanceId | InstanceId |
| 追加情報 | AdditionalInfo: | AdditionalInfo: |
| Tags | Tags | Tags |
| Order Number | OrderNumber | OrderNumber |
| Department Name | DepartmentNames | DepartmentName |
| コスト センター | CostCenters |  CostCenter |
| リソース グループ | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Price Sheet

| CSV の列名 | JSON の列名 | Comment (コメント) |
| --- | --- | --- |
| Service | Service |  価格に変更はありません |
| 計算単位 | UnitOfMeasure |   |
| Overage Part Number | ConsumptionPartNumber |   |
| Overage Unit Price | ConsumptionPrice |   |
| 通貨コード | CurrencyCode |     |

### <a name="common-api-issues"></a>API に関する一般的な問題

Azure Enterprise REST API を使用すると、次のような一般的な問題が発生する可能性があります。

適切な承認の種類がない API キーを使用しようとしている可能性があります。 API キーは次のユーザーによって生成されます。

- エンタープライズ管理者
- 部門管理者 (DA)
- アカウント所有者 (AO)

EA 管理者によって生成されたキーを使用すると、その登録に関するすべての情報にアクセスできます。 読み取り専用の EA 管理者は API キーを生成できません。

DA または AO によって生成されたキーでは、残高、料金、価格シートの各情報にはアクセスできません。

API キーは 6 か月ごとに有効期限が切れます。 期限切れになった場合は、再生成する必要があります。

タイムアウト エラーが発生した場合は、タイムアウトしきい値の上限を増やすことで解決できます。

401 (未承認) 期限切れエラーが発生することがあります。 このエラーは、通常、期限切れのキーが原因で発生します。 キーの有効期限が切れている場合は、キーを再生成することができます。

選択した日付範囲に使用できる最新データがない場合、API 呼び出しから 400 および 404 (利用不可) エラーが返されることがあります。 たとえば、登録の転送が最近開始されたために、このエラーが発生することがあります。 特定の日付以降のデータは、新しい登録に格納されます。 それ以外に、新しい登録番号を使用して古い登録に存在する情報を取得しようとすると、エラーが発生することがあります。

## <a name="next-steps"></a>次の手順

- Azure EA Portal の管理者は「[Azure EA Portal の管理](billing-ea-portal-administration.md)」を読んで、一般的な管理タスクについて学習する必要があります。
- Azure EA Portal の問題のトラブルシューティングに関するヘルプが必要な場合は、「[Troubleshoot Azure EA portal access (Azure EA Portal へのアクセスのトラブルシューティング)](billing-ea-portal-troubleshoot.md)」を参照してください。
