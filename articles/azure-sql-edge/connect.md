---
title: Azure SQL Edge の接続とクエリ
description: Azure SQL Edge に接続してクエリを実行する方法について説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395208"
---
# <a name="connect-and-query-azure-sql-edge"></a>Azure SQL Edge の接続とクエリ

Azure SQL Edge では、コンテナーをデプロイした後、次のいずれかの場所から SQL データベース エンジンに接続できます。

- コンテナー内
- 同じホストで実行されている別の Docker コンテナーから
- ホスト マシンから
- ネットワーク上の他のクライアント コンピューターから

## <a name="tools-to-connect-to-azure-sql-edge"></a>Azure SQL Edge に接続するためのツール

次のいずれかの一般的なツールから Azure SQL Edge インスタンスに接続できます。

* [sqlcmd](/sql/linux/sql-server-linux-setup-tools): sqlcmd クライアント ツールは、Azure SQL Edge のコンテナー イメージに既に含まれています。 対話型の Bash シェルで実行中のコンテナーにアタッチする場合は、ツールをローカルで実行できます。 SQL クライアント ツールは、ARM64 プラットフォームでは使用できません。そのため、ARM64 バージョンの SQL Edge コンテナーには含まれていません。 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

ネットワーク マシンから Azure SQL Edge データベース エンジンに接続するには、次のものが必要です。

- **ホスト マシンの IP アドレスまたはネットワーク名**:これは、Azure SQL Edge コンテナーが実行されているホスト マシンです。
- **Azure SQL Edge コンテナーのホスト ポートのマッピング**:これは、Docker コンテナー ポートからホスト上のポートへのマッピングです。 コンテナー内では、Azure SQL Edge は常にポート 1433 にマップされます。 これは、必要に応じて変更できます。 ポート番号を変更するには、Azure IoT Edge の Azure SQL Edge モジュールの **[コンテナーの作成オプション]** を更新します。 次の例では、コンテナーのポート 1433 がホストのポート 1600 にマッピングされています。

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Azure SQL Edge インスタンスの SA パスワード**:これは、Azure SQL Edge のデプロイ時に `SA_PASSWORD` 環境変数に指定された値です。

## <a name="connect-to-the-database-engine-from-within-the-container"></a>コンテナー内からデータベース エンジンに接続する

[SQL Server コマンドライン ツール](/sql/linux/sql-server-linux-setup-tools)は、Azure SQL Edge のコンテナー イメージに含まれています。 対話型のコマンド プロンプトを使用してコンテナーにアタッチすると、ツールをローカルで実行できます。 SQL クライアント ツールは、ARM64 プラットフォームでは使用できません。そのため、ARM64 バージョンの SQL Edge コンテナーには含まれていません。 

1. 実行中のコンテナー内で対話型の Bash シェルを開始するには、`docker exec -it` コマンドを使用します。 次の例では、`e69e056c702d` はコンテナー ID です。

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > 常にコンテナー ID 全体を指定する必要はありません。 一意に識別するのに十分な文字を指定するだけでかまいません。 したがって、この例では、完全な ID ではなく `e6` または `e69` を使用すれば十分な可能性があります。

2. コンテナー内では sqlcmd とローカル接続してください。 既定では sqlcmd はパスにないため、完全なパスを指定する必要があります。

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. sqlcmd が完了したら、「`exit`」と入力します。

4. 対話型コマンド プロンプトでの操作が完了したら、「`exit`」と入力します。 コンテナーは、対話型の Bash シェルを終了した後も引き続き実行されます。

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>同じホスト上の別のコンテナーから Azure SQL Edge に接続する

同じホスト上で実行されている 2 つのコンテナーは同じ Docker ネットワーク上にあるため、サービスのコンテナー名とポート アドレスを使用して簡単にアクセスできます。 たとえば、同じホスト上の別の Python モジュール (コンテナー) から Azure SQL Edge のインスタンスに接続している場合は、次のような接続文字列を使用できます (この例では、既定のポートでリッスンするように Azure SQL Edge が構成されていることを前提としています)。

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>別のネットワーク マシンから Azure SQL Edge に接続する

ネットワーク上の別のマシンから Azure SQL Edge のインスタンスに接続することもできます。 そのためには、Docker ホストの IP アドレスと、Azure SQL Edge コンテナーがマップされているホスト ポートを使用します。 たとえば、Docker ホストの IP アドレスが *xxx.xxx.xxx.xxx* であり、Azure SQL Edge コンテナーがホストポート *1600* にマップされている場合、Azure SQL Edge のインスタンスのサーバー アドレスは *xxx.xxx.xxx.xxx,1600* になります。 更新された Python スクリプトは次のようになります。

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Windows マシン上で実行されている SQL Server Management Studio を使用して Azure SQL Edge のインスタンスに接続するには、[SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms) に関するページを参照してください。

Windows、Mac、または Linux マシン上で Visual Studio Code を使用して Azure SQL Edge のインスタンスに接続するには、[Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode) に関するページを参照してください。

Windows、Mac、または Linux マシン上で Azure Data Studio を使用して Azure SQL Edge のインスタンスに接続するには、[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[接続とクエリ](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Linux に SQL Server ツールをインストールする](/sql/linux/sql-server-linux-setup-tools)