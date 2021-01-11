---
title: Microsoft 顧客契約の Azure クレジット残高を追跡する
description: Microsoft 顧客契約の Azure クレジット残高を追跡する方法を説明します。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2019
ms.author: banders
ms.openlocfilehash: 334193f64c49b546bab5f44840df5b3f59e32c4f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690580"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Microsoft 顧客契約の Azure クレジット残高を追跡する

Microsoft 顧客契約の課金アカウントの Azure クレジット残高は、Azure portal または REST API で確認することができます。

Microsoft 顧客契約の課金アカウントでは、クレジットが課金プロファイルに割り当てられます。 それぞれの課金プロファイルには、その請求書上の料金に自動的に適用される固有のクレジットが存在します。 課金プロファイルの Azure クレジット残高を表示するには、課金プロファイルの所有者、共同作成者、閲覧者、請求書管理者のいずれかのロール、または課金アカウントの所有者、共同作成者、閲覧者のいずれかのロールが必要となります。 ロールの詳細については、「[Azure での Microsoft 顧客契約の管理ロールを理解する](understand-mca-roles.md)」を参照してください。

> [!NOTE]
> 新しいクレジットが Azure portal に表示されるまでに最長で 24 時間かかることがあります。 新しいクレジットを取得してもポータルに表示されない場合、24 時間後にはクレジットが表示されます。

