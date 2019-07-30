---
title: Azure Backup による Azure VM での SQL Server データベースのバックアップについてよく寄せられる質問
description: Azure Backup を使用した Azure VM 上での SQL Server データベースのバックアップに関する一般的な質問への回答を見つけてください。
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vijayts
ms.openlocfilehash: 082817899adc6ab9b4d57f7e0d4bc4e7c2f0a2ab
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464821"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Azure VM バックアップ上で実行されている SQL Server データベースに関する FAQ

この記事では、Azure 仮想マシン (VM) 上で実行され、[Azure Backup](backup-overview.md) サービスを使用する SQL Server データベースのバックアップに関する一般的な質問にお答えします。

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>IaaS VM の Azure バックアップと SQL Server を同じマシン上で使用できますか？
はい、同じ VM上で VM のバックアップと SQL のバックアップを共存させることができます。 この場合はログを切り詰めないようにするため、VM 上でコピーのみの完全バックアップを内部でトリガーします。


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>このソリューションではバックアップは再試行または自動回復されますか?

状況によっては、Azure Backup サービスが修復バックアップをトリガーします。 自動回復は、次に示す 6 つの条件のいずれかに対して実行されます。

  - ログまたは差分バックアップが LSN 検証エラーのために失敗した場合は、代わりに次のログまたは差分バックアップが完全バックアップに変換されます。
  - ログまたは差分バックアップの前に完全バックアップが実行されていない場合は、代わりにそのログまたは差分バックアップが完全バックアップに変換されます。
  - 最新の完全バックアップの特定の時点が 15 日より前の場合は、代わりに次のログまたは差分バックアップが完全バックアップに変換されます。
  - 拡張機能のアップグレードのために取り消されたバックアップ ジョブはすべて、そのアップグレードが完了し、拡張機能が起動された後に再トリガーされます。
  - 復元中にデータベースを上書きすることを選択した場合は、次のログ/差分バックアップが失敗し、代わりに完全バックアップがトリガーされます。
  - データベース復旧モデルの変更のために完全バックアップでログ チェーンをリセットする必要がある場合は、次のスケジュールで完全バックアップが自動的にトリガーされます。

機能としての自動回復は、すべてのユーザーに対して既定で有効になります。ただし、これを無効にすることを選択した場合は、次のことを実行してください。

  * SQL Server インスタンスで、*C:\Program Files\Azure Workload Backup\bin* フォルダーで **ExtensionSettingsOverrides.json** ファイルを作成または編集します。
  *  **ExtensionSettingsOverrides.json** で、 *{"EnableAutoHealer":false}* を設定します。
  * 変更を保存し、ファイルを閉じます。
  * SQL Server インスタンスで、**タスク マネージャー**を開き、**AzureWLBackupCoordinatorSvc** サービスを再起動します。  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>SQL サーバー上で実行される同時実行バックアップの数を制御できますか?

はい。 バックアップ ポリシーが実行される速度を調整して、SQL Server インスタンスへの影響を最小限に抑えることができます。 設定を変更するには:
1. SQL Server インスタンスで、*C:\Program Files\Azure Workload Backup\bin* フォルダーに *ExtensionSettingsOverrides.json* ファイルを作成します。
2. *ExtensionSettingsOverrides.json* ファイルで、**DefaultBackupTasksThreshold** 設定を小さな値 (たとえば、5) に変更します。 <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. 変更を保存し、ファイルを閉じます。
4. SQL Server インスタンスで、**タスク マネージャー**を開きます。 **AzureWLBackupCoordinatorSvc** サービスを再起動します。<br/> <br/>
 このメソッドにより、バックアップ アプリケーションで多くのリソースを消費している場合、SQL Server の[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017)は、受信するアプリケーション要求で使用できる CPU、物理 IO、およびメモリの量をより汎用的なやり方で制限できます。

> [!NOTE]
> UX では、引き続き先に進みいつでも、いくつでもバックアップをスケジュールできますが、それらは上の例に従った値 (たとえば、5) のスライディング ウィンドウで処理されます。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>セカンダリ レプリカから完全バックアップを実行できますか?
SQL の制限に従って、セカンダリ レプリカでコピーのみの完全バックアップを実行できます。ただし、完全バックアップは許可されていません。

## <a name="can-i-protect-availability-groups-on-premises"></a>オンプレミスの可用性グループを保護できますか?
いいえ。 Azure Backup は、Azure で実行されている SQL Server データベースを保護します。 可用性グループ (AG) が Azure とオンプレミスのコンピューターに分散しているとき、AG は、プライマリ レプリカが Azure で実行されている場合にのみ保護できます。 また、Azure Backup は、Recovery Services コンテナーと同じ Azure リージョンで実行されているノードしか保護しません。

