---
title: Azure portal を使用して Azure SQL Edge をデプロイする
description: Azure portal を使用して Azure SQL Edge をデプロイする方法について調べる
keywords: SQL Edge をデプロイする
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d7d232ada814d5d3c30e7b012c6289f847d641f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395089"
---
# <a name="deploy-azure-sql-edge"></a>Azure SQL Edge のデプロイ 

Azure SQL Edge は、IoT および Azure IoT Edge のデプロイ向けに最適化されたリレーショナル データベース エンジンです。 これは、IoT のアプリケーションおよびソリューションに向けて、パフォーマンスの高いデータ ストレージと処理レイヤーを作成する機能を提供します。 このクイックスタートでは、Azure portal を使用して Azure IoT Edge を介して Azure SQL Edge モジュールの作成を開始する方法を示します。

## <a name="before-you-begin"></a>開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure portal](https://portal.azure.com/) にサインインします。
* [Azure IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)を作成します。
* [Azure IoT Edge デバイス](../iot-edge/how-to-install-iot-edge.md)を作成します。

> [!NOTE]   
> Azure Linux VM を IoT Edge デバイスとしてデプロイする場合は、こちらの[クイックスタート ガイド](../iot-edge/quickstart-linux.md)を参照してください。

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Azure Marketplace から SQL Edge モジュールをデプロイする

Azure Marketplace は、アプリケーションとサービスのオンライン マーケットプレースであり、[IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)など、Azure での実行を認定されてそれに最適化されている、さまざまなエンタープライズ アプリケーションとソリューションを参照できます。 Azure SQL Edge は、マーケットプレースからエッジ デバイスにデプロイできます。

1. Azure Marketplace で Azure SQL Edge モジュールを検索します。<br><br>

   ![MarketPlace での SQL Edge](media/deploy-portal/find-offer-marketplace.png)

2. 要件に最も合うソフトウェア プランを選択し、 **[作成]** をクリックします。 <br><br>

   ![適切なソフトウェア プランの選択](media/deploy-portal/pick-correct-plan.png)

3. [IoT Edge モジュールのターゲット デバイス] ページで、次の詳細情報を指定し、 **[作成]** をクリックします。

   |**フィールド**  |**説明**  |
   |---------|---------|
   |サブスクリプション  |  IoT Hub が作成された Azure サブスクリプション |
   |IoT Hub   |  IoT Edge デバイスが登録され、その後 [デバイスへのデプロイ] オプションを選択する IoT Hub の名前|
   |IoT Edge デバイス名  |  SQL Edge がデプロイされる IoT Edge デバイスの名前 |

4. **[デバイスのモジュールを設定してください:]** ページで、 **[IoT Edge モジュール]** の下にある Azure SQL Edge モジュールをクリックします。 既定のモジュール名は *AzureSQLEdge* に設定されます。 

5. **[IoT Edge モジュールの更新]** ブレードの *[モジュールの設定]* セクションで、 *[IoT Edge モジュール名]* 、 *[再起動ポリシー]* 、 *[必要な状態]* に必要な値を指定します。 

   > [!IMPORTANT]    
   > モジュールの **[イメージの URI]** 設定を変更または更新しないでください。

6. **[IoT Edge モジュールの更新]** ブレードの *[環境変数]* セクションで、環境変数に必要な値を指定します。 Azure SQL Edge 環境変数の詳細な一覧については、「[環境変数を使用して構成する](configure.md#configure-by-using-environment-variables)」を参照してください。 次の既定の環境変数がモジュールに対して定義されています。 

   |**パラメーター**  |**説明**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | 既定値を変更して、SQL Edge 管理者アカウント用の強力なパスワードを指定します。 |
   | MSSQL_LCID   | 既定値を変更して、SQL Edge に使用する目的の言語 ID を設定します。 たとえば、1036 はフランス語です。 |
   | MSSQL_COLLATION | 既定値を変更して、SQL Edge の既定の照合順序を設定します。 この設定は、照合順序に対する言語 ID (LCID) の既定のマッピングをオーバーライドします。 |

   > [!IMPORTANT]    
   > モジュールの **ACCEPT_EULA** 環境変数を変更または更新しないでください。

7. **[IoT Edge モジュールの更新]** ブレードの *[コンテナーの作成オプション]* セクションで、要件に従って次のオプションを更新します。 
   - **ホスト ポート:** 指定したホスト ポートをコンテナーのポート 1433 (既定の SQL ポート) にマップします。
   - **バインド** と **マウント:** 複数の SQL Edge モジュールをデプロイする必要がある場合は、必ずマウントオプションを更新して、永続的なボリューム用の新しいソースとターゲットのペアを作成してください。 マウントとボリュームの詳細については、docker ドキュメントの「[ボリュームの使用](https://docs.docker.com/storage/volumes/)」を参照してください。 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
        "Binds": [
            "sqlvolume:/sqlvolume"
        ],
        "PortBindings": {
            "1433/tcp": [
                {
                    "HostPort": "1433"
                }
            ]
        },
        "Mounts": [
            {
                "Type": "volume",
                "Source": "sqlvolume",
                "Target": "/var/opt/mssql"
            }
        ]
    },
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > 構成の作成設定に定義されている `PlanId` 環境変数を変更しないでください。 この値を変更すると、Azure SQL Edge コンテナーの起動に失敗します。 
   
8. **[IoT Edge モジュールの更新]** ペインで、 **[更新]** をクリックします。
9. デプロイのルートを定義する必要がある場合は、 **[デバイスのモジュールを設定してください]** ページで **[次へ:ルート >]** をクリックします。 それ以外の場合は、 **[確認と作成]** をクリックします。 ルートの構成の詳細については、[モジュールのデプロイと IoT Edge へのルートの確立](../iot-edge/module-composition.md)に関する記事をご覧ください。
11. **[デバイスのモジュールを設定してください]** ページで **[作成]** をクリックします。

## <a name="connect-to-azure-sql-edge"></a>Azure SQL Edge に接続する

次の手順では Azure SQL Edge に接続するためにコンテナー内で Azure SQL Edge コマンド ライン ツールである **sqlcmd** を使用します。

> [!NOTE]      
> SQL コマンド ライン ツール (sqlcmd) は、ARM64 バージョンの Azure SQL Edge コンテナー内では使用できません。

1. 実行中のコンテナー内で対話型の Bash シェルを開始するには、`docker exec -it` コマンドを使用します。 次の例の `azuresqledge` は、ユーザーの IoT Edge モジュールの `Name` パラメーターによって指定された名前です。

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

後続のセクションでは **sqlcmd** と Transact-SQL to を使用し、新しいデータベースを作成し、データを追加し、クエリを実行します。

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

SQL 接続をサポートしている外部の Linux、Windows、または macOS ツールから、Azure SQL Edge インスタンスに対して SQL クエリを接続して実行することができます。 外部から SQL Edge コンテナーに接続する方法の詳細については、[Azure SQL Edge の接続とクエリ](./connect.md)に関する記事を参照してください。

このクイックスタートでは、SQL Edge モジュールを IoT Edge デバイスにデプロイしました。 

## <a name="next-steps"></a>次の手順

- [SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)
- [IoT Edge を使用して SQL Edge でエンド ツー エンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)
- [Azure SQL Edge でのデータ ストリーミング](stream-data.md)
- [デプロイ エラーのトラブルシューティング](troubleshoot.md)