---
title: "Azure Stack 統合システムの計画に関する考慮事項 | Microsoft Docs"
description: "マルチノードの Azure Stack について今すぐ計画し、準備するためにできることについて説明します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: twooley
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 54d9ba1ac1e46843740b7dcec2c9bef80b2325b4
ms.contentlocale: ja-jp
ms.lasthandoff: 09/27/2017

---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Azure Stack 統合システムの計画に関する考慮事項

*適用先: Azure Stack 統合システム*

Azure Stack 統合システムに関心をお持ちの場合、デプロイ方法およびシステムとデータセンターの適合に関する計画の主な考慮事項について理解することをお勧めします。 このトピックでは、これらの考慮事項の概要を説明します。

統合システムの購入を決定した場合、相手先ブランド供給 (OEM) ハードウェア ベンダーから計画プロセスの大部分について詳しく案内があります。 実際のデプロイもベンダーが実行します。

## <a name="management-considerations"></a>管理の考慮事項

Azure Stack は、アクセス許可とネットワークの両方の観点から制限されたインフラストラクチャを持つシールド システムです。 ネットワーク アクセス制御リスト (ACL) を適用することで、承認されていないすべての受信トラフィックと、インフラストラクチャ コンポーネント間のすべての不要な通信をブロックします。 そのため、承認されていないユーザーがシステムにアクセスすることは困難です。

日常の管理と運用のために管理者がインフラストラクチャに無制限にアクセスすることはできません。 Azure Stack オペレーターは、管理者ポータルまたは Azure Resource Manager から (PowerShell または REST API を使用して) システムを管理する必要があります。 Hyper-V マネージャーやフェールオーバー クラスター マネージャーなどの他の管理ツールでシステムにアクセスすることはできません。 システム保護の目的で、サード パーティのソフトウェア (エージェントなど) を Azure Stack インフラストラクチャのコンポーネント内部にインストールすることはできないようになっています。 PowerShell または REST API を介して、管理とセキュリティ用の外部ソフトウェアとの相互運用性が提供されます。

アラート修正の手順を使用して解決できない問題のトラブルシューティングを行うために上位レベルのアクセス権が必要な場合は、サポートに問い合わせる必要があります。 サポートを通して、より高度な操作を実行するために、システムに対する一時的な管理者のフル権限を得る方法があります。 

## <a name="deploy-connected-or-disconnected"></a>接続モードまたは切断モードのデプロイ
 
Azure Stack をインターネットに (および Azure に) 接続した状態でデプロイするか、接続していない状態でデプロイするかを選択できます。 Azure Stack と Azure 間のハイブリッド シナリオを含めて、Azure Stack から最大のメリットを得るには、Azure に接続した状態でのデプロイをお勧めします。 次の表は、デプロイ モード間の主な違いをわかりやすくまとめたものです。

| 領域 | 接続モード | 切断モード |
| -------- | ------------- | ----------|
| ID プロバイダー | Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) | AD FS のみ |
| マーケットプレース シンジケーション | Azure Marketplace から直接 Azure Stack 内のマーケットプレースにアイテムをダウンロード | Azure Stack 内のマーケットプレースの手動による管理が必要 |
| ライセンス モデル | 従量課金制または容量ベース | 容量ベースのみ|
| パッチと更新プログラム  | Azure Stack に更新プログラム パッケージを直接ダウンロード | リムーバブル メディアと、別の接続されたデバイスが必要 |
| | | |

後からデプロイ モードを変更するには、システム全体を再デプロイする必要があります。

## <a name="identity-considerations"></a>ID に関する考慮事項

### <a name="choose-identity-provider"></a>ID プロバイダーの選択

Azure Stack のデプロイに Azure AD と AD FS のどちらの ID プロバイダーを使用するかを検討する必要があります。 デプロイ後に ID プロバイダーを切り替えるには、システム全体を再デプロイする必要があります。

ID プロバイダーの選択は、テナントの仮想マシン、ID システム、使用するアカウント、Active Directory ドメインに参加できるかどうかなどには関係しません。この選択とは独立しています。

