---
title: Azure NetApp Files についてよく寄せられる質問 | Microsoft Docs
description: ネットワーク、セキュリティ、パフォーマンス、容量管理、データ移行/保護など、Azure NetApp Files についてよく寄せられる質問を確認します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: b-juche
ms.openlocfilehash: 330131ea7e9a364a31d25a6f3f0a75b1adbeb27a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799889"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp Files についての FAQ

この記事では、Azure NetApp Files についてよく寄せられる質問とその回答を紹介します。 

## <a name="networking-faqs"></a>ネットワークに関する FAQ

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS データ パスはインターネットを経由しますか?  

いいえ。 NFS データ パスはインターネットを経由しません。 Azure NetApp Files は、サービスが使用可能な Azure Virtual Network (VNet) にデプロイされている Azure のネイティブ サービスです。 Azure NetApp Files は、委任されたサブネットを使用し、VNet 上に直接ネットワーク インターフェイスをプロビジョニングします。 

詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](./azure-netapp-files-network-topologies.md)」を参照してください。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>既に作成済みの VNet を Azure NetApp Files サービスに接続できますか?

はい。作成済みの VNet をサービスに接続することができます。 

詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](./azure-netapp-files-network-topologies.md)」を参照してください。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 名を使用して Azure NetApp Files の NFS ボリュームをマウントできますか?

はい。必要な DNS エントリを作成する場合は、できます。 Azure NetApp Files には、プロビジョニングされたボリューム用の サービス IP が用意されています。 

> [!NOTE] 
> Azure NetApp Files は、必要に応じてサービスの追加の IP をデプロイできます。  DNS エントリの定期的な更新が必要になる場合があります。

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Azure NetApp Files ボリュームの独自の IP アドレスを設定または選択できますか?  

いいえ。 Azure NetApp Files ボリュームへの IP 割り当ては動的です。 静的 IP の割り当てはサポートされていません。 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files はデュアル スタック (IPv4 および IPv6) VNet をサポートしていますか?

いいえ。Azure NetApp Files は現時点ではデュアル スタック (IPv4 および IPv6) VNet をサポートしていません。  
 
## <a name="security-faqs"></a>セキュリティに関する FAQ

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM とストレージ間のネットワーク トラフィックは暗号化できますか?

NFSv4.1 クライアントと Azure NetApp Files ボリューム間のデータ トラフィックは、AES-256 暗号化を使用した Kerberos を使用して暗号化できます。 詳細については、「[Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)」を参照してください。   

NFSv3 または SMB3 クライアントから Azure NetApp Files ボリュームへのデータ トラフィックは暗号化されません。 ただし、(NFS または SMB クライアントを実行している) Azure VM から Azure NetApp Files へのトラフィックは、Azure VM 間のトラフィックと同様に安全です。 このトラフィックは、Azure データセンター ネットワークに対してローカルです。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>ストレージは保存時に暗号化できますか?

Azure NetApp Files のすべてのボリュームは、FIPS 140-2 標準を使用して暗号化されます。 すべてのキーは Azure NetApp Files サービスによって管理されています。 

### <a name="how-are-encryption-keys-managed"></a>暗号化キーはどのように管理されるのですか? 

Azure NetApp Files のキー管理は、サービスによって処理されます。 ボリュームごとに一意の XTS-AES-256 データ暗号化キーが生成されます。 すべてのボリューム キーは、暗号化キーの階層を使用して暗号化され、保護されます。 これらの暗号化キーは決して表示されず、また、暗号化されていない形式でレポートされることもありません。 ボリュームが削除されると暗号化キーは直ちに削除されます。

Azure Dedicated HSM を使用したカスタマー マネージド キー (Bring Your Own Key) のサポートは、制御ベースで、米国東部、米国中南部、米国西部 2、および US Gov バージニアの各リージョンで利用できます。 [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) にアクセスを要求することもできます。 容量が利用可能になると、要求が承認されます。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>NFS エクスポート ポリシー規則を、Azure NetApp Files サービスのマウント ターゲットへのアクセスを制御するように構成できますか?

はい。1 つの NFS エクスポート ポリシーで、最大 5 つの規則を構成できます。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files は、ネットワーク セキュリティ グループをサポートしていますか?

いいえ。現在のところ、ネットワーク セキュリティ グループは、Azure NetApp Files の委任されたサブネットまたはサービスによって作成されたネットワーク インターフェイスに適用することはできません。

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Azure NetApp Files で Azure RBAC を使用できますか?

はい。Azure NetApp Files では、Azure RBAC の機能がサポートされています。

## <a name="performance-faqs"></a>パフォーマンスに関する FAQ

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp Files のパフォーマンスを最適化したり調整したりするには、どうすればよいですか?

