---
title: バックアップ センターを使用してアクションを実行する
description: この記事では、バックアップ センターを使用してアクションを実行する方法について説明します
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506451"
---
# <a name="perform-actions-using-backup-center"></a>バックアップ センターを使用してアクションを実行する

バックアップ センターを使用すると、個々のコンテナーに移動することなく、中央のインターフェイスからバックアップ関連の主要なアクションを実行できます。 バックアップ センターから実行できるいくつかのアクションを次に示します。

* データソースのバックアップを構成する
* バックアップ インスタンスを復元する
* 新しい資格情報コンテナーの作成
* 新しいバックアップ ポリシーを作成する
* バックアップ インスタンスのオンデマンド バックアップをトリガーする
* バックアップ インスタンスのバックアップを停止する

## <a name="supported-scenarios"></a>サポートされるシナリオ

* バックアップ センターでは現在、Azure VM のバックアップ、Azure VM 内の SQL のバックアップ、Azure VM 内の SAP HANA のバックアップ、Azure Files のバックアップ、Azure Database for PostgreSQL サーバーのバックアップがサポートされています。
* サポートされるシナリオとサポートされないシナリオの詳細な一覧については、[サポート マトリックス](backup-center-support-matrix.md)を参照してください。

## <a name="configure-backup"></a>バックアップの構成

Azure AM、Azure VM 内の SQL、Azure VM 内の SAP HANA、または Azure Files をバックアップする場合は、Recovery Services コンテナーを使用する必要があります。 Azure Database for PostgreSQL サーバーをバックアップする場合は、バックアップ コンテナーを使用する必要があります。 

バックアップするデータソースの種類に応じて、以下で説明されている適切な手順に従ってください。

### <a name="configure-backup-to-a-recovery-services-vault"></a>Recovery Services コンテナーへのバックアップを構成する

