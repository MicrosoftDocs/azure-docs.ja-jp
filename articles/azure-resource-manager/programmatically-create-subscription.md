---
title: Azure Enterprise サブスクリプションをプログラムで作成 | Microsoft Docs
description: 追加の Azure Enterprise または Enterprise Dev/Test サブスクリプションをプログラムで作成する方法について説明します。
services: azure-resource-manager
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: tomfitz
ms.openlocfilehash: 93df0c196d78a4685ff82108354b82a07d67695d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256925"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Azure Enterprise サブスクリプションをプログラムで作成する (プレビュー)

[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) を契約されているお客様の場合、EA (MS-AZR-0017P) および EA Dev/Test (MS-AZR-0148P) サブスクリプションをプログラミングによって作成できます。 この記事では、Azure Resource Manager を使用してプログラムからサブスクリプションを作成する方法について説明します。

この API から Azure サブスクリプションを作成した場合は、お客様が Microsoft または正規の販売代理店と締結した Microsoft Azure サービスの契約によって、サブスクリプションが統制されます。 詳細については、「[Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal/)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

サブスクリプションを作成する登録アカウントの所有者ロールが必要です。 これらの役割を取得する方法は 2 つあります。

* 登録管理者は、登録アカウントの所有者である[アカウント所有者](https://ea.azure.com/helpdocs/addNewAccount) (サインインが必要) にユーザーを設定できます。 受信した登録手続きのメールに記載されている指示に従って、初期サブスクリプションを手動で作成します。 次の手順に進む前に、アカウント所有権を確認し、初期 EA サブスクリプションを手動で作成します。 登録にアカウントを追加するだけでは不十分です。

* 登録アカウントの既存の所有者が、[アクセス許可を付与](grant-access-to-create-subscription.md)できます。 同様に、サービス プリンシパルを使用して EA サブスクリプションを作成する場合は、[そのサービス プリンシパルにサブスクリプションを作成する権限を付与](grant-access-to-create-subscription.md)する必要があります。

## <a name="find-accounts-you-have-access-to"></a>アクセスできるアカウントを検索します。

アカウント所有者として Azure EA 登録に追加されると、アカウントと登録の関係を使用してサブスクリプション料金の請求先が決定されます。 このアカウント内で作成されたすべてのサブスクリプションは、アカウントが含まれる EA 登録に課金されます。 サブスクリプションを作成するには、サブスクリプションを所有するための登録アカウントおよびユーザー プリンシパルに関する値を受け渡す必要があります。 

次のコマンドを実行するには、サブスクリプションが既定で作成されるディレクトリである、アカウント所有者の*ホーム ディレクトリ*にログインしている必要があります。

# <a name="resttabrest"></a>[REST](#tab/rest)

すべての登録アカウントを一覧表示することを要求する:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

自分がアクセスできるすべての登録アカウントの一覧が Azure から返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) コマンドレットを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

アカウントのオブジェクト ID とメール アドレスの一覧が Azure から返されます。

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) コマンドを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurecli-interactive 
az billing enrollment-account list
```

アカウントのオブジェクト ID とメール アドレスの一覧が Azure から返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 次の手順で、`id` を `enrollmentAccount` 値として使用して、サブスクリプションを作成します。

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>特定の登録アカウントの中にサブスクリプションを作成する 

次の例では、*開発チーム サブスクリプション*という名前で、サブスクリプション オファー *MS-AZR-0017P* (正規 EA) のサブスクリプションを作成する要求を作成します。 登録アカウントは `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (プレースホルダーの値、この値は GUID です) で、これは SignUpEngineering@contoso.com の登録アカウントです。 また、必要に応じて RBAC 所有者として 2 人のユーザーをサブスクリプションに追加することもできます。

# <a name="resttabrest"></a>[REST](#tab/rest)

