---
title: Azure Files のネットワークに関する考慮事項 | Microsoft Docs
description: Azure Files のネットワーク オプションの概要。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cec386b798b843a5badc9d52d9c71bd7df54b59a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601935"
---
# <a name="azure-files-networking-considerations"></a>Azure Files のネットワークに関する考慮事項 
Azure ファイル共有には、次の 2 つの方法で接続できます。

- サーバー メッセージ ブロック (SMB)、Network File System (NFS) (プレビュー)、または FileREST プロトコルを使用して、共有に直接アクセスします。 このアクセス パターンは主に、できるだけ多くのオンプレミス サーバーを排除するために使用されます。
- Azure File Sync を使用してオンプレミス サーバー上 (Azure VM 上) に Azure ファイル共有のキャッシュを作成し、ユース ケースに合った任意のプロトコル (SMB、NFS、FTPS など) を使用してオンプレミス サーバーからファイル共有のデータにアクセスします。 このアクセス パターンは、オンプレミスのパフォーマンスとクラウド スケールの両方、およびサーバーレスの接続可能なサービス (Azure Backup など) のメリットが組み合わされるために非常に便利です。

この記事では、Azure File Sync を使用するのではなく、直接 Azure ファイル共有にアクセスするためにユース ケースに必要となるネットワークの構成方法に重点を置いています。Azure File Sync のデプロイのネットワークに関する考慮事項の詳細については、「[Azure File Sync のネットワークに関する考慮事項](storage-sync-files-networking-overview.md)」を参照してください。

Azure ファイル共有のネットワーク構成は Azure ストレージ アカウントで行います。 ストレージ アカウントは、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。 ストレージ アカウントには、ネットワーク エンドポイント、ストレージ アカウントのファイアウォール設定、転送中の暗号化など、ファイル共有へのネットワーク アクセスをセキュリティで保護するうえで役立つ多数の設定が公開されています。 

この概念的なガイドを読む前に、「[Azure Files のデプロイの計画](storage-files-planning.md)」を読むことをお勧めします。

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        このビデオは、簡単な 5 つのステップでインフォメーション ワーカーやアプリに直接 Azure ファイル共有を安全に公開する方法についてのガイドとデモです。 下のセクションでは、ビデオ内で参照されているドキュメントへのリンクと追加のコンテキストを提供します。
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Azure ファイル共有へのアクセス
ストレージ アカウントに Azure ファイル共有をデプロイするとすぐに、そのストレージ アカウントのパブリック エンドポイント経由でファイル共有にアクセスできるようになります。 つまり、ユーザーのログオン ID によって承認された要求など、認証済みの要求は、Azure の内外から安全に送信できます。 

多くのお客様の環境では、Azure VM からの Azure ファイル共有のマウントは成功しても、オンプレミス ワークステーションでの最初のマウントは失敗します。 その理由は、SMB での通信に使用されるポート (445) が、多くの組織とインターネット サービス プロバイダー (ISP) によってブロックされているためです。 NFS 共有にはこの問題はありません。 この慣例の由来は、今や非推奨の古いバージョンの SMB プロトコルに関するセキュリティ ガイダンスです。 SMB 3.0 はインターネットでも安全に使用できるプロトコルですが、以前のバージョンの SMB、特に SMB 1.0 はそうではありません。 Azure ファイル共有に外部からアクセスするには、SMB 3.0 と FileREST プロトコル (こちらもインターネットで安全に使用できるプロトコル)、およびパブリック エンドポイントを使用する必要があります。

オンプレミスから Azure SMB ファイル共有にアクセスする最も簡単な方法は、オンプレミス ネットワークをポート 445 に開放することです。そのため、Microsoft では、次の手順に従って SMB 1.0 をお使いの環境から取り除くことを推奨しています。

