1. 新しいウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。

2. 左側のウィンドウで **[新規]** > **[データベース]** > **[Azure Cosmos DB]** > **[作成]** の順に選択します。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. **[新しいアカウント]** で、この Azure Cosmos DB アカウントに必要な構成を指定します。 

    Azure Cosmos DB では、Gremlin (グラフ)、MongoDB、SQL (DocumentDB)、および Table (キー値) の 4 つのプログラミング モデルのいずれかを選択できます。 現在は、モデルごとに異なるアカウントが必要となります。
       
    このクイック スタートの記事では、Graph API に対してプログラミングするため、フォームに入力する際に、**[Gremlin (グラフ)]** を選択します。 カタログ アプリのドキュメント データ、キー/値 (テーブル) データ、または MongoDB アプリから移行されたデータがある場合は、Azure Cosmos DB が、ミッション クリティカルなすべてのアプリケーションのための可用性の高い、世界中に分散したデータベース サービス プラットフォームを提供できることを認識してください。

    **[新しいアカウント]** ブレードで、次のスクリーンショットの情報を参考にしてフィールドに入力します。 実際の値は、スクリーンショットの値とは異なる場合があります。
 
    ![[新しいアカウント] ブレード](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    設定|推奨値|説明
    ---|---|---
    ID|*一意の値*|この Azure Cosmos DB アカウントを識別する一意の名前。 指定した ID に *documents.azure.com* が付加されて URI が作成されるので、ID は一意であっても識別可能なものを使用してください。 ID に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 3 文字以上 50 文字以内にする必要があります。
    API|Gremlin (グラフ)|この記事では後から、[Graph API](../articles/cosmos-db/graph-introduction.md) に対してプログラミングします。|
    サブスクリプション|*該当するサブスクリプション*|この Azure Cosmos DB アカウントに使用する Azure サブスクリプション。 
    リソース グループ|*ID と同じ値*|自分のアカウントの新しいリソース グループの名前。 簡略化のため、ID と同じ名前を使用することができます。 
    場所|*ユーザーに最も近いリージョン*|Azure Cosmos DB アカウントをホストする地理的な場所です。 データに最も高速にアクセスできる、ユーザーに最も近い場所を選択します。

4. **[作成]** を選択して、アカウントを作成します。

5. ツール バーの**通知**アイコン![通知アイコン](./media/cosmos-db-create-dbaccount-graph/notification-icon.png)を選択して、デプロイ プロセスを監視します。

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount-graph/notification.png)

6. デプロイに成功したことが **[通知]** ウィンドウで確認できたら、ウィンドウを閉じます。 **[ダッシュボード]** の **[すべてのリソース]** タイルから新しいアカウントを開きます。 

    ![[すべてのリソース] タイル](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)