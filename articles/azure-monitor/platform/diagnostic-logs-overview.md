---
title: Azure 診断ログの概要
description: Azure 診断ログの概要と、診断ログを使用して Azure リソース内で発生するイベントを把握する方法について説明します。
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 890f2224a4053ec8cad65b44b85eab0e31be3b64
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519393"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Azure リソースからのログ データの収集と使用

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Azure Monitor 診断ログの概要

**Azure Monitor 診断ログ**は、Azure サービスによって生成されるログです。このサービスの操作に関するさまざまなデータが頻繁に記録されています。 Azure Monitor には、次の 2 種類の診断ログが用意されています。
* **テナント ログ**: Azure Active Directory ログなど、Azure サブスクリプションの外部に存在するテナント レベルのサービスのログです。
* **リソースログ**: ネットワーク セキュリティ グループやストレージ アカウントなど、Azure サブスクリプション内でリソースをデプロイする Azure サービスのログです。

    ![リソース診断ログとその他の種類のログ](./media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

これらのログの内容は、Azure サービスとリソースの種類によって異なります。 たとえば、ネットワーク セキュリティ グループのルール カウンターとキー コンテナーの監査は、2 種類の診断ログです。

これらのログは、[アクティビティ ログ](activity-logs-overview.md)とは異なります。 アクティビティ ログを使用すると、サブスクリプション内で Resource Manager を使用して実行された操作 (仮想マシンの作成やロジック アプリの削除など、) を分析できます。 アクティビティ ログはサブスクリプションレベルのログです。 リソースレベルの診断ログを使用すると、そのリソース内で実行された操作 (Key Vault からのシークレットの取得など) を分析できます。

これらのログは、ゲスト OS レベルの診断ログとも異なります。 ゲスト OS 診断ログは、仮想マシンの内部で実行されているエージェントや、他のサポートされるリソースの種類によって収集されるログです。 リソースレベルの診断ログの場合、エージェントは不要です。Azure プラットフォーム自体からリソース固有のデータをキャプチャします。一方、ゲスト OS レベルの診断ログの場合、仮想マシン上で実行されているオペレーティング システムとアプリケーションからデータをキャプチャします。

ここで説明するサービスは、診断ログでサポートされていない場合があります。 [この記事には、診断ログ をサポートするサービスの一覧が記載されたセクションがあります](./../../azure-monitor/platform/diagnostic-logs-schema.md)。

## <a name="what-you-can-do-with-diagnostic-logs"></a>診断ログで実行できること
診断ログでは次のことを実行できます。

![診断ログの論理的配置](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* 監査や手動での検査に使用するために診断ログを[**ストレージ アカウント**](../../azure-monitor/platform/archive-diagnostic-logs.md)に保存する。 **リソース診断設定**を使用して、リテンション期間 (日数) を指定できます。
* サード パーティのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[診断ログを **Event Hubs** にストリーミング](diagnostic-logs-stream-event-hubs.md)する。
* これを [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md) で分析する。ここでは、データが Azure Monitor に即座に書き込まれ、データを最初にストレージに出力する必要はありません。  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたは Event Hubs 名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な RBAC アクセスを持っている必要があります。

> [!NOTE]
>  現在、セキュリティで保護された仮想ネットワークの背後にあるストレージ アカウントにネットワーク フロー ログをアーカイブすることはできません。

## <a name="diagnostic-settings"></a>診断設定

リソース診断ログは、リソース診断設定を使用して構成します。 テナント診断ログは、テナント診断設定を使用して構成します。 サービス コントロールの**診断設定**:

* 診断ログおよびメトリックの送信先 (ストレージ アカウント、Event Hubs、Azure Monitor)。
* 送信されるログ カテゴリ、メトリック データが送信されるかどうか。
* ログの各カテゴリをストレージ アカウントに保持する期間。
    - リテンション期間が 0 日の場合、ログは永続的に保持されます。 または、1 日から 365 日の間の任意の日数を値として指定できます。
    - リテンション ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合 (たとえば、Event Hubs または Log Analytics オプションだけが選択されている場合)、リテンション ポリシーは無効になります。
    - 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。 削除プロセスは午前 0 時 (UTC) に開始されますが、ストレージ アカウントからのログの削除には最大で 24 時間かかる可能性があるので注意してください。

これらの設定は、ポータルの診断設定から、Azure PowerShell および CLI のコマンドで、または [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) を使用して簡単に構成できます。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>診断ログの収集を有効にする方法

診断ログの収集は、[Resource Manager テンプレートでリソースを作成する一環](./../../azure-monitor/platform/diagnostic-logs-stream-template.md)で有効にすることも、リソースの作成後にポータルのそのリソースのページで有効にすることもできます。 また、収集は、Azure PowerShell または CLI のコマンドを使用するか、Azure Monitor REST API を使用していつでも有効にすることができます。

> [!TIP]
> これらの手順は、すべてのリソースに直接適用できるわけではありません。 特定のリソースの種類に適用できる具体的な手順については、このページの最後にあるスキーマのリンクを参照してください。

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>ポータルで診断ログの収集を有効にする

リソースの作成後に Azure Portal でリソース診断ログの収集を有効にするには、特定のリソースに移動するか、Azure Monitor に移動します。 Azure Monitor でこれを有効にする手順は次のとおりです。

1. [Azure Portal](https://portal.azure.com) で、Azure Monitor に移動し、**[診断設定]** をクリックします。

    ![Azure Monitor の [監視] セクション](media/diagnostic-logs-overview/diagnostic-settings-blade.png)

2. 必要に応じて、リソース グループまたはリソースの種類で一覧をフィルタリングして、診断設定を行うリソースをクリックします。

3. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 [診断を有効にする] をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/diagnostic-logs-overview/diagnostic-settings-none.png)

   リソースに既存の設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 [診断設定の追加] をクリックします。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-overview/diagnostic-settings-multiple.png)

3. 設定に名前を付け、データの送信先それぞれのボックスをオンにし、各送信先で使用されるリソースを構成します。 必要に応じて **[リテンション期間 (日数)]** スライダーを使用し、ログを保持する日数を設定します(送信先がストレージ アカウントの場合のみ)。 リテンション期間を 0 にすると、ログが無期限に保存されます。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-overview/diagnostic-settings-configure.png)