**Azure AD の使用を検討する理由**

- Azure AD (Azure や Office 365 など) に対して既に投資している。
- Azure クラウドと Azure Stack クラウド全体で同じ ID を使用したい。
- 同じ 1 つの Azure Stack インスタンスで異なる複数の組織をホストできる、マルチテナント シナリオをサポートする必要がある。
- Azure AD Graph でユーザーやグループなどを API を介してプロビジョニングする、REST ベースのディレクトリ管理を行いたい。

> [!NOTE]
> Azure Stack のデプロイ時に Azure AD インスタンスと既存の AD FS インスタンスの両方に同時に接続することはできません。 Azure AD を使用してデプロイし、既存の AD FS インスタンスを使用する場合は、オンプレミスの AD FS インスタンスと Azure AD のフェデレーションを設定できます。

**AD FS の使用を検討する理由**

- インターネット接続が使用できないか、部分的な使用のみである。
- 規制または法的要件がある。
- オペレーターとユーザーのアカウントに独自の ID システム (会社の Active Directory など) を使用したい。 これを行うために、Active Directory に基づく既存の AD FS インスタンス (Windows Server 2012、2012 R2、または 2016) とのフェデレーションを設定できます。
- Azure への既存の投資があり、Azure AD の使用を希望しない。

> [!NOTE]
> Active Directory によってサポートされる別の AD FS インスタンスとのみ、Azure Stack のフェデレーションを設定できます。 その他の ID プロバイダーはサポートされていません。 Azure Stack で使用したい他の ID プロバイダーがある場合は、Azure AD ベースのデプロイを選択します。

### <a name="ad-fs-and-graph-integration"></a>AD FS と Graph の統合

ID プロバイダーとして AD FS を使用して Azure Stack をデプロイする場合は、フェデレーションの信頼関係を介して Azure Stack 上の AD FS インスタンスと既存の AD FS インスタンスを統合する必要があります。 これにより、既存の Active Directory フォレスト内の ID を Azure Stack 内のリソースに対して認証できます。

Azure Stack の Graph サービスを既存の Active Directory と統合することもできます。 これにより、Azure Stack でロールベースのアクセス制御 (RBAC) を管理できます。 リソースへのアクセスが委任されると、Graph のコンポーネントは LDAP プロトコルを使用して、既存の Active Directory フォレストでユーザー アカウントを検索します。

