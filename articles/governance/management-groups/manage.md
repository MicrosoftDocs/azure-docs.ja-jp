---
title: 管理グループを変更、削除、または管理する方法 - Azure のガバナンス
description: 管理グループ階層を表示、保守、更新、および削除する方法について説明します。
author: rthorn17
ms.service: governance
ms.date: 05/22/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 7fd9a8e00072bfb8d3a0e57fb552822588111019
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241194"
---
# <a name="manage-your-resources-with-management-groups"></a>管理グループを使用してリソースを管理する

組織に多数のサブスクリプションがある場合は、これらのサブスクリプションのアクセス、ポリシー、およびコンプライアンスを効率的に管理する方法が必要になることがあります。 Azure 管理グループの範囲は、サブスクリプションを上回ります。 "管理グループ" と呼ばれるコンテナーにサブスクリプションを整理して、管理グループに管理条件を適用できます。 管理グループ内のすべてのサブスクリプションは、管理グループに適用された条件を自動的に継承します。

管理グループを使うと、サブスクリプションの種類に関係なく、大きな規模でエンタープライズ レベルの管理を行うことができます。  管理グループの詳細については、「[Azure 管理グループでリソースを整理する](overview.md)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>管理グループの名前を変更する

管理グループの名前を変更するには、ポータル、PowerShell、または Azure CLI を使用します。

### <a name="change-the-name-in-the-portal"></a>ポータルでの名前の変更

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理グループ]** を選択します。

1. 名前を変更する管理グループを選択します。

1. **詳細**を選択します。

1. ページの上部にある **[グループ名の変更]** オプションを選択します。

   ![管理グループのページにある [グループ名の変更] オプション](./media/detail_action_small.png)

1. メニューが開いたら、表示する新しい名前を入力します。

   ![管理グループの名前を変更する [グループ名の変更] ウィンドウ](./media/rename_context.png)

1. **[保存]** を選択します。

### <a name="change-the-name-in-powershell"></a>PowerShell での名前の変更

表示名を更新するには、**Update-AzManagementGroup** を使用します。 たとえば、管理グループの表示名を "Contoso IT" から "Contoso Group" に変更するには、次のコマンドを実行します。

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Azure CLI での名前の変更

Azure CLI を使用する場合は、update コマンドを使用します。

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>管理グループの削除

管理グループを削除するには、次の要件を満たす必要があります。

1. 管理グループの下に子管理グループやサブスクリプションがないこと。

   - サブスクリプションを管理グループ外に移動する場合は、[別の管理グループへのサブスクリプションの移動](#move-subscriptions-in-the-hierarchy)に関する記事を参照してください。

   - 管理グループを別の管理グループに移動するには、[階層内での管理グループの移動](#move-management-groups-in-the-hierarchy)に関する記事をご覧ください。

1. 管理グループに対する書き込みアクセス許可 ("所有者"、"共同作成者"、または "管理グループ共同作成者") が必要です。 どのアクセス許可があるかを確認するには、管理グループを選択し、 **[IAM]** を選択します。 RBAC ロールについて詳しくは、[RBAC を使用したアクセスとアクセス許可の管理](../../role-based-access-control/overview.md)に関する記事をご覧ください。  

### <a name="delete-in-the-portal"></a>ポータルでの削除

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理グループ]** を選択します。

1. 削除する管理グループを選択します。

1. **詳細**を選択します。

1. **[削除]** を選択します

    > [!TIP]
    > アイコンが無効になっている場合は、アイコンの上にマウス セレクターを置くと理由が表示されます。

   ![[グループの削除] オプション](./media/delete.png)

1. 管理グループを削除することを確認するウィンドウが開きます。

   ![グループの削除の確認ウィンドウ](./media/delete_confirm.png)

1. **[はい]** を選択します。

### <a name="delete-in-powershell"></a>PowerShell での削除

管理グループを削除するには、PowerShell で **Remove-AzManagementGroup** コマンドを使用します。

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Azure CLI での削除

Azure CLI で、az account management-group delete コマンドを使用します。

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>管理グループの表示

直接または継承された RBAC ロールがある管理グループを表示することができます。  

### <a name="view-in-the-portal"></a>ポータルでの表示

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理グループ]** を選択します。

