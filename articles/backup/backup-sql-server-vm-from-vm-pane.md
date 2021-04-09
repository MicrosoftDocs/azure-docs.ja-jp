---
title: VM ウィンドウから SQL Server VM をバックアップする
description: この記事では、VM ウィンドウから Azure 仮想マシン上の SQL Server データベースをバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891659"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>VM ウィンドウから SQL Server をバックアップする

この記事では、[Azure Backup](backup-overview.md) サービスを使用して Azure VM で実行されている SQL Server をバックアップする方法について説明します。 SQL Server VM は、次の 2 つの方法でバックアップできます。

- 1 つの SQL Server Azure VM:この記事の手順では、VM ビューから直接 SQL Server をバックアップする方法について説明します。
- 複数の SQL Server Azure VM:Recovery Services コンテナーを設定して、複数の VM のバックアップを構成することができます。 そのシナリオについては、[こちらの記事](backup-sql-server-database-azure-vms.md)の説明に従ってください。

## <a name="before-you-start"></a>開始する前に

1. [サポート マトリックス](sql-support-matrix.md)を使用して環境を確認する。
2. SQL Server VM の Azure Backup の[概要](backup-azure-sql-database.md)を確認する。
3. VM が[ネットワーク接続](backup-sql-server-database-azure-vms.md#establish-network-connectivity)を備えていることを確認する。

## <a name="configure-backup-on-the-sql-server"></a>SQL Server でバックアップを構成する

VM の **[バックアップ]** ウィンドウから SQL Server VM のバックアップを有効にすることができます。 この方法では、次の 2 つのことが行われます。

- SQL VM を Azure Backup サービスに登録して、アクセスできるようにします。
- VM 内で実行されているすべての SQL Server インスタンスを自動保護します。 つまり、バックアップ ポリシーは、既存のすべてのデータベースと、今後これらのインスタンスに追加されるデータベースに適用されます。

1. ページの上部にあるバナーを選択して、SQL Server のバックアップ ビューを開きます。

    ![SQL Server のバックアップ ビュー](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >バナーが表示されない場合、 このバナーは、Azure Marketplace イメージを使用して作成された SQL Server VM に対してのみ表示されます。 また、Azure VM Backup で保護されている VM に対しても表示されます。 その他のイメージの場合は、[こちら](backup-sql-server-database-azure-vms.md)で説明されているように、バックアップを構成できます。

2. Recovery Services コンテナー名を入力します。 コンテナーは、すべてのバックアップを格納および管理するための論理エンティティです。 新しいコンテナーを作成する場合:

    - これは、保護している SQL Server VM と同じサブスクリプションとリージョンに作成されます。
    - これは、すべてのバックアップの geo 冗長ストレージ (GRS) 設定を使用して作成されます。 冗長性の種類を変更する場合は、VM を保護する前に行う必要があります。 詳細については、 [こちらの記事](backup-create-rs-vault.md#set-storage-redundancy)を参照してください。

3. **バックアップ ポリシー** を選択します。 既定のポリシー、またはコンテナーで作成したその他の既存のポリシーから選択できます。 新しいポリシーを作成する場合、ステップ バイ ステップ ガイドについては[こちらの記事](backup-sql-server-database-azure-vms.md#create-a-backup-policy)を参照してください。

    ![バックアップ ポリシーの選択](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. **[バックアップの有効化]** を選択します。 この操作は、完了するまで数分かかる場合があります。

    ![[バックアップの有効化] の選択](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. 操作が完了すると、バナーに **コンテナー名** が表示されます。

    ![バナーにコンテナー名が表示される](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. 登録されているすべての VM とその保護の状態を確認できるコンテナー ビューに移動するには、バナーを選択します。

    ![登録されている VM が表示されているコンテナー ビュー](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. マーケットプレース以外のイメージの場合、登録は成功する可能性がありますが、**バックアップの構成** は、SQL Server 上で Azure Backup 拡張機能にアクセス許可が与えられるまでトリガーされない可能性があります。 このような場合、 **[バックアップの準備]** 列には **[準備ができていません]** と表示されます。 バックアップの構成がトリガーされるようにするためには、マーケットプレイス以外のイメージに[適切なアクセス許可](backup-azure-sql-database.md#set-vm-permissions)を手動で割り当てる必要があります。

    ![[バックアップの準備] が [準備ができていません] になっている](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. バックアップされた SQL Server VM で行う必要がある他の操作または監視については、対応する Recovery Services コンテナーを参照してください。 このコンテナーにバックアップされているすべてのデータベースを表示し、オンデマンドのバックアップや復元などの操作をトリガーするには、 **[バックアップ項目]** にアクセスします。 同様に、保護の構成、バックアップ、復元などの操作に対応するジョブを [監視](manage-monitor-sql-database-backup.md)するには、 **[バックアップ ジョブ]** にアクセスします。

    ![[バックアップ項目] でバックアップされたデータベースを表示する](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>このバックアップは、保護された VM に後で追加された新しい SQL Server インスタンスでは自動的に構成されません。 新しく追加されたインスタンスでバックアップを構成するには、VM が登録されているコンテナーにアクセスして、[こちら](backup-sql-server-database-azure-vms.md)に記載されている手順に従います。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。

- [バックアップした SQL Server データベースの復元](restore-sql-database-azure-vm.md)
- [バックアップした SQL Server データベースの管理](manage-monitor-sql-database-backup.md)
