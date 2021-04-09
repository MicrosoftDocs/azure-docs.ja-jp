---
title: Azure Virtual Machines での Oracle Database のバックアップ戦略
description: Azure Linux VM 環境で Oracle Database をバックアップするためのオプション。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673210"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Azure Linux VM での Oracle Database のバックアップ戦略

データベースのバックアップによって、ストレージ コンポーネント障害やデータセンター障害が原因のデータ損失からデータベースが保護されます。 また、人的エラーから復旧する手段として、あるいは開発やテスト目的でデータベースを複製する方法としても使用できます。 

Azure では、すべてのストレージが高度に冗長化されており、1 つまたは複数のディスクの損失によってデータベースの停止が発生することはないため、バックアップは、人的エラーからの保護、複製操作、または規制目的でのデータ保存のために特に多く使用されます。 また、DataGuard などのディザスター リカバリー テクノロジが使用されない場合のリージョンの停止から保護する手段でもあります。 この場合、プライマリ データベース リージョン外で使用できるように、geo 冗長レプリケーションを使用して、バックアップを異なる Azure リージョンに格納する必要があります。

## <a name="azure-storage"></a>Azure Storage 

[Azure Storage サービス](../../../storage/common/storage-introduction.md)は、最新のデータ ストレージ シナリオのための Microsoft のクラウド ストレージ ソリューションです。 Azure Storage には、Oracle データベースのバックアップ メディアとして適している、Azure Linux VM に外部ストレージをマウントするために使用できる多数のサービスが用意されています。 バックアップまたは復元操作を開始し、Azure Storage サービスとの間でバックアップをコピーするには、Oracle RMAN などのバックアップ ツールが必要です。
 
Azure Storage サービスには次の利点があります。

-  持続性と高可用性。 冗長性により、一時的なハードウェア障害が発生した場合でも、データが安全に保たれます。 すべてのストレージは、既定でトリプルミラー化されます。 地域的な災害または自然災害から保護するために、データ センターまたは地理的リージョンにまたがってデータをレプリケートすることもできます。 この方法でレプリケートされたデータは、予期しない停止が発生した場合でも高可用性を維持します。

-  セキュリティで保護されます。 Azure Storage アカウントに書き込まれたすべてのデータがサービスによって暗号化されます。 Azure Storage では、データにアクセスできるユーザーをきめ細かく制御できます。

-  スケーラブル。 Azure Storage には、今日のアプリケーションのデータ ストレージとパフォーマンスに関するニーズを満たすために、高度にスケーラブルな設計が採用されています。

-  管理されている Azure では、お客様に代わって、ハードウェアのメンテナンス、更新プログラム、および重大な問題への対処が行われます。

-  アクセス可能。 Azure Storage 内のデータには、世界中のどこからでも HTTP または HTTPS 経由でアクセスできます。 Microsoft では、.NET、Java、Node.js、Python、PHP、Ruby、Go などのさまざまな言語の Azure Storage 用のクライアント ライブラリと、成熟した REST API を提供しています。 Azure Storage では、Azure PowerShell または Azure CLI によるスクリプトの実行がサポートされます。 また、Azure Portal と Azure Storage Explorer により、データを操作するための使いやすい視覚的ソリューションが提供されます。

Azure Storage プラットフォームには、Oracle データベースのバックアップ メディアとして使用するのに適した次のデータ サービスが含まれています。

-  Azure BLOB:テキストおよびバイナリ データのための高度にスケーラブルなオブジェクト ストア。 また、Data Lake Storage Gen2 によるビッグ データ分析のサポートも含まれています。

-  Azure Files:クラウドまたはオンプレミスのデプロイ用のマネージド ファイル共有。

-  Azure ディスク: Azure VM のためのブロック レベルのストレージ ボリューム。

### <a name="cross-regional-storage-mounting"></a>リージョンをまたがるストレージ マウント

