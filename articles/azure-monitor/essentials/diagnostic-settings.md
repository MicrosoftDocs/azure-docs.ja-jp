---
title: プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する
description: 診断設定を使用して、Azure Monitor プラットフォーム メトリックとログを Azure Monitor ログ、Azure Storage、または Azure Event Hubs に送信します。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 254d403adc687074eae772bcdcc55793bb25b336
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048915"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する
Azure のアクティビティ ログとリソース ログを含む Azure の[プラットフォーム ログ](./platform-logs-overview.md)では、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 [プラットフォーム メトリック](./data-platform-metrics.md)は、既定で収集され、通常は Azure Monitor メトリック データベースに格納されます。 この記事では、プラットフォーム メトリックとプラットフォーム ログをさまざまな送信先に送信するための診断設定を作成して構成する方法について詳しく説明します。

> [!IMPORTANT]
> アクティビティ ログの診断設定を作成する前に、まずレガシ構成を無効にする必要があります。 詳細については、「[従来の収集方法](../essentials/activity-log.md#legacy-collection-methods)」を参照してください。

各 Azure リソースには、次の条件を定義する独自の診断設定が必要です。

- 設定で定義されている送信先に送信されるログとメトリック データのカテゴリ。 使用できるカテゴリは、リソースの種類によって異なります。
- ログを送信する 1 つ以上の送信先。 現在の送信先には、Log Analytics ワークスペース、Event Hubs、Azure Storage が含まれます。

1 つの診断設定では、各送信先を 1 つだけ定義することができます。 特定の種類の複数の送信先 (たとえば、2 つの異なる Log Analytics ワークスペース) にデータを送信する場合は、複数の設定を作成します。 各リソースには、最大 5 つの診断設定を作成できます。

次のビデオでは、診断設定を使用してプラットフォーム ログをルーティングする手順について説明します。
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [プラットフォーム メトリック](./metrics-supported.md)は、[Azure Monitor のメトリック](./data-platform-metrics.md)へと自動的に送信されます。 診断設定を使用すると、特定の Azure サービスのメトリックを Azure Monitor ログに送信し、特定の制限で[ログ クエリ](../logs/log-query-overview.md)を使用して他の監視データと組み合わせて分析することができます。 
>  
>  
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。 *例*: ブロックチェーンに関する "IOReadBytes" メトリックは、ノード レベルごとに探索してグラフ化できます。 ただし、診断設定を使用してエクスポートすると、エクスポートされたメトリックは、すべてのノードのすべての読み取りバイト数として表されます。 また、内部の制限により、すべてのメトリックが Azure Monitor ログまたは Log Analytics にエクスポート可能であるとは限りません。 詳細については、[エクスポート可能なメトリック一覧](./metrics-supported-export-diagnostic-settings.md)をご覧ください。 
>  
>  
> 特定のメトリックのこれらの制限を回避するには、[メトリック REST API](/rest/api/monitor/metrics/list) を使用してこれらを抽出し、[Azure Monitor データ コレクター API](../logs/data-collector-api.md) を使用して Azure Monitor ログにインポートすることをお勧めします。  


## <a name="destinations"></a>変換先
プラットフォーム ログとメトリックは、次の表の送信先に送信できます。 

| 到着地 | 説明 |
|:---|:---|
| [Log Analytics ワークスペース](../logs/design-logs-deployment.md) | Log Analytics ワークスペースにログとメトリックを送信すると、強力なログ クエリを使用して、Azure Monitor で収集された他の監視データと組み合わせて分析できるほか、アラートや視覚化などの Azure Monitor の他の機能を活用することもできます。 |
| [Event Hubs](../../event-hubs/index.yml) | Event Hubs にログとメトリックを送信すると、サードパーティ製の SIEM やその他のログ分析ソリューションなどの外部システムにデータをストリーミングできます。  |
| [Azure Storage アカウント](../../storage/blobs/index.yml) | Azure ストレージ アカウントにログとメトリックをアーカイブすると、監査、スタティック分析、またはバックアップに役立ちます。 Azure Monitor ログや Log Analytics ワークスペースと比較すると、Azure ストレージはコストが低く、ログを無期限に保持することができます。  |


### <a name="destination-requirements"></a>送信先の要件

診断設定の送信先は、診断設定の作成前に作成する必要があります。 設定を構成するユーザーが両方のサブスクリプションに対して適切な Azure RBAC アクセスを持っている限り、送信先はログを送信するリソースと同じサブスクリプションに属している必要はありません。 次の表では、リージョン制限など、送信先別の固有要件をまとめてあります。

| 到着地 | 要件 |
|:---|:---|
| Log Analytics ワークスペース | ワークスペースは、監視対象のリソースと同じリージョンにする必要がありません。|
| Event Hubs | 名前空間の共有アクセス ポリシーでは、ストリーミング メカニズムが保有するアクセス許可が定義されます。 Event Hubs にストリーミングするには、管理、送信、リッスンの各アクセス許可が必要です。 ストリーミングを含めるように診断設定を更新するには、その Event Hubs の承認規則に対する ListKey アクセス許可が必要です。<br><br>イベント ハブ名前空間は、リソースがリージョン別の場合、監視対象のリソースと同じリソースにする必要があります。 |
| Azure ストレージ アカウント | データへのアクセスをより適切に制御できるように、他の非監視データが格納されている既存のストレージ アカウントは使用しないでください。 アクティビティ ログとリソース ログを一緒にアーカイブする場合は、中央の場所にすべての監視データを保持するために、同じストレージ アカウントを使用できます。<br><br>データを不変ストレージに送信するには、「[BLOB ストレージの不変ポリシーを設定および管理する](../../storage/blobs/storage-blob-immutability-policies-manage.md)」の説明に従って、ストレージ アカウントの不変ポリシーを設定します。 この記事のすべての手順に従う必要があります。これには、保護された追加 BLOB の書き込みの有効化が含まれます。<br><br>ストレージ アカウントは、リソースがリージョン別の場合、監視対象のリソースと同じリソースにする必要があります。 |

> [!NOTE]
> Azure Data Lake Storage Gen2 アカウントは、Azure portal では有効なオプションとして表示されていますが、診断設定の送信先としては現在サポートされていません。

> [!NOTE]
> Azure Monitor (診断設定) では、仮想ネットワークが有効になっていると Event Hubs リソースにアクセスできません。 Event Hub で、[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する] を有効にして、Azure Monitor (診断設定) サービスに Event Hubs リソースへのアクセス権が付与されるようにする必要があります。 


## <a name="create-in-azure-portal"></a>Azure Portal での作成

Azure portal では、Azure Monitor メニューから、またはリソースのメニューから診断設定を構成できます。

1. Azure portal で診断設定を構成する場所は、リソースによって異なります。

   - リソースが 1 つの場合は、リソースのメニューで、 **[監視]** の **[診断設定]** をクリックします。

        ![Azure portal のリソース メニューの [監視] セクションのスクリーンショット。[診断設定] が強調表示されています。](media/diagnostic-settings/menu-resource.png)

   - リソースが 1 つまたは複数の場合は、Azure Monitor メニューで、 **[設定]** の **[診断設定]** をクリックし、次にリソースをクリックします。

        ![[Azure Monitor] メニューの [設定] セクションのスクリーンショット。[診断設定] が強調表示されています。](media/diagnostic-settings/menu-monitor.png)

   - アクティビティ ログの場合は、 **[Azure Monitor]** メニューの **[アクティビティ ログ]** 、 **[診断設定]** の順にクリックします。 アクティビティ ログのレガシ構成が無効になっていることを確認してください。 詳細については、「[既存の設定を無効にする](./activity-log.md#legacy-collection-methods)」を参照してください。

        ![[Azure Monitor] メニューのスクリーンショット。アクティビティ ログが選択されており、[監視 - アクティビティ ログ] メニュー バーで [診断設定] が強調表示されています。](media/diagnostic-settings/menu-activity-log.png)

2. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 **[診断設定の追加]** をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/diagnostic-settings/add-setting.png)

   リソースに対する既存の設定が存在する場合は、構成済みの設定の一覧が表示されます。 **[診断設定を追加する]** をクリックして新しい設定を追加するか、または **[設定の編集]** を選択して既存の設定を編集します。 各設定には、各送信先の種類を 1 つだけ含めることができます。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-settings/edit-setting.png)