次の図は、統合された AD FS と Graph のトラフィック フローを示しています。
![AD FS と Graph のトラフィック フローの図](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>ライセンス モデル

使用するライセンス モデルを決める必要があります。 接続されたデプロイの場合、従量制ライセンスか容量ベースのライセンスを選択できます。 従量制では、Azure との通信を通じて課金される使用量を報告するために Azure に接続する必要があります。 インターネットに接続していないデプロイでは、容量ベースのライセンスのみがサポートされます。 ライセンス モデルの詳細については、「[Microsoft Azure Stack packaging and pricing](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)」 (Microsoft Azure Stack のパッケージと料金) を参照してください。

## <a name="naming-decisions"></a>名前付けの決定事項

Azure Stack の名前空間 (特にリージョン名) と外部のドメイン名を計画する方法について検討する必要があります。 Azure Stack のデプロイで公開されたエンドポイントの完全修飾ドメイン名 (FQDN) は、&lt;*region*&gt;&lt;*external_FQDN*&gt; という 2 つの名前の組み合わせです (たとえば *east.cloud.fabrikam.com*)。この例で Azure Stack ポータルは、次の URL で使用可能になります。

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

次の表は、これらのドメイン名前付けの決定事項をまとめたものです。

| 名前 | Description | 
| -------- | ------------- | 
|リージョン名 | 最初の Azure Stack リージョンの名前。 この名前は、Azure Stack によって管理されるパブリック仮想 IP アドレス (VIP) の FQDN の一部として使用されます。 通常、リージョン名は、データセンターの場所などの物理的な場所の識別子になります。 | 
| 外部ドメイン名 | 外部接続 VIP を持つエンドポイントのドメイン ネーム システム (DNS) ゾーンの名前。 パブリック VIP の FQDN に使用します。 | 
| プライベート (内部) ドメイン名 | インフラストラクチャ管理のために Azure Stack 上に作成されるドメイン (および内部の DNS ゾーン) の名前。 
| | |

## <a name="certificate-requirements"></a>証明書の要件

デプロイに使用するために、公開されたエンドポイントの Secure Sockets Layer (SSL) 証明書を提供する必要があります。 証明書には以下の要件があります。

- 1 つのワイルドカード証明書を使用するか、専用証明書のセットを使用することができます。記憶域や Key Vault などのエンドポイントに対してのみワイルドカードを使用できます。
- 証明書は信頼された公的証明機関 (CA) またはエンタープライズ CA によって発行されたものである必要があります。
- 中間 CA による証明書の発行はできません。
 
次の表は、最初の Azure Stack のデプロイで証明書が必要になるサービスと公開されたエンドポイントの数を示しています。 

| 用途 | エンドポイント 
| -------- | ------------- | 
| Azure Resource Manager (管理者) | adminmanagment.[region].[external_domain] |
| Azure Resource Manager (ユーザー) | management.[region].[external_domain] |
| ポータル (管理者) | adminportal.[region].[external_domain] |
| ポータル (ユーザー) | portal. [region].[external_domain] |
| Key Vault (ユーザー) | &#42;.vault.[region].[external_domain] |
| Key Vault (管理者) | &#42;.adminvault.[region].[external_domain] |
| Storage | &#42;blob.[region].[external_domain]<br>&#42;table.[region].[external_domain]<br>&#42;queue.[region].[external_domain]  |
| Graph** | graph.[region].[external_domain] |
| AD FS** | adfs.[region].[external_domain] |
| | |

**Graph および AD FS エンドポイントの証明書は、AD FS のデプロイ時にのみ必要です。

1 つのワイルドカード証明書を使用する場合は、最初の Azure Stack のデプロイ時に合計 6 つのサブジェクト代替名 (SAN) が必要です。 必要な SAN は次のとおりです。 

- &#42;.[region].[external_domain]
- &#42;.vault.[region].[external_domain]
- &#42;.adminvault.[region].[external_domain]
- &#42;.blob.[region].[external_domain]
- &#42;.table.[region].[external_domain] 
- &#42;.queue.[region].[external_domain]

## <a name="time-synchronization"></a>時刻同期

IP アドレスによる解決が可能な外部タイム サーバーと Azure Stack タイム サーバーを同期する必要があります。 切断されたデプロイの場合は企業ネットワーク上にタイム サーバーが必要です。

## <a name="network-connectivity"></a>ネットワーク接続

### <a name="dns-integration"></a>DNS の統合

Azure Stack から外部の DNS 名を解決するには (たとえば www.bing.com)、Azure Stack が権限のない DNS 要求の転送に使用できる DNS サーバーを提供する必要があります。 フォールト トレランスを確保するため、DNS 転送に使用するサーバーを少なくとも 2 つ指定する必要があります。

Azure Stack の外部から (たとえば、会社のフォレストから) Azure Stack エンドポイントの DNS 名を解決するには、Azure Stack 用の外部 DNS ゾーンをホストする DNS サーバーと、使用したい親ゾーンをホストする DNS サーバーを統合する必要があります。 そのためには、Azure Stack とデータセンター内の既存の DNS ゾーンの間で DNS 名を解決する必要があります。 これを実現するには、条件付き転送やゾーンの委任などの方法を使用します。 Azure Stack 外部 DNS 名前空間の親ゾーンをホストしている DNS サーバーを直接制御できる場合は、条件付き転送をお勧めします。 Azure Stack 外部 DNS ゾーンが会社ドメイン名の子ドメインになっている場合は (たとえば、azurestack.contoso.com と contoso.com)、ゾーンの委任の機能を使用する必要があります。

### <a name="network-infrastructure"></a>ネットワーク インフラストラクチャ

Azure Stack のネットワーク インフラストラクチャは、スイッチ上に構成されたいくつかの論理ネットワークから成ります。 それらの論理ネットワークを次の図に示し、各論理ネットワークと TOR (top-of-rack)、ベースボード管理コントローラー (BMC)、境界 (カスタマー ネットワーク) スイッチを統合する方法を説明します。

![論理ネットワーク図とスイッチ接続](media/azure-stack-planning-considerations/NetworkDiagram.png)

次の表は、論理ネットワークと、計画する必要がある関連付けらた IPv4 サブネット範囲を示します。

| 論理ネットワーク | Description | サイズ | 
| -------- | ------------- | ------------ | 
| パブリック VIP | 少数の Azure Stack サービスのパブリック IP アドレス (残りはテナント仮想マシンによって使用される)。 Azure Stack インフラストラクチャは、このネットワークから 32 個のアドレスを使用します。 App Service と SQL リソース プロバイダーを使用する場合は、さらに 7 個を使用します。 | /26 (62 ホスト) - /22 (1022 ホスト)<br><br>推奨 = /24 (254 ホスト) | 
| スイッチのインフラストラクチャ | ルーティングを目的としたポイント ツー ポイント IP アドレス (スイッチ管理専用インターフェイス) と、スイッチに割り当てられたループバック アドレス。 | /26 | 
| インフラストラクチャ | Azure Stack 内部コンポーネントの通信に使用します。 | /24 |
| プライベート | 記憶域ネットワークとプライベート VIP に使用します。 | /24 | 
| BMC | 物理ホスト上の BMC との通信に使用します。 | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>境界スイッチへのアップリンク

データセンター内の境界スイッチに対して構成したアップリンクが必要です。 Azure Stack 統合システムの一部である TOR (top-of-rack) スイッチから、次のどちらかの方法を使用して、このレイヤー 3 のトラフィックをルーティングできます。

- ボーダー ゲートウェイ プロトコル (BGP) 
- 静的ルーティング

BGP を使用すると、ソフトウェア負荷分散マルチプレクサー (SLB MUX) によって外部ネットワークに公開されるルートの自動更新が可能になるため、BGP をお勧めします。 これは、デプロイ後にパブリック IP アドレス範囲を追加する場合に重要です。 TOR スイッチから、TOR スイッチが接続されている集合スイッチへの BGP ピアリングを行う場合、デプロイ後に追加するパブリック IP アドレス範囲は集合スイッチに自動的に公開され、手動による介入は発生しません。 静的ルーティングを使用する場合は、パブリック IP アドレスのブロックを追加するたびに、新しい範囲のルートを手動で更新する必要があります。

### <a name="proxy-server"></a>プロキシ サーバー

Azure Stack は、透過的なプロキシ サーバーのみをサポートします。 透過プロキシは、ネットワーク層で要求をインターセプトします。特別なクライアント構成は不要です。

### <a name="publish-azure-stack-services"></a>Azure Stack サービスの公開

Azure Stack の外部のユーザーに対して Azure Stack サービスを使用可能にする必要があります。 Azure Stack は、さまざまなエンドポイントをそのインフラストラクチャ ロールに応じて設定します。 それらのエンドポイントには、パブリック IP アドレス プールから VIP が割り当てられます。 デプロイ時に指定した外部 DNS ゾーン内のエンドポイントごとに DNS エントリが作成されます。 たとえば、ユーザー ポータルに "portal.<*region*>.<*external_FQDN*>" という DNS ホスト エントリが割り当てられます。 

#### <a name="ports-and-urls"></a>ポートと URL

Azure Stack サービス (ポータル、Azure Resource Manager、DNS など) を外部ネットワークに対して使用可能にするには、特定の URL、ポート、プロトコルに対して、これらのエンドポイントへの受信トラフィックを許可する必要があります。
 
透過プロキシから従来のプロキシ サーバーへのアップリンクが存在するデプロイ環境では、特定のポートと URL に外部への通信を許可する必要があります。 これには、ID、マーケットプレース シンジケーション、パッチと更新プログラム、登録、使用状況データに使用するポートと URL が該当します。

詳細については、次を参照してください。
- [受信用のポートとプロトコル](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [送信用のポートと URL](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Azure への Azure Stack の接続

ハイブリッド クラウド シナリオでは、Azure Stack を Azure に接続する方法を計画する必要があります。 Azure Stack 内の仮想ネットワークを Azure 内の仮想ネットワークに接続する方法は 2 つあります。 
- **サイト間**。 IPsec (IKE v1 および IKE v2) 経由の仮想プライベート ネットワーク (VPN) 接続。 この種の接続には、VPN デバイスまたは RRAS (ルーティングとリモート アクセス サービス) が必要です。 Azure での VPN Gateway の詳細については、「[VPN Gateway について](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)」を参照してください。 このトンネル経由の通信は暗号化されており、安全です。 ただし、帯域幅はトンネルの最大スループット (100 - 200 Mbps) によって制限されます。
- **送信 NAT**。 既定では、Azure Stack 内のすべての仮想マシンが送信 NAT を経由して外部ネットワークに接続できます。 Azure Stack 内に作成される各仮想ネットワークには、パブリック IP アドレスが割り当てられます。 仮想マシンにパブリック IP アドレスが直接割り当てられているか、パブリック IP アドレスを持つロード バランサーを介しているかにかかわらず、仮想ネットワークの VIP を使用して送信 NAT 経由の送信アクセスが可能です。 これは、仮想マシンによって開始され、外部ネットワーク (インターネットまたはイントラネット) を接続先とする通信についてのみ有効です。 外部から仮想マシンと通信するために使用することはできません。

#### <a name="hybrid-connectivity-options"></a>ハイブリッド接続のオプション

ハイブリッド接続に関して重要なことは、提供するデプロイの種類とデプロイ先の場所です。 ネットワーク トラフィックをテナントごとに分離する必要があるかどうか、イントラネットまたはインターネットをデプロイするかどうかを検討する必要があります。

- **シングル テナントの Azure Stack**。 少なくともネットワークの観点からは 1 つのテナントと見なされる Azure Stack のデプロイです。 多数のテナント サブスクリプションが存在できますが、イントラネット サービスと同様に、すべてのトラフィックが同じネットワークを経由して送信されます。 1 つのサブスクリプションからのネットワーク トラフィックは、別のサブスクリプションと同じネットワーク接続を経由して送信されます。暗号化されたトンネルを使用して分離する必要はありません。

- **マルチテナントの Azure Stack**。 テナント サブスクリプションごとに、Azure Stack の外部ネットワークを送信先とするトラフィックを他のテナントのネットワーク トラフィックから分離する必要がある Azure Stack のデプロイです。
 
- **イントラネット デプロイ**。 一般的に 1 つ以上のファイアウォールの内側にあるプライベート IP アドレス空間である企業のイントラネット上に配置される Azure Stack のデプロイです。 パブリック IP アドレスは、パブリック インターネット経由で直接ルーティングできないため、実際にはパブリックではありません。

- **インターネット デプロイ**。 パグリック インターネットに接続し、インターネットでルーティング可能なパブリック IP アドレスをパブリック VIP 範囲として使用する Azure Stack のデプロイです。 このデプロイはファイアウォールの内側への配置が可能でありながら、パブリック VIP 範囲はパブリック インターネットおよび Azure から直接到達可能です。
 
次の表は、ハイブリッド接続のシナリオの長所と短所、ユース ケースをまとめたものです。

| シナリオ | 接続方法 | 長所 | 短所 | 適した用途 |
| -- | -- | --| -- | --|
| シングル テナントの Azure Stack、イントラネット デプロイ | 送信 NAT | 帯域幅向上による転送の高速化。 実装が簡単。ゲートウェイは必要ありません。 | トラフィックは暗号化されません。TOR より先の分離や暗号化が行われません。 | すべてのテナントが平等に信頼されているエンタープライズ デプロイ。<br><br>Azure への Azure ExpressRoute 回線を所有している企業。 |
| マルチテナントの Azure Stack、イントラネット デプロイ | サイト間 VPN | テナント VNet から送信先へのトラフィックはセキュリティで保護されます。 | 帯域幅がサイト間 VPN トンネルによって制限されます。<br><br>仮想ネットワークにゲートウェイが、また接続先ネットワークに VPN デバイスが必要です。 | 一部のテナント トラフィックを他のテナントから分離して保護する必要があるエンタープライズ デプロイ。 |
| シングル テナントの Azure Stack、インターネット デプロイ | 送信 NAT | 帯域幅向上による転送の高速化。 | トラフィックは暗号化されません。TOR より先の分離や暗号化が行われません。 | テナント専用に Azure Stack をデプロイし、Azure Stack 環境への専用回線を使用するホスティングのシナリオ。 例として、ExpressRoute とマルチプロトコル ラベル スイッチング (MPLS) があります。
| マルチテナントの Azure Stack、インターネット デプロイ | サイト間 VPN | テナント VNet から送信先へのトラフィックはセキュリティで保護されます。 | 帯域幅がサイト間 VPN トンネルによって制限されます。<br><br>仮想ネットワークにゲートウェイが、また接続先ネットワークに VPN デバイスが必要です。 | プロバイダーがマルチテナント クラウドを提供する必要があるホスティング シナリオ。テナント相互の信頼関係がないため、トラフィックを暗号化する必要があります。
|  |  |  |  |  |

#### <a name="using-expressroute"></a>ExpressRoute の使用

シングル テナントのイントラネットとマルチテナントの両方のシナリオで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) を経由して Azure Stack を Azure に接続することができます。 [接続プロバイダー](https://docs.microsoft.com/azure/expressroute/expressroute-locations)経由でプロビジョニングされている ExpressRoute 回線が必要です。

次の図は、シングル テナント シナリオの ExpressRoute ("顧客の接続" が ExpressRoute 回線) を示しています。

![シングル テナントの ExpressRoute シナリオを示した図](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

次の図は、マルチテナント シナリオの ExpressRoute を示しています。

![マルチテナントの ExpressRoute シナリオを示した図](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>外部の監視
Azure Stack のデプロイとデバイスからすべてのアラートを 1 つのビューとして取得し、チケット発行用の既存の IT サービス管理ワークフローにアラートを統合するために、外部のデータセンター監視ソリューションと Azure Stack を統合することができます。

Azure Stack ソリューションに含まれているハードウェア ライフサイクル ホストは、Azure Stack の外部にあるコンピューターで、ハードウェアを対象にした OEM ベンダー提供の管理ツールを実行しています。 それらのツールを使用することも、データセンター内の既存の監視ソリューションに直接統合されたソリューションを使用することもできます。

次の表は、現在、使用可能なオプションの一覧です。

| 領域 | 外部の監視ソリューション |
| -- | -- |
| Azure Stack ソフトウェア | - [Operations Manager 用 Azure Stack 管理パック](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios プラグイン](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>- REST ベースの API 呼び出し | 
| 物理サーバー (IPMI 経由 BMC) | - Operations Manager ベンダー管理パック<br>- OEM ハードウェア ベンダー提供のソリューション<br>- ハードウェア ベンダーの Nagios プラグイン | OEM パートナーがサポートしている監視ソリューション (含まれている) | 
| ネットワーク デバイス (SNMP) | - Operations Manager ネットワーク デバイスの検出<br>- OEM ハードウェア ベンダー提供のソリューション<br>- Nagios スイッチ プラグイン |
| テナント サブスクリプションの正常性の監視 | - [Windows Azure 用 System Center 管理パック](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

以下の要件に注意してください。
- 使用するソリューションは、エージェントレスである必要があります。 Azure Stack コンポーネント内にサード パーティ製エージェントをインストールすることはできません。 
- System Center Operations Manager を使用する場合は、Operations Manager 2012 R2 または Operations Manager 2016 が必要です。

## <a name="backup-and-disaster-recovery"></a>バックアップと障害復旧

バックアップとディザスター リカバリーの計画では、次の両方を計画に含める必要があります。つまり、基になる Azure Stack インフラストラクチャ (IaaS 仮想マシンと PaaS サービスをホストする) と、テナント アプリケーションとデータです。 これらの計画は個別に行う必要があります。

### <a name="protect-infrastructure-components"></a>インフラストラクチャ コンポーネントの保護

Azure Stack では、指定した共有にインフラストラクチャのコンポーネントがバックアップされます。

- 既存の Windows ベース ファイル サーバーまたはサード パーティのデバイス上に、外部 SMB ファイル共有が必要です。
- この同じ共有を、ネットワーク スイッチとハードウェア ライフサイクル ホストのバックアップに使用する必要があります。 これらのコンポーネントは Azure Stack の外部にあるため、これらのコンポーネントのバックアップと復元については、OEM ハードウェア ベンダーに問い合わせてください。 OEM ベンダーの推奨に基づいたバックアップ ワークフローを実行することは、お客様の責任となります。

致命的なデータ消失が発生した場合は、インフラストラクチャのバックアップを使用してデプロイ データを再生成します。デプロイ データには、デプロイの入力データや ID、サービス アカウント、CA ルート証明書、フェデレーション リソース (切断モードでのデプロイ時)、プラン、オファー、サブスクリプション、クォータ、RBAC ポリシー、ロール割り当て、Key Vault のシークレットなどが含まれます。
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>IaaS 仮想マシン上のテナント アプリケーションの保護

Azure Stack でテナント アプリケーションとデータはバックアップされません。 Azure Stack の外部にあるターゲットに対するバックアップとディザスター リカバリーの保護を計画する必要があります。 テナントの保護は、テナント ドリブン アクティビティです。 IaaS 仮想マシンに対しては、テナントはゲスト内テクノロジを使用して、ファイル フォルダー、アプリケーション データ、システム状態を保護できます。 ただし、エンタープライズまたはサービス プロバイダーの場合は、同じデータセンター内または外部のクラウドでバックアップと復元のソリューションを提供することができます。

Linux または Windows の IaaS 仮想マシンをバックアップするには、ゲスト オペレーティング システムへのアクセス権を持つバックアップ製品を使用して、ファイル、フォルダー、オペレーティング システムの状態、アプリケーション データを保護する必要があります。 Azure Backup、System Center Data Center Protection Manager、またはサポートされているサード パーティ製品を使用できます。

セカンダリ ロケーションにデータをレプリケートし、障害発生時のアプリケーションのフェールオーバーを調整するには、Azure Site Recovery またはサポートされているサード パーティの製品を使用することができます。 (統合システムの最初のリリースで、Azure Site Recovery でフェールバックはサポートされません。 ただし、手動プロセスでフェールバックを実現できます。)また、ネイティブ レプリケーション (Microsoft SQL Server など) をサポートするアプリケーションは、アプリケーションが実行されている別の場所にデータをレプリケートできます。

> [!IMPORTANT]
> 統合システムの最初のリリースで、IaaS 仮想マシンのゲスト レベルで機能する保護テクノロジがサポートされます。 基になるインフラストラクチャ サーバーにエージェントをインストールすることはできません。

## <a name="next-steps"></a>次のステップ

- ユース ケース、購入、パートナー、OEM ハードウェア ベンダーの詳細については、[Azure Stack](https://azure.microsoft.com/overview/azure-stack/) の製品ページを参照してください。
- Azure Stack 統合システムのロードマップと地理的な可用性については、ホワイト ペーパー「[Azure Stack: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)」 (Azure Stack: Azure の拡張機能) を参照してください。 

