---
title: Microsoft Azure Backup Server と System Center DPM のサポート マトリックス
description: この記事では、Microsoft Azure Backup Server または System Center DPM を使用してオンプレミスおよび Azure VM のリソースをバックアップする場合の Azure Backup サポートについてまとめます。
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 777d2286ed24123011e06edfb468d0f2db61715f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464874"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure Backup Server または System Center DPM を使用したバックアップのサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用すると、オンプレミスのコンピューターとワークロード、および Azure 仮想マシン (VM) をバックアップできます。 この記事では、Microsoft Azure Backup Server (MABS) または System Center Data Protection Manager (DPM) と Azure Backup を使用してコンピューターをバックアップするためのサポートの設定および制限を要約しています。

## <a name="about-dpmmabs"></a>DPM/MABS について

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) は、エンタープライズ コンピューターおよびデータのバックアップと復旧を構成、促進、および管理するエンタープライズ ソリューションです。 これは製品の [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) スイートの一部です。

MABS は、オンプレミスの物理サーバー、VM、およびそれらで実行されているアプリをバックアップするために使用できるサーバー製品です。

MABS は System Center DPM に基づいており、同様の機能を提供しますが、次のようないくつかの違いがあります。
- MABS を実行するために System Center ライセンスは必要ありません。
- MABS と DPM の両方に対して、Azure は長期的なバックアップ ストレージを提供します。 さらに、DPM では、テープで長期保存するためのデータのバックアップが可能になります。 MABS では、この機能は提供されません。

MABS は、[Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=57520)からダウンロードします。 これは、オンプレミスまたは Azure VM で実行できます。

DPM および MABS は、さまざまなアプリ、およびサーバーとクライアントのオペレーティング システムのバックアップをサポートします。 次のように、複数のバックアップ シナリオが提供されています。

- システム状態バックアップまたはベア メタル バックアップを使用して、マシン レベルでバックアップできます。
- 特定のボリューム、共有、フォルダー、およびファイルをバックアップできます。
- 最適化されたアプリ対応の設定を使用して、特定のアプリをバックアップできます。

## <a name="dpmmabs-backup"></a>DPM/MABS でのバックアップ

DPM/MABS と Azure Backup を使用したバックアップは、次のように動作します。

1. DPM/MABS 保護エージェントは、バックアップされる各コンピューターにインストールされます。
1. コンピューターとアプリは、DPM/MABS 上のローカル ストレージにバックアップされます。
1. Microsoft Azure Recovery Services (MARS) エージェントが DPM サーバー/MABS にインストールされます。
1. MARS エージェントは、Azure Backup を使用して、DPM/MABS ディスクを Azure の Backup Recovery Services コンテナーにバックアップします。

詳細:

