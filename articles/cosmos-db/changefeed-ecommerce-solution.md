---
title: Azure Cosmos DB 変更フィードを使用してリアルタイムのデータ分析を視覚化する
description: この記事では、小売企業が変更フィードを活用してユーザーのパターンを理解し、リアルタイムでのデータ分析を実行して視覚化する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: e7b75c71d64054e38630677ecd38f8e3e2483c12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606336"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Azure Cosmos DB 変更フィードを使用してリアルタイムのデータ分析を視覚化する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 変更フィードは、Azure Cosmos コンテナーのレコードが作成または変更されるたびに、それらのレコードのフィードを継続的かつ増分的に取得するメカニズムです。 変更フィードのサポートは、コンテナーに加えられた変更をリッスンすることで機能します。 変更されたドキュメントは、変更された順に並べ替えられた一覧に出力されます。 変更フィードについて詳しくは、[変更フィードでの作業](change-feed.md)に関する記事をご覧ください。 

この記事では、eコマース企業が変更フィードを活用してユーザーのパターンを理解し、リアルタイムでのデータ分析を実行して、視覚化する方法について説明します。 ユーザーが商品を閲覧する、カートに商品を追加する、商品を購入するなどのイベントを分析します。 これらのいずれかのイベントが発生すると、新しいレコードが作成され、変更フィードがそのレコードをログします。 その後、変更フィードが一連のステップをトリガーし、企業のパフォーマンスやアクティビティを分析するメトリックを視覚化します。 視覚化できるメトリックのサンプルには、収益、ユニーク訪問者数、最も人気のある商品、および閲覧された商品、カートに追加された商品、購入された商品の平均価格の比較などがあります。 これらのメトリックのサンプルは、e コマース企業がサイトの普及率を評価し、広告や価格設定に関する戦略を策定して、投資する在庫について意思決定を行うのに役立ちます。

始める前に、このソリューションに関する次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>ソリューションのコンポーネント
次の図は、ソリューションに関連するデータ フローやコンポーネントを表します。

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="プロジェクトのビジュアル" border="false":::
 
1. **データの生成:** データ シミュレーターは、ユーザーが商品を閲覧する、カートに商品を追加する、商品を購入するなどのイベントを表す小売データを生成するために使用します。 データ ジェネレーターを使用して、大規模なサンプル データのセットを生成できます。 生成されたサンプル データには、次の形式のドキュメントが含まれています。
   
    ```json
    {
      "CartID": 2486,
      "Action": "Viewed",
      "Item": "Women's Denim Jacket",
      "Price": 31.99
    }
    ```

2. **Cosmos DB:** 生成されたデータは、Azure Cosmos コンテナーに格納されます。  

3. **変更フィード:** 変更フィードでは、Azure Cosmos コンテナーに対する変更がリッスンされます。 新しいドキュメントがコレクションに追加されるたびに (つまりユーザーが商品を閲覧する、カートに商品を追加する、商品を購入するなどのイベントが発生したとき)、変更フィードは [Azure Function](../azure-functions/functions-overview.md)をトリガーします。  

4. **Azure Function:** Azure 関数が新しいデータを処理して [Azure Event Hub](../event-hubs/event-hubs-about.md)に送信します。  

5. **Event Hub:** Azure Event Hub がそれらのイベントを格納し、[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) に送信してさらに分析します。  

6. **Azure Stream Analytics:** Azure Stream Analytics がイベントを処理し、リアルタイムでデータ分析を実行するクエリを定義します。 その後、このデータは [Microsoft Power BI](/power-bi/desktop-what-is-desktop) に送信されます。  

7. **Power BI:** Power BI は、Azure Stream Analytics によって送信されたデータを視覚化します。 ダッシュボードをビルドしてメトリックがリアルタイムでどのように変化するかを確認できます。  

## <a name="prerequisites"></a>前提条件

* Microsoft .NET Framework 4.7.1 以降

* Microsoft .NET Core 2.1 (またはそれ以降)

* ユニバーサル Windows プラットフォーム開発、.NET デスクトップ開発、ASP.NET と Web 開発のワークロードが付属する Visual Studio

* Microsoft Azure サブスクリプション

* Microsoft Power BI アカウント