パフォーマンス要件に従って、次の操作を実行できます。 
- 仮想マシンのサイズが適切であることを示します。
- VM の高速ネットワークを有効にします。
- 容量プールに使用したいサービス レベルとサイズを選択します。
- 容量とパフォーマンスに使用したいクォータ サイズのボリュームを作成します。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files のスループット ベースのサービス レベルを IOPS に変換するには、どうすればよいですか?

次の数式を使用して、MB/秒を IOPS に変換できます。  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>ボリュームのサービス レベルを変更するにはどうすればよいですか?

既存のボリュームのサービス レベルは変更できます。それには、目的のボリュームで使用したい[サービス レベル](azure-netapp-files-service-levels.md)を使用中の別の容量プールに、目的のボリュームを移動します。 「[ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md)」を参照してください。 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp Files のパフォーマンスを監視するには、どうすればよいですか?

Azure NetApp Files には、ボリュームのパフォーマンス メトリックが用意されています。 また、Azure Monitor を使用して Azure NetApp Files の使用状況のメトリックを監視することもできます。  Azure NetApp Files のパフォーマンス メトリックの一覧については、「[Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)」を参照してください。

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv4.1 での Kerberos のパフォーマンスへの影響はどのようなものですか?

NFSv 4.1 のセキュリティ オプション、テスト済みのパフォーマンス ベクトル、予想されるパフォーマンスへの影響については、[NFSv 4.1 ボリュームでの Kerberos のパフォーマンスへの影響](performance-impact-kerberos.md)に関するページを参照してください。 

## <a name="nfs-faqs"></a>NFS に関する FAQ

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM が起動または再起動されたときに、ボリュームが自動的にマウントされるようにしたいと考えています。  NFS の永続ボリュームのホストを構成するにはどうすればよいですか?

VM の起動または再起動時に NFS ボリュームを自動的にマウントする場合は、ホスト上の `/etc/fstab` ファイルにエントリを追加します。 

詳細については、「[Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)」を参照してください。  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>NFS クライアントでの DF コマンドでプロビジョニングされたボリューム サイズが表示されないのはなぜでしょうか?

DF で報告されるボリューム サイズは、Azure NetApp Files ボリュームを拡張できる最大サイズです。 DF コマンドでの Azure NetApp Files ボリュームのサイズは、クォータやボリュームのサイズを反映するものではありません。  API Azure NetApp Files ボリュームのサイズまたはクォータは、Azure portal または API を使用して取得できます。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files でサポートされている NFS のバージョンを何ですか?

Azure NetApp Files では、NFSv3 および NFSv4.1 がサポートされています。 いずれかの NFS バージョンを使用して[ボリュームを作成](azure-netapp-files-create-volumes.md)できます。 

### <a name="how-do-i-enable-root-squashing"></a>ルート スカッシュを有効にするにはどうすればよいですか?

ボリュームのエクスポート ポリシーを使用して、ルート アカウントがボリュームにアクセスできるかどうかを指定できます。 詳細については、「[NFS ボリュームのエクスポート ポリシーを構成する](azure-netapp-files-configure-export-policy.md)」を参照してください。

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>複数のボリュームに対して同じファイル パス (ボリューム作成トークン) を使用することはできますか?

はい、できます。 ただし、ファイル パスは、別のサブスクリプションまたは別のリージョンのいずれかで使用されているものでなくてはなりません。   

たとえば、`vol1` というボリュームを作成するとします。 次に、別の容量プールで、ただし同じサブスクリプションとリージョン内で、同じく `vol1` と呼ばれる別のボリュームを作成します。 この場合は、同じボリューム名 `vol1` を使用すると、エラーが発生します。 同じファイル パスを使用するには、名前が別のリージョンまたはサブスクリプションに存在している必要があります。

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Windows クライアントを使用して NFS ボリュームにアクセスしようとすると、クライアントによるフォルダーやサブフォルダーの検索に時間がかかるのはなぜですか?

フォルダーとサブフォルダーの検索を高速化するために、Windows クライアントで `CaseSensitiveLookup` が有効になっていることを確認します。

1. 次の PowerShell コマンドを使用して CaseSensitiveLookup を有効にします。   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Windows サーバーでそのボリュームをマウントします。   
    例:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Azure NetApp Files では NFSv4.1 のファイル ロックはどのようにサポートされますか? 

NFSv 4.1 クライアントの場合、Azure NetApp Files では、リース ベースのモデルですべてのファイル ロックの状態を維持する NFSv4.1 ファイル ロック メカニズムをサポートします。 