- MABS アーキテクチャーについての[詳細を確認](backup-architecture.md#architecture-back-up-to-dpmmabs)してください。
- MARS エージェントに対して[何がサポートされているかを確認](backup-support-matrix-mars-agent.md)してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

**シナリオ** | **エージェント** | **Location**
--- | --- | ---
**オンプレミス マシン/ワークロードのバックアップ** | DPM/MABS 保護エージェントは、バックアップするコンピューター上で実行されます。<br/><br/> DPM/MABS サーバー上の MARS エージェント。<br/> この機能を有効にするための、Microsoft Azure Recovery Services エージェントまたは Azure Backup エージェントの最小バージョンは、2.0.8719.0 です。  | DPM/MABS は、オンプレミスで実行する必要があります。
**Azure VM/ワークロードのバックアップ** | 保護されたコンピューター上の DPM/MABS 保護エージェント。<br/><br/> DPM/MABS サーバー上の MARS エージェント。 | DPM/MABS は、Azure VM で実行する必要があります。

## <a name="supported-deployments"></a>サポートされているデプロイ

DPM/MABS は、次の表に要約されているようにデプロイできます。

**Deployment** | **サポート** | **詳細**
--- | --- | ---
**オンプレミスでデプロイ** | 物理サーバー<br/><br/>Hyper-V VM<br/><br/> VMware VM | DPM/MABS は、VMware VM としてインストールされている場合、VMware VM とそれらの VM 上で実行されているワークロードのみをバックアップします。
**Azure Stack VM としてデプロイ** | MABS のみ | DPM を使用して Azure Stack VM をバックアップすることはできません。
**Azure VM としてデプロイ** | Azure VM とそれらの VM 上で実行されているワークロードを保護します。 | Azure で実行されている DPM/MABS は、オンプレミスのコンピューターをバックアップできません。


## <a name="supported-mabs-and-dpm-operating-systems"></a>サポートされている MABS および DPM オペレーティング システム

Azure Backup は、次のいずれかのオペレーティング システムを実行している DPM/MABS インスタンスをバックアップできます。 オペレーティング システムで最新の Service Pack および更新プログラムが実行されている必要があります。

**シナリオ** | **DPM/MABS**
--- | ---
**Azure VM 上の MABS** | Windows Server 2012 R2 です。<br/><br/> Windows 2016 Datacenter。<br/><br/> Windows 2019 Datacenter。<br/><br/> マーケットプレースのイメージから始めることをお勧めします。<br/><br/> 2 つのコアと 3.5 GB の RAM を備えた最小の A2 Standard。
**Azure VM 上の DPM** | 更新プログラム 3 以降が適用された System Center 2012 R2。<br/><br/> [System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) で必要とされる Windows オペレーティング システム。<br/><br/> マーケットプレースのイメージから始めることをお勧めします。<br/><br/> 2 つのコアと 3.5 GB の RAM を備えた最小の A2 Standard。
**MABS オンプレミス** | サポートされている 64 ビット オペレーティング システム:<br/><br/> MABS v3 以降: Windows Server 2019 (Standard、Datacenter、Essentials)。 <br/><br/> MABS v2 以降: Windows Server 2016 (Standard、Datacenter、Essentials)。<br/><br/> MABS のすべてのバージョン:  Windows Server 2012 R2 です。<br/><br/>MABS のすべてのバージョン: Windows Storage Server 2012 R2。
**DPM オンプレミス** | 物理サーバー/Hyper-V VM: System Center 2012 SP1 以降。<br/><br/> VMware VM: 更新プログラム 5 以降が適用された System Center 2012 R2。



## <a name="management-support"></a>管理のサポート

**問題** | **詳細**
--- | ---
**インストール** | DPM/MABS は、単一目的のコンピューターにインストールします。<br/><br/> ドメイン コントローラー、アプリケーション サーバー ロールがインストールされているコンピューター、Microsoft Exchange Server または System Center Operations Manager を実行しているコンピューター、クラスター ノードには DPM/MABS をインストールしないでください。<br/><br/> [DPM のすべてのシステム要件を確認してください](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)。
**ドメイン** | DPM/MABS はドメインに参加させる必要があります。 最初に DPM/MABS をインストールし、次にこれらをドメインに参加させます。 デプロイ後に DPM/MABS を新しいドメインに移動することはサポートされていません。
**Storage** | Modern Backup Storage (MBS) は、DPM 2016/MABS v2 以降でサポートされています。 MABS v1 では使用できません。
**MABS のアップグレード** | MABS v3 を直接インストールすることも、MABS v2 から MABS v3 にアップグレードすることもできます。 [詳細情報](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**MABS の移動** | MBS を使用している場合、ストレージを保持したまま MABS を新しいサーバーに移動できます。<br/><br/> このサーバーは元のサーバーと同じ名前でなければなりません。 同じ記憶域プールを保持し、同じ MABS データベースを使用してデータ復旧ポイントを格納する場合、この名前は変更できません。<br/><br/> MABS データベースを復元する必要があるため、このデータベースのバックアップが必要です。


## <a name="mabs-support-on-azure-stack"></a>Azure Stack での MABS のサポート

1 つの場所から Azure Stack VM とワークロードのバックアップを管理できるようにするために、Azure Stack VM に MABS をデプロイできます。

**コンポーネント** | **詳細**
--- | ---
**Azure Stack VM 上の MABS** | 少なくともサイズ A2。 Azure Marketplace の Windows Server 2012 R2 または Windows Server 2016 のイメージから始めることをお勧めします。<br/><br/> MABS VM には他に何もインストールしないでください。
**MABS のストレージ** | MABS VM には個別のストレージ アカウントを使用します。 MABS 上で実行されている MARS エージェントには、キャッシュの場所のためと、クラウドから復元されたデータを保持するための一時ストレージが必要です。
**MABS の記憶域プール** | MABS 記憶域プールのサイズは、MABS VM に接続されているディスクの数とサイズによって決定されます。 Azure Stack の VM サイズごとに、ディスクの最大数が設定されています。 たとえば、A2 は 4 ディスクです。
**MABS の保持期間** | バックアップされたデータを 5 日を超えてローカルの MABS ディスクに保持しないでください。
**MABS のスケールアップ** | デプロイをスケールアップするために、MABS VM のサイズを増やすことができます。 たとえば、A から D シリーズに変更できます。<br/><br/> また、Azure へのバックアップでデータを定期的にオフロードするようにもできます。 必要に応じて、追加の MABS サーバーをデプロイできます。
**MABS 上の .NET Framework** | MABS VM には .NET Framework 3.3 SP1 以降がインストールされている必要があります。
**MABS のドメイン** | MABS VM はドメインに参加させる必要があります。 管理特権を持つドメイン ユーザーが MABS を VM にインストールする必要があります。
**Azure Stack VM データのバックアップ** | ファイル、フォルダー、およびアプリをバックアップできます。
**サポートされているバックアップ** | バックアップする VM に対して、次のオペレーティング システムがサポートされています。<br/><br/> Windows Server 半期チャネル (Datacenter、Enterprise、Standard)<br/><br/> Windows Server 2016、Windows Server 2012 R2、Windows Server 2008 R2
**Azure Stack VM の SQL Server サポート** | SQL Server 2016、SQL Server 2014、SQL Server 2012 SP1 をバックアップします。<br/><br/> データベースをバックアップおよび復旧します。
**Azure Stack VM の SharePoint サポート** | SharePoint 2016、SharePoint 2013、SharePoint 2010。<br/><br/> ファーム、データベース、フロント エンド、および Web サーバーをバックアップおよび復旧します。
**バックアップされた VM のネットワーク要件** | Azure Stack ワークロード内のすべての VM が同じ仮想ネットワークに属し、かつ同じサブスクリプションに属している必要があります。

## <a name="dpmmabs-networking-support"></a>DPM/MABS ネットワークのサポート

### <a name="url-access"></a>URL アクセス

DPM サーバー/MABS は、次の URL にアクセスできる必要があります。

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup への DPM/MABS 接続

バックアップが正しく機能するには Azure Backup サービスへの接続が必要です。また、Azure サブスクリプションがアクティブである必要があります。 次の表に、これらの 2 つの要件が満たされていない場合の動作を示します。

**MABS から Azure** | **サブスクリプション** | **バックアップ/復元**
--- | --- | ---
接続中 | アクティブ | DPM/MABS ディスクへのバックアップ。<br/><br/> Azure へのバックアップ。<br/><br/> ディスクからの復元。<br/><br/> Azure からの復元。
接続中 | 期限切れ/プロビジョニング解除済み | ディスクまたは Azure へのバックアップなし。<br/><br/> サブスクリプションの有効期限が切れている場合は、ディスクまたは Azure から復元できます。<br/><br/> サブスクリプションが使用停止されている場合は、ディスクまたは Azure から復元できません。 Azure の復旧ポイントが削除されます。
15 日よりも長い期間接続なし | アクティブ | ディスクまたは Azure へのバックアップなし。<br/><br/> ディスクまたは Azure から復元できます。
15 日よりも長い期間接続なし | 期限切れ/プロビジョニング解除済み | ディスクまたは Azure へのバックアップなし。<br/><br/> サブスクリプションの有効期限が切れている場合は、ディスクまたは Azure から復元できます。<br/><br/> サブスクリプションが使用停止されている場合は、ディスクまたは Azure から復元できません。 Azure の復旧ポイントが削除されます。

## <a name="dpmmabs-storage-support"></a>DPM/MABS ストレージのサポート

DPM/MABS にバックアップされたデータは、ローカル ディスク ストレージに格納されます。

**Storage** | **詳細**
--- | ---
**MBS** | Modern Backup Storage (MBS) は、DPM 2016/MABS v2 以降でサポートされています。 MABS v1 では使用できません。
**Azure VM 上の MABS ストレージ** | データは、DPM/MABS VM に接続され、DPM/MABS で管理されている Azure ディスクに格納されます。 DPM/MABS 記憶域プールに使用できるディスクの数は、VM のサイズによって制限されます。<br/><br/> A2 VM: 4 ディスク、A3 VM: 8 ディスク、A4 VM: 16 ディスク (各ディスクの最大サイズは 1 TB)。 これにより、使用可能な合計のバックアップ記憶域プールが決定されます。<br/><br/> バックアップできるデータの量は、接続されたディスクの数とサイズによって決まります。
**Azure VM 上の MABS のデータ保持期間** | データは DPM/MABS Azure ディスクに 1 日保持し、それより長期間の場合は DPM/MABS からコンテナーにバックアップすることをお勧めします。 そのため、Azure Backup にデータをオフロードすることで大量のデータを保護できます。


### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)
DPM 2016/MABS v2 (Windows Server 2016 上で実行されている) 以降は、Modern Backup Storage (MBS) を利用できます。

- MBS バックアップは、Resilient File System (ReFS) ディスクに格納されます。
- MBS は、より高速なバックアップのため、および記憶域スペースのより効率的な使用のために ReFS ブロックの複製を使用します。
- ローカルの DPM/MABS 記憶域プールにボリュームを追加する場合は、それらをドライブ文字を使用して構成します。 その後、別のボリュームでワークロード ストレージを構成できます。
- データを DPM/MABS にバックアップするために保護グループを作成する場合、使用するドライブを選択します。 たとえば、SQL やその他の高 IOPS ワークロードのバックアップを高パフォーマンスのドライブに格納し、バックアップ頻度の低いワークロードをより低パフォーマンスのドライブに格納することができます。


## <a name="supported-backups-to-mabs"></a>サポートされる MABS へのバックアップ

次の表に、オンプレミス マシンと Azure VM から MABS に何をバックアップできるかについてまとめます。


**Backup** | **バージョン** | **MABS** | **詳細** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 ビット) | MABS v3、v2 | オンプレミス。 | ボリューム/共有/フォルダー/ファイル。<br/><br/> 重複除去されたボリュームはサポートされています。<br/><br/> ボリュームは少なくとも 1 GB の NTFS でなければなりません。 |
**Windows Server 2016 (Datacenter、Standard、Not Nano)**<br/><br/> 64/32 ビット | MABS v3、v2 | オンプレミス/Azure VM。| ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> 重複除去されたボリュームはサポートされています。 |
**Windows Server 2012 R2 (Datacenter および Standard)**<br/><br/> 64/32 ビット | MABS v3、v2 | オンプレミス/Azure VM。 | **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> **Azure VMprotection**: ボリューム/共有/フォルダー/ファイル。<br/><br/> 重複除去されたボリュームはサポートされています。 |
**Windows Server 2012 SP1 (Datacenter および Standard)**<br/><br/> 64/32 ビット | MABS v3、v2 <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) がインストールされている必要があります。 | オンプレミス/Azure VM。 | **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> **Azure VM の保護**: ボリューム/共有/フォルダー/ファイル。<br/><br/> 重複除去されたボリュームはサポートされています。 |
**Windows 2008 R2 SP1 (Standard および Enterprise)**<br/><br/> 64/32 ビット | MABS v3、v2 でサポートされています。<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) がインストールされている必要があります。 | オンプレミス/Azure VM。 |   **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> **Azure VM の保護**: ボリューム/共有/フォルダー/ファイル。<br/><br/> 重複除去されたボリュームはサポートされています。 |
**Windows 2008 R2 (Standard および Enterprise)**<br/><br/> 64/32 ビット | MABS v2/v3 では、OS で SP1 が実行されている必要があります。 | オンプレミス/Azure VM。 | **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> **Azure VM の保護**: ボリューム/共有/フォルダー/ファイル。<br/><br/> 重複除去されたボリュームはサポートされています。 |
**Windows Server 2008 SP2**<br/><br/> 64/32 ビット | MABS v2、v3 | MABS が VMware VM としてデプロイされる場合、MABS v2、v3 がサポートされます。<br/><br/> Azure VM で実行されている MABS ではサポートされません。 | ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル |
**Windows Storage Server 2008** | MABS v2、v3 | オンプレミスの物理サーバー/Hyper-V VM としての MABS。 <br/><br/> Azure VM で実行されている MABS ではサポートされません。 | ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル
**SQL Server 2017** | MABS v3 | オンプレミス/Azure VM。| SQL Server データベースをバックアップします。<br/><br/> SQL Server クラスターのバックアップがサポートされます。<br/><br/>CSV へのデータベースの格納はサポートされません。 |
**SQL Server 2016/2016 SP1** | MABS v3、v2 | オンプレミス/Azure VM。| SQL Server データベースをバックアップします。<br/><br/> SQL Server クラスターのバックアップがサポートされます。<br/><br/>CSV へのデータベースの格納はサポートされません。 |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3、v2 | オンプレミス/Azure VM。| SQL Server データベースをバックアップします。<br/><br/> SQL Server クラスターのバックアップがサポートされます。<br/><br/>CSV へのデータベースの格納はサポートされません。 |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3、v2 | オンプレミス。 | スタンドアロンの Exchange サーバー、DAG の下のデータベースをバックアップします。<br/><br/> メールボックス、および DAG の下のメールボックス データベースを復旧します。<br/><br/> ReFS はサポートされません。<br/><br/> 共有されていないディスク クラスターをバックアップします。<br/><br/> 継続的レプリケーション用に構成された Exchange Server をバックアップします。 |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3、v2 | オンプレミス/Azure VM。 | ファーム、フロントエンド Web サーバーをバックアップします。<br/><br/> ファーム、データベース、Web アプリ、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバーを復旧します。<br/><br/> コンテンツ データベースに対して SQL Server AlwaysOn を使用してファームをバックアップすることはできません。 |
**Windows Server 2016 上の Hyper-V**<br/><br/> **Windows Server 2008 R2 (SP1)** | MABS v3、v2 | オンプレミス。 | **Hyper-V ホスト上の MABS エージェント**: VM 全体とホスト データ ファイルをバックアップします。 ローカル ストレージを持つ VM、CSV ストレージを持つクラスター内の VM、SMB ファイル サーバー ストレージを持つ VM をバックアップします。<br/><br/> **ゲスト VM 上の MABS エージェント**: VM で実行されているワークロードをバックアップします。 CSV。<br/><br/> **復旧**: VM、および VHD/ボリューム/フォルダー/ファイルの項目レベルの復旧。<br/><br/> **Linux VM**:Hyper-V が Windows Server 2012 R2 以降で実行されている場合にバックアップします。 Linux VM の復旧は、マシン全体に対してです。 |
**VMware VM: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 | オンプレミス。 | CSV、NFS、および SAN ストレージ上の VMware VM をバックアップします。<br/><br/> VM 全体を復旧します。<br/><br/> Windows/Linux のバックアップ。<br/><br/> Windows VM のフォルダー/ファイルの項目レベルの復旧のみ。<br/><br/> VMware vApps はサポートされません。<br/><br/> Linux VM の復旧は、マシン全体に対してです。 |



