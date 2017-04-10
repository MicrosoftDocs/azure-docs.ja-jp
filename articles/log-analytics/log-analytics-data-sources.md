---
title: "OMS Log Analytics のデータ ソースの構成 | Microsoft Docs"
description: "データ ソースは、Log Analytics がエージェントや接続されている他のソースから収集するデータを定義します。  この記事では、Log Analytics でのデータ ソースの扱い方の概念、それらを構成する方法の詳細、および使用可能なさまざまなデータ ソースの概要について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 653696779e612726ed5b75829a5c6ed2615553d7
ms.openlocfilehash: cec0ceb0da57150e4bdd9f9a0f6d3e751c108523


---
# <a name="data-sources-in-log-analytics"></a>Log Analytics のデータ ソース
Log Analytics は、OMS ワークスペース内の接続されたソースからデータを収集して OMS リポジトリに格納します。  Log Analytics が収集するデータは、構成するデータ ソースによって定義されます。  OMS リポジトリ内のデータは、一連のレコードとして保存されます。  データ ソースはそれぞれ異なるタイプのレコードを作成し、各レコード タイプは独自のプロパティ セットを持っています。

![Log Analytics のデータ収集](./media/log-analytics-data-sources/overview.png)

データ ソースは OMS ソリューションとは異なります。  OMS ソリューションも接続されたソースからデータを収集し、OMS リポジトリ内にレコードを作成しますが、ソリューションはソリューション ギャラリーからワークスペースに追加することができ、通常、OMS ポータルに追加の分析ツールを提供します。  

## <a name="summary-of-data-sources"></a>データ ソースの概要
次の表に、現在 Log Analytics で使用できるデータ ソースを示します。  各データ ソースのリンクをクリックすると、それぞれのデータ ソースについて詳しく説明する記事に移動します。

| データ ソース | イベントの種類 | 説明 |
|:--- |:--- |:--- |
| [カスタム ログ](log-analytics-data-sources-custom-logs.md) |\<LogName\>_CL |Windows エージェントまたは Linux エージェント上の、ログ情報を含んだテキスト ファイル。 |
| [Windows イベント ログ](log-analytics-data-sources-windows-events.md) |イベント |Windows コンピューター上のイベント ログから収集されたイベント。 |
| [Windows パフォーマンス カウンター](log-analytics-data-sources-performance-counters.md) |Perf |Windows コンピューターから収集されたパフォーマンス カウンター。 |
| [Linux パフォーマンス カウンター](log-analytics-data-sources-performance-counters.md) |Perf |Linux コンピューターから収集されたパフォーマンス カウンター。 |
| [IIS ログ](log-analytics-data-sources-iis-logs.md) |W3CIISLog |インターネット インフォメーション サービス ログ (W3C 形式)。 |
| [Syslog](log-analytics-data-sources-syslog.md) |syslog |Windows コンピューターまたは Linux コンピューターの Syslog イベント。 |

## <a name="configuring-data-sources"></a>データ ソースの構成
Log Analytics の **[設定]** の **[データ]** メニューからデータ ソースを構成します。  構成はすべて、OMS ワークスペース内の接続されているすべてのソースに配信されます。  現時点では、この構成からエージェントを除外することはできません。

![Windows イベントの構成](./media/log-analytics-data-sources/configure-events.png)

1. OMS コンソールで、 **[設定]** タイルを選択します。
2. **[データ]**を選択します。
3. 構成するデータ ソースをクリックします。
4. 構成の詳細については、上記の表のデータ ソース名をクリックして、リンク先のドキュメントを参照してください。

## <a name="data-collection"></a>データ収集
データ ソースの構成は、数分以内に Log Analytics に直接接続されたエージェントに配信されます。  指定されたデータがエージェントから収集され、各データ ソースに固有の間隔で Log Analytics に直接配信されます。  詳しくは、各データ ソースのドキュメントを参照してください。

接続された管理グループ内の System Center Operations Manager (SCOM) エージェントの場合、データ ソースの構成は管理パックに変換され、既定で 5 分ごとに管理グループに配信されます。  このエージェントは、他のエージェントと同じように管理パックをダウンロードし、指定されたデータを収集します。 データは、そのソースに応じて、管理サーバーに送信されてそこから Log Analytics に転送されるか、またはエージェントが管理サーバーを介さずに Log Analytics に送信します。 詳細については、「[OMS の機能とソリューションにおけるデータ収集の詳細](log-analytics-add-solutions.md#data-collection-details)」を参照してください。  SCOM および OMS と接続し、構成の配信頻度を変更する方法の詳細については、[System Center Operations Manager との統合の構成](log-analytics-om-agents.md)に関するページを参照してください。

エージェントが Log Analytics または Operations Manager に接続できない場合は、そのままデータの収集を続け、接続が確立されたときにデータを送信します。  データの量がクライアントの最大キャッシュ サイズに達した場合、またはエージェントが 24 時間以内に接続を確立できなかった場合は、データが失われることがあります。

## <a name="log-analytics-records"></a>Log Analytics のレコード
Log Analytics によって収集されたデータはすべて、レコードとして OMS リポジトリに保存されます。  さまざまなデータ ソースから収集されたレコードは、独自のプロパティ セットを持ち、 **Type** プロパティによって識別されます。  各レコードの種類の詳細については、各データ ソースのドキュメントおよびソリューションを参照してください。

## <a name="next-steps"></a>次のステップ
* Log Analytics に機能を追加し、OMS リポジトリにデータを収集する [ソリューション](log-analytics-add-solutions.md) について学習します。
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。  
* データ ソースやソリューションから収集された重要なデータについて事前に通知する[アラート](log-analytics-alerts.md)を構成します。



<!--HONumber=Jan17_HO4-->


