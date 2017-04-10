---
title: "Azure CLI を使用したロールベースの Access Control (RBAC) の管理 | Microsoft Docs"
description: "Azure コマンド ライン インターフェイスを使用して、ロールとロールのアクションの表示、サブスクリプションとアプリケーションのスコープへのロールの割り当てなどを行って、ロールベースの Access Control (RBAC) を管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3c8ee4a51d69b7511445d441e28a1014200bdc4a
ms.lasthandoff: 03/21/2017


---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Azure コマンド ライン インターフェイスを使用したロールベースの Access Control の管理
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)


Azure ポータルと Azure Resource Manager API のロールベースの Access Control (RBAC) を使用すると、サブスクリプションとリソースへのアクセスを詳細に管理できます。 この機能を使用すると、Active Directory ユーザー、グループ、サービス プリンシパルに特定のスコープで役割を割り当てて、アクセス権を付与できます。

Azure コマンド ライン インターフェイス (CLI) を使用して RBAC を管理するには、事前に以下の前提条件を用意しておく必要があります。

* Azure CLI バージョン 0.8.8 以降。 最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「 [Azure CLI のインストールと構成](../cli-install-nodejs.md)」を参照してください。
* Azure CLI の Azure Resource Manager。 詳細については、 [Resource Manager での Azure CLI の使用](../xplat-cli-azure-resource-manager.md) に関するページをご覧ください。

## <a name="list-roles"></a>ロールの一覧表示
### <a name="list-all-available-roles"></a>使用可能なすべてのロールの表示
使用可能なすべてのロールを表示するには、次のコマンドを使用します。

        azure role list

次の例では、 *使用可能なすべてのロール*のリストを表示しています。

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![RBAC Azure コマンド ライン - azure ロール一覧 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>ロールのアクションの表示
ロールのアクションを表示するには、次のコマンドを使用します。

    azure role show "<role name>"

次の例では、"*共同作成者*" ロールと "*仮想マシンの共同作成者*" ロールのアクションを表示しています。

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![RBAC Azure コマンド ライン - azure ロールの表示 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>アクセス権の表示
### <a name="list-role-assignments-effective-on-a-resource-group"></a>リソース グループに対して有効なロールの割り当ての表示
リソース グループに存在しているロールの割り当ての一覧を表示するには、次のコマンドを使用します。

    azure role assignment list --resource-group <resource group name>

次の例は、 *pharma-sales-projecforcast* グループのロールの割り当てを示しています。

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure コマンド ライン - グループ別の azure ロール割り当て一覧 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示
特定のユーザーのロールの割り当てとユーザーのグループに割り当てられた割り当てを表示するには、次のコマンドを使用します。

    azure role assignment list --signInName <user email>

このコマンドに少し変更を加えて、グループから継承されたロールの割り当てを確認することもできます。

    azure role assignment list --expandPrincipalGroups --signInName <user email>

次の例は、 *sameert@aaddemo.com* ユーザーに付与されたロールの割り当てを示しています。 これには、ユーザーに直接割り当てられたロールと、グループから継承されたロールも含まれます。

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure コマンド ライン - ユーザー別の azure ロール割り当て一覧 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>アクセス権の付与
割り当てるロールを特定した後、アクセス権を付与するには、次のコマンドを使用します。

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>サブスクリプションのスコープでのグループへのロールの割り当て
サブスクリプションのスコープでグループにロールを割り当てるには、次のコマンドを使用します。

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

次の例では、*サブスクリプション*のスコープで *Christine Koch のチーム*に "*閲覧者*" ロールを割り当てています。

![RBAC Azure コマンド ライン - グループ別の azure ロール割り当ての作成 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>サブスクリプションのスコープでのアプリケーションへのロールの割り当て
サブスクリプションのスコープでアプリケーションにロールを割り当てるには、次のコマンドを使用します。

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

次の例では、選択したサブスクリプションの *Azure AD* アプリケーションに "*共同作成者*" ロールを付与しています。

 ![RBAC Azure コマンド ライン - アプリケーション別の azure ロール割り当ての作成](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>リソース グループのスコープでのユーザーへのロールの割り当て
リソース グループのスコープでユーザーにロールを割り当てるには、次のコマンドを使用します。

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

次の例では、*Pharma-Sales-ProjectForcast* リソース グループのスコープで、*samert@aaddemo.com* ユーザーに "*仮想マシンの共同作成者*" ロールを付与しています。

![RBAC Azure コマンド ライン - ユーザー別の azure ロール割り当ての作成 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>リソースのスコープでのグループへのロールの割り当て
リソースのスコープでグループにロールを割り当てるには、次のコマンドを使用します。

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

次の例では、"*サブネット*" の *Azure AD* グループに "*仮想マシンの共同作成者*" ロールを付与しています。

![RBAC Azure コマンド ライン - グループ別の azure ロール割り当ての作成 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>アクセス権の削除
ロールの割り当てを削除するには、次のコマンドを使用します。

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

次の例では、*Pharma-Sales-ProjectForcast* リソース グループの *sammert@aaddemo.com* ユーザーから、"*仮想マシンの共同作成者*" ロールの割り当てを削除しています。
次に、この例ではこのサブスクリプションのグループからロールの割り当てを削除しています。

![RBAC Azure コマンド ライン - azure ロール割り当ての削除 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>カスタム ロールの作成
カスタム ロールを作成するには、次のコマンドを使用します。

    azure role create --inputfile <file path>

次の例では、 *仮想マシン オペレーター*というカスタム ロールが作成されます。 このカスタム ロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作を許可し、仮想マシンの起動、再起動、監視を許可します。 このカスタム ロールは、2 つのサブスクリプションで使うことができます。 この例では、入力として JSON ファイルを使用します。

![JSON - カスタム ロールの定義 - スクリーン ショット](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![RBAC Azure コマンド ライン - azure ロールの作成 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>カスタム ロールの修正
カスタム ロールを修正するには、まず `azure role show` コマンドを使用してロール定義を取得します。 次に、必要に応じてロール定義ファイルを変更します。 最後に、 `azure role set` を使用して、変更したロール定義を保存します。

    azure role set --inputfile <file path>

次の例では、*Microsoft.Insights/diagnosticSettings/* 操作が **Actions** に追加され、Azure サブスクリプションが仮想マシン オペレーター カスタム ロールの **AssignableScopes** に追加されます。

![JSON - カスタム ロール定義の変更 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![RBAC Azure コマンド ライン - azure ロールの設定 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>カスタム ロールの削除
カスタム ロールを削除するには、まず、 `azure role show` コマンドを使用してロールの **ID** を特定します。 次に、 `azure role delete` コマンドで **ID**を指定してロールを削除します。

次の例では、 *仮想マシン オペレーター* カスタム ロールが削除されます。

![RBAC Azure コマンド ライン - azure ロールの削除 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示
特定のスコープで割り当て可能なロールを一覧表示するには、 `azure role list` コマンドを使用します。

次のコマンドでは、選んだサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![RBAC Azure コマンド ライン - azure ロール一覧 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

次の例では、"*仮想マシン オペレーター*" カスタム ロールは *Production4* サブスクリプションでは利用できません。そのサブスクリプションはロールの **AssignableScopes** にないためです。

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![RBAC Azure コマンド ライン - カスタム ロールの azure ロール一覧 - スクリーンショット](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="rbac-topics"></a>RBAC のトピック
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]


