---
title: Azure のための信頼できるインターネット接続ガイダンス
description: Azure と SaaS サービスのための信頼できるインターネット接続ガイダンス
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: bb186ab2700b147bee3a7dd81474409ccafb76fc
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341720"
---
# <a name="trusted-internet-connections-guidance"></a>信頼できるインターネット接続のガイダンス

この記事では、政府機関が Microsoft Azure Government を使用して Trusted Internet Connections (TIC) イニシアティブに準拠する方法について説明します。 この記事では、Azure サービスとしてのインフラストラクチャ (IaaS) オファリングおよび Azure サービスとしてのプラットフォーム (PaaS) オファリングで Azure Government を使用する方法を説明します。

## <a name="trusted-internet-connections-overview"></a>信頼できるインターネット接続

TIC イニシアティブの目的は、連邦機関によって利用されている個々の外部ネットワーク接続のセキュリティを最適化し、標準化することです。 この方針の概要は、[行政管理予算局 (OMB) の覚書 M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) にあります。

2007 年 11 月、OMB は連邦ネットワーク周辺のセキュリティとインシデントに対する対応機能を改善する目的で TIC プログラムを作成しました。 TIC は、.gov のあらゆる送受信トラフィックをネットワーク分析し、特定の署名とパターンに基づくデータを識別するように設計されています。 TIC はボットネット アクティビティなどの異常な行動を発見します。 政府機関は、外部のネットワーク接続を統合し、EINSTEIN と呼ばれる不正侵入検出および防止デバイスにすべてのトラフィックをルーティングすることを義務付けられています。 デバイスは、"_信頼できるインターネット接続_" と呼ばれる限定された数のネットワーク エンドポイントでホストされています。

TIC の目的は政府機関が次のことを認識することです。
- 誰が自分たちのネットワーク上にいるのか (承認されているか、無許可か)?
- 自分たちのネットワークはいつ、どのような理由でアクセスされているのか?
- どのようなリソースがアクセスされているのか?

現在、政府機関のすべての外部接続は、OMB が承認した TIC を通過する必要があります。 連邦機関は、TIC アクセス プロバイダー (TICAP) として、または主要なティア 1 インターネット サービス プロバイダーの 1 つとサービス契約することで、TIC プログラムに参加する必要があります。 これらのプロバイダーは、Managed Trusted Internet Protocol Service (MTIPS) プロバイダーと呼ばれます。 TIC には重要な必須の機能が含まれ、それは政府機関と MTIPS プロバイダーによって実行されています。 現在のバージョンの TIC では、EINSTEIN バージョン 2 の侵入検出および EINSTEIN バージョン 3 の高速 (3A) 侵入防止デバイスが、各 TICAP と MTIPS にデプロイされています。 政府機関は、国土安全保障省 (DHS) と "合意の覚書" を結び、連邦システムに EINSTEIN の機能をデプロイします。

.gov ネットワークの保護というその責任の一環として、DHS は連邦エンタープライズ全体のインシデントを関連付け、特別なツールで分析する目的で、機関の未加工ネット フロー データのフィードを要求します。 DHS ルーターは、インターフェイスに出入りする IP ネットワーク トラフィックを収集する機能を提供します。 ネットワーク管理者は、ネット フロー データを分析して、トラフィックの送信元と送信先、サービスのクラスなどを特定できます。 ネット フロー データは、ヘッダー、送信元 IP、送信先 IP などのような "コンテンツ以外のデータ" と見なされます。 コンテンツ以外のデータにより、DHS は、だれが何をどのくらいの時間行っていたか、といったコンテンツに関する情報を把握できます。

このイニシアティブには、セキュリティに関する方針、ガイドライン、オンプレミス インフラストラクチャを前提とするフレームワークも含まれています。 政府機関がコスト削減、運用効率、技術革新を達成するためにクラウドに移行すると、TIC の実装要件によりネットワーク トラフィックが遅くなることがあります。 その結果、政府ユーザーがクラウドベース データにアクセスするときのスピードとアジャイル性が制限されます。

## <a name="azure-networking-options"></a>Azure のネットワーク オプション

Azure サービスに接続するとき、主なオプションが 3 つあります。

