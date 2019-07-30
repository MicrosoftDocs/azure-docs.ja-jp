---
title: クラウド ソリューション プロバイダー向け ExpressRoute - Azure | Microsoft Docs
description: この記事では、Azure サービスと ExpressRoute を独自のサービスに組み込むことを希望するクラウド ソリューション プロバイダー向けに情報を提供します。
services: expressroute
author: richcar
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: ricarlse
ms.custom: seodec18
ms.openlocfilehash: a3bd48f32dfcee1a666ff842cfcab2384a5459ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849260"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>クラウド ソリューション プロバイダー (CSP) 向けの ExpressRoute
Microsoft では、新たな開発投資を行わずに顧客向けの新しいサービスやソリューションを迅速にプロビジョニングできる、従来のリセラーおよび代理店 (CSP) 向けの超大規模サービスを提供しています。 このような新しいサービスをクラウド ソリューション プロバイダー (CSP) が直接管理できるようにするため、Microsoft では、CSP が顧客に代わって Microsoft Azure のリソースを管理するために利用できるプログラムや API を用意しています。 ExpressRoute は、そのようなリソースの 1 つです。 ExpressRoute を利用することで、CSP は既存の顧客リソースを Azure サービスに接続できます。 ExpressRoute とは、Azure のサービスにリンクする高速プライベート通信です。 

ExpressRoute は高可用性を実現するための一対の回線で構成されています。これらの回線は単一の顧客のサブスクリプションに関連付けられており、複数の顧客によって共有されることはありません。 高可用性を維持するため、回線はそれぞれ別のルーターに接続する必要があります。

> [!NOTE]
> ExpressRoute には帯域幅と接続数に制限があり、実装が大規模または複雑になると、単一の顧客に対して複数の ExpressRoute 回線が必要になります。
> 
> 

Microsoft Azure には顧客向けに提供できるさまざまなサービスがあり、その数はますます増加しています。 ExpressRoute を利用すれば、Microsoft Azure 環境に高速かつ短い待ち時間でアクセスできるため、そうしたサービスを企業やその顧客が最大限に活用することができます。

## <a name="microsoft-azure-management"></a>Microsoft Azure の管理
Microsoft では、プログラムによって独自のサービス管理システムに統合することで Azure 顧客サブスクリプションを管理できる API を、CSP 向けに提供しています。 サポートされている管理機能は [こちら](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx)から確認できます。

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure リソースの管理
サブスクリプションの管理方法は、顧客との契約内容によって異なります。 CSP がリソースの作成とメンテナンスを直接管理することもできますが、顧客が Microsoft Azure サブスクリプションを制御し、必要に応じて Azure リソースを作成することもできます。 顧客が Microsoft Azure サブスクリプションでのリソースの作成を管理する場合、"*Connect-Through*" モデルと "*Direct-To*" モデルという 2 つのモデルのいずれかを使用することになります。 以降のセクションで、これらのモデルの詳細について説明します。  

