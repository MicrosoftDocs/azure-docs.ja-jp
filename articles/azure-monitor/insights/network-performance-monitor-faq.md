---
title: FAQ - Azure の Network Performance Monitor ソリューション | Microsoft Docs
description: この記事では、Azure の Network Performance Monitor についてよく寄せられる質問について説明します。 Network Performance Monitor (NPM) は、ネットワークのパフォーマンスをほぼリアルタイムで監視し、ネットワーク パフォーマンスのボトルネックを検出して特定するのに役立ちます。
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 909cbd6174fe7eceaa8b53b5ba44fe72990b56d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708052"
---
# <a name="network-performance-monitor-solution-faq"></a>Network Performance Monitor ソリューションの FAQ

![ネットワーク パフォーマンス モニターのシンボル](media/network-performance-monitor-faq/npm-symbol.png)

> [!IMPORTANT]
> 2021 年 7 月 1 日以降、既存のワークスペースに新しいテストを追加したり、Network Performance Monitor で新しいワークスペースを有効にしたりできなくなります。 2021 年 7 月 1 日より前に作成されたテストは使い続けることができます。 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、[Network Performance Monitor から Azure Network Watcher の新しい接続モニターにテストを移行](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md)します。

この記事では、Azure の Network Performance Monitor (NPM) についてよく寄せられる質問 (FAQ) について説明します

[Network Performance Monitor](../../networking/network-monitoring-overview.md) は、クラウド ベースの[ハイブリッド ネットワーク監視](./network-performance-monitor-performance-monitor.md)ソリューションであり、ネットワーク インフラストラクチャ内のさまざまなポイント間のネットワーク パフォーマンスを監視するのに役立ちます。 また、[サービスやアプリケーションのエンドポイント](./network-performance-monitor-service-connectivity.md)へのネットワーク接続の監視、および[ Azure ExpressRoute のパフォーマンスの監視](./network-performance-monitor-expressroute.md)にも利用できます。 

トラフィックのブラックホール、ルーティング エラーなどのネットワークの問題のほか、従来のネットワーク監視手法では検出されない問題を検出します。 ネットワーク リンクに関するしきい値を超えた場合、このソリューションはアラートと通知を生成します。 また、ネットワーク パフォーマンスの問題をタイムリーに検出できるほか、問題の原因を特定のネットワーク セグメントまたはデバイスに限定できます。 

[Network Performance Monitor](../../networking/network-monitoring-overview.md) がサポートするさまざまな機能の詳細は、オンラインで参照できます。

## <a name="set-up-and-configure-agents"></a>エージェントのセットアップと構成

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>NPM による監視に使用するノードにはどのようなプラットフォーム要件がありますか。
NPM の様々な機能に対するプラットフォーム要件を、以下に示します。

- NPM のパフォーマンス モニターとサービス接続モニターの機能は、Windows Server および Windows デスクトップ/クライアント オペレーティング システムの両方をサポートします。 サポートされている Windows Server OS のバージョンは 2008 SP1 以降です。 サポートされている Windows デスクトップ/クライアント バージョンは、Windows 10、Windows 8.1、Windows 8、および Windows 7 です。 
- NPM の ExpressRoute モニター機能は Windows Server (2008 SP1 またはそれ以降) オペレーティング システムのみをサポートします。

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>NPM 内の監視ノードとして Linux マシンを使用できますか。
Linux ベースのノードを使用してネットワークを監視する機能が一般提供されました。 [こちら](../../virtual-machines/extensions/oms-linux.md)からエージェントにアクセスします。 

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>NPM による監視で使用するノードのサイズ要件はありますか。
ネットワークを監視するためにノード VM 上に NPM ソリューションを実行するには、ノードには少なくとも 500 MB のメモリと 1 つのコアが必要です。 NPM を実行するために別のノードを使用する必要はありません。 ソリューションは、別のワークロードが実行されているノードでも実行できます。 このソリューションには、その CPU 使用率が 5% を超えた場合に監視プロセスを停止する機能があります。

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>NPM を使用するには、ノードを直接エージェントとして、または System Center Operations Manager を介して接続する必要がありますか。
パフォーマンス モニター機能とサービス接続モニター機能のどちらも、[直接エージェントとして接続されている](../agents/agent-windows.md)ノードと [Operations Manager 経由で接続されている](../agents/om-agents.md)ノードをサポートします。

