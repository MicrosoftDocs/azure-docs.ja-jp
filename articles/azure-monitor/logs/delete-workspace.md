---
title: Azure Log Analytics ワークスペースの削除と復旧 | Microsoft Docs
description: 個人用の月額プランで Log Analytics ワークスペースを作成した場合や、ワークスペース モデルを再構築する場合に、Log Analytics ワークスペースを削除する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/20/2020
ms.openlocfilehash: 83a64e3348d4af768c56609df3df5c9194ec5af1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030870"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Azure Log Analytics ワークスペースの削除と復旧

この記事では、Azure Log Analytics ワークスペースの論理的な削除の概念および削除したワークスペースを回復させる方法について説明します。

## <a name="considerations-when-deleting-a-workspace"></a>ワークスペースを削除するときの考慮事項

Log Analytics ワークスペースを削除すると、論理的な削除操作が実行されます。削除操作が不注意によるものであれ、意図的なものであれ、14 日以内であればそのデータや接続されているエージェントを含め、ワークスペースを回復させることができます。 論理的な削除の期間が過ぎると、ワークスペース リソースとそのデータは回復不能となります。それらはキューに格納され、30 日以外に完全に削除されます。 ワークスペース名は "リリース済み" であり、新しいワークスペースの作成に使用できます。

