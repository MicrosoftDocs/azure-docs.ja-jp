---
title: Recovery Services コンテナーの概要
description: Recovery Services コンテナーの概要と Azure Backup コンテナーとの比較。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: raynew
ms.openlocfilehash: 924b36701ecf21f6bd84938aeefbf25e47fcbaa7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878663"
---
# <a name="recovery-services-vaults-overview"></a>Recovery Services コンテナーの概要

この記事では、Recovery Services コンテナーの機能について説明します。 Recovery Services コンテナーは、データを格納する Azure のストレージ エンティティです。 データは通常、データのコピーであるか、仮想マシン (VM)、ワークロード、サーバー、ワークステーションのいずれかの構成情報です。 Recovery Services コンテナーを使用すると、IaaS VM (Linux または Windows) や Azure SQL Database などのさまざまな Azure サービスのバックアップ データを保持できます。 Recovery Services コンテナーは、System Center DPM、Windows Server、Azure Backup Server などをサポートします。 Recovery Services コンテナーでは、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。

1 つの Azure サブスクリプション内に、リージョンあたり最大 500 個の Recovery Services コンテナーを作成できます。

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Recovery Services コンテナーと Backup コンテナーの比較

Backup コンテナーをまだ使用している場合、Recovery Services コンテナーに自動アップグレードされます。 2017 年 11 月までに、すべての Backup コンテナーは Recovery Services コンテナーにアップグレードされます。

Recovery Services コンテナーが Azure の Azure Resource Manager モデルに基づいているのに対し、バックアップ コンテナーは Azure Service Manager モデルに基づいています。 Backup コンテナーを Recovery Services コンテナーにアップグレードする場合、バックアップ データはアップグレード プロセスの実行中でも実行後でもそのまま残ります。 Recovery Services コンテナーには、Backup コンテナーにはない次のような機能があります。

- **強化されたバックアップ データの保護機能**:Recovery Services コンテナーの場合、Azure Backup によってクラウド バックアップを保護するセキュリティ機能が提供されます。 このセキュリティ機能により、バックアップをセキュリティで保護することができ、運用サーバーとバックアップ サーバーが侵害された場合でもデータを安全に回復できます。 [詳細情報](backup-azure-security-feature.md)

- **ハイブリッド IT 環境の一元監視**:Recovery Services コンテナーの場合、[Azure IaaS VM](backup-azure-manage-vms.md) だけでなく、[オンプレミス資産](backup-azure-manage-windows-server.md#manage-backup-items)も中央ポータルで監視することができます。 [詳細情報](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **ロールベースのアクセス制御 (RBAC)**:RBAC を使用して、Azure のアクセス権を詳細に管理できます。 [Azure にはさまざまな組み込みのロールがあります](../role-based-access-control/built-in-roles.md)。また、Azure Backup には、[復旧ポイントを管理するための 3 つの組み込みのロールがあります](backup-rbac-rs-vault.md)。 Recovery Services コンテナーは、定義されたユーザー ロールのセットに対するバックアップと復元アクセスを制限する RBAC と互換性があります。 [詳細情報](backup-rbac-rs-vault.md)

- **Azure Virtual Machines のあらゆる構成の保護**:Recovery Services コンテナーは、Resource Manager ベースの VM (Premium ディスク、マネージド ディスク、暗号化された VM など) を保護します。 Backup コンテナーを Recovery Services コンテナーにアップグレードすると、Service Manager ベースの VM を Resource Manager ベースの VM にアップグレードできます。 コンテナーをアップグレードするときに、Service Manager ベースの VM の復旧ポイントを維持し、アップグレードされた (Resource Manager が有効な) VM の保護を構成できます。 [詳細情報](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **IaaS VM のインスタント リストア**:Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できるため、復元時間を短縮できます。 IaaS VM のインスタント リストアは、Windows VM と Linux VM の両方で利用できます。 [詳細情報](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>ポータルでの Recovery Services コンテナーの管理
Backup サービスは他の Azure に統合されるため、Recovery Services コンテナーは Azure portal で簡単に作成および管理できます。 この統合は、"*ターゲット サービスのコンテキストで*" Recovery Services コンテナーを作成または管理できることを意味します。 たとえば、VM の復旧ポイントを表示するには、VM を選択し、[操作] メニューで **[バックアップ]** をクリックします。

![Recovery Services コンテナーの VM の詳細](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

VM にバックアップが構成されていない場合、バックアップを構成するよう求められます。 バックアップが構成されている場合は、復元ポイントの一覧など、VM のバックアップに関する情報が表示されます。  

![Recovery Services コンテナーの VM の詳細](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

前の例では、**ContosoVM** が仮想マシンの名前です。 **ContosoVM-demovault** は、Recovery Services コンテナーの名前です。 復旧ポイントを格納する Recovery Services コンテナーの名前を覚えておく必要はありません。この情報には仮想マシンからアクセスできます。  

1 つの Recovery Services コンテナーが複数のサーバーを保護する場合、Recovery Services コンテナーを確認するのがより合理的です。 サブスクリプション内のすべての Recovery Services コンテナーを検索し、一覧から選択できます。

次のセクションに、アクティビティの種類ごとに Recovery Services コンテナーを使用する方法が説明された記事へのリンクを示します。

> [!NOTE]
> Recovery Services コンテナーを削除した場合、24 時間以内に同じ名前の Recovery Services コンテナーを作成することはできません。 別のリソース名を使用するか、別のリソース グループを選択するか、24 時間後にもう一度再試行してください。

### <a name="back-up-data"></a>データのバックアップ
- [Azure VM のバックアップ](backup-azure-vms-first-look-arm.md)
- [Windows Server または Windows ワークステーションのバックアップ](backup-try-azure-backup-in-10-mins.md)
- [Azure への DPM ワークロードのバックアップ](backup-azure-dpm-introduction.md)
- [Azure Backup Server を使用してワークロードをバックアップするための準備](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>復旧ポイントの管理
- [Azure VM バックアップの管理](backup-azure-manage-vms.md)
- [ファイルとフォルダーの管理](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>コンテナーからのデータの復元
- [Azure VM からの個々のファイルの回復](backup-azure-restore-files-from-vm.md)
- [Azure VM の復元](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>コンテナーのセキュリティ保護
- [Recovery Services コンテナー内のクラウド バックアップ データのセキュリティ保護](backup-azure-security-feature.md)



## <a name="next-steps"></a>次の手順
次の記事を参照してください。</br>
[IaaS VM のバックアップ](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server のバックアップ](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server のバックアップ](backup-configure-vault.md)