ExpressRoute モニター機能では、Azure ノードは直接エージェントとしてのみ接続する必要があります。 Operations Manager 経由で接続されている Azure ノードはサポートされません。 オンプレミス ノードの場合、直接エージェントとして接続されるノードおよび Operations Manager を介して接続されているノードは、ExpressRoute 回線の監視用としてサポートされます。

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>TCP と ICMP のどちらのプロトコルを監視用に選択する必要がありますか。
Windows Server ベースのノードを使用してネットワークを監視する場合は、正確さに優れている TCP を監視プロトコルとして使用することをお勧めします。 

Windows デスクトップ/クライアント オペレーティング システム ベースのノードには、ICMP を使用することをお勧めします。 このプラットフォームでは、NPM がネットワーク トポロジの検出に使用する RAW ソケット経由での TCP データの送信は許可されていません。

各プロトコルの相対的な利点の詳細を[ここ](./network-performance-monitor-performance-monitor.md#choose-the-protocol)から取得できます。

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>TCP プロトコルを使用して監視をサポートするにはノードをどのように構成できますか。
TCP プロトコルを使用してノードが監視をサポートするには、次のようにします。 
* ノードのプラットフォームが Windows Server (2008 SP1 またはそれ以降) であるようにします。
* [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell スクリプトをノードで実行します。 詳細については、[手順](./network-performance-monitor.md#configure-log-analytics-agents-for-monitoring)を参照してください。


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>監視のために NPM で使用する TCP ポートをどのように変更すればよいですか。
監視のために NPM によって使用される TCP ポートを、[EnableRules.ps1](https://aka.ms/npmpowershellscript) スクリプトを実行することによって変更できます。 パラメーターとして使用するポート番号を入力する必要があります。 たとえば、TCP ポート 8060 を有効にするには、`EnableRules.ps1 8060` を実行します。 監視に使用するすべてのノードで同じ TCP ポートを使用するようにしてください。

このスクリプトでは、Windows ファイアウォールがローカルでのみ構成されます。 ネットワーク ファイアウォールまたはネットワーク セキュリティ グループ (NSG) 規則がある場合は、NPM で使用される TCP ポート宛てのトラフィックを許可するようにしてください。

### <a name="how-many-agents-should-i-use"></a>いくつのエージェントを使用する必要がありますか。
監視する各サブネットに対して少なくとも 1 つのエージェントを使用してください。

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>使用できるエージェントの最大数はいくつですか。または、構成の上限に達したことを示すエラーが表示されます。
NPM では、ワークスペースあたりの IP アドレス数が 5,000 個に制限されます。 ノードに IPv4 と IPv6 の両方のアドレスがある場合、そのノードに 2 つの IP アドレスがあるものとしてカウントされます。 したがって、5,000 個の IP アドレスというこの制限によって、エージェント数の上限が決まります。 [NPM] > [構成] の [ノード] タブを使用して、非アクティブなエージェントを削除できます。 NPM には、エージェントをホストしている VM にこれまで割り当てられたすべての IP アドレスの履歴も保持されます。それぞれは、5,000 個の IP アドレスの上限に関係する個別の IP アドレスとしてカウントされます。 ワークスペース用に IP アドレスを解放するために、使用されていない IP アドレスを [ノード] ページで削除できます。

## <a name="monitoring"></a>監視

### <a name="how-are-loss-and-latency-calculated"></a>損失と待機時間をどのように計算しますか。
ソース エージェントは、TCP SYN 要求 (監視用プロトコルとして TCP が選択された場合) または ICMP ECHO 要求 (監視用プロトコルとして ICMP が選択された場合) のいずれかを宛先 IP に一定の間隔で送信して、ソースと宛先の IP の組み合わせの間のすべてのパスに対応できるようにします。 受信したパケットの割合およびパケットのラウンドトリップ時間が測定されて、各パスの損失と待機時間が計算されます。 このデータは、特定のポーリング間隔での IP の組み合わせについての損失と待機時間の集計値を取得するために、ポーリング間隔およびすべてのパスにわたって集計されます。

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>ソース エージェントはどの程度の頻度で監視対象の宛先にパケットを送信しますか。
パフォーマンス モニター機能と ExpressRoute モニター機能の場合、ソースは 5 秒ごとにパケットを送信し、ネットワーク測定を記録します。 このデータは 3 分間のポーリング間隔で集計され、損失と待機時間の平均値およびピーク時の値が計算されます。 サービス接続モニター機能の場合、ネットワーク測定のためにパケットを送信する頻度は、特定のテストについてテストを構成するときにユーザーが入力した頻度によって決まります。

### <a name="how-many-packets-are-sent-for-monitoring"></a>監視用にいくつのパケットが送信されますか。
ポーリングでソース エージェントから宛先に送信されるパケット数は適応性があり、当社独自のアルゴリズムによって決まるため、ネットワーク トポロジの種類によって異なる場合があります。 ソースと宛先の IP の組み合わせの間でのネットワーク パスの数が多くなるほど、送信されるパケットの数も多くなります。 システムにより、ソースと宛先の IP の組み合わせの間のすべてのパスに対応するようになります。

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>NPM はソースと宛先の間のネットワーク トポロジをどのように検出しますか。
NPM は、Traceroute に基づく独自のアルゴリズムを使用して、ソースと宛先の間のすべてのパスとホップを検出します。

### <a name="does-npm-provide-routing-and-switching-level-info"></a>NPM はルーティング レベルおよびスイッチング レベルの情報を提供しますか。 
NPM は、ソース エージェントと宛先の間のすべての可能なルートを検出できますが、特定のワークロードによって送信されたパケットが通ったルートを表示することはできません。 ソリューションは、予想よりも多くの待機時間を追加した、パスとその基になるネットワーク ホップを特定するのに役立ちます。

### <a name="why-are-some-of-the-paths-unhealthy"></a>いくつかのパスが正常でないのはなぜですか。
ソースと宛先の IP の間には様々なネットワーク パスが存在し、パスによって損失と待機時間の値が異なることがあります。 NPM がパスを異常としてマークする (赤色で示す) のは、損失または待機時間の値が監視構成で設定されたそれぞれのしきい値を超えた場合です。

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>ネットワーク トポロジ マップで赤色のホップは何を表していますか。
ホップが赤色の場合、そのホップは少なくとも 1 つの異常なパスの一部であることを示しています。 NPM は異常なパスをマークするだけで、各パスの正常性状態を分離することはできません。 問題のあるホップを特定するには、ホップ単位の待機時間を表示し、予想以上の待ち時間を追加するホップを分離します。

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>パフォーマンス モニターの障害の特定はどのように機能しますか。
NPM は確率論的メカニズムを使用して、各ネットワーク パス、ネットワーク セグメント、および構成要素のネットワーク ホップに対し、それらがその一部となる異常なパスの数に基づき障害確率を割り当てます。 ネットワーク セグメントとホップが異常なパスの一部になる場合が多くなるほど、それらに関連付けられる障害確率が増加します。 このアルゴリズムは、NPM エージェントが相互に接続された多くのノードがある場合に最適です。この場合は障害確率を計算するためのデータ ポイントが増加するためです。

### <a name="how-can-i-create-alerts-in-npm"></a>NPM でアラートを作成する方法はありますか。
現時点では、NPM UI からのアラートの作成は、既知の問題により失敗します。 [手動でアラートを作成](../alerts/alerts-log.md)してください。

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>アラート用の既定の Log Analytics クエリはどのようなものですか。
パフォーマンス モニター クエリ

```kusto
NetworkMonitoring
 | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
```

サービス接続モニター クエリ

```kusto
NetworkMonitoring
 | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
```

ExpressRoute モニター クエリ: 回線クエリ

```kusto
NetworkMonitoring
 | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"
```

プライベート ピアリング

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"
```

Microsoft ピアリング

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"
```

一般的なクエリ

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy")
```

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>NPM はルーターとサーバーを個別のデバイスとして監視できますか。
NPM は、ソース IP と宛先 IP の間の基になるネットワーク ホップ (スイッチ、ルーター、サーバーなど) の IP およびホスト名のみを識別します。 また、識別されたこれらのホップ間の待機時間を識別します。 これらの基になるホップを個別に監視しません。

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM を使用して Azure と AWS の間のネットワーク接続を監視できますか。
はい。 詳細については、[NPM を使用した Azure、AWS、およびオンプレミス ネットワークの監視](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)に関するページを参照してください。

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>ExpressRoute の帯域幅使用量は着信または発信のどちらですか。
帯域幅使用量は着信および発信の帯域幅の合計です。 これはビット/秒で表されます。

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>ExpressRoute についての着信および発信の帯域幅の情報を入手できますか。
プライマリ帯域幅とセカンダリ帯域幅の両方についての着信および発信の値を取得できます。

MS ピアリング レベル情報については、ログ検索で次に示すクエリを使用します。

```kusto
NetworkMonitoring
 | where SubType == "ERMSPeeringUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond 
```

プライベート ピアリング レベル情報については、ログ検索で次に示すクエリを使用します。

```kusto
NetworkMonitoring
 | where SubType == "ERVNetConnectionUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond
```

回線レベル情報については、ログ検索で次に示すクエリを使用します。

```kusto
NetworkMonitoring
 | where SubType == "ERCircuitTotalUtilization"
 | project CircuitName, BitsInPerSecond, BitsOutPerSecond
```

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>NPM のパフォーマンス モニターではどのリージョンがサポートされていますか。
NPM は、[サポートされるリージョン](./network-performance-monitor.md#supported-regions)のいずれかでホストされているワークスペースから、世界のあらゆる地域でのネットワーク間の接続を監視できます

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>NPM のサービス接続モニターではどのリージョンがサポートされていますか。
NPM は、[サポートされるリージョン](./network-performance-monitor.md#supported-regions)のいずれかでホストされているワークスペースから、世界のあらゆる地域でのサービスへの接続を監視できます

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>NPM の ExpressRoute モニターではどのリージョンがサポートされていますか。
NPM は、Azure のすべてのリージョンにある ExpressRoute 回線を監視できます。 NPM をオンボードする場合、[サポートされているリージョン](../../expressroute/how-to-npm.md)のいずれかでホストする必要がある Log Analytics ワークスペースが必要です

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>ネットワーク トポロジ ビューでいくつかのホップが識別されていないものとしてマークされているはなぜですか。
NPM は、traceroute の修正バージョンを使用して、ソース エージェントから宛先へのトポロジを検出します。 識別不能のホップは、ネットワーク ホップがソース エージェントの traceroute 要求に応答しなかったことを表します。 3 つの連続するネットワーク ホップがエージェントの traceroute に応答しない場合、ソリューションはこれらの応答しないホップを識別不能ホップとしてマークし、追加のホップを検出しようとしません。

ホップは以下の 1 つ以上のシナリオで traceroute に応答しないことがあります。

* ルーターが ID を公開しないように構成されている。
* ネットワーク デバイスが ICMP_TTL_EXCEEDED トラフィックを許可していない。
* ネットワーク デバイスからの ICMP_TTL_EXCEEDED 応答がファイアウォールによってブロックされている。

どちらかのエンドポイントが Azure に存在する場合、Azure インフラストラクチャで ID が traceroute に公開されないため、traceroute で識別不能ホップが示されます。 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>異常なテストに関するアラートが表示されますが、NPM の損失と待機時間のグラフに高い値が表示されません。 何が異常であるかを確認するにはどうすればよいですか。
NPM は、ソースと宛先の間のエンド ツー エンドの待機時間が、それらの間のいずれかのパスのしきい値を超えた場合にアラートを生成します。 一部のネットワークには、同じソースと宛先を接続している複数のパスがあります。 いずれかのパスに異常があると、NPM はアラートを生成します。 グラフに表示される損失と待機時間は、すべてのパスの平均値であるため、1 つのパスの正確な値を示すとは限りません。 しきい値を超えた場所を把握するには、アラートの "サブタイプ" 列を確認します。 この問題がパスによって発生している場合、サブタイプ値は NetworkPath (パフォーマンス モニター テストの場合)、EndpointPath (サービス接続モニター テストの場合)、および ExpressRoutePath (ExpressRoute モニター テストの場合) になります。 

パスが異常であることを検索するサンプル クエリ:

```kusto
NetworkMonitoring
 | where ( SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
 | project SubType, LossHealthState, LatencyHealthState, MedianLatency
```

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>テストに異常が表示されるのに、トポロジには表示されないのはなぜですか 
NPM は、エンドツーエンドの損失、待ち時間、およびトポロジをさまざまな間隔で監視します。 損失と待ち時間は 5 秒に 1 回測定され、3 分ごとに集計されます (Performance Monitor と Express Route Monitor の場合)。一方、トポロジは、10 分に 1 回 traceroute を使用して計算されます。 たとえば、3 時 44 分から 4 時 04 分までに、トポロジは 3 回 (3 時 44 分、3 時 54 分、4 時 04 分) 更新される可能性がありますが、損失と待ち時間は約 7 回 (3 時 44 分、3 時 47 分、3 時 50 分、3 時 53 分、3 時 56 分、3 時 59 分、4 時 02 分) 更新されます。 3 時 54 分に生成されるトポロジは、3 時 56 分、3 時 59 分、および 4 時 02 分に計算される損失と待ち時間に対してレンダリングされます。 たとえば、3 時 59 分に ER 回線の異常を示すアラートを受け取ったとします。 あなたは、NPM にログオンし、トポロジの時刻を 3 時 59 分に設定します。 NPM には、3 時 54 分に生成されたトポロジがレンダリングされます。 ネットワークの最後の既知のトポロジを理解するには、TimeProcessed (損失と待ち時間が計算された時刻) フィールドと TracerouteCompletedTime (トポロジが計算された時刻) を比較します。 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>NetworkMonitoring テーブルの E2EMedianLatency フィールドと AvgHopLatencyList フィールドの違いは何ですか
E2EMedianLatency は、tcp ping テストの結果を集計した後 3 分ごとに更新される待ち時間です。AvgHopLatencyList は、traceroute に基づいて 10 分ごとに更新されます。 E2EMedianLatency が計算された正確な時刻を知るには、TimeProcessed フィールドを使用します。 traceroute が完了して AvgHopLatencyList を更新した正確な時刻を知るには、TracerouteCompletedTime フィールドを使用します

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>ホップバイホップの待ち時間の値が HopLatencyValues と異なるのはなぜですか 
HopLatencyValues は、ソースからエンドポイントまでを示します。
たとえば次のようになります。ホップ - A、B、C。 AvgHopLatency - 10、15、20。 つまり、ソースから A への待ち時間 = 10、ソースから B への待ち時間 = 15、ソースから C への待ち時間は 20 です。 UI では、トポロジ内の A-B ホップ間の待ち時間が 5 として計算されます

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>ソリューションは 100% の損失と示していますがソースと宛先の間の接続が存在します
これが発生することがあるのは、ホスト ファイアウォールまたは中間ファイアウォール (ネットワーク ファイアウォールまたは Azure NSG) のいずれかが、NPM による監視に使用されているポート経由で (顧客が変更しない場合の既定はポート 8084) ソース エージェントと宛先の間の通信をブロックしている場合です。

* ホスト ファイアウォールが必要なポートの通信をブロックしていないことを確認するには、次のビューからソースと宛先ノードの正常性状態を表示します。[Network Performance Monitor] -> [構成] -> [ノード] 
  正常でない場合、指示を表示して、是正措置を行います。 ノードが正常である場合は、以下の手順 b に移動します。 設定します。
* 中間ネットワーク ファイアウォールまたは Azure NSG が必要なポートで通信をブロックしていないことを確認するには、次の手順を使用して、サード パーティ製の PsPing ユーティリティを使用します。
  * psping ユーティリティは[こちら](/sysinternals/downloads/psping)からダウンロードできます 
  * ソース ノードから次のコマンドを実行します。
    * psping -n 15 \<destination node IPAddress\>:portNumber。既定で NPM には 8084 ポートが使用されます。 この値を EnableRules.ps1 スクリプトを使用して明示的に変更した場合、使用するカスタム ポート番号を入力します。 これは Azure マシンからオンプレミスへの ping です
* ping が成功したかどうかを確認します。 成功しない場合は、中間ネットワーク ファイアウォールまたは Azure NSG がこのポートでトラフィックをブロックしていることを示しています。
* ここで、宛先ノードからソース ノード IP にコマンドを実行します。


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>ノード A から B への損失があるのにノード B から A への損失がないのはなぜですか。
A から B へのネットワーク パスが B から A へのネットワーク パスと異なる場合があるように、損失と待機時間についても異なる値が観察されることがあります。

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>すべての ExpressRoute 回線とピアリング接続が検出されないのはなぜですか。
NPM は、ユーザーがアクセスできるすべてのサブスクリプションの ExpressRoute 回線とピアリング接続を検出します。 Express Route リソースがリンクされているすべてのサブスクリプションを選択し、検出された各リソースの監視を有効にします。 NPM はプライベート ピアリングの検出時に、接続オブジェクトを検索するため、VNET がピアリングに関連付けられているかどうかを確認してください。 NPM では、Log Analytics ワークスペースとは異なるテナントにある回線とピアリングが検出されません。

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>ER モニター機能で「Traffic is not passing through ANY circuit」という診断メッセージが表示されます。 これはどういう意味でしょうか。

オンプレミスと Azure ノード間の接続は正常ですが、NPM によって監視されるように構成された ExpressRoute 回線をトラフィックが通過していないというシナリオがある場合があります。 

これは次の場合に発生する可能性があります。

* ER 回線がダウンしている。
* ルート フィルターが、目的の ExpressRoute 回線よりも、他のルート (VPN 接続や他の ExpressRoute 回線) に高い優先順位を与えるように構成されている。 
* 監視構成で ExpressRoute 回線を監視するように選択されたオンプレミスと Azure ノードが、目的の ExpressRoute 回線経由で相互接続されていない。 監視対象の ExpressRoute 回線経由で相互に接続されている適切なノードを選択してください。

### <a name="why-does-expressroute-monitor-report-my-circuitpeering-as-unhealthy-when-it-is-available-and-passing-data"></a>回線またはピアリングが使用可能であり、データが渡されている場合でも、ExpressRoute モニターに異常と報告されるのはなぜですか。
ExpressRoute モニターでは、エージェントまたはサービスから報告されたネットワーク パフォーマンス値 (損失、待機時間、帯域幅の使用率) と、構成時に設定されたしきい値が比較されます。 回線の場合、報告された帯域幅の使用率が構成で設定されたしきい値よりも高い場合、回線は異常とマークされます。 ピアリングの場合、報告された損失、待機時間、または帯域幅の使用率が構成で設定されたしきい値よりも高い場合、ピアリングは異常とマークされます。 NPM には、正常性の状態を判断するためにメトリックやその他の形式のデータが利用されません。

### <a name="why-does-expressroute-monitorbandwidth-utilization-report-a-value-different-from-metrics-bits-inout"></a>ExpressRoute モニターの帯域幅の使用率にメトリックのビットの送受信とは異なる値が報告されるのはなぜですか
ExpressRoute モニターの場合、帯域幅の使用率は、過去 20 分間の受信および送信の帯域幅の平均値であり、ビット/秒で表されます。Express Route メトリックの場合、ビットの送受信は 1 分あたりのデータ ポイントです。内部的に両方に使用されるデータセットは同じですが、集計値は NPM と ER の各メトリックで異なります。 きめ細かい、分単位の監視と高速アラートの場合は、ER メトリックに直接アラートを設定することをお勧めします

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>ExpressRoute 回線の監視を構成中に Azure ノードが検出されません。
これは Azure ノードが Operations Manager 経由で接続されているときに発生することがあります。 ExpressRoute モニター機能は、直接エージェントとして接続された Azure ノードのみサポートします。

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>OMS ポータルで ExpressRoute 回線によって検出できません
NPM は Azure portal からでも OMS ポータルからでも使用できますが、ExpressRoute モニター機能の回線検出は、Azure portal からのみ動作します。 Azure portal によって回線が検出されると、2 つのポータルのいずれかの機能を使用できます。 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>サービス接続モニター機能で、サービス応答時間、ネットワーク損失、および待機時間が NA と表示されます
これは次のいずれかが該当する場合に発生することがあります。

* サービスがダウンしている。
* サービスへのネットワーク接続のチェックに使用されているノードがダウンしている。
* テスト構成で入力したターゲットが正しくない。
* ノードにネットワーク接続がない。

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>サービス接続モニター機能で、有効なサービス応答時間が表示されているものの、ネットワーク損失と待機時間が NA と表示されます
 これは次のいずれかが該当する場合に発生することがあります。

* サービスへのネットワーク接続のチェックに使用されたノードが Windows クライアント コンピューターである場合は、ターゲット サービスが ICMP 要求をブロックしているか、またはネットワーク ファイアウォールがノードからの ICMP 要求をブロックしている。
* テスト構成で [ネットワークの測定を実行します] チェック ボックスがオフになっている。

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>サービス接続モニター機能で、サービス応答時間が NA ですが、ネットワーク損失および待機時間が有効になっています
これは、ターゲット サービスが Web アプリケーションではなく、テストが Web テストとして構成されている場合に発生することがあります。 テスト構成を編集し、テストの種類として、[Web] ではなく [ネットワーク] を選んでください。

## <a name="miscellaneous"></a>その他

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>監視に使用されるノードでパフォーマンスに影響はありますか。
NPM プロセスは、ホスト CPU リソースの 5% 以上を使用した場合に停止するよう構成されています。 これにより、パフォーマンスに影響を及ぼすことなく、ノードをノードの通常のワークロードのために使用し続けることができます。

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>NPM は監視目的でファイアウォール ルールを編集しますか。
NPM は、指定したポートでの TCP 相互接続をエージェントが作成できるようにするために、EnableRules.ps1 PowerShell スクリプトを実行するノード上でローカルの Windows ファイアウォール ルールを作成することのみ行います。 このソリューションは、ネットワーク ファイアウォールまたはネットワーク セキュリティ グループ (NSG) ルールを変更しません。

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>監視に使用されるノードの正常性をどのように確認できますか。
監視に使用されるノードの正常性状態は、次のビューから表示できます。[Network Performance Monitor] -> [構成] -> [ノード] ノードが正常でない場合は、エラーの詳細を表示し、推奨されるアクションを実行できます。

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>NPM は待機時間をマイクロ秒単位で報告できますか。
NPM は UI では待機時間の数値をミリ秒単位に丸めます。 このデータは、さらに高い精度で (場合によっては小数点以下 4 桁で) 格納されます。

## <a name="next-steps"></a>次のステップ

- [Azure の Network Performance Monitor ソリューション](./network-performance-monitor.md)を参照して、Network Performance Monitor の詳細について学習します。