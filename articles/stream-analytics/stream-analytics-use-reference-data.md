---
title: Azure Stream Analytics での参照に参照データを使用する
description: この記事では、Azure Stream Analytics ジョブのクエリ デザインで参照データを使用してデータを参照または関連付ける方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ed50dfd7e3c423c1c26a7dc19ae60dcb319f1850
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621613"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Stream Analytics での参照に参照データを使用する

参照データ (別名、ルックアップ テーブル) は、静的または本来はあまり変更されない有限のデータ セットであり、データ ストリームのルックアップや増大を行うために使用されます。 たとえば、IoT のシナリオでは、センサーに関する (変化が頻繁ではない) メタデータを参照データに格納し、リアルタイムの IoT データ ストリームと結合することができます。 Azure Stream Analytics は、参照データをメモリに読み込んで、待機時間の短いストリーム処理を実現します。 Azure Stream Analytics ジョブで参照データを使用するには、一般にクエリで[参照データの結合](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics)を使用します。 

Stream Analytics は、参照データの格納レイヤーとして Azure BLOB ストレージおよび Azure SQL Database をサポートします。 [任意の数のクラウド ベースおよびオンプレミスのデータ ストア](../data-factory/copy-activity-overview.md)を使用するために、Azure Data Factory から参照データを BLOB ストレージに変換またはコピー (あるいは両方) することもできます。

## <a name="azure-blob-storage"></a>Azure BLOB ストレージ

参照データは、BLOB (入力構成に定義された) のシーケンスとしてモデル化され、BLOB の名前内で指定された日付/時刻の昇順で並べられます。 シーケンス内の最後の BLOB で指定された日付/時刻より**新しい**日付/時刻を使用してシーケンスの末尾に追加することがサポートされている**だけ**です。

### <a name="configure-blob-reference-data"></a>BLOB 参照データを構成する

参照データを構成するには、まず、タイプが **参照データ**の入力を作成する必要があります。 次の表では、参照データ入力とその説明を作成するときに指定する必要がある各プロパティについて説明します。

|**プロパティ名**  |**説明**  |
|---------|---------|
|入力のエイリアス   | この入力を参照するジョブ クエリで使用されるわかりやすい名前。   |
|ストレージ アカウント   | BLOB が配置されるストレージ アカウントの名前。 Stream Analytics のジョブと同じサブスクリプションにある場合は、ドロップ ダウンから選択することができます。   |
|ストレージ アカウント キー   | ストレージ アカウントに関連付けられている秘密キー。 ストレージ アカウントが Stream Analytics のジョブと同じサブスクリプションにある場合は、自動的に設定されます。   |
|ストレージ コンテナー   | コンテナーにより、Microsoft Azure Blob service に格納される BLOB が論理的にグループ化されます。 BLOB を Blob service にアップロードするとき、その BLOB のコンテナーを指定する必要があります。   |
|パスのパターン   | 指定されたコンテナー内に BLOB を配置するために使用されるパス。 このパス内に、次の 2 つの変数のいずれかまたは両方のインスタンスを指定できます。<BR>{date}、{time}<BR>例 1: products/{date}/{time}/product-list.csv<BR>例 2: products/{date}/product-list.csv<BR>例 3: product-list.csv<BR><br> 指定されたパスに、BLOB が存在しない場合、BLOB が使用可能になるまで、Stream Analytics ジョブは無期限に待機します。   |
|日付形式 [省略可能]   | 指定したパス パターン内で {date} を使用した場合は、サポートされている形式のドロップ ダウンから、BLOB を編成する日付形式を選択できます。<BR>例:YYYY/MM/DD、MM/DD/YYYY など   |
|時刻形式 [省略可能]   | 指定したパス パターン内で {time} を使用した場合は、サポートされている形式のドロップ ダウンから、BLOB を編成する時刻形式を選択できます。<BR>例:HH、HH/mm、HH-mm  |
|イベントのシリアル化の形式   | クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式が Stream Analytics で認識される必要があります。 参照データの場合、サポートされている形式は CSV と JSON です。  |
|エンコード   | 現時点でサポートされているエンコード形式は UTF-8 だけです。  |

### <a name="static-reference-data"></a>静的参照データ

参照データの変更が予期されない場合は、入力構成に静的パスを指定することで、静的参照データのサポートを有効にできます。 Azure Stream Analytics は、指定されたパスから BLOB を取得します。 置換トークン ({date} と {time}) は必要ありません。 参照データは Stream Analytics 内で不変であるため、静的参照データ BLOB の上書きは推奨されません。

