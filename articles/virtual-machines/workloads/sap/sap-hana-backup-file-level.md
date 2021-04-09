---
title: ファイル レベルの SAP HANA Azure バックアップ | Microsoft Docs
description: Azure 仮想マシン上の SAP HANA をバックアップする方法は主に 2 つあります。この記事では、ファイル レベルの SAP HANA Azure バックアップについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0ff226a156721382a289af0f2a8a0f898a57bab9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667977"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>ファイル レベルの SAP HANA Azure バックアップ

## <a name="introduction"></a>はじめに

この記事は、「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](./sap-hana-backup-guide.md)」の関連記事です。そのバックアップ ガイドの記事では、Azure Backup サービスとストレージ スナップショットの概要と作業の開始方法を示し、詳しく説明しています。 

Azure の別の VM タイプでは、異なる数の VHD の接続が許可されます。 正確な詳細については、「[Azure の Linux 仮想マシンのサイズ](../../sizes.md)」を参照してください。 このドキュメントで参照されているテストのために、64 個の接続されたデータ ディスクが許可される GS5 Azure VM を使用しました。 大規模な SAP HANA システムでは、データ ファイルとログ ファイルに多数のディスクが既に使用されていることがあります。場合によっては、ディスク IO スループットを最適化するためにソフトウェア ストライピングが併用されています。 Azure VM での SAP HANA デプロイ向けに推奨されるディスク構成の詳細については、記事「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」を参照してください。 推奨事項には、ローカルのバックアップのディスク領域の推奨事項も含まれています。

ファイル レベルでバックアップ/復元を管理する標準的な方法としては、SAP HANA Studio または SAP HANA SQL ステートメントを使用したファイルベースのバックアップがあります。 詳細については、記事「[SAP HANA SQL とシステム ビューのリファレンス](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html)」を参照してください。

![この図は、SAP HANA Studio のバックアップ メニュー項目のダイアログを示しています](media/sap-hana-backup-file-level/backup-menue-dialog.png)

この図は、SAP HANA Studio のバックアップ メニュー項目のダイアログを示しています。 種類 &quot;ファイル&quot; を選択した場合、SAP HANA のバックアップ ファイルの書き込み先となるファイル システムのパスを指定する必要があります。 これは、復元を実行するときも同様です。

この選択は簡単で単純なように思えますが、いくつかの考慮事項が存在します。 Azure VM に接続できるデータ ディスクの数には上限があります。 データベースのサイズとディスク スループットの要件しだいでは、VM のファイル システム上に SAP HANA バックアップ ファイルを格納する容量がない可能性があります。場合によっては、複数のデータ ディスクへのソフトウェア ストライピングが必要です。 これらのバックアップ ファイルを移動したり、テラバイト単位のデータを処理する際にファイル サイズ制限とパフォーマンスを管理したりするための各種の方法について、この記事で後ほど説明します。

合計容量に関して自由度の高い別のオプションとしては、Azure Blob Storage があります。 1 つの BLOB の容量は 1 TB に制限されているものの、1 つの BLOB コンテナーの合計容量は現在 500 TB です。 さらに Azure Blob Storage では、コスト面で優れている、いわゆる &quot;クール&quot; BLOB ストレージを選択して使用できます。 クール BLOB ストレージの詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)」を参照してください。

安全性を追求する場合は、geo レプリケーションされたストレージ アカウントを使用して SAP HANA バックアップを格納します。 ストレージの冗長性とストレージのレプリケーションの詳細については、「[Azure Storage の冗長性](../../../storage/common/storage-redundancy.md)」を参照してください。

SAP HANA バックアップの専用 VHD を、geo レプリケーションされた専用バックアップ ストレージ アカウントに格納することができます。 そのほか、geo レプリケーションされたストレージ アカウントまたは別のリージョンにあるストレージ アカウントに、SAP HANA バックアップが保持された VHD をコピーできます。

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer ユーティリティの詳細

