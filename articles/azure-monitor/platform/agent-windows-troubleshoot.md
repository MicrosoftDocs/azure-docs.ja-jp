---
title: Windows 用 Log Analytics エージェントに関する問題のトラブルシューティング方法 | Microsoft Docs
description: Azure Monitor の Windows 用 Log Analytics エージェントに関する最も一般的な問題の現象、原因、および解決策について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120795"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Windows 用 Log Analytics エージェントに関する問題のトラブルシューティング方法 

Azure Monitor の Windows 用 Log Analytics エージェントで発生する可能性のあるエラーのトラブルシューティングのヘルプを提供し、それらを解決するための考えられる解決策を提案します。

どの手順でも解決しない場合は、以下のサポート チャネルもご利用いただけます。

* Premier サポート ベネフィットをお持ちのお客様は、[Premier](https://premier.microsoft.com/) でサポート要求を開くことができます。
* Azure サポート契約のお客様は、[Azure portal](https://manage.windowsazure.com/?getsupport=true) でサポート要求を開くことができます。
* Log Analytics のフィードバック ページ ([https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)) で、提出されたアイデアやバグを確認したり、新しく登録します。 

## <a name="important-troubleshooting-sources"></a>重要なトラブルシューティング ソース

 Windows 用 Log Analytics エージェントに関連する問題のトラブルシューティングを支援するために、エージェントではイベントが Windows イベント ログ (特に *アプリケーションとサービス\Operations Manager* 以下) に記録されます。  

## <a name="connectivity-issues"></a>接続に関する問題

エージェントがプロキシ サーバーまたはファイアウォールを介して通信する場合は、ソース コンピューターと Azure Monitor サービスからの通信を妨げる制限が設定されている場合があります。 通信がブロックされる場合、または不適切な構成の場合、エージェントをインストールするときや設定後に追加のワークスペースに報告するようにエージェントを構成するときにワークスペースへの登録が失敗することがあります。また、登録が成功した後にエージェントの通信が失敗します。 このセクションでは、Windows エージェントに関するこの種の問題を解決する方法について説明します。 

次の表に示す以下のポートと URL を許可するようにファイアウォールまたはプロキシが構成されていることを再確認してください。 また、エージェントと Azure Monitor 間のセキュリティで保護された TLS チャネルが妨げられる可能性があるため、Web トラフィックに対して HTTP 検査が有効になっていないことを確認します。  

|エージェントのリソース|Port |Direction |バイパス HTTPS 検査|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |ポート 443 |送信|はい |  
|*.oms.opinsights.azure.com |ポート 443 |送信|はい |  
|*.blob.core.windows.net |ポート 443 |送信|はい |  
|*.azure-automation.net |ポート 443 |送信|はい |  

Azure Government に必要なファイアウォールの情報については、[Azure Government の管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)に関するトピックを参照してください。 

エージェントが Azure Monitor と正常に通信しているかどうかを確認する方法はいくつかあります。

- ワークスペースで [[Azure Log Analytics Agent Health assessment]\(Azure Log Analytics の Agent Health の評価\)](../insights/solution-agenthealth.md)を有効にします。 Agent Health ダッシュボードで **[Count of unresponsive agents]\(応答していないエージェントの数\)** 列を表示すると、エージェントが一覧にあるかどうかを簡単に確認できます。  

- 次のクエリを実行して、レポート先に構成されているワークスペースにエージェントからハートビートが送信されていることを確認します。 <ComputerName> は、マシンの実際の名前に置き換えます。

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    コンピューターがサービスと正常に通信している場合、クエリからは結果が返されます。 クエリから結果が返されない場合は、まず適切なワークスペースに報告するようにエージェントが構成されていることを確認します。 正しく構成されている場合は、手順 3 に進み、Windows イベント ログを検索して、Azure Monitor との通信を妨げている可能性がある問題がエージェントによってログに記録されているかどうかを確認します。

- 接続の問題を特定するもう 1 つの方法は、**TestCloudConnectivity** ツールを実行することです。 このツールは、既定でエージェントと共にフォルダー *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent* にインストールされます。 管理者特権でのコマンド プロンプトから、このフォルダーに移動してツールを実行します。 ツールから結果が返され、テストが失敗した箇所を強調表示されます (たとえば、ブロックされた特定のポート/URL に関連している場合など)。 

    ![TestCloudConnection ツールの実行結果](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- **イベント ソース** - *Health Service Modules*、*HealthService*、*Service Connector* で *Operations Manager* イベント ログを絞り込み、**イベント レベル**の*警告*と*エラー*で絞り込んで、次の表のイベントが書き込まれたかどうかを確認します。 その場合は、発生する可能性がある各イベントについて記載されている解決手順を確認します。

    |イベント ID |source |説明 |解決策 |
    |---------|-------|------------|-----------|
    |2133 と 2129 |Health Service |エージェントからサービスへの接続に失敗しました |このエラーは、エージェントが Azure Monitor サービスと直接またはファイアウォール/プロキシ サーバーを介して通信できない場合に発生する可能性があります。 エージェントのプロキシ設定、またはネットワーク ファイアウォール/プロキシでコンピューターからサービスへの TCP トラフィックが許可されていることを確認します。|
    |2138 |Health Service Modules |プロキシに認証が必要です |エージェント プロキシ設定を構成し、プロキシ サーバーとの認証に必要なユーザー名/パスワードを指定します。 |
    |2129 |Health Service Modules |接続の失敗/SSL ネゴシエーションの失敗 |ネットワーク アダプターの TCP/IP 設定とエージェント プロキシ設定を確認します。|
    |2127 |Health Service Modules |データ送信失敗によるエラー コードの受信 |1 日にときおり発生するだけの場合は、無視することができる単なる偶然の異常の可能性があります。 監視して発生する頻度を把握します。 1 日中頻繁に発生する場合は、まずネットワーク構成とプロキシ設定を確認します。 説明に HTTP エラー コード 404 が含まれていて、エージェントからサービスにデータを送信しようとしたのが初めての場合、内部 404 エラー コードには 500 エラーが含まれます。 404 は見つからないことを意味します。つまり、新しいワークスペース用のストレージ領域がまだプロビジョニング中であることを示します。 次回の再試行時に、データはワークスペースに正常に書き込まれます。 HTTP エラー 403 は、アクセス許可または資格情報の問題を示している可能性があります。 403 エラーには、問題の解決に役立つ詳しい情報が含まれています。|
    |4000 |Service Connector |DNS の名前解決に失敗しました |マシンで、サービスにデータを送信するときに使用されるインターネット アドレスを解決できませんでした。 これは、マシン上の DNS リゾルバーの設定、不適切なプロキシの設定、またはプロバイダーとの一時的な DNS 問題の場合があります。 ときおり発生する場合は、一時的なネットワーク関連の問題が原因の可能性があります。|
    |4001 |Service Connector |サービスへの接続に失敗しました。 |このエラーは、エージェントが Azure Monitor サービスと直接またはファイアウォール/プロキシ サーバーを介して通信できない場合に発生する可能性があります。 エージェントのプロキシ設定、またはネットワーク ファイアウォール/プロキシでコンピューターからサービスへの TCP トラフィックが許可されていることを確認します。|
    |4002 |Service Connector |クエリに応答してサービスから HTTP 状態コード 403 が返されました。 サービスの状態についてサービス管理者に確認してください。 クエリは後で再試行されます。 |このエラーはエージェントの初期登録フェーズ中に書き込まれ、*https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest* のような URL が表示されます。 エラー コード 403 は禁止されていることを意味し、誤入力されたワークスペース ID またはキー、またはコンピューターの日時が不適切なことが原因で発生する可能性があります。 時刻が現在の時刻より 15 分後または前である場合、オンボードは失敗します。 これを修正するには、Windows コンピューターの日付やタイムゾーンを更新します。|

## <a name="data-collection-issues"></a>データ収集の問題

エージェントがインストールされ、構成された 1 つまたは複数のワークスペースにレポートされると、有効なものとそのコンピューターをターゲットにしているものに応じて、構成の受信、パフォーマンス、ログ、またはその他のデータのサービスへの収集または転送が停止します。 次のことを判断する必要があります。

- ワークスペースで使用できないのは特定のデータ型ですか、それともすべてのデータですか。
- データ型はソリューションによって指定されていますか、それともワークスペース データ収集の構成の一部として指定されていますか。
- 何台のコンピューターが影響を受けますか。 ワークスペースに報告しているコンピューターは 1 台ですか、それとも複数ですか。
- それは機能していて 1 日の特定の時点で停止しましたか、それとも収集が実行されたことはありませんでしたか。 
- 使用しているログ検索クエリの構文は正しいですか。 
- エージェントで Azure Monitor からその構成を受信したことはありますか。

トラブルシューティングの最初の手順は、コンピューターからハートビート イベントが送信されているかどうかを確認することです。

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

クエリから結果が返される場合は、特定のデータ型の収集とサービスへの転送が行われていないかどうかを判断する必要があります。 この原因は、更新された構成をエージェントがサービスから受信していないこと、またはエージェントの正常な動作を妨げる他の何らかの症状の可能性があります。 さらにトラブルシューティングを行うには、次の手順を実行します。

1. コンピューターで管理者特権のコマンド プロンプトを開き、`net stop healthservice && net start healthservice` を入力してエージェント サービスを再起動します。
2. *Operations Manager* イベント ログを開き、**イベント ソース** *HealthService* から **イベント ID** *7023、7024、7025、7028*、および *1210* を検索します。  これらのイベントは、エージェントが Azure Monitor から構成を正常に受信しており、コンピューターをアクティブに監視していることを示します。 イベント ID 1210 のイベントの説明では、最後の行に、エージェントの監視範囲に含まれるすべての解決策と分析情報も指定されます。  

    ![イベント ID 1210 の説明](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 数分経ってもクエリ結果または視覚化に想定されるデータが表示されない場合は、*Operations Manager* イベント ログの解決策と分析情報のどちらのデータを表示しているかに応じて、**イベント ソース** *HealthService*  と *Health Service Modules* を検索します。また、**イベント レベル** *警告*と*エラー* で絞り込み、次の表のイベントが書き込まれたかどうかを確認します。

    |イベント ID |source |説明 |解決策 |
    |---------|-------|------------|
    |8000 |HealthService |このイベントは、パフォーマンス、イベント、または収集されたその他のデータ型に関連するワークフローを、ワークスペースへの取り込みのためにサービスに転送できないかどうかを示します。 | ソース HealthService からのイベント ID 2136 がこのイベントと共に書き込まれます。また、プロキシと認証設定の不適切な構成、ネットワークの停止、またはネットワーク ファイアウォール/プロキシでコンピューターからサービスへの TCP トラフィックが許可されていないことが原因で、エージェントがサービスと通信できないことを示します。| 
    |10102 と 10103 |Health Service Modules |ワークフローでデータ ソースを解決できませんでした。 |これは、指定されたパフォーマンス カウンターまたはインスタンスがコンピューター上に存在しないか、ワークスペース データ設定で誤って定義されている場合に発生する可能性があります。 これがユーザー指定の[パフォーマンス カウンター](data-sources-performance-counters.md#configuring-performance-counters)である場合は、指定されている情報が正しい形式であり、ターゲット コンピューター上に存在することを確認します。 |
    |26002 |Health Service Modules |ワークフローでデータ ソースを解決できませんでした。 |これは、指定された Windows イベント ログがコンピューター上に存在しない場合に発生する可能性があります。 コンピューターにこのイベント ログが登録されていないと想定される場合はこのエラーを無視しても問題ありません。それ以外の場合、これはユーザー指定の[イベント ログ](data-sources-windows-events.md#configuring-windows-event-logs)であり、指定された情報が正しいことを確認します。 |