### <a name="generate-reference-data-on-a-schedule"></a>スケジュールに従って参照データを生成する

参照データが変更頻度の低いデータセットである場合、参照データの更新をサポートするには、{date} および {time} 置換トークンを使用する入力構成でパス パターンを指定します。 Stream Analytics は、このパス パターンに基づいて、更新された参照データ定義を取得します。 たとえば、日付形式が "**YYYY-MM-DD**" で、時刻形式が "**HH-mm**" の `sample/{date}/{time}/products.csv` パターンは、更新された BLOB `sample/2015-04-16/17-30/products.csv` を UTC タイム ゾーンの 2015 年 4 月 16 日の午後 5 時 30 分に回収するように Stream Analytics に指示します。

Azure Stream Analytics は、更新された参照データ BLOB を、1 分間隔で自動的にスキャンします。 わずかな遅延ありでタイムスタンプ 10:30:00 で BLOB をアップロードした場合 (たとえば、10:30:30)、この BLOB を参照する Stream Analytics ジョブでわずかな遅延が認識されます。 このようなシナリオを避けるためには、対象の有効時刻 (この例では 10:30:00) より前に、BLOB をアップロードし、Azure Stream Analytics ジョブが十分な時間を持ってメモリ内を探したりロードし、操作を実行できるようにすることをお勧めします。 

> [!NOTE]
> 現在、Stream Analytics のジョブは、コンピューター時間が、BLOB の名前でエンコードされた時刻まで進んだ場合にのみ、BLOB の更新を検索します。 たとえば、ジョブは、`sample/2015-04-16/17-30/products.csv` を、できるだけ早く、ただし、UTC タイム ゾーンの 2015 年 4 月 16 日午後 5 時 30 分以降に検索します。 BLOB のエンコードされた時刻が、検出された最新時刻よりも前の場合、その BLOB は "*決して*" 検索されません。
> 
> たとえば、ジョブによって BLOB `sample/2015-04-16/17-30/products.csv` が検索されると、エンコードされた日付が 2015 年 4 月 16 日午後 5 時 30 分より前のファイルはすべて無視されるため、到着が遅れた `sample/2015-04-16/17-25/products.csv` BLOB が同じコンテナーに作成された場合でも、それはジョブでは使用されません。
> 
> 同様に、`sample/2015-04-16/17-30/products.csv` が 2015 年 4 月 16 日午後 10 時 03 分にのみ生成され、同じコンテナーに前の日付の BLOB が存在しない場合、2015 年 4 月 16 日午後 10 時 03 分以降はこのファイルを使用し、その時点までは前の参照データを使用します。
> 
> これに対する例外は、ジョブが時間をさかのぼってデータを再処理する必要がある場合、またはジョブが最初に開始される場合です。 開始時点で、ジョブは、指定されたジョブ開始時刻より前に生成された最新の BLOB を探します。 これにより、ジョブの開始時に、 **空ではない** 参照データ セットが必ず存在するようになります。 見つからない場合は、ジョブによって次の診断が表示されます: `Initializing input without a valid reference data blob for UTC time <start time>`。

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) を使用して Stream Analytics で必要な更新された BLOB を作成するタスクを調整し、参照データ定義を更新することができます。 Data Factory は、データの移動や変換を調整し自動化するクラウドベースのデータ統合サービスです。 Data Factory は、 [クラウド ベースとオンプレミスの多数のデータ ストアへの接続](../data-factory/copy-activity-overview.md) 、および指定された定期的なスケジュールに基づく簡単なデータの移動をサポートします。 事前に定義されたスケジュールで更新される Stream Analytics の参照データを生成するために Data Factory パイプラインを設定する方法の詳細とステップ バイ ステップのガイダンスについては、この [GitHub のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)を確認してください。

### <a name="tips-on-refreshing-blob-reference-data"></a>BLOB 参照データの更新に関するヒント

1. 参照データ BLOB は不変であるため、上書きしないでください。
2. 参照データを更新するための推奨方法は次のとおりです。
    * パス パターンで {date}/{time} を使用する
    * ジョブ入力に定義されているコンテナーとパス パターンを使用して、新しい BLOB を追加する
    * シーケンスの最後の BLOB で指定されている日付/時刻よりも**後の**値を使用する