## <a name="supported-backups-to-dpm"></a>サポートされる DPM へのバックアップ

次の表に、オンプレミス マシンと Azure VM から DPM に何をバックアップできるかについてまとめます。



**Backup** | **DPM** | **詳細**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 ビット) | オンプレミスのみ。<br/><br/> DPM 2012 R2 を使用した Windows 10 のバックアップでは、[更新プログラム 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) をインストールすることをお勧めします。 | ボリューム/共有/フォルダー/ファイル。<br/><br/> 重複除去されたボリュームはサポートされています。<br/><br/> ボリュームは少なくとも 1 GB の NTFS でなければなりません。
**Windows Server 2016 (Datacenter、Standard、Not Nano)**<br/><br/> 64/32 ビット | オンプレミス/Azure VM。<br/><br/> DPM 2016 のみ。| ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> 重複除去されたボリュームはサポートされています。
**Windows Server 2012 R2 (Datacenter および Standard)**<br/><br/> 64/32 ビット | オンプレミス/Azure VM。 | **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> **Azure VM の保護**: ボリューム/共有/フォルダー/ファイル。<br/><br/> DPM 2012 R2 以降でサポートされる重複除去されたボリューム。
**Windows Server 2012 SP1 (Datacenter および Standard)**<br/><br/> 64/32 ビット | オンプレミス/Azure VM。 | **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> **Azure VM の保護**: ボリューム/共有/フォルダー/ファイル。<br/><br/> DPM 2012 R2 以降でサポートされる重複除去されたボリューム。
**Windows 2008 R2 SP1 (Standard および Enterprise)**<br/><br/> 64/32 ビット | オンプレミス/Azure VM。<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) がインストールされている必要があります。 |   **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル<br/><br/> **Azure VM の保護**: ボリューム/共有/フォルダー/ファイル。
**Windows 2008 R2 (Standard および Enterprise)**<br/><br/> 64/32 ビット | オンプレミス。<br/><br/> DPM は VMware VM としてインストールできません。<br/><br/> Azure VM で実行されている DPM はサポートされません。 | **オンプレミスの保護**: ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル
**Windows Server 2008 SP2**<br/><br/> 64/32 ビット | オンプレミスのみ。<br/><br/> VMware VM として実行されている DPM はサポートされません。 物理サーバーまたは Hyper-V VM としての実行はサポートされません。 | ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル
**Windows Storage Server 2008** | 物理サーバーまたは Hyper-V VM として実行されている DPM オンプレミス。 | ボリューム/共有/フォルダー/ファイル/システム状態/ベア メタル
**SQL Server 2017** | DPM SAC、更新プログラム ロールアップ 5 以降を実行している DPM 2016。<br/><br/> オンプレミス/Azure VM。| SQL Server データベースをバックアップします。<br/><br/> SQL Server クラスターのバックアップがサポートされます。<br/><br/>CSV へのデータベースの格納はサポートされません。
**SQL Server 2016 SP1** | DPM 2012 R2 ではサポートされません。DPM SAC、および更新プログラム ロールアップ 4 以降を実行している DPM 2016 ではサポートされます。<br/><br/> オンプレミス/Azure VM。| SQL Server データベースをバックアップします。<br/><br/> SQL Server クラスターのバックアップがサポートされます。<br/><br/>CSV へのデータベースの格納はサポートされません。
**SQL Server 2016** | DPM 2012 R2 ではサポートされません。 DPM SAC、更新プログラム ロールアップ 2 以降の DPM 2016 でサポートされます。<br/><br/> オンプレミス/Azure VM。| SQL Server データベースをバックアップします。<br/><br/> SQL Server クラスターのバックアップがサポートされます。<br/><br/>CSV へのデータベースの格納はサポートされません。
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | 更新プログラム ロールアップ 4 以降を実行している DPM 2012 R2 を備えた SQL Server 2014。<br/><br/> オンプレミス/Azure VM。| SQL Server データベースをバックアップします。<br/><br/> SQL Server クラスターのバックアップがサポートされます。<br/><br/>CSV へのデータベースの格納はサポートされません。
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Exchange 2016 の場合、DPM 2012 R2 には、更新プログラム ロールアップ 9 以降が必要です。<br/><br/> オンプレミス | スタンドアロンの Exchange サーバー、DAG の下のデータベースをバックアップします。<br/><br/> メールボックス、および DAG の下のメールボックス データベースを復旧します。<br/><br/> ReFS はサポートされません。<br/><br/> 共有されていないディスク クラスターをバックアップします。<br/><br/> 継続的レプリケーション用に構成された Exchange Server をバックアップします。
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | DPM 2016 以降での SharePoint 2016。<br/><br/>オンプレミス/Azure VM。 | ファーム、フロントエンド Web サーバーをバックアップします。<br/><br/> ファーム、データベース、Web アプリ、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバーを復旧します。<br/><br/> コンテンツ データベースに対して SQL Server AlwaysOn を使用してファームをバックアップすることはできません。
**Windows Server 2016 上の Hyper-V**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (SP1)** | DPM 2016 以降でサポートされる 2016 上の Hyper-V。<br/><br/> オンプレミス。 | **Hyper-V ホスト上の MABS エージェント**: VM 全体とホスト データ ファイルをバックアップします。 ローカル ストレージを持つ VM、CSV ストレージを持つクラスター内の VM、SMB ファイル サーバー ストレージを持つ VM をバックアップします。<br/><br/> **ゲスト VM 上の MABS エージェント**: VM で実行されているワークロードをバックアップします。 CSV。<br/><br/> **復旧**: VM、および VHD/ボリューム/フォルダー/ファイルの項目レベルの復旧。<br/><br/> **Linux VM**:Hyper-V が Windows Server 2012 R2 以降で実行されている場合にバックアップします。 Linux VM の復旧は、マシン全体に対してです。
**VMware VM: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 <br/><br/> DPM 2012 R2 には、System Center の更新プログラム ロールアップ 1 が必要です。 <br/><br/>オンプレミス。 | CSV、NFS、および SAN ストレージ上の VMware VM をバックアップします。<br/><br/> VM 全体を復旧します。<br/><br/> Windows/Linux のバックアップ。<br/><br/> Windows VM のフォルダー/ファイルの項目レベルの復旧のみ。<br/><br/> VMware vApps はサポートされません。<br/><br/> Linux VM の復旧は、マシン全体に対してです。


- DPM/MABS によってバックアップされるクラスター化されたワークロードは DPM/MABS と同じドメインか、または子/信頼できるドメインに存在する必要があります。
- NTLM/証明書認証を使用して、信頼されていないドメインまたはワークグループのデータをバックアップできます。



## <a name="next-steps"></a>次の手順

- MABS アーキテクチャーについての[詳細を確認](backup-architecture.md#architecture-back-up-to-dpmmabs)してください。
- MARS エージェントに対して何がサポートされているか[確認](backup-support-matrix-mars-agent.md)してください。
- MABS サーバーを[設定](backup-azure-microsoft-azure-backup.md)します。
- [DPM を設定](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)します。