3. 設定にまだ名前がない場合は、名前を付けます。

    ![診断設定を追加する](media/diagnostic-settings/setting-new-blank.png)

4. **カテゴリの詳細 (ルーティングの対象)** - 後で指定する送信先に送信する各データ カテゴリのチェック ボックスをオンにします。 カテゴリの一覧は、Azure サービスごとに異なります。

     - **[AllMetrics]** では、リソースのプラットフォーム メトリックを Azure ログ ストアにルーティングしますが、ログ形式になります。 これらのメトリックは、通常、Azure Monitor メトリックの時系列データベースのみに送信されます。 それらを Azure Monitor ログ ストア (Log Analytics を使用して検索可能) に送信すると、他のログ全体を検索するクエリに統合できます。 このオプションは、すべてのリソースの種類で使用できるとは限りません。 サポートされている場合は、[Azure Monitor のサポートされるメトリック](./metrics-supported.md)の一覧に、どのリソースの種類についてどのようなメトリックが収集されるかが示されます。

       > [!NOTE]
       > この記事の前半にある Azure Monitor ログにメトリックをルーティングするための制限に関する記事を参照してください。  


     - **[ログ]** には、リソースの種類に応じて使用可能なさまざまなカテゴリが一覧表示されます。 送信先にルーティングするカテゴリをオンにします。