リージョンをまたいでバックアップ ストレージにアクセスできる機能は、BCDR の重要な側面であり、データベースをバックアップから別の地理的リージョンに複製する場合に便利です。 Azure クラウド ストレージでは、5 つのレベルの[冗長性](../../../storage/common/storage-redundancy.md)が提供されます。
- ローカル冗長ストレージ [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) では、プライマリ リージョンの 1 つの物理的な場所内で、データが 3 回レプリケートされます。  
- ゾーン冗長ストレージ [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) では、データがプライマリ リージョンで LRS によって保護され、プライマリ リージョンの 3 つの可用性ゾーン (AZ) 間で同期的にレプリケートされます。 各 AZ は LRS でも保護されます。 
- geo 冗長ストレージ [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) では、データがプライマリ リージョンで LRS によって保護され、やはり LRS によって保護されているセカンダリ リージョンに非同期的にレプリケートされます。
- geo ゾーン冗長ストレージ [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) では、データは ZRS を使用して、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にコピーされます。 その後、セカンダリ リージョンの 1 つの物理的な場所にデータを非同期的にコピーします。 すべての場所で、データは LRS によっても保護されます。 
- 読み取りアクセス geo 冗長ストレージ [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) および読み取りアクセス geo ゾーン冗長ストレージ [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) では、セカンダリ リージョンにレプリケートされたデータへの読み取り専用アクセスが常時許可されます。 

#### <a name="blob-storage-and-file-storage"></a>BLOB ストレージとファイル ストレージ

SMB または NFS 4.1 (プレビュー) プロトコルと共に Azure Files を使用してバックアップ ストレージとしてマウントする場合、Azure Files では読み取りアクセス geo 冗長ストレージ (RA-GRS) と読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) がサポートされないことに注意してください。 

また、バックアップ ストレージ要件が 5 TiB を超える場合、Azure Files では、[大きなファイル共有機能](../../../storage/files/storage-files-planning.md)を有効にする必要がありますが、この場合 GRS または GZRS の冗長性はサポートされず、LRS のみがサポートされます。 

NFS 3.0 (プレビュー) プロトコルを使用してマウントされた Azure Blob では、現在、LRS と ZRS の冗長性のみがサポートされています。  

任意の冗長性オプションを使用して構成された Azure Blob は、Blobfuse を使用してマウントできます。

#### <a name="recovery-services-vault"></a>Recovery Services コンテナー 

Recovery Services コンテナーは、時間の経過と共に作成された復旧ポイントを格納する管理エンティティであり、バックアップ関連の操作を実行するためのインターフェイスが用意されています。 たとえば、オンデマンドのバックアップの作成、復元の実行、バックアップ ポリシーの作成などの操作です。
Azure Backup では、コンテナーのストレージが自動的に処理されます。 そのストレージをレプリケートする方法を、作成時に指定する必要があります。 これは、コンテナーで項目が保護された後に変更することはできません。 リージョンの冗長性を確保するには、geo 冗長設定を選択します。

