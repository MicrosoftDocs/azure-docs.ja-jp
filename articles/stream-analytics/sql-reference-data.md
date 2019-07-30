---
title: SQL Database からの参照データを Azure Stream Analytics ジョブに使用する
description: この記事では、Azure portal および Visual Studio で Azure Stream Analytics ジョブに対する参照データ入力として SQL Database を使用する方法について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: ae1954b99e268e8bc44c4ba29bbc79d7734fda6e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461731"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>SQL Database からの参照データを Azure Stream Analytics ジョブに使用する

Azure Stream Analytics では、参照データ入力のソースとして Azure SQL Database がサポートされています。 Stream Analytics ツールを使用して、Azure portal および Visual Studio で Stream Analytics ジョブに対する参照データとして SQL Database を使用できます。 この記事では、両方の方法の実行方法を示します。

## <a name="azure-portal"></a>Azure ポータル

Azure portal を使用して参照入力ソースとして Azure SQL Database を追加するには、次の手順のようにします。

### <a name="portal-prerequisites"></a>ポータルの前提条件

1. Stream Analytics ジョブを作成します。

2. Stream Analytics ジョブで使用するストレージ アカウントを作成します。

3. Stream Analytics ジョブで参照データとして使用されるデータ セットを含む Azure SQL Database を作成します。

### <a name="define-sql-database-reference-data-input"></a>SQL Database の参照データ入力を定義する

1. Stream Analytics ジョブで、 **[ジョブ トポロジ]** の **[入力]** を選択します。 **[参照入力の追加]** をクリックして、 **[SQL Database]** を選択します。

   ![Stream Analytics ジョブの入力](./media/sql-reference-data/stream-analytics-inputs.png)

