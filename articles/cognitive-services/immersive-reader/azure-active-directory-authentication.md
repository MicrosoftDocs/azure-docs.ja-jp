---
title: Azure Active Directory (Azure AD) 認証
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム サブドメインを含む新しいイマーシブ リーダー リソースを作成した後、Azure テナントで Azure AD を構成する方法について説明します。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: e4b792a04b4926fdb56f37c089e73b90cde905d3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990141"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>イマーシブ リーダー サービスで Azure Active Directory (Azure AD) 認証を使用する

以降のセクションでは、Azure Cloud Shell 環境または Azure CLI を使用して、カスタム サブドメインを含む新しいイマーシブ リーダー リソースを作成し、Azure テナントで Azure AD を構成します。 最初の構成が完了したら、Azure AD を呼び出してアクセス トークンを取得します。これは、Immersive Reader SDK を使用する場合の方法と似ています。 問題が発生した場合は、各セクションに、Azure CLI の各コマンドで使用可能なすべてのオプションへのリンクが提供されています。

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>カスタム サブドメインを含むイマーシブ リーダー リソースを作成する

1. まず、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を開きます。 [サブスクリプションを選択](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description)します。

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. 次に、[カスタム サブドメインを含むイマーシブ リーダー リソースを作成](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)します。

   >[!NOTE]
   > サブドメイン名は、launchAsync 関数でリーダーを起動するときに Immersive Reader SDK で使用されます。

   -SkuName は F0 (Free レベル) または S0 (Standard レベル、パブリック プレビュー期間中も無料) に設定できます。 S0 レベルの方が呼び出しレートの上限が高く、呼び出し回数に月単位のクォータはありません。

   -Location は次のいずれかに設定できます: `eastus`、`westus`、`australiaeast`、`centralindia`、`japaneast`、`northeurope`、`westeurope`

   -CustomSubdomainName は、グローバルに一意である必要があります。また、"."、"!"、"," などの特殊文字を含めることはできません。


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   成功すると、リソース **エンドポイント**にリソースに固有のサブドメイン名が表示されます。

   ここでは、新しく作成されたリソース オブジェクトを **$resource** 変数にキャプチャします。これは、後でこのリソースへのアクセスを許可するときに使用されるためです。


   >[!NOTE]
   > Azure portal でリソースを作成する場合、リソース "Name" がカスタム サブドメインとして使用されます。 ポータルでサブドメイン名を確認するには、リソースの概要ページに移動し、そこで列挙されているエンドポイント内のサブドメインを検索します。たとえば、`https://[SUBDOMAIN].cognitiveservices.azure.com/` のようになります。 SDK と統合するためのサブドメインを取得する必要がある場合は、後からここで確認することもできます。

   リソースがポータルで作成された場合、今すぐ[既存のリソースを取得](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0)することもできます。

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>サービス プリンシパルにロールを割り当てる

これで、カスタム サブドメインがリソースに関連付けられたので、ロールをサービス プリンシパルに割り当てる必要があります。

1. まず、[Azure AD アプリケーションを作成](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)しましょう。

   >[!NOTE]
   > "クライアント シークレット" とも呼ばれるパスワードは、認証トークンを取得するときに使用されます。

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   ここでは、次の手順で使用するために、新しく作成した Azure AD アプリ オブジェクトを **$aadApp** 変数にキャプチャします。

2. 次に、Azure AD アプリケーションの[サービス プリンシパルを作成](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)する必要があります。

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   ここでは、次の手順で使用するために、新しく作成したサービス プリンシパル オブジェクトを **$principal** 変数にキャプチャします。


3. 最後の手順では、(リソースにスコープが設定された) サービス プリンシパルに ["Cognitive Services ユーザー" ロールを割り当て](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)ます。 ロールを割り当てることにより、このリソースにサービス プリンシパル アクセス権を付与します。 同じサービス プリンシパル アクセスをサブスクリプション内の複数のリソースに対して許可できます。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > 作成するイマーシブ リーダー リソースごとに、この手順を実行する必要があります。 たとえば、異なるグローバル リージョン用に複数のリソースを作成する場合は、すべてのリージョンで Azure AD 認証が機能するように、これらの各リージョン リソースに対してこの手順を実行する必要があります。 または、後で新しいリソースを作成する場合は、その新しいリソースに対してもこのロール割り当て手順を実行する必要があります。


## <a name="obtain-an-azure-ad-token"></a>Azure AD トークンを取得する

この例では、パスワードを使用してサービス プリンシパルを認証し、Azure AD トークンを取得します。

1. **TenantId** を取得します。
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. トークンを取得します。
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > Immersive Reader SDK はトークンの AccessToken プロパティを使用します (例: $token.AccessToken)。 詳細については、SDK [リファレンス](reference.md)およびコード [サンプル](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples)を参照してください。

または、証明書を使用してサービス プリンシパルを認証することもできます。 サービス プリンシパルに加えて、別の Azure AD アプリケーションから委任されたアクセス許可を持つことで、ユーザー プリンシパルもサポートされます。 この場合、パスワードまたは証明書の代わりに、ユーザーはトークンを取得するときに 2 要素認証が求められます。

## <a name="next-steps"></a>次の手順

* [Node.js チュートリアル](./tutorial-nodejs.md)で、Node.js と Immersive Reader SDK を使用して他にできることを確認する
* [Python チュートリアル](./tutorial-python.md)で、Python と Immersive Reader SDK を使用して他にできることを確認する
* [Swift チュートリアル](./tutorial-ios-picture-immersive-reader.md)で、Swift と Immersive Reader SDK を使用して他にできることを確認する
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する