---
title: マネージド ID を持つ Azure マネージド アプリケーション
description: マネージド ID を持つマネージド アプリケーションを構成する方法について説明します。 マネージド ID を使用すると、既存のリソースにリンクされたマネージド アプリケーションをデプロイしたり、マネージド アプリケーションにマネージド リソース グループの外部にある Azure リソースの管理を付与したり、アクティビティ ログや Azure 内のその他のサービスのためのマネージド アプリケーションの運用 ID を提供したりできます。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 9fb5f7a4a62c2d323059f7c0b879482e93feef2f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434859"
---
# <a name="azure-managed-application-with-managed-identity"></a>マネージド ID を持つ Azure マネージド アプリケーション

> [!NOTE]
> マネージド アプリケーションでのマネージド ID のサポートは現在、プレビュー段階です。 マネージド ID を利用するには、2018-09-01-preview api バージョンを使用してください。

マネージド ID を含むようにマネージド アプリケーションを構成する方法について説明します。 マネージド ID を使用すると、顧客は、マネージド アプリケーションに追加の既存のリソースへのアクセスを付与できます。 ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングまたはローテーションする必要はありません。 Azure Active Directory (AAD) でのマネージド ID の詳細については、「[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

アプリケーションには 2 種類の ID を付与できます。

- **システム割り当て ID** はアプリケーションに関連付けられているため、アプリが削除されると削除されます。 アプリは 1 つのシステム割り当て ID しか持つことはできません。
- **ユーザー割り当て ID** は、アプリに割り当てることができるスタンドアロン Azure リソースです。 アプリは複数のユーザー割り当て ID を持つことができます。

## <a name="how-to-use-managed-identity"></a>マネージド ID を使用する方法

マネージド ID は、マネージド アプリケーションのための多数のシナリオを可能にします。 解決できるいくつかの一般的なシナリオには、次のものがあります。

- 既存の Azure リソースにリンクされたマネージド アプリケーションのデプロイ。 例として、[既存のネットワーク インターフェイス](../virtual-network/virtual-network-network-interface-vm.md)に接続されているマネージド アプリケーション内での Azure 仮想マシン (VM) のデプロイがあります。
- マネージド アプリケーションやパブリッシャーへの**マネージド リソース グループ**の外部にある Azure リソースへのアクセスの付与。
- アクティビティ ログや Azure 内のその他のサービスのためのマネージド アプリケーションの運用 ID の提供。

## <a name="adding-managed-identity"></a>マネージド ID の追加

マネージド ID を持つマネージド アプリケーションを作成するには、Azure リソースに追加のプロパティを設定する必要があります。 次の例は、サンプルの **identity** プロパティを示しています。

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

**identity** でマネージド アプリケーションを作成するための一般的な方法には、[CreateUIDefinition.json](./create-uidefinition-overview.md) と [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)の 2 つがあります。 単純な 1 つの作成のシナリオの場合は、より豊富なエクスペリエンスが提供されるため、CreateUIDefinition を使用してマネージド ID を有効にしてください。 ただし、マネージド アプリケーションの自動化された、または複数のデプロイを必要とする高度なシステムや複雑なシステムに対応する場合は、テンプレートを使用できます。

### <a name="using-createuidefinition"></a>CreateUIDefinition の使用

マネージド アプリケーションは、[CreateUIDefinition.json](./create-uidefinition-overview.md) を使用してマネージド ID で構成できます。 [outputs セクション](./create-uidefinition-overview.md#outputs)では、キー `managedIdentity` を使用して、マネージド アプリケーション テンプレートの identity プロパティをオーバーライドできます。 次のサンプルでは、マネージド アプリケーションで**システム割り当て** ID を有効にします。 コンシューマーに入力を求める CreateUIDefinition 要素を使用して、より複雑な ID オブジェクトを形成できます。 これらの入力を使用すると、**ユーザー割り当て ID** でマネージド アプリケーションを構築できます。

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>マネージド ID のために CreateUIDefinition を使用する状況

マネージド アプリケーションでマネージド ID を有効にするために CreateUIDefinition を使用する状況に関するいくつかの推奨事項を次に示します。

- マネージド アプリケーションの作成は、Azure Portal またはマーケットプレース経由で実行されます。
- マネージド ID には複雑なコンシューマー入力が必要です。
- マネージド アプリケーションの作成ではマネージド ID が必要です。

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

マネージド アプリケーションの SystemAssigned ID を有効にする基本的な CreateUIDefinition。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

入力として**ユーザー割り当て ID** リソースを受け取り、マネージド アプリケーションの UserAssigned ID を有効にする基本的な CreateUIDefinition。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

上の CreateUIDefinition.json では、コンシューマーが**ユーザー割り当て ID** Azure リソース ID を入力するためのテキスト ボックスを含むユーザーの作成エクスペリエンスが生成されます。 生成されたエクスペリエンスは次のようになります。

![サンプルのユーザー割り当て ID CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

> [!NOTE]
> Marketplace マネージド アプリケーション テンプレートは、Azure Portal の作成エクスペリエンスを実行する顧客のために自動的に生成されます。
> これらのシナリオでは、ID を有効にするために CreateUIDefinition の `managedIdentity` 出力キーを使用する必要があります。

マネージド ID は、Azure Resource Manager テンプレートを使用して有効にすることもできます。 次のサンプルでは、マネージド アプリケーションで**システム割り当て** ID を有効にします。 入力を指定する Azure Resource Manager テンプレート パラメーターを使用して、より複雑な ID オブジェクトを形成できます。 これらの入力を使用すると、**ユーザー割り当て ID** でマネージド アプリケーションを構築できます。

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>マネージド ID のために Azure Resource Manager テンプレートを使用する状況

マネージド アプリケーションでマネージド ID を有効にするために Azure Resource Manager テンプレートを使用する状況に関するいくつかの推奨事項を次に示します。

- マネージド アプリケーションは、テンプレートに基づいてプログラムでデプロイできます。
- マネージド アプリケーションをプロビジョニングするには、マネージド ID へのカスタム ロールの割り当てが必要です。
- マネージド アプリケーションに Azure Portal やマーケットプレース作成フローは必要ありません。

#### <a name="systemassigned-template"></a>SystemAssigned テンプレート

**システム割り当て** ID を使用してマネージド アプリケーションをデプロイする基本的な Azure Resource Manager テンプレート。

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned テンプレート

**ユーザー割り当て ID** を使用してマネージド アプリケーションをデプロイする基本的な Azure Resource Manager テンプレート。

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Azure リソースへのアクセスの付与

マネージド アプリケーションに ID が付与されたら、それに既存の azure リソースへのアクセスを付与できます。 このプロセスは、Azure Portal のアクセス制御 (IAM) インターフェイス経由で実行できます。 ロールの割り当てを追加するために、マネージド アプリケーションの名前または**ユーザー割り当て ID** を検索できます。

![マネージド アプリケーションへのロールの割り当てを追加する](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>既存の Azure リソースのリンク

> [!NOTE]
> マネージド アプリケーションをデプロイする前に、**ユーザー割り当て ID** を[構成](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)する必要があります。 さらに、マネージド アプリケーションのリンクされたリソースのデプロイは、**マーケットプレース**の種類に対してのみサポートされます。

マネージド ID を使用すると、デプロイ中に既存のリソースへのアクセスが必要なマネージド アプリケーションをデプロイすることもできます。 マネージド アプリケーションが顧客によってプロビジョニングされる場合は、**ユーザー割り当て ID** を追加して **mainTemplate** デプロイに追加の承認を提供できます。

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>リンクされたリソースによる CreateUIDefinition の作成

マネージド アプリケーションのデプロイを既存のリソースにリンクする場合は、既存の Azure リソースと、そのリソースに対する適用可能なロールの割り当てを持つ**ユーザー割り当て ID** の両方を指定する必要があります。

 ネットワーク インターフェイス リソース ID とユーザー割り当て ID リソース ID の 2 つの入力が必要なサンプルの CreateUIDefinition。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

この CreateUIDefinition.json では、2 つのフィールドを含むユーザーの作成エクスペリエンスが生成されます。 最初のフィールドは、ユーザーがマネージド アプリケーションのデプロイにリンクされたリソースの Azure リソース ID を入力できるようにします。 2 番目は、コンシューマーが、リンクされた Azure リソースにアクセスできる**ユーザー割り当て ID** Azure リソース ID を入力するためのものです。 生成されたエクスペリエンスは次のようになります。

![ネットワーク インターフェイス リソース ID とユーザー割り当て ID リソース ID の 2 つの入力を含むサンプルの CreateUIDefinition。](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>リンクされたリソースによる mainTemplate の作成

CreateUIDefinition の更新に加えて、メイン テンプレートも、渡されるリンクされたリソース ID を受け付けるように更新する必要があります。 メイン テンプレートは、新しいパラメーターを追加することによって、新しい出力を受け付けるように更新できます。 生成されたマネージド アプリケーション テンプレート上の値は `managedIdentity` 出力によってオーバーライドされるため、メイン テンプレートに渡されず、parameters セクションには含まれません。

ネットワーク プロファイルを CreateUIDefinition によって提供された既存のネットワーク インターフェイスに設定するサンプルのメイン テンプレート。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>リンクされたリソースによるマネージド アプリケーションの消費

マネージド アプリケーション パッケージが作成されたら、Azure Portal 経由でマネージド アプリケーションを消費できます。 消費する前に必要ないくつかの前提条件となる手順があります。

- 必要なリンクされた Azure リソースのインスタンスを作成する必要があります。
- **ユーザー割り当て ID** を[作成し、リンクされたリソースへのロールの割り当てを与える](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)必要があります。
- 既存のリンクされたリソース ID と**ユーザー割り当て ID** が CreateUIDefinition に提供されます。

## <a name="accessing-the-managed-identity-token"></a>マネージド ID トークンへのアクセス

これでマネージド アプリケーションのトークンに、パブリッシャー テナントから `listTokens` api を使用してアクセスできるようになりました。 要求の例は次のようになります。

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

要求本文のパラメーター:

パラメーター | 必須 | 説明
---|---|---
authorizationAudience | *いいえ* | ターゲット リソースのアプリ ID URI。 発行されたトークンの `aud` (対象ユーザー) 要求でもあります。 既定値は "https://management.azure.com/" です
userAssignedIdentities | *いいえ* | トークンを取得するためのユーザー割り当て済みマネージド ID のリスト。 指定しない場合、`listTokens` はシステム割り当てマネージド ID のトークンを返します。


サンプルの応答は次のようになります。

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

応答では、`value` プロパティの下にトークンの配列が含まれます。

パラメーター | 説明
---|---
access_token | 要求されたアクセス トークン。
expires_in | アクセス トークンの残り有効秒数。
expires_on | アクセス トークンが期限切れになるまでの期間。 これは、エポックからの秒数で表されます。
not_before | アクセス トークンが有効になるまでの期間。 これは、エポックからの秒数で表されます。
authorizationAudience | アクセス トークンが要求された `aud` (対象ユーザー)。 これは、`listTokens` 要求で指定されたものと同じです。
resourceId | 発行されたトークンの Azure リソース ID。 これは、マネージド アプリケーション ID またはユーザー割り当て済み ID のどちらかです。
token_type | トークンの種類。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [カスタム プロバイダーを使用してマネージド アプリケーションを構成する方法](./custom-providers-overview.md)
