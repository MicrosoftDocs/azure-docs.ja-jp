---
title: Azure File Sync のネットワークに関する考慮事項 | Microsoft Docs
description: Azure File Sync を使用してファイルをオンプレミスでキャッシュするようにネットワークを構成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629361"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure File Sync のネットワークに関する考慮事項
Azure ファイル共有には、次の 2 つの方法で接続できます。

- SMB または FileREST プロトコル経由で直接共有にアクセスします。 このアクセス パターンは主に、できるだけ多くのオンプレミス サーバーを排除するために使用されます。
- Azure File Sync を使用してオンプレミス サーバー上 (Azure VM 上) に Azure ファイル共有のキャッシュを作成し、ユース ケースに合った任意のプロトコル (SMB、NFS、FTPS など) を使用してオンプレミス サーバーからファイル共有のデータにアクセスします。 このアクセス パターンは、オンプレミスのパフォーマンスとクラウド スケールの両方、およびサーバーレスの接続可能なサービス (Azure Backup など) のメリットが組み合わされるために非常に便利です。

この記事では、SMB 経由で Azure ファイル共有を直接マウントするのではなく、Azure File Sync を使用してファイルをオンプレミスにキャッシュするために実際のユース ケースに必要となるネットワークの構成方法に重点を置いています。 Azure Files のデプロイのネットワークに関する考慮事項の詳細については、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

Azure File Sync のネットワーク構成は、2 つの異なる Azure オブジェクト (ストレージ同期サービスと Azure ストレージ アカウント) にまたがります。 ストレージ アカウントは、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。 ストレージ同期サービスは、Azure File Sync との信頼関係が確立された Windows ファイル サーバーである登録済みサーバーと、同期関係のトポロジを定義する同期グループを表す管理構造です。 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Azure File Sync を使用して Windows ファイル サーバーを Azure に接続する 
Azure Files と Azure File Sync を設定して、オンプレミスの Windows ファイル サーバーと共に使用する場合、基本的なインターネット接続以外に Azure への特別なネットワークは必要ありません。 Azure File Sync をデプロイするには、Azure と同期する Windows ファイル サーバー上に Azure File Sync エージェントをインストールします。 Azure File Sync エージェントは、次の 2 つのチャネルを介して Azure ファイル共有との同期を実現します。

- FileREST プロトコル。これは、ご自分の Azure ファイル共有にアクセスするための HTTPS ベースのプロトコルです。 FileREST プロトコルではデータ転送に標準の HTTPS が使用されるため、アウトバウンド アクセスが可能な必要があるポートは 443 のみです。 Azure File Sync では、オンプレミスのご使用の Windows Server からご自分の Azure ファイル共有にデータを転送するのに SMB プロトコルを使用しません。
- Azure File Sync 同期プロトコル。これは、実際の環境内のエンドポイント間で同期ナレッジ (つまり、実際の環境内のファイルとフォルダーに関するバージョン情報) を交換するための HTTPS ベースのプロトコルです。 このプロトコルは、タイムスタンプやアクセス制御リスト (ACL) など、実際の環境内のファイルとフォルダーに関するメタデータを交換するためにも使用されます。 

Azure Files は Azure ファイル共有に対する SMB プロトコルによる直接アクセスを提供しているため、お客様は SMB を使用して Azure ファイル共有をマウントする場合、Azure File Sync エージェントがアクセスできるように特別なネットワークを構成する必要があるのかという疑問をよく持ちます。 これは必要ではないうえに、管理者シナリオを除き、非推奨です。これは、Azure ファイル共有に直接変更が加えられた場合に、すぐに変更が検出されないためです (Azure ファイル共有内の項目のサイズと数によっては変更が 24 時間以上も検出されない場合があります)。 Azure ファイル共有を直接使用する必要がある場合 (つまり、Azure File Sync を使用せずにオンプレミスにキャッシュする場合)、[Azure Files ネットワークの概要](storage-files-networking-overview.md)に関するページを参照して、直接アクセスのためのネットワークに関する考慮事項の詳細を確認してください。

