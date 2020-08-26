---
title: 管理グループを作成してリソースを整理する - Azure のガバナンス
description: ポータル、Azure PowerShell、および Azure CLI を使用して、複数のリソースを管理する Azure 管理グループを作成する方法について説明します。
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 0739eafdf4cf41195b189a8496c331ab470bbec0
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056637"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>リソースの整理と管理のための管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="create-a-management-group"></a>管理グループの作成

テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループの子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート  
レベルでの最初の管理グループの作成が許可されています。

管理グループを作成するには、ポータル、[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)、PowerShell、または Azure CLI を使用します。

### <a name="create-in-portal"></a>ポータルで作成する

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理 + ガバナンス]** を選択します。

1. **[管理グループ]** を選択します。

1. **[+ 管理グループの追加]** を選択します。

   :::image type="content" source="./media/main.png" alt-text="管理グループを操作するためのページ" border="false":::

1. [管理グループ ID] フィールドに入力します。

   - **[管理グループ ID]** は、この管理グループでコマンドを送信するために使用するディレクトリの一意識別子です。 この識別子は、このグループを識別するために Azure システム全体で使用されるため、作成後は編集できません。 [ルート管理グループ](overview.md#root-management-group-for-each-directory)は、Azure Active Directory ID である ID を使用して自動的に作成されます。 他のすべての管理グループには、一意の ID を割り当てます。
   - 表示名フィールドは、Azure Portal 内で表示される名前です。 別の表示名は、管理グループの作成時には省略可能なフィールドであり、  
     いつでも変更できます。

   :::image type="content" source="./media/create_context_menu.png" alt-text="新しい管理グループを作成するための [オプション] ペイン" border="false":::

1. **[保存]** を選択します。

### <a name="create-in-powershell"></a>PowerShell で作成する

PowerShell では、[ New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) コマンドレットを使用して新しい管理グループを作成します。

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** は、作成される一意識別子です。 この ID は、このグループを参照するために他のコマンドで使用され、後で変更することはできません。

Azure portal 内で管理グループを別の名前で表示する場合は、**DisplayName** パラメーターを追加します。 たとえば、Contoso という GroupName と "Contoso Group" という表示名を持つ管理グループを作成する場合は、次のコマンドレットを使用します。

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

前述の例では、新しい管理グループはルート管理グループ以下に作成されます。 別の管理グループを親として指定するには、**ParentId** パラメーターを使用します。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Azure CLI で作成する

Azure CLI では、[az account management-group create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) コマンドを使用して新しい管理グループを作成します。

```azurecli-interactive
az account management-group create --name Contoso
```

**name** は、作成される一意識別子です。 この ID は、このグループを参照するために他のコマンドで使用され、後で変更することはできません。

Azure portal 内で管理グループを別の名前で表示する場合は、**display-name** パラメーターを追加します。 たとえば、Contoso という GroupName と "Contoso Group" という表示名を持つ管理グループを作成する場合は、次のコマンドを使用します。

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

前述の例では、新しい管理グループはルート管理グループ以下に作成されます。 別の管理グループを親として指定するには、**parent** パラメーターを使用し、親グループの名前を指定します。

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>次のステップ

管理グループについて詳しくは、以下をご覧ください。

- [管理グループを作成して Azure リソースを整理する](./create.md)
- [管理グループを変更、削除、または管理する方法](./manage.md)
- [Azure PowerShell Resources モジュールで管理グループを確認する](/powershell/module/az.resources#resources)
- [REST API で管理グループを確認する](/rest/api/resources/managementgroups)
- [Azure CLI で管理グループを確認する](/cli/azure/account/management-group)
