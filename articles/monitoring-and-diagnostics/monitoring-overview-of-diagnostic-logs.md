---
title: "Azure 診断ログの概要 | Microsoft Docs"
description: "Azure 診断ログの概要と、診断ログを使用して Azure リソース内で発生するイベントを把握する方法について説明します。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem; magoedte
ms.openlocfilehash: d59abde29fc7b73a799e5bf3659b02f824b693de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Azure リソースからのログ データの収集と使用

## <a name="what-are-azure-resource-diagnostic-logs"></a>Azure リソース診断ログとは
**Azure リソースレベル診断ログ**は、リソースによって出力されるログであり、そのリソースの操作に関する豊富なデータを提供します。 これらのログの内容は、リソースの種類によって異なります。 たとえば、ネットワーク セキュリティ グループ ルール カウンターと Key Vault 監査は、リソース ログの 2 カテゴリです。

リソースレベルの診断ログは、は、[アクティビティ ログ](monitoring-overview-activity-logs.md)とは異なります。 アクティビティ ログを使用すると、サブスクリプション内で Resource Manager を使用して実行された操作 (仮想マシンの作成やロジック アプリの削除など、) を分析できます。 アクティビティ ログはサブスクリプションレベルのログです。 リソースレベルの診断ログを使用すると、そのリソース内で実行された操作 (Key Vault からのシークレットの取得など) を分析できます。

リソースレベルの診断ログは、ゲスト OS レベルの診断ログとも異なります。 ゲスト OS 診断ログは、仮想マシンの内部で実行されているエージェントや、他のサポートされるリソースの種類によって収集されるログです。 リソースレベルの診断ログの場合、エージェントは不要です。Azure プラットフォーム自体からリソース固有のデータをキャプチャします。一方、ゲスト OS レベルの診断ログの場合、仮想マシン上で実行されているオペレーティング システムとアプリケーションからデータをキャプチャします。

ここで説明した新しい種類のリソース診断ログは、すべてのリソースでサポートされているわけではありません。 この記事には、新しいリソースレベルの診断ログをサポートするリソースの種類の一覧を紹介するセクションがあります。

