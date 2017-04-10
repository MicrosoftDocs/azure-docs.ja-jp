---
title: "OMS Log Analytics での Windows イベント ログの収集と分析 | Microsoft Docs"
description: "Windows イベント ログは、Log Analytics で使用される最も一般的なデータ ソースの&1; つです。  この記事では、Windows イベント ログの収集を構成する方法と OMS リポジトリに作成されるレコードの詳細について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 653696779e612726ed5b75829a5c6ed2615553d7
ms.openlocfilehash: b6627ed7e3b08e0a94dec229d735114b3ed1b9df


---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Log Analytics での Windows イベント ログのデータ ソース
Windows イベント ログは、多くのアプリケーションが書き込みを行うため、Windows エージェントを使用してデータを収集する際の最も一般的な[データ ソース](log-analytics-data-sources.md) の&1; つです。  システムやアプリケーションなどの標準ログに加えて、アプリケーションによって作成される監視が必要なカスタム ログを指定して、イベントを収集できます。

![Windows イベント](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows イベント ログの構成
Windows イベント ログは、[Log Analytics の [設定] の [データ] メニュー](log-analytics-data-sources.md#configuring-data-sources)から構成します。

Log Analytics は、設定で指定されている Windows イベント ログからのイベントのみを収集します。  イベント ログを追加するには、追加するログの名前を入力して、**+** をクリックします。  各ログについて、選択した重大度レベルのイベントのみが収集されます。  各ログで収集する重大度レベルにチェックマークを入れます。  イベントをフィルター処理するための追加条件を指定することはできません。

イベント ログの名前を入力し始めると、Log Analytics によって、共通するイベント ログ名の候補が表示されます。 追加しようとするログが候補リストに表示されない場合、ログの完全な名前を入力して追加することもできます。 ログの完全な名前はイベント ビューアーを使用して確認できます。 イベント ビューアーで、ログの *[プロパティ]* ページを開き、*[フル ネーム]* フィールドの文字列をコピーします。

![Windows イベントの構成](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>データ収集
Log Analytics は、監視対象のイベントが作成された時点で、選択した重大度に一致するイベントをそのイベント ログから収集します。  エージェントは、収集元の場所を各イベント ログに記録します。  エージェントが一定時間オフラインになった場合、Log Analytics は中止した箇所からイベントの収集を再開します。オフラインの間に作成されたイベントも収集されます。  エージェントがオフラインのときに、未収集のイベントにラップされたイベント ログが上書きされた場合は、これらのイベントが収集されない可能性もあります。



## <a name="windows-event-records-properties"></a>Windows イベント レコードのプロパティ
Windows イベント レコードの型は **Event** になり、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Computer |イベント収集元のコンピューターの名前。 |
| EventCategory |イベントのカテゴリ。 |
| EventData |元の形式のすべてのイベント データ。 |
| EventID |イベントの番号。 |
| EventLevel |数値形式で示すイベントの重大度。 |
| EventLevelName |テキスト形式で示すイベントの重大度。 |
| EventLog |イベント収集元のイベント ログの名前。 |
| ParameterXml |XML 形式でのイベント パラメーターの値。 |
| ManagementGroupName |System Center Operations Manager エージェントの管理グループの名前。  その他のエージェントの場合、この値は AOI-<workspace ID> です。 |
| RenderedDescription |イベントの説明とパラメーターの値 |
| Source |イベントのソース。 |
| SourceSystem |イベント収集元のエージェントの種類。 <br> OpsManager – Windows エージェント、直接接続または Operations Manager による管理 <br> Linux – すべての Linux エージェント  <br> AzureStorage – Azure 診断 |
| TimeGenerated |イベントが Windows で作成された日付と時刻。 |
| UserName |イベントのログを記録したアカウントのユーザー名。 |

## <a name="log-searches-with-windows-events"></a>Windows イベントのログ検索
次の表は、Windows イベント レコードを取得するログ検索のさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--- |:--- |
| Type=Event |すべての Windows イベント。 |
| Type=Event EventLevelName=error |重大度が「エラー」のすべての Windows イベント。 |
| Type=Event &#124; Measure count() by Source |ソース別の Windows イベントの数。 |
| Type=Event EventLevelName=error &#124; Measure count() by Source |ソース別の Windows エラー イベントの数。 |

## <a name="next-steps"></a>次のステップ
* 分析のために別の [データ ソース](log-analytics-data-sources.md) を収集するように Log Analytics を構成します。
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。  
* [カスタム フィールド](log-analytics-custom-fields.md) を使用し、イベント レコードを個別のフィールドに解析します。
* お使いの Windows エージェントから [パフォーマンス カウンターの収集](log-analytics-data-sources-performance-counters.md) を構成します。




<!--HONumber=Jan17_HO4-->


