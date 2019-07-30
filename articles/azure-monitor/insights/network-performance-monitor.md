---
title: Azure の Network Performance Monitor ソリューション | Microsoft Docs
description: Azure の Network Performance Monitor は、ネットワークのパフォーマンスを監視して、ネットワーク パフォーマンスのボトルネックをほぼリアルタイムで検出して特定するのに役立ちます。
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: vinigam
ms.openlocfilehash: c350ca3cd8cbfb5e550fccd0bae0df53168de178
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312075"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Azure の Network Performance Monitor ソリューション

![ネットワーク パフォーマンス モニターのシンボル](./media/network-performance-monitor/npm-symbol.png)


Network Performance Monitor は、クラウド ベースのハイブリッド ネットワーク監視ソリューションであり、ネットワーク インフラストラクチャ内のさまざまなポイント間のネットワーク パフォーマンスを監視するのに役立ちます。 また、サービスやアプリケーションのエンドポイントへのネットワーク接続の監視、および Azure ExpressRoute のパフォーマンスの監視にも利用できます。 

トラフィックのブラックホール、ルーティング エラーなどのネットワークの問題のほか、従来のネットワーク監視手法では検出されない問題を検出します。 ネットワーク リンクに関するしきい値を超えた場合、このソリューションはアラートと通知を生成します。 また、ネットワーク パフォーマンスの問題をタイムリーに検出できるほか、問題の原因を特定のネットワーク セグメントまたはデバイスに限定できます。 

Network Performance Monitor では、次の 3 つの広範な機能が提供されます。 

* [[パフォーマンス モニター]](network-performance-monitor-performance-monitor.md): クラウドの展開やオンプレミスの場所、複数のデータ センターやブランチ オフィス、ミッション クリティカルな多層アプリケーション/マイクロ サービスのネットワーク接続を監視できます。 パフォーマンスの監視では、ユーザーから不満があがる前に、ネットワークの問題を検出できます。

* [[サービス接続モニター]](network-performance-monitor-service-connectivity.md): 注意を払ってサービスへのユーザーからの接続を監視して、パス内のインフラストラクチャを特定し、ネットワークのボトルネックが発生している場所を識別できます。 ユーザーより前に障害について把握して、ネットワーク パスの問題の正確な場所を確認できます。 

    この機能では、HTTP、HTTPS、TCP、および ICMP に基づくテストを行うことで、サービスの可用性と応答時間を、ほぼリアルタイムに、または履歴情報として監視できます。 ネットワークに起因するパケット損失と待ち時間を監視することもできます。 ネットワーク トポロジ マップでは、ネットワークの速度低下を分離できます。 各ホップでの待ち時間のデータを使ってノードからサービスへのネットワーク パスで発生する問題点を識別できます。 組み込みのテストでは、Office 365 や Dynamics CRM へのネットワーク接続を事前構成なしで監視できます。 この機能により、Web サイト、SaaS アプリケーション、PaaS アプリケーション、SQL データベースなどの TCP 対応のエンドポイントへのネットワーク接続を監視することができます。

* [ExpressRoute モニター](network-performance-monitor-expressroute.md): Azure ExpressRoute 経由での、お客様のブランチ オフィスと Azure の間のエンド ツー エンド接続とパフォーマンスを監視します。  

[Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) がサポートするさまざまな機能の詳細は、オンラインで参照できます。
 
## <a name="supported-regions"></a>サポートされているリージョン
NPM は、次のリージョンのいずれかでホストされているワークスペースから、世界の任意の部分のネットワークまたはアプリケーション間の接続を監視できます。
* 西ヨーロッパ
* 米国中西部
* East US
* 東日本
* 東南アジア
* オーストラリア南東部
* 英国南部
* インド中部
* 米国政府バージニア州


ExpressRoute モニターのサポート対象リージョンの一覧は、[こちらのドキュメント](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117)で確認できます。


## <a name="set-up-and-configure"></a>セットアップと構成

### <a name="install-and-configure-agents"></a>エージェントのインストールと構成 

「[Windows コンピューターを Azure Monitor に接続する](../platform/agent-windows.md)」と「[Operations Manager を Azure Monitor に接続する](../platform/om-agents.md)」にある、エージェントをインストールするための基本的な手順に従ってください。

