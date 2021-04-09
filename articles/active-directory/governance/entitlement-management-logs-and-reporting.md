---
title: Azure Monitor を使用したアーカイブとレポート - Azure AD のエンタイトルメント管理
description: Azure Active Directory のエンタイトルメント管理で、Azure Monitor を使用してログをアーカイブし、レポートを作成する方法について説明します。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c5ab92fcc1d70d12e37ae351e768514b4e7522f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501704"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure Monitor での Azure AD のエンタイトルメント管理に関するアーカイブ ログとレポート

Azure AD では、監査ログに最大 30 日間の監査イベントが格納されます。 ただし、Azure ストレージ アカウントにルーティングするか、Azure Monitor を使用することで、「[Azure AD にレポート データが保存される期間](../reports-monitoring/reference-reports-data-retention.md)」で説明されている既定の保持期間よりも長く監査データを保持できます。 これで、ブックと、このデータに対するカスタム クエリとレポートを使用できるようになります。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure Monitor を使用するように Azure AD を構成する
Azure Monitor ブックを使用する前に、監査ログのコピーを Azure Monitor に送信するように Azure AD を構成する必要があります。

Azure AD 監査ログをアーカイブするには、Azure サブスクリプションに Azure Monitor が必要です。 [Azure Monitor の Azure AD アクティビティ ログ](../reports-monitoring/concept-activity-logs-azure-monitor.md)で、Azure Monitor を使用するための前提条件と推定コストの詳細を確認できます。

**事前に必要なロール**:全体管理者

1. 全体管理者であるユーザーとして Azure portal にサインインします。Azure Monitor ワークスペースが含まれているリソース グループにアクセスできることを確認します。
 
1. **[Azure Active Directory]** を選択し、左側のナビゲーション メニューの [監視] で **[診断設定]** をクリックします。 監査ログをそのワークスペースに送信する設定が既にあるかどうかを確認します。

