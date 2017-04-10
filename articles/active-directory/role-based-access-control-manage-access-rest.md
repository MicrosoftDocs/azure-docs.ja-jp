---
title: "REST を使用したロールベースのアクセス制御 - Azure AD | Microsoft Docs"
description: "REST API を使用したロールベースの Access Control の管理"
services: active-directory
documentationcenter: na
author: kgremban
manager: femila
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4547a805c1827a703bf0ef118387882e45c3f241
ms.openlocfilehash: f63381e3349063ba9dd4ceb67d644c1d71d73369


---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>REST API を使用してロールベースのアクセス制御を管理する
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

Azure ポータルと Azure Resource Manager API のロールベースの Access Control (RBAC) を使用すると、サブスクリプションとリソースへのアクセスを詳細に管理できます。 この機能を使用すると、Active Directory ユーザー、グループ、サービス プリンシパルに特定のスコープで役割を割り当てて、アクセス権を付与できます。

## <a name="list-all-role-assignments"></a>ロールの割り当てをすべて一覧表示する
指定されたスコープとサブスコープにあるロールの割り当てをすべて一覧表示します。

ロールの割り当てを一覧表示するには、指定されたスコープにおける `Microsoft.Authorization/roleAssignments/read` 操作のアクセス権が必要です。 この操作のアクセス権はすべての組み込みロールに付与されています。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **GET** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、ロールの割り当てを一覧表示する対象のスコープに置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{api-version}* は 2015-07-01 に置き換えます。
3. *{filter}* には、ロールの割り当て一覧をフィルター処理するために適用する条件を指定します。

   * (サブスコープのロールの割り当ては含めずに) 指定したスコープのみを対象にロールの割り当てを一覧表示する: `atScope()`    
   * 特定のユーザー、グループ、またはアプリケーションを対象にロールの割り当てを一覧表示する: `principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * (グループから継承されたものも含む) 特定のユーザーを対象にロールの割り当てを一覧表示する: `assignedTo('{objectId of user}')`

### <a name="response"></a>応答
状態コード: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>ロールの割り当てに関する情報を取得する
ロール割り当て ID によって指定された単一のロールの割り当てに関する情報を取得します。

ロールの割り当てに関する情報を取得するには、 `Microsoft.Authorization/roleAssignments/read` 操作のアクセス権が必要です。 この操作のアクセス権はすべての組み込みロールに付与されています。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **GET** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、ロールの割り当てを一覧表示する対象のスコープに置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-assignment-id}* は、ロールの割り当ての GUID 識別子に置き換えます。
3. *{api-version}* は 2015-07-01 に置き換えます。

### <a name="response"></a>応答
状態コード: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>ロールの割り当てを作成する
対象となるプリンシパルとスコープを指定してロールの割り当てを作成し、指定したロールを付与します。

ロールの割り当てを作成するには、 `Microsoft.Authorization/roleAssignments/write` 操作のアクセス権が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **PUT** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、ロールの割り当てを作成する対象のスコープに置き換えます。 親スコープでロールの割り当てを作成すると、同じロールの割り当てが、すべての子スコープに継承されます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1   
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-assignment-id}* は新しい GUID に置き換えます。これは新しいロールの割り当ての GUID 識別子になります。
3. *{api-version}* は 2015-07-01 に置き換えます。

要求本文には、次の形式で値を指定します。

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| 要素名 | 必須 | 型 | Description |
| --- | --- | --- | --- |
| roleDefinitionId |はい |文字列 |ロールの識別子 識別子の形式: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |はい |文字列 |ロールの割り当て先となる Azure AD プリンシパル (ユーザー、グループ、またはサービス プリンシパル) の objectId。 |

### <a name="response"></a>応答
状態コード: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>ロールの割り当てを削除する
指定したスコープにおけるロールの割り当てを削除します。

ロールの割り当てを削除するには、 `Microsoft.Authorization/roleAssignments/delete` 操作のアクセス権が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **DELETE** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、ロールの割り当てを作成する対象のスコープに置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-assignment-id}* は、ロール割り当て ID (GUID) に置き換えます。
3. *{api-version}* は 2015-07-01 に置き換えます。

### <a name="response"></a>応答
状態コード: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>すべてのロールを一覧表示する
指定したスコープでの割り当てに使用できるすべてのロールを一覧表示します。

ロールを一覧表示するには、指定されたスコープにおける `Microsoft.Authorization/roleDefinitions/read` 操作のアクセス権が必要です。 この操作のアクセス権はすべての組み込みロールに付与されています。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **GET** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、ロールを一覧表示する対象のスコープに置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{api-version}* は 2015-07-01 に置き換えます。
3. *{filter}* には、ロールの一覧をフィルター処理するために適用する条件を指定します。

   * 指定したスコープとそのすべての子スコープでの割り当てに使用できるロールを一覧表示する: `atScopeAndBelow()`
   * 完全な表示名を使用してロールを検索する: `roleName%20eq%20'{role-display-name}'` ロールの完全な表示名の URL エンコード形式を使用します。 例: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>応答
状態コード: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>ロールに関する情報を取得する
ロール定義識別子によって指定された単一のロールに関する情報を取得します。 単一のロールに関する情報をその表示名を使って取得する方法については、「 [すべてのロールを一覧表示する](role-based-access-control-manage-access-rest.md#list-all-roles)」を参照してください。

ロールに関する情報を取得するには、 `Microsoft.Authorization/roleDefinitions/read` 操作のアクセス権が必要です。 この操作のアクセス権はすべての組み込みロールに付与されています。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **GET** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、ロールの割り当てを一覧表示する対象のスコープに置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* は、ロールの定義の GUID 識別子に置き換えます。
3. *{api-version}* は 2015-07-01 に置き換えます。

### <a name="response"></a>応答
状態コード: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>カスタム ロールの作成
カスタム ロールを作成します。

カスタム ロールを作成するには、すべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/write` 操作のアクセス権が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **PUT** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、カスタム ロールの&1; つ目の *AssignableScope* に置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* は新しい GUID に置き換えます。これは新しいカスタム ロールの GUID 識別子になります。
3. *{api-version}* は 2015-07-01 に置き換えます。