この記事では、Microsoft 顧客契約の課金アカウントについて説明します。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance"></a>クレジット残高を確認する

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

    ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. クレジット残高を追跡する課金アカウントを [課金スコープ] ページで選択します。 課金アカウントの種類は、**Microsoft 顧客契約**である必要があります。

    ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > ユーザーが最近アクセスした課金スコープは、Azure portal によって記憶されます。次回 [コストの管理と請求] ページにアクセスすると、そのスコープが表示されます。 過去に [コストの管理と請求] にアクセスしたことがなければ、課金スコープ ページは表示されません。 その場合は、[適切なスコープ](#check-access-to-a-microsoft-customer-agreement)になっていることを確認してください。 適切でない場合は、[スコープを切り替え](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)て Microsoft 顧客契約の課金アカウントを選択します。

3. 左側から **[支払い方法]** を選択し、 **[Azure クレジット]** を選択します。

   ![課金プロファイルのクレジット残高のスクリーンショット](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. [Azure クレジット] ページには、次のセクションがあります。

   #### <a name="balance"></a>Balance

   残高セクションには、Azure クレジット残高の概要が表示されます。

   ![課金プロファイルのクレジット残高のスクリーンショット](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | 期間               | 定義                           |
   |--------------------|--------------------------------------------------------|
   | 推定残高  | すべての課金トランザクションと保留中トランザクションを考慮した後の、クレジットの推定金額 |
   | 現在の残高    | 前回請求時点でのクレジットの金額。 保留中のトランザクションは含まれません |

   推定残高が 0 になると、クレジット対象製品も含めて、すべての使用料がお客様に請求されます。

   #### <a name="credits-list"></a>クレジットの一覧

   クレジットの一覧セクションには、Azure クレジットが一覧表示されます。

   ![課金プロファイルのクレジット一覧のスクリーンショット](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | 期間 | 定義 |
   |---|---|
   | source | クレジット取得の基になったもの |
   | 開始日 | クレジットを取得した日付 |
   | 有効期限 | クレジットの有効期限が切れる日付 |
   | 現在の残高 | 前回の請求時点での残高 |
   | 元の金額 | クレジットの元の金額 |
   | Status | クレジットの現在の状態。 状態は、アクティブ、使用済み、期限切れ、または期限切れ間近です。 |

   #### <a name="transactions"></a>トランザクション

   取引セクションには、クレジット残高に関係したすべての取引が表示されます。

   ![課金プロファイルのクレジット取引のスクリーンショット](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | 期間 | 定義 |
   |---|---|
   | トランザクション日時 | 取引が発生した日付 |
   | 説明 | 取引の説明 |
   | 金額| トランザクションの金額 |
   | Balance | 取引後の残高 |

    > [!NOTE]
    >
    > 支払い方法のページに Azure クレジットが表示されない場合、クレジットをお持ちでないか、または適切なスコープが選択されていません。 クレジットがある課金アカウントまたはそのいずれかの課金プロファイルを選択してください。 スコープの変更方法については、[Azure portal での課金スコープの切り替え](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)に関するセクションを参照してください。

5. 課金アカウント スコープで Azure クレジットを表示していて、なおかつ課金アカウントに複数の課金プロファイルが存在する場合、Azure クレジット ページには、課金プロファイルごとに Azure クレジットの概要を記載したテーブルが表示されます。 一覧から課金プロファイルを選択し、支払い方法と Azure クレジットを順に選択すると、課金プロファイルの詳細が表示されます。

    ![課金アカウントのクレジット一覧のスクリーンショット](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[REST API](#tab/rest)

[Azure Billing](https://docs.microsoft.com/rest/api/billing/) API と [Consumption](https://docs.microsoft.com/rest/api/consumption/) API を使用すると、課金アカウントのクレジット残高をプログラムから取得できます。

次に示す例では、REST API を使用します。 現時点では、PowerShell と Azure CLI はサポートされていません。

### <a name="find-billing-profiles-you-have-access-to"></a>アクセスできる課金プロファイルを検索する

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
課金アカウントとその課金プロファイルのリストが API 応答で返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

クレジット残高の確認対象となる課金プロファイルは、課金プロファイルの `displayName` プロパティを使用して特定します。 該当する課金プロファイルの `id` をコピーしてください。 たとえば、**Development** という課金プロファイルのクレジット残高を確認したければ、```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx``` をコピーします。 次のステップで使用できるように、この値をどこかに貼り付けておきます。

### <a name="get-azure-credit-balance"></a>Azure クレジット残高を取得する

次の要求を実行します。その際、`<billingProfileId>` を最初のステップでコピーした `id` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```) と置き換えます。

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

API 応答では、課金プロファイルの推定残高と現在の残高が返されます。

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| 要素名  | 説明                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | 請求済みの取引と保留中の取引をすべて考慮した後の、クレジットの推定金額。 |
| `currentBalance`   | 前回請求時点でのクレジットの金額。 保留中の取引は含まれません。    |
| `pendingCreditAdjustments`      | 未請求となっている払戻などの調整。  |
| `expiredCredit`      |  前回の請求以降に有効期限が切れたクレジット。  |
| `pendingEligibleCharges`  | 未請求となっているクレジット対象料金。   |

### <a name="get-list-of-credits"></a>クレジットの一覧を取得する

次の要求を実行します。その際、`<billingProfileId>` を最初のステップでコピーした `id` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```) と置き換えます。

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
課金プロファイルの Azure クレジットのリストが API 応答で返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| 要素名  | 説明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | クレジットの元の金額。 |
| `closedBalance`   | 前回の請求時点での残高。    |
| `source`      | クレジットをだれがどのように取得したかを定義するソース。 |
| `startDate`      |  クレジットが有効になった日付。  |
| `expirationDate`  | クレジットの有効期限が切れる日付。   |
| `poNumber`  | クレジットが請求された請求書の発注番号。   |

### <a name="get-transactions-that-affected-credit-balance"></a>クレジット残高に影響を与えた取引を取得する

次の要求を実行します。その際、`<billingProfileId>` を最初のステップでコピーした `id` (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```) と置き換えます。 目的とする期間の取引を取得するには、**startDate** と **endDate** を渡す必要があります。

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
課金プロファイルのクレジット残高に影響を与えたすべての取引が API 応答で返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| 要素名  | 説明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | 取引が発生した日付。 |
| `description` | 取引の説明。 |
| `adjustments`   | 取引のクレジット調整。    |
| `creditExpired`      | 有効期限が切れたクレジットの金額。 |
| `charges`      |  取引の請求金額。  |
| `closedBalance`  | 取引後の残高。   |
| `eventType`  | トランザクションの種類。   |
| `invoiceNumber`  | 取引の請求先となる請求書の請求書番号。 保留中の取引では空になります。   |

---

## <a name="how-credits-are-used"></a>クレジットの使用方法

Microsoft 顧客契約の請求先アカウントでは、請求書および支払い方法を管理するために課金プロファイルを使用します。 課金プロファイルに対して毎月の請求書が生成され、支払い方法を使用して請求書に対する支払いを行います。

取得したクレジットは、課金プロファイルに割り当てます。 課金プロファイルに対して請求書が生成されるときは、請求金額の合計にクレジットが自動的に適用されて、支払う必要のある金額が計算されます。 小切手や電信送金、クレジット カードなどの支払い方法を使用して、残りの金額を支払うことになります。

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure クレジットの対象ではない製品

 次の製品は、Azure クレジットの対象ではありません。 クレジット残高に関係なく、これらの製品を使用した場合はお客様に対して請求されます。

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- 登録ユーザー
- OpenLogic
- Remote Access Rights XenApp Essentials 登録ユーザー
- Ubuntu Advantage
- Visual Studio Enterprise (月払い)
- Visual Studio Enterprise (年払い)
- Visual Studio Professional (月払い)
- Visual Studio Professional (年払い)
- Azure Marketplace 製品
- Azure のサポート プラン

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

- [Microsoft 顧客契約の請求先アカウントについて理解する](../understand/mca-overview.md)
- [Microsoft 顧客契約の請求書の用語を理解する](../understand/mca-understand-your-invoice.md)