3. 参照データの BLOB の並び替えは、BLOB の "最終変更" 時刻では行われ**ません**。{date} および {time} の置換文字を使用して BLOB 名に指定されている時刻と日付によってのみ行われます。
3. 多数の BLOB を列挙する必要がないように、今後処理を行う予定がない非常に古い BLOB は削除することを検討してください。 ASA では、再起動のような一部のシナリオで少量の再処理が必要になる可能性がある点に注意してください。

## <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database の参照データは、Stream Analytics ジョブによって取得され、処理のためにスナップショットとしてメモリに格納されます。 参照データのスナップショットも、構成設定で指定したストレージ アカウントのコンテナーに格納されます。 ジョブを開始すると、コンテナーは自動作成されます。 ジョブが停止するか失敗状態になると、自動作成されたコンテナーはジョブの再開時に削除されます。  

参照データが変化の遅いデータ セットである場合、ジョブで使用されるスナップショットを定期的に更新する必要があります。 Stream Analytics では、Azure SQL Database 入力接続を構成するときにリフレッシュ レートを設定できます。 Stream Analytics ランタイムは、リフレッシュ レートによって指定された間隔で Azure SQL Database に対してクエリを実行します。 サポートされている最速のリフレッシュ レートは 1 分に 1 回です。 更新のたびに、Stream Analytics は指定されたストレージ アカウントに新しいスナップショットを格納します。

Stream Analytics には、Azure SQL Database に対するクエリの実行に関する 2 つのオプションがあります。 スナップショット クエリは必須であり、各ジョブに含まれている必要があります。 Stream Analytics は、リフレッシュ間隔に基づいて定期的にスナップショット クエリを実行し、クエリの結果 (スナップショット) を参照データ セットとして使用します。 スナップショット クエリはほとんどのシナリオに適合しますが、大規模データ セットと高速リフレッシュ レートでパフォーマンスの問題が発生した場合、デルタ クエリ オプションを使用できます。 クエリは、60 秒以内に参照データセットを返せないと、タイムアウトになります。

デルタ クエリ オプションでは、Stream Analytics はスナップショット クエリを最初に実行してベースライン参照データ セットを取得します。 その後 Stream Analytics は、リフレッシュ間隔に基づいてデルタ クエリを定期的に実行し、増分の変更を取得します。 これらの増分変更が参照データ セットに継続的に適用されることで、更新された状態が維持されます。 デルタ クエリの使用により、ストレージ コストおよびネットワーク I/O 操作を削減できる場合があります。

### <a name="configure-sql-database-reference"></a>SQL Database 参照を構成する

SQL Database 参照データを構成するには、まず**参照データ**入力を作成する必要があります。 次の表は、参照データ入力の作成中に指定する必要がある各プロパティとその説明を示しています。 詳細については、[SQL Database からの参照データの Azure Stream Analytics ジョブでの使用](sql-reference-data.md)に関するページを参照してください。

|**プロパティ名**|**説明**  |
|---------|---------|
|入力のエイリアス|この入力を参照するジョブ クエリで使用されるわかりやすい名前。|
|Subscription|サブスクリプションの選択|
|Database|参照データを含む Azure SQL Database。|
|ユーザー名|Azure SQL Database に関連付けられているユーザー名。|
|パスワード|Azure SQL Database に関連付けられているパスワード。|
|定期的に更新|このオプションでは、リフレッシュ レートを選択できます。 "On"(オン) を選択すると、リフレッシュ レートを DD:HH:MM で指定できます。|
|スナップショット クエリ|これは、SQL Database から参照データを取得する既定のクエリ オプションです。|
|デルタ クエリ|データ セットが大きくリフレッシュ レートが短い高度なシナリオでは、デルタ クエリの追加を選択します。|

## <a name="size-limitation"></a>サイズ制限

Stream Analytics は、**最大 300 MB のサイズ**の参照データをサポートします。 この 300 MB という参照データの最大サイズの制限は、単純なクエリでのみ達成可能です。 クエリが複雑になり、時間帯集計、一時的な結合、一時的な分析関数などのステートフル処理が含まれるようになると、サポートされる参照データの最大サイズは減少することが予期されます。 Azure Stream Analytics が参照データを読み込むことができないときに、複雑な操作が実行された場合、ジョブはメモリ不足になり、操作は失敗します。 このような場合は、SU % の使用状況メトリックは 100% になります。    

|**ストリーミング ユニットの数**  |**サポートされるおおよその最大サイズ (MB 単位)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 以上   |300   |

ジョブのストリーミング ユニットの数を 6 を超える数に増やしても、サポートされる参照データの最大サイズが増えることはありません。

参照データの圧縮はサポートされていません。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
