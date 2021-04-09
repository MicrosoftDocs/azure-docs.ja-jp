---
title: Azure Backup Server と DPM に関する FAQ
description: この記事では、Microsoft Azure Backup Server (MABS) と DPM (Data Protection Manager) に関してよく寄せられる質問への回答を示します。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 1663a842b7e00c611543451d4caef96b5b5a913f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97954993"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server と DPM - FAQ

## <a name="general-questions"></a>一般的な質問

この記事では、Azure Backup Server と DPM についてよく寄せられる質問に回答します。

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Azure Backup Server を使用して、物理サーバーのベア メタル回復 (BMR) バックアップを作成できますか。

はい。

### <a name="can-i-register-the-server-to-multiple-vaults"></a>サーバーを複数のコンテナーに登録することはできますか。

いいえ。 DPM サーバーまたは Azure Backup Server は 1 つのコンテナーにしか登録できません。

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>DPM を使用して Azure Stack でアプリをバックアップすることはできますか。

いいえ。 Azure Backup を使用して Azure Stack を保護することはできますが、Azure Backup では DPM を使用した Azure Stack でのアプリのバックアップをサポートしていません。

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>ファイルとフォルダーを保護するために Azure Backup エージェントをインストールしてある場合、System Center DPM をインストールしてオンプレミスのワークロードを Azure にバックアップすることはできますか。

はい。 ただし、最初に DPM をセットアップしてから、Azure Backup エージェントをインストールする必要があります。  この順序でコンポーネントをインストールすることで、Azure Backup エージェントが DPM と連携するようになります。 DPM をインストールする前にエージェントをインストールする方法は推奨されておらず、サポートもされていません。

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>UR7 と最新の Azure Backup エージェントをインストールしましたが、外部 DPM サーバーを追加できません。なぜですか。

ロールアップ 7 以前の更新プログラム ロールアップを利用して、データ ソースがクラウドに保護されている DPM サーバーの場合、UR7 と最新の Azure Backup エージェントをインストールした後、少なくとも 1 日待ってから **外部 DPM サーバーの追加** を開始する必要があります。 1 日の期間は、DPM 保護グループのメタデータを Azure にアップロードするために必要です。 保護グループ メタデータは、夜間ジョブを通じて、1 回目のアップロードが行われます。

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>ウイルス対策ソフトウェアの除外を構成するための推奨事項はありますか。

はい。ウイルス対策の除外を構成することをお勧めします。 DPM の除外については、「[DPM サーバーでのウイルス対策ソフトウェアの実行](/system-center/dpm/run-antivirus-server)」を参照してください。 MABS の除外については、「[MABS サーバー用ウイルス対策ソフトウェアの構成](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server)」を参照してください。

## <a name="vmware-and-hyper-v-backup"></a>VMware および Hyper-V のバックアップ

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>VMware vCenter サーバーを Azure にバックアップできますか。

はい。 Azure Backup Server を使用して、VMware vCenter Server および ESXi ホストを Azure にバックアップできます。

- サポートされるバージョンについて詳しくは、[こちら](backup-mabs-protection-matrix.md)をご覧ください。
- VMware サーバーをバックアップするには、[この手順](backup-azure-backup-server-vmware.md)に従ってください。

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>オンプレミス VMware/Hyper-V クラスター全体を復旧するには、個別のライセンスが必要ですか。

VMware/Hyper-V を保護するために個別のライセンスは必要ありません。

- System Center のお客様は、System Center Data Protection Manager (DPM) を使用して VMware VM を保護できます。
- System Center のお客様でない場合、VMware VM を保護するには、Azure Backup Server (従量課金制) を使用できます。

### <a name="can-i-restore-a-backup-of-a-hyper-v-or-vmware-vm-stored-in-azure-to-azure-as-an-azure-vm"></a>Azure に格納されている Hyper-V または VMware VM のバックアップを Azure VM として Azure に復元することはできますか?

いいえ、これは現時点ではできません。 オンプレミスのホストにのみ復元できます。

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>SharePoint が SQL AlwaysOn を使用して構成されている場合 (ディスクでの保護)、SharePoint アイテムを元の場所に回復できますか?

はい、元の SharePoint サイトにアイテムを回復できます。

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>SharePoint が SQL AlwaysOn を使用して構成されている場合、SharePoint データベースを元の場所に回復できますか?

SharePoint データベースは SQL AlwaysOn で構成されているので、可用性グループを削除しない限り、変更することはできません。 結果として、DPM は元の場所にデータベースを復元できません。 SQL Server データベースを別の SQL Server インスタンスに回復することはできます。

## <a name="next-steps"></a>次のステップ

その他のよく寄せられる質問をお読みください。

- Azure Backup Server と DPM のサポート マトリックスの[詳細](backup-support-matrix-mabs-dpm.md)。
- Azure Backup Server と DPM のトラブルシューティングのガイドラインの[詳細](backup-azure-mabs-troubleshoot.md)。
