---
title: "Azure Backup Server v2 で Modern Backup Storage を使用する |Microsoft ドキュメント"
description: "Azure Backup Server v2 の新機能について説明します。 この記事では、Backup Server インストールをアップグレードする方法について説明します。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Azure Backup Server v2 へのストレージの追加

Azure Backup Server v2 には、System Center 2016 Data Protection Manager Modern Backup Storage がついてきます。 Modern Backup Storage を使用すると、ストレージを 50% 削減でき、バックアップ速度が 3 倍に高速化され、より効率的なストレージを利用できます。 また、ワークロード対応型ストレージも提供されます。 

> [!NOTE]
> Modern Backup Storage を使用するには、Backup Server v2 を Windows Server 2016 で稼働させる必要があります。 Backup Server v2 を以前のバージョンの Windows Server で稼働させると、Azure Backup Server は Modern Backup Storage を利用できません。 代わりに、Backup Server v1 の場合と同様に、ワークロードを保護します。 詳細については、Backup Server のバージョンの[保護マトリックス](backup-mabs-protection-matrix.md)をご覧ください。

## <a name="volumes-in-backup-server-v2"></a>Backup Server v2 のボリューム

Backup Server v2 ではストレージ ボリュームを使用できます。 ボリュームを追加すると、Backup Server はボリュームを Resilient File System (ReFS) にフォーマットします。これは Modern Backup Storage で必要とされるフォーマットです。 ボリュームを追加し、必要に応じて後で展開する場合、以下のワークフローを使用することをお勧めします。

1.  VM に Backup Server v2 を設定します。
2.  記憶域プール内の仮想ディスクにボリュームを作成します。
    1.  記憶域プールにディスクを追加し、単純なレイアウトの仮想ディスクを作成します。
    2.  任意のディスクを追加し、仮想ディスクを拡張します。
    3.  仮想ディスクにボリュームを作成します。
3.  Backup Server にボリュームを追加します。
4.  ワークロード対応型ストレージを構成します。

## <a name="create-a-volume-for-modern-backup-storage"></a>Modern Backup Storage のボリュームの作成

ボリュームを含む Backup Server v2 をディスク ストレージとして使用すると、ストレージの管理を維持できます。 ボリュームは単一のディスクにすることができます。 ただし、将来ストレージを拡張する場合は、ストレージ スペースを使用して作成したディスクからボリュームを作成してください。 これは、バックアップ ストレージのボリュームを展開する場合に役立ちます。 このセクションでは、このセットアップを使用してボリュームを作成するためのベスト プラクティスを提供します。

1. サーバー マネージャーで、**[ファイル サービスおよびストレージ サービス]** > **[ボリューム]** > **[記憶域プール]** の順に選択します。 **[物理ディスク]** から、**[記憶域プールの新規作成]** を選択します。 

    ![新しい記憶域プールの作成](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. **[タスク]** ドロップダウン ボックスで、**[仮想ディスクの新規作成]** を選択します。

    ![仮想ディスクの追加](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. 記憶域プールを選択し、**[物理ディスクの追加]** を選択します。

    ![物理ディスクの追加](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. 物理ディスクを選択し、**[仮想ディスクの拡張]** を選択します。

    ![仮想ディスクの拡張](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. 仮想ディスクを選択し、**[ボリュームの新規作成]** を選択します。

    ![新しいボリュームの作成](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. **[サーバーとディスクの選択]** ダイアログで、サーバーと新しいディスクを選択します。 次に、**[次へ]** を選択します。

    ![サーバーとディスクの選択](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Backup Server ディスク ストレージへのボリュームの追加

Backup Server にボリュームを追加するには、**[管理]** ウィンドウで、ストレージを再スキャンして **[追加]** を選択します。 Backup Server Storage に追加できるすべてのボリュームの一覧が表示されます。 選択したボリュームの一覧に使用可能なボリュームを追加したら、管理しやすいように、それらのボリュームにフレンドリ名をつけることができます。 これらのボリュームを ReFS にフォーマットして Backup Server が Modern Backup Storage の利点を活用できるようにするには、**[OK]** を選択します。

![使用可能なボリュームの追加](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>ワークロード対応型ストレージの設定

ワークロード対応型ストレージでは、特定の種類のワークロードを優先的に保存するボリュームを選択できます。 たとえば、1 秒あたりの入出力操作 (IOPS) が多い高コストのボリュームには、大量バックアップが頻繁に必要なワークロードのみを保存するように設定できます。 例として、トランザクション ログを含む SQL Server が挙げられます。 VM のようなバックアップ頻度の低いその他のワークロードは、低コストのボリュームにバックアップできます。

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

PowerShell コマンドレットの Update-DPMDiskStorage を使用して、ワークロード対応型ストレージを設定できます。これにより、Data Protection Manager サーバー上の記憶域プールのボリュームのプロパティが更新されます。

構文:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
次のスクリーン ショットは、PowerShell ウィンドウ内の Update-DPMDiskStorage コマンドレットを示しています。

![PowerShell ウィンドウ内の Update-DPMDiskStorage コマンド](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

PowerShell を使用して行った変更は Backup Server 管理者コンソールに反映されます。

![管理者コンソール内のディスクとボリューム](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>次のステップ
Backup Server をインストールしたら、サーバーを準備する方法、またはワークロードの保護を開始する方法について見ていきましょう。

- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)