セカンダリに復元する予定の場合は、[Azure ペア リージョン](../../../best-practices-availability-paired-regions.md)によって、[リージョンをまたがる復元](../../../backup/backup-create-rs-vault.md)機能が有効になります。 リージョンをまたがる復元が有効になっている場合、バックアップ ストレージは GRS から読み取りアクセス geo 冗長ストレージ (RA-GRS) に移されます。 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob ストレージは、大量の非構造化データを格納するために使用される、非常にコスト効率に優れ、耐久性が高く、安全なクラウドベースのストレージ サービスであり、Oracle Database バックアップに使用するのに最適なメディアです。 Azure Blob ストレージは、NFS v3.0 プロトコルまたは Blobfuse (Linux FUSE) を使用して、Azure Linux VM にマウントできます。

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) は、Azure Blob ストレージによってサポートされる仮想ファイルシステムを提供するために開発されたオープンソース プロジェクトです。 libfuse オープンソース ライブラリを使用して Linux FUSE カーネル モジュールと通信し、Azure Storage Blob REST API を使用してファイルシステム操作を実装します。 Blobfuse は現在、Ubuntu および Centos/RedHat ディストリビューションで使用できます。 [CSI ドライバー](https://github.com/kubernetes-sigs/blob-csi-driver)を使用して Kubernetes で使用することもできます。 

すべての Azure リージョンで広く普及しており、汎用の v1 および v2 や Azure Data Lake Store Gen2 を含むすべての種類のストレージ アカウントで動作しますが、Blobfuse で提供されるパフォーマンスは、SMB や NFS などの代替プロトコルよりも低いことが明らかになっています。 データベース バックアップ メディアとしての適合性を確保するには、SMB または [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) プロトコルを使用して Azure Blob ストレージをマウントすることをお勧めします。 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v3.0 (プレビュー)

ネットワーク ファイル システム (NFS) v3.0 プロトコルの Azure サポートがプレビュー段階になりました。 [NFS](../../../storage/blobs/network-file-system-protocol-support.md) サポートにより、Windows および Linux クライアントは Blob ストレージ コンテナーを Azure VM にマウントできるようになります。 

NFS 3.0 パブリック プレビューでは、Standard パフォーマンス レベルを持つ GPV2 ストレージ アカウントが次のリージョンでサポートされています。 
- オーストラリア東部
- 韓国中部
- 米国中南部。 

ネットワーク セキュリティを確保するには、NFS マウントに使用されるストレージ アカウントが VNet 内に含まれている必要があります。 Azure Active Directory (AD) セキュリティおよびアクセス制御リスト (ACL) は、NFS 3.0 プロトコルのサポートが有効にされているアカウントではまだサポートされていません。

### <a name="azure-files"></a>Azure Files

[Azure Files](../../../storage/files/storage-files-introduction.md) は、オンプレミスまたはクラウドベースの Windows、Linux、または macOS クライアントにマウントできる、クラウドベースのフル マネージド分散ファイルシステムです。

Azure Files では、サーバー メッセージ ブロック (SMB) プロトコルおよびネットワーク ファイル システム (NFS) プロトコル (プレビュー) を介してアクセスできる、フル マネージドのクロスプラットフォームのファイル共有がクラウド上で提供されます。 現在、Azure Files ではマルチプロトコル アクセスがサポートされていないため、共有は NFS 共有または SMB 共有のどちらかに限られます。 Azure ファイル共有を作成する前に、自分のニーズに最も適したプロトコルを特定することをお勧めします。

Azure ファイル共有は、Recovery Services コンテナーに対する Azure Backup によって保護することもでき、これにより Oracle RMAN バックアップに追加の保護レイヤーが提供されます。

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files NFS v4.1 (プレビュー)

Azure ファイル共有は、ネットワーク ファイル システム (NFS) v4.1 プロトコルを使用して Linux ディストリビューションにマウントできます。 プレビュー段階では、サポートされる機能にはいくつかの制限があり、これらは[こちら](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)に記載されています。 

プレビュー段階では、Azure Files NFS v4.1 はまた、次の[リージョン](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)に制限されています。
- 米国東部 (LRS および ZRS)
- 米国東部 2
- 米国西部 2
- 西ヨーロッパ
- 東南アジア
- 英国南部
- オーストラリア東部

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3.0

Azure ファイル共有は、SMB カーネル クライアントを使用して Linux ディストリビューションにマウントできます。 Linux ディストリビューションで使用できる Common Internet File System (CIFS) プロトコルは、SMB の言語です。 Linux VM に Azure Files SMB をマウントする場合、CIFS タイプのファイルシステムとしてマウントされるため、CIFS パッケージがインストールされている必要があります。 

Azure Files SMB は、すべての Azure リージョンで一般提供されており、NFS v3.0 と v4.1 のプロトコルと同じパフォーマンス特性を示しているため、現時点では、Azure Linux VM へのバックアップ ストレージ メディアを提供するための推奨される方法となっています。  

SMB 2.1 と SMB 3.0 という 2 つのバージョンの SMB がサポートされており、後者は転送中の暗号化をサポートしているため後者が推奨されます。 ただし、Linux カーネルのバージョンによって SMB 2.1 と 3.0 のサポートが異なるため、[こちら](../../../storage/files/storage-how-to-use-files-linux.md)の表を確認してご使用のアプリケーションで SMB 3.0 がサポートされていることを確認する必要があります。 

Azure Files はマルチユーザー ファイル共有サービスとして設計されているため、バックアップ ストレージ メディアとしてより適したものにするために調整する必要があるいくつかの特性があります。 キャッシュをオフにすることと、作成されたファイルのユーザーとグループの ID を設定することをお勧めします。

## <a name="azure-netapp-files"></a>Azure NetApp Files

[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) サービスは、Azure VM の Oracle Database 用の完全なストレージ ソリューションです。 エンタープライズ クラスの高パフォーマンスの従量制課金ファイル ストレージをベースに構築されており、あらゆる種類のワークロードをサポートし、既定で高可用性を実現します。 Oracle Direct NFS (dNFS) ドライバーと共に、Azure NetApp Files によって、Oracle Database に対して高度に最適化されたストレージ層が提供されます。

Azure NetApp Files は、書き込み時のリダイレクト (RoW) メカニズムを使用する基になるストレージ システム上で、効率的なストレージベースのスナップショット コピーを提供します。 スナップショット コピーの取得と復元は非常に高速ですが、これは防御の第一線としてのみ機能し、特定の組織で必要とされる復元操作の大部分を占める場合があります。これは多くの場合、人的エラーからの復旧です。 ただし、スナップショット コピーは完全なバックアップではありません。 バックアップと復元のすべての要件に対応するには、リージョンの障害から保護するために、外部スナップショットのレプリカやその他のバックアップ コピーをリモートの場所に作成する必要があります。 Azure での Oracle Database 用の NetApp Files の使用について詳しくは、こちらの[レポート](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)を参照してください。

## <a name="azure-backup-service"></a>Azure Backup サービス

[Azure Backup](../../../backup/backup-overview.md) は、データをバックアップし、それを Microsoft Azure クラウドから復旧するための、シンプルで安全かつコスト効率の高いソリューションを提供するフル マネージド PaaS です。 Azure Backup は、オンプレミスのクライアント、Azure VM、Azure Files 共有のほか、Azure VM 上の SQL Server、Oracle、MySQL、PostreSQL、および SAP HANA データベースのバックアップと復元を行うことができます。 

Azure Backup では、元のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 バックアップは、復旧ポイントの管理機能が組み込まれた [Recovery Services コンテナー](../../../backup/backup-azure-recovery-services-vault-overview.md)に格納されます。 構成とスケーラビリティは単純で、バックアップは最適化され、必要に応じて簡単に復元することができます。 Azure クラウドの基盤となる機能と無制限のスケールを使用して、高可用性を実現します。その際、保守と監視のオーバーヘッドは発生しません。 Azure Backup では、転送するインバウンドまたはアウトバウンドのデータの量に制限がなく、転送されるデータに料金はかかりません。データは転送中も保存中も保護されます。 

持続性を高めるために、Azure Backup では、バックアップ データの高可用性を維持するための複数の種類のレプリケーションが提供されています。

- ローカル冗長ストレージ [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) では、データセンターのストレージ スケール ユニットにデータが 3 回レプリケートされます (データのコピーが 3 つ作成されます)。
- geo 冗長ストレージ [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) は、既定の推奨レプリケーション オプションです。 GRS では、セカンダリ リージョン (ソース データのプライマリの場所から数百マイル離れた場所) にデータがレプリケートされます。

GRS 冗長性を使用して作成されたコンテナーには[リージョンをまたがる復元](../../../backup/backup-create-rs-vault.md#set-storage-redundancy)機能を構成するオプションが含まれています。これにより、セカンダリの Azure ペア リージョンにデータを復元できます。

Azure Backup サービスでは、Oracle、MySQL、Mongo DB、SAP HANA、PostGreSQL などのさまざまなアプリケーションの Windows および Linux VM のバックアップ中に、アプリケーションの整合性を確保するための[フレームワーク](../../../backup/backup-azure-linux-app-consistent.md)が提供されており、これはアプリケーション整合性スナップショットと呼ばれています。 これには、ディスクのスナップショットを作成する前に (アプリケーションを停止するために) 事前スクリプトを呼び出すことと、アプリケーションを通常モードに戻すためにスナップショットの完了後に事後スクリプト (アプリケーションを停止解除するためのコマンド) を呼び出すことが含まれます。 サンプルの事前スクリプトと事後スクリプトは GitHub に用意されていますが、これらのスクリプトの作成と保守はお客様の責任となります。 Oracle の場合、データベースはオンライン バックアップを許可するためにアーカイブログ モードになっている必要があります。また、適切な database begin および end backup コマンドが事前および事後スクリプトで実行されますが、これはお客様が作成して管理する必要があります。 

Azure Backup は、[拡張された事前スクリプトと事後スクリプトのフレームワーク](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) (現時点ではプレビュー段階) を提供するようになりました。ここで、Azure Backup サービスは、選択したアプリケーションのパッケージ化された事前スクリプトと事後スクリプトを提供します。 Azure Backup ユーザーはアプリケーションに名前を付けるだけで済み、その後 Azure VM バックアップによって、関連する事前スクリプトおよび事後スクリプトが自動的に呼び出されます。 パッケージ化された事前スクリプトと事後スクリプトは Azure Backup チームによって管理されるので、ユーザーに対してこれらのスクリプトのサポート、所有権、および有効性が保証されます。 現時点では、拡張フレームワークでサポートされているアプリケーションは Oracle と MySQL で、今後アプリケーションの種類が追加される予定です。 スナップショットはストレージの完全なコピーであり、増分または書き込み時コピーのスナップショットではないため、データベースの復元元として有効なメディアです。

## <a name="next-steps"></a>次のステップ

- [Oracle Database の作成のクイックスタート](oracle-database-quick-create.md)
- [Azure Files への Oracle Database のバックアップ](oracle-database-backup-azure-storage.md)
- [Azure Backup サービスを使用した Oracle Database のバックアップ](oracle-database-backup-azure-backup.md)


