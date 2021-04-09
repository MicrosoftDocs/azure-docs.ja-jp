---
title: MABS と System Center DPM のサポート マトリックス
description: この記事では、Microsoft Azure Backup Server (MABS) または System Center DPM を使用してオンプレミスおよび Azure VM のリソースをバックアップする場合の、Azure Backup のサポートについてまとめます。
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609755"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure Backup Server または System Center DPM を使用したバックアップのサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用すると、オンプレミスのコンピューターとワークロード、および Azure 仮想マシン (VM) をバックアップできます。 この記事では、Microsoft Azure Backup Server (MABS) または System Center Data Protection Manager (DPM) と Azure Backup を使用してコンピューターをバックアップするためのサポートの設定および制限を要約しています。

## <a name="about-dpmmabs"></a>DPM/MABS について

[System Center DPM](/system-center/dpm/dpm-overview) は、エンタープライズ コンピューターおよびデータのバックアップと復旧を構成、促進、および管理するエンタープライズ ソリューションです。 これは製品の [System Center](https://www.microsoft.com/system-center/pricing) スイートの一部です。

MABS は、オンプレミスの物理サーバー、VM、およびそれらで実行されているアプリをバックアップするために使用できるサーバー製品です。

MABS は System Center DPM に基づいており、同様の機能を提供しますが、次のようないくつかの違いがあります。

- MABS を実行するために System Center ライセンスは必要ありません。
- MABS と DPM の両方に対して、Azure は長期的なバックアップ ストレージを提供します。 さらに、DPM では、テープで長期保存するためのデータのバックアップが可能になります。 MABS では、この機能は提供されません。
- [セカンダリ DPM サーバーを使用してプライマリ DPM サーバーをバックアップすることができます。](/system-center/dpm/back-up-the-dpm-server) セカンダリ サーバーは、プライマリ サーバー データベースと、プライマリ サーバーに格納されているデータ ソース レプリカを保護します。 プライマリ サーバーに障害が発生した場合、セカンダリ サーバーは、プライマリ サーバーが再び使用可能になるまで、プライマリ サーバーによって保護されているワークロードを引き続き保護することができます。  MABS では、この機能は提供されません。

MABS は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57520)からダウンロードします。 これは、オンプレミスまたは Azure VM で実行できます。

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

**シナリオ** | **エージェント** | **場所**
--- | --- | ---
**オンプレミス マシン/ワークロードのバックアップ** | DPM/MABS 保護エージェントは、バックアップするコンピューター上で実行されます。<br/><br/> DPM/MABS サーバー上の MARS エージェント。<br/> この機能を有効にするための、Microsoft Azure Recovery Services エージェントまたは Azure Backup エージェントの最小バージョンは、2.0.8719.0 です。  | DPM/MABS は、オンプレミスで実行する必要があります。

## <a name="supported-deployments"></a>サポートされているデプロイ

DPM/MABS は、次の表に要約されているようにデプロイできます。

**デプロイ** | **サポート** | **詳細**
--- | --- | ---
**オンプレミスでデプロイ** | 物理サーバーですが、物理クラスターにはありません。<br/><br/>Hyper-V VM。 MABS は、スタンドアロンのハイパーバイザーまたはクラスター上のゲスト マシンとしてデプロイできます。 クラスターまたはスタンドアロンのハイパーバイザーのノードにデプロイすることはできません。 Azure Backup Server は、単一目的の専用サーバーで動作するように設計されています。<br/><br/> VMware 環境内の Windows 仮想マシンとして。 | オンプレミスの MABS サーバーでは、Azure ベースのワークロードを保護できません。 <br><br> 詳細については、[保護マトリックス](backup-mabs-protection-matrix.md)に関する記事を参照してください。
**Azure Stack VM としてデプロイ** | MABS のみ | DPM を使用して Azure Stack VM をバックアップすることはできません。
**Azure VM としてデプロイ** | Azure VM とそれらの VM 上で実行されているワークロードを保護します。 | Azure で実行されている DPM/MABS は、オンプレミスのコンピューターをバックアップできません。 Azure IaaS VM で実行されているワークロードのみを保護できます。

## <a name="supported-mabs-and-dpm-operating-systems"></a>サポートされている MABS および DPM オペレーティング システム