ディレクトリとファイルを Azure ストレージに格納するには、CLI または PowerShell を使用できます。また、いずれかの [Azure SDK](https://azure.microsoft.com/downloads/) を使用してツールを作成できます。 Azure Storage にデータをコピーするための、すぐに使用できるユーティリティ AzCopy もあります。 ([AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../../../storage/common/storage-use-azcopy-v10.md)方法に関するページを参照してください)。

そのため、SAP HANA バックアップ ファイルのコピーには blobxfer を使用しました。 blobxfer は、多くのお客様が運用環境で使用しているオープン ソース ツールであり、[GitHub](https://github.com/Azure/blobxfer) で入手できます。 このツールでは、Azure Blob Storage と Azure ファイル共有のどちらにもデータを直接コピーできます。 さらに、md5 ハッシュや、複数のファイルが含まれたディレクトリをコピーする際の自動並列処理など、さまざまな便利な機能があります。

## <a name="sap-hana-backup-performance"></a>SAP HANA バックアップのパフォーマンス
この章では、パフォーマンスに関する考慮事項について説明します。 Azure Storage のスループット向上を目指した継続的な開発が行われているため、達成された数値は最新の状態を表していない可能性があります。 そのため、お使いの構成と Azure リージョンに対して個々のテストを実行する必要があります。

![このスクリーンショットは、SAP HANA Studio の SAP HANA バックアップ コンソールの一部です](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

このスクリーンショットは、SAP HANA Studio の SAP HANA バックアップ コンソールを示しています。 1 台のディスク上で XFS ファイル システムを使用している HANA VM があり、それに接続された単一の Azure Standard HDD ストレージ ディスクで 230 GB のバックアップを実行したところ、約 42 分かかりました。

![このスクリーンショットは、SAP HANA テスト VM の YaST の一部です](media/sap-hana-backup-file-level/one-backup-disk.png)

このスクリーンショットは、SAP HANA テスト VM の YaST の一部です。 SAP HANA バックアップ用に 1 TB のディスクが 1 つ使用されていることがわかります。 230 GB のバックアップに約 42 分かかりました。 さらに、200 GB のディスクを 5 つ接続してソフトウェア RAID md0 を作成し、これらの 5 つの Azure データ ディスクに対してストライピングを行いました。

![接続した 5 つの Azure Standard ストレージ データ ディスクでストライピングを行い、ソフトウェア RAID で同様のバックアップをもう一度行います](media/sap-hana-backup-file-level/five-backup-disks.png)

接続した 5 つの Azure Standard ストレージ データ ディスクでストライピングを行い、ソフトウェア RAID で同様のバックアップをもう一度行うと、バックアップ時間は 42 分から 10 分に減少しました。 ディスクは VM へのキャッシュなしで接続してあったので、 この演習では、適切なバックアップ時間を実現するためのディスク書き込みスループットの重要性について説明します。 最適なパフォーマンスが得られるように、Azure Standard SSD ストレージまたは Azure Premium Storage に切り替えてプロセスをさらに高速化することができます。 一般に、Azure Standard HDD ストレージは推奨されず、デモンストレーションの目的でのみ使用されていました。 実稼働システムには、少なくとも Azure Standard SSD ストレージまたは Azure Premium Storage を使用することをお勧めします。

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Azure Blob Storage への SAP HANA バックアップ ファイルのコピー
示されているパフォーマンスの数値、バックアップ所要時間の数値、コピー所要時間の数値は、Azure テクノロジの最新の状態を表していない可能性があります。 Microsoft は、スループットの向上と待機時間の短縮を実現するために、Azure Storage を継続的に改善しています。 そのため、数値はデモンストレーションのみを目的としたものです。 自分にどの方法が最適かを判断できるように、選択した Azure リージョンで個別のニーズをテストする必要があります。

SAP HANA バックアップ ファイルを迅速に格納する他の方法は Azure Blob Storage です。 1 つの BLOB コンテナーには約 500 TB という上限があります。VM の種類が M32ts、M32ls、M64ls、GS5 の Azure を使用する SAP HANA システムでは、この容量で十分な SAP HANA バックアップを保持できます。 お客様は、&quot;ホット&quot; BLOB ストレージと &quot;コールド&quot; BLOB ストレージのいずれかを選択できます (「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)」を参照)。

blobxfer ツールを使用すると、SAP HANA バックアップ ファイルを Azure Blob Storage に簡単に直接コピーできます。

![この図には、完全な SAP HANA ファイル バックアップのファイルが表示されています](media/sap-hana-backup-file-level/list-of-backups.png)

この図には、完全な SAP HANA ファイル バックアップのファイルが表示されています。 4 つあるファイルのうち、最大のファイルは約 230 GB です。

![Azure Standard ストレージ アカウントの BLOB コンテナーにこの 230 GB のファイルをコピーするのに、約 3,000 秒かかりました](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

最初のテストでは md5 ハッシュを使用せず、Azure Standard ストレージ アカウントの BLOB コンテナーにこの 230 GB のファイルをコピーしたところ、約 3,000 秒かかりました。

HANA Studio バックアップ コンソールでは、HANA バックアップ ファイルの最大ファイル サイズを制限できます。 サンプル環境では、大きな 1 つの 230 GB のファイルを複数の小さなバックアップ ファイルとして処理することで、パフォーマンスが向上しました。

HANA 側でバックアップ ファイル サイズの上限を設定しても、バックアップ時間は短縮できません。ファイルの書き込みが順番に行われるためです。 ファイル サイズの上限を 60 GB に設定したため、230 GB の 1 つのファイルではなく 4 つの大きなデータ ファイルがバックアップで作成されました。 バックアップ ターゲットに BLOB サイズを表すファイル サイズの制限がある場合は、複数のバックアップ ファイルを使用することが、HANA データベースのバックアップに必要になることがあります。

![blobxfer ツールの並列処理をテストするために、HANA バックアップのファイル サイズの上限を 15 GB に設定しました](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

blobxfer ツールの並列処理をテストするために、HANA バックアップのファイル サイズの上限を 15 GB に設定しました。その結果バックアップ ファイルは 19 個になりました。 この構成では、blobxfer を使用して 230 GB のファイルを Azure Blob Storage にコピーするのにかかる時間が 3,000 秒から 875 秒になりました。

ローカル ディスクに対して実行されたバックアップを Azure Blob Storage などの他の場所にコピーする方法を検討しているときは、最終的な並列コピー プロセスで使用される帯域幅が、個々の VM の種類のネットワークまたはストレージ クォータを考慮していることに注意してください。 そのため、VM で実行している通常のワークロードに必要なネットワークおよびストレージの帯域幅と、コピーの所要時間とのバランスを取る必要があります。 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS 共有への SAP HANA バックアップ ファイルのコピー

Microsoft Azure では、[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) を介してネイティブ NFS 共有が提供されます。 バックアップを格納および管理するために、容量が数十 TB のさまざまなボリュームを作成できます。 また、NetApp のテクノロジに基づいて、それらのボリュームのスナップショットを作成することもできます。 Azure NetApp Files (ANF) は、ストレージ スループットが異なる 3 つの異なるサービス レベルで提供されます。 詳細については、記事「[Azure NetApp Files のサービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)」を参照してください。 ANF から NFS ボリュームを作成し、マウントすることができます。詳細については、記事「[クイック スタート: Azure NetApp Files を設定し、NFS ボリュームを作成する](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal)」を参照してください。

ANF から Azure のネイティブな NFS ボリュームを使用する以外にも、Azure 上に NFS 共有を提供する独自のデプロイの作成について、さまざまな可能性が考えられます。 すべてに欠点があります。これらのソリューションを自分でデプロイして管理する必要があるということです。 これらの可能性の一部については、次の記事で説明されています。

- [SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](./high-availability-guide-suse-nfs.md)
- [Red Hat Enterprise Linux for SAP NetWeaver における Azure VM での GlusterFS](./high-availability-guide-rhel-glusterfs.md)

上記の方法で作成された NFS 共有を使用して、ローカル ディスクに対して HANA バックアップを直接実行できます。または、ローカル ディスクに対して実行されたバックアップを、それらの NFS 共有にコピーできます。

> [!NOTE]
> SAP HANA では NFS v3 と NFS v4.x がサポートされています。 CIFS ファイル システムを使用する SMB のようなその他の形式では、HANA バックアップの書き込みはサポートされていません。 [SAP サポート ノート #1820529](https://launchpad.support.sap.com/#/notes/1820529) も参照してください

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Azure ファイルへの SAP HANA バックアップ ファイルのコピー

Azure Linux VM 内の Azure ファイル共有をマウントすることができます。 構成を行う方法の詳細については、記事「[Linux で Azure File Storage を使用する方法](../../../storage/files/storage-how-to-use-files-linux.md)」を参照してください。 Azure Files または Azure Premium ファイルの制限事項については、記事「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../../../storage/files/storage-files-scale-targets.md)」を参照してください。

> [!NOTE]
> CIFS ファイル システムを使用する SMB は、SAP HANA で HANA バックアップの書き込み先としてサポートされていません。 [SAP サポート ノート #1820529](https://launchpad.support.sap.com/#/notes/1820529) も参照してください。 このため、このソリューションは、ローカル接続されたディスクに対して直接実行された HANA データベース バックアップの最終的な宛先としてのみ使用できます。
> 

Azure Premium Files ではなく Azure Files に対して実行したテストで、全体のボリュームが 230 GB である 19 個のバックアップ ファイルをコピーするのに約 929 秒かかりました。 Azure Premium Files を使用すると、その時間が大幅に短縮されることが予想されます。 ただし、高速コピーの目的と、ネットワーク帯域幅に対するワークロードの要件とのバランスを取る必要があることに注意する必要があります。 すべての Azure VM の種類によってネットワーク帯域幅のクォータが適用されるため、ワークロードとバックアップ ファイルのコピーを含めて、そのクォータの範囲内に収まっている必要があります。

![この図は、19 個の SAP HANA バックアップ ファイルのコピーに約 929 秒かかったことを示しています](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Azure Files に SAP HANA バックアップ ファイルを格納することは、興味深いオプションです。 Azure Premium ファイルの待機時間とスループットが向上しているため、特にそうです。

## <a name="next-steps"></a>次のステップ
* 「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」には、概要と基本的な情報が記載されています。
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
