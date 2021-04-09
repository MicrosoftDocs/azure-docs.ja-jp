---
title: Azure SQL Edge の構成
description: Azure SQL Edge の構成について説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 0c49f5ab9f10456c32f7f8516cba0e851fa80e74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392335"
---
# <a name="configure-azure-sql-edge"></a>Azure SQL Edge の構成

Azure SQL Edge では、次の 2 つのオプションのいずれかを使用した構成がサポートされています。

- 環境変数
- /var/opt/mssql フォルダーに配置される mssql.conf フォルダー

> [!NOTE]
> 環境変数を設定すると、mssql.conf ファイルに指定した設定がオーバーライドされます。

## <a name="configure-by-using-environment-variables"></a>環境変数を使用して構成する

Azure SQL Edge では、SQL Edge コンテナーの構成に使用できるいくつかの異なる環境変数が公開されています。 これらの環境変数は、SQL Server on Linux で使用できる環境変数のサブセットです。 SQL Server on Linux 環境変数の詳細については、[環境変数](/sql/linux/sql-server-linux-configure-environment-variables/)に関する記事をご覧ください。

次の新しい環境変数が Azure SQL Edge に追加されました。 

| 環境変数 | 説明 | 値 |     
|-----|-----| ---------- | 
| **PlanId** | 初期化中に使用される Azure SQL Edge SKU を指定します。 この環境変数は、Azure IoT Edge を使用して Azure SQL Edge をデプロイする場合にのみ必要です。 | **sde-developer-on-iot-edge** または **asde-premium-on-iot-edge** | 
| **MSSQL_TELEMETRY_ENABLED** | 使用状況および診断データの収集を有効または無効にします。 | TRUE または FALSE |  
| **MSSQL_TELEMETRY_DIR** | 使用状況および診断データ コレクションの監査ファイルのターゲット ディレクトリを設定します。 | SQL Edge コンテナー内のフォルダーの場所。 このフォルダーは、マウント ポイントまたはデータ ボリュームのいずれかを使用してホスト ボリュームにマップできます。 | 
| **MSSQL_PACKAGE** | デプロイする dacpac または bacpac パッケージの場所を指定します。 | dacpac パッケージまたは bacpac パッケージが格納されているフォルダー、ファイル、または SAS URL。 詳細については、[SQL Edge での SQL Database DACPAC および BACPAC パッケージのデプロイ](deploy-dacpac.md)に関するページをご覧ください。 |


次の SQL Server on Linux 環境変数は、Azure SQL Edge ではサポートされていません。 この環境変数を定義しても、コンテナーの初期化中に無視されます。

| 環境変数 | 説明 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 可用性グループを有効にします。 たとえば、**1** は有効、**0** は無効です。 |

> [!IMPORTANT]
> SQL Edge の **MSSQL_PID** 環境変数には、有効な値として **Premium** と **Developer** のみを指定できます。 Azure SQL Edge では、プロダクト キーを使用した初期化はサポートされていません。

### <a name="specify-the-environment-variables"></a>環境変数を指定する

[Azure portal](deploy-portal.md) を使用してサービスをデプロイする場合は、SQL Edge 用の環境変数を指定します。 それらは、モジュールのデプロイの **[環境変数]** セクションに追加することも、 **[コンテナー作成オプション]** の一部として追加することもできます。

**[環境変数]** に値を追加します。

![環境変数リストを使用して設定する](media/configure/set-environment-variables.png)

**[コンテナー作成オプション]** に値を追加します。

