---
title: ファイル レベルの SAP HANA Azure バックアップ | Microsoft Docs
description: Azure 仮想マシン上の SAP HANA をバックアップする方法は主に 2 つあります。この記事では、ファイル レベルの SAP HANA Azure バックアップについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 8860c943dafdb9d166510519d0fb058f523537b3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078898"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>ファイル レベルの SAP HANA Azure バックアップ

## <a name="introduction"></a>はじめに

この記事は、SAP HANA のバックアップに関する 3 部構成の記事の 1 つです。 「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](./sap-hana-backup-guide.md)」には、概要と基本的な情報が記載されており、「[ストレージ スナップショットに基づいた SAP HANA のバックアップ](./sap-hana-backup-storage-snapshots.md)」には、ストレージ スナップショットベースのバックアップ オプションについての説明があります。

Azure の別の VM タイプでは、異なる数の VHD の接続が許可されます。 正確な詳細については、「[Azure の Linux 仮想マシンのサイズ](../../linux/sizes.md)」を参照してください。 このドキュメントで参照されているテストのため、64 の接続されたデータ ディスクが許可される GS5 Azure VM を使用しました。 大規模な SAP HANA システムでは、データ ファイルとログ ファイルに多数のディスクが既に使用されていることがあります。場合によっては、ディスク IO スループットを最適化するためにソフトウェア ストライピングが併用されています。 Azure VM での SAP HANA デプロイ向けに推奨されるディスク構成の詳細については、記事「[SAP HANA on Azure 運用ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)」を参照してください。 推奨事項には、ローカルのバックアップのディスク領域の推奨事項も含まれています。

現時点では、Azure Backup サービスと共に使用できる SAP HANA バックアップ統合はありません。 ファイル レベルでバックアップ/復元を管理する標準的な方法としては、SAP HANA Studio または SAP HANA SQL ステートメントを使用したファイルベースのバックアップがあります。 詳細については、「[SAP HANA SQL and System Views Reference (SAP HANA SQL とシステム ビューのリファレンス)](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)」を参照してください。

![この図は、SAP HANA Studio のバックアップ メニュー項目のダイアログを示しています](media/sap-hana-backup-file-level/image022.png)

この図は、SAP HANA Studio のバックアップ メニュー項目のダイアログを示しています。 種類 &quot;ファイル&quot; を選択した場合、SAP HANA のバックアップ ファイルの書き込み先となるファイル システムのパスを指定する必要があります。 これは、復元を実行するときも同様です。

この選択は簡単で単純なように思えますが、いくつかの考慮事項が存在します。 前述のとおり、Azure VM に接続できるデータ ディスクの数には上限があります。 データベースのサイズとディスク スループットの要件しだいでは、VM のファイル システム上に SAP HANA バックアップ ファイルを格納する容量がない可能性があります。場合によっては、複数のデータ ディスクへのソフトウェア ストライピングが必要です。 これらのバックアップ ファイルを移動したり、テラバイト単位のデータを処理する際にファイル サイズ制限とパフォーマンスを管理したりするための各種の方法について、この記事で後ほど説明します。

合計容量に関して自由度の高い別のオプションとしては、Azure Blob Storage があります。 1 つの BLOB の容量は 1 TB に制限されているものの、1 つの BLOB コンテナーの合計容量は現在 500 TB です。 さらに Azure Blob Storage では、コスト面で優れている、いわゆる &quot;クール&quot; BLOB ストレージを選択して使用できます。 クール BLOB ストレージの詳細については、「[Azure Blob Storage: ホット ストレージ層とクール ストレージ層](../../../storage/blobs/storage-blob-storage-tiers.md)」を参照してください。

安全性を追求する場合は、geo レプリケーションされたストレージ アカウントを使用して SAP HANA バックアップを格納します。 ストレージ アカウント レプリケーションの詳細については、「[Azure Storage のレプリケーション](../../../storage/common/storage-redundancy.md)」を参照してください。

