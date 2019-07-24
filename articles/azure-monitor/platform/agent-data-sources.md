---
title: Azure Monitor でのエージェント データ ソースの構成 | Microsoft Docs
description: データ ソースは、Azure Monitor がエージェントや接続されている他のソースから収集するログ データを定義します。  この記事では、Azure Monitor でのデータ ソースの扱い方の概念、それらを構成する方法の詳細、および使用可能なさまざまなデータ ソースの概要について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: d7d4aa89c4dcf2ac9cc0c393e0481cae1f3aeaf2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847015"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Azure Monitor のエージェント データ ソース
Azure Monitor がエージェントから収集するデータは、構成されたデータ ソースによって定義されます。  エージェントからのデータは、一連のレコードを含んだ[ログ データ](data-platform-logs.md)として格納されます。  データ ソースはそれぞれ異なるタイプのレコードを作成し、各レコード タイプは独自のプロパティ セットを持っています。

![ログ データ コレクション](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>データ ソースの概要
次の表に、現在 Azure Monitor で使用できるエージェント データ ソースを示します。  各データ ソースのリンクをクリックすると、それぞれのデータ ソースについて詳しく説明する記事に移動します。   また、収集の手法および頻度に関する情報についても提供します。 


| データ ソース | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [カスタム ログ](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | 着信時 |
| [カスタム ログ](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | 着信時 |
| [IIS ログ](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |ログ ファイル ロールオーバー設定によって異なる |
| [パフォーマンス カウンター](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [パフォーマンス カウンター](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合) |
| [Windows イベント ログ](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 着信時 |


## <a name="configuring-data-sources"></a>データ ソースの構成
データ ソースは、ワークスペースの **[詳細設定]** の **[データ]** メニューから構成します。  すべての構成は、ワークスペース内の接続されているすべてのソースに配信されます。  現時点では、この構成からエージェントを除外することはできません。

![Windows イベントの構成](media/agent-data-sources/configure-events.png)

1. Azure portal で、**[Log Analytics ワークスペース]**、目的のワークスペース、**[詳細設定]** の順に選択します。
2. **[データ]** を選択します。
3. 構成するデータ ソースをクリックします。
4. 構成の詳細については、上記の表のデータ ソース名をクリックして、リンク先のドキュメントを参照してください。


## <a name="data-collection"></a>データ収集
データ ソースの構成は、数分以内に Azure Monitor に直接接続されたエージェントに配信されます。  指定されたデータがエージェントから収集され、各データ ソースに固有の間隔で Azure Monitor に直接配信されます。  詳しくは、各データ ソースのドキュメントを参照してください。

接続された管理グループ内の System Center Operations Manager エージェントの場合、データ ソースの構成は管理パックに変換され、既定で 5 分ごとに管理グループに配信されます。  このエージェントは、他のエージェントと同じように管理パックをダウンロードし、指定されたデータを収集します。 データは、そのソースに応じて、管理サーバーに送信されてそこから Azure Monitor に転送されるか、エージェントが管理サーバーを介さずに Azure Monitor に送信します。 詳しくは、[Azure における監視ソリューションのデータ収集の詳細](../insights/solutions-inventory.md)に関するページを参照してください。  Operations Manager および Azure Monitor への接続と構成の配信頻度の変更の詳細については、[System Center Operations Manager との統合の構成](om-agents.md)に関するページを参照してください。

エージェントが Azure Monitor または Operations Manager に接続できない場合は、そのままデータの収集を続け、接続が確立されたときにデータを送信します。  データの量がクライアントの最大キャッシュ サイズに達した場合、またはエージェントが 24 時間以内に接続を確立できなかった場合は、データが失われることがあります。

## <a name="log-records"></a>ログ レコード
Azure Monitor によって収集されたすべてのログ データは、レコードとしてワークスペースに保存されます。  さまざまなデータ ソースから収集されたレコードは、独自のプロパティ セットを持ち、 **Type** プロパティによって識別されます。  各レコードの種類の詳細については、各データ ソースのドキュメントおよびソリューションを参照してください。

## <a name="next-steps"></a>次の手順
* Azure Monitor に機能を追加し、Azure Monitor ワークスペース内にデータを収集する[監視ソリューション](../insights/solutions.md)について学習します。
* [ログ クエリ](../log-query/log-query-overview.md)について学習し、データ ソースと監視ソリューションから収集されたデータを分析します。  
* データ ソースや監視ソリューションから収集された重要なデータについて事前に通知する[アラート](alerts-overview.md)を構成します。