---
title: Azure Backup Server と DPM に関する FAQ
description: 一般的な質問への回答:Azure Backup Server と DPM。
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 54727daa158172ae44379b847c70602ca998c65d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466410"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server と DPM - FAQ
この記事では、Azure Backup Server と DPM についてよく寄せられる質問に回答します。

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Azure Backup Server を使用して、物理サーバーのベア メタル回復 (BMR) バックアップを作成できますか。 <br/>
はい。

### <a name="can-i-register-the-server-to-multiple-vaults"></a>サーバーを複数のコンテナーに登録することはできますか。
いいえ。 DPM サーバーまたは Azure Backup Server は 1 つのコンテナーにしか登録できません。


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>DPM を使用して Azure Stack でアプリをバックアップすることはできますか。
いいえ。 Azure Backup を使用して Azure Stack を保護することはできますが、Azure Backup では DPM を使用した Azure Stack でのアプリのバックアップをサポートしていません。

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>ファイルとフォルダーを保護するために Azure Backup エージェントをインストールしてある場合、System Center DPM をインストールしてオンプレミスのワークロードを Azure にバックアップすることはできますか。
はい。 ただし、最初に DPM をセットアップしてから、Azure Backup エージェントをインストールする必要があります。  この順序でコンポーネントをインストールすることで、Azure Backup エージェントが DPM と連携するようになります。 DPM をインストールする前にエージェントをインストールする方法は推奨されておらず、サポートもされていません。

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>UR7 と最新の Azure Backup エージェントをインストールしましたが、外部 DPM サーバーを追加できません。なぜですか。
ロールアップ 7 以前の更新プログラム ロールアップを利用して、データ ソースがクラウドに保護されている DPM サーバーの場合、UR7 と最新の Azure Backup エージェントをインストールした後、少なくとも 1 日待ってから**外部 DPM サーバーの追加**を開始する必要があります。 1 日の期間は、DPM 保護グループのメタデータを Azure にアップロードするために必要です。 保護グループ メタデータは、夜間ジョブを通じて、1 回目のアップロードが行われます。

## <a name="vmware-and-hyper-v-backup"></a>VMware および Hyper-V のバックアップ

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>VMware vCenter サーバーを Azure にバックアップできますか。
はい。 Azure Backup Server を使用して、VMware vCenter Server および ESXi ホストを Azure にバックアップできます。

- サポートされるバージョンについて詳しくは、[こちら](backup-mabs-protection-matrix.md)をご覧ください。
- VMware サーバーをバックアップするには、[この手順](backup-azure-backup-server-vmware.md)に従ってください。

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>オンプレミス VMware/Hyper-V クラスター全体を復旧するには、個別のライセンスが必要ですか。
VMware/Hyper-V を保護するために個別のライセンスは必要ありません。

- System Center のお客様は、System Center Data Protection Manager (DPM) を使用して VMware VM を保護できます。
- System Center のお客様でない場合、VMware VM を保護するには、Azure Backup Server (従量課金制) を使用できます。


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>SharePoint が SQL AlwaysOn を使用して構成されている場合 (ディスクでの保護)、SharePoint アイテムを元の場所に回復できますか?
はい、元の SharePoint サイトにアイテムを回復できます。

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>SharePoint が SQL AlwaysOn を使用して構成されている場合、SharePoint データベースを元の場所に回復できますか?
SharePoint データベースは SQL AlwaysOn で構成されているので、可用性グループを削除しない限り、変更することはできません。 結果として、DPM は元の場所にデータベースを復元できません。 SQL Server データベースを別の SQL Server インスタンスに回復することはできます。

## <a name="next-steps"></a>次の手順

その他のよく寄せられる質問をお読みください。

- Azure Backup Server と DPM のサポート マトリックスの[詳細](backup-support-matrix-mabs-dpm.md)。
- Azure Backup Server と DPM のトラブルシューティングのガイドラインの[詳細](backup-azure-mabs-troubleshoot.md)。
