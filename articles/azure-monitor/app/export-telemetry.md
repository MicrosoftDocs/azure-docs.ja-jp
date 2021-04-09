---
title: Application Insights からのテレメトリの連続エクスポート | Microsoft Docs
description: 診断および利用状況データを Microsoft Azure のストレージにエクスポートし、そこからダウンロードします。
ms.topic: conceptual
ms.date: 02/19/2021
ms.custom: references_regions
ms.openlocfilehash: e7831123834df9186310453106c50261373160ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737037"
---
# <a name="export-telemetry-from-application-insights"></a>Application Insights からのテレメトリのエクスポート
標準的なリテンション期間より長くテレメトリを残しておきたい、 または特別な方法でテレメトリを処理したい、 そのようなケースには、連続エクスポートが最適です。 Application Insights ポータルに表示されるイベントは、JSON 形式で Microsoft Azure のストレージにエクスポートできます。 そこからデータをダウンロードしたり、データを処理するためのコードを自由に記述したりできます。  

> [!IMPORTANT]
> 連続エクスポートは非推奨となりました。 テレメトリをエクスポートするために[診断設定](#diagnostic-settings-based-export)を使用するには、[ワークスペースベースの Application Insights リソースに移行](convert-classic-resource.md)します。

> [!NOTE]
> 連続エクスポートは、従来の Application Insights リソースに対してのみサポートされます。 [ワークスペース ベースの Application Insights リソース](./create-workspace-resource.md)では、[診断設定](./create-workspace-resource.md#export-telemetry)を使用する必要があります。
>

連続エクスポートをセットアップする前に、次の代替手段を検討してください。

* メトリック タブや検索タブの上部にある [エクスポート] ボタンを使用すると、テーブルやグラフを Excel のスプレッドシートに転送できます。

* [Analytics](../logs/log-query-overview.md) にはテレメトリ用の強力なクエリ言語があります。 結果をエクスポートすることもできます。
* [Power BI でデータを探索](./export-power-bi.md)する場合は、連続エクスポートを使用せずに実行できます。
* [データ アクセス REST API](https://dev.applicationinsights.io/) を使用すると、テレメトリにプログラムでアクセスすることができます。
* [PowerShell を使用して連続エクスポート](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport)の設定にアクセスすることもできます。

連続エクスポートによってストレージ (必要な期間の保持が可能) にコピーされたデータは、通常の[リテンション期間](./data-retention-privacy.md)が過ぎるまで引き続き Application Insights で使用できます。

## <a name="supported-regions"></a>サポートされているリージョン

連続エクスポートは、次のリージョンでサポートされています。

* 東南アジア
* カナダ中部
* インド中部
* 北ヨーロッパ
* 英国南部
* オーストラリア東部
* 東日本
* 韓国中部
* フランス中部
* 東アジア
* 米国西部
* 米国中部
* 米国東部 2
* 米国中南部
* 米国西部 2
* 南アフリカ北部
* 米国中北部
* ブラジル南部
* スイス北部
* オーストラリア南東部
* 英国西部
* ドイツ中西部
* スイス西部
* オーストラリア中部 2
* アラブ首長国連邦中部
* ブラジル南東部
* オーストラリア中部
* アラブ首長国連邦北部
* ノルウェー東部
* 西日本

> [!NOTE]
> **西ヨーロッパ** と **米国東部** で既に構成されているアプリケーションはサポートされますが、これらのリージョンへの新しいアプリケーションのオンボードはサポートされていません。

## <a name="continuous-export-advanced-storage-configuration"></a>連続エクスポートの高度なストレージ構成

連続エクスポートでは、次の Azure のストレージ機能または構成は **サポートされません**。

* [VNET/Azure Storage ファイアウォール](../../storage/common/storage-network-security.md)と Azure Blob Storage の併用。

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)。

## <a name="create-a-continuous-export"></a><a name="setup"></a>連続エクスポートを作成する

> [!NOTE]
> アプリケーションでは、1 日に 3 TB を超えるデータをエクスポートできません。 1 日あたり 3 TB を超える場合、エクスポートは無効になります。 制限なしでエクスポートするには、[診断設定ベースのエクスポート](#diagnostic-settings-based-export)を使用します。

1. アプリの Application Insights リソースで、左側の [構成] の下の [連続エクスポート] を開き、 **[追加]** を選択します。

2. テレメトリをエクスポートするデータ型を選択します。

3. データの保存先となる [Azure ストレージ アカウント](../../storage/common/storage-introduction.md)を作成または選択します。 ストレージの価格オプションの詳細については、[価格に関する公式のページ](https://azure.microsoft.com/pricing/details/storage/)を参照してください。

     [追加]、[エクスポート先]、[ストレージ アカウント] の順にクリックし、新しいストアを作成するかまたは既存のストアを使用するかを選択します。

    > [!Warning]
    > 既定では、ストレージの場所は、Application Insights のリソースと同じ地理的リージョンに設定されます。 別のリージョンに保存する場合は、転送の料金が発生する可能性があります。

4. ストレージにコンテナーを作成するか、選択します。

> [!NOTE]
> エクスポートを作成すると、新しく取り込まれたデータが Azure Blob storage へフローするようになります。 連続エクスポートでは、連続エクスポートが有効になった後に作成または取り込まれた新しいテレメトリのみが送信されます。 連続エクスポートを有効にする前に存在していたデータはエクスポートされず、連続エクスポートを使用して以前に作成されたデータをさかのぼってエクスポートすることはできません。

ストレージにデータが表示されるまで、約 1 時間の遅延が発生する可能性があります。

最初のエクスポートが完了すると、Azure Blob Storage コンテナーに次のような構造が表示されます。(これは収集するデータに応じて異なります。)

|名前 | 説明 |
|:----|:------|
| [可用性](export-data-model.md#availability) | [可用性 Web テスト](./monitor-web-app-availability.md)をレポートします。  |
| [Event](export-data-model.md#events) | [TrackEvent()](./api-custom-events-metrics.md#trackevent)によって生成されたカスタム イベント。 
| [例外](export-data-model.md#exceptions) |サーバーおよびブラウザーの [例外](./asp-net-exceptions.md) をレポートします。
| [Messages (メッセージ)](export-data-model.md#trace-messages) | [TrackTrace](./api-custom-events-metrics.md#tracktrace) および[ログ アダプター](./asp-net-trace-logs.md)によって送信されます。
| [Metrics](export-data-model.md#metrics) | メトリック API 呼び出しによって生成されます。
| [PerformanceCounters](export-data-model.md) | Application Insights によって収集されたパフォーマンス カウンター。
| [要求数](export-data-model.md#requests)| [TrackRequest](./api-custom-events-metrics.md#trackrequest)によって送信されます。 標準モジュールはこれを使用して、サーバーで測定されたサーバー応答時間をレポートします。| 

### <a name="to-edit-continuous-export"></a>連続エクスポートを編集するには

[連続エクスポート] をクリックし、編集するストレージ アカウントを選択します。

### <a name="to-stop-continuous-export"></a>連続エクスポートを停止するには

エクスポートを停止するには、[無効] をクリックします。 もう一度 [有効] をクリックすると、新しいデータでエクスポートが再開されます。 エクスポートが無効な場合、ポータルに到着したデータは取得されません。

エクスポートを完全に停止するには、対象のエクスポートを削除します。 エクスポートを削除しても、ストレージのデータは削除されません。

### <a name="cant-add-or-change-an-export"></a>エクスポートを追加または変更できない
* エクスポートを追加または変更するには、所有者、共同作成者、または Application Insights 共同作成者のアクセス権が必要になります。 [ロールの詳細については、こちらを参照してください][roles]。

## <a name="what-events-do-you-get"></a><a name="analyze"></a> 取得されるイベント
エクスポートされたデータは、お客様のアプリケーションから受け取った未加工のテレメトリですが、クライアントの IP アドレスから計算された位置データが追加されます。

[サンプリング](./sampling.md) によって破棄されたデータは、エクスポートされるデータに含まれません。

他の計算メトリックは含まれません。 たとえば、平均 CPU 使用率はエクスポートされませんが、平均の計算に使用された未加工のテレメトリはエクスポートされます。

データには、セットアップ済みのすべての[可用性 Web テスト](./monitor-web-app-availability.md)の結果も含まれます。

> [!NOTE]
> **サンプリング。** アプリケーションで大量のデータを送信すると、サンプリング機能が動作して、生成されたテレメトリのごく一部だけが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> データの確認
ポータルでストレージを直接検査することができます。 一番左のメニューで [ホーム] をクリックし、上部の [Azure サービス] で **[ストレージ アカウント]** を選択し、ストレージ アカウント名を選択します。概要ページの [サービス] で **[BLOB]** を選択し、最後にコンテナー名を選択します。

Visual Studio で Azure ストレージを検査するには、 **[表示]** 、 **[Cloud Explorer]** の順に開きます (このメニュー コマンドがない場合は、Azure SDK をインストールする必要があります。 **[新しいプロジェクト]** ダイアログを開き、[Visual C#]、[クラウド] の順に展開して、 **[Microsoft Azure SDK for .NET の取得]** を選択します)。

BLOB ストアを開くと、BLOB ファイルのセットを含むコンテナーが表示されます。 各ファイルの URI は、Application Insights のリソース名、そのインストルメンテーション キー、テレメトリの種類/日付/時刻から派生します (リソース名はすべて小文字になり、インストルメンテーション キーのダッシュは省略されます)。

![適切なツールで Blob ストアを調べます](./media/export-telemetry/04-data.png)

日付と時刻は UTC 形式で表され、テレメトリが生成された時間ではなく、ストアに格納された日時を示します。 そのため、データをダウンロードするコードを記述する場合は、直線的にデータ内を移動できます。

パスの形式を以下に示します。

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Where

* `blobCreationTimeUtc` は、BLOB が内部ステージング ストレージで作成された日時です
* `blobDeliveryTimeUtc` は、BLOB がエクスポート先のストレージにコピーされた日時です。

## <a name="data-format"></a><a name="format"></a> データ形式
* それぞれの Blob は、"\n" で区切られた複数の行を含むテキスト ファイルです。 約 30 秒の間に処理されたテレメトリが含まれています。
* 各行は、要求やページ表示などのテレメトリ データ ポイントを表します。
* それぞれの行は、書式設定されていない JSON ドキュメントです。 行を表示する場合は、Visual Studio で BLOB を開き、 **[編集]**  >  **[詳細設定]**  >  **[フォーマットファイル]** の順に選択します。

   ![適切なツールでテレメトリを表示します](./media/export-telemetry/06-json.png)

時間の長さはティック単位で表記されます。10,000 ティックが 1 ミリ秒です。 たとえば、次の値は、ブラウザーから要求を送信するのに 1 ミリ秒、要求を受信するのに 3 ミリ秒、ブラウザーでページを処理するのに 1.8 秒の時間がかかったことを示しています。

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[データ モデルについては、プロパティの型と値のリファレンスで詳しく説明されています。](export-data-model.md)

## <a name="processing-the-data"></a>データの処理
小規模な処理では、データを分解してスプレッドシートに読み込んだ後で他の処理を実行するコードを記述できます。 次に例を示します。

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

大規模なコード サンプルについては、「[worker ロールの使用][exportasa]」をご覧ください。

## <a name="delete-your-old-data"></a><a name="delete"></a>古いデータの削除
ストレージ容量の管理と古いデータの削除は、必要に応じて自分で行う必要があります。

## <a name="if-you-regenerate-your-storage-key"></a>ストレージ キーを再生成する場合
ストレージのキーを変更した場合、連続エクスポートは動作しなくなります。 Azure アカウントに通知が表示されます。

[連続エクスポート] タブを開き、エクスポートを編集します。 エクスポート先の編集画面では、同じストレージを選択したままにします。 [OK] をクリックして確定します。

連続エクスポートが再開されます。

## <a name="export-samples"></a>エクスポート サンプル

* [Stream Analytics を使用して SQL にエクスポートする][exportasa]
* [Stream Analytics のサンプル 2](export-stream-analytics.md)

大規模な処理の場合は、 [HDInsight](https://azure.microsoft.com/services/hdinsight/) (クラウドの Hadoop クラスター) を検討してください。 HDInsight はビッグ データの管理と分析を行うためのさまざまなテクノロジを備えており、Application Insights からエクスポートされたデータの処理に使用できます。

## <a name="q--a"></a>Q & A
* *グラフを 1 回だけダウンロードしたいのですが。*  

    はい、できます。 タブの上部にある **[データのエクスポート]** をクリックします。
* *エクスポートを設定したのにストアにデータがありません。*

    エクスポートを設定した時点以降に Application Insights がアプリからテレメトリを受信していますか。 取得されるのは新しいデータのみです。
* *エクスポートを設定しようとしたところ、アクセスが拒否されました。*

    アカウントが組織によって所有されている場合は、所有者または共同作成者グループのメンバーである必要があります。
* *自分のオンプレミスのストアに直接エクスポートできますか。*

    いいえ、できません。 現在のところ、エクスポート エンジンは、Azure Storage でのみ動作します。  
* *ストアに格納できるデータの量に制限はありますか。*

    いいえ。 データのプッシュ配信は、エクスポートが削除されるまで続行されます。 Blob Storage の制限に達した場合は配信が停止されますが、その制限には非常に大きな値が設定されています。 使用するストレージの量を管理するのはお客様です。  
* *ストレージに表示される BLOB の数を教えてください。*

  * エクスポートに選択した各データの種類ごとに、1 つの新しい BLOB が 1 分ごとに作成されます (データを使用できる場合)。
  * さらに、トラフィック負荷の高いアプリケーションでは、追加のパーティション単位が割り当てられます。 この場合、各単位で 1 分ごとに 1 つの BLOB が作成されます。
* *ストレージのキーを再生成した後、またはコンテナーの名前を変更した後、エクスポートが動作しません。*

    エクスポートを編集し、[エクスポート先] タブを開きます。以前と同じストレージが選択されていることを確認し、[OK] をクリックして確定します。 エクスポートが再開されます。 変更を加えたのが数日前のことであれば、データは失われません。
* *エクスポートを一時停止できますか。*

    はい。 [無効] をクリックします。

## <a name="code-samples"></a>コード サンプル

* [Stream Analytics のサンプル](export-stream-analytics.md)
* [Stream Analytics を使用して SQL にエクスポートする][exportasa]
* [データ モデルについては、プロパティの型と値のリファレンスで詳しく説明されています。](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>診断設定ベースのエクスポート

診断設定ベースのエクスポートでは、連続エクスポートとは異なるスキーマが使用されます。 また、連続エクスポートではサポートされない次のような機能もサポートします。

* VNet、ファイアウォール、およびプライベート リンクがある Azure ストレージ アカウント。
* イベント ハブへのエクスポート。

診断設定ベースのエクスポートに移行するには:

1. 現在の連続エクスポートを無効にします。
2. [アプリケーションをワークスペースベース](convert-classic-resource.md)に移行します。
3. [診断設定のエクスポートを有効にします](create-workspace-resource.md#export-telemetry)。 Application Insights リソース内で、 **[診断設定] > [診断設定を追加する]** を選択します。

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

