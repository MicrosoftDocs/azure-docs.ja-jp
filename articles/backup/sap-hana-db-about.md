---
title: Azure VM での SAP HANA データベースバックアップについて
description: この記事では、Azure 仮想マシン上で実行されている SAP HANA データベースをバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: efb9c3f786e429df404e261f053a9c9a9b032e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96296456"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Azure VM での SAP HANA データベースバックアップについて

SAP HANA データベースは、復旧ポイント目標 (RPO) と目標復旧時間を (RTO) の必要性が低い極めて重要なワークロードです。 [Azure Backup](./backup-overview.md) を使用して、[Azure VM 上で実行されている SAP HANA データベースをバックアップ](./tutorial-backup-sap-hana-db.md)できるようになりました。

Azure Backup は、SAP による [Backint 認定](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)がされています。これにより、SAP HANA のネイティブ API を活用してネイティブ バックアップ サポートを提供します。 Azure Backup からのこのオファリングは、Azure Backup の **ゼロインフラストラクチャ** バックアップの理念に沿っており、バックアップ インフラストラクチャのデプロイと管理を不要にします。 Azure VＭ 上で実行されている SAP HANA データベースをシームレスにバックアップおよび復元できるようになりました ([M シリーズの VM](../virtual-machines/m-series.md) もサポートされるようになりました！)。また、Azure Backup が提供するエンタープライズ管理機能を活用できます。

## <a name="added-value"></a>付加価値

Azure Backup を使用して SAP HANA データベースをバックアップおよび復元することには、次の利点があります：

* **15 分の復旧ポイント目標 (RPO)** :最大15分間までの重要なデータの回復が可能になりました。
* **ワンクリックで、特定の時点への復元をする**：運用データを、別の HANA サーバーに復元するのが簡単です。 バックアップとカタログのチェーン化による復元の実行は、すべて Azure によってバックグラウンドで管理されます。
* **長期保存**：厳格な準拠と監査のニーズに対応しています。 保有期間に基づいてバックアップを何年も保持し、復旧期間を超えると、組み込みのライフサイクル管理機能によって、自動的に削除されます。
* **Azure からのバックアップ管理サーバー**：Azure Backup の管理および監視機能を使用して、管理エクスペリエンスを向上させます。 Azure CLI もサポートされています。

現在サポートされているバックアップと復元のシナリオを確認するには、[SAP HANA シナリオのサポートマトリックス](./sap-hana-backup-support-matrix.md#scenario-support) を参照してください。

## <a name="backup-architecture"></a>Backup のアーキテクチャ

![Backup アーキテクチャの図](./media/sap-hana-db-about/backup-architecture.png)

* バックアップ プロセスは、Azure で [Recovery services コンテナー](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault)を作成することによって開始されます。 このコンテナーは、時間の経過と共に作成されたバックアップと復元ポイントを格納するために使用されます。
* SAP HANA サーバーを実行している Azure VM がコンテナーに登録されており、バックアップされるデータベースが [検出](./tutorial-backup-sap-hana-db.md#discover-the-databases) されます。 Azure Backup サービスでデータベースを検出できるようにするには、[事前登録スクリプト](https://aka.ms/scriptforpermsonhana) を HANA サーバーで、root ユーザーとして実行する必要があります。
* このスクリプトは **AZUREWLBACKUPHANAUSER** DB ユーザー　を作成し、**hdbuserstore** に同じ名前の対応するキーを作成します。 スクリプトで実行される処理の詳細の解釈については、「[事前登録スクリプトで実行される処理](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)」セクションをご参照ください。
* Azure Backup サービスは、登録されている SAP HANA サーバー上に、 **HANA 用の Azure Backup Plugin** をインストールするようになりました。
* 事前登録スクリプトによって作成された **AZUREWLBACKUPHANAUSER** DB ユーザーは、**HANA の Microsoft Azure Backup プラグイン** によって使用され、すべてのバックアップおよび復元操作を実行するために使用されます。 このスクリプトを実行せずに SAP HANA Db のバックアップを構成しようとすると、次のエラーが表示される場合があります：**UserErrorHanaScriptNotRun**.
* 検出されたデータベースで [バックアップを構成](./tutorial-backup-sap-hana-db.md#configure-backup) するには、必要なバックアップポリシーを選択し、バックアップを有効にする必要があります。

* バックアップが構成されると、Azure Backup サービスは、保護された SAP HANA サーバーの DATABASE レベルで、次の Backint パラメーターを設定します：
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>これらのパラメーターが、 HOST レベルに存在 *しない* ことを確認してください。 ホスト レベルのパラメーターによってこれらのパラメーターがオーバーライドされるため、予期しない動作が発生することがあります。
>

* **HANA 用の Azure Backup Plugin** は、すべてのバックアップスケジュールとポリシーの詳細を保持します。 それは、スケジュールされたバックアップをトリガーし、Backint API を介して **HANA Backup Engine** と通信します。
* **HANA Backup Engine** は、バックアップするデータを含む Backint ストリームを返します。
* 完全または差分のすべてのスケジュールされたバックアップとオンデマンドバックアップ (Microsoft Azure portal からトリガーされた) は、 **HANA 用の Azure Backup Plugin** によって開始されます。 ただし、ログバックアップは **HANA Backup Engine** によって管理およびトリガーされます。
* BackInt 認定ソリューションである SAP HANA 用 Azure Backup は、基になるディスクまたは VM の種類に依存しません。 このバックアップは、HANA で生成されたストリームによって実行されます。

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Azure VM バックアップを Azure SAP HANA バックアップと共に使用

データベース レベルのバックアップと回復を提供する Azure の SAP HANA バックアップを使用することに加え、Azure VM バックアップ ソリューションを使用して、OS と非データベース ディスクをバックアップすることができます。

[Backint 認定の Azure SAP HANA バックアップ ソリューション](#backup-architecture)は、データベースのバックアップと回復に使用できます。

[Azure VM バックアップ](backup-azure-vms-introduction.md)は、OS と他の非データベース ディスクのバックアップに使用できます。 VM のバックアップは毎日 1 回実行され、(**書き込みアクセラレータ (WA) OS ディスク** と **Ultra Disk** を除く) すべてのディスクがバックアップされます。 データベースは Azure SAP HANA バックアップ ソリューションを使用してバックアップされているため、[Azure 仮想マシンの選択的なディスク バックアップと復元](selective-disk-backup-restore.md)機能を使用して、OS と非データベース ディスクのみのファイル整合バックアップを作成できます。

SAP HANA を実行している VM を復元するには、次の手順に従います。

* 最新の復旧ポイントから、[新しい VM を Azure VM バックアップから復元](backup-azure-arm-restore-vms.md)します。 または、新しい空の VM を作成し、最新の復旧ポイントからディスクを接続します。
* WA ディスクが除外されている場合は、復元されません。 その場合は、空の WA ディスクとログ領域を作成します。
* 他のすべての構成 (IP、システム名など) が設定された後、VM は Azure Backup から DB データを受信するように設定されます。
* 次に、[Azure SAP HANA DB バックアップ](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point)から目的の時点までデータベースを VM に復元します。

## <a name="next-steps"></a>次のステップ

* [Azure VM で稼働している SAP HANA データベースを復元する](./sap-hana-db-restore.md)方法について学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](./sap-hana-db-manage.md)方法を学習する
