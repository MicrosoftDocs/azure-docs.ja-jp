---
title: チュートリアル - CLI を使用した Azure 上の SAP HANA DB の復元
description: このチュートリアルでは、Azure CLI を使用して、Azure VM 上で実行されている SAP HANA データベースを Azure Backup Recovery Services コンテナーから復元する方法について説明します。
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334805"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure VM 内の SAP HANA データベースを復元する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このドキュメントでは、Azure CLI を使用して、バックアップされた SAP HANA データベースを Azure VM に復元する方法について詳しく説明します。 これらの手順は、[Azure portal](./sap-hana-db-restore.md) を使用して実行することもできます。

CLI コマンドを実行するには、[Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) を使用します。

このチュートリアルを終了すると、次のことができるようになります。

> [!div class="checklist"]
>
> * バックアップされたデータベースの復元ポイントを表示する
> * データベースを復元する

このチュートリアルでは、Azure Backup を使用してバックアップされている SAP HANA データベースが Azure VM 上で実行されていることを前提としています。 [CLI を使用して Azure の SAP HANA データベースをバックアップする方法](tutorial-sap-hana-backup-cli.md)を使用して SAP HANA データベースをバックアップした場合は、次のリソースを使用しています。

* *saphanaResourceGroup* という名前のリソース グループ
* *saphanaVault* という名前のコンテナー
* *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM* という名前の保護されたコンテナー
* *saphanadatabase;hxe;hxe* という名前のバックアップされたデータベースと項目
* *westus2* リージョン内のリソース

## <a name="view-restore-points-for-a-backed-up-database"></a>バックアップされたデータベースの復元ポイントを表示する

データベースのすべての復旧ポイントの一覧を表示するには、次のように [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) コマンドレットを使用します。

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

復旧ポイントの一覧は次のようになります。

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

ご覧のとおり、上記の一覧には、完全バックアップ、差分バックアップ、ログ バックアップという 3 つの復旧ポイントが含まれています。

>[!NOTE]
>[az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) コマンドレットを使用して、すべての連続したログ バックアップ チェーンの開始ポイントと終了ポイントを表示することもできます。

## <a name="prerequisites-to-restore-a-database"></a>データベースを復元するための前提条件

データベースを復元する前に、次の前提条件が満たされていることを確認してください。

* データベースの復元先にできるのは、同じリージョン内の SAP HANA インスタンスのみです。
* ターゲット インスタンスは、ソースと同じコンテナーに登録されている必要があります。
* Azure Backup は、同じ VM 上にある 2 つの異なる SAP HANA インスタンスを識別できません。 そのため、同じ VM 上の一方のインスタンスから他方のインスタンスにデータを復元することはできません。

## <a name="restore-a-database"></a>データベースを復元する

Azure Backup は、Azure VM 上で実行されている SAP HANA データベースを次のように復元できます。

* ログ バックアップを使用して、特定の日付または時刻 (秒) に復元する。 選択された時刻を基に、Azure Backup は完全、差分、または一連のログ バックアップから、必要になる適切なバックアップを自動的に判別します。
* 特定の完全バックアップまたは差分バックアップに復元することで、特定の復旧ポイントに復元する。

データベースを復元するには、[az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) コマンドレットを使用します。これには、入力の 1 つとして復旧構成オブジェクトが必要です。 このオブジェクトは、[az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) コマンドレットを使用して生成できます。 復旧構成オブジェクトには、復元を実行するためのすべての詳細が含まれています。 いずれかの復元モード (**OriginalWorkloadRestore** または **AlternateWorkloadRestore**)。

>[!NOTE]
> **OriginalWorkloadRestore** - 元のソースと同じ SAP HANA インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。 <br>
> **AlternateWorkloadRestore** - データベースを別の場所に復元し、元のソース データベースを保持します。

## <a name="restore-to-alternate-location"></a>別の場所に復元する

別の場所にデータベースを復元するには、復元モードとして **AlternateWorkloadRestore** を使用します。 次に、復元ポイントを選択する必要があります。以前の特定の時点または以前の復元ポイントのいずれかを選択できます。

