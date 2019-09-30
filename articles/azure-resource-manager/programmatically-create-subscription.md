---
title: Azure Enterprise サブスクリプションをプログラムで作成 | Microsoft Docs
description: 追加の Azure Enterprise または Enterprise Dev/Test サブスクリプションをプログラムで作成する方法について説明します。
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 755eabe97508b403205ff04a8d2d35feee314eb9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258925"
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

## <a name="resttabrest"></a>[REST](#tab/rest)

自分がアクセスできるすべての登録アカウントを一覧表示するための要求:

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

`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 そのアカウントの `name` をコピーします。 たとえば、登録アカウント SignUpEngineering@contoso.com でサブスクリプションを作成する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 この識別子は登録アカウントのオブジェクト ID です。 次のステップでこの値を `enrollmentAccountObjectId` として使用できるよう、どこかに貼り付けておきます。

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[[Azure Cloud Shell]](https://shell.azure.com/) を開き、[PowerShell] を選択します。

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) コマンドレットを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

自分がアクセスできる登録アカウントの一覧が Azure から返されます。

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 そのアカウントの `ObjectId` をコピーします。 たとえば、登録アカウント SignUpEngineering@contoso.com でサブスクリプションを作成する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 次のステップで `enrollmentAccountObjectId` としてこのオブジェクト ID を使用できるよう、どこかに貼り付けておきます。

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) コマンドを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurecli-interactive 
az billing enrollment-account list
```

自分がアクセスできる登録アカウントの一覧が Azure から返されます。

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 そのアカウントの `name` をコピーします。 たとえば、登録アカウント SignUpEngineering@contoso.com でサブスクリプションを作成する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 この識別子は登録アカウントのオブジェクト ID です。 次のステップでこの値を `enrollmentAccountObjectId` として使用できるよう、どこかに貼り付けておきます。

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>特定の登録アカウントの中にサブスクリプションを作成する

次の例では、前のステップで選択した登録アカウントに *Dev Team Subscription* という名前のサブスクリプションを作成します。 サブスクリプション オファーは *MS-AZR-0017P* (通常のマイクロソフトエンタープライズ契約) です。 また、必要に応じて RBAC 所有者として 2 人のユーザーをサブスクリプションに追加することもできます。

# <a name="resttabrest"></a>[REST](#tab/rest)

次の要求を実行します。その際、`<enrollmentAccountObjectId>` を最初のステップでコピーした `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) と置き換えます。 所有者を指定する場合、[ユーザー オブジェクト ID の取得方法](grant-access-to-create-subscription.md#userObjectId)をご確認ください。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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
| `displayName` | いいえ      | string | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offerType`   | はい      | string | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `owners`      | いいえ       | string | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |

監視対象の `subscriptionOperation` オブジェクトが返されます。 サブスクリプションの作成が完了すると、`subscriptionOperation` オブジェクトから、サブスクリプション ID を持つ `subscriptionLink` オブジェクトが返されます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

まず、`Install-Module Az.Subscription -AllowPrerelease` を実行してこのプレビュー モジュールをインストールします。 `-AllowPrerelease` が正しく動作することを確認するには、[Get PowerShellGet Module](/powershell/gallery/installing-psget) から最新バージョンの PowerShellGet をインストールします。

次の [New-AzSubscription](/powershell/module/az.subscription) コマンドを実行します。その際、`<enrollmentAccountObjectId>` を最初のステップで収集した `ObjectId` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) で置き換えます。 所有者を指定する場合、[ユーザー オブジェクト ID の取得方法](grant-access-to-create-subscription.md#userObjectId)をご確認ください。

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 要素名  | 必須 | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | いいえ      | string | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `OfferType`   | はい      | string | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `EnrollmentAccountObjectId`      | はい       | string | サブスクリプションが作成された、請求先の登録アカウントのオブジェクト ID。 この値は、`Get-AzEnrollmentAccount` から取得する GUID です。 |
| `OwnerObjectId`      | いいえ       | string | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `OwnerSignInName`    | いいえ       | string | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。|
| `OwnerApplicationId` | いいえ       | string | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。 このパラメーターを使用している場合、サービス プリンシパルには[ディレクトリへの読み取りアクセス権](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)が必要です。| 

すべてのパラメーターの完全な一覧については、[New-AzSubscription](/powershell/module/az.subscription) を参照してください。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、`az extension add --name subscription` を実行してこのプレビュー拡張機能をインストールします。

次の [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) コマンドを実行します。その際、`<enrollmentAccountObjectId>` を最初のステップでコピーした `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) で置き換えます。 所有者を指定する場合、[ユーザー オブジェクト ID の取得方法](grant-access-to-create-subscription.md#userObjectId)をご確認ください。

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 要素名  | 必須 | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | いいえ      | string | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offer-type`   | はい      | string | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `enrollment-account-object-id`      | はい       | string | サブスクリプションが作成された、請求先の登録アカウントのオブジェクト ID。 この値は、`az billing enrollment-account list` から取得する GUID です。 |
| `owner-object-id`      | いいえ       | string | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `owner-upn`    | いいえ       | string | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `owner-object-id` の代わりにこのパラメーターを使用することができます。|
| `owner-spn` | いいえ       | string | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `owner-object-id` の代わりにこのパラメーターを使用することができます。 このパラメーターを使用している場合、サービス プリンシパルには[ディレクトリへの読み取りアクセス権](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)が必要です。| 

すべてのパラメーターの完全な一覧については、[az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) を参照してください。

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure Enterprise のサブスクリプション作成 API の制限事項

- この API を使用して作成できるのは、Azure Enterprise サブスクリプションのみです。
- 登録アカウントあたり作成できるサブスクリプションの上限数は 200 です。 その後、アカウントの追加のサブスクリプションは、アカウント センターからのみ作成することができます。 API を使用してさらにサブスクリプションを作成する場合は、別の登録アカウントを作成します。
- アカウント所有者ではないが、RBAC 経由で登録アカウントに追加されたユーザーは、アカウント センターを使用してサブスクリプションを作成できません。
- サブスクリプションが作成されるテナントを選択することはできません。 サブスクリプションは常に、アカウント所有者のホーム テナント内に作成されます。 サブスクリプションを別のテナントに移動する場合は、[テナントのサブスクリプションの変更](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* .NET を使用してサブスクリプションを作成する例については、[GitHub のサンプル コード](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)を参照してください。
* サブスクリプションを作成し終えたら、他のユーザーおよびサービス プリンシパルでその機能を利用できるようになります。 詳細については、「[Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)](grant-access-to-create-subscription.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法については、「[Azure 管理グループのリソースを整理する](management-groups-overview.md)」を参照してください。
