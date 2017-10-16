1. 新しいウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。
2. 左側のウィンドウで、**[新規]**、**[データベース]** の順にクリックし、**[Azure Cosmos DB]** の下にある **[作成]** をクリックします。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. **[新しいアカウント]** ブレードで、この Azure Cosmos DB アカウントに必要な構成を指定します。 

    Azure Cosmos DB では、Gremlin (グラフ)、MongoDB、SQL (DocumentDB)、および Table (キー値) の 4 つのプログラミング モデルのいずれかを選択できますが、現時点では、それぞれ別個のアカウントが必要です。
    
    このクイック スタートの記事では、DocumentDB API に対してプログラミングするため、フォームに入力するときに **SQL (DocumentDB)** を選択します。 ソーシャル メディア アプリ用のグラフ データ、キー/値 (テーブル) データ、または MongoDB アプリから移行されたデータがある場合は、Azure Cosmos DB が、ミッション クリティカルなすべてのアプリケーションのための可用性の高い、世界中に分散したデータベース サービス プラットフォームを提供できることを認識してください。

    **[新しいアカウント]** ブレードで、次のスクリーンショットの情報を参考にしてフィールドに入力します。実際の値は、スクリーンショットの値とは異なる場合があります。
 
    ![Azure Cosmos DB の [新しいアカウント] ブレード](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    設定|推奨値|説明
    ---|---|---
    ID|*一意の値*|この Azure Cosmos DB アカウントを識別する一意の名前。 指定した ID に *documents.azure.com* が付加されて URI が作成されるので、ID は一意であっても識別可能なものを使用してください。 ID に含めることができるのは英小文字、数字、ハイフン (-) のみ、3 文字以上で 50 文字以内にする必要があります。
    API|SQL (DocumentDB)|この記事の後半で、[DocumentDB API](../articles/cosmos-db/documentdb-introduction.md) に対してプログラミングします。|
    サブスクリプション|*該当するサブスクリプション*|この Azure Cosmos DB アカウントに使用する Azure サブスクリプション。 
    リソース グループ|*ID と同じ値*|自分のアカウントの新しいリソースグループの名前。 簡略化のため、ID と同じ名前を使用することができます。 
    場所|*ユーザーに最も近いリージョン*|Azure Cosmos DB アカウントをホストする地理的な場所です。 データに最も高速にアクセスできる、ユーザーに最も近い場所を選択します。
4. **[作成]** をクリックしてアカウントを作成します。
5. 上部のツール バーの**通知**アイコン![通知アイコン](./media/cosmos-db-create-dbaccount/notification-icon.png)をクリックして、デプロイ プロセスを監視します。

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  デプロイに成功したことを [通知] ウィンドウで確認できたら、[通知] ウィンドウを閉じて、ダッシュボードの **[すべてのリソース]** タイルから新しいアカウントを開きます。 

    ![[すべてのリソース] タイルの Azure Cosmos DB アカウント](./media/cosmos-db-create-dbaccount/all-resources.png)
 
