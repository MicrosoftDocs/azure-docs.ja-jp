---
title: Azure Databricks ノートブックから仮想ネットワーク内の SQL Server Linux Docker コンテナーのクエリを実行する
description: この記事では、仮想ネットワークに Azure Databricks をデプロイする (VNet インジェクションとも呼ばれる) 方法について説明します。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59287622"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>チュートリアル:Azure Databricks ノートブックから仮想ネットワーク内の SQL Server Linux Docker コンテナーのクエリを実行する

このチュートリアルでは、仮想ネットワーク内の SQL Server Linux Docker コンテナーと Azure Databricks を統合する方法を説明します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Databricks ワークスペースを仮想ネットワークにデプロイする
> * Linux 仮想マシンをパブリック ネットワークにインストールする
> * Docker をインストールする
> * Microsoft SQL Server on Linux コンテナー をインストールする
> * Databricks ノートブックから JDBC を使用して SQL Server のクエリを実行する

## <a name="prerequisites"></a>前提条件

* [仮想ネットワーク内に Databricks ワークスペース](quickstart-create-databricks-workspace-vnet-injection.md)を作成します。

* [Ubuntu for Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab) をインストールします。

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)をダウンロードします。

## <a name="create-a-linux-virtual-machine"></a>Linux 仮想マシンの作成