1. 管理グループ階層ページが読み込まれます。 このページで、アクセスできるすべての管理グループとサブスクリプションを調べることができます。 グループ名を選択すると、階層の下位レベルに移動します。 ナビゲーションは、ファイル エクスプローラーと同じように機能します。

1. 管理グループの詳細を表示するには、管理グループのタイトルの横にある **[(詳細])** リンクを選択します。 このリンクを使用できない場合は、その管理グループを表示するアクセス許可がありません。

   ![メイン](./media/main.png)

### <a name="view-in-powershell"></a>PowerShell での表示

すべてのグループを取得するには、Get-AzManagementGroup コマンドを使用します。  管理グループの GET PowerShell コマンドの一覧については、[Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) モジュールを参照してください。  

```azurepowershell-interactive
Get-AzManagementGroup
```

1 つの管理グループの情報の場合は、-GroupName パラメーターを使用します

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

特定の管理グループとその下の階層のすべてのレベルを返すには、 **-Expand** パラメーターと **-Recurse** パラメーターを使用します。  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Azure CLI での表示

すべてのグループを取得するには、list コマンドを使用します。  

```azurecli-interactive
az account management-group list
```

1 つの管理グループの情報の場合は、show コマンドを使用します

```azurecli-interactive
az account management-group show --name 'Contoso'
```

特定の管理グループとその下の階層のすべてのレベルを返すには、 **-Expand** パラメーターと **-Recurse** パラメーターを使用します。

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>階層内でのサブスクリプションの移動

管理グループを作成する理由の 1 つは、サブスクリプションをバンドルするためです。 管理グループとサブスクリプションのみが別の管理グループの子になることができます。 管理グループに移動するサブスクリプションは、親管理グループからすべてのユーザー アクセスとポリシーを継承します。

サブスクリプションを移動するには、次のすべての RBAC アクセス許可に該当する必要があります。

- 子サブスクリプションに対する "所有者" ロール。
- ターゲット親管理グループに対する "所有者"、"共同作成者"、または "管理グループ共同作成者" ロール。
- 既存の親管理グループに対する "所有者"、"共同作成者"、または "管理グループ共同作成者" ロール。

ターゲットまたは既存の親管理グループがルート管理グループである場合、この管理の要件は必要ありません。 すべての新しい管理グループとサブスクリプションは既定でルート管理グループに追加されるため、項目を移動するためにこのグループに対するアクセス許可は不要です。

サブスクリプションの所有者ロールが現在の管理グループから継承される場合、移動先は制限されます。 サブスクリプションは、所有者ロールを持つ別の管理グループにのみ移動できます。 サブスクリプションの所有者ではなくなってしまうので、ご自分が共同作成者である管理グループには移動できません。 サブスクリプションの所有者ロールに (管理グループから継承しているのではなく) 直接割り当てられている場合、ご自分が共同作成者である任意の管理グループに移動できます。

現在割り当てられているアクセス許可を Azure portal で確認するには、管理グループを選択し、 **[IAM]** を選択します。 RBAC ロールについて詳しくは、[RBAC を使用したアクセスとアクセス許可の管理](../../role-based-access-control/overview.md)に関する記事をご覧ください。

### <a name="move-subscriptions-in-the-portal"></a>ポータルでのサブスクリプションの移動

#### <a name="add-an-existing-subscription-to-a-management-group"></a>既存のサブスクリプションを管理グループに追加する

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理グループ]** を選択します。

1. 親にする予定の管理グループを選択します。

1. ページの上部で、 **[サブスクリプションの追加]** を選択します。

1. 一覧で、正しい ID を持つサブスクリプションを選択します。

   ![管理グループに追加できるサブスクリプション](./media/add_context_sub.png)

1. [保存] を選択します。

