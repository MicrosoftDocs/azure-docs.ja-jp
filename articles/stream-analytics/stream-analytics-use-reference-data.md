---
title: "Stream Analytics で参照データとルックアップ テーブルを使用する | Microsoft Docs"
description: "Stream Analytics クリエで参照データを使用する"
keywords: "ルックアップ テーブル、参照データ"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 06103be5-553a-4da1-8a8d-3be9ca2aff54
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: eec8c82857ef9e2806c953ae996f9e7970e64392
ms.openlocfilehash: 4d6bfebd9faa4e37de148539e7248f88e711bf43
ms.lasthandoff: 02/07/2017


---
# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Stream Analytics の入力ストリームでの参照データまたはルックアップ テーブルの使用
参照データ (別名、ルックアップテーブル) は、静的または本来はあまり変更されない有限のデータ セットです。参照の実行やデータ ストリームとの相互の関連付けに使用されます。 Azure Stream Analytics のジョブで参照データを使用するには、一般的にクエリで[参照データの結合](https://msdn.microsoft.com/library/azure/dn949258.aspx)を使用します。 Stream Analytics は、参照データのストレージ レイヤーとして Azure Blob Storage を使用し、Azure Data Factory を使用して参照データを Azure Blob Storage に変換、コピー、またはその両方を実行して、[任意の数のクラウドベースとオンプレミスのデータ ストア](../data-factory/data-factory-data-movement-activities.md)から、参照データとして使用することができます。 参照データは、BLOB (入力構成に定義された) のシーケンスとしてモデル化され、BLOB の名前内で指定された日付/時刻の昇順で並べられます。 シーケンス内の最後の BLOB で指定された日付/時刻より**新しい**日付/時刻を使用してシーケンスの末尾に追加することがサポートされている**だけ**です。

Stream Analytics には **BLOB あたり 100 MB の制限**がありますが、ジョブは **[パス パターン]** プロパティを使用して複数の参照 BLOB を処理できます。


## <a name="configuring-reference-data"></a>参照データの構成
参照データを構成するには、まず、タイプが **参照データ**の入力を作成する必要があります。 次の表では、参照データ入力とその説明を作成するときに指定する必要がある各プロパティについて説明します。


<table>
<tbody>
<tr>
<td>プロパティ名</td>
<td>Description</td>
</tr>
<tr>
<td>入力のエイリアス</td>
<td>この入力を参照するジョブ クエリで使用されるわかりやすい名前。</td>
</tr>
<tr>
<td>ストレージ アカウント</td>
<td>BLOB が配置されるストレージ アカウントの名前。 Stream Analytics のジョブと同じサブスクリプションにある場合は、ドロップ ダウンから選択することができます。</td>
</tr>
<tr>
<td>ストレージ アカウント キー</td>
<td>ストレージ アカウントに関連付けられている秘密キー。 ストレージ アカウントが Stream Analytics のジョブと同じサブスクリプションにある場合は、自動的に設定されます。</td>
</tr>
<tr>
<td>ストレージ コンテナー</td>
<td>コンテナーにより、Microsoft Azure BLOB サービスに格納される BLOB が論理的にグループ化されます。 BLOB を BLOB サービスにアップロードするとき、その BLOB のコンテナーを指定する必要があります。</td>
</tr>
<tr>
<td>パスのパターン</td>
<td>指定されたコンテナー内に BLOB を配置するために使用されるパス。 このパス内に、次の 2 つの変数のいずれかまたは両方のインスタンスを指定できます。<BR>{date}、{time}<BR>例 1: products/{date}/{time}/product-list.csv<BR>例 2: products/{date}/product-list.csv
</tr>
<tr>
<td>日付形式 [省略可能]</td>
<td>指定したパス パターン内で {date} を使用した場合は、サポートされている形式のドロップ ダウンから、BLOB を編成する日付形式を選択できます。<BR>例: YYYY/MM/DD、MM/DD/YYYY など</td>
</tr>
<tr>
<td>時刻形式 [省略可能]</td>
<td>指定したパス パターン内で {time} を使用した場合は、サポートされている形式のドロップ ダウンから、BLOB を編成する時刻形式を選択できます。<BR>例: HH、HH/mm、HH-mm</td>
</tr>
<tr>
<td>イベントのシリアル化の形式</td>
<td>クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式が Stream Analytics で認識される必要があります。 参照データの場合、サポートされている形式は CSV と JSON です。</td>
</tr>
<tr>
<td>エンコード</td>
<td>現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>スケジュールに従った参照データの生成
参照データが変更頻度の低いデータセットである場合、参照データの更新をサポートするには、{date} および {time} 置換トークンを使用する入力構成でパス パターンを指定します。 Stream Analytics は、このパス パターンに基づいて、更新された参照データ定義を取得します。 たとえば、日付形式が "**YYYY-MM-DD**" で、時刻形式が "**HH-mm**" の `sample/{date}/{time}/products.csv` パターンは、更新された BLOB `sample/2015-04-16/17-30/products.csv` を UTC タイム ゾーンの 2015 年 4 月 16 日の午後 5 時 30 分に回収するように Stream Analytics に指示します。

> [!NOTE]
> 現在、Stream Analytics のジョブは、コンピューター時間が、BLOB の名前でエンコードされた時刻まで進んだ場合にのみ、BLOB の更新を検索します。 たとえば、ジョブは、`sample/2015-04-16/17-30/products.csv` を、できるだけ早く、ただし、UTC タイム ゾーンの 2015 年 4 月 16 日午後 5 時 30 分以降に検索します。 BLOB のエンコードされた時刻が、検出された最新時刻よりも前の場合、その BLOB は "*決して*" 検索されません。
> 
> 例: たとえば、ジョブによって BLOB `sample/2015-04-16/17-30/products.csv` が検索されると、エンコードされた日付が 2015 年 4 月 16 日午後 5 時 30 分より前のファイルはすべて無視されます。したがって、到着が遅れた `sample/2015-04-16/17-25/products.csv` BLOB が同じコンテナーに作成されると、その BLOB はジョブでは使用されません。
> 
> 同様に、`sample/2015-04-16/17-30/products.csv` が 2015 年 4 月 16 日午後 10 時 03 分にのみ生成され、同じコンテナーに前の日付の BLOB が存在しない場合、2015 年 4 月 16 日午後 10 時 03 分以降はこのファイルを使用し、その時点までは前の参照データを使用します。
> 
> これに対する例外は、ジョブが時間をさかのぼってデータを再処理する必要がある場合、またはジョブが最初に開始される場合です。 開始時点で、ジョブは、指定されたジョブ開始時刻より前に生成された最新の BLOB を探します。 これにより、ジョブの開始時に、 **空ではない** 参照データ セットが必ず存在するようになります。 見つからない場合は、ジョブによって次の診断が表示されます: `Initializing input without a valid reference data blob for UTC time <start time>`。
> 
> 

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) を使用して Stream Analytics で必要な更新された BLOB を作成するタスクを調整し、参照データ定義を更新することができます。 Data Factory は、データの移動や変換を調整し自動化するクラウドベースのデータ統合サービスです。 Data Factory は、 [クラウド ベースとオンプレミスの多数のデータ ストアへの接続](../data-factory/data-factory-data-movement-activities.md) 、および指定された定期的なスケジュールに基づく簡単なデータの移動をサポートします。 事前に定義されたスケジュールで更新される Stream Analytics の参照データを生成するために Data Factory パイプラインを設定する方法の詳細とステップ バイ ステップのガイダンスについては、この [GitHub のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)を確認してください。

## <a name="tips-on-refreshing-your-reference-data"></a>参照データの更新に関するヒント
1. 参照データの BLOB を上書きしても、Stream Analytics は BLOB の再読み込みを行いません。場合によっては、その上書きが原因でジョブが失敗することがあります。 参照データを変更する場合は、ジョブ入力に定義されているのと同じコンテナーおよびパス パターンを使用して新しい BLOB を追加するという方法、およびシーケンス内の最後の BLOB で指定されている日付/時刻より**新しい**日付/時刻を使用するという方法をお勧めします。
2. 参照データの BLOB の並び替えは、BLOB の “最終変更” 時刻では行われ**ません**。{date} および {time} の置換文字を使用して BLOB 名に指定されている時刻と日付によってのみ行われます。
3. 場合によって、ジョブは時間をさかのぼる必要があります。したがって、参照データの BLOB は変更することも削除することもできません。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
モ ノのインターネットからのデータをストリーム分析する管理サービスである、 Stream Analytics の概要です。 このサービスの詳細については、以下の情報をご覧ください。

* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