- 直接インターネット接続: オープンなインターネット接続を介して Azure サービスに直接接続します。 媒体と接続はパブリックです。 プライバシーの確保はアプリケーションとトランスポートのレベルの暗号化に依存します。 帯域幅は、インターネットへのサイトの接続状態によって制限されます。 複数のアクティブなプロバイダーを使用して、回復性を確保します。
- 仮想プライベート ネットワーク (VPN): VPN ゲートウェイを使って、Azure 仮想ネットワークにプライベートに接続します。
サイトの標準インターネット接続を通過するため媒体はパブリックですが、トンネルの接続は暗号化されてプライバシーが確保されます。 帯域幅の上限は、選択した VPN デバイスと構成に依存します。 通常、Azure のポイント対サイト接続は 100 Mbps に制限され、サイト対サイト接続は 1.25 Gbps に制限されます。
- Azure ExpressRoute: ExpressRoute の場合、Microsoft のサービスに直接接続します。 分離されたファイバー チャネルを介するため、この接続は、使用される構成に基づいてパブリックまたはプライベートになります。 帯域幅は一般的に最大 10 Gbps に制限されます。

DHS の "Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0" で指定されている TIC 付録 H (クラウドに関する考慮事項) 要件は、複数の方法で満たすことができます。 この記事では、DHS TIC ガイダンスを **TIC 2.0** と呼びます。

**省庁または機関 (D/A)** の TIC を通過するようにトラフィックをルーティングすることなく、D/A から Azure または Office 365 への接続を有効にするには、D/A は暗号化トンネルかクラウド サービス プロバイダー (CSP) への専用接続を利用する必要があります。 CSP サービスは、機関の人間に直接アクセスするとき、D/A クラウド アセットへの接続が公共のインターネットに確実に提供されないようにします。

Office 365 は、[Microsoft ピアリング](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings)を有効にして ExpressRoute を使用するか、TLS 1.2 ですべてのトラフィックを暗号化するインターネット接続を使用して TIC 2.0 付録 H に準拠します。 D/A ネットワークの D/A エンド ユーザーは、機関ネットワークと TIC インフラストラクチャを介してインターネット接続できます。 Office 365 へのリモート インターネット アクセスはすべてブロックされ、機関を通過するようにルーティングされます。 D/A は Microsoft ピアリングを有効にした ExpressRoute で Office 365 に接続することもできます。Microsoft ピアリングはパブリック ピアリングの一種です。  

Azure の場合のみ、2 番目のオプション (VPN) と 3 番目のオプション (ExpressRoute) は、インターネットへのアクセスを制限するサービスと併用するとき、これらの要件を満たすことができます。

![Microsoft による信頼できるインターネット接続 (TIC)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Azure サービスとしてのインフラストラクチャ オファリング

Azure IaaS を使用して TIC ポリシーに準拠する方法は比較的シンプルです。Azure の顧客が自分の仮想ネットワークのルーティングを管理するからです。

TIC 参照アーキテクチャに準拠するための主な要件は、仮想ネットワークが D/A ネットワークのプライベートな拡張になるようにすることです。 "_プライベート_" な拡張にするためには、オンプレミスの TIC ネットワーク接続経由以外のトラフィックをネットワークから出さないようにすることをポリシーで要求します。 このプロセスは "_強制トンネリング_" と呼ばれます。 TIC コンプライアンスの場合、CSP 環境にあるあらゆるシステムからのトラフィックをすべて、組織のネットワークにあるオンプレミス ゲートウェイを通過し、TIC でインターネットに入るようにルーティングするプロセスとなります。

Azure IaaS TIC コンプライアンスは大きく 2 つの手順に分かれます。

- 手順 1: 構成。
- 手順 2: 監査。

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS TIC コンプライアンス: 構成

Azure で TIC 準拠アーキテクチャを構成するには、まず、仮想ネットワークへの直接的なインターネット アクセスを防ぎ、それからオンプレミス ネットワークを通過するようにインターネット トラフィックに強制する必要があります。

#### <a name="prevent-direct-internet-access"></a>直接インターネット アクセスの禁止

Azure IaaS ネットワーキングは、サブネットから構成される仮想ネットワーク経由で実施されます。このサブネットには、仮想マシンの NIC (ネットワーク インターフェイス コントローラー) が関連付けられます。

TIC コンプライアンスをサポートする最も単純なシナリオは、仮想マシンまたは仮想マシンの集合があらゆる外部リソースに接続できなくすることです。 ネットワーク セキュリティ グループ (NSG) を使用して外部ネットワークからの切断を確保します。 NSG を使用して、仮想ネットワーク内の 1 つまたは複数の NIC またはサブネットにトラフィックを制御します。 NSG には、トラフィックの方向、プロトコル、ソース アドレスとポート、および送信先アドレスとポートに基づいて、トラフィックを許可または拒否するアクセス制御ルールが含まれています。 NSG のルールは、いつでも変更でき、変更は関連付けられているすべてのインスタンスに適用されます。 NSG の作成方法の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)に関するページをご覧ください。

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>オンプレミス ネットワークを通過することをインターネット トラフィックに強制する