サブスクリプションを作成する要求のパスに、`enrollmentAccount` の`id` を使用します。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 要素名  | 必須 | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | いいえ       | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offerType`   | はい      | String | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `owners`      | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |

監視対象の `subscriptionOperation` オブジェクトが返されます。 サブスクリプションの作成が完了すると、`subscriptionOperation` オブジェクトから、サブスクリプション ID を持つ `subscriptionLink` オブジェクトが返されます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

このプレビュー モジュールを使用するには、まず `Install-Module Az.Subscription -AllowPrerelease` を実行してモジュールをインストールします。 `-AllowPrerelease` が正しく動作することを確認するには、[Get PowerShellGet Module](/powershell/gallery/installing-psget) から最新バージョンの PowerShellGet をインストールします。

新しいサブスクリプションを作成するには、`enrollmentAccount` オブジェクト ID を パラメーター `EnrollmentAccountObjectId` に設定し、[New-AzSubscription](/powershell/module/az.subscription) を使用します。 

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| 要素名  | 必須 | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | いいえ       | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `OfferType`   | はい      | String | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `EnrollmentAccountObjectId`      | はい       | String | サブスクリプションが作成された、請求先の登録アカウントのオブジェクト ID。 この値は、`Get-AzEnrollmentAccount` から取得する GUID です。 |
| `OwnerObjectId`      | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `OwnerSignInName`    | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。|
| `OwnerApplicationId` | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。 このパラメーターを使用している場合、サービス プリンシパルには[ディレクトリへの読み取りアクセス権](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)が必要です。| 

すべてのパラメーターの完全な一覧については、[New-AzSubscription](/powershell/module/az.subscription.preview) を参照してください。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

このプレビュー拡張機能を使用するには、まず `az extension add --name subscription` を実行して拡張機能をインストールします。

新しいサブスクリプションを作成するには、`enrollmentAccount` オブジェクト ID をパラメーター `enrollment-account-object-id` に設定し、[az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) を使用します。

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 要素名  | 必須 | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | いいえ       | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offer-type`   | はい      | String | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `enrollment-account-object-id`      | はい       | String | サブスクリプションが作成された、請求先の登録アカウントのオブジェクト ID。 この値は、`az billing enrollment-account list` から取得する GUID です。 |
| `owner-object-id`      | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `owner-upn`    | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `owner-object-id` の代わりにこのパラメーターを使用することができます。|
| `owner-spn` | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `owner-object-id` の代わりにこのパラメーターを使用することができます。 このパラメーターを使用している場合、サービス プリンシパルには[ディレクトリへの読み取りアクセス権](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)が必要です。| 

すべてのパラメーターの完全な一覧については、[az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) を参照してください。

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure Enterprise のサブスクリプション作成 API の制限事項

- この API を使用して作成できるのは、Azure Enterprise サブスクリプションのみです。
- アカウントあたり作成できるサブスクリプションの上限数は 50 です。 50 個を超えるサブスクリプションは、アカウント センターを使用してのみ作成できます。
- アカウント内に 1 つ以上の EA または EA Dev/Test サブスクリプションがある必要があり、つまり、アカウント所有者が 1 回以上手動でサインアップしている必要があります。
- アカウント所有者ではないが、RBAC 経由で登録アカウントに追加されたユーザーは、アカウント センターを使用してサブスクリプションを作成できません。
- サブスクリプションが作成されるテナントを選択することはできません。 サブスクリプションは常に、アカウント所有者のホーム テナント内に作成されます。 サブスクリプションを別のテナントに移動する場合は、[テナントのサブスクリプションの変更](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* .NET を使用してサブスクリプションを作成する例については、[GitHub のサンプル コード](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)を参照してください。
* サブスクリプションを作成し終えたら、他のユーザーおよびサービス プリンシパルでその機能を利用できるようになります。 詳細については、「[Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)](grant-access-to-create-subscription.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法については、「[Azure 管理グループのリソースを整理する](management-groups-overview.md)」を参照してください。