* [Azure Cosmos DB 変更フィード ラボ](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample)は GitHub からダウンロードしてください。 

## <a name="create-azure-resources"></a>Azure リソースを作成する 

ソリューションで必要とされる Azure のリソース (Azure Cosmos DB、ストレージ アカウント、イベント ハブ、Stream Analytics) を作成します。 これらのリソースは、Azure Resource Manager テンプレートを通じてデプロイします。 次の手順を使用して、これらのリソースをデプロイします。 

1. Windows PowerShell の実行ポリシーを **[無制限]** に設定します。 これを行うには、**管理者として Windows PowerShell** を開き、次のコマンドを実行します。

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. 前の手順でダウンロードした GitHub リポジトリから、**Azure Resource Manager** フォルダーに移動し、**parameters.json** というファイルを開きます。  

3. **parameters.json** ファイルで示す cosmosdbaccount_name、eventhubnamespace_name、storageaccount_name の値、パラメーターを指定します。 後で各リソースに指定する名前を使用する必要があります。  

4. **Windows PowerShell** で、**Azure Resource Manager** フォルダーに移動して次のコマンドを実行します。

   ```powershell
   .\deploy.ps1
   ```
5. プロンプトが表示されたら、Azure **サブスクリプション ID**、リソース グループ名に「**changefeedlab**」、デプロイ名に「**run1**」を入力します。 リソースがデプロイを開始すると、完了までに最大で 10 分かかる場合があります。

## <a name="create-a-database-and-the-collection"></a>データベースとコレクションを作成する

次は e コマース サイトのイベントを保持するコレクションを作成します。 ユーザーが商品を閲覧する、カートに商品を追加する、または商品を購入すると、コレクションはアクション (「閲覧」、「追加」、または「購入」)、関連する商品の名前、関連する商品の価格、関連するユーザーのカートの ID 番号などのレコードを受け取ります。