Azure では、システム ルートが自動的に作成され、仮想ネットワークの各サブネットに割り当てられます。 システム ルートを作成または削除することはできませんが、システム ルートをカスタム ルートでオーバーライドすることはできます。 Azure では、サブネットごとに既定のシステム ルートが作成されます。 Azure の特定の機能を使用するときは、特定のサブネットまたはすべてのサブネットへのオプションの既定のルートが追加されます。 この種類のルーティングにより次のことを確実にします。
- 仮想ネットワーク内に送信先があるトラフィックは、その仮想ネットワーク内のみで通信が行われます。
- IANA で指定されたプライベート アドレス空間 (10.0.0.0/8 など) は、仮想ネットワーク アドレス空間に含まれている場合を除き、削除されます。
- "最後の手段" である仮想ネットワーク インターネット エンドポイントへの 0.0.0.0/0 のルーティング。

![TIC のトンネリング強制](media/tic-diagram-c.png)

すべてのトラフィックが D/A TIC を通過するには、仮想ネットワークから出るすべてのトラフィックがオンプレミス接続を確実に通過するようにします。 カスタム ルートを作成するには、ユーザー定義ルートを作成するか、オンプレミス ネットワーク ゲートウェイと Azure VPN ゲートウェイの間で Border Gateway Protocol (BGP) ルートを交換します。 ユーザー定義のルートについて詳しくは、[仮想ネットワーク トラフィックのルーティングのユーザー定義ルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)に関するページをご覧ください。 BGP について詳しくは、[仮想ネットワーク トラフィックのルーティングの Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) に関するページをご覧ください。

#### <a name="add-user-defined-routes"></a>ユーザー定義ルートを追加する

ルートベースの仮想ネットワーク ゲートウェイを使用している場合、Azure でトンネリングを強制することができます。 0.0.0.0/0 トラフィックが仮想ネットワーク ゲートウェイの**次のホップ**にルーティングされるように設定するユーザー定義ルート (UDR) を追加します。 Azure はシステム定義ルートよりユーザー定義ルートを優先させます。 すべての非仮想ネットワーク トラフィックは仮想ネットワーク ゲートウェイに送信され、仮想ネットワーク ゲートウェイはトラフィックをオンプレミスにルーティングできます。 UDR を定義した後、Azure 環境内にあるすべての仮想ネットワーク内の既存のサブネットまたは新しいサブネットに、ルートを関連付けます。