2. Stream Analytics の入力構成を指定します。 データベース名、サーバー名、ユーザー名、パスワードを選択します。 参照データ入力を定期的に更新する場合は、[オン] を選択し、DD:HH:MM で更新間隔を指定します。 データ セットが大きくて更新間隔が短い場合は、[デルタ クエリ](sql-reference-data.md#delta-query)を使用できます。

   ![SQL Database の参照の構成](./media/sql-reference-data/sql-input-config.png)

3. SQL クエリ エディターでスナップショット クエリをテストします。 詳しくは、「[Azure portal の SQL クエリ エディターを使用した接続とデータの照会](../sql-database/sql-database-connect-query-portal.md)」をご覧ください

### <a name="specify-storage-account-in-job-config"></a>ジョブの構成でストレージ アカウントを指定する

**[構成]** で **[ストレージ アカウントの設定]** に移動し、 **[ストレージ アカウントの追加]** を選択します。

   ![Stream Analytics のストレージ アカウントの設定](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>ジョブの開始

他の入力、出力、およびクエリを構成した後、Stream Analytics ジョブを開始できます。

## <a name="tools-for-visual-studio"></a>Tools for Visual Studio

Visual Studio を使用して参照入力ソースとして Azure SQL Database を追加するには、次の手順のようにします。

### <a name="visual-studio-prerequisites"></a>Visual Studio の前提条件

1. [Visual Studio 用の Stream Analytics ツールをインストールします](stream-analytics-tools-for-visual-studio-install.md)。 次のバージョンの Visual Studio がサポートされています。

   * Visual Studio 2015
   * Visual Studio 2019

2. [Visual Studio 用 Stream Analytics ツール](stream-analytics-quick-create-vs.md)のクイック スタートで理解を深めます。

3. ストレージ アカウントを作成します。

### <a name="create-a-sql-database-table"></a>SQL Database テーブルの作成

SQL Server Management Studio を使用して、参照データを格納するためのテーブルを作成します。 詳しくは、「[SSMS を使用して最初の Azure SQL データベースを設計する](../sql-database/sql-database-design-first-database.md)」をご覧ください。

後の例で使用するテーブルの例は、次のステートメントで作成しました。

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>サブスクリプションの選択

1. Visual Studio の **[表示]** メニューで **[サーバー エクスプローラー]** を選択します。

2. **[Azure]** を右クリックし、 **[Microsoft Azure サブスクリプションへの接続]** を選択して、Azure アカウントでサインインします。

### <a name="create-a-stream-analytics-project"></a>Stream Analytics プロジェクトを作成する

1. **[ファイル] > [新しいプロジェクト]** を選択します。 

2. 左側のテンプレートの一覧で **[Stream Analytics]** を選択し、 **[Azure Stream Analytics アプリケーション]** を選択します。 

3. プロジェクトの **[名前]** 、 **[場所]** 、 **[ソリューション名]** を入力し、 **[OK]** を選択します。

   ![Visual Studio での新しい Stream Analytics プロジェクト](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>SQL Database の参照データ入力を定義する

1. 新しい入力を作成します。

   ![Visual Studio での新しい Stream Analytics 入力](./media/sql-reference-data/stream-analytics-vs-input.png)

2. **ソリューション エクスプローラー**で、**Input.json** をダブルクリックします。

3. **[Stream Analytics Input Configuration]\(Stream Analytics の入力構成\)** を設定します。 データベース名、サーバー名、更新の種類、更新間隔を選択します。 `DD:HH:MM` の形式で更新間隔を指定します。

   ![Visual Studio での Stream Analytics の入力構成](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   [Execute only once]\(1 回だけ実行\) または [Execute periodically]\(定期的に実行\) を選択した場合は、プロジェクトの **Input.json** ファイル ノードの下に **<入力エイリアス>.snapshot.sql** という名前の SQL 分離コード ファイルが 1 つ生成されます。

   ![Visual Studio の入力分離コード ファイル](./media/sql-reference-data/once-or-periodically-codebehind.png)

   [Refresh Periodically with Delta]\(デルタで定期的に更新\) を択した場合は、2 つの SQL 分離コード ファイル **<入力エイリアス>.snapshot.sql** と **<入力エイリアス>.delta.sql** が生成されます。

   ![ソリューション エクスプローラーでの分離コード ファイル](./media/sql-reference-data/periodically-delta-codebehind.png)

4. エディターで SQL ファイルを開き、SQL クエリを記述します。

5. Visual Studio 2019 を使用していて、SQL Server Data Tools をインストールしている場合は、 **[実行]** をクリックしてクエリをテストできます。 SQL Database への接続を支援するウィザード ウィンドウがポップアップし、クエリの結果が下部のウィンドウに表示されます。

### <a name="specify-storage-account"></a>ストレージ アカウントを指定する

**JobConfig.json** を開き、SQL 参照スナップショットを格納するためのストレージ アカウントを指定します。

   ![Visual Studio での Stream Analytics ジョブの構成](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>ローカル環境でテストして Azure にデプロイする

ジョブを Azure にデプロイする前に、ローカル環境でライブ入力データに対してクエリ ロジックをテストできます。 この機能について詳しくは、「[Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする (プレビュー)](stream-analytics-live-data-local-testing.md)」をご覧ください。 テストが完了したら、 **[Azure に送信]** をクリックします。 ジョブを開始する方法については、「[クイック スタート: Visual Studio の Azure Stream Analytics ツールを使用した Stream Analytics ジョブの作成](stream-analytics-quick-create-vs.md)」をご覧ください。

## <a name="delta-query"></a>デルタ クエリ

デルタ クエリを使用するときは、[Azure SQL Database のテンポラル テーブル](../sql-database/sql-database-temporal-tables.md)をお勧めします。

1. Azure SQL Database にテンポラル テーブルを作成します。
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. スナップショット クエリを作成します。 

   **\@snapshotTime** パラメーターを使用して、システム時刻で有効な SQL Database のテンポラル テーブルから参照データ セットを取得するよう Stream Analytics ランタイムに指示します。 このパラメーターを指定しないと、クロックのずれが原因で不正確な基本参照データ セットを取得する可能性があります。 完全なスナップショット クエリの例を以下に示します。
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. デルタ クエリを作成します。 
   
   このクエリでは、開始時刻 **\@deltaStartTime** と終了時刻 **\@deltaEndTime** の間に挿入または削除された SQL Database のすべての行が取得されます。 デルタ クエリでは、スナップショット クエリと同じ列および列の **_操作_** を返す必要があります。 この列では、 **\@deltaStartTime** と **\@deltaEndTime** の間に行が挿入または削除されたかどうかが定義されています。 結果の行には、レコードが挿入された場合は **1**、削除された場合は **2** のフラグが設定されます。 

   更新されたレコードの場合、テンポラル テーブルでは挿入と削除の操作をキャプチャすることによってブックキーピングが行われます。 その場合、Stream Analytics ランタイムでは、前のスナップショットにデルタ クエリの結果を適用することによって、参照データが最新の状態に維持されます。 デルタ クエリの例を次に示します。

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Stream Analytics ランタイムでは、デルタ クエリに加えてスナップショット クエリが定期的に実行されてチェックポイントが保存される場合があることに注意してください。

## <a name="test-your-query"></a>クエリをテストする
   クエリで、Stream Analytics ジョブによって参照データとして使用される予期されるデータセットが返されることを確認することが重要です。 クエリをテストするには、ポータルの [ジョブ トポロジ] セクションの [入力] に移動します。 SQL Database 参照入力で、サンプル データを選択することができます。 サンプルが利用できるようになったら、ファイルをダウンロードし、返されるデータが予想どおりであるかどうかを確認します。 開発を最適化し、イテレーションをテストする場合、[Visual Studio の Stream Analytics ツール](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)を使用することをお勧めします。 または希望する他の任意のツールを使用して、まずクエリによって Azure SQL Database から正しい結果が返されることを確認してから、Stream Analytics ジョブでそれを使用することもできます。 

## <a name="faqs"></a>FAQ

**Azure Stream Analytics で SQL 参照データ入力を使用すると追加コストが発生しますか?**

Stream Analytics ジョブでは[ストリーミング ユニットごとにコスト](https://azure.microsoft.com/pricing/details/stream-analytics/)が追加されることはありません。 ただし、Stream Analytics ジョブには、Azure ストレージ アカウントが関連付けられている必要があります。 Stream Analytics ジョブでは、SQL DB のクエリが実行されて (ジョブの開始時と定期更新時)、参照データ セットが取得され、そのスナップショットがストレージ アカウントに格納されます。 これらのスナップショットを格納するため、Azure ストレージ アカウントの[価格に関するページ](https://azure.microsoft.com/pricing/details/storage/)で詳しく説明されている追加料金が発生します。

**参照データ スナップショットが SQL DB から照会され、Azure Stream Analytics ジョブで使用されていることは、どのようにすれば知ることができますか?**

論理名でフィルター処理される 2 つのメトリックがあり (メトリック Azure portal の下)、それを使用して SQL Database 参照データ入力の正常性を監視できます。

   * InputEvents:このメトリックでは、SQL Database 参照データ セットから読み込まれたレコードの数が測定されます。
   * InputEventBytes:このメトリックでは、Stream Analytics ジョブのメモリに読み込まれた参照データ スナップショットのサイズが測定されます。 

両方のメトリックの組み合わせを使用して、ジョブで SQL Database のクエリが実行されて参照データ セットがフェッチされ、メモリに読み込まれているかどうかを推論できます。

**特殊な種類の Azure SQL Database が必要ですか**

Azure Stream Analytics は、任意の種類の Azure SQL Database で動作します。 ただし、参照データ入力に対して設定されている更新間隔が、クエリの負荷に影響を与える可能性があることを理解しておく必要があります。 デルタ クエリ オプションを使用するには、Azure SQL Database のテンポラル テーブルを使用することをお勧めします。

**Azure Stream Analytics で Azure ストレージ アカウントにスナップショットが格納されるのはなぜですか?**

厳密には、Stream Analytics で保証されるイベントの処理は 1 回、イベントの配信は 1 回以上となります。 一時的な問題でジョブが影響を受けた場合、状態を復元するために若干の再生が必要です。 再生を有効にするには、これらのスナップショットが Azure ストレージ アカウントに格納されている必要があります。 チェックポイントの再生について詳しくは、「[Azure Stream Analytics ジョブでのチェックポイントと再生の概念](stream-analytics-concepts-checkpoint-replay.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [Stream Analytics での参照に参照データを使用する](stream-analytics-use-reference-data.md)
* [クイック スタート:Visual Studio の Azure Stream Analytics ツールを使用した Stream Analytics ジョブの作成](stream-analytics-quick-create-vs.md)
* [Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする (プレビュー)](stream-analytics-live-data-local-testing.md)