1. バックアップ センターに移動し、 **[概要]** タブの上部にある **[+ バックアップ]** を選択します。

    ![バックアップ センターの概要](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. バックアップするデータソースの種類を選択します。

    ![VM バックアップを構成するためのデータソースを選択する](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Recovery Services コンテナーを選択し、 **[続行]** を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じバックアップの構成エクスペリエンスに移動します。 Recovery Services コンテナーで Azure 仮想マシンのバックアップを構成する方法の詳細については、[ここ](tutorial-backup-vm-at-scale.md)を参照してください。

### <a name="configure-backup-to-a-backup-vault"></a>バックアップ コンテナーへのバックアップを構成する

1. バックアップ センターに移動し、 **[概要]** タブの上部にある **[+ バックアップ]** を選択します。
2. バックアップするデータソースの種類 (この場合は Azure Database for PostgreSQL サーバー) を選択します。

    ![Azure Database for PostgreSQL サーバーのバックアップを構成するためのデータソースを選択する](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. **[続行]** を選択します。 これにより、バックアップ コンテナーから到達できるものと同じバックアップの構成エクスペリエンスに移動します。 バックアップ コンテナーで Azure Database for PostgreSQL サーバーのバックアップを構成する方法の詳細については、[ここ](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)を参照してください。

## <a name="restore-a-backup-instance"></a>バックアップ インスタンスを復元する

復元するデータソースの種類に応じて、以下で説明されている適切な手順に従ってください。

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Recovery Services コンテナーから復元する場合

1. バックアップ センターに移動し、 **[概要]** タブの上部にある **[復元]** を選択します。

    ![VM を復元するためのバックアップ センターの概要](./media/backup-center-actions/backup-center-overview-restore.png)

2. 復元するデータソースの種類を選択します。

    ![VM の復元のためのデータソースを選択する](./media/backup-center-actions/restore-select-datasource-vm.png)

3. バックアップ インスタンスを選択し、 **[続行]** を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じ設定の復元エクスペリエンスに移動します。 Recovery Services コンテナーで Azure 仮想マシンを復元する方法の詳細については、[ここ](backup-azure-arm-restore-vms.md#before-you-start)を参照してください。

### <a name="if-youre-restoring-from-a-backup-vault"></a>バックアップ コンテナーから復元する場合

1. バックアップ センターに移動し、 **[概要]** タブの上部にある **[復元]** を選択します。
2. 復元するデータソースの種類 (この場合は Azure Database for PostgreSQL サーバー) を選択します。

    ![Azure Database for PostgreSQL サーバーの復元のためのデータソースを選択する](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. バックアップ インスタンスを選択し、 **[続行]** を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じ設定の復元エクスペリエンスに移動します。 バックアップ コンテナーで Azure Database for PostgreSQL サーバーを復元する方法の詳細については、[ここ](backup-azure-database-postgresql.md#restore)を参照してください。

## <a name="create-a-new-vault"></a>新しい資格情報コンテナーの作成

バックアップ センターに移動し、 **[概要]** タブの上部にある **[+ コンテナー]** を選択することによって新しいコンテナーを作成できます。

![[コンテナーの作成]](./media/backup-center-actions/backup-center-create-vault.png)

* [Recovery Services コンテナーの作成の詳細について学習する](backup-create-rs-vault.md)
* [バックアップ コンテナーの作成の詳細について学習する](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>新しいバックアップ ポリシーを作成する

バックアップするデータソースの種類に応じて、以下で説明されている適切な手順に従ってください。

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Recovery Services コンテナーにバックアップする場合

1. バックアップ センターに移動し、 **[概要]** タブの上部にある **[+ ポリシー]** を選択します。

    ![バックアップ ポリシーのためのバックアップ センターの概要](./media/backup-center-actions/backup-center-overview-policy.png)

2. バックアップするデータソースの種類を選択します。

    ![VM バックアップのポリシーのためのデータソースを選択する](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Recovery Services コンテナーを選択し、 **[続行]** を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じポリシーの作成エクスペリエンスに移動します。 Recovery Services コンテナーで Azure 仮想マシンの新しいバックアップ ポリシーを作成する方法の詳細については、[ここ](backup-azure-arm-vms-prepare.md#create-a-custom-policy)を参照してください。

### <a name="if-youre-backing-up-to-a-backup-vault"></a>バックアップ コンテナーにバックアップする場合

1. バックアップ センターに移動し、 **[概要]** タブの上部にある **[+ ポリシー]** を選択します。
2. バックアップするデータソースの種類 (この場合は Azure Database for PostgreSQL サーバー) を選択します。

    ![Azure Database for PostgreSQL サーバーのバックアップのポリシーのためのデータソースを選択する](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. **[続行]** を選択します。 これにより、バックアップ コンテナーから到達できるものと同じポリシーの作成エクスペリエンスに移動します。 バックアップ コンテナーで新しいバックアップ ポリシーを作成する方法の詳細については、[ここ](backup-azure-database-postgresql.md#create-backup-policy)を参照してください。

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>バックアップ インスタンスのオンデマンド バックアップを実行する

バックアップ センターを使用すると、バックアップ資産全体にわたってバックアップ インスタンスを検索したり、オンデマンドでバックアップ操作を実行したりできます。

オンデマンド バックアップをトリガーするには、バックアップ センターに移動し、 **[バックアップ インスタンス]** メニュー項目を選択します。 これを選択すると、アクセスできるすべてのバックアップ インスタンスの詳細を表示できます。 バックアップするバックアップ インスタンスを検索できます。 グリッド内の項目を右クリックすると、使用可能なアクションの一覧が表示されます。 オンデマンド バックアップを実行するには、 **[今すぐバックアップ]** オプションを選択します。

![オンデマンド バックアップ](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Azure Virtual Machines のオンデマンド バックアップの実行の詳細について学習します](backup-azure-manage-vms.md#run-an-on-demand-backup)。

[Azure Database for PostgreSQL サーバーのオンデマンド バックアップの実行の詳細について学習します](backup-azure-database-postgresql.md#on-demand-backup)。

## <a name="stop-backup-for-a-backup-instance"></a>バックアップ インスタンスのバックアップを停止する

バックアップの対象である基になるリソースが既に存在しなくなった場合など、バックアップ インスタンスのバックアップを停止することが必要になるシナリオが存在します。

オンデマンド バックアップをトリガーするには、バックアップ センターに移動し、 **[バックアップ インスタンス]** メニュー項目を選択します。 これを選択すると、アクセスできるすべてのバックアップ インスタンスの詳細を表示できます。 バックアップするバックアップ インスタンスを検索できます。 グリッド内の項目を右クリックすると、使用可能なアクションの一覧が表示されます。 バックアップ インスタンスのバックアップを停止するには、 **[バックアップの停止]** オプションを選択します。

![保護の停止](./media/backup-center-actions/backup-center-stop-protection.png)

[Azure Virtual Machines のバックアップの停止の詳細について学習する](backup-azure-manage-vms.md#stop-protecting-a-vm)。

[Azure Database for PostgreSQL サーバーのバックアップの停止の詳細について学習する](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>次のステップ

* [バックアップを監視および操作する](backup-center-monitor-operate.md)
* [バックアップ資産を管理する](backup-center-govern-environment.md)
* [バックアップに関する分析情報を取得する](backup-center-obtain-insights.md)
