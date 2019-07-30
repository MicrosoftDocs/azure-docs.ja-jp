---
title: Azure トラフィック分析 | Microsoft Docs
description: トラフィック分析を使用して、Azure ネットワーク セキュリティ グループ フロー ログを分析する方法について説明します。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: kumud
ms.reviewer: yagup
ms.openlocfilehash: ca3174ad69185da88bf89c843f641dd2b20d9ac5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872488"
---
# <a name="traffic-analytics"></a>Traffic Analytics

Traffic Analytics は、クラウド ネットワークでのユーザーとアプリケーションのアクティビティを可視化するクラウドベースのソリューションです。 トラフィック分析では、Network Watcher のネットワーク セキュリティ グループ (NSG) フロー ログを分析して、Azure クラウドでのトラフィック フローに関する洞察を示します。 トラフィック分析を使用すると、次のことが可能になります。

- Azure サブスクリプション全体のネットワーク アクティビティを視覚化し、ホットスポットを特定する。
- 開いているポート、インターネット にアクセスしようとしているアプリケーション、承認されていないネットワークに接続している仮想マシン (VM) などの情報を使用して、ネットワークに対するセキュリティの脅威を特定し、ネットワークをセキュリティで保護する。
- Azure リージョン間やインターネット上でのトラフィック フロー パターンを把握して、ネットワークのデプロイをパフォーマンスと容量に最適化する。
- ネットワークでの接続の失敗の原因となるネットワークの構成の誤りを特定する。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>トラフィック分析が必要な理由

妥協のないセキュリティ、コンプライアンス、パフォーマンスを実現するには、独自のネットワークを監視、管理し、把握することが不可欠です。 独自の環境を保護および最適化するうえで最も重要なのは、環境を理解することです。 多くの場合、ネットワークの現在の状態、接続しているユーザーとその接続先、接続元、インターネットに対して開いているポート、想定されたネットワークの動作、不規則なネットワークの動作、トラフィックの急激な増加を把握する必要があります。

クラウド ネットワークは、ネットワーク インターフェイスを出入りする IP ネットワーク トラフィックを収集できる、Netflow や同等のプロトコル対応のルーターとスイッチを備えたオンプレミスの企業ネットワークとは異なります。 トラフィック フロー データを分析することで、ネットワークのトラフィック フローとトラフィック量の分析を構築できます。

Azure 仮想ネットワークには、個々のネットワーク インターフェイス、VM、またはサブネットに関連付けられたネットワーク セキュリティ グループを使用して、イングレス/エグレス IP トラフィックに関する情報を提供する NSG フロー ログがあります。 トラフィック分析では、生の NSG フロー ログを分析し、セキュリティ、トポロジ、地域に関するインテリジェンスを挿入することによって、環境内のトラフィック フローに関する洞察を示すことができます。 Traffic Analytics は、通信量の多いホスト、通信量の多いアプリケーション プロトコル、会話が多いホスト ペア、許可/ブロックされたトラフィック、受信/送信トラフィック、開いているインターネット ポート、ブロックの多いルール、Azure データ センター、仮想ネットワーク、サブネット、または承認されていないネットワークごとのトラフィック分布などの情報を提供します。

## <a name="key-components"></a>主なコンポーネント