#### <a name="remove-a-subscription-from-a-management-group"></a>管理グループからサブスクリプションを削除する

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理グループ]** を選択します。

1. 現在親となっている管理グループを選択します。  

1. 一覧内で移動するサブスクリプションの行末にある省略記号を選択します。

   ![管理グループの [移動] オプション](./media/move_small.png)

1. **[移動]** を選択します。

1. 開かれたメニューで、**親管理グループ**を選択します。

   ![親グループを変更する [移動] ウィンドウ](./media/move_small_context.png)

1. **[保存]** を選択します。

### <a name="move-subscriptions-in-powershell"></a>PowerShell でのサブスクリプションの移動

PowerShell でサブスクリプションを移動するには、New-AzManagementGroupSubscription コマンドを使用します。  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

サブスクリプションと管理グループ間のリンクを削除するには、Remove-AzManagementGroupSubscription コマンドを使用します。

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Azure CLI でのサブスクリプションの移動

CLI でサブスクリプションを移動するには、add コマンドを使用します。

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

管理グループからサブスクリプションを削除するには、サブスクリプションの remove コマンドを使用します。  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>階層内での管理グループの移動  

親管理グループを移動すると、そのグループの下の階層も一緒に移動します。 管理グループを移動するために必要なアクセスについては、「[管理グループ アクセス](index.md#management-group-access)」を参照してください。

### <a name="move-management-groups-in-the-portal"></a>ポータルでの管理グループの移動

1. [Azure Portal](https://portal.azure.com) にログインします。

1. **[すべてのサービス]**  >  **[管理グループ]** を選択します。

1. 親にする予定の管理グループを選択します。

1. ページの上部で、 **[管理グループの追加]** を選択します。

1. 開いたメニューで、管理グループを新規作成するか、既存のものを使用するかを選択します。

   - 新規作成を選んだ場合、新しい管理グループが作成されます。
   - 既存のものを使用することを選んだ場合は、この管理グループに移動できるすべての管理グループのドロップダウンが表示されます。  

   ![管理グループを新規または既存のグループに移動する](./media/add_context_MG.png)

1. **[保存]** を選択します。

### <a name="move-management-groups-in-powershell"></a>PowerShell での管理グループの移動

管理グループを別のグループの下に移動するには、PowerShell で Update-AzManagementGroup コマンドを使用します。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Azure CLI での管理グループの移動

Azure CLI で管理グループを移動するには update コマンドを使用します。

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>アクティビティ ログを使用した監査管理グループ

管理グループは、[Azure アクティビティ ログ](../../azure-monitor/platform/activity-logs-overview.md)内でサポートされます。 他の Azure リソースと同じ一元的な場所で、管理グループに発生するすべてのイベントのクエリを実行できます。  たとえば、特定の管理グループに対して行われた、ロールの割り当てまたはポリシーの割り当ての変更を、すべて確認できます。

![管理グループが含まれているアクティビティ ログ](media/al-mg.png)

Azure portal の外部で管理グループに対するクエリを使用する場合、管理グループのターゲット スコープは、 **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** のようになります。

## <a name="referencing-management-groups-from-other-resource-providers"></a>他のリソース プロバイダーからの管理グループの参照

他のリソース プロバイダーのアクションから管理グループを参照するときは、スコープとして次のパスを使用します。 このパスは、PowerShell、Azure CLI、REST API を使用しているときに使用されます。  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

たとえば、PowerShell で管理グループに新しいロールを割り当てるときにこのパスを使用します。

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

管理グループでポリシー定義を取得するときにも、同じスコープ パスが使用されます。

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>次の手順

管理グループについて詳しくは、以下をご覧ください。

- [管理グループを作成して Azure リソースを整理する](create.md)
- [管理グループを変更、削除、または管理する方法](manage.md)
- [Azure PowerShell Resources モジュールで管理グループを確認する](/powershell/module/az.resources#resources)
- [REST API で管理グループを確認する](/rest/api/resources/managementgroups)
- [Azure CLI で管理グループを確認する](/cli/azure/account/management-group)