RFC 3530 に基づき、Azure NetApp Files では、NFS クライアントによって保持されるすべての状態に対して 1 つのリース期間を定義します。 定義された期間内にクライアントによってリースが更新されない場合、クライアントのリースに関連付けられているすべての状態がサーバーによって解放されます。  

たとえば、ボリュームをマウントするクライアントがタイムアウトを超えて応答しなくなったりクラッシュしたりすると、ロックが解放されます。 クライアントでは、ファイルの読み取りなどの操作を実行することで、明示的または暗黙的にリースを更新できます。   

猶予期間とは、クライアントがサーバーの回復中にロック状態を回収できるようにする特別な処理の期間を定義するものです。 リースの既定のタイムアウトは 30 秒で、猶予期間は 45 秒です。 その期間が経過すると、クライアントのリースが解放されます。   

## <a name="smb-faqs"></a>SMB に関する FAQ

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp Files でサポートされている SMB のバージョンは何ですか?

Azure NetApp Files では、SMB 2.1 と SMB 3.1 (SMB 3.0 のサポートを含む) がサポートされています。    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB でのアクセスに Active Directory 接続は必要ですか? 

はい。SMB ボリュームをデプロイする前に Active Directory の接続を作成する必要があります。 指定したドメイン コントローラーは、正常に接続するためには、Azure NetApp Files の委任されたサブネットからアクセス可能である必要があります。  詳細については、「[SMB ボリュームを作成する](./azure-netapp-files-create-volumes-smb.md)」を参照してください。 

### <a name="how-many-active-directory-connections-are-supported"></a>サポートされている Active Directory 接続はいくつですか?

Azure NetApp Files は、AD 接続が異なる NetApp アカウントにある場合でも、1 つの "*リージョン*" で複数の Active Directory (AD) 接続をサポートしていません。 ただし、AD 接続が異なるリージョンにある場合に限り、1 つの "*サブスクリプション*" で複数の AD 接続を使用できます。 1 つのリージョンに複数の AD 接続が必要な場合は、別のサブスクリプションを使用してこれを行うことができます。 

AD 接続は、NetApp アカウントごとに構成され、作成された NetApp アカウントを介してのみ表示されます。

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files で Azure Active Directory はサポートされていますか? 

[Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) と [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) の両方がサポートされます。 Azure NetApp Files で既存の Active Directory ドメイン コントローラーを使用することができます。 ドメイン コントローラーは、仮想マシンとして Azure に配置することも、ExpressRoute またはサイト間 VPN 経由でオンプレミスに配置することもできます。 Azure NetApp Files は現時点で、AD join for [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) をサポートしていません。

Azure Active Directory Domain Services と組み合わせて Azure NetApp Files を使用している場合、NetApp アカウント用に Active Directory を構成する際の組織単位のパスは `OU=AADDC Computers` になります。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>サポートされる Windows Server Active Directory のバージョンを教えてください。

Azure NetApp Files では、Windows Server 2008r2SP1-2019 バージョンの Active Directory Domain Services がサポートされています。

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>SMB クライアントで使用可能な領域にプロビジョニング済みのサイズが表示されないのはなぜですか?

SMB クライアントで報告されるボリューム サイズは、Azure NetApp Files ボリュームを拡張できる最大サイズです。 SMB クライアントに表示される Azure NetApp Files ボリュームのサイズは、クォータやボリュームのサイズを反映するものではありません。 API Azure NetApp Files ボリュームのサイズまたはクォータは、Azure portal または API を使用して取得できます。

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>SMB 共有に接続すると問題が発生します。 どうすればよいですか。

ベスト プラクティスとして、コンピューターのクロック同期の最大許容値を 5 分に設定します。 詳細については、[コンピューターのクロック同期の最大許容値](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11))に関するページをご覧ください。 

## <a name="capacity-management-faqs"></a>容量管理に関する FAQ

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Azure NetApp Files の容量プールとボリュームの使用状況を監視するには、どうすればよいですか? 

Azure NetApp Files には、容量プールとボリュームの使用状況のメトリックが用意されています。 また、Azure Monitor を使用して Azure NetApp Files の使用状況を監視することもできます。 詳細については、「[Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)」を参照してください。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure NetApp Files は Azure Storage Explorer を使用して管理できますか?

いいえ。 Azure NetApp Files は、Azure Storage Explorer ではサポートされていません。

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>ディレクトリがサイズ制限に近づいているかどうかを確認するにはどうすればよいですか?

クライアントから `stat` コマンドを使用することで、ディレクトリがディレクトリ メタデータのサイズ上限 (320 MB) に近づいているかどうかを確認できます。

320 MB のディレクトリの場合、ブロック数は 655360、各ブロック サイズは 512 バイトです  (つまり、320 x 1024 x 1024/512)。  

