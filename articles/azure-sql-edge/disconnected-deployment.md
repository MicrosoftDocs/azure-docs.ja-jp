---
title: Docker を使用して Azure SQL Edge をデプロイする - Azure SQL Edge
description: Docker を使用した Azure SQL Edge のデプロイについて説明します
keywords: SQL Edge, コンテナー, Docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392080"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Docker を使用して Azure SQL Edge をデプロイする

このクイックスタートでは、Docker を使用することで Azure SQL Edge のコンテナー イメージをプルして実行します。 次に、**sqlcmd** と接続して最初のデータベースを作成し、クエリを実行します。

このイメージは、Ubuntu 18.04 に基づく Azure SQL Edge で構成されています。 イメージは Linux の Docker エンジン 1.8+ または Mac/Windows 用 Docker で使用することができます。

## <a name="prerequisites"></a>前提条件

- サポートされているいずれかの Linux ディストリビューションの Docker エンジン 1.8+ または Mac/Windows 用 Docker。 詳細については、「[Install Docker](https://docs.docker.com/engine/installation/)」(Docker をインストールする) を参照してください。 Azure SQL Edge のイメージは Ubuntu 18.04 が基になっているため、Ubuntu 18.04 の Docker ホストを使用することをお勧めします。
- Docker **overlay2** ストレージ ドライバー。 ほとんどのユーザーでは、これが既定値です。 このストレージ プロバイダーを使用しておらず、変更が必要であることがわかった場合は、[overlay2 の構成に関する Docker のドキュメント](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver)上で、その手順と警告について確認してください。
- 10 GB 以上のディスク領域。
- 1 GB 以上の RAM。
- [Azure SQL Edge のハードウェア要件](./features.md#hardware-support)。


## <a name="pull-and-run-the-container-image"></a>コンテナー イメージのプルと実行

次の手順を開始する前に、この記事で上述した推奨シェル (bash、PowerShell、または cmd) を選択していることを確認してください。

1. Microsoft Container Registry から Azure SQL Edge のコンテナー イメージをプルします。

    - Azure SQL Edge のコンテナー イメージをプルする
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> この記事の bash コマンドには、`sudo` が使用されています。 macOS と Windows では、sudo は不要な場合があります。 Linux では、Docker を実行するのに sudo を使いたくない場合は、docker グループを構成し、そのグループにユーザーを追加できます。 詳細については、「[Linux でのインストール後の手順](https://docs.docker.com/engine/install/linux-postinstall/)」を参照してください。

前のコマンドでは、最新の Azure SQL Edge のコンテナー イメージがプルされます。 利用可能なすべてのイメージを確認するには、[azure-sql-egde Docker Hub のページ](https://hub.docker.com/_/microsoft-azure-sql-edge)を参照してください。

2. Docker でコンテナー イメージを実行する場合は、Bash シェル (Linux/macOS) または管理者特権での PowerShell コマンド プロンプトから次のコマンドを使用することができます。
    
    - Developer エディションとして実行される Azure SQL Edge インスタンスを起動する
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Premium エディションとして実行される Azure SQL Edge インスタンスを起動する
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Windows 上の PowerShell を使用してこれらのコマンドを実行している場合は、単一引用符ではなく二重引用符を使用します。


    > [!NOTE]
    > パスワードは Microsoft SQL データベース エンジンの既定のパスワード ポリシーに従う必要があります。従わない場合、コンテナーで SQL エンジンをセットアップできず、動作が停止します。 既定で、パスワードは 8 文字以上の長さにする必要があり、次の 4 つのセットのうち 3 つから文字を含める必要があります。大文字、小文字、10 進数、記号です。 [docker ログ](https://docs.docker.com/engine/reference/commandline/logs/) コマンドを実行することでエラー ログを調査することができます。
    
    次の表は、前の `docker run` の例のパラメーターについて説明しています。

    | パラメーター | 説明 |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  **ACCEPT_EULA** 変数を任意の値に設定し、[使用許諾契約書](https://go.microsoft.com/fwlink/?linkid=2139274)の承諾を確定します。 Azure SQL Edge イメージに必要な設定。 |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!)Password"** | 8 文字以上の、[Azure SQL Edge のパスワード要件](/sql/relational-databases/security/password-policy)を満たす強力なパスワードを指定します。 Azure SQL Edge イメージに必要な設定。 |
    | **-p 1433:1433** | ホスト環境の TCP ポート (最初の値) とコンテナーの TCP ポート (2 番目の値) をマップします。 この例では、コンテナー内では Azure SQL Edge によって TCP 1433 がリッスンされており、これがホスト上ではポート 1433 に公開されます。 |
    | **--name azuresqledge** | ランダムに生成された名前ではなく、コンテナーのカスタム名を指定します。 複数のコンテナーを実行する場合は、この同じ名前を再利用することはできません。 |
    | **-d** | コンテナーをバックグラウンド (デーモン) で実行します |

    Azure SQL Edge のすべての環境変数の完全な一覧については、[環境変数を使用した Azure SQL Edge の構成](configure.md#configure-by-using-environment-variables)に関する記事を参照してください。また、[mssql.conf ファイル](configure.md#configure-by-using-an-mssqlconf-file) を使用して、Azure SQL Edge コンテナーを構成することもできます。

3. Docker コンテナーを表示するには、`docker ps` コマンドを使用します。
   
   ```bash
    sudo docker ps -a
   ```

4. **[STATUS]** 列に **[Up]** の状態が表示されている場合、Azure SQL Edge はコンテナーで実行されており、 **[PORTS]** 列に指定されたポートでリッスンしています。 Azure SQL Edge コンテナーの **[STATUS]** 列に **[Exited]** と表示されている場合は、Azure SQL Edge のドキュメントのトラブルシューティングに関するセクションを参照してください。

    `-h` (ホスト名) のパラメーターも役に立ちますが、簡略化のためこのチュートリアルでは使用しません。 これにより、コンテナーの内部名がカスタム値に変更されます。 これは、次の Transact-SQL クエリで返される名前です。

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    ターゲット コンテナーを特定しやすくするため、`-h` と `--name` を同じ値に設定することをお勧めします。

5. 最後の手順として、`SA_PASSWORD` が `ps -eax` 出力に表示され、同じ名前の環境変数に格納されるため、SA パスワードを変更します。 以下の手順を参照してください。

## <a name="change-the-sa-password"></a>SA パスワードの変更

**SA** アカウントは、セットアップ時に作成される Azure SQL Edge インスタンスのシステム管理者です。 Azure SQL Edge のコンテナーを作成した後、そのコンテナーで `echo $SA_PASSWORD` を実行すると、指定した環境変数 `MSSQL_SA_PASSWORD` が検索できるようになります。 セキュリティのため、SA のパスワードを変更してください。

1. SA ユーザーに使用する強力なパスワードを選択します。

2. `docker exec` を使用して **sqlcmd** を実行し、Transact-SQL でパスワードを変更します。 次の例では、古いパスワード `<YourStrong!Passw0rd>` と新しいパスワード `<YourNewStrong!Passw0rd>` を実際のパスワード値に置き換えます。

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Azure SQL Edge に接続する

次の手順では Azure SQL Edge に接続するためにコンテナー内で Azure SQL Edge コマンド ライン ツールである **sqlcmd** を使用します。

> [!NOTE]
> sqlcmd ツールは、ARM64 バージョンの SQL Edge コンテナー内では使用できません。

1. 実行中のコンテナー内で対話型の Bash シェルを開始するには、`docker exec -it` コマンドを使用します。 次の例の `azuresqledge` は、コンテナーを作成したときに `--name` パラメーターによって指定された名前です。

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. コンテナー内では sqlcmd とローカル接続してください。 既定では sqlcmd はパスにないため、完全なパスを指定する必要があります。

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > コマンド ラインでパスワードを省略すると、入力を求められます。

3. 成功すると、**sqlcmd** コマンド プロンプト `1>` が表示されます。

## <a name="create-and-query-data"></a>データの作成とクエリ

以下のセクションでは、**sqlcmd** と Transact-SQL を使用して新しいデータベースを作成し、データを追加して簡単なクエリを実行します。

### <a name="create-a-new-database"></a>新しいデータベースの作成

次の手順では、`TestDB` という名前の新しいデータベースを作成します。

1. **sqlcmd** のコマンド プロンプトに次の Transact-SQL コマンドを貼り付け、テスト データベースを作成します。

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. 次の行に、サーバー上のすべてのデータベースの名前を返すクエリを記述します。

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>データの挿入

次に、新しいテーブル `Inventory` を作成し、2 つの新しい行を挿入します。

1. **sqlcmd** のコマンド プロンプトで、コンテキストを新しい `TestDB` データベースに切り替えます。

   ```sql
   USE TestDB
   ```

2. `Inventory` という名前の新しいテーブルを作成します。

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. 新しいテーブルにデータを挿入します。

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. 「`GO`」と入力して前のコマンドを実行します。

   ```sql
   GO
   ```

### <a name="select-data"></a>データの選択

ここで、`Inventory` テーブルからデータを返すクエリを実行します。

1. **sqlcmd** のコマンド プロンプトで、数量が 152 より大きい`Inventory` テーブルから行を返すクエリを入力します。

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. 次のコマンドを実行します。

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>sqlcmd コマンド プロンプトの終了

1. **sqlcmd** セッションを終了するには、「`QUIT`」と入力します。

   ```sql
   QUIT
   ```

2. コンテナー内で対話型のコマンド プロンプトを終了するには、`exit` と入力します。 コンテナーは、対話型の Bash シェルを終了した後も引き続き実行されます。

## <a name="connect-from-outside-the-container"></a> コンテナーの外からの接続

SQL 接続をサポートする外部の Linux、Windows、macOS ツールから、Docker コンピューターの Azure SQL Edge インスタンスに接続することもできます。 外部から SQL Edge コンテナーに接続する方法の詳細については、[Azure SQL Edge の接続とクエリ](connect.md)に関する記事を参照してください。

## <a name="remove-your-container"></a>コンテナーの削除

このチュートリアルで使用した Azure SQL Edge のコンテナーを削除する場合は、次のコマンドを実行します。

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> コンテナーを完全に停止して削除すると、コンテナー内のすべての Azure SQL Edge データが削除されます。 データを保持する必要がある場合は、[コンテナーからバックアップ ファイルを作成してコピーする](backup-restore.md)か、[コンテナー データを永続化する手法](configure.md#persist-your-data)を使用します。

## <a name="next-steps"></a>次の手順

- [SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)。
- [IoT Edge を使用して SQL Edge でエンド ツー エンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge でのデータ ストリーミング](stream-data.md)