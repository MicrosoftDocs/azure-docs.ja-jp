---
title: システムに割り当ての MI を使用して Service Fabric アプリをデプロイする
description: この記事では、システム割り当てのマネージド ID を Azure Service Fabric アプリケーションに割り当てる方法について説明します
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81415647"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>システム割り当てのマネージド ID を持つ Service Fabric アプリケーションをデプロイする

Azure Service Fabric アプリケーションのマネージド ID 機能にアクセスするには、まずクラスターでマネージド ID トークン サービスを有効にする必要があります。 このサービスは、マネージド ID を使用して Service Fabric アプリケーションの認証を実行し、アクセス トークンを代理で取得します。 サービスが有効になると、Service Fabric Explorer の左側のウィンドウの **[システム]** セクションに表示され、他のシステム サービスの横で **fabric:/System/ManagedIdentityTokenService** という名前で実行されます。

> [!NOTE] 
> マネージ ID を持つ Service Fabric アプリケーションのデプロイは、API バージョン `"2019-06-01-preview"` 以降でサポートされています。 アプリケーションの種類、アプリケーションの種類のバージョン、サービス リソースに対しても同じ API バージョンを使用できます。 サポートされている Service Fabric ランタイムの最小バージョンは 6.5 CU2 です。 さらに、ビルド/パッケージ環境には CU2 以上の SF .Net SDK も含まれている必要があります。

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

### <a name="application-template"></a>アプリケーション テンプレート

システム割り当てのマネージド ID を持つアプリケーションを有効にするには、以下の例に示すように、**systemAssigned** の種類で、**identity** プロパティをアプリケーション リソースに追加します。

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
このプロパティは、Azure Resource Manager、および Managed Identity プロバイダーと Service Fabric Resource プロバイダーのそれぞれに対して、このリソースが暗黙的な (`system assigned`) マネージド ID を持つことを宣言します。

### <a name="application-and-service-package"></a>アプリケーションとサービス パッケージ

1. 以下に示すように、アプリケーション マニフェストを更新して、1 つのエントリを含む **ManagedIdentity** 要素を **Principals** セクションに追加します。

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    これにより、アプリケーションを構成するサービスへの今後の割り当てについては、リソースとしてアプリケーションに割り当てられた ID がフレンドリ名にマップされます。 

2. 以下に示すように、マネージド ID を割り当てるサービスに対応する **ServiceManifestImport** セクションに **IdentityBindingPolicy** 要素を追加します。

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    この要素は、アプリケーションの ID をサービスに割り当てます。この割り当てがないと、サービスはアプリケーションの ID にアクセスできなくなります。 上記のスニペットで、`SystemAssigned` ID (予約済みキーワード) は、フレンドリ名 `WebAdmin` でサービスの定義にマップされています。

3. サービス マニフェストを更新して、アプリケーション マニフェストの `IdentityBindingPolicy` 定義の `ServiceIdentityRef` 設定の値に一致する名前を使用して、**ManagedIdentity** 要素を **Resources** セクション内に追加します。

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    これは、前述の ID からサービスへのマッピングと同等ですが、サービス定義の観点からのものになります。 ID は、アプリケーション マニフェストで宣言されているように、ここではフレンドリ名 (`WebAdmin`) で参照されます。

## <a name="next-steps"></a>次の手順
* Azure Service Fabric での[マネージド ID のサポート](./concepts-managed-identity.md)を確認します
* マネージド ID サポートを備えた[新しい Azure Service Fabric クラスターをデプロイする](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* 既存の Azure Service Fabric クラスターで[マネージド ID を有効にする](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [ソース コードから Service Fabric アプリケーションのマネージド ID](./how-to-managed-identity-service-fabric-app-code.md) を活用する
* [ユーザー割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセス権を付与する](./how-to-grant-access-other-resources.md)