![ユーザー定義ルートと TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Border Gateway Protocol を使用する

ExpressRoute または BGP 対応の仮想ネットワーク ゲートウェイを使用している場合、ルートをアドバタイズするためのメカニズムとしては BGP が推奨されます。 BGP がルート 0.0.0.0/0 をアドバタイズする場合、ExpressRoute と BGP 対応仮想ネットワーク ゲートウェイは、このデフォルト ルートが仮想ネットワーク内のすべてのサブネットに確実に適用されるようにします。

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS TIC コンプライアンス: 監査

Azure では、いくつかの方法で TIC コンプライアンスを監査できます。

#### <a name="view-effective-routes"></a>有効なルートの表示

[Azure portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) または [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell) で、特定の仮想マシン、特定の NIC、またはユーザー定義ルート テーブルに対する "_有効なルート_" を観察することにより、既定のルートが伝達されていることを確認します。 **[有効なルート]** には、関連するエンティティに適用される関連ユーザー定義ルート、BGP アドバタイズ ルート、システム ルートが表示されます (下の画像を参照)。

![有効なルート](media/tic-screen-1.png)

> [!NOTE]
> NIC が実行中の仮想マシンに関連付けられていない場合、NIC の有効なルートは表示できません。

#### <a name="use-azure-network-watcher"></a>Azure Network Watcher を使用する

Azure Network Watcher には、TIC コンプライアンスを監査するためのツールがいくつかあります。 詳しくは、[Network Watcher に関するこちらの概要](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)をご覧ください。

##### <a name="capture-network-security-group-flow-logs"></a>ネットワーク セキュリティ グループのフロー ログをキャプチャする 

IP トラフィック関連のメタデータを示すネットワーク フロー ログをキャプチャするには、Network Watcher を使用します。 ネットワーク フロー ログには、ターゲットの送信元アドレスと送信先アドレスや、その他のデータが含まれます。 このデータと、仮想ネットワーク ゲートウェイ、オンプレミスのエッジ デバイス、または TIC からのログを使用して、すべてのトラフィックがオンプレミスにルーティングされることを監視できます。 

## <a name="azure-platform-as-a-service-offerings"></a>Azure サービスとしてのプラットフォームのオファリング

Azure Storage などの Azure PaaS サービスにはインターネットでアクセス可能な URL を介してアクセスできます。 承認された資格情報が与えられた人なら誰でも、TIC を通過しなくても、あらゆる場所からストレージ アカウントなどのリソースにアクセスできます。 そのような理由から、政府関連の顧客の多くは、Azure PaaS サービスが TIC ポリシーに準拠していないという誤った判断を下します。 多くの Azure PaaS サービスは、TIC ポリシーに準拠できます。 サービスが準拠しているのは、アーキテクチャが TIC に準拠する IaaS 環境 ([前述の説明を参照](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) と同じであり、サービスが Azure 仮想ネットワークに接続されている場合です。

Azure PaaS サービスが仮想ネットワークと統合されると、その仮想ネットワークからサービスにプライベート アクセスできます。 ユーザー定義ルートまたは BGP 経由で、0.0.0.0/0 に対するカスタム ルーティングを適用できます。 カスタム ルーティングにより、インターネットに送信されるすべてのトラフィックがオンプレミスにルーティングされて TIC を走査することが確実になります。 仮想ネットワークに Azure サービスを統合するには、次のパターンを使用します。

- **サービスの専用インスタンスをデプロイする**: 仮想ネットワーク接続エンドポイントを使用し、専用インスタンスとしてデプロイできる PaaS サービスが増えています。 PowerApps 用 App Service Environment を "分離" モードでデプロイし、ネットワーク エンドポイントを仮想ネットワークに制約することができます。 App Service Environment は、Azure Web Apps、Azure API Management、Azure Functions など、多くの Azure PaaS サービスをホストできます。
- **仮想ネットワーク サービス エンドポイントを使用する**: エンドポイントをパブリック アドレスではなく仮想ネットワークのプライベート IP に移動できる PaaS サービスが増えています。

2018 年 5 月の時点で、仮想ネットワークへの専用インスタンスのデプロイまたはサービス エンドポイントの使用をサポートしているサービスを、次の表に示します。

> [!Note]
> 利用可能かどうかの状態は、販売されている Azure サービスに対応します。 Azure Government での Azure サービスの利用可能性の状態は、保留中です。

### <a name="support-for-service-endpoints"></a>サービス エンドポイントのサポート

|Service                        |可用性      |
|-------------------------------|------------------|
|Azure Key Vault                | プライベート プレビュー  |
|Azure Cosmos DB                | プライベート プレビュー  |
|ID サービス              | プライベート プレビュー  |
|Azure Data Lake                | プライベート プレビュー  |
|Azure Database for PostgreSQL  | プライベート プレビュー  |
|Azure Database for MySQL       | プライベート プレビュー  |
|Azure SQL Data Warehouse       | パブリック プレビュー   |
|Azure SQL Database             | 一般公開 (GA) |
|Azure Storage                  | 一般公開               |

### <a name="support-for-virtual-network-injection"></a>仮想ネットワークの挿入のサポート

|Service                               |可用性      |
|--------------------------------------|------------------|
|Azure SQL Database Managed Instance   | パブリック プレビュー   |
|Azure Kubernetes Service (AKS)        | パブリック プレビュー   |
|Azure Service Fabric                  | 一般公開               |
|Azure API Management                  | 一般公開               |
|Azure Active Directory                | 一般公開               |
|Azure Batch                           | 一般公開               |
|App Service 環境               | 一般公開               |
|Azure Cache for Redis                     | 一般公開               |
|Azure HDInsight                       | 一般公開               |
|仮想マシン スケール セット             | 一般公開               |
|Azure クラウド サービス                  | 一般公開               |


### <a name="virtual-network-integration-details"></a>仮想ネットワークの統合の詳細

次の図では、PaaS サービスにアクセスするための一般的なネットワーク フローを示します。 仮想ネットワーク挿入と仮想ネットワーク サービス トンネリングの両方からのアクセスを示しています。 ネットワーク サービス ゲートウェイ、仮想ネットワーク、サービス タグについて詳しくは、[ネットワークとアプリケーションのセキュリティ グループのサービス タグ](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)に関するページをご覧ください。

![TIC の PaaS 接続オプション](media/tic-diagram-e.png)

1. Azure へのプライベート接続は ExpressRoute を使用して行われます。 ExpressRoute プライベート ピアリングと強制トンネリングを使用して、顧客のすべての仮想ネットワーク トラフィックが ExpressRoute を経由し、オンプレミスに戻るように強制します。 Microsoft ピアリングは必要ありません。
2. Azure VPN Gateway を ExpressRoute および Microsoft ピアリングと併用すると、顧客の仮想ネットワークとオンプレミス エッジの間でエンドツーエンドの IPSec 暗号化をオーバーレイできます。 
3. 顧客の仮想ネットワークへのネットワーク接続は、顧客が IP、ポート、プロトコルに基づいて許可/拒否できる NSG を使用して制御されます。
4. 顧客の仮想ネットワークは、顧客のサービス用のサービス エンドポイントを作成することにより、PaaS サービスまで拡張されます。
5. PaaS サービス エンドポイントはセキュリティで保護されており、**すべてのアクセスが既定で拒否され**、顧客の仮想ネットワーク内の指定されたサブネットからのアクセスのみが許可されます。 既定での拒否には、インターネットからの接続も含まれます。
6. 顧客の仮想ネットワーク内のリソースにアクセスする必要があるその他の Azure サービスには、次のいずれかであることが求められます。  
   - 仮想ネットワークに直接デプロイされている。
   - それぞれの Azure サービスからのガイダンスに基づいて選択的に許可されている。

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>オプション A: サービスの専用インスタンスをデプロイする (仮想ネットワークの挿入)

仮想ネットワークの挿入を使用すると、顧客は、HDInsight などの特定の Azure サービスの専用インスタンスを、独自の仮想ネットワークに選択的にデプロイできます。 サービス インスタンスは、顧客の仮想ネットワークの専用サブネットにデプロイされます。 仮想ネットワークの挿入により、インターネット以外のルーティング可能アドレス経由でサービス リソースにアクセスできます。 オンプレミスのインスタンスは、インターネットのパブリック アドレス空間に対してファイアウォールを開く代わりに、ExpressRoute またはサイト間 VPN を使用して、仮想ネットワーク アドレス空間経由でサービス インスタンスに直接アクセスします。 専用インスタンスがエンドポイントに関連付けられている場合は、IaaS TIC コンプライアンスと同じ戦略を使用できます。 既定のルーティングにより、インターネットへのトラフィックはオンプレミスにバインドされた仮想ネットワーク ゲートウェイに確実にリダイレクトされます。 さらに、特定のサブネットに対する NSG を使用して受信および送信のアクセスを制御できます。

![仮想ネットワークの挿入の概要](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>オプション B: 仮想ネットワーク サービス エンドポイントを使用する (サービス トンネル)

"サービス エンドポイント" を提供する Azure マルチテナント サービスの数が増えています。 サービス エンドポイントは、Azure 仮想ネットワークに統合するためのもう 1 つの方法です。 仮想ネットワーク サービス エンドポイントは、仮想ネットワークの IP アドレス空間と仮想ネットワークの ID を、直接接続によってサービスまで拡張します。 仮想ネットワークから Azure サービスへのトラフィックは常に、Azure のバックボーン ネットワーク内に留まります。 

サービスのサービス エンドポイントを有効にした後は、そのサービスによって公開されるポリシーを使用して、サービスへの接続をその仮想ネットワークに制限します。 プラットフォームでは Azure サービスによってアクセス チェックが強制されます。 ロックされているリソースへのアクセスは、許可されている仮想ネットワークまたはサブネットから要求が送信された場合、または ExpressRoute を使用しているときは、オンプレミスのトラフィックを示すために使われている 2 つの IP から送信された場合にのみ、許可されます。 この方法を使って、受信/送信トラフィックが PaaS サービスから直接離れることを効果的に防ぎます。

![サービス エンドポイントの概要](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>ネットワークの状況認識のためのクラウド ネイティブ ツール

Azure には、ネットワークのトラフィック フローを理解するために必要な状況認識の確保に役立つクラウド ネイティブのツールがあります。 これらのツールは、TIC ポリシーに準拠するためには不要です。 これらのツールにより、セキュリティ機能を大幅に向上させることができます。

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) は、コンプライアンス イニシアティブを監査し、強制するためのすぐれた機能を組織に与える Azure サービスです。 お客様は、今のうちに Azure Policy のルールを計画およびテストしておき、後で TIC に確実に準拠できます。

Azure Policy は、サブスクリプション レベルを対象としています。 サービスが提供する一元的なインターフェイスを使用して、次のようなコンプライアンス タスクを実行できます。
- イニシアティブを管理する
- ポリシー定義を構成する
- コンプライアンスを監査する
- コンプライアンスを適用する
- 例外を管理する

管理者は、多くの組み込み定義に加えて、シンプルな JSON テンプレートを使用して独自のカスタム定義を定義できます。 Microsoft は、可能な限り、強制より監査を優先させることをお勧めします。

次のサンプル ポリシーを TIC コンプライアンス シナリオに使用できます。

|ポリシー  |サンプル シナリオ  |Template  |
|---------|---------|---------|
|ユーザー定義ルート テーブルを強制する。 | すべての仮想ネットワークの既定のルートを、オンプレミスにルーティングするための承認済み仮想ネットワーク ゲートウェイに確実にポイントするようにします。    | この[テンプレート](../../governance/policy/samples/no-user-defined-route-table.md)で今すぐ開始。 |
|リージョンで Network Watcher が有効になっていない場合を監査する。  | 使用されているすべてのリージョンで Network Watcher が確実に有効になっているようにします。  | この[テンプレート](../../governance/policy/samples/network-watcher-not-enabled.md)で今すぐ開始。 |
|すべてのサブネット上の NSG x。  | インターネット トラフィックをブロックする NSG (または承認済み NSG のセット) を、すべての仮想ネットワークのすべてのサブネットに確実に適用されるようにします。 | この[テンプレート](../../governance/policy/samples/nsg-on-subnet.md)で今すぐ開始。 |
|すべての NIC 上の NSG x。 | インターネット トラフィックをブロックする NSG が、すべての仮想マシンのすべての NIC に確実に適用されるようにします。 | この[テンプレート](../../governance/policy/samples/nsg-on-nic.md)で今すぐ開始。 |
|仮想マシン ネットワーク インターフェイスに対して承認された仮想ネットワークを使用する。  | すべての NIC が承認された仮想ネットワーク上に確実に配置されるようにします。 | この[テンプレート](../../governance/policy/samples/use-approved-vnet-vm-nics.md)で今すぐ開始。 |
|許可される場所。 | 仮想ネットワークと Network Watcher 構成が準拠しているリージョンに、すべてのリソースが確実にデプロイされるようにします。  | この[テンプレート](../../governance/policy/samples/allowed-locations.md)で今すぐ開始。 |
|**PublicIP** など、許可されないリソースの種類。 | コンプライアンス プランのない種類のリソースのデプロイを禁止します。 パブリック IP アドレス リソースのデプロイを禁止するには、このポリシーを使います。 NSG ルールを使って受信インターネット トラフィックを効果的にブロックできますが、パブリック IP の使用を禁止すると攻撃をさらに減らすことができます。   | この[テンプレート](../../governance/policy/samples/not-allowed-resource-types.md)で今すぐ開始。  |

### <a name="network-watcher-traffic-analytics"></a>Network Watcher のトラフィック分析

Network Watcher の[トラフィック分析](https://azure.microsoft.com/blog/traffic-analytics-in-preview/)は、フロー ログ データや他のログを利用して、ネットワーク トラフィックの概要を提供します。 このデータは、TIC コンプライアンスの監査と問題点の特定に役立ちます。 概要ダッシュボードを使って、インターネットと通信している仮想マシンをすばやく選別し、TIC ルーティングのための絞り込んだリストを取得できます。

![トラフィック分析](media/tic-traffic-analytics-1.png)

仮想マシンに対するインターネット トラフィックの可能性のある物理的な宛先をすばやく識別するには、**geo マップ**を使います。 疑わしい場所またはパターンの変化を識別してトリアージできます。

![geo マップ](media/tic-traffic-analytics-2.png)

既存の仮想ネットワークをすばやく調査するには、**仮想ネットワーク トポロジ**を使用します。

![ネットワーク トポロジ マップ](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Network Watcher の次のホップのテスト

Network Watcher によって監視されているリージョン内のネットワークでは、次のホップのテストを行うことができます。 Azure portal で、Network Watcher のサンプル ネットワーク フローに対して送信元と送信先を入力し、次のホップの送信先を解決できます。 仮想マシンとサンプル インターネット アドレスに対してこのテストを実行し、次のホップの送信先が予期される確実にネットワーク仮想ゲートウェイであるようにします。

![次のホップのテスト](media/tic-network-watcher.png)

## <a name="conclusions"></a>まとめ

2018 年 5 月に記述されて定義された TIC 2.0 付録 H ガイダンスに準拠するように、Microsoft Azure、Office 365、Dynamics 365 に対するアクセスを簡単に構成できます。 Microsoft は、TIC ガイダンスが変更される可能性があることを認識しています。 Microsoft は、新しいガイダンスがリリースされたときにお客様がすぐにガイダンスに対応できるよう努めています。

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>付録: 一般的なワークロード向けの Trusted Internet Connections のパターン

| Category | ワークロード | IaaS | 専用 PaaS/仮想ネットワーク挿入  | サービス エンドポイント  |
|---------|---------|---------|---------|--------|
| Compute | Azure Linux 仮想マシン | はい | | |
| Compute | Azure Windows ウィンドウ | はい | | |
| Compute | 仮想マシン スケール セット | はい | | |
| Compute | Azure Functions | | App Service 環境 | |
| Web とモバイル | 内部 Web アプリケーション | | App Service 環境| |
| Web とモバイル | 内部モバイル アプリケーション | | App Service 環境 | |
| Web とモバイル | API アプリケーション | | App Service 環境 | |
| Containers | Azure Container Service | | | はい |
| Containers | Azure Kubernetes Service (AKS) \* | | | はい |
| Database | Azure SQL Database | | Azure SQL Database Managed Instance \* | Azure SQL |
| Database | Azure Database for MySQL | | | はい |
| Database | Azure Database for PostgreSQL | | | はい |
| Database | Azure SQL Data Warehouse | | | はい |
| Database | Azure Cosmos DB | | | はい |
| Database | Azure Cache for Redis | | はい | |
| Storage | Azure BLOB ストレージ | はい | | |
| Storage | Azure Files | はい | | |
| Storage | Azure Queue Storage | はい | | |
| Storage | Azure Table Storage | はい | | |
| Storage | Azure Disk Storage | はい | | |

\* 2018 年 5 現在、Azure Government ではパブリック プレビュー。