5. **[宛先の詳細]** - 各送信先のチェック ボックスをオンにします。 各チェックボックスをオンにすると、さらに情報を追加できるオプションが表示されます。

      ![Log Analytics または Event Hubs への送信](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - サブスクリプションとワークスペースを入力します。  ワークスペースがない場合は、[先に進む前に作成する](../logs/quick-create-workspace.md)必要があります。

    1. **イベント ハブ** - 次の条件を指定します。
       - イベント ハブが含まれているサブスクリプション
       - イベント ハブの名前空間 - まだ作成していない場合は、[作成する](../../event-hubs/event-hubs-create.md)必要があります。
       - すべてのデータの送信先となるイベント ハブの名前 (省略可能)。 名前を指定しない場合は、ログ カテゴリごとにイベント ハブが作成されます。 複数のカテゴリを送信する場合は、名前を指定して、作成するイベント ハブの数を制限することができます。 詳細については、「[Azure Event Hubs のクォータと制限](../../event-hubs/event-hubs-quotas.md)」を参照してください。
       - イベント ハブ ポリシー (省略可能) - ポリシーによってストリーミング メカニズムのアクセス許可が定義されます。 詳細については、[Event Hubs の機能](../../event-hubs/event-hubs-features.md#publisher-policy)に関するページを参照してください。

    1. **ストレージ** - サブスクリプション、ストレージ アカウント、アイテム保持ポリシーを選択します。

        ![ストレージへの送信](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 今後、混乱を避けるために、アイテム保持ポリシーを 0 に設定し、スケジュールされたジョブを使用してストレージからデータを手動で削除することを検討してください。
        >
        > まず、ストレージをアーカイブ用に使用している場合は、一般的にデータを 365 日以上保持することをお勧めします。 2 つ目の方法として、0 より大きいアイテム保持ポリシーを選択すると、保存時に有効期限日がログに付加されます。 保存後にログの日付を変更することはできません。
        >
        > たとえば、*WorkflowRuntime* のアイテム保持ポリシーを 180 日に設定し、24 時間後に 365 日に設定した場合、最初の 24 時間の間に保存されたログは 180 日後に自動的に削除されますが、その種類の後続のすべてのログは、365 日後に自動的に削除されます。 後でアイテム保持ポリシーを変更しても、最初の 24 時間のログが 365 日間保持されるようにはなりません。

6. **[保存]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されると、ログが指定の送信先にストリーミングされます。 イベントが生成されてから、それが [Log Analytics ワークスペースに表示される](../logs/data-ingestion-time.md)まで、15 分ほどかかる場合があります。

## <a name="create-using-powershell"></a>PowerShell を使用して作成する

[Azure PowerShell](../powershell-samples.md) を使用して診断設定を作成するには、[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) コマンドレットを使用します。 パラメーターの説明については、このコマンドレットのドキュメントを参照してください。

> [!IMPORTANT]
> この方法を Azure アクティビティ ログに使用することはできません。 代わりに、[Azure Monitor での Resource Manager テンプレートを使用した診断設定の作成](./resource-manager-diagnostic-settings.md)に関するページを参照して、Resource Manager テンプレートを作成し、それを PowerShell を使用してデプロイします。

3 つの送信先すべてを使用して診断設定を作成するための PowerShell コマンドレットの例を次に示します。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Azure CLI を使用して作成する

[Azure CLI](/cli/azure/monitor) を使用して診断設定を作成するには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使用します。 パラメーターの説明については、このコマンドのドキュメントを参照してください。

> [!IMPORTANT]
> この方法を Azure アクティビティ ログに使用することはできません。 代わりに、[Azure Monitor での Resource Manager テンプレートを使用した診断設定の作成](./resource-manager-diagnostic-settings.md)に関するページを参照して、Resource Manager テンプレートを作成し、それを CLI を使用してデプロイします。

3 つの送信先すべてを使用して診断設定を作成するための CLI コマンドの例を次に示します。

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>Resource Manager テンプレートを使用して作成する
Resource Manager テンプレートを使用して診断設定を作成または更新するには、「[Azure Monitor の診断設定用の Resource Manager テンプレートのサンプル](./resource-manager-diagnostic-settings.md)」を参照してください。

## <a name="create-using-rest-api"></a>REST API を使用して作成する
[Azure Monitor REST API](/rest/api/monitor/) を使用して診断設定を作成または更新するには、「[診断設定](/rest/api/monitor/diagnosticsettings)」を参照してください。

## <a name="create-using-azure-policy"></a>Azure Policy を使用して作成する
診断設定は Azure リソースごとに作成する必要があるため、Azure Policy を使用して、各リソースの作成時に診断設定を自動的に作成することができます。 詳細については、「[Azure Policy を使用して大規模に Azure Monitor をデプロイする](../deploy-scale.md)」を参照してください。

## <a name="metric-category-is-not-supported-error"></a>メトリック カテゴリがサポートされないエラー
診断設定をデプロイすると、次のエラー メッセージが表示されます。

   "メトリック カテゴリ '*xxxx*' はサポートされていません"

次に例を示します。 

   "メトリック カテゴリ 'ActionsFailed' はサポートされていません"

この場合、以前はデプロイに成功しました。 

この問題は、Resource Manager テンプレート、診断設定 REST API、Azure CLI または Azure PowerShell を使用するときに発生します。 Azure portal を介して作成された診断設定は、サポートされているカテゴリ名のみが表示されるため、影響を受けません。

この問題は、基になる API の最近の変更が原因で発生します。 'AllMetrics' 以外のメトリック カテゴリはサポートされておらず、少数の非常に特殊な Azure サービスの場合を除き、一度もサポートされたことはありません。 以前は、診断設定のデプロイ時に他のカテゴリ名は無視されていました。 Azure Monitor バックエンドでは単にこれらのカテゴリは 'AllMetrics' にリダイレクトされました。  2021 年 2 月の時点で、提供されたメトリック カテゴリが正確であることを明確に確認するためにバックエンドが更新されました。 この変更が原因で一部のデプロイに失敗しました。

このエラーが発生した場合は、メトリック カテゴリ名をすべて 'AllMetrics' に置き換えるようにデプロイを更新して問題を解決してください。 以前にデプロイで複数のカテゴリを追加していた場合は、'AllMetrics' 参照を持つ 1 つのみを保持する必要があります。 引き続き問題が発生する場合は、Azure portal を通じて Azure サポートにお問い合わせください。 



## <a name="next-steps"></a>次のステップ

- [Azure でのプラットフォーム ログの詳細について読む](./platform-logs-overview.md)