このチュートリアルでは、以前の復元ポイントに復元します。 データベースの[復元ポイントの一覧を表示](#view-restore-points-for-a-backed-up-database)して、復元先のポイントを選択します。 このチュートリアルでは、*7660777527047692711* という名前の復元ポイントを使用します。

上記の復元ポイント名と復元モードを使用し、[az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) コマンドレットを使用して復旧構成オブジェクトを作成します。 このコマンドレットの残りの各パラメーターの意味を見てみましょう。

* **--target-item-name** これは、復元されたデータベースが使用する名前です。 この例では、*restored_database* という名前を使用しました。
* **--target-server-name** これは、Recovery Services コンテナーに正常に登録され、復元対象のデータベースと同じリージョンにある SAP HANA サーバーの名前です。 このチュートリアルでは、*hxehost* という名前の、保護したものと同じ SAP HANA サーバーにデータベースを復元します。
* **--target-server-type** SAP HANA データベースを復元するには、**HANAInstance** を使用する必要があります。

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

上記のクエリに対する応答は、次のような復旧構成オブジェクトになります。

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

ここで、データベースを復元するには、 [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) コマンドレットを実行します。 このコマンドを使用するには、*recoveryconfig.json* という名前のファイルに保存されている上記の json 出力を入力します。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

出力は次のようになります。

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

応答によってジョブ名がわかります。 このジョブ名を使用し、[az backup job show](/cli/azure/backup/job#az-backup-job-show) コマンドレットを使用してジョブの状態を追跡できます。

## <a name="restore-and-overwrite"></a>復元および上書き

元の場所に復元するには、復元モードとして **OrignialWorkloadRestore** を使用します。 次に、復元ポイントを選択する必要があります。以前の特定の時点または以前の復元ポイントのいずれかを選択できます。

このチュートリアルでは、復元先として以前の特定の時点の "28-11-2019-09:53:00" を選択します。 この復元ポイントは、dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss の形式で指定できます。 復元先として有効な特定の時点を選択するには、[az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) コマンドレットを使用します。これにより、連続したログ チェーン バックアップの間隔が一覧表示されます。

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

上記のクエリに対する応答は、次のような復旧構成オブジェクトになります。

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

ここで、データベースを復元するには、 [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) コマンドレットを実行します。 このコマンドを使用するには、*recoveryconfig.json* という名前のファイルに保存されている上記の json 出力を入力します。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

出力は次のようになります。

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

応答によってジョブ名がわかります。 このジョブ名を使用し、[az backup job show](/cli/azure/backup/job#az-backup-job-show) コマンドレットを使用してジョブの状態を追跡できます。

## <a name="restore-as-files"></a>ファイルとして復元

バックアップ データをデータベースとしてではなくファイルとして復元するために、ここでは復元モードとして **RestoreAsFiles** を使用します。 次に、復元ポイントを選択します。以前の特定の時点または以前の復元ポイントのいずれかを選択できます。 指定されたパスにファイルがダンプされると、それらのファイルをデータベースとして復元したい任意の SAP HANA マシンにそれらを移すことができます。 これらのファイルを任意のマシンに移動できるので、サブスクリプションやリージョンをまたいでデータを復元できるようになりました。

このチュートリアルでは、過去における特定の時点 (`28-11-2019-09:53:00`) を復元先として選択し、バックアップ ファイルのダンプ先に、同じ SAP HANA サーバー上の `/home/saphana/restoreasfiles` を選択します。 この復元ポイントは、**dd-mm-yyyy** または **dd-mm-yyyy-hh:mm:ss** の形式で指定できます。 復元先として有効な特定の時点を選択するには、[az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) コマンドレットを使用します。これにより、連続したログ チェーン バックアップの間隔が一覧表示されます。

上記の復元ポイント名と復元モードを使用し、[az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) コマンドレットを使用して復旧構成オブジェクトを作成します。 このコマンドレットの残りの各パラメーターの意味を見てみましょう。

* **--target-container-name** これは、Recovery Services コンテナーに正常に登録され、復元対象のデータベースと同じリージョンにある SAP HANA サーバーの名前です。 このチュートリアルでは、*hxehost* という名前の、保護したものと同じ SAP HANA サーバーにデータベースをファイルとして復元します。
* **--rp-name** ポイントインタイム リストアでは、復元ポイントの名前が **DefaultRangeRecoveryPoint** になります。

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

上記のクエリに対する応答は、次のような復旧構成オブジェクトになります。

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

ここで、データベースをファイルとして復元するには、[az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) コマンドレットを実行します。 このコマンドを使用するには、*recoveryconfig.json* という名前のファイルに保存されている上記の json 出力を入力します。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

出力は次のようになります。

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

応答によってジョブ名がわかります。 このジョブ名を使用し、[az backup job show](/cli/azure/backup/job#az-backup-job-show) コマンドレットを使用してジョブの状態を追跡できます。

ターゲット コンテナーにダンプされるファイルは次のとおりです。

* データベース バックアップ ファイル
* カタログ ファイル
* (関連するバックアップ ファイルごとの) JSON メタデータ ファイル

通常、ネットワーク共有パスや、宛先パスとして指定されているマウントされた Azure ファイル共有のパスを使うと、同じネットワーク内の他のマシンや、それらにマウントされている同じ Azure ファイル共有でこれらのファイルに簡単にアクセスできます。

>[!NOTE]
>ターゲットとなる登録済み VM にマウントされている Azure ファイル共有でデータベース バックアップ ファイルを復元するには、Azure ファイル共有に対する読み取り/書き込みアクセス許可がルート アカウントに含まれていることを確認します。

選択した復元ポイントの種類 (**ポイント イン タイム** または **完全および差分**) に基づいて、宛先パスに 1 つまたは複数のフォルダーが作成されるのを確認できます。 `Data_<date and time of restore>` という名前のフォルダーの 1 つには、完全および差分バックアップが含まれ、`Log` という名前の他のフォルダーにはログ バックアップが含まれます。

これらの復元されたファイルを、データベースとして復元する先の SAP HANA サーバーに移動します。 さらに、次の手順に従ってデータベースを復元します。

1. 次のコマンドを使用して、バックアップ ファイルが格納されているフォルダーまたはディレクトリに対してアクセス許可を設定します。

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. `<SID>adm` として、次のコマンド セットを実行します。

    ```bash
    su - <sid>adm
    ```

1. 復元用のカタログ ファイルを生成します。 完全バックアップの JSON メタデータ ファイルから **BackupId** を抽出します。これは後で行う復元操作で使用されます。 完全バックアップとログ バックアップがそれぞれ異なるフォルダーにあることを確認し、これらのフォルダー内のカタログ ファイルと JSON メタデータ ファイルを削除します。

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    上記のコマンドでは:

    * `<DataFileDir>` - 完全バックアップが格納されているフォルダー
    * `<LogFilesDir>` - ログ バックアップが格納されているフォルダー
    * `<PathToPlaceCatalogFile>` - 生成されたカタログ ファイルを配置する必要があるフォルダー

1. HANA Studio を通して新たに生成されたカタログ ファイルを使用して復元するか、この新しく生成されたカタログを使用して HDBSQL 復元クエリを実行します。 HDBSQL クエリを以下に示します。

    * 特定の時点まで復元するには:

        復元されたデータベースを新しく作成する場合は、HDBSQL コマンドを実行して新しいデータベース `<DatabaseName>` を作成してから、復元用のデータベースを停止します。 ただし、既存のデータベースのみを復元する場合は、HDBSQL コマンドを実行してそのデータベースを停止します。

        そして、次のコマンドを実行してデータベースを復元します。

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - 復元する新しいデータベースまたは既存のデータベースの名前
        * `<Timestamp>` - ポイントインタイム リストアの正確なタイムスタンプ
        * `<DatabaseName@HostName>` - バックアップが復元に使用されるデータベースの名前と、このデータベースが存在する **ホスト**/SAP HANA サーバーの名前。 `USING SOURCE <DatabaseName@HostName>` オプションでは、データ バックアップ (復元に使用) が、ターゲットの SAP HANA マシンとは異なる SID または名前を持つデータベースであることが指定されます。 そのため、バックアップが作成されたのと同じ HANA サーバーで復元を実行する場合には、これを指定する必要はありません。
        * `<PathToGeneratedCatalogInStep3>` - **手順 3.** で生成されたカタログ ファイルへのパス
        * `<DataFileDir>` - 完全バックアップが格納されているフォルダー
        * `<LogFilesDir>` - ログ バックアップが格納されているフォルダー
        * `<BackupIdFromJsonFile>` - **手順 3.** で抽出された **BackupId**

    * 特定の完全または差分バックアップに復元するには、次のようにします。

        復元されたデータベースを新しく作成する場合は、HDBSQL コマンドを実行して新しいデータベース `<DatabaseName>` を作成してから、復元用のデータベースを停止します。 ただし、既存のデータベースのみを復元する場合は、HDBSQL コマンドを実行してそのデータベースを停止します:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 復元する新しいデータベースまたは既存のデータベースの名前
        * `<Timestamp>` - ポイントインタイム リストアの正確なタイムスタンプ
        * `<DatabaseName@HostName>` - バックアップが復元に使用されるデータベースの名前と、このデータベースが存在する **ホスト** / SAP HANA サーバー名。 `USING SOURCE <DatabaseName@HostName>` オプションでは、データ バックアップ (復元に使用) が、ターゲットの SAP HANA マシンとは異なる SID または名前を持つデータベースであることが指定されます。 そのため、バックアップが作成されたのと同じ HANA サーバーで復元を実行する場合には、これを指定する必要はありません。
        * `<PathToGeneratedCatalogInStep3>` - **手順 3.** で生成されたカタログ ファイルへのパス
        * `<DataFileDir>` - 完全バックアップが格納されているフォルダー
        * `<LogFilesDir>` - ログ バックアップが格納されているフォルダー
        * `<BackupIdFromJsonFile>` - **手順 3.** で抽出された **BackupId**

## <a name="next-steps"></a>次のステップ

* Azure CLI を使用してバックアップされる SAP HANA データベースを管理する方法については、[CLI を使用して Azure VM で SAP HANA データベースを管理する方法](tutorial-sap-hana-backup-cli.md)のチュートリアルに進んでください。

* Azure portal を使用して Azure VM 上で実行されている SAP HANA データベースを復元する方法については、「[Azure VM 上の SAP HANA データベースの復元](./sap-hana-db-restore.md)」を参照してください。
