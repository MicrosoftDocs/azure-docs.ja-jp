---
title: Cosmos DB エンドポイントを使用して Azure Databricks を構築する
description: このチュートリアルでは、仮想ネットワークに、Cosmos DB に対して有効なサービス エンドポイントを使用して Azure Databricks を構築する方法について説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: d1268ea2cfc22e6350edb32230588a497be8bc79
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054509"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>チュートリアル:Cosmos DB エンドポイントを使用して Azure Databricks を構築する

このチュートリアルでは、Cosmos DB に対して有効なサービス エンドポイントを使用して、VNet に導入された Databricks 環境を構築する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想ネットワーク内に Azure Databricks ワークスペースを作成する
> * Cosmos DB サービス エンドポイントを作成する
> * Cosmos DB アカウントを作成してデータをインポートする
> * Azure Databricks クラスターを作成する
> * Azure Databricks ノートブックから Cosmos DB にクエリを実行する

## <a name="prerequisites"></a>前提条件

開始する前に、以下を行います。

* [仮想ネットワーク内に Azure Databricks ワークスペース](quickstart-create-databricks-workspace-vnet-injection.md)を作成します。

* [Spark コネクタ](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar)をダウンロードします。

* [NOAA National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/) からサンプル データをダウンロードします。 州または地区を選択して **[Search]\(検索\)** を選択します。 次のページで、既定値をそのまま使用して **[Search]\(検索\)** を選択します。 次に、ページの左側にある **[CSV Download]\(CSV ダウンロード\)** を選択すると、結果がダウンロードされます。