Azure File Sync には特別なネットワーク構成は必要ありませんが、一部のお客様は、次のシナリオを実現するために、ネットワークの詳細設定を構成することができます。

- ご自分の組織のプロキシ サーバー構成と相互運用する。
- Azure Files および Azure File Sync サービスに対してご自分の組織のオンプレミスのファイアウォールを開く。
- ExpressRoute または VPN 接続経由で Azure Files と Azure File Sync をトンネリングする。

### <a name="configuring-proxy-servers"></a>プロキシ サーバーの構成
多くの組織では、オンプレミス ネットワーク内のリソースと、Azure 内などのネットワーク外部のリソース間の仲介役としてプロキシ サーバーを使用しています。 プロキシ サーバーは、ネットワークの分離とセキュリティ、監視、ログ記録などのさまざまな用途で役立ちます。 Azure File Sync はプロキシ サーバーと完全に相互運用できますが、Azure File Sync を使用する実際の環境に合わせてプロキシ エンドポイントの設定を手動で構成する必要があります。これは、PowerShell を介して Azure File Sync のサーバー コマンドレットを使用して行う必要があります。 

プロキシ サーバーを使用する場合の Azure File Sync の構成方法の詳細については、[プロキシ サーバーを使用する場合の Azure File Sync の構成](storage-sync-files-firewall-and-proxy.md)に関するページを参照してください。

### <a name="configuring-firewalls-and-service-tags"></a>ファイアウォールとサービス タグを構成する
ご使用のファイル サーバーは、セキュリティ目的で、ほとんどのインターネットの場所から分離することができます。 実際の環境内で Azure File Sync を使用するには、ご使用のファイアウォールを調整して、選択した Azure サービスに対して開く必要があります。 これを行うには、[サービス タグ](../../virtual-network/service-tags-overview.md)と呼ばれるメカニズムを使用して、必要なサービスの IP アドレス範囲を取得します。

Azure File Sync には、次のサービスのサービス タグによって識別されるそれぞれの IP アドレス範囲が必要です。

| サービス | 説明 | サービス タグ |
|---------|-------------|-------------|
| Azure File Sync | ストレージ同期サービス オブジェクトによって表される Azure File Sync サービスは、Azure ファイル共有と Windows ファイル サーバーとの間でデータを同期するための主要なアクティビティを担当します。 | `StorageSyncService` |
| Azure Files | Azure File Sync 経由で同期されるデータはすべて Azure ファイル共有内に格納されます。 ご自分の Windows ファイル サーバー上で変更されたファイルはご使用の Azure ファイル共有にレプリケートされます。オンプレミスのご使用のファイル サーバー上で階層化されたファイルは、ユーザーが要求したときにシームレスにダウンロードされます。 | `Storage` |
| Azure Resource Manager | Azure Resource Manager は、Azure の管理インターフェイスです。 Azure File Sync サーバー登録と同期サーバーの継続的なタスクを含むすべての管理呼び出しは、Azure Resource Manager を通じて行われます。 | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory (Azure AD) には、ストレージ同期サービスに対するサーバーの登録を承認するために必要なユーザー プリンシパルと、ご使用のクラウド リソースへのアクセスを Azure File Sync に許可するために必要なサービス プリンシパルが含まれています。 | `AzureActiveDirectory` |

Azure 内で Azure File Sync を使用している場合は、リージョンが異なっていても、ご自分のネットワーク セキュリティ グループ内でサービス タグ名を直接使用して、そのサービスに対するトラフィックを許可することができます。 詳しい方法については、「[セキュリティ グループ](../../virtual-network/network-security-groups-overview.md)」を参照してください。 

オンプレミスの Azure File Sync を使用している場合は、サービス タグ API を使用して、ファイアウォールの許可リスト用の特定の IP アドレス範囲を取得できます。 この情報を取得するには、次の 2 つの方法があります。