![リソース診断ログとその他の種類のログ ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>リソースレベルの診断ログの利用方法
リソース診断ログでは次のことを実行できます。

![リソース診断ログの論理的配置](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* 監査や手動での検査に使用するために診断ログを[**ストレージ アカウント**](monitoring-archive-diagnostic-logs.md)に保存する。 **リソース診断設定**を使用して、リテンション期間 (日数) を指定できます。
* サード パーティのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[診断ログを **Event Hubs** にストリーミング](monitoring-stream-diagnostic-logs-to-event-hubs.md)する。
* 診断ログを [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたは Event Hubs 名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な RBAC アクセスを持っている必要があります。

## <a name="resource-diagnostic-settings"></a>リソース診断設定
非コンピューティング リソースのリソース診断ログは、リソース診断設定を使用して構成します。 リソースの**リソース診断設定**では、以下を制御します。

* リソース診断ログおよびメトリックの送信先 (ストレージ アカウント、Event Hubs、OMS Log Analytics)。
* 送信されるログ カテゴリ、メトリック データが送信されるかどうか。
* ログの各カテゴリをストレージ アカウントに保持する期間。
    - リテンション期間が 0 日の場合、ログは永続的に保持されます。 リテンション期間が 0 日の場合、ログは永続的に保持されます。
    - リテンション期間ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合 (たとえば、Event Hubs または OMS オプションだけが選択されている場合)、保持ポリシーは無効になります。
    - 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。

これらの設定は、Azure Portal 内のリソースの診断設定、Azure PowerShell および CLI のコマンド、または [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) を使用して簡単に構成できます。

> [!WARNING]
> コンピューティング リソース (VM や Service Fabric など) のゲスト OS レイヤーからの診断ログとメトリックでは、[出力の構成と選択に別のメカニズム](../azure-diagnostics.md)を使用します。
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>リソース診断ログの収集を有効にする方法
リソース診断ログの収集は、[Resource Manager テンプレートでリソースを作成する一環](./monitoring-enable-diagnostic-logs-using-template.md)で有効にすることも、リソースの作成後にポータルのそのリソースのページで有効にすることもできます。 また、収集は、Azure PowerShell または CLI のコマンドを使用するか、Azure Monitor REST API を使用していつでも有効にすることができます。

> [!TIP]
> これらの手順は、すべてのリソースに直接適用できるわけではありません。 特定のリソースの種類に適用できる具体的な手順については、このページの最後にあるスキーマのリンクを参照してください。
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>ポータルでリソース診断ログの収集を有効にする
リソースの作成後に Azure Portal でリソース診断ログの収集を有効にするには、特定のリソースに移動するか、Azure Monitor に移動します。 Azure Monitor でこれを有効にする手順は次のとおりです。

1. [Azure Portal](http://portal.azure.com) で、Azure Monitor に移動し、**[診断設定]** をクリックします。

    ![Azure Monitor の [監視] セクション](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. 必要に応じて、リソース グループまたはリソースの種類で一覧をフィルタリングして、診断設定を行うリソースをクリックします。

3. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 [診断を有効にする] をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   リソースに既存の設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 [診断設定の追加] をクリックします。

   ![診断設定の追加 - 既存の設定が存在する](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. 設定に名前を付け、データの送信先それぞれのボックスをオンにし、各送信先で使用されるリソースを構成します。 必要に応じて **[リテンション期間 (日数)]** スライダーを使用し、ログを保持する日数を設定します(送信先がストレージ アカウントの場合のみ)。 リテンション期間を 0 にすると、ログが無期限に保存されます。
   
   ![診断設定の追加 - 既存の設定が存在する](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)
    
4. [ **Save**] をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されるとすぐに、診断ログが指定の宛先に送信されます。

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>PowerShell でリソース診断ログの収集を有効にする
Azure PowerShell でリソース診断ログの収集を有効にするには、次のコマンドを使用します。

ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

イベント ハブへの診断ログのストリーミングを有効にするには、次のコマンドを使用します。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Service Bus 規則 ID は、`{Service Bus resource ID}/authorizationrules/{key name}` という形式の文字列です。

Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>CLI を使用してリソース診断ログの収集を有効にする
Azure CLI でリソース診断ログの収集を有効にするには、次のコマンドを使用します。

ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

イベント ハブへの診断ログのストリーミングを有効にするには、次のコマンドを使用します。

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Service Bus 規則 ID は、`{Service Bus resource ID}/authorizationrules/{key name}` という形式の文字列です。

Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>REST API を使用してリソース診断ログの収集を有効にする
Azure Monitor REST API を使用して診断設定を変更する場合は、[こちらのドキュメント](https://msdn.microsoft.com/library/azure/dn931931.aspx)をご覧ください。

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>ポータルでリソース診断設定を管理する
すべてのリソースについて診断設定がセットアップされていることを確認してください。 ポータルの **[モニター]** に移動し、**[診断設定]** を開きます。

![ポータルの [診断ログ] ブレード](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

[その他のサービス] をクリックして、[モニター] セクションを見つけます。

ここでは、診断ログをサポートするすべてのリソースを表示し、フィルター処理することで、有効になっている診断設定があるかどうかを確認できます。 また、ドリルダウンすると、複数の設定が 1 つのリソースに設定されているかどうかを調べて、データの送信先であるストレージ アカウント、Event Hubs 名前空間、Log Analytics ワークスペースを確認することもできます。

![ポータルの診断ログの結果](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

診断設定を追加すると、[診断設定] ビューが表示され、選択したリソースの診断設定を有効化、無効化、または変更できます。

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>リソース診断ログでサポートされているサービス、カテゴリ、スキーマ
サポートされているサービスと、それらのサービスで使用されるログ カテゴリおよびスキーマの完全な一覧については、[この記事](monitoring-diagnostic-logs-schema.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [リソース診断ログを **Event Hubs** にストリーミングする](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API を使用してリソース診断設定を変更する](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md)
