---
title: Azure Monitor の Log Analytics エージェントのデータ ソース
description: データ ソースは、Azure Monitor がエージェントや接続されている他のソースから収集するログ データを定義します。  この記事では、Azure Monitor でのデータ ソースの扱い方の概念、それらを構成する方法の詳細、および使用可能なさまざまなデータ ソースの概要について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 51cdee9c899feeb003a7d6301d2da0749fad65e9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041938"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Azure Monitor の Log Analytics エージェントのデータ ソース
Azure Monitor で [Log Analytics](./log-analytics-agent.md) エージェントを使用して仮想マシンから収集されるデータは、[Log Analytics ワークスペース](../logs/data-platform-logs.md)で構成するデータ ソースによって定義されます。   データ ソースはそれぞれ異なるタイプのレコードを作成し、各レコード タイプは独自のプロパティ セットを持っています。

> [!IMPORTANT]
> この記事では、Azure Monitor で使用されるエージェントの 1 つである [Log Analytics エージェント](./log-analytics-agent.md)のデータ ソースについて説明します。 他のエージェントは異なるデータを収集し、異なる方法で構成されます。 使用可能なエージェントとそれらが収集できるデータの一覧については、「[Azure Monitor エージェントの概要](agents-overview.md)」を参照してください。

![ログ データ コレクション](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> この記事で説明するデータ ソースは、Log Analytics エージェントを実行している仮想マシンにのみ適用されます。 

## <a name="summary-of-data-sources"></a>データ ソースの概要
次の表に、現在 Log Analytics で使用できるエージェントのデータ ソースを示します。  各データ ソースのリンクをクリックすると、それぞれのデータ ソースについて詳しく説明する記事に移動します。   また、収集の手法および頻度に関する情報についても提供します。 


| データ ソース | プラットフォーム | Log Analytics エージェント | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [カスタム ログ](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | 着信時 |
| [カスタム ログ](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | 着信時 |
| [IIS ログ](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |ログ ファイル ロールオーバー設定によって異なる |
| [パフォーマンス カウンター](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [パフォーマンス カウンター](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合) |
| [Windows イベント ログ](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 着信時 |


## <a name="configuring-data-sources"></a>データ ソースの構成
Log Analytics エージェントのデータ ソースを構成するには、Azure portal の **[Log Analytics ワークスペース]** メニューに移動し、ワークスペースを選択します。 **[エージェント構成]** をクリックします。 構成するデータ ソースのタブを選択します。 上記の表のリンクをクリックすると、各データ ソースとそれらの構成の詳細に関するドキュメントを参照できます。

すべての構成は、そのワークスペースに接続されているすべてのエージェントに伝達されます。  この構成から、接続されている任意のエージェントを除外することはできません。

[![Windows イベントの構成](media/agent-data-sources/configure-events.png)](media/agent-data-sources/configure-events.png#lightbox)



## <a name="data-collection"></a>データ コレクション
データ ソースの構成は、数分以内に Azure Monitor に直接接続されたエージェントに配信されます。  指定されたデータがエージェントから収集され、各データ ソースに固有の間隔で Azure Monitor に直接配信されます。  詳しくは、各データ ソースのドキュメントを参照してください。

接続された管理グループ内の System Center Operations Manager エージェントの場合、データ ソースの構成は管理パックに変換され、既定で 5 分ごとに管理グループに配信されます。  このエージェントは、他のエージェントと同じように管理パックをダウンロードし、指定されたデータを収集します。 データは、そのソースに応じて、管理サーバーに送信されてそこから Azure Monitor に転送されるか、エージェントが管理サーバーを介さずに Azure Monitor に送信します。 詳しくは、[Azure における監視ソリューションのデータ収集の詳細](../monitor-reference.md)に関するページを参照してください。  Operations Manager および Azure Monitor への接続と構成の配信頻度の変更の詳細については、[System Center Operations Manager との統合の構成](./om-agents.md)に関するページを参照してください。

エージェントが Azure Monitor または Operations Manager に接続できない場合は、そのままデータの収集を続け、接続が確立されたときにデータを送信します。  データの量がクライアントの最大キャッシュ サイズに達した場合、またはエージェントが 24 時間以内に接続を確立できなかった場合は、データが失われることがあります。

## <a name="log-records"></a>ログ レコード
Azure Monitor によって収集されたすべてのログ データは、レコードとしてワークスペースに保存されます。  さまざまなデータ ソースから収集されたレコードは、独自のプロパティ セットを持ち、 **Type** プロパティによって識別されます。  各レコードの種類の詳細については、各データ ソースのドキュメントおよびソリューションを参照してください。

## <a name="next-steps"></a>次のステップ
* Azure Monitor に機能を追加し、Azure Monitor ワークスペース内にデータを収集する[監視ソリューション](../insights/solutions.md)について学習します。
* [ログ クエリ](../logs/log-query-overview.md)について学習し、データ ソースと監視ソリューションから収集されたデータを分析します。  
* データ ソースや監視ソリューションから収集された重要なデータについて事前に通知する[アラート](../alerts/alerts-overview.md)を構成します。