---
title: Azure Virtual Machines 上の SAP HANA のバックアップ ガイド | Microsoft Docs
description: SAP HANA のバックアップ ガイドでは、Azure 仮想マシン上の SAP HANA をバックアップする 2 つの主要な方法について説明します
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0004afb5895d7549e5db8ad5e53b52fa17991520
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667915"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SAP HANA のバックアップ ガイド

## <a name="getting-started"></a>作業の開始

Azure Virtual Machines で実行されている SAP HANA のバックアップ ガイドでは、Azure 固有のトピックのみを取り上げます。 SAP HANA のバックアップ関連の一般的な項目については、SAP HANA のドキュメントを参照してください。 ここでは、原則的なデータベースのバックアップ戦略、適切で有効なバックアップ戦略を用意する理由と動機を理解しており、バックアップ手順、バックアップの保有期間、および復元手順について会社が有する要件を認識していることを前提にしています。

SAP HANA は、Azure M シリーズなどのさまざまな Azure VM で正式にサポートされています。 SAP HANA が認定されている Azure VM と HANA Large Instance ユニットの完全な一覧については、「[Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)」(認定 IaaS プラットフォームの検索) を参照してください。 Microsoft Azure では、SAP HANA が物理サーバー上で非仮想化を実行する多数のユニットを提供しています。 このサービスは、[HANA Large Instances](hana-overview-architecture.md) と呼ばれます。 このガイドでは、HANA Large Instances のバックアップ プロセスとツールについては説明しません。 Azure 仮想マシンに限定されます。 HANA Large Instances でのバックアップ/復元プロセスの詳細については、[HLI のバックアップと復元](./hana-backup-restore.md)に関する記事を参照してください。

この記事では、Azure 仮想マシン上の SAP HANA をバックアップする 3 つの方法を中心に説明します。

- [Azure Backup サービス](../../../backup/backup-overview.md)により HANA をバックアップする 
- Azure Linux 仮想マシンのファイル システムに HANA をバックアップする (「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」を参照)
- 手動の Azure ストレージ BLOB スナップショット機能または Azure Backup サービスを使用して、ストレージ スナップショットに基づいて HANA をバックアップする


SAP HANA には、サードパーティ製のバックアップ ツールを SAP HANA に直接統合できるバックアップ API が用意されています Azure Backup サービスや [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) などの製品では、この専用のインターフェイスを使用して SAP HANA データベースまたは再実行ログのバックアップがトリガーされます。 


Azure でサポートされている SAP ソフトウェアの詳細については、「[Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md)」を参照してください。

## <a name="azure-backup-service"></a>Azure Backup サービス

最初に示すシナリオは、Azure Backup サービスが SAP HANA `backint` インターフェイスを使用して、SAP HANA データベースからのストリーミング バックアップを実行するシナリオです。 または、Azure Backup サービスのより汎用的な機能を使用して、アプリケーション整合性のあるディスク スナップショットを作成し、それを Azure Backup サービスに転送します。

