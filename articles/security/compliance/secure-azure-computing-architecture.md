---
title: Secure Azure Computing Architecture
description: この参照アーキテクチャはエンタープライズ レベルの DMZ アーキテクチャ向けであり、ネットワーク仮想アプライアンスとその他のツールを使用します。 このアーキテクチャは、国防総省のセキュア クラウド コンピューティング アーキテクチャ機能要件を満たすように設計されています。 どのような組織でも使用できます。 このリファレンスには、Citrix または F5 アプライアンスを使用する 2 つの自動化オプションが含まれています。
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 3a27eac3d4609f1054b0ef6a9417fe2f1ca53ae4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656642"
---
# <a name="secure-azure-computing-architecture"></a>Secure Azure Computing Architecture

米国Azure にワークロードをデプロイする国防総省 (DoD) の顧客は、セキュリティで保護された仮想ネットワークをセットアップし、DoD の標準と慣行で規定されているセキュリティ ツールとサービスを構成するためのガイダンスを求めています。 

国防情報システム局 (DISA) は 2017 年、[セキュア クラウド コンピューティング アーキテクチャ (SCCA) 機能要件文書 (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) を発行しました。 SCCA では、国防情報システム ネットワーク (DISN) と商用クラウド プロバイダーの接続ポイントをセキュリティで保護するための機能目標を説明しています。 SCCA では、ミッション所有者が接続境界でクラウド アプリケーションをセキュリティで保護する方法についても説明しています。 商用クラウドに接続するすべての DoD エンティティは、SCCA FRD で規定されているガイドラインに従う必要があります。
 
SCCA には 4 つのコンポーネントがあります。
 
- BCAP (Boundary Cloud Access Point: 境界クラウド アクセス ポイント)
- VDSS (Virtual Datacenter Security Stack: 仮想データセンター セキュリティ スタック)
- VDMS (Virtual Datacenter Managed Service: 仮想データセンター管理サービス)
- TCCM (Trusted Cloud Credential Manager: 信頼されたクラウド資格情報マネージャー) 

Microsoft では、Azure で実行される IL4 ワークロードと IL5 ワークロードの両方について SCCA 要件を満たすソリューションを開発しました。 この Azure 固有のソリューションは Secure Azure Computing Architecture (SACA) と呼ばれます。 SACA を導入するお客様は SCCA FRD に準拠しています。 DoD の顧客はこれらを利用して、接続後にワークロードを Azure に移動できます。

SCCA のガイダンスとアーキテクチャは DoD の顧客に固有のものですが、SACA の最新の改訂は、信頼性の高いインターネット接続 (TIC) のガイダンスに民間の顧客が準拠するために役立ちます。 最新の改訂は、自らの Azure 環境を保護するために、セキュリティで保護された DMZ を実装することを希望する商用顧客にも役立ちます。


## <a name="secure-cloud-computing-architecture-components"></a>Secure Cloud Computing Architecture のコンポーネント

### <a name="bcap"></a>BCAP

BCAP の目的は、クラウド環境で発生する攻撃から DISN を保護することです。 BCAP は侵入の検出と防止を実行します。 また、不正なトラフィックを遮断します。 このコンポーネントは SCCA の他のコンポーネントと併置できます。 このコンポーネントは物理ハードウェアを使用してデプロイすることをお勧めします。 BCAP セキュリティ要件の一覧を次の表に示します。

#### <a name="bcap-security-requirements"></a>BCAP セキュリティ要件

![BCAP 要件のマトリックス](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS の目的は、Azure でホストされている DoD ミッション所有者のアプリケーションを保護することです。 VDSS は SCCA でのセキュリティ オペレーションの大部分を実行します。 Azure で実行されるアプリケーションをセキュリティで保護するために、トラフィック検査を実施します。 このコンポーネントは Azure 環境内で提供できます。

#### <a name="vdss-security-requirements"></a>VDSS セキュリティ要件

![VDSS 要件のマトリックス](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDMS の目的は、ホスト セキュリティと共有データ センター サービスを提供することです。 VDMS の機能は、SCCA のハブで実行することも、ミッション所有者が自らの特定の Azure サブスクリプションにその一部をデプロイすることもできます。 このコンポーネントは Azure 環境内で提供できます。

#### <a name="vdms-security-requirements"></a>VDMS セキュリティ要件

![VDMS 要件のマトリックス](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM はビジネス ロールです。 この個人は SCCA を管理する責任を担います。 その任務は次のとおりです。 

- クラウド環境へのアカウント アクセスのための計画とポリシーを確立します。 
- ID とアクセスの管理が正しく運用されていることを保証します。 
- クラウド資格情報管理計画を保守します。 

この個人は承認機関によって任命されます。 BCAP、VDSS、および VDMS は、TCCM がその職務を遂行するために必要とする機能を提供します。

#### <a name="tccm-security-requirements"></a>TCCM セキュリティ要件

![TCCM 要件のマトリックス](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA のコンポーネントと計画に関する考慮事項 

SACA 参照アーキテクチャは、VDSS および VDMS コンポーネントを Azure にデプロイするために、また TCCM を有効にするために設計されています。 このアーキテクチャはモジュール形式です。 VDSS と VDMS のすべての部分は、一元管理されたハブで実行できます。 一部のコントロールはミッション所有者の空間でも、さらにはオンプレミスでも満たすことができます。 Microsoft では、すべてのミッション所有者が接続できる中央の仮想ネットワークに、VDSS および VDMS コンポーネントを併置することを推奨しています。 このアーキテクチャを次の図に示します。 


![SACA 参照アーキテクチャ図](media/sacav2generic.png)

以下のトピックはすべての顧客に影響を与えるため、SCCA のコンプライアンス戦略と技術的アーキテクチャを計画するときは、これらのトピックについて最初から検討してください。 以下の問題が DoD 顧客のもとで発生し、計画と実行を遅らせる傾向があります。 

#### <a name="which-bcap-will-your-organization-use"></a>組織でどの BCAP を使用するか?
   - DISA BCAP:
        - DISA は、ペンタゴンとカリフォルニア州キャンプロバーツで 2 つの運用 BCAP を運用しています。 3 番目がまもなくオンラインになる予定です。 
        - DISA のすべての BCAP には、DoD 顧客が Azure への接続に使用できる Azure ExpressRoute 回線が敷設されています。 
        - DISA では、Office 365 などの Microsoft SaaS (Software as a Service) ツールのサブスクリプションを希望する DoD 顧客向けに、エンタープライズ レベルの Microsoft ピアリング セッションを用意しています。 DISA BCAP を使用して、お客様の SACA インスタンスへの接続とピアリングを有効にすることができます。 
    - 独自の BCAP を構築する:
        - このオプションでは、併置されたデータ センター内の空間をリースし、Azure への ExpressRoute 回線を敷設する必要があります。 
        - このオプションには追加の承認が必要です。 
        - 追加の承認と物理的な構築のため、このオプションは最も時間がかかります。 
    - DISA BCAP を使用することをお勧めします。 このオプションはすぐに利用でき、冗長性が組み込まれており、現在も実稼働環境で顧客の運用基盤になっています。
- DoD ルーティング可能 IP 空間:
    - エッジでは DoD ルーティング可能 IP 空間を使用する必要があります。 NAT を使用してこれらの空間を Azure 内のプライベート IP 空間に接続するオプションが利用できます。
    - IP 空間の取得については DoD Network Information Center (NIC) にお問い合わせください。 それは、DISA へのシステム/ネットワーク承認プロセス (SNAP) 提出の一環として必要です。 
    - NAT を使用して Azure のプライベート アドレス空間を接続することを計画している場合、SACA のデプロイを計画している各リージョンの NIC から割り当てられたアドレス空間の /24 サブネットが少なくとも必要です。
- 冗長性:
    - SACA インスタンスを少なくとも 2 つのリージョンにデプロイします。 DoD クラウドでは、両方の利用可能な DoD リージョンにそれをデプロイします。
    - 少なくとも 2 つの BCAP に、別個の ExpressRoute 回線を介して接続します。 その後、両方の ExpressRoute 接続を各リージョンの SACA インスタンスにリンクできます。 
- DoD コンポーネント固有の要件:
    - SCCA の要件以外に組織固有の要件がありますか? 組織によっては、特定の IPS 要件が存在します。
- SACA はモジュール形式アーキテクチャです。
    - 環境に必要なコンポーネントのみを使用します。 
        - ネットワーク仮想アプライアンスを単一層または複数層にデプロイします。
        - 統合 IPS または自前の IPS を使用します。
- アプリケーションとデータの DoD 影響レベル:
    - Microsoft IL5 リージョンでアプリケーションを実行する可能性がある場合は、IL5 で SACA インスタンスをビルドします。 このインスタンスは IL4 アプリケーションおよび IL5 と組み合わせて使用できます。 IL4 SACA インスタンスと IL5 アプリケーションの組み合わせは、おそらく認定を受けられません。

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>どのネットワーク仮想アプライアンス ベンダーを VDSS に利用しますか?
前述のように、この SACA リファレンスは、さまざまなアプライアンスと Azure サービスを使用して構築できます。 Microsoft は、F5 と Citrix の両方で SACA アーキテクチャをデプロイするためのソリューション テンプレートを自動化しました。 これらのソリューションについては、以降のセクションで説明します。

#### <a name="which-azure-services-will-you-use"></a>どの Azure サービスを使用しますか?
- ログ分析、ホスト ベースの保護、および IDS 機能性の要件を満たすことができる Azure サービスがあります。 一部のサービスは Microsoft IL5 リージョンで一般提供されない可能性があります。 この場合、これらの Azure サービスでお客様の要件を満たせなければ、サード パーティ製ツールの使用が必要になる可能性があります。 お客様が慣れているツールを使用するか、それとも Azure ネイティブ ツールを使用できるかどうか、比較検討してください。
- できるだけ多くの Azure ネイティブ ツールを使用することをお勧めします。 それらはクラウド セキュリティを考慮して設計されており、Azure プラットフォームの他の部分とシームレスに統合します。 SCCA のさまざまな要件を満たすには、次の一覧にある Azure ネイティブ ツールを使用します。

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- サイズ変更
    - サイズ変更の演習を完了する必要があります。 SACA インスタンス経由で発生する可能性があるコンカレント接続数と、ネットワーク スループットの要件に注目します。 
    - この手順は重要です。 VM のサイズを変更したり、SACA インスタンスで利用するさまざまなベンダーから要求されるライセンスを識別したりするために役立ちます。 
    - このサイズ変更の演習なしでは、的確なコスト分析はできません。 正確なサイズ変更は最高のパフォーマンスにもつながります。 


## <a name="most-common-deployment-scenario"></a>最も一般的なデプロイのシナリオ

 Microsoft の多くのお客様が、自前の SACA 環境のフル デプロイ、または少なくともその計画の段階を経験しました。 これらの経験から、最も一般的なデプロイのシナリオに関する分析情報が明らかになりました。 次の図に、最も一般的なアーキテクチャを示します。 


![SACA 参照アーキテクチャ図](media/sacav2commonscenario.png) 


図からわかるように、DoD 顧客は通常、DISA BCAP のうちの 2 つをサブスクライブしています。 これらの片方は西海岸に、もう片方は東海岸に位置します。 それぞれの DISA BCAP ロケーションで、ExpressRoute プライベート ピアが Azure に対して有効になっています。 その後、これらの ExpressRoute ピアは、DoD East および DoD Central の各 Azure リージョン内の仮想ネットワーク ゲートウェイにリンクされます。 SACA インスタンスは、DoD East および DoD Central の各 Azure リージョンにデプロイされます。 すべてのイングレスおよびエグレス トラフィックは、DISA BCAP までの ExpressRoute 接続を行き来します。

その後、ミッション所有者のアプリケーションが、アプリケーションをデプロイする予定の Azure リージョンを選択します。 それらは仮想ネットワーク ピアリングを使用して、アプリケーションの仮想ネットワークを SACA 仮想ネットワークに接続します。 その後、すべてのトラフィックを VDSS インスタンス経由で強制的にトンネリングします。

SCCA の要件を満たしているため、このアーキテクチャをお勧めします。 可用性が高く、容易に拡張できます。 また、デプロイと管理が簡易になります。

## <a name="automated-saca-deployment-options"></a>自動 SACA デプロイ オプション

 既に説明したように、Microsoft は 2 社のベンダーと提携して、自動化された SACA インフラストラクチャ テンプレートを作成しました。 どちらのテンプレートも、次の Azure コンポーネントをデプロイします。 

- SACA 仮想ネットワーク
    - 管理サブネット
        - このサブネットは管理 VM と管理サービスがデプロイされる場所であり、ジャンプ ボックスとも呼ばれます。
        - VDMS サブネット
            - このサブネットは、VDMS に使用される VM とサービスがデプロイされる場所です。
        - 信頼されていないサブネットと信頼されているサブネット
            - これらのサブネットは、仮想アプライアンスがデプロイされる場所です。
        - ゲートウェイ サブネット
            - このサブネットは、ExpressRoute Gateway がデプロイされる場所です。
- 管理ジャンプ ボックス仮想マシン
    - 環境の帯域外管理に使用されます。
- ネットワーク仮想アプライアンス
    - デプロイするテンプレートに応じて、Citrix または F5 のどちらかを使用します。
- パブリック IP
    - ExpressRoute がオンラインになるまでの間、フロント エンドに使用されます。 これらの IP は、バックエンドの Azure プライベート アドレス空間に変換されます。
- ルート テーブル 
    - 自動化中に適用されるこれらのルート テーブルは、すべてのトラフィックを仮想アプライアンス経由で強制的にトンネリングします。
- Azure ロード バランサー - Standard SKU
    - アプライアンス間でトラフィックを負荷分散するために使用されます。
- ネットワーク セキュリティ グループ
    - 特定のエンドポイントまでトラバースできるトラフィックの種類を制御するために使用されます。


### <a name="citrix-saca-deployment"></a>Citrix SACA デプロイ

Citrix デプロイ テンプレートは、2 レイヤーの高可用性 Citrix ADC アプライアンスをデプロイします。 このアーキテクチャは VDSS の要件を満たしています。 

![Citrix SACA ダイアグラム](media/citrixsaca.png)


Citrix のドキュメントおよびデプロイ スクリプトについては、[この GitHub リンク](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)を参照してください。


 ### <a name="f5-saca-deployment"></a>F5 SACA デプロイ

2 つの独立した F5 デプロイ テンプレートは、2 つの異なるアーキテクチャに対応しています。 最初のテンプレートには、アクティブ/アクティブの高可用性構成の F5 アプライアンスのレイヤーが 1 つしかありません。 このアーキテクチャは VDSS の要件を満たしています。 2 番目のテンプレートは、アクティブ/アクティブ高可用性 F5 の 2 番目のレイヤーを追加します。 この 2 番目のレイヤーでは、顧客は F5 とは別に、独自の IPS を F5 レイヤーの中間に追加できます。 使用が規定された特定の IPS が、すべての DoD コンポーネントに存在するわけではありません。 その場合、F5 デバイス上に IPS が含まれるアーキテクチャであることから、単一レイヤーの F5 アプライアンスが最もうまく機能します。

![F5 SACA ダイアグラム](media/f5saca.png)

F5 のドキュメントおよびデプロイ スクリプトについては、[この GitHub リンク](https://github.com/f5devcentral/f5-azure-saca)を参照してください。