1. 組織のデバイス上で SMB 1.0 が削除されているか、または無効になっていることを確認します。 現在サポートされているすべてのバージョンの Windows および Windows Server で SMB 1.0 の削除または無効化がサポートされており、Windows 10 バージョン 1709 からは、SMB 1.0 が既定では Windows 上にインストールされません。 SMB 1.0 を無効にする方法の詳細については、次の OS 固有のページを参照してください。
    - [Windows/Windows Server のセキュリティ保護](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux のセキュリティ保護](storage-how-to-use-files-linux.md#securing-linux)
1. SMB 1.0 が必要な製品が組織内に存在しないことを確認し、存在する場合はそれを削除します。 Microsoft は、SMB 1.0 が必要であると Microsoft で認識しているすべてのファーストパーティ製品とサードパーティ製品が含まれた [SMB1 製品クリアリングハウス](https://aka.ms/stillneedssmb1)を保持しています。 
1. (省略可能) 組織のオンプレミス ネットワークでサードパーティのファイアウォールを使用し、SMB 1.0 トラフィックが組織の境界から出て行かないようにします。

組織でポリシーまたは規制に従ってポート 445 をブロックする必要がある場合や、Azure へのトラフィックが確定的なパスをたどらなければならない組織の場合は、Azure VPN Gateway または ExpressRoute を使用して、Azure ファイル共有へのトラフィックをトンネリングできます。 NFS 共有ではポート 445 は必要ないため、このような操作は必要ありません。

> [!Important]  
> 別の方法で Azure ファイル共有にアクセスすることを決めた場合でも、Microsoft は SMB 1.0 をお使いの環境から取り除くことをお勧めします。

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>仮想プライベート ネットワークまたは ExpressRoute でトラフィックをトンネリングする
オンプレミス ネットワークと Azure との間にネットワーク トンネルを確立したら、Azure 内の 1 つ以上の仮想ネットワークとオンプレミス ネットワークをピアリングします。 [仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) (VNet) は、オンプレミスで運用する従来のネットワークに似ています。 Azure ストレージ アカウントや Azure VM と同様に、VNet は、リソース グループにデプロイされる Azure リソースです。 

オンプレミスのワークステーションやサーバーと Azure SMB/NFS ファイル共有の間のトラフィックをトンネリングするために、Azure Files は次のメカニズムをサポートしています。

- VPN ゲートウェイは、暗号化されたトラフィックをインターネット経由で Azure 仮想ネットワークと別の場所 (オンプレミスなど) の間で送信するために使用される特定の種類の仮想ネットワーク ゲートウェイです。 Azure VPN Gateway は、ストレージ アカウントまたはその他の Azure リソースと共にリソース グループにデプロイできる Azure リソースです。 VPN ゲートウェイは、次の 2 つの異なる種類の接続を公開します。
    - [ポイント対サイト (P2S) VPN](../../vpn-gateway/point-to-site-about.md) ゲートウェイ接続。これは、Azure と個々のクライアントの間の VPN 接続です。 このソリューションは主に、自宅、コーヒー ショップ、または出先のホテルから自分の Azure ファイル共有をマウントできるようにしたい在宅勤務者などの、組織のオンプレミス ネットワークの一部ではないデバイスに役立ちます。 Azure Files で P2S VPN 接続を使用するには、接続したいクライアントごとに P2S VPN 接続を構成する必要があります。 P2S VPN 接続のデプロイを簡略化するには、「[Windows 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する](storage-files-configure-p2s-vpn-windows.md)」および「[Linux 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する](storage-files-configure-p2s-vpn-linux.md)」を参照してください。
    - [サイト間 (S2S) VPN](../../vpn-gateway/design.md#s2smulti)。これは、Azure と組織のネットワークの間の VPN 接続です。 S2S VPN 接続を使用すると、Azure ファイル共有にアクセスする必要のあるクライアント デバイスごとにではなく、組織のネットワークでホストされている VPN サーバーまたはデバイスについて 1 回 VPN 接続を構成するだけで済みます。 S2S VPN 接続のデプロイを簡略化するには、[Azure Files で使用するサイト間 (S2S) VPN の構成](storage-files-configure-s2s-vpn.md)に関するページを参照してください。
- [ExpressRoute](../../expressroute/expressroute-introduction.md)。これにより、Azure とインターネットを経由しないオンプレミス ネットワークの間に定義されたルートを作成できます。 ExpressRoute はオンプレミスのデータセンターと Azure の間の専用のパスを提供するため、ExpressRoute は、ネットワーク パフォーマンスが考慮事項であるときに役立つことがあります。 ExpressRoute はまた、組織のポリシーまたは規制要件にクラウド内のリソースへの確定的なパスが必要な場合の適切なオプションでもあります。

Azure ファイル共有へのアクセスに使用するトンネリング方法に関係なく、ストレージ アカウントへのトラフィックを、通常のインターネット接続ではなく、確実にトンネルに通すためのメカニズムは必要です。 ストレージ アカウントのパブリック エンドポイントにルーティングすることは技術的には可能ですが、そのためには、特定のリージョン内にある Azure ストレージ クラスターの IP アドレスをすべてハードコーディングする必要があります。これは、ストレージ アカウントはストレージ クラスター間で随時移動される可能性があるためです。 また、新しいクラスターが常時追加されるので、IP アドレスのマッピングも絶えず更新する必要があります。

ストレージ アカウントの IP アドレスを VPN のルーティング規則にハードコーディングするのではなく、プライベート エンドポイントを使用することをお勧めします。そうすると、Azure 仮想ネットワークのアドレス空間の IP アドレスがストレージ アカウントに与えられます。 Azure へのトンネルを作成すると、オンプレミス ネットワークと 1 つ以上の仮想ネットワークの間にピアリングが確立されるので、適切なルーティングが永続的に可能になります。

### <a name="private-endpoints"></a>プライベート エンドポイント
Azure Files では、ストレージ アカウントの既定のパブリック エンドポイントに加え、1 つ以上のプライベート エンドポイントを使用することもできます。 プライベート エンドポイントは、Azure 仮想ネットワーク内でのみアクセス可能なエンドポイントです。 ストレージ アカウントのプライベート エンドポイントを作成すると、ストレージ アカウントには、仮想ネットワークのアドレス空間内のプライベート IP アドレスが与えられます。ちょうど、オンプレミスのファイル サーバーや NAS デバイスに、そのオンプレミス ネットワークの専用アドレス空間から IP アドレスが割り当てられるのと似ています。 

個々のプライベート エンドポイントは、Azure 仮想ネットワークの特定のサブネットに関連付けられます。 ストレージ アカウントには、複数の仮想ネットワーク内のプライベート エンドポイントを使用することができます。

Azure Files でプライベート エンドポイントを使用することにより、次のことが可能となります。
- オンプレミス ネットワークから、VPN または ExpressRoute 接続によるプライベートピアリングを使用して Azure ファイル共有に安全に接続します。
- パブリック エンドポイントでのすべての接続をブロックするようにストレージ アカウントのファイアウォールを構成することで、Azure ファイル共有をセキュリティで保護します。 既定では、プライベート エンドポイントを作成しても、パブリック エンドポイントへの接続はブロックされません。
- 仮想ネットワーク (およびピアリングの境界) からのデータの流出をブロックできるようにすることで、仮想ネットワークのセキュリティを強化します。

プライベート エンドポイントを作成する方法については、[Azure Files 用のプライベート エンドポイントの構成](storage-files-networking-endpoints.md)に関するページを参照してください。

### <a name="private-endpoints-and-dns"></a>プライベート エンドポイントと DNS
プライベート エンドポイントを作成すると、既定では、`privatelink` サブドメインに対応するプライベート DNS ゾーンも作成 (または既存のゾーンが更新) されます。 厳密には、ストレージ アカウントにプライベート エンドポイントを使用するためにプライベート DNS ゾーンの作成は必要ありませんが、一般にはこれを強くお勧めします。また、Active Directory のユーザー プリンシパルで Azure ファイル共有をマウントしたり、FileREST API からアクセスしたりする場合には、間違いなく必要です。

> [!Note]  
> この記事では、Azure パブリック リージョンを対象に、ストレージ アカウントの DNS サフィックス `core.windows.net` を使用しています。 この解説は、Azure ソブリン クラウド (Azure US Government クラウドや Azure China クラウドなど) にも当てはまります。ご利用の環境の適切なサフィックスに置き換えてください。 

プライベート DNS ゾーンに、`storageaccount.privatelink.file.core.windows.net` の A レコードを作成すると共に、`storageaccount.file.core.windows.net` というパターンに従って、ストレージ アカウントの通常の名前に対応する CNAME レコードを作成します。 Azure のプライベート DNS ゾーンは、プライベート エンドポイントを含んだ仮想ネットワークに接続されているため、Azure VM の PowerShell から `Resolve-DnsName` コマンドレット (Windows および Linux では `nslookup` でも可) を呼び出せば、DNS 構成を観察することができます。

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

このことは、ストレージ アカウントが、パブリック エンドポイントと 1 つ以上のプライベート エンドポイントの両方を公開できるという事実を表しています。 ストレージ アカウント名が確実にプライベート エンドポイントのプライベート IP アドレスに解決されるようにするには、オンプレミスの DNS サーバーの構成を変更する必要があります。 これはいくつかの方法で実行できます。

- `storageaccount.file.core.windows.net` が目的のプライベート エンドポイントのプライベート IP アドレスに解決されるように、クライアントの hosts ファイルを変更します。 これは、運用環境では決してお勧めできません。Azure ファイル共有をマウントする予定のすべてのクライアントに対してこれらの変更を行う必要が生じるからです。ストレージ アカウントまたはプライベート エンドポイントへの変更も自動的には処理されません。
- オンプレミスの DNS サーバーに `storageaccount.file.core.windows.net` の A レコードを作成します。 この方法の利点は、オンプレミス環境内のクライアントを個別に構成しなくても、クライアントが自動的にストレージ アカウントを解決できるようになることです。ただし、変更は反映されないので、hosts ファイルを編集するのと同様の弱点があります。 この解決策は完璧ではありませんが、環境によっては最適な選択肢となりえます。
- `core.windows.net` ゾーンをオンプレミスの DNS サーバーから Azure のプライベート DNS ゾーンに転送します。 Azure のプライベート DNS ホストには、特殊な IP アドレス (`168.63.129.16`) でアクセスできますが、このアドレスには、Azure のプライベート DNS ゾーンにリンクされた仮想ネットワーク内からしかアクセスできません。 この制限を回避するには、Azure のプライベート DNS ゾーンに `core.windows.net` を転送する別の DNS サーバーを仮想ネットワーク内で実行します。 この設定を簡略化するために、Azure 仮想ネットワークに DNS サーバーを自動デプロイしてそれらを必要に応じて構成する PowerShell コマンドレットを用意しています。 DNS 転送を設定する方法については、[Azure Files を使用した DNS の構成](storage-files-networking-dns.md)に関するページを参照してください。

## <a name="storage-account-firewall-settings"></a>ストレージ アカウントのファイアウォール設定
ファイアウォールは、ストレージ アカウントのパブリック エンドポイントへのアクセスをどの要求に許可するかを制御するネットワーク ポリシーです。 ストレージ アカウントのファイアウォールを使用すると、ストレージ アカウントのパブリック エンドポイントへのアクセスを特定の IP アドレスや IP アドレス範囲、または仮想ネットワークに制限することができます。 一般に、ストレージ アカウントのほとんどのファイアウォール ポリシーではネットワーク アクセスが 1 つ以上の仮想ネットワークに制限されます。 

ストレージ アカウントへのアクセスを仮想ネットワークに制限するには、次の 2 つの方法があります。
- ストレージ アカウントに 1 つ以上のプライベート エンドポイントを作成し、パブリック エンドポイントへのアクセスをすべて制限します。 これで、そのストレージ アカウント内の Azure ファイル共有にアクセスできるのは、特定の仮想ネットワーク内から送信されたトラフィックだけになります。
- パブリック エンドポイントを 1 つ以上の仮想ネットワークに制限します。 これは、"*サービス エンドポイント*" と呼ばれる仮想ネットワークの機能を使用して実現できます。 ストレージ アカウントへのトラフィックをサービス エンドポイントを使用して制限していても、ストレージ アカウントへのアクセスには、やはりパブリック IP アドレスを使用します。

> [!NOTE]
> NFS 共有は、パブリック IP アドレスを使用してストレージ アカウントのパブリック エンドポイントにアクセスすることはできません。仮想ネットワークを使用した場合のみストレージ アカウントのパブリック エンドポイントにアクセスできます。 NFS 共有は、プライベート エンドポイントを使用してストレージ アカウントにアクセスすることもできます。

ストレージ アカウントのファイアウォールを構成する方法の詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

## <a name="encryption-in-transit"></a>転送中の暗号化

> [!IMPORTANT]
> このセクションでは、SMB 共有の転送中の暗号化について詳しく取り上げます。 NFS 共有による転送中の暗号化の詳細については、「[セキュリティ](storage-files-compare-protocols.md#security)」を参照してください。

既定では、すべての Azure ストレージ アカウントで転送中の暗号化が有効になっています。 つまり、SMB 経由でファイル共有をマウントするか、または FileREST プロトコル (Azure portal、PowerShell/CLI、Azure SDK など) 経由でファイル共有にアクセスすると、Azure Files では、暗号化または HTTPS が設定されている SMB 3.0 以上で作成された接続のみが許可されます。 SMB 3.0 をサポートしていないクライアント、または SMB 3.0 をサポートしているが、SMB 暗号化をサポートしていないクライアントは、転送中の暗号化が有効になっている場合は Azure ファイル共有をマウントできません。 どのオペレーティング システムが暗号化付き SMB 3.0 をサポートしているかの詳細については、[Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md)、および [Linux](storage-how-to-use-files-linux.md) に関する当社の詳細なドキュメントを参照してください。 PowerShell、CLI、および SDK の現在のバージョンはすべて HTTPS をサポートしています。  

Azure ストレージ アカウントでの転送中の暗号化を無効にすることができます。 暗号化が無効になっている場合、Azure Files では、SMB 2.1、暗号化なしの SMB 3.0、および HTTP 経由の暗号化されていない FileREST API 呼び出しも許可されます。 転送中の暗号化を無効にする主な理由は、古いオペレーティング システム (Windows Server 2008 R2 や古い Linux ディストリビューションなど) 上で実行する必要のあるレガシ アプリケーションをサポートするためです。 Azure Files では、Azure ファイル共有と同じ Azure リージョン内の SMB 2.1 接続のみが許可されます。Azure ファイル共有の Azure リージョンの外部 (オンプレミスまたは異なる Azure リージョン内など) の SMB 2.1 クライアントは、ファイル共有にアクセスできません。

転送中の暗号化の詳細については、「[Azure Storage で安全な転送が必要](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

## <a name="see-also"></a>関連項目
- [Azure Files の概要](storage-files-introduction.md)
- [Azure Files のデプロイの計画](storage-files-planning.md)