Azure Backup は、[backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) と呼ばれる専用の SAP HANA インターフェイスを使用して統合され、SAP HANA のバックアップ ソリューションとして認定を受けています。 ソリューション、その機能、および使用できる Azure リージョンの詳細については、[Azure VM 上の SAP HANA データベースのバックアップに関するサポート マトリックス](../../../backup/sap-hana-backup-support-matrix.md#scenario-support)」を参照してください。 HANA の Azure Backup サービスの詳細と原則については、「[Azure VM での SAP HANA データベースバックアップについて](../../../backup/sap-hana-db-about.md)」の記事を参照してください。 

Azure Backup サービスを利用する 2 つ目の方法は、Azure Premium Storage のディスク スナップショットを使用してアプリケーション整合性バックアップを作成することです。 [Azure Ultra ディスク](../../disks-enable-ultra-ssd.md)や [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) など、その他の HANA 認定 Azure ストレージでは、Azure Backup サービスによるこの種のスナップショットはサポートされていません。 次の記事を参照してください。

- [Azure における VM バックアップ インフラストラクチャの計画を立てる](../../../backup/backup-azure-vms-introduction.md)
- [Azure Linux VM のアプリケーション整合性バックアップ](../../../backup/backup-azure-linux-app-consistent.md) 

次の一連のアクティビティが発生します。

- Azure Backup は、アプリケーション (この場合 SAP HANA) を整合性のある状態にするプリスナップショット スクリプトを実行する必要があります。
- この整合性の状態が確認されると、Azure Backup によってディスクのスナップショットが実行されます。
- スナップショットを完了すると、プリスナップショット スクリプトで実行したアクティビティが Azure Backup によって元に戻されます。
- 実行が成功すると、Azure Backup はデータをバックアップ コンテナーにストリーミングします。

SAP HANA の場合、ほとんどのお客様は、SAP HANA の再実行ログを含むボリュームに対して Azure 書き込みアクセラレータを使用しています。 Azure Backup サービスは、これらのボリュームをスナップショットから自動的に除外します。 この除外によって、HANA の復元機能に悪影響を与えることはありません。 ただし、SAP でサポートされているその他のほとんどすべての DBMS では、復元できなくなります。

この可能性の欠点は、独自のプリおよびポストスナップショット スクリプトを開発する必要があることです。 プリスナップショット スクリプトでは、HANA スナップショットを作成し、その後発生する例外ケースを処理する必要があります。 これに対して、ポストスナップショット スクリプトは HANA スナップショットを再度削除する必要があります。 必要なロジックの詳細については、「[SAP サポート ノート #2039883](https://launchpad.support.sap.com/#/notes/2039883)」を参照してください。 この記事の「ストレージ スナップショットを作成するときの SAP HANA データの一貫性」セクションの考慮事項は、この種類のバックアップに完全に適用されます。

> [!NOTE]
> 複数のデータベース コンテナーが使用されている配置での SAP HANA のディスク スナップショット ベースのバックアップでは、HANA 2.0 SP04 以上のリリースが必要です。
> 

ストレージ スナップショットの詳細については、このドキュメントの後半を参照してください。

![この図は、現在の VM の状態を保存する 2 つの方法を示しています](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>その他の HANA バックアップ方法
次の 3 つのバックアップ方法またはパスを検討することができます。

- Azure NetApp Files (ANF) に基づく NFS 共有に対するバックアップ。 ANF では、バックアップを格納するボリュームのスナップショットを作成することができます。 バックアップを書き込むために最終的に必要なスループットを考えると、このソリューションはコストのかかる方法になる可能性があります。 ただし、HANA は Azure ネイティブの NFS 共有にバックアップを直接書き込むことができるため、簡単に確立できます。
- Standard SSD または Azure Premium Storage の VM に接続されているディスクに対して HANA バックアップを実行する。 次の手順として、これらのバックアップ ファイルを Azure BLOB ストレージにコピーできます。 この戦略は、価格の面で魅力的な場合があります。
- Standard SSD または Azure Premium Storage の VM に接続されているディスクに対して HANA バックアップを実行する。 次の手順として、ディスクのスナップショットが定期的に取得されます。 最初のスナップショットの後、増分スナップショットを使用してコストを削減することができます。

![この図は、VM 内の SAP HANA ファイル バックアップを作成する方法について示しています](media/sap-hana-backup-guide/other-hana-backup-paths.png)

この図は、VM 内の SAP HANA ファイル バックアップを作成したうえで、各種ツールを使用してその HANA バックアップ ファイルを別の場所に格納する方法を示しています。 ただし、サードパーティのバックアップ サービスまたは Azure Backup サービスに関係しないすべてのソリューションには、共通するいくつかの課題があります。 その中には、リテンション期間の管理、自動復元プロセス、Azure Backup サービスやその他の特殊なサードパーティ製のバックアップ スイートとサービスが提供する特定の時点への自動復旧などが挙げられます。 これらのサードパーティのサービスの多くは、Azure で実行できます。 


## <a name="sap-resources-for-hana-backup"></a>HANA バックアップ関連の SAP リソース

### <a name="sap-hana-backup-documentation"></a>SAP HANA のバックアップに関するドキュメント

- [SAP HANA 管理の概要](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [バックアップと回復の戦略の計画](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [ABAP DBACOCKPIT を使用した HANA バックアップのスケジュール設定](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [データ バックアップのスケジュール設定 (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- [SAP ノート 1642148](https://launchpad.support.sap.com/#/notes/1642148) に記載されている SAP HANA バックアップに関する FAQ
- [SAP ノート 2039883](https://launchpad.support.sap.com/#/notes/2039883) に記載されている、SAP HANA のデータベース スナップショットとストレージ スナップショットに関する FAQ
- [SAP ノート 1820529](https://launchpad.support.sap.com/#/notes/1820529) に記載されている、バックアップと回復に適していないネットワーク ファイル システム

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA バックアップの正確性を確認する方法
バックアップ方法に関係なく、別のシステムに対してテスト復元を実行することは絶対に必要です。 そうすることで、バックアップが正しいことと、バックアップと復元の内部処理が正常に機能することを確かめられます。 バックアップの復元は、インフラストラクチャの要件が理由でオンプレミスでは非常に困難ですが、目的に合わせて必要なリソースを一時的に増やせるクラウドでは、はるかに簡単に実行できます。 HANA で提供されるツールによって、バックアップ ファイルを復元できるかどうかを確認できます。 ただし、頻繁に復元を実行する目的は、データベースの復元プロセスをテストし、運用スタッフと一緒にそのプロセスをトレーニングすることです。

簡単な復元を行って HANA の稼働を確認するだけでは不十分であることに留意してください。 テーブルの整合性チェックを実行して、復元されたデータベースに問題がないことを確認する必要があります。 SAP HANA には数種類の整合性チェックが用意されており、これらは [SAP ノート 1977584](https://launchpad.support.sap.com/#/notes/1977584) で説明されています。

テーブルの整合性チェックに関する情報は、SAP Web サイトの「[Table and Catalog Consistency Checks (テーブルとカタログの整合性チェック)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)」にもあります。

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA バックアップとストレージ スナップショットの長所と短所

HANA バックアップとストレージ スナップショットについては、いずれかが SAP によって推奨されているということはありません。 状況と使用できるストレージ テクノロジに応じてどちらを使用するかを判断できるよう、双方の長所と短所の一覧が SAP によって作成されています (「[Planning Your Backup and Recovery Strategy (バックアップと回復の戦略の計画)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)」を参照)。

Azure では、Azure BLOB スナップショット機能を使っても複数のディスクにまたがってファイル システムの整合性が確保されるわけでない点に注意してください ([PowerShell による BLOB スナップショットの使用](/archive/blogs/cie/using-blob-snapshots-with-powershell)に関するページを参照)。 

さらに、BLOB スナップショットを頻繁に使用する場合、次の記事で説明されているとおり、課金への影響を把握する必要があります: 「[Understanding How Snapshots Accrue Charges (スナップショットの課金方法について)](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)」。これは Azure 仮想ディスクの使用よりもわかりにくくなっています。

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>ストレージ スナップショットを作成する際の SAP HANA データの整合性

前に説明したように、ストレージ スナップショットを取得する場合は、Azure Backup のスナップショット バックアップ機能、ファイルシステム、およびアプリケーション整合性について説明する必要があります。 問題を回避するための方法として、SAP HANA (場合によっては仮想マシン全体) をシャットダウンするのが最も簡単です。 これは、実稼働インスタンスでは実現できないことです。

> [!NOTE]
> 複数のデータベース コンテナーが使用されている配置での SAP HANA のディスク スナップショット ベースのバックアップでは、HANA 2.0 SP04 以上のリリースが必要です。
> 

Azure Storage では、スナップショット処理中に VM に接続されている複数のディスクまたはボリューム間でのファイル システムの整合性は提供されません。 つまり、スナップショット中のアプリケーションの整合性は、アプリケーションによって実現される必要があります (この場合 SAP HANA 自体です)。 [SAP ノート 2039883](https://launchpad.support.sap.com/#/notes/2039883) には、ストレージ スナップショットを使用した SAP HANA バックアップに関する重要な情報が記載されています。 たとえば、XFS ファイル システムでアプリケーションの整合性を確保するには、ストレージ スナップショットを開始する前に **xfs\_freeze** を実行する必要があります (**xfs\_freeze** の詳細については「[xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) (xfs_freeze(8) - Linux man ページ)」を参照)。

XFS ファイル システムが 4 つの Azure 仮想ディスクにまたがっていると仮定すると、HANA のデータ領域を表す一貫したスナップショットを作成する手順は、次のようになります。

1. HANA データ スナップショットの準備を作成する
1. すべてのディスク/ボリュームのファイル システムをフリーズする (たとえば、**xfs\_freeze** を使用する)
1. Azure で必要な BLOB スナップショットをすべて作成する
1. ファイル システムのフリーズを解除する
1. HANA データ スナップショットを確認する (スナップショットは削除されます)

Azure Backup の機能を使用してアプリケーション整合性スナップショットのバックアップを実行する場合、手順 1 は、プリスナップショット スクリプト用にコード化/スクリプト作成を行う必要があります。 Azure Backup サービスは、手順 2 と 3 を実行します。 手順 4 と 5 は、ポストスナップショット スクリプト内のコードで指定する必要があります。 Azure Backup サービスを使用していない場合は、手順 2 と 3 も独自にコーディング/スクリプト化する必要があります。
HANA データ スナップショットの作成の詳細については、次の記事を参照してください。

- [HANA データ スナップショット](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- 手順 1 の詳細については、「[Create a Data Snapshot (Native SQL) (データ スナップショットを作成する (ネイティブ SQL))](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)」を参照してください。 
- 手順 5 で必要とされる HANA データ スナップショットの確認/削除の詳細については、「[Create a Data Snapshot (Native SQL) (データ スナップショットを作成する (ネイティブ SQL))](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)」を参照してください。 

HANA スナップショットの確認は重要です。 &quot;コピーオンライト&quot; 方式のため、このスナップショット準備モードの間、追加のディスク領域が SAP HANA に必要でない場合があります。 さらに、SAP HANA スナップショットを確認するまで、新しいバックアップを開始することもできません。


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA バックアップのスケジュール戦略

SAP HANA の記事「[Planning Your Backup and Recovery Strategy (バックアップと回復の戦略の計画)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)」には、バックアップを実行する基本的な計画が記載されています。 SAP HANA のバックアップ/復元戦略とプロセスを定義する際には、HANA に関する SAP ドキュメントとその他の DBMS に関するご自身の経験を参考にしてください。 さまざまな種類のバックアップの順序、および保持期間は、提供する必要がある SLA に大きく依存します。


### <a name="sap-hana-backup-encryption"></a>SAP HANA バックアップの暗号化

SAP HANA ではデータとログの暗号化を行えます。 SAP HANA のデータとログが暗号化されていない場合、既定ではバックアップは暗号化されません。 ただし、SAP HANA では、「[SAP HANA Backup Encryption (SAP HANA バックアップの暗号化)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)」に記載されているように、個別のバックアップ暗号化が提供されています。 以前のリリースの SAP HANA を実行している場合は、バックアップ暗号化が既に提供されている機能の一部であったかどうかの確認が必要になることがあります。  


## <a name="next-steps"></a>次のステップ
* 「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」では、ファイルベースのバックアップ方法について説明されています。
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