Azure Backup は、次のいずれかのオペレーティング システムを実行している DPM/MABS インスタンスをバックアップできます。 オペレーティング システムで最新の Service Pack および更新プログラムが実行されている必要があります。

**シナリオ** | **DPM/MABS**
--- | ---
**Azure VM 上の MABS** |  Windows 2016 Datacenter。<br/><br/> Windows 2019 Datacenter。<br/><br/> マーケットプレースのイメージから始めることをお勧めします。<br/><br/> 4 つのコアと 8 GB の RAM を持つ最小の Standard_A4_v2 です。
**Azure VM 上の DPM** | 更新プログラム 3 以降が適用された System Center 2012 R2。<br/><br/> [System Center](/system-center/dpm/prepare-environment-for-dpm#dpm-server) で必要とされる Windows オペレーティング システム。<br/><br/> マーケットプレースのイメージから始めることをお勧めします。<br/><br/> 4 つのコアと 8 GB の RAM を持つ最小の Standard_A4_v2 です。
**MABS オンプレミス** |  MABS v3 以降: Windows Server 2016 または Windows Server 2019
**DPM オンプレミス** | 物理サーバー/Hyper-V VM: System Center 2012 SP1 以降。<br/><br/> VMware VM: 更新プログラム 5 以降が適用された System Center 2012 R2。

>[!NOTE]
>Azure Backup Server のインストールは、Windows Server Core または Microsoft Hyper-V Server ではサポートされていません。

## <a name="management-support"></a>管理のサポート

**問題点** | **詳細**
--- | ---
**インストール** | DPM/MABS は、単一目的のコンピューターにインストールします。<br/><br/> ドメイン コントローラー、アプリケーション サーバー ロールがインストールされているマシン、Microsoft Exchange Server または System Center Operations Manager を実行しているマシン、またはクラスター ノードには DPM/MABS をインストールしないでください。<br/><br/> [DPM のすべてのシステム要件を確認してください](/system-center/dpm/prepare-environment-for-dpm#dpm-server)。
**[ドメイン]** | DPM/MABS はドメインに参加させる必要があります。 最初に DPM/MABS をインストールし、次にこれらをドメインに参加させます。 デプロイ後に DPM/MABS を新しいドメインに移動することはサポートされていません。
**Storage** | Modern Backup Storage (MBS) は、DPM 2016/MABS v2 以降でサポートされています。 MABS v1 では使用できません。
**MABS のアップグレード** | MABS v3 を直接インストールすることも、MABS v2 から MABS v3 にアップグレードすることもできます。 [詳細については、こちらを参照してください](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**MABS の移動** | MBS を使用している場合、ストレージを保持したまま MABS を新しいサーバーに移動できます。<br/><br/> このサーバーは元のサーバーと同じ名前でなければなりません。 同じ記憶域プールを保持し、同じ MABS データベースを使用してデータ復旧ポイントを格納する場合、この名前は変更できません。<br/><br/> MABS データベースを復元する必要があるため、このデータベースのバックアップが必要です。

>[!NOTE]
>DPM/MABS サーバーの名前変更はサポートされていません。

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

DPM サーバー/MABS サーバーは、次の URL と IP アドレスにアクセスできる必要があります。

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP アドレス
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Azure ExpressRoute のサポート

パブリック ピアリング (古い回線で使用可能) と Microsoft ピアリングを使用して、Azure ExpressRoute 経由でデータをバックアップできます。 プライベート ピアリング経由のバックアップはサポートされていません。

パブリック ピアリングを使用して、次のドメインまたはアドレスへのアクセスを確保します。

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP アドレス
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft ピアリングの使用時には、サービス、リージョン、関連するコミュニティについて以下の値を選択します。

- Azure Active Directory (12076:5060)
- Microsoft Azure リージョン (Recovery Services コンテナーの場所による)
- Azure Storage (Recovery Services コンテナーの場所による)

詳細については、「[ExpressRoute ルーティングの要件](../expressroute/expressroute-routing.md)」を参照してください。

>[!NOTE]
>パブリック ピアリングは、新しい回線では非推奨です。

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup への DPM/MABS 接続

バックアップが正しく機能するには Azure Backup サービスへの接続が必要です。また、Azure サブスクリプションがアクティブである必要があります。 次の表に、これらの 2 つの要件が満たされていない場合の動作を示します。

**MABS から Azure** | **サブスクリプション** | **バックアップ/復元**
--- | --- | ---
接続中 | アクティブ | DPM/MABS ディスクへのバックアップ。<br/><br/> Azure へのバックアップ。<br/><br/> ディスクからの復元。<br/><br/> Azure からの復元。
接続中 | 期限切れ/プロビジョニング解除済み | ディスクまたは Azure へのバックアップなし。<br/><br/> サブスクリプションの有効期限が切れている場合は、ディスクまたは Azure から復元できます。<br/><br/> サブスクリプションが使用停止されている場合は、ディスクまたは Azure から復元できません。 Azure の復旧ポイントが削除されます。
15 日よりも長い期間接続なし | アクティブ | ディスクまたは Azure へのバックアップなし。<br/><br/> ディスクまたは Azure から復元できます。
15 日よりも長い期間接続なし | 期限切れ/プロビジョニング解除済み | ディスクまたは Azure へのバックアップなし。<br/><br/> サブスクリプションの有効期限が切れている場合は、ディスクまたは Azure から復元できます。<br/><br/> サブスクリプションが使用停止されている場合は、ディスクまたは Azure から復元できません。 Azure の復旧ポイントが削除されます。

## <a name="domain-and-domain-trusts-support"></a>ドメインとドメインの信頼のサポート

|要件 |詳細 |
|---------|---------|
|Domain    | DPM/MABS サーバーは、Windows Server 2019、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 ドメインに存在する必要があります。        |
|ドメインの信頼   |  DPM/MABS では、異なるフォレスト間にフォレスト レベルの双方向信頼関係が確立されていれば、フォレストをまたがるデータ保護がサポートされます。   <BR><BR>   DPM/MABS サーバー ドメインとの双方向の信頼関係を持つフォレスト内において、DPM/MABS ではドメインをまたいでサーバーとワークステーションを保護できます。 ワークグループまたは信頼されていないドメイン内のコンピューターを保護するには、[ワークグループと信頼されていないドメイン内のワークロードのバックアップと復元](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains)に関する記事を参照してください。 <br><br> Hyper-V サーバー クラスターをバックアップするには、そのサーバー クラスターが MABS サーバーと同じドメインか、信頼されたドメインまたは子ドメインに配置されている必要があります。 1 台のサーバーの場合は NTLM または証明書の認証を、クラスターの場合は証明書の認証のみを使用することにより、信頼されていないドメインにあるサーバーおよびクラスターまたはワークロードをバックアップできます。  |

## <a name="dpmmabs-storage-support"></a>DPM/MABS ストレージのサポート

DPM/MABS にバックアップされたデータは、ローカル ディスク ストレージに格納されます。

USB ドライブまたはリムーバブル ドライブはサポートされていません。

DPM/MABS ボリュームで NTFS 圧縮はサポートされていません。

BitLocker は、記憶域プールにディスクを追加した後でのみ有効にすることができます。 BitLocker を追加する前に有効にしないでください。

DPM 記憶域プールでは、ネットワーク接続記憶域 (NAS) の使用はサポートされていません。

**Storage** | **詳細**
--- | ---
**MBS** | Modern Backup Storage (MBS) は、DPM 2016/MABS v2 以降でサポートされています。 MABS v1 では使用できません。
**Azure VM 上の MABS ストレージ** | データは、DPM/MABS VM に接続され、DPM/MABS で管理されている Azure ディスクに格納されます。 DPM/MABS 記憶域プールに使用できるディスクの数は、VM のサイズによって制限されます。<br/><br/> A2 VM: 4 ディスク、A3 VM: 8 ディスク、A4 VM: 16 ディスク (各ディスクの最大サイズは 1 TB)。 これにより、使用可能なバックアップ ストレージ プールの合計が決定されます。<br/><br/> バックアップできるデータの量は、接続されたディスクの数とサイズによって決まります。
**Azure VM 上の MABS のデータ保持期間** | データは DPM/MABS Azure ディスクに 1 日保持し、それより長期間の場合は DPM/MABS からコンテナーにバックアップすることをお勧めします。 これにより、Azure Backup にオフロードすることで大量のデータを保護できます。

### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)

DPM 2016/MABS v2 (Windows Server 2016 上で実行されている) 以降は、Modern Backup Storage (MBS) を利用できます。

- MBS バックアップは、Resilient File System (ReFS) ディスクに格納されます。
- MBS は、より高速なバックアップのため、および記憶域スペースのより効率的な使用のために ReFS ブロックの複製を使用します。
- ローカルの DPM/MABS 記憶域プールにボリュームを追加する場合は、それらをドライブ文字を使用して構成します。 その後、別のボリュームでワークロード ストレージを構成できます。
- データを DPM/MABS にバックアップするために保護グループを作成する場合、使用するドライブを選択します。 たとえば、SQL やその他の高 IOPS ワークロードのバックアップを高パフォーマンスのドライブに格納し、バックアップ頻度の低いワークロードをより低パフォーマンスのドライブに格納することができます。

## <a name="supported-backups-to-mabs"></a>サポートされる MABS へのバックアップ

Azure Backup Server で保護できるさまざまなサーバーとワークロードについては、[Azure Backup Server Protection Matrix](./backup-mabs-protection-matrix.md#protection-support-matrix) を参照してください。

## <a name="supported-backups-to-dpm"></a>サポートされる DPM へのバックアップ

Data Protection Manager で保護できる各種サーバーとワークロードの詳細については、[「DPM でバックアップできるものは何か」を参照してください。](/system-center/dpm/dpm-protection-matrix)

- DPM/MABS によってバックアップされるクラスター化されたワークロードは DPM/MABS と同じドメインか、または子/信頼できるドメインに存在する必要があります。
- NTLM/証明書認証を使用して、信頼されていないドメインまたはワークグループのデータをバックアップできます。

## <a name="deduplicated-volumes-support"></a>重複除去されたボリュームのサポート

>[!NOTE]
> MABS の重複除去のサポートは、オペレーティング システムのサポートによって異なります。

### <a name="for-ntfs-volumes"></a>NTFS ボリュームの場合

| 保護されたサーバーのオペレーティング システム  | MABS サーバーのオペレーティング システム  | MABS のバージョン  | 重複除去のサポート |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| Windows Server 2019                       | Windows Server 2019                  | MABS v3            | Y                    |
| Windows Server 2016                       | Windows Server 2019                  | MABS v3            | Y*                   |
| Windows Server 2012 R2                    | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2012                       | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2019                       | Windows Server 2016                  | MABS v3            | Y**                  |
| Windows Server 2016                       | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012 R2                    | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012                       | Windows Server 2016                  | MABS v3            | Y                    |

- \* WS 2019 上で MABS v3 が実行された WS 2016 NTFS 重複除去ボリュームを保護する場合、回復に影響が出る可能性があります。 非重複除去の方法で回復を実行するための修正プログラムがあります。 MABS v3 UR1 でこの修正プログラムが必要な場合は、MABS サポートにお問い合わせください。
- \** WS 2016 上の MABS v3 を使用して WS 2019 NTFS 重複除去ボリュームを保護する場合、バックアップと復元は非重複除去になります。 これは、元の NTFS 重複除去ボリュームよりも多くの MABS サーバー上の領域がバックアップに要することを意味します。

**問題**: 保護されたサーバーのオペレーティング システムを Windows Server 2016 から Windows Server 2019 にアップグレードすると、重複除去ロジックの変更により、NTFS 重複除去ボリュームのバックアップが影響を受けます。

**回避策**: MABS v3 UR1 にこの修正プログラムが必要な場合は、MABS サポートにお問い合わせください。

### <a name="for-refs-volumes"></a>ReFS ボリュームの場合

>[!NOTE]
> 重複除去 ReFS ボリュームのバックアップに関していくつかの問題を特定しました。 これらの修正に取り組んでおり、修正プログラムが利用可能になり次第、このセクションを更新します。 それまでは、重複除去された ReFS ボリュームのバックアップ サポートを MABSv3 から削除します。
>
> MABS v3 UR1 以降では、通常の ReFS ボリュームの保護と回復が引き続きサポートされます。

## <a name="next-steps"></a>次のステップ

- MABS アーキテクチャーについての[詳細を確認](backup-architecture.md#architecture-back-up-to-dpmmabs)してください。
- MARS エージェントに対して何がサポートされているか[確認](backup-support-matrix-mars-agent.md)してください。
- MABS サーバーを[設定](backup-azure-microsoft-azure-backup.md)します。
- [DPM を設定](/system-center/dpm/install-dpm)します。