4. **[Save]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されるとすぐに、診断ログが指定の宛先に送信されます。

テナントの診断設定は、テナント サービスのポータル ブレードでのみ構成できます。これらの設定は、Azure Monitor の診断設定ブレードには表示されません。 たとえば、Azure Active Directory 監査ログを構成するには、[監査ログ] ブレードの **[データ設定のエクスポート]** をクリックします。

![AAD の診断設定](./media/diagnostic-logs-overview/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>PowerShell でリソース診断ログの収集を有効にする

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell でリソース診断ログの収集を有効にするには、次のコマンドを使用します。

ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

イベント ハブへの診断ログのストリーミングを有効にするには、次のコマンドを使用します。

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Service Bus 規則 ID は、`{Service Bus resource ID}/authorizationrules/{key name}` という形式の文字列です。

Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId
```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

現在、Azure PowerShell を使用してテナント診断設定を構成することはできません。

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Azure CLI を使用してリソース診断ログの収集を有効にする

Azure CLI によるリソース診断ログの収集を有効にするには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使用します。

ストレージ アカウントへの診断ログの保存を有効にするには:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

`--resource-group` 引数は、`--storage-account` がオブジェクト ID でない場合にのみ必要です。

イベント ハブへの診断ログのストリーミングを有効にするには:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

ルール ID は、`{Service Bus resource ID}/authorizationrules/{key name}` という形式の文字列です。

Log Analytics ワークスペースへの診断ログの送信を有効にするには:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--resource-group` 引数は、`--workspace` がオブジェクト ID でない場合にのみ必要です。

どのコマンドでも、`--logs` パラメーターとして渡された JSON 配列にディクショナリを追加することによって、診断ログに他のカテゴリを追加できます。 `--storage-account`、`--event-hub`、`--workspace` パラメーターを組み合わせて、複数の出力オプションを有効にできます。

現在、CLI を使用してテナント診断設定を構成することはできません。

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>REST API を使用してリソース診断ログの収集を有効にする

Azure Monitor REST API を使用して診断設定を変更する場合は、[こちらのドキュメント](https://docs.microsoft.com/rest/api/monitor/)をご覧ください。

現在、Azure Monitor REST API を使用してテナント診断設定を構成することはできません。

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>ポータルでリソース診断設定を管理する

すべてのリソースについて診断設定がセットアップされていることを確認してください。 ポータルの **[モニター]** に移動し、**[診断設定]** を開きます。

![ポータルの [診断ログ] ブレード](./media/diagnostic-logs-overview/diagnostic-settings-nav.png)

[すべてのサービス] をクリックして、[モニター] セクションを見つけます。

ここでは、診断ログをサポートするすべてのリソースを表示し、フィルター処理することで、有効になっている診断設定があるかどうかを確認できます。 また、ドリルダウンすると、複数の設定が 1 つのリソースに設定されているかどうかを調べて、データの送信先であるストレージ アカウント、Event Hubs 名前空間、Log Analytics ワークスペースを確認することもできます。

![ポータルの診断ログの結果](./media/diagnostic-logs-overview/diagnostic-settings-blade.png)

診断設定を追加すると、[診断設定] ビューが表示され、選択したリソースの診断設定を有効化、無効化、または変更できます。

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>診断ログでサポートされているサービス、カテゴリ、スキーマ

サポートされているサービスと、それらのサービスで使用されるログ カテゴリおよびスキーマの完全な一覧については、[この記事](../../azure-monitor/platform/diagnostic-logs-schema.md)をご覧ください。

## <a name="next-steps"></a>次の手順

* [リソース診断ログを **Event Hubs** にストリーミングする](diagnostic-logs-stream-event-hubs.md)
* [Azure Monitor REST API を使用してリソース診断設定を変更する](https://docs.microsoft.com/rest/api/monitor/)
* [Azure Monitor を使用した、Azure ストレージからのログの分析](collect-azure-metrics-logs.md)
