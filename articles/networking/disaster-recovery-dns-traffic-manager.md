---
title: Azure DNS と Traffic Manager を使用したディザスター リカバリー | Microsoft Docs
description: Azure DNS と Traffic Manager を使用したディザスター リカバリー ソリューションの概要
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 8cb1a490ac8edf2630253b45d99c3394bbe721b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234156"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Azure DNS と Traffic Manager を使用したディザスター リカバリー

ディザスター リカバリーは、アプリケーションの機能の深刻な損失からの復旧に重点を置きます。 ディザスター リカバリー ソリューションを選択するには、ビジネスおよびテクノロジの所有者は、災害中に必要な機能レベルを最初に判別する必要があります (例えば、利用不可、機能を制限して一部利用可能、遅延するが利用可能、または完全に利用可能など)。
ほとんどの企業のお客様は、アプリケーションまたはインフラストラクチャ レベルの フェールオーバーに対する回復性を確保するためにマルチリージョン アーキテクチャを選択します。 お客様は、冗長なアーキテクチャを使用したフェールオーバーと高可用性の実現を追求して、いくつかのアプローチを選択できます。 一般的な手法の一部を次に示します。

- **アクティブ/パッシブ (コールド スタンバイ)**: このフェールオーバー ソリューションでは、スタンバイ リージョンで実行中の VM とその他のアプライアンスは、フェールオーバーのニーズが発生しない限りアクティブではありません。 ただし運用環境は、バックアップ、VM イメージ、または Resource Manager テンプレートの形式で別のリージョンにレプリケートされます。 このフェールオーバーのメカニズムはコスト効果は高いですが、完全なフェールオーバーを行うのに長い時間がかかります。
 
    ![アクティブ/パッシブ (コールド スタンバイ)](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *図 - アクティブ/パッシブ (コールド スタンバイ) のディザスター リカバリー構成*

- **アクティブ/パッシブ (パイロット ライト)**: このフェールオーバー ソリューションでは、スタンバイ環境が最小構成でセットアップされます。 セットアップでは、最小限かつ重要なアプリケーションのセットのみをサポートするために必要なサービスのみが実行されます。 ネイティブ形式では、このシナリオでは最小限の機能のみ実行されますが、フェールオーバーが発生した場合は実稼働負荷を処理するためにスケール アップして追加のサービスを起動することができます。
    
    ![アクティブ/パッシブ (パイロット ライト)](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *図 - アクティブ/パッシブ (パイロット ライト) のディザスター リカバリー構成*

- **アクティブ/パッシブ (ウォーム スタンバイ)**: このフェールオーバー ソリューションでは、スタンバイ リージョンは事前にウォームアップされてベースロードを引き受ける準備ができており、自動スケーリングが有効で、すべてのインスタンスが起動しています。 このソリューションは、実稼働負荷全体を引き受けるほどにはスケールアップされていませんが、機能を有しており、すべてのサービスは起動しています。 このソリューションは、パイロット ライト アプローチの強化されたバージョンです。
    
    ![アクティブ/パッシブ (ウォーム スタンバイ)](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *図: アクティブ/パッシブ (ウォーム スタンバイ)のディザスター リカバリー構成*
    
フェールオーバーおよび高可用性について詳しくは、 [Azure アプリケーションのディザスター リカバリー](/azure/architecture/resiliency/disaster-recovery-azure-applications)に関する記事をご覧ください。


## <a name="planning-your-disaster-recovery-architecture"></a>ディザスター リカバリー アーキテクチャの計画

ディザスター リカバリー アーキテクチャの設定には次の 2 つの技術的な側面があります。
-  デプロイ メカニズムを使用して、プライマリおよびスタンバイの環境間でのインスタンス、データ、および構成をレプリケートします。 この種のディザスター リカバリーは、Veritas や NetApp などの Microsoft Azure パートナー アプライアンス/サービスを介して Azure Site Recovery を使用してネイティブで実行できます。 
- ネットワーク トラフィックまたは Web トラフィックをプライマリ サイトからスタンバイ サイトに迂回させるソリューションを開発します。 この種のディザスター リカバリーは、Azure DNS、Azure Traffic Manager (DNS)、またはサード パーティ製のグローバル ロード バランサーを使用して実現できます。

この記事では、ネットワーク トラフィックおよび Web トラフィックのリダイレクトを使用した方法のみを扱います。 Azure Site Recovery のセットアップの詳細については、 [Azure Site Recovery のドキュメント](../site-recovery/index.yml)を参照してください。
DNS は多くの場合、データ センターの外部で使用できるグローバル サービスであり、リージョンや可用性ゾーン (AZ) レベルのあらゆる障害から隔離されているため、DNS はネットワーク トラフィックを迂回させる最も効率的なメカニズムです。 ユーザーは DNS ベースのフェールオーバーのメカニズムを使用することができ、Azure では 2 つの DNS サービス、つまり Azure DNS (権限のある DNS) と Azure Traffic Manager (DNS ベースのスマート トラフィック ルーティング) が何らかの方法で同じ効果を実現できます。 

この記事で説明する解決策について説明するために広く使用される DNS に関するいくつかの概念を理解しておく必要があります。
- **DNS の A レコード** - A レコードは、ドメインから IPv4 アドレスへの対応を示すポインターです。 
- **CNAME または正規名** - このレコード タイプは別の DNS レコードを指すために使用します。 CNAME は IP アドレスに応答せず、IP アドレスを含むレコードへのポインターに応答します。 
- **重み付けルーティング** – サービス エンドポイントに重み付けを関連付け、割り当てられた重み付けに基づいてトラフィックを分散させることができます。 このルーティング方式は、 Traffic Manager で使用可能な 4 つのトラフィック ルーティング メカニズムのうちの 1 つです。 詳細については、「[重み付けルーティング方式](../traffic-manager/traffic-manager-routing-methods.md#weighted)」を参照してください。
- **優先順位によるルーティング** – 優先順位によるルーティングはエンドポイントの正常性チェックに基づいています。 既定では、Azure Traffic Manager は優先順位が最高のエンドポイントにすべてのトラフィックを送信し、障害や災害が発生すると、Traffic Manager はセカンダリ エンドポイントにトラフィックをルーティングします。 詳細については、「[優先順位によるルーティング方法](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)」を参照してください。

## <a name="manual-failover-using-azure-dns"></a>Azure DNS を使用した手動フェールオーバー
ディザスター リカバリー用の Azure DNS 手動フェールオーバー ソリューションでは、標準的な DNS メカニズムを使用して、バックアップ サイトにフェールオーバーします。 Azure DNS を使用した手動オプションは、コールド スタンバイまたはパイロット ライトの方法を組み合わせて使用した場合に最適に機能します。 

![Azure DNS を使用した手動フェールオーバー](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*図 - Azure DNS を使用した手動フェールオーバー*

ソリューションに対して行われた前提条件は次のとおりです。
- プライマリとセカンダリの両方のエンドポイントには、頻繁に変更されない静的 IP があります。 たとえば、プライマリ サイトの IP は 100.168.124.44 で、セカンダリ サイトの IP は 100.168.124.43 です。
- Azure DNS ゾーンはプライマリ サイトおよびセカンダリ サイトの両方に存在します。 たとえば、プライマリ サイトのエンドポイントは prod.contoso.com で、バックアップ サイトのエンドポイントは dr.contoso.com です。 www\.contoso.com と呼ばれるメイン アプリケーション用の DNS レコードも存在します。   
- TTL は、組織で設定された RTO SLA 以下です。 たとえば、エンタープライズがアプリケーション ディザスター応答の RTO を 60 分に設定した場合、TTL は 60 分未満である必要があり、少ないほど優れています。 
  Azure DNS を手動フェールオーバー用に以下のようにセットアップできます。
- DNS ゾーンの作成
- DNS ゾーン レコードの作成
- CNAME レコードの更新

### <a name="step-1-create-a-dns"></a>手順 1: DNS の作成
以下に示すように、DNS ゾーン (たとえば、www\.contoso.com) を作成します。

![Azure での DNS ゾーンの作成](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*図 - Azure での DNS ゾーンの作成*

### <a name="step-2-create-dns-zone-records"></a>手順 2: DNS ゾーン レコードの作成

このゾーン内に、以下に示すように 3 つのレコード (たとえば、www\.contoso.com、prod.contoso.com、および dr.consoto.com) を作成します。

![DNS ゾーン レコードの作成](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*図 - Azure での DNS ゾーン レコードの作成*

このシナリオでは、サイト www\.contoso.com は TTL が 30 分で、これは明記された RTO を十分に下回っており、さらに実稼働サイト prod.contoso.com を指しています。 この構成は通常の業務操作中の構成です。 prod.contoso.com および dr.contoso.com の TTL は、300 秒つまり 5 分に設定されています。 Azure Monitor や Azure App Insights などの Azure 監視サービスを使用したり、Dynatrace などのパートナー監視ソリューションを使用したりできます。さらに、アプリケーションや仮想インフラストラクチャ レベルの障害を監視または検出できる自家製のソリューションを使用することもできます。

### <a name="step-3-update-the-cname-record"></a>手順 3: CNAME レコードの更新

エラーが検出されると、次に示すように dr.contoso.com を指すようにレコード値が変更します。
       
![CNAME レコードの更新](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*図 - Azure での CNAME レコードの更新*

ほとんどのリゾルバーが、キャッシュされたゾーン ファイルを更新する 30 分以内に、www\.contoso.com への任意のクエリは dr.contoso.com にリダイレクトされます。
また、以下の Azure CLI コマンドを実行して CNAME 値を変更することもできます。
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
この手順は手動またはオートメーションで実行することができます。 手動の場合はコンソールまたは Azure CLI を使用して実行できます。 Azure SDK と API を使用して CNAME の更新を自動化し、手動による介入が不要になるようにすることができます。 オートメーションは、Azure 関数を使用して構築するか、サードパーティー製の監視アプリケーション内で構築するか、オンプレミスから構築することもできます。

### <a name="how-manual-failover-works-using-azure-dns"></a>Azure DNS を使用した手動フェールオーバーの動作のしくみ
DNS サーバーはフェールオーバー ゾーンまたはディザスター ゾーンの外部にあるため、あらゆるダウンタイムに対して保護されます。 これによりユーザーは、コスト効果の高い単純なフェールオーバー シナリオを構築し、オペレーターは障害発生時にネットワーク接続が可能で対策を実行できるという仮定の下で、常に作業することができます。 ソリューションをスクリプトに含める場合、スクリプトを実行するサーバーまたはサービスが、実稼働環境に影響する問題から隔離されているようにする必要があります。 また、低い TTL をゾーンに対して設定することで、世界中のリゾルバーがエンドポイントを長期間キャッシュ し続けないようにして、顧客が RTO 時間内にサイトにアクセスできるようにすることに留意してください。 コールド スタンバイおよびパイロット ライトの場合は、事前ウォームアップやその他の管理作業が必要な場合があるため、対策を実行する前に十分な時間を確保する必要もあります。

## <a name="automatic-failover-using-azure-traffic-manager"></a>Azure Traffic Manager を使用した自動フェールオーバー
アーキテクチャが複雑で、同じ機能を実行できる複数のリソースのセットがある場合、Azure Traffic Manager を (DNS に基づいて) 構成して、リソースの正常性を確認し、正常でないリソースから正常なリソースにトラフィックをルーティングすることができます。 次の例では、プライマリ リージョンとセカンダリ リージョンの両方が完全にデプロイされています。 このデプロイには、クラウド サービスと同期化されたデータベースが含まれます。 

![Azure Traffic Manager を使用した自動フェールオーバー](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*図 - Azure Traffic Manager を使用した自動フェールオーバー*

ただし、プライマリ リージョンのみが、ユーザーからのネットワーク要求をアクティブに処理しています。 セカンダリ リージョンは、プライマリ リージョンでサービスの中断が発生した場合にのみアクティブになります。 その場合、新しいネットワーク要求はすべて、セカンダリ リージョンにルーティングされます。 データベースのバックアップはほぼ瞬時で行われ、両方のロード バランサーには正常性のチェックが可能な IP が存在し、インスタンスは常に動作しているため、このトポロジでは、低い RTO での実行と、手動による介入が不要なフェールオーバーのオプションが提供されます。 セカンダリ フェールオーバー リージョンは、プライマリ リージョンで障害が発生した直後に動作可能な状態になる必要があります。
このシナリオは、http、https、TCP などのさまざまな種類の正常性のチェックのための組み込みのプローブを持つ Azure Traffic Manager の使用するために最適です。 Azure Traffic Manager には、以下に示すように障害が発生したときにフェールオーバーを構成できるルール エンジンもあります。 Traffic Manager を使用した次のソリューションについて検討してみましょう。
- お客様は、静的 IP が 100.168.124.44 であるprod.contoso.com というリージョン 1 のエンドポイントと、静的 IP が 100.168.124.43 である dr.contoso.com というリージョン 2 のエンドポイントを持っています。 
-   各環境は、ロード バランサーのようにパブリックに公開されたプロパティを使用してアクセスされます。 ロード バランサーは、上記のように、DNS ベース エンドポイントまたは完全修飾ドメイン名 (FQDN) の設定を構成できます。
-   リージョン 2 のすべてのインスタンスは、リージョン 1 とほぼリアルタイムでレプリケーションされます。 さらに、マシンのイメージは最新状態で、すべてのソフトウェアおよび構成データはパッチが適用され、リージョン 1 に合致しています。  
-   自動スケーリングが事前に構成済みです。 

Azure Traffic Manager によるフェールオーバーを構成するための手順については次のとおりです。
1. 新しい Azure Traffic Manager プロファイルの作成
2. Traffic Manager プロファイル内のエンドポイントの作成
3. 正常性チェックとフェールオーバー構成の設定

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>手順 1: 新しい Azure Traffic Manager プロファイルの作成
contoso123 という名前で新しい Azure Traffic Manager プロファイルを作成し、ルーティング方法については優先順位を選択します。 関連付けを行う既存のリソース グループがある場合は既存のリソース グループを選択でき、それ以外の場合は新しいリソース グループを作成します。

![Traffic Manager プロファイルを作成する](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*図 - Traffic Manager プロファイルの作成*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>手順 2: Traffic Manager プロファイル内のエンドポイントの作成

この手順では、実稼働サイトとディザスター リカバリー サイトを指すエンドポイントを作成します。 ここでは、**[Type]\(タイプ)** を外部エンドポイントとして選択しますが、リソースが Azure でホストされている場合は、**[Azure エンドポイント]** も選択できます。 **[Azure エンドポイント]** を選択した場合、Azure によって割り当てられた **[App Service]** または **[パブリック IP]** のいずれかである **[ターゲット リソース]** を選択します。 これはリージョン 1 のプライマリ サービスであるため、優先順位は **1** に設定されます。
同様に、Traffic Manager 内でディザスター リカバリーエンド ポイントを作成します。

![ディザスター リカバリー エンドポイントの作成](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*図 - ディザスター リカバリー エンドポイントの作成*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>手順 3: 正常性チェックとフェールオーバー構成の設定

この手順では、DNS の TTL を 10 秒に設定します。この時間はインターネットに接続されたほとんどの再帰的なリゾルバーで受け入れられる設定です。 この構成は、DNS リゾルバーが情報を 10 秒以上キャッシュしないことを意味します。 エンドポイント監視設定については、パスは現在 / すなわちルートに設定されていますが、たとえば prod.contoso.com/index などのパスを評価するためにエンドポイントの設定をカスタマイズできます。 以下の例ではプローブ プロトコルとして **https** が示されています。 ただし、**http** または **tcp** を選択することもできます。 プロトコルの選択は、目的のアプリケーションによって異なります。 プローブの間隔は、高速な調査を可能にする 10 秒に設定され、再試行回数は 3 に設定されます。 その結果、連続する 3 回の間隔でエラーが登録された場合、Traffic Manager は 2 番目のエンドポイントにフェールオーバーします。 自動フェールオーバーの合計時間は次の数式で定義されます。フェールオーバーの時間 = TTL + 再試行回数 * プローブ間隔。この場合、値は 10 + 3 * 10 = 40 秒 (最大) です。
再試行回数が 1 に設定され、TTL が 10 秒に設定された場合、フェールオーバーの時間は 10 + 1 * 10 = 20 秒になります。 偽陽性または ネットワークの軽微な一時的悪化によるフェールオーバーの可能性を排除するために、再試行回数を **1** より大きい値に設定してください。 


![正常性チェックの設定](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*図 - 正常性チェックとフェールオーバー構成の設定*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Traffic Manager をた使用し自動フェールオーバーが動作するしくみ

障害発生時に、プライマリ エンドポイントがプローブされ、状態が **[低下]** に変わり、ディザスター リカバリー サイトが **[オンライン]** のままになります。 Traffic Manager では、既定では、すべてのトラフィックがプライマリ (優先順位が一番高い) エンドポイントに送信されます。 プライマリ エンドポイントが低下したとき、Traffic Manager は、2 番目のエンドポイントが正常である限り、トラフィックを 2 番目のエンドポイントにルーティングします。 Traffic Manager 内に追加のエンドポイントを 構成して、追加のフェイル オーバーのエンドポイントとして使用したり、エンドポイント間の負荷を分散するロード バランサーとして使用したりするオプションもあります。

## <a name="next-steps"></a>次のステップ
- Azure Traffic Manager の詳細については、[こちら](../traffic-manager/traffic-manager-overview.md)をご覧ください。
- [Azure DNS](../dns/dns-overview.md) の詳細を学習する。