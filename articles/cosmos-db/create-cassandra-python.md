---
title: クイック スタート:Cassandra API (Python) - Azure Cosmos DB
description: このクイック スタートでは、Azure Cosmos DB の Apache Cassandra API を使って、Python でプロファイル アプリケーションを作成する方法を示します
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea5a443caba25b7b5e6d843408c12e61a55b9e10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047441"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>クイック スタート:Python SDK と Azure Cosmos DB を使用して Cassandra アプリを構築する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

このクイックスタートでは、Azure Cosmos DB Cassandra API アカウントを作成し、GitHub からクローンした Cassandra Python アプリを使用して Cassandra データベースとコンテナーを作成します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。
- [Python 2.7 または 3.6 以降](https://www.python.org/downloads/)。
- [Git](https://git-scm.com/downloads).
- [Apache Cassandra 用 Python ドライバー](https://github.com/datastax/python-driver)。

## <a name="create-a-database-account"></a>データベース アカウントの作成

ドキュメント データベースを作成するには、Azure Cosmos DB を含んだ Cassandra アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

GitHub から Cassandra API アプリを複製し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. コマンド プロンプトを開きます。 `git-samples` という名前の新しいフォルダーを作成します。 その後、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コードでデータベース リソースを作成する方法に関心がある場合は、次のスニペットで確認できます。 スニペットはすべて *pyquickstart.py* ファイルから取得します。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

* `cluster` は、Azure portal から取得される `contactPoint` と `port` の情報で初期化されます。 その後、`cluster` は、`connect()` メソッドを使用して Azure Cosmos DB Cassandra API に接続します。 ユーザー名とパスワード、さらに既定の証明書または明示的な証明書 (構成ファイル内で指定されている場合) を使用して、承認された接続が確立されます。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="authenticateAndConnect":::

* 新しいキースペースが作成されます。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createKeyspace":::

* 新しいテーブルが作成されます。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createTable":::

* キー/値エンティティが挿入されます。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="insertData":::

* クエリを実行して、すべてのキー値を取得します。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryAllItems":::
    
* クエリを実行して、キーの値を取得します。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryByID":::

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 アプリはこの接続文字列によって、ホストされているデータベースと通信できます。

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、 **[接続文字列]** を選択します。 

1. 画面右側の :::image type="icon" source="./media/create-cassandra-python/copy.png"::: ボタンを使用して、一番上の [CONTACT POINT]\(コンタクト ポイント\) の値をコピーします。 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="Azure portal の接続文字列ブレードに表示されるアクセス ユーザー名、パスワード、コンタクト ポイントをコピーする":::

1. *config.py* ファイルを開きます。 

1. 10 行目の `<FILLME>` にポータルのコンタクト ポイントの値を貼り付けます。

    10 行目は次のようになります。 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. ポータルの [USERNAME]\(ユーザー名\) の値をコピーし、6 行目の `<FILLME>` に貼り付けます。

    6 行目は次のようになります。 

    `'username': 'cosmos-db-quickstart',`
    
1. ポータルの [PASSWORD]\(パスワード\) の値をコピーし、8 行目の `<FILLME>` に貼り付けます。

    8 行目は次のようになります。

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. *config.py* ファイルを保存します。
    
## <a name="use-the-x509-certificate"></a>X509 証明書を使う

1. Baltimore CyberTrust Root 証明書を [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt) からローカルにダウンロードします。 ファイル拡張子 *.cer* を使用して、ファイルの名前を変更します。

   証明書のシリアル番号は `02:00:00:b9`、SHA1 フィンガープリントは `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74` です。

2. *pyquickstart.py* を開き、`path\to\cert` を新しい証明書を示すように変更します。

3. *pyquickstart.py* を保存します。

## <a name="run-the-python-app"></a>Python のアプリを実行する

1. Git ターミナルで cd コマンドを使用して、`azure-cosmos-db-cassandra-python-getting-started` フォルダーに変更します。 

2. 次のコマンドを実行して、必要なモジュールをインストールします。

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. 次のコマンドを実行して、Python アプリケーションを開始します。

    ```
    python pyquickstart.py
    ```

3. コマンド ラインから予想される結果を確認します。

    Ctrl + C キーを押してプログラムの実行を停止し、コンソール ウィンドウを閉じます。 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="出力を表示して検証する":::
    
4. Azure portal で **Data Explorer** を開き、この新しいデータのクエリ、変更、操作を行います。 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="データ エクスプローラーでのデータの表示":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Cassandra API を使用して Azure Cosmos DB アカウントを作成する方法と、Cassandra データベースとコンテナーを作成する Cassandra Python アプリの実行方法について説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB への Cassandra データのインポート](cassandra-import-data.md)