> [!NOTE]
> 論理的な削除の動作をオーバーライドして、ご利用のワークスペースを完全に削除する場合は、「[ワークスペースの完全削除](#permanent-workspace-delete)」の手順に従います。

重要なデータや構成が含まれているとサービスの運用に悪影響が生じるおそれがあるため、ワークスペースを削除する場合は注意が必要です。 エージェントやソリューションなど、Log Analytics にデータを格納する各種 Azure サービスを再確認してください。そうしたサービスとソースの例を次に示します。

* 管理ソリューション
* Azure Automation
* Windows と Linux の仮想マシンで実行されているエージェント
* 環境内の Windows および Linux のコンピューターで実行されているエージェント
* System Center Operations Manager

論理的な削除操作では、ワークスペース リソースが削除され、関連するユーザーのアクセス許可がすべて破棄されます。 ユーザーは、他のワークスペースに関連付けられていれば、それらのワークスペースを使用して Log Analytics を使い続けることができます。

## <a name="soft-delete-behavior"></a>論理的な削除の動作

ワークスペースの削除操作では、ワークスペースの Resource Manager リソースが削除されますが、ワークスペースは見かけ上、削除されたように見えるだけで、その構成とデータは 14 日間維持されます。 ワークスペースに対してレポートするよう構成されたエージェントと System Center Operations Manager の管理グループは、論理的な削除の期間、孤立した状態で維持されます。 さらに、削除されたワークスペース (そのデータと接続されているリソースを含む) を回復させる、実質的には削除を元に戻すメカニズムも用意されています。

> [!NOTE] 
> インストールされているソリューションおよびリンクされたサービス (Azure Automation アカウントなど) は、削除時点でワークスペースから完全に削除され、回復させることはできません。 ワークスペースを以前に構成した状態に戻すには、回復操作の後で再構成する必要があります。

ワークスペースは、[PowerShell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace)、[REST API](/rest/api/loganalytics/workspaces/delete)、[Azure portal](https://portal.azure.com) のいずれかの方法で削除することができます。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。
3. Log Analytics ワークスペースの一覧でワークスペースを選択して、中央のウィンドウの上部にある **[削除]** をクリックします。
4. 過去 1 週間のワークスペースへのデータ インジェストを示す確認ページが表示されます。 
5. ワークスペースを完全に削除し、後で回復するオプションを消す場合は、 **[ワークスペースを完全に削除する]** チェック ボックスをオンにします。
6. 確認するワークスペースの名前を入力し、 **[削除]** をクリックします。

   ![ワークスペースの削除の確定](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>ワークスペースの完全削除
開発やテストなどの一部のシナリオでは、同じ設定とワークスペース名を使用してデプロイを繰り返す必要があるため、論理的な削除方法は適さない場合があります。 このような場合は、ワークスペースを完全に削除し、論理的な削除期間を "オーバーライド" できます。 ワークスペースの完全削除を行うと、そのワークスペース名は解放され、同じ名前を使用して新しいワークスペースを作成できるようになります。

> [!IMPORTANT]
> ワークスペースの完全削除操作を行うと元に戻すことができず、ワークスペースとそのデータを復元できないため、注意して使用してください。

Azure portal を使用してワークスペースを完全に削除するには、 **[削除]** ボタンをクリックする前に、 **[ワークスペースを完全に削除する]** チェック ボックスをオンにします。

PowerShell を使用してワークスペースを完全に削除するには、'-ForceDelete' タグを追加してワークスペースを完全に削除します。 '-ForceDelete ' オプションは、現在 Az.OperationalInsights 2.3.0 以降で使用できます。 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>ワークスペースを回復させる
誤って、または意図的に Log Analytics ワークスペースを削除すると、ワークスペースは論理的な削除状態になり、どの操作からもアクセスできなくなります。 論理的な削除期間中は、削除されたワークスペースの名前が維持されるため、新しいワークスペースを作成するときにその名前を使用することはできません。 論理的な削除期間が終了すると、ワークスペースは回復不可能になります。永続的な削除がスケジュールされて名前が解放され、新しいワークスペースの作成に使用できるようになります。

論理的な削除期間中は、データ、構成、および接続されているエージェントを含めてワークスペースを回復できます。 論理的な削除操作の前にワークスペースが配置されていたサブスクリプションとリソース グループに対する共同作成者アクセス許可が必要です。 ワークスペースの回復を実行するには、次のような削除されたワークスペースの詳細を含む Log Analytics ワークスペースを再作成します。

- サブスクリプション ID
- リソース グループ名
- ワークスペース名
- リージョン

> [!IMPORTANT]
> リソース グループを削除したとき、ワークスペースが削除された場合、まず、リソース グループを再作成する必要があります。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。 選択したスコープにあるワークスペースの一覧が表示されます。
3. 左上のメニューにある **[回復]** をクリックして、論理的な削除状態にあり回復可能なワークスペースを含むページを開きます。

   ![Azure portal の、メニュー バーの [回復] が強調表示されている Log Analytics ワークスペース画面のスクリーンショット。](media/delete-workspace/recover-menu.png)

4. ワークスペースを選択し、 **[回復]** をクリックしてそのワークスペースを回復させます。

   ![Azure portal の、ワークスペースが強調表示され、[回復] ボタンが選択されている [削除された Log Analytics ワークスペースの回復] ダイアログのスクリーンショット。](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

ワークスペースとそのすべてのデータは、復旧操作の後に戻されます。 ソリューションおよびリンクされたサービスは、ワークスペースが削除されたときに完全に削除されます。ワークスペースを以前に構成した状態に戻すには、これらを再構成する必要があります。 ワークスペースの復旧後、関連するソリューションが再インストールされ、そのスキーマがワークスペースに追加されるまで、一部のデータがクエリに使用できないことがあります。

## <a name="troubleshooting"></a>トラブルシューティング

ワークスペースを削除するには、少なくとも "*Log Analytics の共同作成者*" のアクセス許可が必要です。

* 削除されたワークスペースが論理的な削除状態で回復できるかどうかわからない場合は、*Log Analytics ワークスペース* ページの [[ごみ箱を開く]](#recover-workspace) をクリックして、サブスクリプションごとに論理的に削除されたワークスペースの一覧を表示します。 完全に削除されたワークスペースは一覧に含まれません。
* ワークスペースの作成時に "*このワークスペース名は既に使用されています*" または "*競合*" というエラー メッセージが表示される場合は、次の原因が考えられます。
  * ワークスペース名を使用できず、組織内の誰か、または他の顧客によって使用されている。
  * ワークスペースが過去 14 日以内に削除されており、その名前は論理的な削除期間にわたって予約されている。 ご自分のワークスペースの論理的な削除をオーバーライドし、完全に削除して同じ名前の新しいワークスペースを作成するには、次の手順に従って、最初にワークスペースを回復してから、完全な削除を実行します。<br>
    1. ワークスペースを[回復](#recover-workspace)します。
    2. ワークスペースを[完全に削除](#permanent-workspace-delete)します。
    3. 同じワークスペース名を使用して新しいワークスペースを作成します。
 
      削除呼び出しがバックエンドで正常に完了した後、ワークスペースを復元して、前に示したいずれかの方法で、永続的な削除操作を完了することができます。

* ワークスペースを削除したとき、"*リソースが見つかりません*" の 204 応答コードが届いた場合、連続再試行が行われた可能性があります。 204 は空の応答で、通常はリソースが存在しないことを意味します。そのため、削除は何も行わずに完了します。
* リソース グループと含まれるワークスペースを削除した場合、削除したワークスペースを [[ごみ箱を開く]](#recover-workspace) ページで表示できますが、リソース グループが存在しないため、復元がエラー コード 404 で失敗します。リソース グループを再作成し、もう一度復元をお試しください。
