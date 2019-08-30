---
title: Azure Service Fabric - Service Fabric アプリケーションに他の Azure リソースへのアクセス権を付与する| Microsoft Docs
description: この記事では、マネージド ID が有効になった Service Fabric アプリケーションに、Azure Active Directory ベースの認証をサポートする他の Azure リソースへのアクセス権を付与する方法について説明します。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: 07b26fb86392b26ef45c4370741a32efc7dc436b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640930"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Service Fabric アプリケーションのマネージド ID に Azure リソースへのアクセス権を付与する (プレビュー)

アプリケーションがマネージド ID を使用して他のリソースにアクセスするためには、アクセスする保護された Azure リソースに対するアクセス許可がその ID に付与されている必要があります。 通常、アクセス許可の付与は、Azure Resource Manager 経由でルーティングされる保護されたリソースを所有している Azure サービスの "コントロール プレーン" に対する管理アクションです。これにより、適用可能なすべてのロールベースのアクセス チェックが実施されます。

正確な一連の手順は、アクセスする Azure リソースの種類と、アクセス許可を付与するために使用される言語/クライアントによって異なります。 この記事の残りの部分では、ユーザー割り当ての ID がアプリケーションに割り当てられていることを前提としています。参考のために、いくつかの一般的な例が含まれていますが、このトピックを網羅するリファレンスではありません。アクセス許可を付与するための最新の手順については、それぞれの Azure サービスのドキュメントを参照してください。  

## <a name="granting-access-to-azure-storage"></a>Azure Storage へのアクセス権を付与する
Service Fabric アプリケーションのマネージド ID (この場合はユーザー割り当て) を使用して、Azure Storage BLOB からデータを取得できます。 Azure portal で必要なアクセス許可を ID に付与するには、次の手順を実行します。

1. ストレージ アカウントに移動します。
2. 左側のパネルの [アクセス制御 (IAM)] リンクをクリックします。
3. (省略可能) 既存のアクセス権を確認します。[検索] コントロールで、システム割り当てまたはユーザー割り当てのマネージド ID を選択し、表示された結果一覧から適切な ID を選択します
4. ページの上部にある [+ ロール割り当ての追加] をクリックして、アプリケーションの ID 用に新しいロールの割り当てを追加します。
[ロール] で、ドロップダウンから [ストレージ BLOB データ閲覧者] を選択します。
5. 次のドロップダウンで、[アクセスの割り当て先] として `User assigned managed identity` を選択します。
6. 次に、適切なサブスクリプションが [サブスクリプション] ドロップダウンに表示されていることを確認してから、[リソース グループ] を [すべてのリソース グループ] に設定します。
7. [選択] で、Service Fabric アプリケーションに対応する UAI を選択し、[保存] をクリックします。

Service Fabric のシステム割り当てのマネージド ID のサポートには、Azure portal への統合は含まれていません。アプリケーションでシステム割り当て ID が使用される場合は、最初にアプリケーションの ID のクライアント ID を検索してから、上記の手順を繰り返しますが、[検索] コントロールで `Azure AD user, group, or service principal` オプションを選択する必要があります。

## <a name="granting-access-to-azure-key-vault"></a>Azure Key Vault へアクセス権を付与する
ストレージへのアクセスと同様に、Service Fabric アプリケーションのマネージド ID を活用して Azure Key Vault にアクセスできます。 Azure portal でアクセス権を付与する手順は、上記の手順と同様であり、ここでは繰り返しません。 相違点については、次の図を参照してください。

![Key Vault アクセス ポリシー](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

次の例は、テンプレートのデプロイによってコンテナーへのアクセス権を付与する方法を示しています。テンプレートの `resources` 要素の下に別のエントリとして次のスニペットを追加します。

```json
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```

詳細については、「[コンテナー - アクセス ポリシーの更新](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)」を参照してください。

## <a name="next-steps"></a>次の手順

* [システム割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [ユーザー割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)