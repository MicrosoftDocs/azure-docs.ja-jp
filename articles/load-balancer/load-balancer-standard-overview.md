---
title: Azure Standard Load Balancer とは
titlesuffix: Azure Load Balancer
description: Azure Standard Load Balancer の機能の概要
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: allensu
ms.openlocfilehash: 8eb8134452685add53b9dc339437ac262ecc8a9f
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274394"
---
# <a name="azure-standard-load-balancer-overview"></a>Azure Standard Load Balancer の概要

Azure Load Balancer を使うと、アプリケーションを拡張し、サービスを高可用性にすることができます。 Load Balancer は、受信と送信のどちらのシナリオにも使うことができ、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。 

この記事では、Standard Load Balancer について説明します。  Azure Load Balancer の一般的な概要については、「[Azure Load Balancer の概要](load-balancer-overview.md)」もご覧ください。

## <a name="what-is-standard-load-balancer"></a>Standard Load Balancer とは

Standard Load Balancer は、すべての TCP アプリケーションと UDP アプリケーションのための新しい Load Balancer 製品であり、Basic Load Balancer よりいっそう広範囲で詳細な機能セットが提供されます。  多くの類似点もありますが、この記事で説明する相違点をよく理解しておくことが重要です。

Standard Load Balancer は、パブリック ロード バランサーまたは内部ロード バランサーとして使うことができます。 また、1 つの仮想マシンを、1 つのパブリック ロード バランサー リソースと 1 つの内部ロード バランサー リソースに接続できます。

ロード バランサー リソースの機能は常に、フロントエンド、ルール、正常性プローブ、およびバックエンド プール定義として表されます。  リソースは、複数のルールを含むことができます。 仮想マシンの NIC リソースからバックエンド プールを指定することにより、仮想マシンをバックエンド プールに配置できます。  仮想マシン スケール セットを使用していると、このパラメーターはネットワーク プロファイルによって渡されて展開されます。

1 つの重要な側面は、リソースの仮想ネットワークのスコープです。  Basic Load Balancer が可用性セットのスコープ内に存在するのに対し、Standard Load Balancer は仮想ネットワークのスコープに完全に統合され、仮想ネットワークのすべての概念が適用されます。

ロード バランサー リソースはオブジェクトであり、その中では、ユーザーが作成したいシナリオを実現するために Azure がそのマルチ テナント インフラストラクチャをプログラミングする方法を表すことができます。  ロード バランサー リソースと実際のインフラストラクチャの間に直接的な関係はありません。ロード バランサーを作成してもインスタンスは作成されず、容量は常に使用可能であり、考慮しなければならない起動時またはスケーリング時の遅延はありません。 

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。  TLS 終端 ("SSL オフロード") または HTTP/HTTPS 要求によるアプリケーション レイヤーの処理が必要な場合は、「[Application Gateway](../application-gateway/application-gateway-introduction.md)」をご覧ください。  グローバル DNS の負荷分散が必要な場合は、「[Traffic Manager](../traffic-manager/traffic-manager-overview.md)」をご覧ください。  実際のエンド ツー エンドのシナリオでは、必要に応じてこれらのソリューションを組み合わせると役に立つことがあります。

## <a name="why-use-standard-load-balancer"></a>Standard Load Balancer を使用する理由

Standard Load Balancer を使用すると、アプリケーションをスケーリングし、小規模のデプロイから大規模で複雑なマルチゾーン アーキテクチャまで、高可用性を実現することができます。

Standard Load Balancer と Basic Load Balancer の違いの概要については、次の表をご覧ください。

>[!NOTE]
> 新しい設計では、Standard Load Balancer の採用をお勧めします。 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