例 :

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>データの移行と保護に関する FAQ

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Azure NetApp Files にデータを移行するにはどうすればよいですか?
Azure NetApp Files には NFS ボリュームと SMB ボリュームがあります。  ファイル ベースのコピー ツールを使用すると、データをサービスに移行することができます。 

NetApp には、SaaS ベースのソリューションである [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service) があります。このソリューションでは、NFS または SMB のデータを、Azure NetApp Files の NFS エクスポートまたは SMB 共有にレプリケートすることができます。 

また、さまざまな無料のツールを使用してデータをコピーすることもできます。 NFS の場合は、[rsync](https://rsync.samba.org/examples.html) などのワークロード ツールを使用して、Azure NetApp Files ボリュームにソース データをコピーしたり同期したりできます。 SMB の場合は、ワークロード [robocopy](/windows-server/administration/windows-commands/robocopy) を同じ方法で使用することができます。  これらのツールでは、ファイルまたはフォルダーのアクセス許可をレプリケートすることもできます。 

オンプレミスから Azure NetApp Files へのデータ移行の要件は、次のとおりです。 

- Azure NetApp Files がターゲット Azure リージョンで利用できることを確認します。
- ソースと Azure NetApp Files のターゲット ボリュームの IP アドレス間のネットワーク接続を検証します。 オンプレミスと Azure NetApp Files サービスとの間のデータ転送は、ExpressRoute 経由でサポートされています。
- ターゲットの Azure NetApp Files ボリュームを作成します。
- 使用したいファイル コピー ツールを使用して、ターゲット ボリュームにソース データを転送します。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Azure NetApp Files ボリュームのコピーを別の Azure リージョンに作成するには、どうすればよいですか?
    
Azure NetApp Files には NFS ボリュームと SMB ボリュームがあります。  ファイル ベースのコピー ツールは、Azure リージョン間でデータをレプリケートするために使用できます。 

NetApp には、SaaS ベースのソリューションである [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service) があります。このソリューションでは、NFS または SMB のデータを、Azure NetApp Files の NFS エクスポートまたは SMB 共有にレプリケートすることができます。 

また、さまざまな無料のツールを使用してデータをコピーすることもできます。 NFS の場合は、[rsync](https://rsync.samba.org/examples.html) などのワークロード ツールを使用して、Azure NetApp Files ボリュームにソース データをコピーしたり同期したりできます。 SMB の場合は、ワークロード [robocopy](/windows-server/administration/windows-commands/robocopy) を同じ方法で使用することができます。  これらのツールでは、ファイルまたはフォルダーのアクセス許可をレプリケートすることもできます。 

Azure NetApp Files ボリュームを別の Azure リージョンにレプリケートするための要件は次のとおりです。 
- Azure NetApp Files がターゲット Azure リージョンで利用できることを確認します。
- 各リージョン内の Vnet 間のネットワーク接続を検証します。 現在のところ、VNet 間のグローバル ピアリングはサポートされていません。  VNet 間の接続は、ExpressRoute 回線とリンクするか、S2S VPN 接続を使用することで確立することができます。 
- ターゲットの Azure NetApp Files ボリュームを作成します。
- 使用したいファイル コピー ツールを使用して、ターゲット ボリュームにソース データを転送します。

### <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box を使用した移行はサポートされていますか?

いいえ。 Azure Data Box では、現在のところ Azure NetApp Files はサポートされていません。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Azure Import/Export サービスを使用した移行はサポートされていますか?

いいえ。 Azure Import/Export サービスでは、現在のところ Azure NetApp Files はサポートされません。

## <a name="product-faqs"></a>製品に関する FAQ

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware Solution (AVS) で Azure NetApp Files NFS または SMB ボリュームを使用できますか?

Azure NetApp Files NFS ボリュームは、AVS Windows VM または Linux VM にマウントできます。 AVS Windows VM 上で Azure NetApp Files SMB 共有をマップできます。 詳細については、「[Azure NetApp Files と Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md)」を参照してください。  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware Solution (AVS) で Azure NetApp Files NFS または SMB ボリュームを使用する場合、どのリージョンがサポートされますか?

AVS での Azure NetApp Files NFS または SMB ボリュームの使用は、米国東部、米国西部、西ヨーロッパ、およびオーストラリア東部の各リージョンでサポートされます。

## <a name="next-steps"></a>次のステップ  

- [Microsoft Azure ExpressRoute に関する FAQ](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network に関する FAQ](../virtual-network/virtual-networks-faq.md)
- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Azure NetApp Files の SMB パフォーマンスに関する FAQ](azure-netapp-files-smb-performance.md)