1. [Azure portal](https://portal.azure.com/) に移動し、テンプレートのデプロイによって作成された **Azure Cosmos DB アカウント** を探します。  

2. **[データ エクスプローラー]** ペインで、 **[新しいコレクション]** を選択し、次の詳細でフォームに入力します。  

   * **[データベース ID]** フィールドで、 **[新規作成]** を選択し、「**changefeedlabdatabase**」と入力します。 **[Provision database throughput]\(データベース スループットをプロビジョニングする\)** ボックスはオフにします。  
   * **[コレクション ID]** フィールドに、「**changefeedlabcollection**」と入力します。  
   * **[パーティション キー]** フィールドに、「 **/Item**」と入力します。 これは大文字と小文字が区別されるため、正確に入力してください。  
   * **[スループット]** フィールドに、「**10000**」と入力します。  
   * **[OK]**  ボタンを選択します。  

3. 次に、変更フィードを処理する「**leases**」という名前の別のコレクションを作成します。 リース コレクションは、複数の worker 間で変更フィードの処理を調整します。 個別のコレクションを使用して、リース (パーティションごとに 1 リース) が保存されます。  

4. **[データ エクスプローラー]** ペインに戻り、 **[新しいコレクション]** を選択し、次の詳細でフォームに入力します。

   * **[データベース ID]** フィールドで、 **[既存のものを使用]** を選択し、「**changefeedlabdatabase**」と入力します。  
   * **[コレクション ID]** フィールドに、「**leases**」と入力します。  
   * **[ストレージ容量]** には **[固定]** を選択します。  
   * **[スループット]** フィールドは既定値のままにします。  
   * **[OK]**  ボタンを選択します。

## <a name="get-the-connection-string-and-keys"></a>接続文字列とキーを取得する

### <a name="get-the-azure-cosmos-db-connection-string"></a>Azure Cosmos DB 接続文字列を取得する

1. [Azure portal](https://portal.azure.com/) に移動し、テンプレートのデプロイによって作成された **Azure Cosmos DB アカウント** を探します。  

2. **[キー]** ペインに移動し、PRIMARY CONNECTION STRING をコピーしてノートパッドやラボを通じてアクセスできる別のドキュメントに貼り付けます。 これは **Cosmos DB 接続文字列** とラベル付けしてください。 後でこの文字列をコードにコピーする必要があるため、保存する場所をメモして覚えておきます。

### <a name="get-the-storage-account-key-and-connection-string"></a>ストレージ アカウントのキーと接続文字列を取得する

Azure Storage のアカウントではユーザーがデータを格納できます。 このラボでは、ストレージ アカウントを使用して Azure 関数によって使用さるデータを格納します。 Azure 関数はコレクションに対して何らかの変更が行われたときにトリガーされます。

1. リソース グループに戻り、先ほど作成したストレージ アカウントを開きます。  

2. 左側のメニューで、 **[アクセス キー]** を選択します。  

3. **[key 1]** の下の値をノートパッドやラボを通じてアクセスできる別のドキュメントにコピーします。 **キー** は **ストレージ キー**、**接続文字列** は **ストレージ接続文字列** としてラベル付けしてください。 後でこれらの文字列をコードにコピーする必要があるため、保存する場所をメモして覚えておきます。  

### <a name="get-the-event-hub-namespace-connection-string"></a>イベント ハブの名前空間の接続文字列を取得する

Azure Event Hub は、イベント データを取得し、そのデータを格納、処理、および転送します。 このラボでは、Azure Event Hub は新しいイベント (ユーザーが商品を閲覧した、ユーザーのカートに追加した、購入したなど) が発生するたびにドキュメントを取得し、そのドキュメントを Azure Stream Analytics に転送します。

1. リソース グループに戻り、プレラボで作成して名前を付けた **イベント ハブの名前空間** を開きます。  

2. 左側のメニューで、 **[共有アクセス ポリシー]** を選択します。  

3. **[RootManageSharedAccessKey]** を選択します。 **[接続文字列 – 主キー]** をノートパッドやラボを通じてアクセスできる別のドキュメントにコピーします。 これを **イベント ハブの名前空間** の接続文字列とラベル付けしてください。 後でこの文字列をコードにコピーする必要があるため、保存する場所をメモして覚えておきます。

## <a name="set-up-azure-function-to-read-the-change-feed"></a>変更フィードを読み取るように Azure 関数を設定する

Cosmos コンテナーで新しいドキュメントが作成されるか、または現在のドキュメントが変更されると、変更フィードによって自動的にその変更されたドキュメントがコレクションの変更履歴に追加されます。 次は変更フィードを処理する Azure 関数をビルドして実行します。 作成したコレクションでドキュメントが作成または変更されると、変更フィードによって Azure 関数がトリガーされます。 その後、Azure 関数が変更されたドキュメントをイベント ハブに送信します。

1. デバイスに複製したリポジトリに戻ります。  

2. **ChangeFeedLabSolution.sln** という名前のファイルを右クリックし、 **[Open With Visual Studio]\(Visual Studio で開く\)** を選択します。  

3. Visual Studio で **local.settings.json** に移動します。 その後、以前に記録した値を使用して空欄に入力します。  

4. **ChangeFeedProcessor.cs** に移動します。 **Run** 関数のパラメーターで、次の操作を実行します。  

   * テキスト **YOUR COLLECTION NAME HERE** をコレクションの名前に置き換えます。 前述の手順を実行した場合、コレクションの名前は changefeedlabcollection です。  
   * テキスト **YOUR LEASES COLLECTION NAME HERE** をリース コレクションの名前に置き換えます。 前述の手順を実行した場合、リース コレクションの名前は **leases** です。  
   * Visual Studio の上部で、緑の矢印の左にある [スタートアップ プロジェクト] ボックスが **ChangeFeedFunction** となっていることを確認します。  
   * ページ上部の **[開始]** を選択してプログラムを実行します。  
   * 関数が実行されると、コンソール アプリに「Job host started」(ジョブ ホストが開始されました) と表示されます。

## <a name="insert-data-into-azure-cosmos-db"></a>Azure Cosmos DB にデータを挿入する 

変更フィードが e コマース サイトで新しいアクションを処理する方法を確認するには、ユーザーが商品カタログから商品を閲覧している、それらの商品をカートに追加している、カート内の商品を購入していることを表すデータをシミュレートする必要があります。 このデータは任意であり、e コマースのデータの見た目をレプリケートする目的のためのものです。

1. エクスプローラーのリポジトリに戻り、 **[ChangeFeedFunction.sln]** を右クリックして、それを Visual Studio の新しいウィンドウでもう一度開きます。  

2. **App.config** ファイルに移動します。`<appSettings>` ブロックに、前に取得した Azure Cosmos DB アカウントのエンドポイントおよび一意の **主キー** を追加します。  

3. **コレクション** 名と **データベース** 名を追加します (別の名前にすることを選択していない限り、これらの名前は **changefeedlabcollection** と **changefeedlabdatabase** にしてください)。

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="接続文字列を更新する":::
 
4. 編集したすべてのファイルの変更を保存します。  

5. Visual Studio の上部で、緑の矢印の左にある **[スタートアップ プロジェクト]** ボックスが **DataGenerator** となっていることを確認します。 その後、ページ上部の **[開始]** を選択してプログラムを実行します。  
 
6. プログラムが実行されるのを待ちます。 星はデータを受信していることを意味します。 プログラムを引き続き実行します。大量のデータを収集することが重要です。  

7. [Azure portal](https://portal.azure.com/)、リソース グループ内の Cosmos DB アカウント、**データ エクスプローラー** の順に移動すると、**changefeedlabcollection** にランダム化されたデータがインポートされたことが確認できます。
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="ポータルで生成されたデータ":::

## <a name="set-up-a-stream-analytics-job"></a>Stream Analytics のジョブを設定する

Azure Stream Analytics は、ストリーミング データをリアルタイムで分析するフル マネージドのクラウド サービスです。 このラボでは、Stream Analytics を使用して、イベント ハブからの新しいイベント (商品が閲覧されたとき、カートに追加されたとき、購入されたときなど) を処理し、それらのイベントをリアルタイムのデータ分析に組み込み、Power BI に送信して視覚化します。

1. [Azure portal](https://portal.azure.com/) で、リソース グループ、**streamjob1** (プレラボで作成した Stream Analytics のジョブ) の順に移動します。  

2. 以下に示すように、 **[入力]** を選択します。  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="入力を作成する":::

3. **[+ ストリーム入力の追加]** を選択します。 次に、ドロップダウン メニューから **[イベント ハブ]** を選択します。  

4. 新しい入力フォームに、次の詳細を入力します。

   * **[入力のエイリアス]** フィールドに、「**input**」と入力します。  
   * **[サブスクリプションからイベント ハブを選択する]** のオプションを選択します。  
   * **[サブスクリプション]** フィールドを自身のサブスクリプションに設定します。  
   * **[イベント ハブの名前空間]** フィールドに、プレラボ中に作成したイベント ハブの名前空間の名前を入力します。  
   * **[イベント ハブの名前]** フィールドで、 **[既存のものを使用]** のオプションを選択し、ドロップダウン メニューから **[event-hub1]** を選択します。  
   * **[Event Hub policy]\(イベント ハブのポリシー\)** 名前フィールドは、既定の値のままにします。  
   * **[イベントのシリアル化の形式]** は、 **[JSON]** のままにします。  
   * **[エンコード]** フィールドは、 **[UTF-8]** のままにします。  
   * **[イベントの圧縮タイプ]** フィールドは、 **[なし]** のままにします。  
   * **[保存]** を選択します。

5. Stream Analytics のジョブのページに戻り、 **[出力]** を選択します。  

6. **[+ 追加]** を選択します。 次に、ドロップダウン メニューから **[Power BI]** を選択します。  

7. Power BI の新しい出力を作成して平均価格を視覚化するには、次のアクションを実行します。

   * **[出力のエイリアス]** フィールドに、「**averagePriceOutput**」と入力します。  
   * **[グループ ワークスペース]** フィールドは、 **[接続でのワークスペースの読み込みを承認する]** のままにします。  
   * **[データセット名]** フィールドに、「**averagePrice**」と入力します。  
   * **[テーブル名]** フィールドに、「**averagePrice**」と入力します。  
   * **[承認]** ボタンを選択し、指示に従って Power BI への接続を承認します。  
   * **[保存]** を選択します。  

8. その後、**streamjob1** に戻り、 **[クエリの編集]** を選択します。

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="クエリを編集する":::
 
9. クエリ ウィンドウに次のクエリを貼り付けます。 **AVERAGE PRICE** クエリは、ユーザーによって閲覧されたすべての商品の平均価格、ユーザーのカートに追加されたすべての商品の平均価格、ユーザーによって購入されたすべての商品の平均価格を計算します。 このメトリックは、e コマース企業が商品を販売する価格や投資する在庫を決定するのに役立ちます。 たとえば、閲覧された商品の平均価格が購入された商品の平均価格を大幅に上回る場合、企業はより価格の低い商品を在庫に追加することを選択する可能性があります。

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. その後、左上隅にある **[保存]** を選択します。  

11. 次は **streamjob1** に戻り、ページ上部の **[開始]** ボタンを選択ます。 Azure Stream Analytics は起動に数分かかる場合がありますが、最終的には「開始中」から「実行中」に変わります。

## <a name="connect-to-power-bi"></a>Power BI に接続する

Power BI は、データを分析し、洞察を共有する一連のビジネス分析ツールです。 これは、分析されたデータを戦略的に視覚化する方法を示す優れた例です。

1. Power BI にサインインし、ページの左側のメニューを開いて **[マイ ワークスペース]** に移動します。  

2. 右上隅にある **[+ 作成]** を選択し、 **[ダッシュボード]** を選択してダッシュボードを作成します。  

3. 右上隅にある **[+ タイルの追加]** を選択します。  

4. **[カスタム ストリーミング データ]** を選択し、 **[次へ]** ボタンを選択します。  
 
5. **[YOUR DATASETS]\(あなたのデータセット)** から **[averagePrice]** を選択し、 **[次へ]** を選択します。  

6. **[視覚化タイプ]** フィールドで、ドロップダウン メニューから **[集合横棒グラフ]** を選択します。 **[軸]** で、アクションを追加します。 **[凡例]** は、何も追加せずにスキップします。 その後、次の **[値]** セクションで、 **[avg]** を追加します。 **[次へ]** を選択し、グラフにタイトルを付け、 **[適用]** を選択します。 これでダッシュボードに新しいグラフが表示されます。  

7. ここで、複数のメトリックを視覚化する場合は、**streamjob1** に戻り、次のフィールドを持つ出力をあと 3 つ作成します。

   a. **出力のエイリアス:** incomingRevenueOutput、データセット名: incomingRevenue、テーブル名: incomingRevenue  
   b. **出力のエイリアス:** top5Output、データセット名: top5、テーブル名: top5  
   c. **出力のエイリアス:** uniqueVisitorCountOutput、データセット名: uniqueVisitorCount、テーブル名: uniqueVisitorCount

   その後、 **[クエリの編集]** を選択し、既に記述したクエリの **上** に次のクエリを貼り付けます。

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   TOP 5 クエリは、購入された数によってランク付けされた、上位 5 件の商品を計算します。 このメトリックは、e コマース企業がどの商品が最も人気があるかを評価し、企業の宣伝方法、価格設定、在庫保持の判断に影響を与えます。

   REVENUE クエリは、1 分ごとに購入されたすべての商品の価格を合計することで、収益を計算します。 このメトリックは、e コマース企業が自社の財務パフォーマンスを評価し、1 日のうちどの時間帯が最も収益を得られるかを把握するのに役立ちます。 これは企業の戦略全般、特にマーケティングに影響を与える場合があります。

   UNIQUE VISITORS クエリは、ユニークなカート ID を検出することで、5 秒ごとにサイトのユニーク訪問者の数を計算します。 このメトリックは、e コマース企業がサイトのアクティビティを評価し、より多くの顧客を獲得する戦略を練るのに役立ちます。

8. 同様に、これらのデータセットにタイルを追加できます。

   * トップ 5 には、商品を軸、商品数を値とした集合縦棒グラフが適してします。  
   * 収益には、時間を軸、値を価格の合計とした折れ線グラフが適しています。 できるだけ多くの情報を確認できるように、表示する時間枠はできるだけ大きくすることをお勧めします。  
   * ユニーク訪問者数には、ユニーク訪問者数を値としたカード視覚化が適しています。

   これらのグラフを表示するダッシュボードの例は次のようになります。

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="スクリーンショットには、[Average Price of Items by Action]\(アクションごとの品目の平均価格)、[Unique Visitors]\(一意の訪問者)、[Revenue]\(収益)、および [Top 5 Items Purchased]\(上位 5 つの購入品目) という名前のグラフを含むサンプルのダッシュボードが表示されています。":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>省略可能:eコマース サイトの視覚化

ここでは、実際の e コマース サイトで新しいデータ分析ツールをどのように使用できるかについて観察します。 eコマース サイトをビルドするには、Azure Cosmos データベースを使用して商品カテゴリ (女性、男性、ユニセックス) のリスト、商品カタログ、最も人気のある商品のリストを格納します。

1. [Azure portal](https://portal.azure.com/) に戻り、**Cosmos DB アカウント**、**データ エクスプローラー** と移動します。  

   **changefeedlabdatabase** - **products** と **categories** の下に、2 つのコレクションを、[固定] のストレージ容量で追加します。

   **changefeedlabdatabase** の下に **topItems** という名前のもう 1 つのコレクションと、パーティション キーとして **/Item** を追加します。

2. **topItems** コレクションを選択し、 **[スケールと設定]** で、topItems が 30 秒ごとに更新されるように、 **[Time to Live]** を **[30 秒]** に設定します。

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Time to Live":::

3. **topItems** コレクションに最も頻繁に購入された商品のデータを入力するには、**streamjob1** に戻り、新しい **出力** を追加します。 **[Cosmos DB]** を選択します。

4. 以下の図に示すように、必須フィールドに入力します。

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Cosmos の出力":::
 
5. ラボの前のパートでオプションの TOP 5 クエリを追加した場合は、パート 5a に進みます。 それ以外の場合は、パート 5b に進みます。

   5a. **streamjob1** で、 **[クエリの編集]** を選択し、Azure Stream Analytics クエリ エディターの TOP 5 クエリの下、その他残りのクエリの上に、次のクエリを貼り付けます。

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. **streamjob1** で、 **[クエリの編集]** を選択し、Azure Stream Analytics クエリ エディターのその他すべてのクエリの上に、次のクエリを貼り付けます。

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. **EcommerceWebApp.sln** を開き、**ソリューション エクスプローラー** の **Web.config** ファイルに移動します。  

7. `<appSettings>` ブロック内に、**your URI here** と **your primary key here** と書かれている位置に、前に保存した **URI** と **主キー** を追加します。 その後、**データベース名** と **コレクション名** を示されているとおりに追加します (別の名前にすることを選択していない限り、これらの名前は **changefeedlabdatabase** と **changefeedlabcollection** にしてください)。

   **製品のコレクション名**、**カテゴリのコレクション名**、**人気の商品のコレクション名** を示されているとおりに入力します (別の名前にすることを選択していない限り、これらの名前は **products、categories、および topItems** にしてください)。  

8. **EcommerceWebApp.sln** 内の **[チェックアウト フォルダー]** に移動して開きます。 その後、そのフォルダー内の **Web.config** ファイルを開きます。  

9. `<appSettings>` ブロック内で、以前に保存した **URI** と **主キー** を示されている位置に追加します。 その後、**データベース名** と **コレクション名** を示されているとおりに追加します (別の名前にすることを選択していない限り、これらの名前は **changefeedlabdatabase** と **changefeedlabcollection** にしてください)。  

10. ページ上部の **[開始]** を押してプログラムを実行します。  

11. これで e コマース サイトを操作できるようになります。 商品を閲覧する、商品をカートに追加する、カート内の商品の数を変更する、商品を購入するなどのイベントは Cosmos DB 変更フィードを通じてイベント ハブ、ASA、Power BI に渡されます。 大量の Web トラフィック データを生成し、e コマース サイトの本当の「人気商品」を提供するために、DataGenerator を引き続き実行することをお勧めします。

## <a name="delete-the-resources"></a>リソースの削除

このラボの間に作成したリソースを削除するには、[Azure portal](https://portal.azure.com/) のリソース グループに移動し、ページ上部のメニューで **[リソース グループの削除]** を選択して、表示される手順に従います。

## <a name="next-steps"></a>次のステップ 
  
* 変更フィードについて詳しくは、「[Azure Cosmos DB での変更フィード サポートの使用](change-feed.md)」をご覧ください。