---
title: ユーザー割り当てのマネージド ID を持つアプリをデプロイする
description: この記事では、ユーザー割り当てのマネージド ID を使用して Service Fabric アプリケーションをデプロイする方法について説明します
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260091"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>ユーザー割り当てのマネージド ID を持つ Service Fabric アプリケーションをデプロイする

マネージド ID を持つ Service Fabric アプリケーションをデプロイするには、Azure Resource Manager からアプリケーションをデプロイする必要があり、通常は Azure Resource Manager テンプレートを使用します。 Azure Resource Manager から Service Fabric アプリケーションをデプロイする方法の詳細については、「[アプリケーションとサービスを Azure Resource Manager のリソースとして管理する](service-fabric-application-arm-resource.md)」を参照してください。

> [!NOTE] 
> 
> Azure リソースとしてデプロイされていないアプリケーションは、マネージド ID を持つことが **できません**。 
>
> マネージド ID による Service Fabric アプリケーションのデプロイは、API バージョン `"2019-06-01-preview"` でサポートされています。 アプリケーションの種類、アプリケーションの種類のバージョン、サービス リソースに対しても同じ API バージョンを使用できます。
>

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

ユーザー割り当て ID を持つアプリケーションを有効にするには、まず、**userAssigned** 型と参照されるユーザー割り当て ID でアプリケーション リソースに **ID** プロパティを追加します。 次に、**application** リソースの **properties** セクション内に **managedIdentities** セクションを追加します。このセクションには、ユーザー割り当て ID ごとのフレンドリ名と principalId のマッピングの一覧が含まれます。 ユーザー割り当て ID について詳しくは、[ユーザー割り当てマネージド ID の作成、一覧表示、削除](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)に関する記事をご覧ください。

### <a name="application-template"></a>アプリケーション テンプレート

ユーザー割り当て ID を持つアプリケーションを有効にするには、まず、**userAssigned** 型と参照されるユーザー割り当て ID でアプリケーション リソースに **identity** プロパティを追加し、次にユーザー割り当て ID ごとのフレンドリ名と principalId のマッピングの一覧を格納する **managedIdentities** オブジェクトを **properties** セクション内に追加します。

```json
{
  "apiVersion": "2019-06-01-preview",
  "type": "Microsoft.ServiceFabric/clusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "typeName": "[parameters('applicationTypeName')]",
    "typeVersion": "[parameters('applicationTypeVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

上の例では、アプリケーションのマネージド ID のフレンドリ名として、ユーザー割り当て ID のリソース名が使用されています。 次の例では、実際のフレンドリ名が "AdminUser" であるものとしています。

### <a name="application-package"></a>アプリケーション パッケージ

1. Azure Resource Manager テンプレートの `managedIdentities` セクションに定義されている ID ごとに、アプリケーション マニフェストの **Principals** セクションの下に `<ManagedIdentity>` タグを追加します。 `Name` 属性は、`managedIdentities` セクションに定義されている `name` プロパティと一致する必要があります。

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. **ServiceManifestImport** セクションで、マネージド ID を使用するサービスに **IdentityBindingPolicy** を追加します。 このポリシーによって、後でサービス マニフェストに追加する必要があるサービス固有の ID 名に `AdminUser` ID がマップされます。

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. サービス マニフェストを更新して、**Resources** セクション内に、アプリケーション マニフェストの `IdentityBindingPolicy` 内の `ServiceIdentityRef` と一致する名前で **ManagedIdentity** を追加します。

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>次のステップ

* [Service Fabric アプリケーション コードでマネージド ID を使用する方法](how-to-managed-identity-service-fabric-app-code.md)
* [Service Fabric アプリケーションに他の Azure リソースへのアクセスを許可する方法](how-to-grant-access-other-resources.md)