- **ネットワーク セキュリティ グループ (NSG)** :Azure Virtual Network に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG はサブネットに関連付けることができるほか、クラシック モデルについては個々の VM に、Resource Manager モデルについては VM にアタッチされた個々のネットワーク インターフェイス (NIC) に関連付けることができます。 詳細については、[ネットワーク セキュリティ グループの概要](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関する記事をご覧ください。
- **ネットワーク セキュリティ グループ (NSG) フロー ログ**:ネットワーク セキュリティ グループを使用して、イングレス/エグレス IP トラフィックの情報を確認できます。 NSG フロー ログは JSON 形式で記述され、規則ごとの送信フローと受信フロー、フローが適用される NIC、フローに関する 5 組の情報 (送信元/宛先 IP アドレス、送信元/宛先ポート、プロトコル)、トラフィックが許可されているか拒否されているかが示されます。 NSG フロー ログの詳細については、[NSG フロー ログ](network-watcher-nsg-flow-logging-overview.md)に関する記事をご覧ください。
- **Log Analytics**:監視データを収集し、そのデータを中央リポジトリに格納する Azure サービス。 このデータには、Azure API によって提供されるイベント、パフォーマンス データ、またはカスタム データを含めることができます。 一度収集されたデータは、アラート、分析、エクスポートに使用できます。 Network Performance Monitor やトラフィック分析などの監視アプリケーションは、Azure Monitor ログを基盤として構築されています。 詳細については、[Azure Monitor ログ](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関するページを参照してください。
- **Log Analytics ワークスペース**:Azure アカウントに関するデータが格納される、Azure Monitor ログのインスタンス。 Log Analytics ワークスペースの詳細については、[Log Analytics ワークスペースの作成](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関するページを参照してください。
- **Network Watcher**:ネットワーク シナリオ レベルで Azure の状態を監視および診断できるリージョン サービス。 Network Watcher で NSG フロー ログを有効または無効にすることができます。 詳細については、「[Network Watcher](network-watcher-monitoring-overview.md)」をご覧ください。

## <a name="how-traffic-analytics-works"></a>トラフィック分析のしくみ

トラフィック分析では、生の NSG フロー ログを調べ、同じ送信元 IP アドレス、宛先 IP アドレス、宛先ポート、およびプロトコルでよく発生するフローを集約することによって削減されたログを取り込みます。 たとえば、ホスト 1 (IP アドレス: 10.10.10.10) とホスト 2 (IP アドレス: 10.10.20.10) が、ポート (例: 80) とプロトコル (例: http) を使用して 1 時間に 100 回通信したとします。 削減されたログには、100 個のエントリが含まれるのではなく、ホスト 1 とホスト 2 が、ポート *80* と *HTTP* プロトコルを使用して 1 時間に 100 回通信したことを示す 1 つのエントリが含まれます。 削減されたログは、地域、セキュリティ、トポロジの情報が追加された後に、Log Analytics ワークスペースに格納されます。 次の図は、このデータ フローを示しています。

![NSG フロー ログの処理のデータ フロー](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>サポートされているリージョン

NSG のトラフィック分析は、次のサポートされているどのリージョンでも使用できます。

* カナダ中部
* 米国中西部
* East US
* 米国東部 2
* 米国中北部
* 米国中南部
* 米国中部
* 米国西部
* 米国西部 2
* フランス中部
* 西ヨーロッパ
* 北ヨーロッパ
* ブラジル南部
* 英国西部
* 英国南部
* オーストラリア東部
* オーストラリア南東部
* 東アジア
* 東南アジア
* 韓国中部
* インド中部
* インド南部
* 東日本 
* 西日本
* 米国政府バージニア州

Log Analytics ワークスペースは、次のリージョンに存在する必要があります。
* カナダ中部
* 米国中西部
* East US
* 米国東部 2
* 米国中南部
* 米国西部
* 米国西部 2
* 米国中部
* フランス中部
* 北ヨーロッパ
* 西ヨーロッパ
* 英国南部
* オーストラリア東部
* オーストラリア南東部
* 東アジア
* 東南アジア
* 韓国中部
* インド中部
* 東日本
* 米国政府バージニア州

## <a name="prerequisites"></a>前提条件

### <a name="user-access-requirements"></a>ユーザー アクセスの要件

お使いのアカウントは、次のいずれかの Azure [組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)のメンバーである必要があります。

|デプロイメント モデル   | Role                   |
|---------          |---------               |
|リソース マネージャー   | Owner                  |
|                   | Contributor            |
|                   | Reader                 |
|                   | Network Contributor    |

上記のどの組み込みロールにもアカウントが割り当てられていない場合、サブスクリプション レベルで次のアクションが割り当てられる[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に割り当てる必要があります。

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

ユーザーのアクセス許可を確認する方法については、「[トラフィック分析についてよく寄せられる質問](traffic-analytics-faq.md)」を参照してください。

### <a name="enable-network-watcher"></a>Network Watcher を有効にする

トラフィックを分析するには、トラフィックの分析対象となる NSG がある各リージョンで、既存の Network Watcher を使用するか、[Network Watcher を有効にする](network-watcher-create.md)必要があります。 トラフィック分析は、[サポートされているリージョン](#supported-regions)のいずれかでホストされている NSG に対して有効にすることができます。

### <a name="select-a-network-security-group"></a>ネットワーク セキュリティ グループを選択する

NSG フロー ログを有効にする前に、フローをログに記録する、対象のネットワーク セキュリティ グループを選択する必要があります。 ネットワーク セキュリティ グループがない場合、作成方法については、[ネットワーク セキュリティ グループの作成](../virtual-network/manage-network-security-group.md#create-a-network-security-group)に関する記事をご覧ください。

Azure Portal の左側にある **[監視]** を選択し、 **[Network Watcher]** 、 **[NSG フロー ログ]** の順に選択します。 次の図に示すように、NSG フロー ログを有効にするネットワーク セキュリティ グループを選択します。

![NSG フロー ログを有効にする必要がある NSG の選択](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

[サポートされているリージョン](#supported-regions)以外のリージョンでホストされている NSG に対してトラフィック分析を有効にしようとすると、"見つかりません" というエラーが表示されます。

## <a name="enable-flow-log-settings"></a>フロー ログ設定の有効化

フロー ログ設定を有効にする前に、次の作業を完了する必要があります。

Azure Insights プロバイダーをサブスクリプションに登録します (まだ登録されていない場合)。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

NSG フロー ログの保存先となる Azure ストレージ アカウントがまだない場合は、ストレージ アカウントを作成する必要があります。 次のコマンドを使用して、ストレージ アカウントを作成できます。 コマンドを実行する前に、`<replace-with-your-unique-storage-account-name>` を Azure 全体で一意の名前 (3 ～ 24 文字で、数字と小文字のみを使用) に置き換えます。 必要に応じて、リソース グループ名を変更することもできます。

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

図に示すように、次のオプションを選択します。

1. *[状態]* で、 **[オン]** を選択します。
2. **[フロー ログ バージョン]** で、 *[バージョン 2]* を選択します。 バージョン 2 には、フローセッションの統計 (バイトおよびパケット) が含まれます。
3. フロー ログを保存する既存のストレージ アカウントを選択します。 データを無期限に保存する場合は、値を *0* に設定します。 ストレージ アカウントに対して Azure Storage の料金が発生します。
4. **[リテンション期間]** を、データを保存する日数に設定します。
5. *[Traffic Analytics Status]\(Traffic Analytics の状態\)* で、 **[オン]** を選択します。
6. 既存の Log Analytics (OMS) ワークスペースを選択するか、 **[新しいワークスペースの作成]** を選択して新規作成します。 Log Analytics ワークスペースは、分析の生成に使用される集計済みのインデックス付きデータを格納するために、Traffic Analytics で使用されます。 既存のワークスペースを選択する場合は、[サポートされているリージョン](#supported-regions)のいずれかに存在し、新しいクエリ言語にアップグレードされている必要があります。 既存のワークスペースをアップグレードするのが望ましくない場合や、サポートされているリージョンにワークスペースがない場合は、新しいワークスペースを作成します。 クエリ言語の詳細については、「[新しいログ検索への Azure Log Analytics のアップグレード](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)」をご覧ください。

    トラフィック分析ソリューションをホストするログ分析ワークスペースと NSG は、同じリージョンに存在する必要はありません。 たとえば、西ヨーロッパ リージョンのワークスペースにトラフィック分析があり、米国東部と米国西部に NSG があっても構いません。 同じワークスペースに複数の NSG を構成できます。
7. **[保存]** を選択します。

    ![ストレージ アカウント、Log Analytics ワークスペース、Traffic Analytics の有効化の選択](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement-nsg-flowlogs-v2.png)

トラフィック分析を有効にするその他の NSG に対して前の手順を繰り返します。 フロー ログのデータはワークスペースに送信されるので、ワークスペースが存在するリージョンでのデータの保存が、お住まいの国/地域の法律や規制で許可されていることを確認してください。

また、Azure PowerShell で [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell コマンドレットを使用して、トラフィック分析を構成できます。 `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを見つけます。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

## <a name="view-traffic-analytics"></a>トラフィック分析の表示

ポータルの左側にある **[すべてのサービス]** を選択し、 **[フィルター]** ボックスに「*監視*」と入力します。 検索結果に **[監視]** が表示されたら、それを選択します。 トラフィック分析とその機能の探索を開始するには、 **[Network Watcher]** 、 **[トラフィック分析 ]** の順に選択します。

![Traffic Analytics ダッシュボードへのアクセス](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Traffic Analytics では、レポートを生成するために、有意義な洞察を引き出すことができるだけの十分なデータを先に集計しておく必要があるため、ダッシュボードが最初に表示されるまでに最大 30 分かかることがあります。

## <a name="usage-scenarios"></a>使用シナリオ

Traffic Analytics が完全に構成された後に得られる洞察の一部を以下に示します。

### <a name="find-traffic-hotspots"></a>トラフィックのホットスポットを見つける

**調査項目**

- どのホスト、サブネット、および仮想ネットワークがほとんどのトラフィックを送受信し、最も悪意のあるトラフィックを走査し、重大なフローをブロックしているか。
    - ホスト、サブネット、および仮想ネットワークの比較グラフを確認してください。 送受信するトラフィックの量が多いホスト、サブネット、およびネットワークを把握すると、ほとんどのトラフィックを処理しているホストの特定と、トラフィック分布が適切に行われているかどうかの特定が可能です。
    - トラフィックの量がホストに適しているかどうかを評価できます。 トラフィックの量は通常の動作ですか。あるいは、さらに調査するだけの価値がありますか。
- どれくらいの量の受信/送信トラフィックがあるか。
    -   ホストでは、送信トラフィックよりも受信トラフィックの方が多いと予想されますか。それともその逆ですか。
- ブロックされたトラフィックの統計。
    - ホストが大量の開始トラフィックをブロックしているのはなぜですか。 この動作には、より詳細な調査を行い、構成を適切に最適化することが必要になります。
- 許可/ブロックされた悪意のあるトラフィックの統計
  - ホストは悪意のあるトラフィックをなぜ受信しているのですか、また、悪意のあるソースからのフローがなぜ許可されているのですか。 この動作には、より詳細な調査を行い、構成を適切に最適化することが必要になります。

    次の図に示すように、 **[ホスト]** にある **[See all]\(すべて表示\)** を選択します。

    ![トラフィックが多いホストの詳細を示すダッシュボード](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- 次の図は、トラフィックが多い上位 5 個のホストの時間的傾向と、ホスト のフロー関連の詳細 (許可された受信/送信フローと、拒否された受信/送信フロー) を示しています。

    ![トラフィックが多い上位 5 個のホストの傾向](media/traffic-analytics/top-five-most-talking-host-trend.png)

**調査項目**

- 会話が多いホスト ペアはどれか。
    - 想定された動作 (フロントエンドまたはバックエンド間の通信など) または不規則な動作 (バックエンドのインターネット　トラフィックなど)。
- 許可/ブロックされたトラフィックの統計。
    - ホストが大量のトラフィックを許可またはブロックしている理由。
- 会話が多いホスト ペアの間で最も頻繁に使用されるアプリケーション プロトコル:
    - これらのアプリケーションは、このネットワークで許可されていますか。
    - アプリケーションは正しく構成されていますか。 それらは適切なプロトコルを通信に使用していますか。 次の図に示すように、 **[頻度が最大の会話]** の **[See all]\(すべて表示\)** を選択します。

        ![頻度が最大の会話を示すダッシュボード](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- 次の図は、上位 5 個の会話の時間的傾向と、フロー関連の詳細 (会話ペアの許可および拒否された受信/送信フローなど) を示しています。

    ![上位 5 個の頻繁な会話の詳細と傾向](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**調査項目**

- 環境で最も使用されているのはどのアプリケーション プロトコルか。また、そのアプリケーション プロトコルを最も使用しているのは、会話しているどのホスト ペアか。
    - これらのアプリケーションは、このネットワークで許可されていますか。
    - アプリケーションは正しく構成されていますか。 それらは適切なプロトコルを通信に使用していますか。 想定された動作は、80 や 443 などの一般的なポートです。 標準の通信で通常とは異なるポートが表示された場合、構成を変更する必要があります。 次の図に示すように、 **[アプリケーション　ポート]** の **[See all]\(すべて表示\)** を選択します。

        ![上位のアプリケーション プロトコルを示すダッシュ ボード](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- 次の図は、上位 5 個の L7 プロトコルの時間的傾向と、L7 プロトコルのフロー関連の詳細 (許可および拒否されたフローなど) を示しています。

    ![上位 5 個のレイヤー 7 プロトコルの詳細と傾向](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![ログ検索におけるアプリケーション プロトコルのフローの詳細](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**調査項目**

- 環境内の VPN ゲートウェイの容量使用率の傾向。
    - 各 VPN SKU では一定量の帯域幅を許可します。 VPN ゲートウェイは十分に活用されていますか。
    - ゲートウェイは容量に達しつつありますか。 1 つ上位の SKU にアップグレードする必要はありますか。
- 会話が多いホストはどれか。それらのホストが使用しているポートと VPN ゲートウェイはどれか。
    - これは通常のパターンですか。 次の図に示すように、 **[VPN ゲートウェイ]** にある **[See all]\(すべて表示\)** を選択します。

        ![上位のアクティブ VPN 接続を示すダッシュボード](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- 次の図は、Azure VPN Gateway の容量使用率の時間的傾向と、フロー関連の詳細 (許可されたフローやポートなど) を示しています。

    ![VPN ゲートウェイの使用率の傾向とフローの詳細](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>地域ごとのトラフィック分布を視覚化する

**調査項目**

- データ センターごとのトラフィック分布 (データ センターへのトラフィックの上位の送信元、データ センターと会話している上位の承認されていないネットワーク、上位の会話しているアプリケーション プロトコルなど)。
  - データ センターの負荷の増加が見られる場合は、効率的なトラフィック分布を計画できます。
  - 承認されていないネットワークがデータ センターで会話している場合は、NSG ルールを修正してそれらをブロックします。

    次の図に示すように、 **[Your environment]\(使用している環境\)** の **[マップの表示]** を選択します。

    ![トラフィック分布を示すダッシュボード](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- geo マップの上部には、データ センター (デプロイ済み/デプロイなし/アクティブ/非アクティブ/Traffic Analytics 有効/Traffic Analytics 無効) や、アクティブなデプロイへの問題のないトラフィックまたは悪意のあるトラフィックに関与している国/リージョンなどのパラメーターを選択できるリボンが表示されます。

    ![アクティブなデプロイを示す geo マップ ビュー](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- geo マップには、通信している国/リージョンや大陸からデータ センターへのトラフィック分布が、青色 (問題のないトラフィック) と赤色 (悪意のあるトラフィック) の線で表示されます。

    ![国/リージョンや大陸へのトラフィック分布を示す geo マップ ビュー](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![ログ検索におけるトラフィック分布のフローの詳細](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>仮想ネットワークごとのトラフィック分布を視覚化する

**調査項目**

- 仮想ネットワークごとのトラフィック分布、トポロジ、仮想ネットワークへのトラフィックの上位の送信元、仮想ネットワークと会話している上位の承認されていないネットワーク、上位の会話しているアプリケーション プロトコル。
  - どの仮想ネットワークがどの仮想ネットワークと会話しているかを把握します。 会話が想定外の場合は修正できます。
  - 承認されていないネットワークが仮想ネットワークと会話している場合は、NSG ルールを修正して承認されていないネットワークをブロックできます。
 
    次の図に示すように、 **[Your environment]\(使用している環境\)** の **[View VNets]\(VNets の表示\)** を選択します。

    ![仮想ネットワークの分布を示すダッシュボード](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- 仮想ネットワーク トポロジの上部には、仮想ネットワーク (仮想ネットワーク間接続/アクティブ/非アクティブ)、仮想ネットワークの外部接続、アクティブなフロー、悪意のあるフローなどのパラメーターを選択できるリボンが表示されます。
- サブスクリプション、ワークスペース、リソース グループ、および時間間隔に基づいて、仮想ネットワーク トポロジをフィルター処理することができます。 フローの理解に役立つ追加フィルターは次のとおりです。フローの種類 (InterVNet、IntraVNET など)、フローの方向 (受信、送信)、フローの状態 (許可、ブロック)、VNet (対象および接続)、接続の種類 (ピアリングまたはゲートウェイ - P2S および S2S)、NSG。 これらのフィルターを使用して、詳細に調べる VNet に照準を絞ります。
- 仮想ネットワーク トポロジには、フロー (許可された/ブロックされた/受信/送信/問題のない/悪意のあるフロー)、アプリケーション プロトコル、ネットワーク セキュリティ グループなどについて、仮想ネットワークへのトラフィック分布が表示されます。

    ![トラフィック分布とフローの詳細を示す仮想ネットワーク トポロジ](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![最上位ほかのフィルターを例示した仮想ネットワーク トポロジ](./media/traffic-analytics/virtual-network-filters.png)

    ![ログ検索における仮想ネットワークのトラフィック分布のフローの詳細](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**調査項目**

- サブネットごとのトラフィック分布、トポロジ、サブネットへのトラフィックの上位の送信元、サブネットと会話している上位の非承認ネットワーク、上位の会話しているアプリケーション プロトコル。
    - どのサブネットがどのサブネットと会話しているかを把握します。 想定外の会話がある場合は、構成を修正できます。
    - 非承認ネットワークがサブネットと会話している場合は、非承認ネットワークをブロックするように NSG ルールを構成することで修正できます。
- サブネット トポロジの上部には、アクティブ/非アクティブなサブネット、サブネットの外部接続、アクティブなフロー、悪意のあるフローなどのパラメーターを選択できるリボンが表示されます。
- サブネット トポロジには、フロー (許可された/ブロックされた/受信/送信/問題のない/悪意のあるフロー)、アプリケーション プロトコル、NSG などについて、仮想ネットワークへのトラフィック分布が表示されます。

    ![フローについて、仮想ネットワーク サブネットのトラフィック分布を示すサブネット トポロジ](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**調査項目**

Application Gateway および Load Balancer ごとのトラフィック分布、トポロジ、トラフィックの上位の送信元、Application Gateway および Load Balancer と会話している上位の非承認ネットワーク、および上位の会話しているアプリケーション プロトコル。 
    
 - どのサブネットがどの Application Gateway または Load Balancer と会話しているかを把握します。 想定外の会話を見つけた場合は、構成を修正できます。
 - 非承認ネットワークが、Application Gateway または Load Balancer と会話している場合、非承認ネットワークをブロックするように NSG ルールを構成することで修正できます。 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>インターネットからのトラフィックを受信するポートと仮想マシンを表示する

**調査項目**

- インターネット経由で会話しているのは開いているどのポートか。
  - 想定外のポートが開いていることがわかった場合は、構成を修正できます。

    ![インターネットとの間でトラフィックを送受信するポートを示すダッシュボード](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Azure の宛先ポートとホストの詳細](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**調査項目**

環境に悪意のあるトラフィックはあるか。 そのトラフィックの送信元はどこか。 宛先はどこか。

![ログ検索における悪意のあるトラフィック フローの詳細](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>NSG/NSG ルール ヒット数の傾向を視覚化する

**調査項目**

- フロー分布の比較グラフでは、どの NSG/NSG ルールが最も多くヒットしているか。
- NSG/NSG ルールごとの上位の送信元と宛先の会話ペアはどれか。

    ![NSG ヒット数の統計を示すダッシュボード](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- 次の図は、NSG ルールのヒット数の時間的傾向と、ネットワーク セキュリティ グループの送信元と宛先間のフローの詳細を示しています。

  - どの NSG および NSG ルールで悪意のあるフローを走査していて、お使いのクラウド環境に最もよくアクセスしている IP アドレスはどれかを迅速に検出する
  - どの NSG/NSG ルールが重大なネットワーク トラフィックを許可/ブロックしているかを特定する
  - 上位のフィルターを選択して、NSG または NSG ルールをより詳細に検査する

    ![NSG ルールのヒット数の時間的傾向と上位 NSG ルールの表示](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![ログ検索における上位 NSG ルールの統計の詳細](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

よく寄せられる質問とその回答については、[トラフィック分析に関する FAQ](traffic-analytics-faq.md) のページをご覧ください。

## <a name="next-steps"></a>次の手順

- フロー ログを有効にする方法については、[フロー ログ記録の有効化](network-watcher-nsg-flow-logging-portal.md)に関するページをご覧ください。
- Traffic Analytics のスキーマと処理の詳細について学ぶには、「[Traffic Analytics のスキーマ](traffic-analytics-schema.md)」を参照してください。
