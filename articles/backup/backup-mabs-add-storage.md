---
title: Azure Backup Server で Modern Backup Storage を使用する
description: Azure Backup Server の新機能について説明します。 この記事では、Backup Server インストールをアップグレードする方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 1a265579593c8eb39f0df4037f8ec39c788ddbce
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465121"
---
# <a name="add-storage-to-azure-backup-server"></a>Azure Backup Server へのストレージの追加

Azure Backup Server V2 以降では、ストレージを 50% 削減でき、バックアップ速度が 3 倍に高速化され、より効率的なストレージを利用できる、Modern Backup Storage をサポートしています。 また、ワークロード対応型ストレージも提供されます。

> [!NOTE]
> Modern Backup Storage を使用するには、Backup Server V2 または V3 を Windows Server 2016 上で実行するか、または V3 を Windows Server 2019 上で実行する必要があります。
> Backup Server V2 を以前のバージョンの Windows Server で実行した場合は、Azure Backup Server は Modern Backup Storage を利用できません。 代わりに、Backup Server V1 の場合と同様に、ワークロードを保護します。 詳細については、Backup Server のバージョンの[保護マトリックス](backup-mabs-protection-matrix.md)をご覧ください。

## <a name="volumes-in-backup-server"></a>Backup Server のボリューム

Backup Server V2 以降ではストレージ ボリュームを使用できます。 ボリュームを追加すると、Backup Server はボリュームを Resilient File System (ReFS) にフォーマットします。これは Modern Backup Storage で必要とされるフォーマットです。 ボリュームを追加し、必要に応じて後で展開する場合、以下のワークフローを使用することをお勧めします。

1.  VM 上に Backup Server を設定します。
2.  記憶域プール内の仮想ディスクにボリュームを作成します。
    1.  記憶域プールにディスクを追加し、単純なレイアウトの仮想ディスクを作成します。
    2.  任意のディスクを追加し、仮想ディスクを拡張します。
    3.  仮想ディスクにボリュームを作成します。
3.  Backup Server にボリュームを追加します。
4.  ワークロード対応型ストレージを構成します。

## <a name="create-a-volume-for-modern-backup-storage"></a>Modern Backup Storage のボリュームの作成

ボリュームを含む Backup Server V2 以降をディスク ストレージとして使用すると、ストレージの管理を維持できます。 ボリュームは単一のディスクにすることができます。 ただし、将来ストレージを拡張する場合は、ストレージ スペースを使用して作成したディスクからボリュームを作成してください。 これは、バックアップ ストレージのボリュームを展開する場合に役立ちます。 このセクションでは、このセットアップを使用してボリュームを作成するためのベスト プラクティスを提供します。