### <a name="where-to-install-the-agents"></a>エージェントをインストールする場所 

* **[パフォーマンス モニター]** : Log Analytics エージェントは、他のサブネットワークへのネットワーク接続を監視するサブネットワークのそれぞれに接続されている、少なくとも 1 つのノードにインストールします。

    ネットワーク リンクを監視するには、このリンクの両方のエンドポイントにエージェントをインストールします。 ネットワークのトポロジがわからない場合、ネットワーク パフォーマンスの監視対象の間に重要なワークロードがあるサーバーに、エージェントをインストールしてください。 たとえば、Web サーバーと SQL を実行しているサーバー間のネットワーク接続を監視する場合は、両方のサーバーにエージェントをインストールします。 エージェントによって監視されるのはホストそのものではなく、ホスト間のネットワーク接続 (リンク) です。 

* **[サービス接続モニター]** : Log Analytics エージェントは、サービス エンドポイントへのネットワーク接続を関する各ノードにインストールします。 たとえば、O1、O2、O3 というラベルの付いたオフィス サイトから Office 365 へのネットワーク接続を監視する場合は、 O1、O2、O3 それぞれの少なくとも 1 つのノードに、Log Analytics エージェントをインストールします。 

* **ExpressRoute モニター**: Azure 仮想ネットワークに少なくとも 1 つの Log Analytics エージェントをインストールします。 また、オンプレミスのサブネットワークに少なくとも 1 つのエージェントをインストールし、ExpressRoute のプライベート ピアリング経由で接続します。  

### <a name="configure-log-analytics-agents-for-monitoring"></a>監視用の Log Analytics エージェントの構成 

Network Performance Monitor は、代理トランザクションを使って、送信元と宛先のエージェント間のネットワーク パフォーマンスを監視します。 パフォーマンス モニター機能とサービス接続モニター機能では、監視用のプロトコルとして TCP と ICMP のいずれかを選択できます。 TCP のみが ExpressRoute モニターの監視プロトコルとして使用できます。 ファイアウォールで、選択したプロトコルで監視に使わる Log Analytics エージェント間の通信が許可されていることを確認します。 