* Azure Cosmos DB データ移行ツールの[プリコンパイル済みバイナリ](https://aka.ms/csdmtool)をダウンロードします。

## <a name="create-a-cosmos-db-service-endpoint"></a>Cosmos DB サービス エンドポイントを作成する

1. Azure Databricks ワークスペースを仮想ネットワークにデプロイしたら、[Azure portal](https://portal.azure.com) でその仮想ネットワークに移動します。 Databricks のデプロイを通じて作成されたパブリックおよびプライベート サブネットに注目してください。

   ![仮想ネットワーク サブネット](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. *public-subnet* を選択し、Cosmos DB サービス エンドポイントを作成します。 その後、 **[保存]** を選択します。
   
   ![Cosmos DB サービス エンドポイントの追加](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する

1. Azure Portal を開きます。 画面の左上で、 **[リソースの作成] > [データベース] > [Azure Cosmos DB]** の順に選択します。

2. **[基本]** タブの **[インスタンスの詳細]** に次の設定を入力します。

   |Setting|値|
   |-------|-----|
   |サブスクリプション|*該当するサブスクリプション*|
   |リソース グループ|<*該当するリソース グループ*>|
   |アカウント名|db-vnet-service-endpoint|
   |API|コア (SQL)|
   |Location|米国西部|
   |geo 冗長性|Disable|
   |マルチリージョン書き込み|有効化|

   ![Cosmos DB サービス エンドポイントの追加](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. **[ネットワーク]** タブを選択し、仮想ネットワークを構成します。 

   a. 前提条件として作成した仮想ネットワークを選択し、*public-subnet* を選択します。 *private-subnet* には "*Microsoft AzureCosmosDB のエンドポイントが存在しません*" というメッセージが表示されていることに注意してください。 これは、*public-subnet*のみで Cosmos DB サービス エンドポイントを有効にしたためです。

   b. **[Azure portal からのアクセスを許可する]** が有効になっていることを確認します。 この設定により、Azure portal から Cosmos DB アカウントにアクセスできるようになります。 このオプションが **[拒否]** に設定されている場合は、ご利用のアカウントにアクセスしようとしたときにエラーが発生します。 

   > [!NOTE]
   > このチュートリアルでは必要ありませんが、ローカル コンピューターから Cosmos DB アカウントにアクセスできるようにしたい場合は、 *[自分の IP からのアクセスを許可する]* を有効にすることもできます。 たとえば、Cosmos DB SDK を使用してご利用のアカウントに接続する場合は、この設定を有効にする必要があります。 これが無効になっていると、"アクセスが拒否されました" エラーが発生します。

   ![Cosmos DB アカウントのネットワーク設定](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. **[確認と作成]** を選択した後、 **[作成]** を選択して仮想ネットワーク内に Cosmos DB アカウントを作成します。

5. Cosmos DB アカウントが作成されたら、 **[設定]** の **[キー]** に移動します。 プライマリ接続文字列をコピーし、後で使用できるようテキスト エディターに保存します。

    ![Cosmos DB アカウントの [キー] ページ](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. **[データ エクスプローラー]** と **[新しいコレクション]** を選択して、ご利用の Cosmos DB アカウントに新しいデータベースとコレクションを追加します。

    ![Cosmos DB の新しいコレクション](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Cosmos DB にデータをアップロードする

1. [Cosmos DB のデータ移行ツール](https://aka.ms/csdmtool)のグラフィカル インターフェイス バージョン (**Dtui.exe**) を開きます。

    ![Cosmos DB データ移行ツール](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. **[ソース情報]** タブで、 **[インポート元]** ドロップダウンから **[CSV ファイル]** を選択します。 次に、 **[ファイルの追加]** を選択し、前提条件としてダウンロードした暴風雨データの CSV を追加します。

    ![Cosmos DB データ移行ツールのソース情報](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. **[ターゲット情報]** タブで、実際の接続文字列を入力します。 接続文字列の形式は `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>` です。 前のセクションで保存したプライマリ接続文字列には AccountEndpoint と AccountKey が含まれています。 接続文字列の末尾に `Database=<your database name>` を追加して、 **[確認]** を選択します。 次に、コレクション名とパーティション キーを追加します。

    ![Cosmos DB データ移行ツールのターゲット情報](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. 概要ページが表示されるまで **[次へ]** を選択します。 その後、 **[インポート]** を選択します。

## <a name="create-a-cluster-and-add-library"></a>クラスターを作成してライブラリを追加する

1. [Azure portal](https://portal.azure.com) で Azure Databricks サービスに移動し、 **[ワークスペースの起動]** を選択します。

   ![Databricks ワークスペースの起動](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. 新しいクラスターを作成します。 クラスター名を選択し、それ以外は既定の設定のままにしてください。

   ![新しいクラスターの設定](./media/service-endpoint-cosmosdb/create-cluster.png)

3. クラスターの作成後、そのクラスターのページに移動し、 **[ライブラリ]** タブを選択します。 **[新規インストール]** を選択し、Spark コネクタの jar ファイルをアップロードしてライブラリをインストールします。

    ![Spark コネクタ ライブラリのインストール](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    ライブラリがインストールされたことは、 **[ライブラリ]** タブで確認できます。

    ![Databricks クラスターの [ライブラリ] タブ](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Databricks ノートブックから Cosmos DB にクエリを実行する

1. Azure Databricks ワークスペースに移動して新しい Python ノートブックを作成します。

    ![新しい Databricks ノートブックの作成](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. 次の Python コードを実行して Cosmos DB の接続構成を設定します。 **Endpoint**、**Masterkey**、**Database**、**Collection** を適宜変更します。

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. 次の Python コードを使用してデータを読み込み、一時ビューを作成します。

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. 次のマジック コマンドを使用して、データが返される SQL ステートメントを実行します。

    ```python
    %sql
    select * from storm
    ```

    VNet に導入された Databricks ワークスペースを、サービス エンドポイントに対応した Cosmos DB リソースに正常に接続することができました。 Cosmos DB への接続方法の詳細については、[Azure Cosmos DB Connector for Apache Spark](https://github.com/Azure/azure-cosmosdb-spark) に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、Azure Databricks ワークスペース、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、不必要な課金を回避できます。 Azure Databricks ワークスペースを後で使用する予定がある場合は、クラスターを停止し、後で再起動することができます。 この Azure Databricks ワークスペースの使用を続けない場合は、以下の手順に従って、このチュートリアルで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、 **[リソース グループ]** をクリックしてから、作成したリソース グループの名前をクリックします。

2. リソース グループのページで **[削除]** を選択し、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** を再度選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、仮想ネットワークに Azure Databricks ワークスペースをデプロイし、Cosmos DB Spark コネクタを使用して Databricks から Cosmos DB データにクエリを実行しました。 仮想ネットワークでの Azure Databricks の操作の詳細については、Azure Databricks での SQL Server の使用に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Databricks ノートブックから仮想ネットワーク内の SQL Server Linux Docker コンテナーのクエリを実行する](vnet-injection-sql-server.md)