要求本文には、次の形式で値を指定します。

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 要素名 | 必須 | 型 | Description |
| --- | --- | --- | --- |
| name |はい |文字列 |カスタム ロールの GUID 識別子。 |
| properties.roleName |はい |文字列 |カスタム ロールの表示名。 最大 128 文字です。 |
| properties.description |いいえ |文字列 |カスタム ロールの説明。 最大 1,024 文字です。 |
| properties.type |はい |文字列 |"CustomRole" に設定します。 |
| properties.permissions.actions |あり |String[] |アクション文字列の配列。カスタム ロールでアクセス権を付与する操作を指定します。 |
| properties.permissions.notActions |なし |String[] |アクション文字列の配列。カスタム ロールでアクセス権を付与する操作から除外する操作を指定します。 |
| properties.assignableScopes |はい |String[] |カスタム ロールを使うことができるスコープの配列。 |

### <a name="response"></a>応答
状態コード: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>カスタム ロールの更新
カスタム ロールに変更を加えます。

カスタム ロールに変更を加えるには、すべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/write` 操作のアクセス権が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **PUT** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、カスタム ロールの&1; つ目の *AssignableScope* に置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* は、カスタム ロールの GUID 識別子に置き換えます。
3. *{api-version}* は 2015-07-01 に置き換えます。

要求本文には、次の形式で値を指定します。

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 要素名 | 必須 | 型 | Description |
| --- | --- | --- | --- |
| name |はい |文字列 |カスタム ロールの GUID 識別子。 |
| properties.roleName |はい |文字列 |更新するカスタム ロールの表示名。 |
| properties.description |いいえ |文字列 |更新するカスタム ロールの説明。 |
| properties.type |はい |文字列 |"CustomRole" に設定します。 |
| properties.permissions.actions |あり |String[] |アクション文字列の配列。更新したカスタム ロールでアクセス権を付与する操作を指定します。 |
| properties.permissions.notActions |なし |String[] |アクション文字列の配列。更新したカスタム ロールで付与するアクセス権の対象外とする操作を指定します。 |
| properties.assignableScopes |はい |String[] |更新したカスタム ロールを使うことができるスコープの配列。 |

### <a name="response"></a>応答
状態コード: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>カスタム ロールの削除
カスタム ロールを削除します。

カスタム ロールを削除するには、すべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/delete` 操作のアクセス権が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。 Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「 [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### <a name="request"></a>要求
次の URI で **DELETE** メソッドを使用します。

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

1. *{scope}* は、ロールの定義を削除する対象のスコープに置き換えます。 各種レベルのスコープを指定する例を次に示します。

   * サブスクリプション: /subscriptions/{subscription-id}  
   * リソース グループ: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * リソース: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. *{role-definition-id}* は、カスタム ロールの GUID (ロール定義 ID) に置き換えます。
3. *{api-version}* は 2015-07-01 に置き換えます。

### <a name="response"></a>応答
状態コード: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]



<!--HONumber=Feb17_HO1-->


