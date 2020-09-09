---
title: Azure Backup とは
description: Azure Backup サービスの概要を紹介し、このサービスがお客様の事業継続とディザスター リカバリー (BCDR) 戦略にどのように寄与するかについて説明します。
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 9954c8fa6affca7d2c2b73d7176280587d749476
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017891"
---
# <a name="what-is-the-azure-backup-service"></a>Azure Backup サービスとは

Azure Backup サービスは、データをバックアップし、それを Microsoft Azure クラウドから回復するための、シンプルで安全かつコスト効率の高いソリューションを提供します。

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>バックアップできるデータについて

- **オンプレミス** - [Microsoft Azure Recovery Services (MARS) エージェント](backup-support-matrix-mars-agent.md)を使用して、ファイル、フォルダー、システム状態をバックアップします。 または、DPM または Azure Backup Server (MABS) エージェントを使用して、オンプレミスの VM ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) と [VMware](backup-azure-backup-server-vmware.md)) のほか、[オンプレミスのワークロード](backup-mabs-protection-matrix.md)を保護します。
- **Azure VM** - [Windows VM または Linux VM](backup-azure-vms-introduction.md) 全体を (バックアップ拡張機能を使用して) バックアップするか、[MARS エージェント](backup-azure-manage-mars.md)を使用してファイル、フォルダー、システム状態をバックアップします。
- **Azure Files 共有** - [Azure File 共有はストレージ アカウントにバックアップします](backup-afs.md)。
- **Azure VM 内の SQL Server** -  [Azure VM 上で動作する SQL Server データベースをバックアップします](backup-azure-sql-database.md)。
- **Azure VM 内の SAP HANA データベース** - [Azure VM 上で動作する SAP HANA データベースをバックアップします](backup-azure-sap-hana-database.md)。

![Azure Backup の概要](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Azure Backup を使用する理由

Azure Backup には、以下のような主な利点があります。

- **オンプレミス バックアップのオフロード**: Azure Backup では、お客様のオンプレミス リソースをクラウドにバックアップするためのシンプルなソリューションが提供されます。 複雑なオンプレミス バックアップ ソリューションをデプロイせずに、短期および長期のバックアップを行えます。
- **Azure IaaS VM のバックアップ**: Azure Backup では、元のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 バックアップは、復旧ポイントの管理機能をビルトインで備えた Recovery Services コンテナーに格納されます。 構成とスケーラビリティは単純で、バックアップは最適化され、必要に応じて簡単に復元することができます。
- **容易なスケーリング** - Azure Backup では、Azure クラウドの基盤となる機能と無制限のスケールを使用して、高可用性を実現します。その際、保守と監視のオーバーヘッドは発生しません。
- **無制限のデータ転送が可能**: Azure Backup では、転送するインバウンド データまたはアウトバウンド データの量に制限がなく、転送されるデータに料金はかかりません。
  - 送信データとは、復元操作中に Recovery Services コンテナーから転送されるデータを指します。
  - Azure Import/Export サービスを使用してオフライン初期バックアップを実行し、大量のデータをインポートした場合は、受信データに対してコストがかかります。  [詳細については、こちらを参照してください](backup-azure-backup-import-export.md)。
- **データの安全性の確保**: Azure Backup には、[転送中](backup-azure-security-feature.md)のデータと[保存](backup-azure-security-feature-cloud.md)データを保護するためのソリューションがあります。
- **一元化された監視と管理**: Azure Backup では、Recovery Services コンテナーに[組み込みの監視とアラートの機能](backup-azure-monitoring-built-in-monitor.md)が提供されています。 これらの機能は、管理インフラストラクチャを追加しなくても使用できます。 [Azure Monitor を使用](backup-azure-monitoring-use-azuremonitor.md)して監視とレポートの規模を拡大することもできます。
- **アプリ整合性のあるバックアップの取得**: アプリケーション整合性バックアップは、バックアップ コピーを復元するために必要なすべてのデータが復旧ポイントにあることを意味します。 Azure Backup は、アプリケーション整合性バックアップを提供することで、追加の修正なしでデータを復元できるようにします。 アプリケーション整合性データの復元により復元時間が短縮され、迅速に実行状態に戻ることができます。
- **短期および長期のデータの保持**:短期および長期のデータ保持のために、[Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)を使用することができます。
- **ストレージ管理の自動化** - ハイブリッド環境では、多くの場合、異種混在のストレージが必要です。つまり、ストレージの一部はオンプレミスに、一部はクラウドに存在していなければなりません。 Azure Backup では、オンプレミスのストレージ デバイスを使用するためのコストはありません。 Azure Backup は、従量制課金モデルを使用して、バックアップ ストレージを自動的に割り当てて管理します。 そのため支払いは、使用した分のストレージについてのみ発生します。 価格に関して詳しくは、[こちら](https://azure.microsoft.com/pricing/details/backup)をご覧ください。
- **複数のストレージ オプション** - Azure Backup では、ストレージ/データの高可用性を維持するため、2 種類のレプリケーションが提供されています。
  - [ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy.md) では、データセンターのストレージ スケール ユニットにデータが 3 回レプリケートされます (データのコピーが 3 つ作成されます)。 データのすべてのコピーは、同じリージョン内に存在します。 LRS は、ローカル ハードウェアの障害からデータを保護するための低コストのオプションです。
  - [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy.md) は、既定の推奨レプリケーション オプションです。 GRS では、セカンダリ リージョン (ソース データのプライマリの場所から数百マイル離れた場所) にデータがレプリケートされます。 GRS は LRS よりもコストがかかりますが、地域的な障害が発生しても、より高いレベルのデータ持続性が確保されます。

## <a name="next-steps"></a>次のステップ

- さまざまなバックアップ シナリオのアーキテクチャとコンポーネントを[確認](backup-architecture.md)します。
- バックアップおよび [Azure VM バックアップ](backup-support-matrix-iaas.md)に関するサポート要件と制限を[確認](backup-support-matrix.md)します。