### <a name="connect-through-model"></a>Connect-Through モデル
![alt text](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

Connect-Through モデルでは、CSP がデータセンターと顧客の Azure サブスクリプションとの間で直接接続を構築します。 直接接続は ExpressRoute を使用して構築され、貴社のネットワークが Azure に接続されます。 その後、顧客が貴社のネットワークに接続します。 このシナリオでは、顧客が CSP のネットワークを介して Azure サービスにアクセスする必要があります。 

貴社が管理していない他の Azure サブスクリプションを顧客が保有している場合、その顧客はパブリック インターネットまたは顧客独自のプライベート接続を利用して、このような CSP の管理外のサブスクリプションにプロビジョニングされたサービスに対して接続します。 

CSP が管理する Azure サービスでは、前提条件として CSP が事前に顧客 ID ストアを構築している必要があります。この ID ストアは、代理での管理 (AOBO) による CSP サブスクリプションの管理を行うために、Azure Active Directory にレプリケートされます。 このシナリオの主な推進要因としては、特定のパートナーまたはサービス プロバイダーと顧客との間に関係が確立されている場合、顧客が現在プロバイダーのサービスを利用している場合、CSP 単独では不可能な、柔軟性の提供と顧客の課題への対処を実現するため、プロバイダーがホストするソリューションと Azure がホストするソリューションを組み合わせて提供することをパートナーが望んでいる場合などが挙げられます。 このモデルを表した**図**を以下に示します。

![alt text](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Connect-To モデル
![alt text](./media/expressroute-for-cloud-solution-providers/connect-to.png)

Connect-To モデルでは、サービス プロバイダーが顧客のネットワークを介して ExpressRoute を使用し、顧客のデータセンターと CSP がプロビジョニングした Azure サブスクリプションとの間に直接接続を構築します。

> [!NOTE]
> ExpressRoute に関しては、顧客が ExpressRoute 回線を作成して保守することが必要になります。  
> 
> 

この接続シナリオでは、顧客が一部または全面的に作成、所有、管理する直接ネットワーク接続を使用することで、顧客独自のネットワークを介して CSP が管理する Azure サブスクリプションに直接アクセスする必要があります。 このような顧客の場合、プロバイダーは現時点で顧客 ID ストアを構築していないことが前提となり、顧客が AOBO によるサブスクリプション管理のために現在の ID ストアを Azure Active Directory にレプリケートする際、プロバイダーがそれを支援することになります。 このシナリオの主な推進要因としては、特定のパートナーまたはサービス プロバイダーと顧客との間に関係が確立されている場合、顧客が現在プロバイダーのサービスを利用している場合、既存のプロバイダー データセンターまたはインフラストラクチャを必要としない、Azure がホストするソリューションのみをベースにしたサービスの提供を、パートナーが望んでいる場合などが挙げられます。

![alt text](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

これら 2 つのモデルのどちらを選択するかは、顧客のニーズと、Azure サービスの提供に対する貴社の現在のニーズによって決まります。 各モデルの詳細と、関連するロールベースのアクセス制御、ネットワーク、ID 設計パターンなどの詳細については、以下のリンクを参照してください。

* **ロールベースのアクセス制御 (RBAC)** - RBAC は Azure Active Directory を基にした機能です。  Azure RBAC の詳細については、 [こちら](../role-based-access-control/role-assignments-portal.md)を参照してください。
* **ネットワーク** - Microsoft Azure でのネットワークに関するさまざまなトピックを紹介します。
* **Azure Active Directory (Azure AD)** - Azure AD では、Microsoft Azure およびサード パーティ SaaS アプリケーション向けの ID 管理機能を提供します。 Azure AD の詳細については、 [こちら](https://azure.microsoft.com/documentation/services/active-directory/)を参照してください。  

## <a name="network-speeds"></a>ネットワーク速度
ExpressRoute では、50 Mb/秒～ 10 Gb/秒のネットワーク速度をサポートしています。 このため、顧客固有の環境に必要となる量のネットワーク帯域幅を購入することができます。

> [!NOTE]
> ネットワーク帯域幅は、通信を妨害することなく必要に応じて拡大できますが、ネットワーク速度を低下させるには、回線を削除して低いネットワーク速度で作成し直す必要があります。  
> 
> 

ExpressRoute では、より高速での接続の利用効率を上げるために、複数の VNet から単一の ExpressRoute 回線への接続をサポートしています。 単一の ExpressRoute 回線は、同じ顧客が所有する複数の Azure サブスクリプション間で共有できます。

## <a name="configuring-expressroute"></a>ExpressRoute の構成
ExpressRoute は、1 本の ExpressRoute 回線で 3 種類のトラフィック ([ルーティング ドメイン](#expressroute-routing-domains)) をサポートするように構成できます。 このトラフィックは、Microsoft ピアリング、Azure パブリック ピアリング、プライベート ピアリングに分離されます。 1 種類または全種類のトラフィックを選択して 1 本の ExpressRoute 回線で送信することも、顧客が求める ExpressRoute 回線のサイズや分離性に応じて複数の ExpressRoute 回線を使用することもできます。 顧客のセキュリティ体制によっては、パブリック トラフィックとプライベート トラフィックを同じ回線で送受信できない場合があります。

### <a name="connect-through-model"></a>Connect-Through モデル
Connect-Through の構成では、顧客データセンターのリソースを Azure でホストされるサブスクリプションに接続するためのすべてのネットワーク基盤に対して、貴社が責任を負います。 Azure の機能を利用するそれぞれの顧客には独自の ExpressRoute 接続が必要であり、それを貴社が管理します。 ExpressRoute 回線の調達は、顧客が調達する場合と同じ方法で行います。 回線のプロビジョニングと回線の状態に関しては、 [ExpressRoute ワークフロー](expressroute-workflows.md) に関する記事で概説されているものと同じ手順に従います。 その後、ボーダー ゲートウェイ プロトコル (BGP) のルートを構成して、オンプレミス ネットワークと Azure VNet の間のトラフィックを制御します。

### <a name="connect-to-model"></a>Connect-To モデル
Connect-To の構成では、顧客は Azure への接続を既に確保しているか、貴社のデータセンターではなく、顧客独自のデータセンターから直接 Azure に ExpressRoute をリンクするインターネット サービス プロバイダーへの接続を予定していることになります。 プロビジョニング プロセスを開始するには、前述の「Connect-Through モデル」で説明した手順を顧客が実行します。 回線が確立されると、顧客は貴社のネットワークと Azure VNet の両方にアクセスできるように、オンプレミスのルーターを構成する必要があります。

貴社が接続のセットアップやルートの構成を支援することで、貴社のデータセンターのリソースと、同じデータセンターのクライアント リソース、または Azure でホストされているリソースの間での通信が可能になります。

## <a name="expressroute-routing-domains"></a>ExpressRoute のルーティング ドメイン
ExpressRoute では、パブリック ピアリング、プライベート ピアリング、Microsoft ピアリングという 3 つのルーティング ドメインを提供します。 各ルーティング ドメインは、高可用性を実現するため、同一のルーターを使用してアクティブ/アクティブ構成で構成されます。 ExpressRoute ルーティング ドメインの詳細については、 [こちら](expressroute-circuit-peerings.md)を参照してください。

必要なルートのみを許可するようにカスタム ルート フィルターを定義することができます。 これらの変更を行う方法や詳細については、[PowerShell を使用した ExpressRoute 回線のルーティングの作成と変更](expressroute-howto-routing-classic.md)に関する記事でルーティング フィルターの詳細を参照してください。

> [!NOTE]
> Microsoft ピアリングとパブリック ピアリングの接続は、顧客または CSP が所有するパブリック IP アドレスを介したものであることが必要で、また定義されているすべてのルールに従う必要があります。 詳細については、「 [ExpressRoute の前提条件](expressroute-prerequisites.md) 」を参照してください。  
> 
> 

## <a name="routing"></a>ルーティング
ExpressRoute は、Azure 仮想ネットワーク ゲートウェイを介して Azure ネットワークに接続します。 ネットワーク ゲートウェイは、Azure 仮想ネットワーク向けにルーティングを提供します。

Azure 仮想ネットワークを作成すると、VNet がそのサブネットとの間でトラフィックを送受信するための、既定のルーティング テーブルも作成されます。 既定のルーティング テーブルではソリューション用として不十分な場合、カスタム ルートを作成して送信トラフィックをカスタム アプライアンスにルーティングするか、特定のサブネットまたは外部ネットワークへのルーティングをブロックすることができます。

### <a name="default-routing"></a>既定のルーティング
既定のルーティング テーブルには、以下のようなルートが含まれています。

* サブネット内でのルーティング
* 仮想ネットワーク内のサブネットからサブネットへのルーティング
* インターネットへのルーティング
* VPN ゲートウェイを使用した仮想ネットワークから仮想ネットワークへのルーティング
* VPN または ExpressRoute ゲートウェイを使用した仮想ネットワークからオンプレミス ネットワークへのルーティング

![alt text](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>ユーザー定義のルーティング (UDR)
ユーザー定義のルーティングでは、割り当てられたサブネットから仮想ネットワーク内の他のサブネットへの送信トラフィックや、他の定義済みゲートウェイ (ExpressRoute、インターネット、または VPN) のいずれかを介した送信トラフィックを制御できます。 既定のシステム ルーティング テーブルはユーザー定義のルーティング テーブルに置き換えることができます。これによって既定のルーティング テーブルがカスタム ルートに置き換わります。 ユーザー定義のルーティングを使用すると、顧客はファイアウォールのようなアプライアンスまたは侵入検出アプライアンスへの特定のルートを作成することや、ユーザー定義のルートをホストするサブネットから特定のサブネットへのアクセスをブロックすることができます。 ユーザー定義のルートの概要については、[こちら](../virtual-network/virtual-networks-udr-overview.md)を参照してください。 

## <a name="security"></a>セキュリティ
Connect-To モデルと Connect-Through モデルのどちらが使用されているかによって、顧客が VNet 内のセキュリティ ポリシーを定義するか、VNet に定義するセキュリティ ポリシーの要件を CSP に提供するかが決まります。 定義できるセキュリティ基準を以下に示します。

1. **顧客の分離** - Azure プラットフォームでは、顧客 ID と VNet 情報をセキュリティで保護されたデータベースに保存し、それを使用して顧客ごとのトラフィックを GRE トンネルでカプセル化することにより、顧客の分離を実現しています。
2. **ネットワーク セキュリティ グループ (NSG)** ルールは、Azure の VNet 内のサブネットからの送受信が許可されたトラフィックを定義するためのものです。 既定では、NSG にはインターネットから VNet へのトラフィックをブロックするためのブロック ルールと、VNet 内のトラフィックのための許可ルールが含まれています。 ネットワーク セキュリティ グループの詳細については、 [こちら](https://azure.microsoft.com/blog/network-security-groups/)を参照してください。
3. **強制トンネリング** - これは、Azure 内からインターネットへのトラフィックをリダイレクトするためのオプションであり、ExpressRoute 接続を通じてオンプレミスのデータセンターにリダイレクトされます。 強制トンネリングの詳細については、 [こちら](expressroute-routing.md#advertising-default-routes)を参照してください。  
4. **暗号化** - ExpressRoute 回線は特定の顧客の専用回線となりますが、ネットワーク プロバイダーへの侵入が発生した場合は、侵入者にパケット トラフィックを調べられる可能性があります。 この可能性に対処するため、顧客または CSP は、オンプレミスのリソースと Azure リソースの間で送受信されるすべてのトラフィックの IPSec トンネル モード ポリシーを定義することで、そのトラフィックを暗号化することができます (上記の図 5: ExpressRoute セキュリティで Customer 1 のオプション トンネル モード IPSec を参照)。 2 番目の選択肢として、ExpressRoute 回線の各エンドポイントでファイアウォール アプライアンスを使用する方法があります。 この方法では、ExpressRoute 回線経由のトラフィックを暗号化するために、サード パーティのファイアウォール VM/アプライアンスを両方のエンドポイントにインストールする必要があります。

![alt text](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>次の手順
クラウド ソリューション プロバイダー サービスを利用することで、高価なインフラストラクチャや機能を購入することなく、顧客に対する自社の価値を高めることが可能になり、同時にプライマリ アウトソーシング プロバイダーとしての地位も保つことができます。 CSP API を通じて Microsoft Azure とのシームレスな統合が実現できるため、既存の管理フレームワークの範囲内で Microsoft Azure の管理を統合することができます。  

詳細については、以下のリンクを参照してください。

[クラウド ソリューション プロバイダー プログラムにおける Azure](https://docs.microsoft.com/azure/cloud-solution-provider)。  
[クラウド ソリューション プロバイダーとしての取引の準備](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch)。  
[マイクロソフト クラウド ソリューション プロバイダー リソース](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources)。
