---
title: 管理グループを作成して Azure リソースを整理する - Azure のガバナンス
description: ポータル、Azure PowerShell、および Azure CLI を使用して、複数のリソースを管理する Azure 管理グループを作成する方法について説明します。
author: rthorn17
manager: rithorn
ms.service: governance
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: f34efbbca1616c75c13ee3a8bf73bbee1e66dc92
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241158"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>リソースの整理と管理のための管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。

## <a name="create-a-management-group"></a>管理グループの作成

管理グループを作成するには、ポータル、PowerShell、または Azure CLI を使用します。 現時点では、Resource Manager テンプレートを使用して管理グループを作成することはできません。

### <a name="create-in-portal"></a>ポータルで作成する

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理グループ]** を選択します。

1. メイン ページで、 **[新しい管理グループ]** を選択します。

   ![管理グループを操作するためのページ](./media/main.png)

1. [管理グループ ID] フィールドに入力します。

   - **[管理グループ ID]** は、この管理グループでコマンドを送信するために使用するディレクトリの一意識別子です。 この識別子は、このグループを識別するために Azure システム全体で使用されるため、作成後は編集できません。 [ルート管理グループ](index.md#root-management-group-for-each-directory)は、Azure Active Directory ID である ID を使用して自動的に作成されます。 他のすべての管理グループには、一意の ID を割り当てます。
   - 表示名フィールドは、Azure Portal 内で表示される名前です。 管理グループの作成時には別の表示名は省略可能なフィールドで、いつでも変更できます。  

   ![新しい管理グループを作成するための [オプション] ウィンドウ](./media/create_context_menu.png)  

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

## <a name="next-steps"></a>次の手順

管理グループについて詳しくは、以下をご覧ください。

- [管理グループを作成して Azure リソースを整理する](create.md)
- [管理グループを変更、削除、または管理する方法](manage.md)
- [Azure PowerShell Resources モジュールで管理グループを確認する](/powershell/module/az.resources#resources)
- [REST API で管理グループを確認する](/rest/api/resources/managementgroups)
- [Azure CLI で管理グループを確認する](/cli/azure/account/management-group)