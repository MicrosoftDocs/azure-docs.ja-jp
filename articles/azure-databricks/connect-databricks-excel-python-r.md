---
title: 'Excel、Python、R から Azure Databricks に接続する '
description: Simba ドライバーを使用して Excel、Python、R から Azure Databricks に接続する方法を説明します。
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: c57550a8b683ad8f184884374c4f09216417fc40
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995633"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Excel、Python、R から Azure Databricks に接続する

この記事では、Databricks ODBC ドライバーを使用してMicrosoft Excel、Python、R 言語で Azure Databricks に接続する方法を説明します。 接続を確立すると、Excel、Python、R クライアントから Azure Databricks にアクセスできるようになります。 クライアントを使用して、データをさらに分析することもできます。 

## <a name="prerequisites"></a>前提条件

* Azure Databricks ワークスペース、Spark クラスター、クラスターに関連付けられたサンプル データが必要です。 これらの前提条件がまだ揃っていない場合は、「[クイックスタート: Azure Portal を使用して Azure Databricks で Spark ジョブを実行する](quickstart-create-databricks-workspace-portal.md)」の手順を実行してください。

* Databricks ODBC ドライバーを [Databricks ドライバー ダウンロード ページ](https://databricks.com/spark/odbc-driver-download)からダウンロードします。 Azure Databricks への接続元のアプリケーションに応じて、32 ビットまたは 64 ビット バージョンをインストールします。 たとえば、Excel から接続する場合は 32 ビット バージョンのドライバーをインストールします。 R や Python から接続するには、64 ビット バージョンのドライバーをインストールします。

* Databricks に個人用アクセス トークンを設定します。 手順については、[トークンの管理](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)に関するページをご覧ください。

## <a name="set-up-a-dsn"></a>DSN のセットアップ

データ ソース名 (DSN) には特定のデータ ソースの情報が含まれます。 ODBC ドライバーはデータ ソースに接続するためにこの DSN が必要です。 このセクションでは、Databricks ODBC ドライバーで Microsoft Excel、Python、R などのクライアントから Azure Databricks に接続する際に使用できるDSN を設定します。

1. Azure Databricks ワークスペースから Databricks クラスターに移動します。

    ![Databricks クラスターを開く](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Databricks クラスターを開く")

2. **[構成]** タブの下で、**[JDBC/ODBC]\(JDBC/ODBC\)** タブをクリックし、**[Server Hostname]\(サーバー ホスト名\)** と **[HTTP Path]\(HTTP パス\)** の値をコピーします。 この記事の手順を完了するにはこれらの値が必要です。

    ![Databricks 構成を取得する](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks 構成を取得する")

3. 自分のコンピューターで、アプリケーションに応じた **[ODBC データ ソース]** アプリケーション (32 ビットまたは 64 ビット) を起動します。 Excel から接続するには 32 ビット バージョンを使用します。 R や Python から接続するには 64 ビット バージョンを使用します。

    ![ODBC を起動する](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC アプリを起動する")

4. **[ユーザー DSN]** タブの **[追加]** をクリックします。 **[新規データ ソースの作成]** ダイアログ ボックスで、**[Simba Spark ODBC Driver]\(Simba Spark ODBC ドライバー\)** を選択し、**[完了]** をクリックします。

    ![ODBC を起動する](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC アプリを起動する")

5. **[Simba Spark ODBC Driver]\(Simba Spark ODBC ドライバー\)** ダイアログ ボックスに次の値を入力します。

    ![DSN を構成する](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN を構成する")

    次の表に、このダイアログ ボックスに指定する値に関する情報を示します。
    
    |フィールド  | 値  |
    |---------|---------|
    |**データ ソース名**     | データ ソースの名前を指定します。        |
    |**ホスト**     | Databricks ワークスペースの *[Server hostname]\(サーバー ホスト名\)* からコピーした値を指定します。        |
    |**ポート**     | 「*443*」を入力します。        |
    |**認証** > **機構**     | *[ユーザー名とパスワード]* を選択します。        |
    |**ユーザー名**     | 「*token*」を入力します。        |
    |**パスワード**     | Databricks ワークスペースからコピーしたトークン値を入力します。 |
    
    DSN 設定ダイアログ ボックスでさらに次の手順を実行します。
    
    * **[HTTP Options]\(HTTP オプション\)** をクリックします。 ダイアログ ボックスが開いたら、Databricks ワークスペースからコピーした *[HTTP Path]\(HTTP パス\)* の値を貼り付けます。 Click **OK**.
    * **[SSL Options]\(SSL オプション\)** をクリックします。 ダイアログ ボックスが開いたら、**[SSL を有効にする]** チェック ボックスをオンにします。 Click **OK**.
    * **[テスト]** をクリックして Azure Databricks への接続をテストします。 **[OK]** をクリックして構成を保存します。
    * **[ODBC データ ソース アドミニストレーター]** ダイアログ ボックスで **[OK]** をクリックします。

これで、使用する DSN が設定されました。 次のセクションでは、この DSN を使用して Excel、Python、R から Azure Databricks に接続します。

## <a name="connect-from-microsoft-excel"></a>Microsoft Excel からの接続

このセクションでは、作成した DSN を使用して、Azure Databricks から Microsoft Excel にデータをプルします。 開始する前に、ご使用のコンピューターに Microsoft Excel がインストールされていることを確認します。 Excel の試用版は [Microsoft Excel 試用版リンク](https://products.office.com/excel)で使用できます。

1. Microsoft Excel で空のブックを開きます。 **[データ]** リボンで **[データ取り出し]** をクリックします。 **[その他のデータ ソース]** をクリックし、**[ODBC から]** をクリックします。

    ![Excel から ODBC を起動する](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Excel から ODBC を起動する")

2. **[ODBC から]** ダイアログ ボックスで、前に作成した DSN を選択して **[OK]** をクリックします。

    ![DSN を選択する](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN を選択する")

3. 資格情報の入力を求められたら、ユーザー名として「**token**」を入力します。 パスワードには、Databricks ワークスペースから取得したトークン値を指定します。

    ![Databricks の資格情報を指定する](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN を選択する")

4. [ナビゲーター] ウィンドウで、Excel に読み込む Databricks のテーブルを選択し、**[読み込む]** をクリックします。 

    ![Excel にデータを読み込む](./media/connect-databricks-excel-python-r/excel-load-data.png "Excel にデータを読み込む")

Excel のブックにデータを取得したら、分析操作を実行できます。

## <a name="connect-from-r"></a>R からの接続

> [!NOTE]
> このセクションでは、デスクトップ上で稼働している R Studio クライアントを Azure Databricks に統合する方法について説明します。 Azure Databricks クラスター自体の上で R Studio を使用する方法については、「[R Studio on Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html)」(Azure Databricks 上での R Studio) を参照してください。

このセクションでは、R 言語 IDE を使用して Azure Databricks にあるデータを参照します。 開始する前に、ご使用のコンピューターに以下がインストールされている必要があります。

* R 言語の IDE。 この記事では、RStudio for Desktop を使用します。 これは [R Studio ダウンロード](https://www.rstudio.com/products/rstudio/download/)からインストールできます。
* IDE として RStudio for Desktop を使用する場合は、[https://aka.ms/rclient/](https://aka.ms/rclient/) から Microsoft R Client もインストールしてください。 

RStudio を開き、次の手順を実行します。

- `RODBC` パッケージを参照します。 これにより、前に作成した DSN を使用して Azure Databricks に接続できます。
- DSN を使用して接続を確立します。
- Azure Databricks のデータに対して SQL クエリを実行します。 次のスニペットで、*radio_sample_data* は Azure Databricks に既に存在しているテーブルです。
- クエリに対して何らかの操作を実行して出力を確認します。 

次のコード スニペットはこれらのタスクを実行します。

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Python からの接続

このセクションでは、Python IDE (IDLE など) を使用して Azure Databricks にあるデータを参照します。 開始する前に、次の前提条件を完了しておく必要があります。

* Python を[ここ](https://www.python.org/downloads/)からインストールします。 このリンクから Python をインストールすると IDLE もインストールされます。

* コンピューターのコマンド プロンプトで `pyodbc` パッケージをインストールします。 次のコマンドを実行します。

      pip install pyodbc

IDLE を開き、次の手順を実行します。

- `pyodbc` パッケージをインポートします。 これにより、前に作成した DSN を使用して Azure Databricks に接続できます。
- 前に作成した DSN を使用して接続を確立します。
-  作成した接続を使用して SQL クエリを実行します。 次のスニペットで、*radio_sample_data* は Azure Databricks に既に存在しているテーブルです。
- クエリに対して操作を実行して出力を確認します。

次のコード スニペットはこれらのタスクを実行します。

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>次の手順

* Azure Databricks にデータをインポートできるソースについては、[Azure Databricks のデータ ソース](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)に関するページをご覧ください。