## <a name="can-i-protect-availability-groups-across-regions"></a>リージョンにまたがる可用性グループを保護できますか?
Azure Backup Recovery Services コンテナーは、そのコンテナーと同じリージョンにあるすべてのノードを検出して保護できます。 SQL Server Always On 可用性グループが複数の Azure リージョンにまたがっている場合は、プライマリ ノードが存在するリージョンからのバックアップを設定します。 Azure Backup は、バックアップ設定に従って、可用性グループ内のすべてのデータベースを検出して保護できます。 バックアップ設定が満たされていない場合は、バックアップが失敗し、エラー通知が表示されます。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功したバックアップ ジョブでアラートが作成されますか?
いいえ。 成功したバックアップ ジョブではアラートは生成されません。 アラートは、失敗したバックアップ ジョブに対してのみ送信されます。 ポータル アラートの詳細な動作は[ここ](backup-azure-monitoring-built-in-monitor.md)に記載されています。 ただし、成功したジョブに対してもアラートを生成することに関心がある場合は、[Azure Monitor を使用した監視](backup-azure-monitoring-use-azuremonitor.md)を使用できます。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>スケジュールされたバックアップ ジョブを [バックアップ ジョブ] メニューで確認できますか?
**[バックアップ ジョブ]** メニューには、アドホック バックアップ ジョブしか表示されません。 スケジュールされたジョブの場合は、[Azure Monitor を使用した監視](backup-azure-monitoring-use-azuremonitor.md)を使用してください。

## <a name="are-future-databases-automatically-added-for-backup"></a>今後作成されるデータベースはバックアップに自動的に追加されますか?
はい。この機能は、 [自動保護](backup-sql-server-database-azure-vms.md#enable-auto-protection)で実現できます。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>自動保護されたインスタンスからデータベースを削除した場合、バックアップはどうなりますか?
自動保護されたインスタンスからデータベースが削除された場合でも、データベース バックアップは引き続き試行されます。 これは、削除されたデータベースが **[バックアップ項目]** に異常として表示され始め、引き続き保護されていることを示しています。

このデータベースの保護を停止するための正しい方法は、このデータベースに対して**データを削除**した  **[バックアップの停止]** を実行することです。  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>自動保護されたデータベースのバックアップ操作の停止を実行した場合、その動作はどうなりますか?
**データを保持したバックアップの停止**を実行した場合、将来のバックアップは実行されず、既存の復旧ポイントはそのまま残ります。 そのデータベースは引き続き保護されていると見なされ、 **[バックアップ項目]** に表示されます。

**データを削除したバックアップの停止**を実行した場合、将来のバックアップは実行されず、既存の復旧ポイントも削除されます。 そのデータベースは保護されていないと見なされ、[バックアップの構成] 内のインスタンスに表示されます。 ただし、手動で選択したり、自動保護したりできる他の保護されていないデータベースとは異なり、このデータベースは灰色表示され、選択できません。 このデータベースを再保護するための唯一の方法は、そのインスタンスに対する自動保護を無効にすることです。 これでこのデータベースを選択できるようになり、それに対する保護を構成するか、またはそのインスタンスに対する自動保護を再び有効にします。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>データベースが保護された後にその名前を変更した場合、その動作はどうなりますか?
名前が変更されたデータベースは、新しいデータベースとして処理されます。 このため、サービスはこの状況を、データベースが見つからず、バックアップが失敗したかのように処理します。

名前が変更されているデータベースを選択し、それに対する保護を構成できます。 そのインスタンスに対して自動保護が有効になっている場合は、名前が変更されたデータベースが自動的に検出されて保護されます。

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>自動保護されたインスタンスの追加されたデータベースが表示されないのはなぜですか?
[自動保護されたインスタンスに追加した](backup-sql-server-database-azure-vms.md#enable-auto-protection)データベースは、保護された項目にすぐには表示されない可能性があります。 これは、検出が通常は 8 時間ごとに実行されるためです。 ただし、次の図に示すように、 **[DB の再検出]** を選択することによって検出を手動で実行した場合は、新しいデータベースを直ちに検出して保護できます。

  ![新しく追加されたデータベースを手動で検出する](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>次の手順

Azure VM 上で実行されている[SQL Server データベースをバックアップする](backup-azure-sql-database.md)方法を学習します。
