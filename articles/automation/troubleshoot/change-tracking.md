---
title: Azure Automation の Change Tracking とインベントリに関する問題のトラブルシューティング
description: この記事では、Azure Automation の Change Tracking とインベントリ機能に関する問題のトラブルシューティングを行い、解決する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: dd027f94edad580836f0afb8c7293c81ca77605a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723828"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Change Tracking と Inventory に関する問題のトラブルシューティング

この記事では、Azure Automation の Change Tracking とインベントリの問題のトラブルシューティングを行い、解決する方法について説明します。 Change Tracking とインベントリの一般的な情報については、「[変更履歴とインベントリの概要](../change-tracking/overview.md)」を参照してください。

## <a name="general-errors"></a>一般エラー

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>シナリオ:マシンが違うアカウントに既に登録されている

### <a name="issue"></a>問題

次のエラー メッセージが表示されます。

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

マシンが既に Change Tracking 用の別のワークスペースにデプロイされています。

### <a name="resolution"></a>解決方法

1. マシンが適切なワークスペースにレポートしていることを確認します。 これを確認する方法については、「[Azure Monitor へのエージェント接続を確認する](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)」を参照してください。 このワークスペースが Azure Automation アカウントにリンクされていることも確認します。 確認するには、自分の Automation アカウントに移動して、 **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

1. Automation アカウントにリンクされた Log Analytics ワークスペースにマシンが表示されることを確認します。 Log Analytics ワークスペースで、次のクエリを実行します。

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   クエリ結果にマシンが表示されない場合は、最近チェックインされていません。 ローカルの構成に問題がある可能性があります。 Log Analytics エージェントを再インストールする必要があります。

   コンピューターがクエリ結果に一覧表示されている場合は、Solutions プロパティの下に **changeTracking** が一覧表示されていることを確認します。 これにより、変更履歴とインベントリに登録されていることを確認できます。 そうでない場合は、スコープ構成に問題がないかどうかを確認します。 スコープの構成では、変更履歴とインベントリ用に構成されるマシンが決定されます。 ターゲット コンピューターのスコープ構成を構成するには、「[Automation アカウントで変更履歴とインベントリを有効にする](../change-tracking/enable-from-automation-account.md)」を参照してください。

   ワークスペースで、次のクエリを実行します。

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` という結果が表示される場合、ワークスペースに定義されたクォータに達したため、データの保存が停止されています。 ワークスペースで、 **[使用量と推定コスト]** に移動します。 より多くのデータを使用できる新しい **価格レベル** を選択するか、 **[日次上限]** をクリックして上限を削除します。

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="使用量と推定コスト。" lightbox="./media/change-tracking/change-tracking-usage.png":::

問題が解決しない場合は、「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md)」の手順に従って、Windows 用のハイブリッド worker を再インストールしてください。 Linux の場合は、「[Linux Hybrid Runbook Worker を展開する](../automation-linux-hrw-install.md)」の手順に従います。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>シナリオ: Windows マシンに Change Tracking と Inventory のレコードが表示されない

#### <a name="issue"></a>問題

Windows マシンで Change Tracking とインベントリが有効であるのに、この機能の結果が表示されません。

#### <a name="cause"></a>原因

このエラーには次の原因が考えられます。

* Windows 用 Azure Log Analytics エージェントが実行されていないません。
* Automation アカウントに戻る通信がブロックされています。
* Change Tracking と Inventory 用の管理パックがダウンロードされていません。
* 有効になっている VM の複製元が、Windows 用の Log Analytics エージェントがインストールされた状態でシステム準備 (sysprep) を使用して準備されなかった複製マシンである可能性があります。

#### <a name="resolution"></a>解決方法

Log Analytics エージェント マシン上で **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** に移動し、次のコマンドを実行します。

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

それでもサポートが必要な場合は、診断情報を収集して、サポートにお問い合わせください。

> [!NOTE]
> 既定では、Log Analytics エージェントによってエラー トレースが有効になります。 前の例のように詳細なエラー メッセージを有効にするには、`VER` パラメーターを使用します。 情報トレースの場合は、`StartTracing.cmd` を呼び出すときに `INF` を使用します。

##### <a name="log-analytics-agent-for-windows-not-running"></a>Windows 用 Log Analytics エージェントが実行されていない

Windows 用 Log Analytics エージェント (**HealthService.exe**) がマシン上で実行されていることを確認します。

##### <a name="communication-to-automation-account-blocked"></a>Automation アカウントへの通信がブロックされる

マシン上でイベント ビューアーをチェックして、`changetracking` という単語が含まれているイベントを探します。

Change Tracking とインベントリを動作させるために許可する必要があるアドレスとポートについては、「[ネットワークを構成する](../automation-hybrid-runbook-worker.md#network-planning)」を参照してください。

##### <a name="management-packs-not-downloaded"></a>管理パックがダウンロードされていない

Change Tracking とインベントリの次の管理パックがローカルにインストールされていることを確認します。

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>sysprep されていない複製マシンの VM

複製されたイメージを使用する場合は、まずイメージを sysprep し、次に Windows 用 Log Analytics エージェントをインストールします。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>シナリオ: Linux マシン上に Change Tracking と Inventory の結果がない

#### <a name="issue"></a>問題

Linux マシンで Change Tracking とインベントリが有効であるのに、この機能の結果が表示されません。 

#### <a name="cause"></a>原因
この問題には次のような原因が考えられます。
* Linux 用 Log Analytics エージェントが実行されていません。
* Linux 用 Log Analytics エージェントが正しく構成されていません。
* ファイルの整合性の監視 (FIM) の競合があります。

#### <a name="resolution"></a>解決方法 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 用 Log Analytics エージェントが実行されていない

Linux 用 Log Analytics エージェント (**omsagent**) のデーモンがマシン上で実行されていることを確認します。 自分の Automation アカウントにリンクされた Log Analytics ワークスペースで、次のクエリを実行します。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

クエリ結果にマシンが表示されない場合は、最近チェックインされていません。 ローカルの構成に問題がある可能性があるため、エージェントを再インストールする必要があります。 インストールと構成の詳細については、「[Log Analytics エージェントを使用してログ データを収集する](../../azure-monitor/agents/log-analytics-agent.md)」を参照してください。

マシンがクエリ結果に表示される場合は、スコープの構成を確認します。 「[Azure Monitor での監視ソリューションのターゲット設定](../../azure-monitor/insights/solution-targeting.md)」を参照してください。

この問題のトラブルシューティングの詳細については、「[問題点: Linux データが表示されない](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data)」を参照してください。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 用 Log Analytics エージェントが正しく構成されていない

Linux 用 Log Analytics エージェントは、OMS Log Collector ツールを使用したログおよびコマンド ライン出力収集用に正しく構成されていない可能性があります。 「[変更履歴とインベントリの概要](../change-tracking/overview.md)」を参照してください。

##### <a name="fim-conflicts"></a>FIM の競合

Azure Security Center の FIM 機能で、Linux ファイルの整合性が正しく検証されていない可能性があります。 FIM が動作し、Linux ファイル監視用に正しく構成されていることを確認します。 「[変更履歴とインベントリの概要](../change-tracking/overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。
* Azure サポート インシデントを送信する。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートの要求]** をクリックします。