1. 設定がまだない場合は、 **[診断設定の追加]** をクリックします。 記事「[Azure AD ログを Azure Monitor ログと統合する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor)」の指示に従って、Azure AD 監査ログを Azure Monitor ワークスペースに送信します。

    ![[診断設定] ウィンドウ](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. ログが Azure Monitor に送信された後、 **[Log Analytics ワークスペース]** を選択し、Azure AD 監査ログを含むワークスペースを選択します。

1. **[使用量と推定コスト]** を選択し、 **[データ保有期間]** をクリックします。 監査要件に合わせて、データを保持する日数にスライダーを変更します。

    ![[Log Analytics ワークスペース] ペイン](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. その後、ワークスペースに保持されている日付の範囲を確認するには、「*アーカイブされたログの日付範囲*」ブックを使用できます。  
    
    1. **[Azure Active Directory]** を選択し、 **[ブック]** をクリックします。 
    
    1. **[Azure Active Directory トラブルシューティング]** セクションを展開し、 **[アーカイブされたログの日付範囲]** をクリックします。 


## <a name="view-events-for-an-access-package"></a>アクセス パッケージのイベントを表示する  

アクセス パッケージのイベントを表示するには、次のいずれかのロールで、基になる Azure Monitor ワークスペースにアクセスできる必要があります (詳細については、「[Azure Monitor のログ データとワークスペースへのアクセスを管理する](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions)」を参照してください)。 

- 全体管理者  
- セキュリティ管理者  
- セキュリティ閲覧者  
- レポート閲覧者  
- アプリケーション管理者  

以下の手順でイベントを表示します。 

1. Azure portal で、 **[Azure Active Directory]** を選択し、 **[ブック]** をクリックします。 所有するサブスクリプションが 1 つのみの場合は、ステップ 3 に進んでください。 

1. 複数のサブスクリプションがある場合は、ワークスペースが含まれているサブスクリプションを選択します。  

1. 「*Access Package Activity*」という名前のブックを選択します。 

1. そのブックで、時間範囲を選択し (不明な場合は **[すべて]** に変更)、その時間範囲にアクティビティがあったアクセス パッケージすべてのドロップダウン リストからアクセス パッケージ ID を選択します。 選択した時間範囲内に発生したアクセスパッケージに関連のあるイベントが表示されます。  

    ![アクセス パッケージ イベントを表示する](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    各行には、時刻、アクセス パッケージ ID、操作の名前、オブジェクト ID、UPN、操作を開始したユーザーの表示名が含まれます。  さらなる詳細は JSON に含まれています。   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Azure portal を使用してカスタム Azure Monitor クエリを作成する
エンタイトルメント管理イベントを含め、Azure AD 監査イベントに対する独自のクエリを作成できます。  

1. Azure portal の Azure Active Directory で、左側のナビゲーション メニューの [監視] セクションにある **[ログ]** をクリックして、新しいクエリ ページを作成します。

1. ワークスペースは、クエリ ページの左上に表示されます。 複数の Azure Monitor ワークスペースがあり、Azure AD 監査イベントの格納に使用しているワークスペースが表示されない場合は、 **[スコープの選択]** をクリックします。 次に、適切なサブスクリプションとワークスペースを選択します。

1. 次に、クエリ テキスト領域で、文字列 "search *" を削除し、次のクエリに置き換えます。

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 次に **[実行]** をクリックします。 

    ![[実行] をクリックしてクエリを開始する](./media/entitlement-management-logs-and-reporting/run-query.png)

このテーブルには、既定で過去 1 時間のエンタイトルメント管理の監査ログ イベントが表示されます。 [時間の範囲] 設定を変更して、古いイベントを表示することができます。 ただし、この設定を変更すると、Azure Monitor にイベントを送信するように Azure AD が構成された後に発生したイベントのみが表示されます。

Azure Monitor で保持されている最も古い監査イベントと最新の監査イベントを知りたい場合は、次のクエリを使用します。

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Azure Monitor の監査イベント用に格納される列の詳細については、「[Azure Monitor の Azure AD 監査ログ スキーマを解釈する](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)」を参照してください。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Azure PowerShell を使用してカスタム Azure Monitor クエリを作成する

ログを Azure Monitor に送信するように Azure AD を構成すると、PowerShell を使用してログにアクセスできます。 次に、スクリプトまたは PowerShell コマンド ラインからクエリを送信します。テナントの全体管理者である必要はありません。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>ユーザーまたはサービス プリンシパルに正しいロールが割り当てられていることを確認する

Azure AD に対して認証するユーザーまたはサービス プリンシパルが、Log Analytics ワークスペースの適切な Azure ロールに属していることを確認します。 ロール オプションは、Log Analytics 閲覧者または Log Analytics 共同作成者のいずれかです。 既にこれらのロールのいずれかに属している場合は、「[1 つの Azure サブスクリプションで Log Analytics ID を取得する](#retrieve-log-analytics-id-with-one-azure-subscription)」に進みます。

ロールの割り当てを設定し、クエリを作成するには、次の手順を実行します。

1. Azure portal で、[Log Analytics ワークスペース](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)を検索します。

1. **[アクセス制御 (IAM)]** を選択します。

1. 次に、 **[追加]** をクリックしてロールの割り当てを追加します。

    ![ロールの割り当てを追加する](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell モジュールをインストールする

適切なロールの割り当てが完了したら、PowerShell を起動し、次のように入力して [Azure PowerShell モジュールをインストール](/powershell/azure/install-az-ps)します (まだインストールしていない場合)。

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
これで、Azure AD に対して認証し、クエリを実行している Log Analytics ワークスペースの ID を取得する準備ができました。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>1 つの Azure サブスクリプションで Log Analytics ID を取得する
1 つの Azure サブスクリプションと 1 つの Log Analytics ワークスペースしかない場合は、次のように入力して Azure AD に対して認証し、そのサブスクリプションに接続して、そのワークスペースを取得します。
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>複数の Azure サブスクリプションで Log Analytics ID を取得する

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) は、一度に 1 つのサブスクリプションで動作します。 そのため、複数の Azure サブスクリプションがある場合は、Azure AD ログを含む Log Analytics ワークスペースがあるものに接続する必要があります。 
 
 次のコマンドレットを実行すると、サブスクリプションの一覧が表示されます。Log Analytics ワークスペースがあるサブスクリプションの ID を探します。
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
`Connect-AzAccount –Subscription $subs[0].id` などのコマンドを使用して、そのサブスクリプションに PowerShell セッションを再認証し、関連付けることができます。 非対話式など、PowerShell から Azure への認証方法の詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)」を参照してください。

そのサブスクリプションに複数の Log Analytics ワークスペースがある場合、コマンドレット [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) を実行すると、ワークスペースの一覧が返されます。 これで、Azure AD ログがあるものを見つけることができます。 このコマンドレットから返される `CustomerId` フィールドは、Azure portal の Log Analytics ワークスペースの概要に表示される「ワークスペース ID」の値と同じです。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Log Analytics ワークスペースにクエリを送信する
最後に、ワークスペースを特定したら、[Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) を使用して Kusto クエリをそのワークスペースに送信できます。 これらのクエリは [Kusto クエリ言語](/azure/kusto/query/)で記述されています。
 
たとえば、次のようなクエリを送信する PowerShell コマンドレットを使用して、Log Analytics ワークスペースから監査イベント レコードの日付範囲を取得できます。
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

次のようなクエリを使用して、エンタイトルメント管理イベントを取得することもできます。

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>次のステップ:
- [Azure Monitor ブックを使用した対話型レポートの作成](../../azure-monitor/visualize/workbooks-overview.md)