* **TCP プロトコル**: 監視用のプロトコルとして TCP を選んだ場合は、Network Performance Monitor と ExpressRoute モニターに使われるエージェントのファイアウォール ポートを開いて、エージェントが確実に相互接続できるようにします。 ポートを開くには、[EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell スクリプトを、管理者特権の PowerShell ウィンドウでパラメーターを指定せずに実行します。

    このスクリプトは、ソリューションで必要なレジストリ キーを作成します。 エージェントが互いに TCP 接続を作成することを許可する Windows ファイアウォール規則も作成されます。 スクリプトによって作成されたレジストリ キーは、デバッグ ログとログ ファイルのパスを記録するかどうかを指定します。 このスクリプトは、また、通信で使われるエージェント TCP ポートを定義します。 これらのキーの値は、スクリプトによって自動的に設定されます。 これらのキーを手動で変更しないでください。 既定で開かれるポートは 8084 です。 パラメーター "portNumber" をスクリプトに指定することでカスタム ポートを使用できます。 スクリプトが実行されるすべてのコンピューターで同じポートを使います。 

    >[!NOTE]
    > このスクリプトでは、Windows ファイアウォールがローカルでのみ構成されます。 ネットワーク ファイアウォールがある場合、ネットワーク パフォーマンス モニターによって使われている TCP ポート宛てのトラフィックを許可する必要があります。

    >[!NOTE]
    > サービス接続モニターで [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell スクリプトを実行する必要はありません。

    

* **ICMP プロトコル**: 監視用のプロトコルとして ICMP を選んだ場合は、ICMP を確実に利用するための次のファイアウォール規則を有効にします。
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>ソリューションの構成 

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) から Network Performance Monitor ソリューションをワークスペースに追加します。 [Solutions Gallery からの Azure Monitor ソリューションの追加](../../azure-monitor/insights/solutions.md)に関するページで説明されている手順も使用できます。 
2. Log Analytics ワークスペースを開いて、 **[概要]** タイルを選びます。 
3. " *このソリューションにはさらに構成が必要です*" というメッセージが表示されている **[ネットワーク パフォーマンス モニター]**  タイルを選びます。

   ![[ネットワーク パフォーマンス モニター] タイル](media/network-performance-monitor/npm-config.png)

4. **[セットアップ]** ページに、Log Analytics エージェントのインストールのオプションが表示されます。監視用のエージェントの構成は **[共通設定]** ビューで行います。 前に説明したように、Log Analytics エージェントをインストールして構成した場合は、 **[セットアップ]** ビューを選んで使う機能を構成します。 

   **[パフォーマンス モニター]** : **[既定]** パフォーマンス モニター規則で代理トランザクションに使うプロトコルを選び、 **[保存して続行]** を選びます。 このプロトコルの選択は、システムによって生成される既定の規則に対してのみ保持されます。 パフォーマンス モニター規則を明示的に作成するたびにプロトコルを選ぶ必要があります。 **[パフォーマンス モニター]** タブ (第 0 日用構成の完了後に表示されます) の **[既定]** 規則の設定にはいつでも移動でき、プロトコルは後で変更できます。 パフォーマンス モニター機能が必要ない場合は、 **[パフォーマンス モニター]** タブの **[既定]** 規則の設定から既定の規則を無効にすることができます。

   ![[パフォーマンス モニター] ビュー](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **[サービス接続モニター]** : この機能では、事前構成済みの組み込みのテストを実行して、エージェントから Office 365 と Dynamics 365 へのネットワーク接続を監視することができます。 横にチェック ボックスをオンにして、監視する Office 365 と Dynamics 365 のサービスを選びます。 監視用のエージェントを選ぶには、 **[エージェントの追加]** を選びます。 この機能が必要ない場合、または後で設定する場合は、何も選ばないで、 **[保存して続行]** を選びます。

   ![サービス接続モニター ビュー](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute モニター**: **[今すぐ検出する]** を選び、この Log Analytics ワークスペースにリンクされている Azure サブスクリプション内の仮想ネットワークに接続されているすべての ExpressRoute プライベート ピアリングを検出します。 

   ![[ExpressRoute モニター] ビュー](media/network-performance-monitor/npm-express-route.png)

   検出が完了した後、検出された回線とピアリングが表に一覧表示されます。 

   ![[ネットワーク パフォーマンス モニターの構成] ページ](media/network-performance-monitor/npm-private-peerings.png)
    
これらの回線とピアリングの監視は、最初は無効状態になっています。 監視する各リソースを選び、右側の詳細ビューからそれらの監視を構成します。 **[保存]** を選んで構成を保存します。 詳しくは、ExpressRoute の監視の構成に関する記事をご覧ください。 

セットアップの完了後は、データの読み込みに 30 分から 1 時間かかります。 ソリューションによってネットワークからデータが集計されている間、Network Performance Monitor の **[概要]** タイルには "*このソリューションにはさらに構成が必要です*" と表示されます。 データが収集されてインデックスが設定されると、 **[概要]** タイルは変化し、ネットワークの正常性の概要が表示されます。 その後、Log Analytics エージェントがインストールされているノードと、環境から検出されたサブネットの監視を編集できます。

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>サブネットとノードの監視設定の編集 

構成ページの  **[サブネットワーク]**  タブに、少なくとも 1 つのエージェントがインストールされているすべてのサブネットが一覧表示されます。 


特定のサブネットワークの監視を有効または無効にするには:

1. **[サブネットワーク ID]** のチェック ボックスをオンまたはオフにします。 必要に応じて、 **[監視に使用します]** がオンまたはオフになっていることを確認します。 複数のサブネットをオンまたはオフにできます。 無効にすると、他のエージェントへの ping の実行を停止するようにエージェントが更新されるため、サブネットワークが監視されません。 
2. 特定のサブネットワークで監視するノードを選びます。 一覧からサブネットワークを選び、監視しないノードと監視するノードの一覧の間で必要に応じてノードを移動します。 カスタムの説明をサブネットワークに追加できます。
3. **[保存]** を選んで構成を保存します。 

#### <a name="choose-nodes-to-monitor"></a>監視するノードの選択

エージェントがインストールされているすべてのノードは、 **[ノード]** タブに一覧表示されます。 

1. 監視するノードを選択します。または、監視を停止するノードの選択を解除します。 
2. 必要に応じて、 **[監視に使用します]** ボックスをオンまたはオフにします。 
3. **[保存]** を選択します。 


目的の機能を構成します。

- [パフォーマンス モニター](network-performance-monitor-performance-monitor.md#configuration)
- [サービス接続モニター](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute モニター](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>データ収集の詳細
プロトコルとして TCP が選ばれていると、Network Performance Monitor は損失と待機時間に関する情報を収集するために、TCP の SYN-SYNACK-ACK ハンドシェイク パケットを使います。 プロトコルとして ICMP が選ばれていると、Network Performance Monitor は ICMP の ECHO ICMP ECHO REPLY を使います。 また、トポロジ情報の取得にはトレース ルートが使われます。

次の表は、ネットワーク パフォーマンス モニターにおけるデータの収集に関して、その手段と各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | System Center Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP ハンドシェイク/ICMP ECHO メッセージを 5 秒おきに収集し、3 分おきにデータを送信 |
 

 
このソリューションは、代理トランザクションを使用してネットワークの正常性を評価します。 ネットワーク上のいたるところにインストールされた Log Analytics エージェントどうしが、TCP パケットまたは ICMP エコーを交換します。 エージェントが TCP パケットまたは ICMP エコーのどちらを使うかは、監視用に選ばれているプロトコルによって決まります。 エージェントは、このプロセスを通じて、ラウンドトリップ時間とパケット損失 (発生した場合) を把握します。 各エージェントはさらに、他のエージェントに対して traceroute を定期的に実行し、テストが必要なネットワークのさまざまなルートをすべて検出します。 このデータを使用することで、エージェントはネットワーク待機時間とパケット損失の数値を推定できます。 テストは 5 秒ごとに繰り返されます。 データは、エージェントによって約 3 分間集計されてから、Azure Monitor の Log Analytics ワークスペースにアップロードされます。



>[!NOTE]
> エージェントは互いに頻繁に通信しますが、テストの実行中に大量のネットワーク トラフィックを生成することはありません。 損失と待機時間を特定するためにエージェントが使うのは TCP SYN-SYNACK-ACK ハンドシェイク パケットのみです。 データ パケットが交換されることはありません。 このプロセスの間、エージェントは必要なときにのみ相互に通信します。 エージェントの通信トポロジは、ネットワーク トラフィック削減に最適化されています。

## <a name="use-the-solution"></a>ソリューションの使用 

### <a name="network-performance-monitor-overview-tile"></a>Network Performance Monitor の [概要] タイル 

Network Performance Monitor ソリューションを有効にすると、 **[概要]** ページのソリューション タイルでネットワークの正常性の簡単な概要を確認できます。 

 ![Network Performance Monitor の [概要] タイル](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>ネットワーク パフォーマンス モニターのダッシュボード 

* **[上位ネットワーク正常性イベント]** : このページには、システムの最近の正常性イベントとアラートのほか、イベントがアクティブになってから経過した時間の一覧が表示されます。 正常性イベントまたはアラートは、監視規則に対して選択したメトリック (損失、待ち時間、応答時間、または帯域幅の使用率) の値がしきい値を超えたときに、常に生成されます。 

* **ExpressRoute モニター**: このページでは、ソリューションが監視しているさまざまな ExpressRoute ピアリング接続の正常性の概要が提供されます。 **[トポロジ]** タイルには、ネットワーク内の監視対象の ExpressRoute 回線上のネットワーク パスの数が表示されます。 このタイルを選ぶと、 **[トポロジ]** ビューに移動します。

* **[サービス接続モニター]** : このページでは、ユーザーが作成したさまざまなテストの正常性の概要が提供されます。 **[トポロジ]** タイルには、監視対象のエンドポイントの数が表示されます。 このタイルを選ぶと、 **[トポロジ]** ビューに移動します。

* **[パフォーマンス モニター]** : このページでは、ソリューションが監視している **[ネットワーク]** リンクと **[サブネットワーク]** リンクの正常性の概要が提供されます。 **[トポロジ]** タイルには、ネットワーク内の監視対象のネットワーク パスの数が表示されます。 このタイルを選ぶと、 **[トポロジ]** ビューに移動します。 

* **[共通クエリ]** : このページには、未加工のネットワーク監視データを直接フェッチする検索クエリのセットが表示されます。 これらのクエリは、カスタム レポート用の独自のクエリを作成するための出発点として使用できます。 

   ![ネットワーク パフォーマンス モニターのダッシュボード](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>詳細のドリルダウン 

ソリューション ダッシュボードのさまざまなリンクを選んで、興味のある点の詳細を確認することができます。 たとえば、ダッシュボードに表示されるアラートまたは異常なネットワーク リンクを確認する場合は、それを選んで詳しく調査します。 ページには、特定のネットワーク リンクのサブネットワーク リンクがすべて一覧表示されます。 各サブネットワーク リンクの損失、待機時間、正常性状態を確認できます。 問題の原因になっているサブネットワーク リンクをすばやく発見できます。 **[ノード リンクの表示]**  を選ぶと、異常なサブネット リンクのノード リンクがすべて表示されます。 その後、個別のノード間リンクを確認し、異常なノード リンクを見つけることができます。 

[ **トポロジの表示**] を選ぶと、ソース ノードと宛先ノードの間に存在するルートのホップバイホップ トポロジが表示されます。 正常ではないルートは赤で示されます。 各ホップに起因する待ち時間を確認できるため、ネットワークの特定の部分に問題をすばやく特定できます。

 

### <a name="network-state-recorder-control"></a>ネットワーク状態記録機能のコントロール

それぞれのビューには、特定の時点におけるネットワークの正常性のスナップショットが表示されます。 既定では、最新の状態が表示されます。 スナップショットがどの時点の状態を示したものであるかは、ページ上部のバーに表示されます。 過去のネットワーク正常性のスナップショットを表示するには、 **[Actions]\(アクション\)** を選びます。 最新の状態が表示されているときのページの自動更新を有効にしたり無効にしたりすることもできます。 

 ![ネットワーク状態の記録機能](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>傾向グラフ 

ドリルダウンの各レベルでは、アプリケーション メトリックの傾向を確認できます。 損失、待機時間、応答時間、帯域幅の使用率を確認できます。 傾向の時間間隔を変更するには、グラフの上部にある時間コントロールを使います。 

傾向グラフには、パフォーマンス メトリックのパフォーマンスの履歴が表示されます。 ネットワークの問題には、一過性の性質があり、ネットワークの現在の状態を確認するだけでは把握が困難なものもあります。 問題は、急に現れてだれかに気付かれる前に消え、時間が経ってからでないと再び現れない場合があります。 このような一過性の問題も、アプリケーション管理者にとっては難しい場合があります。 多くの場合、問題は、すべてのアプリケーション コンポーネントがスムーズに実行しているようであっても、アプリケーションの応答時間の異常な上昇として露見します。 

この種の問題は、傾向グラフを見ることで簡単に検出できます。 問題は、ネットワークの待機時間またはパケット損失の急上昇として表面化します。 問題を調査するには、ネットワーク状態の記録機能のコントロールを使って、問題発生時点のネットワークのスナップショットとトポロジを確認します。

 
![傾向グラフ](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>トポロジ マップ 

Network Performance Monitor では、送信元と宛先のエンドポイント間に存在するホップバイホップ トポロジが対話型のトポロジ マップで表示されます。 トポロジ マップを表示するには、ソリューション ダッシュボードで **[トポロジ]** タイルを選びます。 ドリルダウン ページで **[トポロジの表示]** リンクを選ぶこともできます。 

トポロジ マップでは、送信元と宛先の間に存在するルートの数と、データ パケットに使用されたパスが表示されます。 各ネットワーク ホップに起因する待ち時間も表示できます。 パスの合計待ち時間がしきい値 (対応する監視規則で設定) を上回るすべてのパスが、赤色で表示されます。 

ノードを選ぶか、トポロジ マップのノードにポインターを置くと、FQDN や IP アドレスなど、ノードのプロパティが表示されます。 ホップを選ぶと、その IP アドレスが表示されます。 厄介なネットワーク ホップは、それに起因する待ち時間に注意することで特定できます。 特定のルートをフィルターで抽出するには、折りたたみ式の操作ウィンドウにあるフィルターを使います。 ネットワーク トポロジを単純化するには、操作ウィンドウのスライダーを使って中間ホップを非表示にします。 トポロジ マップはマウス ホイールを使って拡大したり縮小したりできます。 

次のマップに示すトポロジはレイヤー 3 トポロジであり、レイヤー 2 のデバイスと接続は含まれていません。 

 
![トポロジ マップ](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Azure Monitor でのログ クエリ

すべてのデータは、Network Performance Monitor ダッシュボードで視覚的に表示されます。さらに、[ログ クエリ](../log-query/log-query-overview.md)では、ドリルダウン ページもネイティブで使用できます。 リポジトリのデータの対話型分析を実行したり、さまざまなソースからのデータを関連付けたりすることができます。 カスタム アラートを作成し、データを表示して、Excel、Power BI、または共有可能なリンクにデータをエクスポートすることもできます。 ダッシュボードの  **[共通クエリ]**  領域には、便利なクエリがいくつかあります。これらのクエリは、独自のクエリとレポートを作成するための出発点として利用できます。 

## <a name="alerts"></a>アラート

Network Performance Monitor は、[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) のアラート機能を使用します。

これは、すべての通知が[アクション グループ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)を使用して管理されることを意味します。  

Log Analytics を介してアラートを作成する NPM ユーザーの場合: 
1. Azure portal にリダイレクトするリンクが表示されます。 それをクリックしてポータルにアクセスします。
2. Network Performance Monitor ソリューションのタイルをクリックします。 
3. [構成] に移動します。  
4. アラートを作成するテストを選択し、後述する手順に従います。

Azure portal を介してアラートを作成する NPM ユーザーの場合:  
1. 電子メール アドレスを直接入力することも、アクション グループを使用してアラートを作成することもできます。
2. 電子メール アドレスを直接入力する場合、**NPM Email ActionGroup** という名前のアクション グループが作成され、そのアクション グループに電子メール ID が追加されます。
3. アクション グループを使用する場合は、以前に作成したアクション グループを選択する必要があります。 アクション グループを作成する方法については、[ここ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal)を参照してください。 
4. アラートが正常に作成されると、[アラートの管理] リンクを使用してアラートを管理できます。 

アラートを作成するたびに NPM によって Azure Monitor 上でクエリ ベースのログ アラート ルールが作成されます。 このクエリは、既定では 5 分ごとにトリガーされます。 Azure Monitor では、最初に作成された 250 件のログ アラート ルールには課金されず、250 件のログ アラート ルール制限を超えるアラート ルールは [Azure Monitor の価格ページのアラートの価格](https://azure.microsoft.com/pricing/details/monitor/)に従って課金されます。
通知は、[「Azure Monitor の価格」ページに記載されている通知の料金](https://azure.microsoft.com/pricing/details/monitor/)に従って個別に課金されます。


## <a name="pricing"></a>価格

価格についての情報は、[オンライン](network-performance-monitor-pricing-faq.md)で入手できます。

## <a name="provide-feedback"></a>フィードバックの提供 

* **UserVoice:** Network Performance Monitor の機能について皆さんのアイデアを投稿できます。 [UserVoice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring)をご覧ください。 

* **コーホートへの参加:** Microsoft が実施しているコーホートにぜひご参加ください。 その一環として、新機能にいち早く触れることができます。ネットワーク パフォーマンス モニターの機能向上にぜひご協力ください。 ご協力いただける場合は、こちらの [簡単なアンケート](https://aka.ms/npmcohort)への記入をお願いいたします。 

## <a name="next-steps"></a>次の手順 
[パフォーマンスの監視](network-performance-monitor-performance-monitor.md)、[サービス接続の監視](network-performance-monitor-performance-monitor.md)、および [ExpressRoute の監視](network-performance-monitor-expressroute.md)の詳細情報を確認します。 