- サービス タグをサポートするすべての Azure サービスの最新の IP アドレス範囲の一覧が、Microsoft ダウンロード センターに JSON ドキュメントの形式で毎週公開されています。 各 Azure クラウドには、そのクラウドに関連する IP アドレス範囲が記載された独自の JSON ドキュメントが存在します。
    - [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- サービス タグ検出 API (プレビュー) を使用すると、サービス タグの現在の一覧をプログラムで取得できます。 プレビューの段階では、サービス タグ検出 API によって返される情報は、Microsoft ダウンロード センターに公開されている JSON ドキュメントから返される情報よりも古い場合があります。 API サーフェスは、ご自分の自動化の設定に基づいて使用できます。
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

サービス タグ API を利用してご使用のサービスのアドレスを取得する方法の詳細については、「[Azure File Sync IP アドレスの許可リスト](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses)」を参照してください。

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>仮想プライベート ネットワークまたは ExpressRoute でトラフィックをトンネリングする
組織によっては、セキュリティ レイヤーを追加したり、Azure との通信が決定的なルートに確実に従うようにしたりするために、仮想プライベート ネットワーク (VPN) や ExpressRoute などのネットワーク トンネル経由で Azure と通信する必要があります。 

オンプレミス ネットワークと Azure との間にネットワーク トンネルを確立したら、Azure 内の 1 つ以上の仮想ネットワークとオンプレミス ネットワークをピアリングします。 [仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) (VNet) は、オンプレミスで運用する従来のネットワークに似ています。 Azure ストレージ アカウントや Azure VM と同様に、VNet は、リソース グループにデプロイされる Azure リソースです。 

オンプレミスのご使用のサーバーと Azure の間のトラフィックをトンネリングするために、Azure Files および File Sync では次のメカニズムをサポートしています。

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): VPN ゲートウェイは、暗号化されたトラフィックをインターネット経由で Azure 仮想ネットワークと別の場所 (オンプレミスなど) の間で送信するために使用される特定の種類の仮想ネットワーク ゲートウェイです。 Azure VPN Gateway は、ストレージ アカウントまたはその他の Azure リソースと共にリソース グループにデプロイできる Azure リソースです。 Azure File Sync ではオンプレミスの Windows ファイル サーバーとの使用が想定されているため、通常は[サイト間 (S2S) VPN](../../vpn-gateway/design.md#s2smulti) を使用しますが、[ポイント対サイト (P2S) VPN](../../vpn-gateway/point-to-site-about.md) を使用することも技術的には可能です。 

    サイト間 (S2S) VPN 接続では、ご使用の Azure 仮想ネットワークとご自分の組織のオンプレミス ネットワークを接続します。 S2S VPN 接続を使用すると、Azure ファイル共有にアクセスする必要のあるクライアント デバイスごとにではなく、組織のネットワークでホストされている VPN サーバーまたはデバイスについて 1 回 VPN 接続を構成するだけで済みます。 S2S VPN 接続のデプロイを簡略化するには、[Azure Files で使用するサイト間 (S2S) VPN の構成](storage-files-configure-s2s-vpn.md)に関するページを参照してください。

- [ExpressRoute](../../expressroute/expressroute-introduction.md)。これにより、Azure とインターネットを経由しないオンプレミス ネットワークの間に定義されたルートを作成できます。 ExpressRoute はオンプレミスのデータセンターと Azure の間の専用のパスを提供するため、ExpressRoute は、ネットワーク パフォーマンスが考慮事項であるときに役立つことがあります。 ExpressRoute はまた、組織のポリシーまたは規制要件にクラウド内のリソースへの確定的なパスが必要な場合の適切なオプションでもあります。

### <a name="private-endpoints"></a>プライベート エンドポイント
Azure Files と File Sync がストレージ アカウントとストレージ同期サービスを通じて提供する既定のパブリック エンドポイントに加えて、Azure Files と File Sync には、リソースごとに 1 つ以上のプライベート エンドポイントを設定するオプションが用意されています。 Azure リソースのプライベート エンドポイントを作成すると、そのリソースには、ご使用の仮想ネットワークのアドレス空間内からプライベート IP アドレスが与えられます。ちょうど、オンプレミスのご使用の Windows ファイル サーバーに、そのオンプレミス ネットワークの専用アドレス空間から IP アドレスが割り当てられるのと似ています。 

> [!Important]  
> ストレージ同期サービス リソースに対してプライベート エンドポイントを使用するには、Azure File Sync エージェント バージョン10.1 以降を使用する必要があります。 10.1 より前のバージョンのエージェントでは、ストレージ同期サービスに対するプライベート エンドポイントはサポートされません。 以前のすべてのエージェント バージョンでは、ストレージ アカウント リソースに対するプライベート エンドポイントがサポートされています。

個々のプライベート エンドポイントは、Azure 仮想ネットワークの特定のサブネットに関連付けられます。 ストレージ アカウントとストレージ同期サービスには、複数の仮想ネットワーク内のプライベート エンドポイントを使用することができます。

プライベート エンドポイントを使用することにより、次のことが可能となります。
- オンプレミス ネットワークから、VPN または ExpressRoute 接続によるプライベートピアリングを利用してご使用の Azure リソースに安全に接続します。
- Azure Files と File Sync のパブリック エンドポイントを無効にして、ご使用の Azure リソースを保護します。既定では、プライベート エンドポイントを作成しても、パブリック エンドポイントへの接続はブロックされません。
- 仮想ネットワーク (およびピアリングの境界) からのデータの流出をブロックできるようにすることで、仮想ネットワークのセキュリティを強化します。

プライベート エンドポイントを作成する方法については、[Azure File Sync 用のプライベート エンドポイントの構成](storage-sync-files-networking-endpoints.md)に関するページを参照してください。

### <a name="private-endpoints-and-dns"></a>プライベート エンドポイントと DNS
プライベート エンドポイントを作成すると、既定では、`privatelink` サブドメインに対応するプライベート DNS ゾーンも作成 (または既存のゾーンが更新) されます。 パブリック クラウド リージョンでは、これらの DNS ゾーンは、Azure Files の場合は `privatelink.afs.azure.net`、Azure File Sync の場合は `privatelink.file.core.windows.net` です。

> [!Note]  
> この記事では、Azure パブリック リージョンを対象に、ストレージ アカウントの DNS サフィックス `core.windows.net` を使用しています。 この解説は、Azure ソブリン クラウド (Azure US Government クラウドや Azure China クラウドなど) にも当てはまります。ご利用の環境の適切なサフィックスに置き換えてください。 

ストレージ アカウントとストレージ同期サービスのプライベート エンドポイントを作成すると、それぞれのプライベート DNS ゾーン内にその A レコードが作成されます。 また、通常の完全修飾ドメイン名が関連する privatelink 名の CNAME になるようにパブリック DNS エントリが更新されます。 これにより、完全修飾ドメイン名が、要求元が仮想ネットワーク内にある場合はプライベート エンドポイントの IP アドレスを指し、要求元が仮想ネットワークの外部にある場合はパブリック エンドポイントの IP アドレスを指すことができます。 

Azure Files の場合、各プライベート エンドポイントには、`storageaccount.privatelink.file.core.windows.net` というパターンに従った 1 つの完全修飾ドメイン名が割り当てられ、プライベート エンドポイントの 1 つのプライベート IP アドレスにマップされます。 Azure File Sync の場合、各プライベート エンドポイントには、Azure File Sync が公開する 4 つの異なるエンドポイント (管理、同期 (プライマリ)、同期 (セカンダリ)、監視) の 4 つの完全修飾ドメイン名があります。 これらのエンドポイントの完全修飾ドメイン名は、通常、名前に ASCII 以外の文字が含まれている場合を除き、ストレージ同期サービスの名前に従います。 たとえば、ストレージ同期サービス名が米国西部 2 リージョン内で `mysyncservice` である場合、対応するエンドポイントは `mysyncservicemanagement.westus2.afs.azure.net`、`mysyncservicesyncp.westus2.afs.azure.net`、`mysyncservicesyncs.westus2.afs.azure.net`、`mysyncservicemonitoring.westus2.afs.azure.net` になります。 ストレージ同期サービスの各プライベート エンドポイントには、4 つの異なる IP アドレスが含まれます。 

Azure のプライベート DNS ゾーンは、プライベート エンドポイントを含んだ仮想ネットワークに接続されているため、Azure VM の PowerShell から `Resolve-DnsName` コマンドレット (Windows および Linux では `nslookup` でも可) を呼び出せば、DNS 構成を観察することができます。

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

この例では、ストレージ アカウント `storageaccount.file.core.windows.net` が、プライベート エンドポイントのプライベート IP アドレス (`192.168.0.4`) に解決されます。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

同じコマンドをオンプレミスから実行した場合、同じストレージ アカウント名が、ストレージ アカウントのパブリック IP アドレスに解決されることがわかります。`storageaccount.file.core.windows.net` は `storageaccount.privatelink.file.core.windows.net` の CNAME レコードで、さらにそれが、ストレージ アカウントをホストする Azure ストレージ クラスターの CNAME レコードになっています。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

このことは、Azure Files および Azure File Sync が、パブリック エンドポイントと、リソースごとに 1 つ以上のプライベート エンドポイントの両方を公開できるという事実を表しています。 ご使用のリソースの完全修飾ドメイン名が確実にプライベート エンドポイントのプライベート IP アドレスに解決されるようにするには、オンプレミスのご使用の DNS サーバーの構成を変更する必要があります。 これはいくつかの方法で実行できます。

- ご使用のクライアント上の hosts ファイルを変更して、ご自分のストレージ アカウントとストレージ同期サービスの完全修飾ドメイン名が目的のプライベート IP アドレスに解決されるようにします。 これは、運用環境では決してお勧めできません。ご使用のプライベート エンドポイントにアクセスする必要があるすべてのクライアントに対してこれらの変更を行う必要が生じるからです。 ご使用のプライベート エンドポイントやリソースに対する変更 (削除や変更など) は、自動的には処理されません。
- オンプレミスのご使用のサーバー上に `privatelink.file.core.windows.net` および `privatelink.afs.azure.net` の DNS ゾーンと、ご使用の Azure リソースの A レコードを作成します。 この方法の利点は、ご使用のオンプレミス環境内のクライアントを個別に構成しなくても、クライアントが自動的に Azure リソースを解決できるようになることです。ただし、変更は反映されないので、hosts ファイルを編集するのと同様の弱点があります。 この解決策は完璧ではありませんが、環境によっては最適な選択肢となりえます。
- `core.windows.net` と `afs.azure.net` ゾーンをオンプレミスのご使用の DNS サーバーから Azure のご自分のプライベート DNS ゾーンに転送します。 Azure のプライベート DNS ホストには、特殊な IP アドレス (`168.63.129.16`) でアクセスできますが、このアドレスには、Azure のプライベート DNS ゾーンにリンクされた仮想ネットワーク内からしかアクセスできません。 この制限を回避するには、Azure の同等のプライベート DNS ゾーンに `core.windows.net` と `afs.azure.net` を転送する別の DNS サーバーをご使用の仮想ネットワーク内で実行します。 この設定を簡略化するために、Azure 仮想ネットワークに DNS サーバーを自動デプロイしてそれらを必要に応じて構成する PowerShell コマンドレットを用意しています。 DNS 転送を設定する方法については、[Azure Files を使用した DNS の構成](storage-files-networking-dns.md)に関するページを参照してください。

## <a name="encryption-in-transit"></a>転送中の暗号化
Azure File Sync エージェントからご使用の Azure ファイル共有またはストレージ同期サービスへの接続は、常に暗号化されます。 Azure ストレージ アカウントには、Azure Files (およびストレージ アカウント外で管理されているその他の Azure ストレージ サービス) への通信に対する転送中の暗号化の要求を無効にする設定がありますが、この設定を無効にしても、Azure Files と通信する際の Azure File Sync の暗号化には影響しません。 既定では、すべての Azure ストレージ アカウントで転送中の暗号化が有効になっています。 

転送中の暗号化の詳細については、「[Azure Storage で安全な転送が必要](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

## <a name="see-also"></a>関連項目
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)