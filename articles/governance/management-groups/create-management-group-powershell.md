---
title: クイック スタート:Azure PowerShell を使用して管理グループを作成する
description: このクイックスタートでは、Azure PowerShell を使用して、リソースを階層で整理する管理グループを作成します。
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216cfeb6e1389793afcfd27d8785a5f912db2c97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592536"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- 開始する前に、最新バージョンの Azure PowerShell がインストールされていることを確認してください。 詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

- [階層の保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)が有効になっていない場合、テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループ ([既定の管理グループ](./how-to/protect-resource-hierarchy.md#setting---default-management-group)) の子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート レベルで最初の管理グループを作成できるようになっています。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Azure PowerShell で作成する

PowerShell では、[ New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) コマンドレットを使用して新しい管理グループを作成します。 この例では、管理グループの **GroupName** は _Contoso_ です。

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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

先ほど作成した管理グループを削除するには、[Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup) コマンドレットを使用します。

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リソース階層を整理するための管理グループを作成しました。 管理グループには、サブスクリプションや他の管理グループを含めることができます。

管理グループについて、またリソース階層の管理方法について詳しくは、次の記事に進んでください。

> [!div class="nextstepaction"]
> [管理グループを使用してリソースを管理する](./manage.md)