1. サーバー マネージャーで、 **[ファイル サービスおよびストレージ サービス]**  >  **[ボリューム]**  >  **[記憶域プール]** の順に選択します。 **[物理ディスク]** から、 **[記憶域プールの新規作成]** を選択します。

    ![新しい記憶域プールの作成](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. **[タスク]** ドロップダウン ボックスで、 **[仮想ディスクの新規作成]** を選択します。

    ![仮想ディスクの追加](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. 記憶域プールを選択し、 **[物理ディスクの追加]** を選択します。

    ![物理ディスクの追加](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. 物理ディスクを選択し、 **[仮想ディスクの拡張]** を選択します。

    ![仮想ディスクの拡張](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. 仮想ディスクを選択し、 **[ボリュームの新規作成]** を選択します。

    ![新しいボリュームの作成](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. **[サーバーとディスクの選択]** ダイアログで、サーバーと新しいディスクを選択します。 次に、 **[次へ]** を選択します。

    ![サーバーとディスクの選択](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Backup Server ディスク ストレージへのボリュームの追加

Backup Server にボリュームを追加するには、 **[管理]** ウィンドウで、ストレージを再スキャンして **[追加]** を選択します。 Backup Server Storage に追加できるすべてのボリュームの一覧が表示されます。 選択したボリュームの一覧に使用可能なボリュームを追加したら、管理しやすいように、それらのボリュームにフレンドリ名をつけることができます。 これらのボリュームを ReFS にフォーマットして Backup Server が Modern Backup Storage の利点を活用できるようにするには、 **[OK]** を選択します。

![使用可能なボリュームの追加](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>ワークロード対応型ストレージの設定

ワークロード対応型ストレージでは、特定の種類のワークロードを優先的に保存するボリュームを選択できます。 たとえば、1 秒あたりの入出力操作 (IOPS) が多い高コストのボリュームには、大量バックアップが頻繁に必要なワークロードのみを保存するように設定できます。 例として、トランザクション ログを含む SQL Server が挙げられます。 VM のようなバックアップ頻度の低いその他のワークロードは、低コストのボリュームにバックアップできます。

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

PowerShell コマンドレットの Update-DPMDiskStorage を使用して、ワークロード対応型ストレージを設定できます。これにより、Azure Backup Server 上の記憶域プールのボリュームのプロパティが更新されます。 

構文:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
次のスクリーン ショットは、PowerShell ウィンドウ内の Update-DPMDiskStorage コマンドレットを示しています。

![PowerShell ウィンドウ内の Update-DPMDiskStorage コマンド](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

PowerShell を使用して行った変更は Backup Server 管理者コンソールに反映されます。

![管理者コンソール内のディスクとボリューム](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>従来の記憶域の Modern Backup Storage への移行
Backup Server V2 にアップグレードまたはこれをインストールし、オペレーティング システムを Windows Server 2016 にアップグレードしたら、 Modern Backup Storage を使用するように保護グループを更新します。 既定では、保護グループは変更されません。 初期のセットアップのとおりに機能します。

Modern Backup Storage を使用するように保護グループを更新することはオプションです。 保護グループを更新するには、データ保持オプションを使用して、すべてのデータ ソースの保護を停止します。 次に、新しい保護グループにデータ ソースを追加します。

1. 管理者コンソールで、 **[Protection]** (保護) 機能を選択します。 **[保護グループ メンバー]** 一覧で、メンバーを右クリックし、 **[Stop protection of member]** (メンバーの保護を停止する) を選択します。

   ![メンバーの保護の停止](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. **[グループから削除]** ダイアログ ボックスで、記憶域プールの使用済みディスク領域と利用可能な空き領域を確認します。 既定では、ディスク上の復旧ポイントをそのままにし、関連付けられている保有ポリシーごとの有効期限が切れるようにします。 Click **OK**.

   空き記憶域プールに使用済みディスク領域をすぐに返す場合は、 **[ディスク上のレプリカを削除]** チェック ボックスを選択し、そのメンバーに関連付けられているバックアップ データ (および復旧ポイント) を削除します。

   ![[グループ] ダイアログ ボックスから削除](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Modern Backup Storage を使用する保護グループを作成します。 保護されていないデータ ソースが含めます。

## <a name="add-disks-to-increase-legacy-storage"></a>ディスクを追加して従来の記憶域を増やす

Backup Server で従来の記憶域を使用する場合、ディスクを追加して従来の記憶域を増やす必要があります。

ディスク ストレージを追加するには、次の手順を実行します。

1. 管理者コンソールで、 **[Management]** (管理) >  **[Disk Storage]** (ディスク ストレージ) >  **[Add]** (追加) の順に選択します。

    ![[Add Disk Storage]\(ディスク ストレージの追加) ダイアログ](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. **[Add Disk Storage]** (ディスク ストレージの追加) ダイアログで **[Add disks]** (ディスクの追加) を選択します。

5. 使用可能なディスクの一覧で、追加するディスクを選択し、 **[追加]** を選択し、 **[OK]** を選択します。

## <a name="next-steps"></a>次の手順
Backup Server をインストールしたら、サーバーを準備する方法、またはワークロードの保護を開始する方法について見ていきましょう。

- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