SAP HANA バックアップの専用 VHD を、geo レプリケーションされた専用バックアップ ストレージ アカウントに格納することができます。 そのほか、geo レプリケーションされたストレージ アカウントまたは別のリージョンにあるストレージ アカウントに、SAP HANA バックアップが保持された VHD をコピーできます。

## <a name="azure-backup-agent"></a>Azure Backup エージェント

Azure Backup には、VM を丸ごとバックアップする方法だけでなく、Backup エージェント (ゲスト OS へのインストールが必要) を介してファイルとディレクトリをバックアップする方法もあります。 ただし、このエージェントがサポートされているのは Windows のみです (「[Resource Manager デプロイ モデルで Windows Server または Windows クライアントを Azure にバックアップする](../../../backup/backup-configure-vault.md)」を参照)。

次善の策としては、(たとえば SAMBA 共有を使用して) SAP HANA バックアップ ファイルを Azure の Windows VM にコピーしてから、そこで Azure Backup エージェントを使用する方法があります。 技術的には可能ですが、Linux VM と Windows VM の間でコピーを行うため、バックアップ プロセスまたは復元プロセスがかなり複雑になるうえ、プロセスにかかる時間がかなり長くなります。 この方法を実行することはお勧めしません。

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer ユーティリティの詳細

ディレクトリとファイルを Azure ストレージに格納するには、CLI または PowerShell を使用できます。また、いずれかの [Azure SDK](https://azure.microsoft.com/downloads/) を使用してツールを作成できます。 Azure Storage にデータをコピーするための、すぐに使用できるユーティリティ AzCopy もあります。 ([AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../../../storage/common/storage-use-azcopy.md)方法に関するページを参照してください)。

そのため、SAP HANA バックアップ ファイルのコピーには blobxfer を使用しました。 blobxfer は、多くのお客様が運用環境で使用しているオープン ソース ツールであり、[GitHub](https://github.com/Azure/blobxfer) で入手できます。 このツールでは、Azure Blob Storage と Azure ファイル共有のどちらにもデータを直接コピーできます。 さらに、md5 ハッシュや、複数のファイルが含まれたディレクトリをコピーする際の自動並列処理など、さまざまな便利な機能があります。

## <a name="sap-hana-backup-performance"></a>SAP HANA バックアップのパフォーマンス

![このスクリーンショットは、SAP HANA Studio の SAP HANA バックアップ コンソールの一部です](media/sap-hana-backup-file-level/image023.png)

このスクリーンショットは、SAP HANA Studio の SAP HANA バックアップ コンソールの一部です。 XFS ファイル システムが使用されている HANA VM に接続された単一の Azure Standard ストレージ ディスクで 230 GB のバックアップを実行したところ、約 42 分かかりました。

![このスクリーンショットは、SAP HANA テスト VM の YaST の一部です](media/sap-hana-backup-file-level/image024.png)

このスクリーンショットは、SAP HANA テスト VM の YaST の一部です。 前述のとおり SAP HANA バックアップ用に 1 TB のディスクが 1 つ使用されていることがわかります。 230 GB のバックアップに約 42 分かかりました。 さらに、200 GB のディスクを 5 つ接続してソフトウェア RAID md0 を作成し、これらの 5 つの Azure データ ディスクに対してストライピングを行いました。

![接続した 5 つの Azure Standard ストレージ データ ディスクでストライピングを行い、ソフトウェア RAID で同様のバックアップをもう一度行います](media/sap-hana-backup-file-level/image025.png)

接続した 5 つの Azure Standard ストレージ データ ディスクでストライピングを行い、ソフトウェア RAID で同様のバックアップをもう一度行うと、バックアップ時間は 42 分から 10 分に減少しました。 ディスクは VM へのキャッシュなしで接続してあったので、 バックアップ時間におけるディスク書き込みのスループットの重要性は明らかです。 また、最適なパフォーマンスが得られるように、Azure Premium Storage に切り替えてプロセスをさらに高速化することができます。 一般に、運用システムでは Azure Premium Storage の使用をお勧めします。

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Azure Blob Storage への SAP HANA バックアップ ファイルのコピー

SAP HANA バックアップ ファイルを迅速に格納する他の方法は Azure Blob Storage です。 1 つの BLOB コンテナーには 500 TB の上限があります。M32ts、M32ls、M64ls、GS5 VM タイプの Azure を使用する小規模な一部の SAP HANA システムでは、この容量で必要な SAP HANA バックアップを十分保持できます。 お客様は、&quot;ホット&quot; BLOB ストレージと &quot;コールド&quot; BLOB ストレージのいずれかを選択できます (「[Azure Blob Storage: ホット ストレージ層とクール ストレージ層](../../../storage/blobs/storage-blob-storage-tiers.md)」を参照)。

blobxfer ツールを使用すると、SAP HANA バックアップ ファイルを Azure Blob Storage に簡単に直接コピーできます。

![この図には、完全な SAP HANA ファイル バックアップのファイルが表示されています](media/sap-hana-backup-file-level/image026.png)

この図には、完全な SAP HANA ファイル バックアップのファイルが表示されています。 4 つのファイルがあり、最大のファイルは約 230 GB です。

![Azure Standard ストレージ アカウントの BLOB コンテナーにこの 230 GB のファイルをコピーするのに、約 3,000 秒かかりました](media/sap-hana-backup-file-level/image027.png)

最初のテストでは md5 ハッシュを使用せず、Azure Standard ストレージ アカウントの BLOB コンテナーにこの 230 GB のファイルをコピーしたところ、約 3,000 秒かかりました。

![このスクリーンショットでは、Azure Portal で BLOB コンテナーがどのように表示されるかがわかります](media/sap-hana-backup-file-level/image028.png)

このスクリーンショットでは、Azure Portal で BLOB コンテナーがどのように表示されるかがわかります。 &quot;sap-hana-backups&quot; という名前の BLOB コンテナーが作成され、SAP HANA バックアップ ファイルを表す 4 つの BLOB がその中に含まれています。 そのうち 1 つのサイズは約 230 GB です。

HANA Studio バックアップ コンソールでは、HANA バックアップ ファイルの最大ファイル サイズを制限できます。 サンプル環境では、大きな 1 つの 230 GB のファイルを複数の小さなバックアップ ファイルとして処理できたため、パフォーマンスが向上しました。

![HANA 側でバックアップ ファイル サイズの上限を設定しても、バックアップ時間は短縮できません](media/sap-hana-backup-file-level/image029.png)

HANA 側でバックアップ ファイル サイズの上限を設定しても、バックアップ時間は短縮できません。この図が示すように、ファイルの書き込みが順番に行われるためです。 ファイル サイズの上限を 60 GB に設定したため、230 GB の 1 つのファイルではなく 4 つの大きなデータ ファイルがバックアップで作成されました。 M64s、M64ms、M128s、M128ms などの大規模な Azure VM のメモリを利用する HANA データベースのバックアップには、複数のバックアップ ファイルを使用する必要があります。

![blobxfer ツールの並列処理をテストするために、HANA バックアップのファイル サイズの上限を 15 GB に設定しました](media/sap-hana-backup-file-level/image030.png)

blobxfer ツールの並列処理をテストするために、HANA バックアップのファイル サイズの上限を 15 GB に設定しました。その結果バックアップ ファイルは 19 個になりました。 この構成では、blobxfer を使用して 230 GB のファイルを Azure Blob Storage にコピーするのにかかる時間が 3,000 秒から 875 秒になりました。

このような結果になる理由は、Azure BLOB の書き込みに関して 60 MB/秒の制限があることです。 複数の BLOB で並列処理を行うと、このボトルネックを解消できます。ただし欠点があって、blobxfer ツールのパフォーマンスを向上させて、これらすべての HANA バックアップ ファイルを Azure Blob Storage にコピーすると、HANA VM とネットワークの両方に負荷がかかります。 これは、HANA システムの動作に影響を及ぼします。

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>バックアップ ソフトウェア RAID 内の専用 Azure データ ディスクの BLOB コピー

この手法では、HANA データ、HANA ログ ファイル、構成ファイルなど、VM 上のすべてのデータ ディスクをバックアップして SAP インストール全体を保存することはしません。この点、手動の VM データ ディスク バックアップと異なります。 その代わり、複数の Azure データ VHD でストライピングを行う専用ソフトウェア RAID を作成し、完全な SAP HANA ファイル バックアップを格納することを検討します。 コピーの対象は、SAP HANA バックアップが含まれているこれらのディスクのみです。 これらのディスクは、専用 HANA バックアップ ストレージ アカウントで簡単に保持できます。または、さらに処理を行うために専用 &quot;バックアップ管理 VM&quot; に接続することができます。

![すべての関連 VHD は、**start-azurestorageblobcopy** PowerShell コマンドを使用してコピーしました](media/sap-hana-backup-file-level/image031.png)

ローカル ソフトウェア RAID へのバックアップが完了した後、すべての関連 VHD は **start-azurestorageblobcopy** PowerShell コマンドを使用してコピーしました (「[Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)」を参照)。 バックアップ ファイルを保持する専用ファイル システムにしか影響がないため、ディスク上の SAP HANA データまたはログ ファイルの整合性について心配する必要がありません。 このコマンドの長所は、VM を稼働したまま使用できる点です。 バックアップ ストライプ セットに対して書き込みを行うプロセスがないように、必ず BLOB コピーの前にマウントを解除し、後でマウントし直してください。 または、適切な方法を使用してファイル システムを &quot;フリーズ&quot; できます。 たとえば、XFS ファイル システムでは xfs\_freeze を使用できます。

![この Azure Portal のスクリーンショットは、vhds コンテナー中の BLOB の一覧を示しています](media/sap-hana-backup-file-level/image032.png)

この Azure Portal のスクリーンショットは、&quot;vhds&quot; コンテナー中の BLOB の一覧を示しています。 ここには 5 つの VHD が表示されています。これらの VHD は、ソフトウェア RAID として機能して SAP HANA バックアップ ファイルを保持するよう、SAP HANA サーバー VM に接続されました。 また、5 つのコピーも表示されています。これらのコピーは BLOB コピーのコマンドを使用して作成しました。

![テスト目的で、SAP HANA バックアップ ソフトウェア RAID ディスクのコピーを、アプリ サーバー VM に接続しました](media/sap-hana-backup-file-level/image033.png)

テスト目的で、SAP HANA バックアップ ソフトウェア RAID ディスクのコピーを、アプリ サーバー VM に接続しました。

![ディスク コピーを接続するためにアプリ サーバー VM をシャットダウンしました](media/sap-hana-backup-file-level/image034.png)

ディスク コピーを接続するためにアプリ サーバー VM をシャットダウンしました。 VM の起動後、ディスクと RAID は正常に見つかりました (UUID によるマウント)。 YaST パーティショナーで作成されたマウント ポイントのみ見つかりませんでした。 その後、OS レベルで SAP HANA バックアップ ファイルのコピーが見えるようになりました。

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS 共有への SAP HANA バックアップ ファイルのコピー

パフォーマンスまたはディスク領域に関する SAP HANA システムへの潜在的な影響を抑えるために、SAP HANA バックアップ ファイルを NFS 共有に格納することを検討できます。 これは技術的に可能ですが、NFS 共有のホストとして 2 つ目の Azure VM を使用することになります。 サイズの小さい VM は、VM ネットワーク帯域幅が低いため使用しないようにしてください。 この &quot;バックアップ VM&quot; は、シャットダウンしておいて SAP HANA バックアップを実行する場合にのみ起動するのが合理的です。 NFS 共有への書き込みを行うと、ネットワークに負荷がかかるほか、SAP HANA システムに影響があります。ただし、&quot;バックアップ VM&quot; でバックアップ ファイルを管理するだけであれば、SAP HANA システムへの影響はありません。

![別の Azure VM の NFS 共有を SAP HANA サーバー VM にマウントしました](media/sap-hana-backup-file-level/image035.png)

NFS のユース ケースを検証するために、別の Azure VM の NFS 共有を SAP HANA サーバー VM にマウントしました。 特別な NFS チューニングは適用していません。

![バックアップを直接実行するのに 1 時間 46 分かかりました](media/sap-hana-backup-file-level/image036.png)

NFS 共有は、SAP HANA サーバーと同様に高速なストライプ セットでした。 しかし、NFS 共有に対してバックアップを直接行ったところ、1 時間 46 分かかりました。ローカル ストライプ セットへの書き込みであれば、これは 10 分で済みます。

![別の方法では 1 時間 43 分かかり、あまり時間の短縮につながりませんでした](media/sap-hana-backup-file-level/image037.png)

ローカル ストライプ セットに対してバックアップを実行し、OS レベルで NFS 共有にコピー (簡単な **cp -avr** コマンドを使用) するという別の方法では、あまり時間の短縮につながりませんでした。 これは 1 時間 43 分かかりました。

このように、NFS 共有は使用できます。ただし、230 GB のバックアップ テストでのパフォーマンスは良くありませんでした。 数テラバイトではパフォーマンスがもっと低くなると予想されます。

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Azure ファイルへの SAP HANA バックアップ ファイルのコピー

Azure Linux VM 内の Azure ファイル共有をマウントすることができます。 これを実行する方法の詳細については、記事「[Linux で Azure File Storage を使用する方法](../../../storage/files/storage-how-to-use-files-linux.md)」を参照してください。 現在、Azure ファイル共有あたり 5 TB のクォータ制限があり、ファイルあたり 1 TB のサイズ制限があることに注意してください。 ストレージの制限については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../../../storage/common/storage-scalability-targets.md)」を参照してください。

しかしテストの結果によると、現時点では CIFS マウントを使用した場合、SAP HANA バックアップを直接実行することができません。 [SAP ノート 1820529](https://launchpad.support.sap.com/#/notes/1820529) でも、CIFS は非推奨であると述べられています。

![この図は、SAP HANA Studio のバックアップ ダイアログのエラーを示しています](media/sap-hana-backup-file-level/image038.png)

この図は、CIFS でマウントされた Azure ファイル共有に直接バックアップしようとした場合に表示される、SAP HANA Studio のバックアップ ダイアログのエラーを示しています。 このため、まず標準的な SAP HANA バックアップを VM ファイル システムに対して行ったうえで、そこからバックアップ ファイルを Azure ファイル サービスにコピーする必要があります。

![この図は、19 個の SAP HANA バックアップ ファイルのコピーに約 929 秒かかったことを示しています](media/sap-hana-backup-file-level/image039.png)

この図は、合計サイズが約 230 GB である 19 個の SAP HANA バックアップ ファイルを Azure ファイル共有にコピーするのに、約 929 秒かかったことを示しています。

![SAP HANA VM 上のソース ディレクトリ構造が Azure ファイル共有にコピーされました](media/sap-hana-backup-file-level/image040.png)

このスクリーンショットでは、SAP HANA VM 上のソース ディレクトリ構造が Azure ファイル共有にコピーされたことがわかります。コピーされたのは、1 つのディレクトリ (hana\_backup\_fsl\_15gb) と 19 個の個別のバックアップ ファイルです。

Azure ファイルへの SAP HANA バックアップ ファイルの格納は、今後興味深いオプションになる可能性があります。それは、SAP HANA ファイル バックアップを直接格納できるようになった場合か、 NFS 経由の Azure ファイルのマウントが可能になり、クォータの上限が 5 TB から大幅に増加した場合です。

## <a name="next-steps"></a>次の手順
* 「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」には、概要と基本的な情報が記載されています。
* 「[ストレージ スナップショットに基づいた SAP HANA のバックアップ](sap-hana-backup-storage-snapshots.md)」では、ストレージ スナップショットベースのバックアップ方法について説明されています。
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