[ロード バランサーのサービス制限](https://aka.ms/lblimits)、[価格](https://aka.ms/lbpricing)、[SLA](https://aka.ms/lbsla) に関するページをご覧ください。


### <a name="backend"></a>バックエンド プール

Standard Load Balancer バックエンド プールは、仮想ネットワーク内の任意の仮想マシン リソースまで広がります。  最大 1,000 個のバックエンド インスタンスを含むことができます。  バックエンド インスタンスは IP 構成であり、NIC リソースのプロパティです。

バックエンド プールは、スタンドアロン仮想マシン、可用性セット、または仮想マシン スケール セットを含むことができます。  バックエンド プールでリソースを混合することもできます。 Load Balancer リソースごとに、バックエンド プールで最大 150 個のリソースを組み合わせることができます。

バックエンド プールの設計方法を検討するときは、個々のバックエンド プール リソースを最小限の数に設計して、管理操作の期間をさらに最適化できます。  データ プレーンのパフォーマンスやスケールに違いはありません。

### <a name="probes"></a>正常性プローブ
  
Standard Load Balancer では、HTTPS アプリケーションを正確に監視するための [HTTPS 正常性プローブ](load-balancer-custom-probe-overview.md#httpprobe) (トランスポート層セキュリティ (TLS) ラッパーを使用する HTTP プローブ) のサポートが追加されます。  

また、バックエンド プール全体で[プローブがダウン](load-balancer-custom-probe-overview.md#probedown)したときに、Standard Load Balancer は確立されたすべての TCP 接続の続行を許可します (Basic Load Balancer は、すべてのインスタンスへのすべての TCP 接続を終了します)。

詳しくは、「[Load Balancer の正常性プローブ](load-balancer-custom-probe-overview.md)」をご覧ください。

### <a name="az"></a>可用性ゾーン

>[!IMPORTANT]
>リージョン固有の情報を含む関連トピックについては、[Availability Zones](../availability-zones/az-overview.md) に関するページを参照してください。

Standard Load Balancer は、可用性ゾーンを利用できるリージョンでの追加機能をサポートします。  これらの機能は、Standard Load Balancer のすべての機能に追加されます。  可用性ゾーンの構成は、パブリックと内部の Standard Load Balancer で利用できます。

可用性ゾーンを備えたリージョンにデプロイされると、非ゾーンのフロントエンドは既定でゾーン冗長になります。   ゾーン冗長フロントエンドは、ゾーンの障害の影響を受けず、すべてのゾーンで同時に専用のインフラストラクチャによって提供されます。 

さらに、特定のゾーンにフロントエンドを保証することができます。 ゾーンのフロントエンドはそれぞれのゾーンと運命を供にし、1 つのゾーンの専用インフラストラクチャによってのみ提供されます。

バックエンド プールではゾーン間負荷分散を使用でき、VNET 内のすべての仮想マシン リソースがバックエンド プールの一部になることができます。

[可用性ゾーン関連の機能の詳細な説明](load-balancer-standard-availability-zones.md)に関するページをご覧ください。

### <a name="diagnostics"></a> 診断

Standard Load Balancer は Azure Monitor を通じて多次元メトリックを提供します。  これらのメトリックは、フィルター処理し、グループ化し、特定のディメンションに分割することができます。  サービスのパフォーマンスと正常性に関する現在と過去の分析情報を提供します。  リソースの正常性もサポートされます。  サポートされている診断の概要を次に示します。

| メトリック | 説明 |
| --- | --- |
| VIP 可用性 | Standard Load Balancer は、リージョン内からロード バランサー フロントエンドを経て、VM をサポートする SDN スタックに至るまでのデータ パスを継続的に学習します。 正常なインスタンスが保持されていれば、測定ではアプリケーションの負荷分散されたトラフィックと同じパスに従います。 顧客が使用しているデータ パスも検証されます。 測定はアプリケーションには見えないので、他の操作と干渉することはありません。|
| DIP 可用性 | Standard Load Balancer では、構成設定に従ってアプリケーション エンドポイントの正常性を監視する、分散型の正常性プローブ サービスが使われます。 このメトリックは、Load Balancer プールの個々のインスタンス エンドポイントの集計ビューまたはエンドポイントごとのフィルター ビューを提供します。  正常性プローブ構成で示されているアプリケーションの正常性を、Load Balancer がどのように表示するのかを確認できます。
| SYN パケット | Standard Load Balancer は、TCP 接続を終了したり、TCP または UDP のパケット フローと対話したりすることはありません。 フローとハンドシェイクは、常にソースと VM インスタンスの間で発生します。 TCP プロトコルのシナリオのトラブルシューティングを適切に行うために、SYN パケット カウンターを使用して TCP 接続試行の数を把握できます。 このメトリックは、受信済みの TCP SYN パケットの数を報告します。|
| SNAT 接続 | Standard Load Balancer は、パブリック IP アドレス フロントエンドにマスカレードされた送信フローの数を報告します。 SNAT ポートは有限のリソースです。 このメトリックはアプリケーションが送信フローで SNAT にどれくらい依存しているかを示すことができます。  成功した送信 SNAT フローと失敗した送信 SNAT フローのカウンターがレポートされるので、送信フローの正常性について、トラブルシューティングしたり、理解したりするのに役立てることができます。|
| バイト カウンター | Standard Load Balancer は、フロントエンドごとに処理されたデータ量を報告します。|
| パケット カウンター | Standard Load Balancer は、フロントエンドごとに処理されたパケット数を報告します。|

[Standard Load Balancer の診断の詳細な説明](load-balancer-standard-diagnostics.md)に関するページをご覧ください。

### <a name="haports"></a>HA ポート

Standard Load Balancer は新しい種類のルールをサポートします。  

アプリケーションがスケーラブルで高信頼性になるように、負荷分散ルールを構成できます。 HA ポートの負荷分散ルールを使うと、Standard Load Balancer は、内部 Standard Load Balancer のフロントエンド IP アドレスのすべてのエフェメラル ポートで、フローごとの負荷分散を提供します。  また、個々のポートを指定するのは実用的ではなかったり、望ましくなかったりするその他のシナリオにも対応できるようになります。

HA ポートの負荷分散ルールでは、ネットワーク仮想アプライアンスおよび大きな範囲の受信ポートを必要とするすべてのアプリケーションに対し、アクティブ/パッシブまたはアクティブ/アクティブの n+1 シナリオを作成することができます。  正常性プローブを使って、新しいフローを受信する必要があるバックエンドを決定できます。  ネットワーク セキュリティ グループを使って、ポート範囲のシナリオをエミュレートできます。

>[!IMPORTANT]
> ネットワーク仮想アプライアンスを使う予定の場合は、製品が HA ポートでテストされているかどうかをベンダーに確認し、実装に関する具体的なガイダンスに従います。 

[HA ポートの詳細な説明](load-balancer-ha-ports-overview.md)に関するページをご覧ください。

### <a name="securebydefault"></a>既定でのセキュリティ保護

Standard Load Balancer は仮想ネットワークに完全にオンボードされます。  仮想ネットワークは、プライベートのクローズ ネットワークです。  Standard Load Balancer と Standard パブリック IP アドレスは、この仮想ネットワークに仮想ネットワークの外部からアクセスできるように設計されているので、これらのリソースは、ユーザーがオープンしない限り、既定ではクローズになります。 つまり、トラフィックを明示的に許可し、許可されたトラフィックをホワイトリストに登録するには、ネットワーク セキュリティ グループ (NSG) が使われるようになっています。  仮想データ センター全体を作成し、それを何がいつ使用できるようにする必要があるかを、NSG によって決定できます。  お使いの仮想マシン リソースのサブネットまたは NIC に NSG がない場合、トラフィックはこのリソースに到達することを許可されません。

NSG と、ネットワーク セキュリティ グループをシナリオに適用する方法の詳細については、[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)に関する記事をご覧ください。

### <a name="outbound"></a> 送信接続

Load Balancer は、受信と送信のシナリオをサポートしています。  Standard Load Balancer は、アウトバウンド接続に関して Basic Load Balancer と大きく異なります。

仮想ネットワーク上の内部プライベート IP アドレスを Load Balancer フロントエンドのパブリック IP アドレスにマッピングするには、送信元ネットワーク アドレス変換 (SNAT) が使われます。

Standard Load Balancer では、[より堅牢かつスケーラブルで予測可能な SNAT アルゴリズム](load-balancer-outbound-connections.md#snat)用に新しいアルゴリズムが導入されており、新しい機能を使用でき、あいまいさがなくなり、副作用ではなく明示的な構成を適用できます。 これらの変更は、新しい機能が出現するために必要なことです。 

以下は、Standard Load Balancer を使うときの重要な原則です。

- ルールの完成によって、Load Balancer リソースが得られます。  Azure のすべてのプログラミングは、その構成から派生します。
- 複数のフロントエンドが使用可能な場合、すべてのフロントエンドが使われて、各フロントエンドは使用可能な SNAT ポートの数を乗算します。
- 送信接続に特定のフロントエンドが使われたくない場合は、フロントエンドを選んで制御できます。
- 送信シナリオは明示的であり、発信接続は指定されるまで存在しません。
- 負荷分散ルールでは、SNAT のプログラミング方法が推測されます。 負荷分散ルールはプロトコルに固有です。 SNAT はプロトコルに固有であり、構成は副作用を作成するのではなくプロトコルを反映する必要があります。

#### <a name="multiple-frontends"></a>複数のフロントエンド
送信接続の需要が多くなると予想されるため、または既に多くなっているために、SNAT ポートを増やす必要がある場合は、同じ仮想マシン リソースに対してフロントエンド、ルール、およびバックエンド プールを追加構成することにより、増分 SNAT ポート インベントリを追加することもできます。

#### <a name="control-which-frontend-is-used-for-outbound"></a>送信に使用されるフロントエンドを制御する
アウトバウンド接続を、特定のフロントエンド IP アドレスからの発信のみに制限したい場合は、必要に応じて、アウトバウンド マッピングを表すルールで送信 SNAT を無効にすることができます。

#### <a name="control-outbound-connectivity"></a>送信接続を制御する
Standard Load Balancer は、仮想ネットワークのコンテキスト内に存在します。  仮想ネットワークは、分離されたプライベート ネットワークです。  パブリック IP アドレスとの関連付けが存在しない場合、パブリック接続は許可されません。  [VNET サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、仮想ネットワークの内側にあり、仮想ネットワークに対してローカルであるため、VNET サービス エンドポイントには到達できます。  仮想ネットワークの外部にある宛先への送信接続を確立したい場合は、次の 2 つのオプションがあります。
- Standard SKU のパブリック IP アドレスを、インスタンスレベル パブリック IP アドレスとして、仮想マシン リソースに割り当てます
- または、仮想マシン リソースを、パブリック Standard Load Balancer のバックエンド プールに配置します。

どちらの方法でも、仮想ネットワークから、仮想ネットワークの外部に、送信接続できます。 

仮想マシン リソースが配置されているバックエンド プールに関連付けられている内部 Standard Load Balancer "_のみ_" がある場合、仮想マシンは仮想ネットワーク リソースと [VNET サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に対してだけ到達できます。  前の段落で説明されている手順に従って、送信接続を作成できます。

Standard SKU に関連付けられていない仮想マシン リソースの送信接続は前に説明したままです。

[送信接続の詳細な説明](load-balancer-outbound-connections.md)に関するページをご覧ください。

### <a name="multife"></a>複数のフロントエンド
Load Balancer は、複数のフロントエンドで複数のルールをサポートします。  Standard Load Balancer は、これを送信シナリオまで広げます。  アウトバウンド シナリオは基本的に、インバウンド負荷分散ルールの逆です。  インバウンド負荷分散ルールも、アウトバウンド接続に対する関連付けを作成します。 Standard Load Balancer は、負荷分散ルールを介して、仮想マシン リソースに関連付けられているすべてのフロントエンドを使います。  さらに、負荷分散ルールのパラメーターにより、アウトバウンド接続のために負荷分散ルールを抑制することができ、特定のフロントエンドを選ぶことが (何も選ばないことも) できます。

これに対し、Basic Load Balancer は単一のフロントエンドをランダムに選び、選ばれるものを制御する機能はありません。

[送信接続の詳細な説明](load-balancer-outbound-connections.md)に関するページをご覧ください。

### <a name="operations"></a> 管理操作

Standard Load Balancer リソースは、まったく新しいインフラストラクチャ プラットフォーム上に存在します。  これにより、Standard SKU の管理操作は高速化され、通常、操作が完了するまでの時間は Standard SKU リソースごとに 30 秒未満です。  バックエンド プールのサイズが大きくなると、バックエンド プールの変更に必要な時間も長くなります。

Standard Load Balancer のリソースは変更することができ、ある仮想マシンから別の仮想マシンに Standard パブリック IP アドレスを非常に速く移動することができます。

## <a name="migration-between-skus"></a>SKU 間での移行

SKU は変更不可です。 一方の SKU からもう一方の SKU に移行する場合は、このセクションの手順に従います。

>[!IMPORTANT]
>このドキュメント全体を確認して、SKU ごとの違いを理解し、シナリオを慎重に検証してください。  シナリオに合わせて追加の変更が必要になる場合があります。

### <a name="migrate-from-basic-to-standard-sku"></a>Basic SKU から Standard SKU への移行

1. 新しい Standard リソース (必要に応じて、Load Balancer とパブリック IP) を作成します。 規則とプローブ定義を再作成します。  443/tcp に対する TCP プローブを以前に使用していた場合は、このプローブ プロトコルを HTTPS プローブに変更することを検討し、パスを追加してください。

2. NIC またはサブネットで新規 NSGを作成するか、既存の NSG を更新して、負荷分散されたトラフィック、プローブ、その他の許可するすべてのトラフィックをホワイトリストに登録します。

3. すべての VM インスタンスから Basic SKU リソース (必要に応じて、Load Balancer とパブリック IP) を削除します。 可用性セットのすべての VM インスタンスも削除してください。

4. すべての VM インスタンスを新しい Standard SKU リソースに接続します。

### <a name="migrate-from-standard-to-basic-sku"></a>Standard SKU から Basic SKU への移行

1. 新しい Basic リソース (必要に応じて、Load Balancer とパブリック IP) を作成します。 規則とプローブ定義を再作成します。  443/tcp に対する HTTPS ブローブを TCP プローブに変更します。 

2. すべての VM インスタンスから Standard SKU リソース (必要に応じて、Load Balancer とパブリック IP) を削除します。 可用性セットのすべての VM インスタンスも削除してください。

3. すべての VM インスタンスを新しい Basic SKU リソースに接続します。

>[!IMPORTANT]
>
>Basic SKU と Standard SKU の使用には制限事項があります。
>
>Standard SKU の HA ポート診断機能は、Standard SKU でのみ提供されます。 Standard SKU から Basic SKU に移行し、この機能を保持することはできません。
>
>Basic SKU と Standard SKU には、この記事で説明したような相違点があります。  よく理解し、それに合わせて準備してください。
>
>Load Balancer リソースとパブリック IP リソースには一致する SKU を使用する必要があります。 Basic SKU リソースと Standard SKU リソースを組み合わせることはできません。 スタンドアロン仮想マシン、可用性セット リソース内の仮想マシン、または仮想マシン スケール セット リソースを、両方の SKU に同時にアタッチすることはできません。

## <a name="region-availability"></a>利用可能なリージョン

現在、Standard Load Balancer はすべてのパブリック クラウド リージョンで利用できます。

## <a name="sla"></a>SLA

Standard Load Balancer は、99.99% の SLA で利用できます。  詳しくは、[Standard Load Balancer の SLA](https://aka.ms/lbsla) に関するページをご覧ください。

## <a name="pricing"></a>価格

Standard Load Balancer の使用量は課金されます。

- 構成されている負荷分散規則とアウトバウンド規則の数 (インバウンド NAT 規則はルールの総数にカウントされません)
- 規則に関係なくインバウンドとアウトバウンドで処理されたデータの量 

Standard Load Balancer の価格の情報については、[Load Balancer の価格](https://azure.microsoft.com/pricing/details/load-balancer/)に関するページをご覧ください。

## <a name="limitations"></a>制限事項

- SKU は変更不可です。 既存のリソースの SKU を変更することはできません。
- スタンドアロン仮想マシン リソース、可用性セット リソース、または仮想マシン スケール セット リソースは、1 つの SKU でのみ参照でき、両方では参照できません。
- Load Balancer の規則は、2 つの仮想ネットワークにまたがることはできません。  フロントエンドとその関連するバックエンド インスタンスは、同じ仮想ネットワークに配置されている必要があります。  
- [サブスクリプションの移動操作](../azure-resource-manager/resource-group-move-resources.md)は、Standard SKU LB および PIP リソースではサポートされていません。
- VNet およびその他の Microsoft プラットフォーム サービスなしの Web Worker ロールにアクセスできるのは、事前 VNet サービスおよびその他のプラットフォーム サービスの動作の副作用により、内部の Standard Load Balancer が使用される場合のみです。 各サービス自体または基になるプラットフォームは予告なく変更される場合があるため、これに依存しないでください。 内部の Standard Load Balancer のみを使用する場合は、必要に応じて、[送信接続](load-balancer-outbound-connections.md)を明示的に作成する必要があることを常に想定する必要があります。
- Load Balancer は TCP または UDP 製品であり、これらの特定の IP プロトコルに対する負荷分散とポート フォワーディングを行います。  負荷分散規則と受信 NAT 規則は TCP および UDP についてサポートされており、ICMP を含む他の IP プロトコルについてはサポートされていません。 Load Balancer は、UDP または TCP のフローのペイロードを終了したり、それに応答したり、それ以外の対話を行うことはありません。 プロキシではありません。 フロントエンドへの接続の検証が、負荷分散または受信 NAT 規則 (TCP または UDP) で使用されるのと同じプロトコルの帯域内で成功する必要があり、"_かつ_"、仮想マシンの少なくとも 1 つがクライアントに対するフロントエンドからの応答を生成する必要があります。  Load Balancer フロントエンドからの帯域内応答を受け取らない場合は、仮想マシンが応答できないことを示します。  応答できる仮想マシンがない状態で、Load Balancer フロントエンドと対話することはできません。  これは、[ポート マスカレード SNAT](load-balancer-outbound-connections.md#snat) が TCP および UDP に対してのみサポートされている送信接続にも当てはまります。ICMP などの他の IP プロトコルも失敗します。  軽減のためにインスタンスレベルのパブリック IP アドレスを割り当てます。
- 仮想ネットワーク内のプライベート IP アドレスからパブリック IP アドレスに遷移するときに[送信接続](load-balancer-outbound-connections.md)を提供するパブリック ロード バランサーとは異なり、内部ロード バランサーは、内部ロード バランサーのフロントエンドへの送信発信接続を変換しません (両方ともプライベート IP アドレス空間内にあるため)。  これにより、変換が必要ない固有内部 IP アドレス空間内の SNAT 枯渇が発生する可能性が回避されます。  副作用として、バックエンド プール内の VM からの送信フローが、それが存在するプール内の内部ロード バランサーのフロントエンド サーバーへのフローを試み、"_かつ_"、それ自体にマップバックされている場合、フローの両方のレッグは一致せず、フローは失敗します。  フローが、フロントエンドへのフローを作成したバックエンド プール内の同じ VM にマップバックしなかった場合、フローは成功します。   フローがそれ自体にマップバックする場合、送信フローは VM からフロントエンドに発信されるように見え、対応する受信フローは VM からそれ自体に発信されるように見えます。 ゲスト OS の観点からは、同じフローの受信部分と送信部分は、仮想マシン内と一致しません。 送信元と送信先が一致しないため、TCP スタックは、同じフローのこれらの半分を、同じフローの一部と認識しません。  フローがバックエンド プール内の他の VM にマップする場合、フローの半分は一致し、VM はフローに正常に応答できます。  このシナリオの現象は、断続的な接続のタイムアウトです。 このシナリオを確実に実現するためのいくつかの一般的な回避策があり (バックエンド プールから、バックエンド プールのそれぞれの内部ロード バランサー フロントエンドへの送信フロー)、それには内部ロード バランサーの背後にあるサード パーティ製プロキシの挿入、または [DSR スタイル規則の使用](load-balancer-multivip-overview.md)が含まれます。  パブリック ロード バランサーを使って軽減できますが、結果として得られるシナリオは、[SNAT の枯渇](load-balancer-outbound-connections.md#snat)が発生しやすいでの、慎重に管理されている場合を除き、回避する必要があります。

## <a name="next-steps"></a>次の手順

- [Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)の使用について学習する。
- [正常性プローブ](load-balancer-custom-probe-overview.md)について学習する。
- [可用性ゾーン](../availability-zones/az-overview.md)の詳細を学習する。
- [Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)について学習する。
- [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) での診断で[サポートされる多次元メトリック](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers)について学習する。
- [送信接続に対する Load Balancer](load-balancer-outbound-connections.md) の使用について学習する。
- [アウトバウンド規則](load-balancer-outbound-rules-overview.md)について学習する。
- [アイドル時の TCP リセット](load-balancer-tcp-reset.md)について学習する。
- [HA ポート負荷分散ルールでの Standard Load Balancer](load-balancer-ha-ports-overview.md) について学習する。
- [複数のフロントエンドでの Load Balancer](load-balancer-multivip-overview.md) の使用について学習する。
- [仮想ネットワーク](../virtual-network/virtual-networks-overview.md)について学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)の詳細を確認する。
- [VNET サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)について学習する。
- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。
- [Load Balancer](load-balancer-overview.md) について詳しく学習する。