![コンテナー作成オプションを使用して設定する](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> 切断されたデプロイ モードでは、`-e` または `--env`、または `docker run` コマンドの `--env-file` オプションを使用して環境変数を指定できます。

## <a name="configure-by-using-an-mssqlconf-file"></a>mssql.conf ファイルを使用して構成する

Azure SQL Edge には、SQL Server on Linux に含まれているような [mssql-conf 構成ユーティリティ](/sql/linux/sql-server-linux-configure-mssql-conf/)は含まれていません。 mssql.conf ファイルを手動で構成し、SQL Edge モジュールの /var/opt/mssql/ フォルダーにマップされている永続的なストレージ ドライブに配置する必要があります。 Azure Marketplace から SQL Edge をデプロイする場合、このマッピングは **[コンテナー作成オプション]** で **[マウント]** オプションとして指定されます。

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

次の新しい mssql.conf オプションが Azure SQL Edge 用に追加されました。 

|オプション|説明|
|:---|:---|
|**customerfeedback** | SQL Server によって Microsoft にフィードバックが送信されるかどうかを選択します。 詳細については、「[使用状況と診断データの収集を無効にする](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)」を参照してください|      
|**userrequestedlocalauditdirectory** | 使用状況および診断データ コレクションの監査ファイルのターゲット ディレクトリを設定します。 詳細については、「[使用状況と診断データの収集のローカル監査](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection)」を参照してください |        

次の mssql.conf のオプションは、SQL Edge には適用されません。

|オプション|説明|
|:---|:---|
|**カスタマー フィードバック** | SQL Server によって Microsoft にフィードバックが送信されるかどうかを選択します。 |
|**データベース メール プロファイル** | SQL Server on Linux の既定のデータベース メール プロファイルを設定します。 |
|**高可用性** | 可用性グループを有効にします。 |
|**Microsoft 分散トランザクション コーディネーター** | Linux で MSDTC の構成とトラブルシューティングを行います。 SQL Edge では、分散トランザクションに関連する追加の構成オプションはサポートされていません。 このような追加の構成オプションの詳細については、「[MSDTC の構成](/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)」を参照してください。 |
|**ML サービスの EULA** | Azure Machine Learning パッケージ用の R および Python の EULA に同意します。 これは SQL Server 2019 のみに適用されます。|
|**outboundnetworkaccess** |[Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) の R、Python、および Java 拡張機能の送信ネットワーク アクセスを有効にします。|

SQL Edge では次の例の mssql.conf ファイルが機能します。 mssql.conf ファイルの形式の詳細については、「[mssql.conf の書式](/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)」をご覧ください。

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>非ルート ユーザーとして Azure SQL Edge を実行する

既定では、Azure SQL Edge コンテナーは非ルート ユーザーまたはグループを使用して実行されます。 Azure Marketplace を通して (または docker run を使用して) デプロイされると、異なるユーザーやグループが指定されていない限り、SQL Edge コンテナーは mssql (非ルート) ユーザーとして起動されます。 デプロイ時に異なる非ルート ユーザーを指定するには、コンテナー作成オプションの下に、キーと値のペア `*"User": "<name|uid>[:<group|gid>]"*` を追加します。 下の例では、SQL Edge はユーザー `*IoTAdmin*` として起動するように構成されています。

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

マウントされたボリューム上にある DB ファイルへのアクセスを非ルート ユーザーに許可するには、コンテナーを実行するのに使用するユーザーまたはグループが、永続的なファイル ストレージに対する読み取りおよび書き込みアクセス許可を持っているようにします。 下の例では、ファイルの所有者として、user_id が 10001 である非ルート ユーザーを設定しています。 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>以前の CTP リリースからのアップグレード

以前の CTP の Azure SQL Edge は、ルート ユーザーとして実行するように構成されていました。 以前の CTP からアップグレードする場合は、以下のオプションを使用できます

- 引き続きルート ユーザーを使用する - ルート ユーザーを使用し続けるには、コンテナー作成オプションの下に、キーと値のペア `*"User": "0:0"*` を追加します。
- 既定の mssql ユーザーを使用する - 既定の mssql ユーザーを使用するには、以下の手順に従います
  - Docker ホストに mssql という名前のユーザーを追加します。 下の例では、ID が 10001 のユーザー mssql を追加しています。 このユーザーは、ルート グループにも追加されます。
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - データベース ファイルがあるディレクトリまたはマウント ボリュームに対するアクセス許可を変更します 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- 異なる非ルート ユーザー アカウントを使用する - 異なる非ルート ユーザー アカウントを使用するには
  - コンテナー作成オプションの下にキーと値のペア `*"User": "user_name | user_id*` を追加することを指定するように、コンテナー作成オプションを更新します。 user_name または user_id は、実際の Docker ホストの user_name または user_id に置き換えてください。 
  - ディレクトリまたはマウント ボリュームに対するアクセス許可を変更します。

## <a name="persist-your-data"></a> データを保持する

`docker stop` と `docker start` を使用してコンテナーを再起動しても、Azure SQL Edge の構成変更とデータベース ファイルはコンテナーに保持されています。 一方、`docker rm` を使用してコンテナーを削除すると、Azure SQL Edge とデータベースを含め、コンテナーの内容がすべて削除されます。 次のセクションでは、関連付けられているコンテナーが削除された場合でも、**データ ボリューム** を使用してデータベース ファイルを保持する方法について説明します。

> [!IMPORTANT]
> Azure SQL Edge の場合、Docker 内でのデータの保持について理解しておくことが重要です。 このセクションの説明に加えて、[Docker コンテナー内でデータを管理する方法](https://docs.docker.com/engine/tutorials/dockervolumes/)については、Docker のドキュメントをご覧ください。

### <a name="mount-a-host-directory-as-data-volume"></a>ホスト ディレクトリをデータ ボリュームとしてマウントする

1 つ目のオプションは、ホスト上のディレクトリをコンテナー内のデータ ボリュームとしてマウントすることです。 これを行うには、`docker run` コマンドを `-v <host directory>:/var/opt/mssql` フラグと共に使用します。 これにより、コンテナーの実行間でデータを復元できます。

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

この手法では、Docker の外部にあるホスト上のファイルを共有して表示することもできます。

> [!IMPORTANT]
> **Windows 上の Docker** のホスト ボリューム マッピングでは現在のところ、完全な `/var/opt/mssql` ディレクトリをマッピングできません。 ただし、`/var/opt/mssql/data` など、サブディレクトリをホスト コンピューターにマッピングできます。

> [!IMPORTANT]
> 現時点では、Azure SQL Edge イメージを使用した **Mac 上の Docker** のホスト ボリューム マッピングはサポートされていません。 代わりにデータ ボリューム コンテナーを使用してください。 この制限は、`/var/opt/mssql` ディレクトリに固有のものです。 マウントされたディレクトリからの読み取りは正常に機能します。 たとえば、Mac 上で -v を使用してホスト ディレクトリをマウントし、ホスト上に存在する .bak ファイルからバックアップを復元することができます。

### <a name="use-data-volume-containers"></a>データ ボリューム コンテナーを使用する

2 つ目のオプションは、データ ボリューム コンテナーを使用することです。 `-v` パラメーターを使用してホスト ディレクトリではなくボリューム名を指定して、データ ボリューム コンテナーを作成できます。 次の例では、**sqlvolume** という名前の共有データ ボリュームを作成します。

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> 実行コマンド内でデータ ボリュームを暗黙的に作成するためのこの手法は、以前のバージョンの Docker では動作しません。 その場合は、Docker のドキュメント、「[データ ボリューム コンテナーの作成とマウント](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container)」に概説されている明示的な手順を使用します。

このコンテナーを停止して削除しても、データ ボリュームは保持されます。 `docker volume ls` コマンドを使用して表示できます。

```bash
docker volume ls
```

その後、同じボリューム名を持つ別のコンテナーを作成すると、新しいコンテナーでは、ボリュームに含まれているのと同じ Azure SQL Edge データが使用されます。

データ ボリューム コンテナーを削除するには、`docker volume rm` コマンドを使用します。

> [!WARNING]
> データ ボリューム コンテナーを削除すると、コンテナー内のすべての Azure SQL Edge データが *完全に* 削除されます。


## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge に接続する](connect.md)
- [SQL Edge を使用してエンドツーエンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)