1. Azure portal で、**[仮想マシン]** のアイコンを選択します。 次に、**[+ 追加]** を選択します。

    ![新しい Azure 仮想マシンを追加する](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. **[基本]** タブで [Ubuntu Server 16.04 LTS] を選択します。 VM のサイズを B1ms に変更します。これには 1 つの VCPUS と 2 GB の RAM が含まれます。 Linux SQL Server Docker コンテナーの最小要件では 2 GB です。 管理者のユーザー名とパスワードを選択します。

    ![新しい仮想マシンの構成の [基本] タブ](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. **[ネットワーク]** タブに移動します。仮想ネットワークと、Azure Databricks クラスターが含まれているパブリック サブネットを選択します。 **[確認と作成]** で、**[作成]** を選択して仮想マシンをデプロイします。

    ![新しい仮想マシンの構成の [ネットワーク] タブ](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. デプロイが完了したら、仮想マシンに移動します。 **[概要]** のパブリック IP アドレスと仮想ネットワーク/サブネットに注目してください。 **[パブリック IP アドレス]** を選択します。

    ![仮想マシンの概要](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. **[割り当て]** を **[静的]** に変更し、**DNS 名ラベル**を入力します。 **[保存]** を選択し、仮想マシンを再起動します。

    ![パブリック IP アドレスの構成](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. **[設定]** で **[ネットワーク]** タブを選択します。 Azure Databricks のデプロイ時に作成されたネットワーク セキュリティ グループが、仮想マシンに関連付けられていることに注意してください。 **[受信ポートの規則を追加する]** を選択します。

7. SSH 用にポート 22 を開く規則を追加します。 次の設定を使用します。
    
    |Setting|推奨値|説明|
    |-------|---------------|-----------|
    |ソース|IP アドレス|IP アドレスでは、特定のソース IP アドレスからの受信トラフィックが、このルールによって許可または拒否されることを指定します。|
    |ソース IP アドレス|<ご使用のパブリック IP\>|パブリック IP アドレスを入力します。 パブリック IP アドレスは、[bing.com](https://www.bing.com/) にアクセスして **"my IP"** を検索することで見つけることができます。|
    |ソース ポート範囲|*|すべてのポートからのトラフィックを許可します。|
    |宛先|IP アドレス|IP アドレスでは、特定のソース IP アドレスの送信トラフィックが、このルールによって許可または拒否されることを指定します。|
    |送信先 IP アドレス|<ご使用の VM のパブリック IP\>|仮想マシンのパブリック IP アドレスを入力します。 これは仮想マシンの **[概要]** ページで見つかります。|
    |宛先ポート範囲|22|SSH 用にポート 22 を開きます。|
    |優先順位|290|ルールに優先順位を付けます。|
    |Name|ssh-databricks-tutorial-vm|ルールに名前を付けます。|


    ![ポート 22 の受信セキュリティ規則を追加する](./media/vnet-injection-sql-server/open-port.png)

8. 次の設定を使用して、SQL 用にポート 1433 を開く規則を追加します。

    |Setting|推奨値|説明|
    |-------|---------------|-----------|
    |ソース|IP アドレス|IP アドレスでは、特定のソース IP アドレスからの受信トラフィックが、このルールによって許可または拒否されることを指定します。|
    |ソース IP アドレス|10.179.0.0/16|仮想ネットワークのアドレス範囲を入力します。|
    |ソース ポート範囲|*|すべてのポートからのトラフィックを許可します。|
    |宛先|IP アドレス|IP アドレスでは、特定のソース IP アドレスの送信トラフィックが、このルールによって許可または拒否されることを指定します。|
    |送信先 IP アドレス|<ご使用の VM のパブリック IP\>|仮想マシンのパブリック IP アドレスを入力します。 これは仮想マシンの **[概要]** ページで見つかります。|
    |宛先ポート範囲|1433|SQL Server 用にポート 22 を開きます。|
    |優先順位|300|ルールに優先順位を付けます。|
    |Name|sql-databricks-tutorial-vm|ルールに名前を付けます。|

    ![ポート 1433 の受信セキュリティ規則を追加する](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Docker コンテナーで SQL Server を実行する

1. [Ubuntu for Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)、または仮想マシンへの SSH による接続を確立するためのその他のツールを開きます。 Azure portal で仮想マシンに移動し、**[接続]** を選択して接続するために必要な SSH コマンドを取得します。

    ![仮想マシンへの接続](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ubuntu ターミナルでコマンドを入力し、仮想マシンを構成したときに作成した管理者パスワードを入力します。

    ![Ubuntu ターミナルでの SSH サインイン](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 次のコマンドを使用して、Docker を仮想マシンにインストールします。

    ```bash
    sudo apt-get install docker.io
    ```

    次のコマンドを使用して Docker のインストールを検証します。

    ```bash
    sudo docker --version
    ```

4. イメージをインストールします。

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    イメージをチェックします。

    ```bash
    sudo docker images
    ```

5. イメージからコンテナーを実行します。

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    コンテナーが動作していることを確認します。

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL Database の作成

1. SQL Server Management Studio を開き、サーバー名と SQL 認証を使用してサーバーに接続します。 サインイン ユーザー名は **SA** で、パスワードは Docker コマンドで設定したパスワードです。 このコマンド例のパスワードは `Password1234` です。

    ![SQL Server Management Studio を使用して SQL Server に接続する](./media/vnet-injection-sql-server/ssms-login.png)

2. 正常に接続したら、**[新しいクエリ]** を選択し、次のコード スニペットを入力してデータベース、テーブルを作成し、テーブルにいくつかのレコードを挿入します。

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![クエリを実行して SQL Server データベースを作成する](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Azure Databricks から SQL Server のクエリを実行する

1. Azure Databricks ワークスペースに移動し、前提条件の一部としてクラスターを作成済みであることを確認します。 次に、**[ノートブックの作成]** を選択します。 ノートブックに名前を付け、言語として *[Python]* を選んで、作成済みのクラスターを選びます。

    ![Databricks ノートブックの新しい設定](./media/vnet-injection-sql-server/create-notebook.png)

2. 次のコマンドを使用して、SQL Server 仮想マシンの内部 IP アドレスを ping します。 この ping は成功するはずです。 そうでない場合は、コンテナーが実行されていることを確認し、ネットワーク セキュリティ グループ (NSG) の構成を確認してください。

    ```python
    %sh
    ping 10.179.64.4
    ```

    nslookup コマンドを使用して確認することもできます。

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. SQL Server の ping が正常に行われたら、データベースとテーブルのクエリを実行できます。 次の python コードを実行します。

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、Azure Databricks ワークスペース、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、不必要な課金を回避できます。 Azure Databricks ワークスペースを後で使用する予定がある場合は、クラスターを停止し、後で再起動することができます。 この Azure Databricks ワークスペースの使用を続けない場合は、以下の手順に従って、このチュートリアルで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックしてから、作成したリソース グループの名前をクリックします。

2. リソース グループのページで **[削除]** を選択し、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** を再度選択します。

## <a name="next-steps"></a>次の手順

次の記事に進んで、Azure Databricks を使ったデータの抽出、変換、および読み込みの方法について学びましょう。
> [!div class="nextstepaction"]
> [チュートリアル:Azure Databricks を使用してデータの抽出、変換、読み込みを行う](databricks-extract-load-sql-data-warehouse.md)
