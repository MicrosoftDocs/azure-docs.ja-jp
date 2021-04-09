---
title: マネージド ID
description: マネージド ID が Azure App Service と Azure Functions でどのように機能するのか、およびマネージド ID を構成してバックエンド リソースのトークンを生成するにはどのようにするのかについて説明します。
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 16cd4685f513eb628372802cc158195b81bce72a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736173"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>App Service と Azure Functions でマネージド ID を使用する方法

このトピックでは、App Service と Azure Functions アプリケーションでマネージド ID を作成し、それを使用して他のリソースにアクセスする方法を説明します。 

> [!Important] 
> ご自分のアプリがサブスクリプションやテナント間で移行されている場合、App Service と Azure Functions のマネージド ID は、予期どおりには動作しません。 アプリ用には、新規で ID を取得する必要があります。これには、この機能を無効にしてから再度有効にする必要があります。 以下の「[ID の削除](#remove)」を参照してください。 また、ダウンストリーム リソースでも新しい ID が使用されるように、アクセス ポリシーを更新する必要があります。

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する

システム割り当て ID を持つアプリを作成するには、アプリケーションで追加のプロパティを設定する必要があります。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

ポータルでマネージド ID を設定するには、最初に通常の方法でアプリケーションを作成した後、機能を有効にします。

1. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。

2. 関数アプリを使っている場合は、 **[プラットフォーム機能]** に移動します。 他のアプリの種類の場合は、左側のナビゲーションを下にスクロールして **[設定]** グループに移動します。

3. **[ID]** を選択します。

4. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。 **[保存]** をクリックします。

    ![[状態] を [オン] に切り替えて [保存] を選択する場所を示すスクリーンショット。](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Azure portal で Web アプリまたはスロット アプリのマネージド ID を検索するには、 **[エンタープライズアプリケーション]** の下にある **[ユーザー設定]** セクションを確認します。 通常、スロット名は `<app name>/slots/<slot name>` に似ています。


### <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure CLI を使用してマネージド ID を設定するには、既存のアプリケーションに対して `az webapp identity assign` コマンドを使用する必要があります。 このセクションの例を実行するためのオプションとして次の 3 つがあります。

- Azure Portal から [Azure Cloud Shell](../cloud-shell/overview.md) を使用する。
- 以下の各コード ブロックの右上隅にある [使ってみる] ボタンを利用して、埋め込まれた Azure Cloud Shell を使用します。
- ローカル CLI コンソールを使用する場合、[Azure CLI の最新バージョン (2.0.31 以降) をインストール](/cli/azure/install-azure-cli)します。 

次の手順では、CLI を使用して、Web アプリを作成し、ID を割り当てる方法について説明します。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 次のように、アプリケーションのデプロイに使用する Azure サブスクリプションに関連付けられているアカウントを使用します。

    ```azurecli-interactive
    az login
    ```

2. CLI を使用して Web アプリケーションを作成します。 App Service で CLI を使用する方法の他の例については、[App Service の CLI のサンプル](../app-service/samples-cli.md)に関するページを参照してください。

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. `identity assign` コマンドを実行してこのアプリケーションの ID を作成します。

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の手順では、Azure PowerShell を使用して、アプリを作成し、ID を割り当てる方法について説明します。 Web アプリを作成する手順と、関数アプリを作成する手順は異なります。

#### <a name="using-azure-powershell-for-a-web-app"></a>Web アプリに Azure PowerShell を使用する

1. 必要に応じて、[Azure PowerShell ガイド](/powershell/azure/)の手順に従って Azure PowerShell をインストールし、`Login-AzAccount` を実行して、Azure との接続を作成します。

2. Azure PowerShell を使用して Web アプリケーションを作成します。 App Service で Azure PowerShell を使用する方法の他の例については、[App Service の PowerShell のサンプル](../app-service/samples-powershell.md)に関するページを参照してください。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. `Set-AzWebApp -AssignIdentity` コマンドを実行してこのアプリケーションの ID を作成します。

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>関数アプリに Azure PowerShell を使用する

1. 必要に応じて、[Azure PowerShell ガイド](/powershell/azure/)の手順に従って Azure PowerShell をインストールし、`Login-AzAccount` を実行して、Azure との接続を作成します。

2. Azure PowerShell を使用して関数アプリを作成します。 Azure Functions で Azure PowerShell を使用する方法の他の例については、[Az.Functions リファレンス](/powershell/module/az.functions/#functions)を参照してください。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

代わりに `Update-AzFunctionApp` を使用して、既存の関数アプリを更新することもできます。

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 App Service および Functions へのデプロイについて詳しくは、「[Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md)」(App Service でのリソースのデプロイの自動化) および「[Azure Functions の関数アプリのリソース デプロイを自動化](../azure-functions/functions-infrastructure-as-code.md)」をご覧ください。

種類が `Microsoft.Web/sites` であるすべてのリソースは、リソース定義に次のプロパティを含めることにより、ID を使って作成できます。

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> アプリケーションは、システム割り当て ID とユーザー割り当て ID の両方を同時に持つことができます。 この場合、`type` プロパティは `SystemAssigned,UserAssigned` になります

システム割り当てタイプを追加すると、Azure に対してアプリケーション用の ID を作成して管理するように指示されます。

たとえば、Web アプリは次のようになります。

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

作成されるサイトには、次の追加プロパティが設定されます。

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

tenantId プロパティは、その ID が属する Azure AD テナントを示します。 principalId は、アプリケーションの新しい ID の一意識別子です。 Azure AD のサービス プリンシパル名は、お使いの App Service または Azure Functions のインスタンスに指定したものと同じです。

テンプレートの後の段階でこれらのプロパティを参照する必要がある場合は、次の例のように、`'Full'` フラグを指定した [`reference()` テンプレート関数](../azure-resource-manager/templates/template-functions-resource.md#reference)を使用して行うことができます。

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>ユーザー割り当て ID を追加する

ユーザー割り当て ID を持つアプリを作成するには、ID を作成してから、そのリソース ID をアプリ構成に追加する必要があります。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

最初に、ユーザー割り当て ID リソースを作成する必要があります。

1. [以下の手順](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)に従って、ユーザー割り当てマネージド ID リソースを作成します。

2. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。

3. 関数アプリを使っている場合は、 **[プラットフォーム機能]** に移動します。 他のアプリの種類の場合は、左側のナビゲーションを下にスクロールして **[設定]** グループに移動します。

4. **[ID]** を選択します。

5. **[ユーザー割り当て済み]** タブで **[追加]** をクリックします。

6. 先ほど作成した ID を検索して選択します。 **[追加]** をクリックします。

    ![App Service のマネージド ID](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の手順では、Azure PowerShell を使用して、アプリを作成し、ID を割り当てる方法について説明します。

> [!NOTE]
> 現在のバージョンの Azure App Service 用の Azure PowerShell コマンドレットでは、ユーザー割り当て ID はサポートされていません。 以下の手順は Azure Functions を対象としています。

1. 必要に応じて、[Azure PowerShell ガイド](/powershell/azure/)の手順に従って Azure PowerShell をインストールし、`Login-AzAccount` を実行して、Azure との接続を作成します。

2. Azure PowerShell を使用して関数アプリを作成します。 Azure Functions で Azure PowerShell を使用する方法の他の例については、[Az.Functions リファレンス](/powershell/module/az.functions/#functions)を参照してください。 次のスクリプトでは、「[Azure PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)」に従って個別にインストールする必要がある `New-AzUserAssignedIdentity` を活用しています。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

代わりに `Update-AzFunctionApp` を使用して、既存の関数アプリを更新することもできます。

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 App Service および Functions へのデプロイについて詳しくは、「[Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md)」(App Service でのリソースのデプロイの自動化) および「[Azure Functions の関数アプリのリソース デプロイを自動化](../azure-functions/functions-infrastructure-as-code.md)」をご覧ください。

種類が `Microsoft.Web/sites` であるリソースは、リソース定義に次のブロックを含めて、`<RESOURCEID>` を目的の ID のリソース ID と置き換えることで、ID を使って作成できます。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> アプリケーションは、システム割り当て ID とユーザー割り当て ID の両方を同時に持つことができます。 この場合、`type` プロパティは `SystemAssigned,UserAssigned` になります

ユーザー割り当ての型を追加すると、アプリケーションに対して指定されたユーザー割り当て ID を使用するように Azure に指示します。

たとえば、Web アプリは次のようになります。

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

作成されるサイトには、次の追加プロパティが設定されます。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

principalId は、Azure AD の管理に使用される ID の一意識別子です。 clientId は、ランタイム呼び出し中に使用する ID を指定するために使用されるアプリケーションの新しい ID の一意識別子です。

## <a name="obtain-tokens-for-azure-resources"></a>Azure リソースのトークンを取得する

アプリは、そのマネージド ID を使って、Azure Key Vault など Azure AD で保護されているその他のリソースにアクセスするトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーではなく、リソースにアクセスしているアプリケーションを表します。 

アプリケーションからのアクセスを許可するように、対象のリソースを構成することが必要な場合があります。 たとえば、Key Vault にアクセスするためのトークンを要求する場合、アプリケーションの ID を含むアクセス ポリシーを追加する必要があります。 追加しないと、トークンを含めた場合でも、Key Vault の呼び出しは拒否されます。 Azure Active Directory トークンをサポートしているリソースの詳細については、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

> [!IMPORTANT]
> マネージド ID のバックエンド サービスは、リソース URI ごとのキャッシュを約 24 時間保持します。 特定のターゲット リソースのアクセス ポリシーを更新し、そのリソースのトークンをすぐに取得した場合、そのトークンの有効期限が切れるまで、期限切れのアクセス許可を持つキャッシュされたトークンを取得し続ける可能性があります。 現在、トークンの更新を強制する方法はありません。

App Service と Azure Functions には、トークンを取得するための簡単な REST プロトコルがあります。 これは、すべてのアプリケーションと言語で使用できます。 .NET と Java では、Azure SDK によってこのプロトコルが抽象化され、ローカル開発エクスペリエンスを支援します。

### <a name="using-the-rest-protocol"></a>REST プロトコルの使用

> [!NOTE]
> "2017-09-01" の API バージョンを使用するこのプロトコルの古いバージョンでは、`X-IDENTITY-HEADER` の代わりに `secret` ヘッダーを使用し、ユーザー割り当てに `clientid` プロパティのみを許可していました。 また、タイムスタンプ形式で `expires_on` も返していました。 MSI_ENDPOINT は IDENTITY_ENDPOINT の別名として使用でき、MSI_SECRET は IDENTITY_HEADER の別名として使用できます。 このプロトコルのこのバージョンは、現在、Linux 従量課金ホスティング プランに必要です。

マネージド ID を使用するアプリでは、2 つの環境変数を定義します。

- IDENTITY_ENDPOINT - ローカル トークン サービスに対する URL。
- IDENTITY_HEADER - サーバー側のリクエスト フォージェリ (SSRF) 攻撃を回避するために使用するヘッダー。 値は、プラットフォームによってローテーションされます。

**IDENTITY_ENDPOINT** は、お使いのアプリがトークンを要求するローカル URL です。 リソースのトークンを取得するには、次のパラメーターを指定して、このエンドポイントに HTTP GET 要求を行います。

> | パラメーター名    | 場所     | 説明                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | クエリ  | トークンを取得する必要のあるリソースの Azure AD リソース URI。 これは [Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)の 1 つか、その他のリソース URI になります。    |
> | api-version       | クエリ  | 使うトークン API のバージョン。 "2019-08-01" 以降を使用してください (ただし、現在 "2017-09-01" のみが提供されている Linux 従量課金プランを使用している場合を除きます。上記の注を参照)。                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | ヘッダー | IDENTITY_HEADER 環境変数の値。 このヘッダーは、サーバー側のリクエスト フォージェリ (SSRF) 攻撃を回避するために使用されます。                                                                                                                                                                                                    |
> | client_id         | クエリ  | (省略可能) 使用するユーザー割り当て ID のクライアント ID。 `principal_id`、`mi_res_id`、または `object_id` を含む要求では使用できません。 すべての ID パラメーター (`client_id`、`principal_id`、`object_id`、および `mi_res_id`) を省略した場合、システム割り当て ID が使用されます。                                             |
> | principal_id      | クエリ  | (省略可能) 使用するユーザー割り当て ID のプリンシパル ID。 `object_id` は代わりに使用する別名です。 client_id、mi_res_id、または object_id を含む要求では使用できません。 すべての ID パラメーター (`client_id`、`principal_id`、`object_id`、および `mi_res_id`) を省略した場合、システム割り当て ID が使用されます。 |
> | mi_res_id         | クエリ  | (省略可能) 使用するユーザー割り当て ID の Azure リソース ID。 `principal_id`、`client_id`、または `object_id` を含む要求では使用できません。 すべての ID パラメーター (`client_id`、`principal_id`、`object_id`、および `mi_res_id`) を省略した場合、システム割り当て ID が使用されます。                                      |

> [!IMPORTANT]
> ユーザー割り当て ID のトークンを取得する場合は、省略可能なプロパティの 1 つを含める必要があります。 このようにしないと、トークン サービスは存在する場合と存在しない場合があるシステムが割り当てた ID のトークンを取得しようとします。

正常終了の応答である 200 OK には、JSON 本文と次のプロパティが含まれています。

> | プロパティ名 | 説明                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | 要求されたアクセス トークン。 呼び出し元の Web サービスは、このトークンを使用して受信側の Web サービスに対する認証処理を行うことができます。                                                                                                                               |
> | client_id     | 使用された ID のクライアント ID。                                                                                                                                                                                                       |
> | expires_on    | アクセス トークンが期限切れになるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `exp` 要求に対応)。                                                                                |
> | not_before    | アクセス トークンが有効になり、承認されるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `nbf` 要求に対応)。                                                      |
> | resource      | アクセス トークンの要求対象リソース。要求の `resource` クエリ文字列パラメーターと一致します。                                                                                                                               |
> | token_type    | トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 |

この応答は、[Azure AD のサービス間アクセス トークン要求に対する応答](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)と同じです。

### <a name="rest-protocol-examples"></a>REST プロトコルの例

要求の例を次に示します。

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

応答の例は次のようになります。

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>コード例

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> .NET 言語では、この要求を自作する代わりに、[Microsoft.Azure.Services.AppAuthentication](#asal) を使うこともできます。

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>.NET 用の Microsoft.Azure.Services.AppAuthentication ライブラリの使用

.NET アプリケーションと Functions の場合、マネージド ID を使用する最も簡単な方法は、Microsoft.Azure.Services.AppAuthentication パッケージを利用することです。 このライブラリを使うと、Visual Studio、[Azure CLI](/cli/azure)、または Active Directory 統合認証のユーザー アカウントを使って、開発用コンピューターでローカルにコードをテストすることもできます。 クラウドでホストされている場合は、システムによって割り当てられた ID が既定で使用されますが、この動作は、ユーザー割り当て ID のクライアント ID を参照する接続文字列環境変数を使用してカスタマイズできます。 このライブラリでの開発オプションについて詳しくは、[Microsoft.Azure.Services.AppAuthentication reference]に関する記事をご覧ください。 このセクションでは、コードでライブラリを使い始める方法を示します。

1. [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) とその他の必要な NuGet パッケージに対する参照をアプリケーションに追加します。 下の例では [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) も使用されています。

2. 次のコードをアプリケーションに追加し、正しいリソースが対象となるように変更します。 この例では、Azure Key Vault と連携動作するための 2 つの方法が示されています。

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

ユーザー割り当てのマネージド ID を使用する場合は、`AzureServicesAuthConnectionString` アプリケーション設定を `RunAs=App;AppId=<clientId-guid>` に設定できます。 `<clientId-guid>` は、使用する ID のクライアント ID に置き換えます。 カスタム アプリケーション設定を使用し、その値を AzureServiceTokenProvider コンストラクターに渡すことで、このような接続文字列を複数定義できます。

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

AzureServiceTokenProvider の構成とそれによって公開される操作の詳細については、[Microsoft.Azure.Services.AppAuthentication reference]に関するページと [MSI .NET での App Service と KeyVault のサンプル](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)に関するページをご覧ください。

### <a name="using-the-azure-sdk-for-java"></a>Azure SDK for Java を使用する

Java のアプリケーションと関数の場合、マネージド ID を利用する最も簡単な方法は、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java) を経由する方法です。 このセクションでは、コードでライブラリを使い始める方法を示します。

1. [Azure SDK ライブラリ](https://mvnrepository.com/artifact/com.microsoft.azure/azure)の参照を追加します。 Maven プロジェクトの場合、プロジェクトの POM ファイルの `dependencies` セクションにこのスニペットを追加できます。

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. 認証には `AppServiceMSICredentials` オブジェクトを使用します。 この例からは、このメカニズムを使用して Azure Key Vault を操作する方法がわかります。

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>ID を削除する

システム割り当て ID は、ポータル、PowerShell、または CLI を使用して、作成時と同じ方法で機能を無効にすることで、削除できます。 ユーザー割り当て ID は個別に削除することはできません。 すべての ID を削除するには、ID の種類を "None" に設定します。

この方法でシステム割り当て ID を削除すると、それは Azure AD からも削除されます。 システム割り当て ID は、アプリ リソースが削除されると、Azure AD からも自動的に削除されます。

[ARM テンプレート](#using-an-azure-resource-manager-template)のすべての ID を削除するには、次のようにします。

```json
"identity": {
    "type": "None"
}
```

Azure PowerShell のすべての ID を削除するには (Azure Functions のみ) 次のようにします。

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> また、単純にローカル トークン サービスを無効にする、設定可能なアプリケーション設定 WEBSITE_DISABLE_MSI もあります。 ただし、ID はその場所に残り、ツールには引き続きマネージド ID が "オン" または "有効" と表示されます。 そのため、この設定の使用はお勧めしません。

## <a name="next-steps"></a>次のステップ

- [マネージド ID を使用して SQL データベースに安全にアクセスする](app-service-web-tutorial-connect-msi.md)
- [マネージド ID を使用して Azure Storage に安全にアクセスする](scenario-secure-app-access-storage.md)
- [マネージド ID を使用して Microsoft Graph を安全に呼び出す](scenario-secure-app-access-microsoft-graph-as-app.md)

[Microsoft.Azure.Services.AppAuthentication のリファレンス]: /dotnet/api/overview/azure/service-to-service-authentication