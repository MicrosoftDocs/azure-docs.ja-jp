---
title: Azure 仮想マシン復元プロセスについて
description: Azure Backup サービスで Azure Virtual Machines を復元する方法を説明します
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 62d1ff7973693f29c77c77fe2ad4fbbb598a5fa4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738414"
---
# <a name="about-azure-vm-restore"></a>Azure VM の復元について

この記事では、[Azure Backup サービス](./backup-overview.md)によって Azure 仮想マシン (VM) がどのように復元されるかについて説明します。 さまざまな復元オプションがあります。 ここでは、それらによってサポートされるさまざまなシナリオについて説明します。

## <a name="concepts"></a>概念

- **復旧ポイント** (**復元ポイント** とも呼ばれます):復旧ポイントは、バックアップされている元のデータのコピーです。

- **階層 (スナップショットとコンテナー)** :Azure VM のバックアップは、2 つのフェーズで行われます。

  - フェーズ 1 では、取得されたスナップショットがディスクと共に格納されます。 これは **スナップショット階層** と呼ばれます。 スナップショット階層の復元は (コンテナーからの復元よりも) 高速です。これは、復元をトリガーする前に、スナップショットがコンテナーにコピーされるまで待つ時間がないためです。 そのため、スナップショット階層からの復元は、[インスタント リストア](./backup-instant-restore-capability.md)とも呼ばれます。
  - フェーズ 2 では、スナップショットが転送され、Azure Backup サービスによって管理されるコンテナーに格納されます。 これは **コンテナー階層** と呼ばれます。

- **元の場所への復旧 (OLR)** :復元ポイントから、バックアップが作成されたソース Azure VM に対して実行される復旧。復旧ポイントに格納されている状態によって、それが置き換えられます。 これにより、ソース VM の OS ディスクとデータ ディスクが置き換えられます。

- **別の場所への復旧 (ALR)** :復旧ポイントからバックアップが作成された元のサーバー以外のサーバーに対して実行される復旧。

- **項目レベルの復元 (ILR):** 復旧ポイントからの VM 内の個々のファイルまたはフォルダーの復元

- **可用性 (レプリケーションの種類)** :Azure Backup では、ストレージ/データの高可用性を維持するため、2 種類のレプリケーションが提供されます。
  - [ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) では、データセンターのストレージ スケール ユニットにデータが 3 回レプリケートされます (データのコピーが 3 つ作成されます)。 データのすべてのコピーは、同じリージョン内に存在します。 LRS は、ローカル ハードウェアの障害からデータを保護するための低コストのオプションです。
  - [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) は、既定の推奨レプリケーション オプションです。 GRS では、セカンダリ リージョン (ソース データのプライマリの場所から数百マイル離れた場所) にデータがレプリケートされます。 GRS は LRS よりもコストがかかりますが、地域的な障害が発生しても、より高いレベルのデータ持続性が確保されます。
  - [ゾーン冗長ストレージ (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) は、[可用性ゾーン](../availability-zones/az-overview.md#availability-zones)内のデータをレプリケートし、同じリージョン内でデータ所在地と回復性を保証します。 ZRS にダウンタイムはありません。 そのため、[データ所在地](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)を必要とし、なおかつダウンタイムが許されない重要なワークロードは、ZRS にバックアップすることができます。

- **リージョンをまたがる復元 (CRR)** :[復元オプション](./backup-azure-arm-restore-vms.md#restore-options)の 1 つであるリージョンをまたがる復元 (CRR) では、[Azure のペア リージョン](../best-practices-availability-paired-regions.md#what-are-paired-regions)であるセカンダリ リージョンで Azure VM を復元できます。セカンダリ リージョンではいつでも (一部停止中でも、完全停止中でも、あるいは自分で選択したその他のタイミングで) データを復元できます。 

## <a name="restore-scenarios"></a>復元シナリオ

![復元シナリオ ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **シナリオ**                                                 | **何を実行するか**                                             | **いつ使用するか**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [復元して新しい仮想マシンを作成する](./backup-azure-arm-restore-vms.md) | VM 全体を OLR (ソース VM がまだ存在する場合) または ALR に復元します | <li> ソース VM が失われるか、破損した場合、VM 全体を復元できます  <li> VM のコピーを作成できます  <li> 監査またはコンプライアンスの復元ドリルを実行できます  <li> このオプションは、Marketplace イメージから作成された Azure VM では機能しません (つまり、ライセンスの有効期限が切れたために使用できない場合)。 |
| [VM のディスクを復元する](./backup-azure-arm-restore-vms.md#restore-disks) | VM に接続されているディスクを復元します                             |  すべてのディスク:このオプションでは、テンプレートが作成され、ディスクが復元されます。 このテンプレートは、要件に合わせて特別な構成 (可用性セットなど) で編集でき、さらにテンプレートを使用し、ディスクを復元して VM を再作成できます。 |
| [VM 内の特定のファイルを復元する](./backup-azure-restore-files-from-vm.md) | 復元ポイントを選択し、ファイルを参照して選択し、バックアップされた VM と同じ (または互換性のある) OS に復元します。 |  復元する特定のファイルがわかっている場合は、VM 全体を復元するのではなく、このオプションを使用します。 |
| [暗号化された VM を復元する](./backup-azure-vms-encryption.md) | ポータルからディスクを復元し、次に PowerShell を使用して VM を作成します | <li> [Azure Active Directory を使用した暗号化された VM](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [Azure AD を使用しない暗号化された VM](../virtual-machines/windows/disk-encryption-windows.md) <li> ["*Azure AD 使用なし*" に移行される "*Azure AD を使用した*" 暗号化された VM](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [リージョンをまたがる復元](./backup-azure-arm-restore-vms.md#cross-region-restore) | 新しい VM を作成するか、セカンダリ リージョン (Azure のペアになっているリージョン) にディスクを復元します | <li> **完全な停止**:リージョンをまたがる復元機能では、セカンダリ リージョンでデータの復旧を待つ時間がありません。 Azure で停止が宣言される前でも、セカンダリ リージョンで復元を開始できます。 <li> **部分的な停止**:ダウンタイムは、Azure Backup によって、バックアップしたデータが格納されている特定のストレージ クラスターで、または Azure Backup と、バックアップしたデータに関連付けられているストレージ クラスターを接続しているネットワークでも、発生する可能性があります。 リージョンをまたがる復元では、セカンダリ リージョンでバックアップされたデータのレプリカを使用して、セカンダリ リージョンで復元を実行できます。 <li> **停止なし**:セカンダリ リージョンのデータを使用して、ビジネス継続性とディザスター リカバリー (BCDR) ドリルを監査またはコンプライアンス目的で実施することができます。 これにより、プライマリ リージョンでビジネス継続性とディザスター リカバリー ドリルのための完全な停止や部分的な停止が発生していない場合でも、セカンダリ リージョンで、バックアップされたデータの復元を実行できます。  |

## <a name="next-steps"></a>次のステップ

- [VM の復元に関してよく寄せられる質問](/azure/backup/backup-azure-vm-backup-faq#restore)
- [サポートされる復元方法](./backup-support-matrix-iaas.md#supported-restore-methods)
- [復元の問題のトラブルシューティング](./backup-azure-vms-troubleshoot.md#restore)
