---
title: Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する
description: Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 65af5810152034fd7b6014041edd07835eebd194
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101479"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する

[Azure Private Link](../../private-link/private-link-overview.md) を使用すると、プライベート エンドポイントを使用して Azure PaaS サービスを仮想ネットワークに安全に接続できます。 多くのサービスでは、リソースごとにエンドポイントを設定するだけです。 ただし、Azure Monitor は、相互に連携してワークロードを監視する、相互接続されたさまざまなサービスの集まりです。 その結果、Azure Monitor Private Link スコープ (AMPLS) と呼ばれるリソースが作成されました。 AMPLS を使用すると、監視ネットワークの境界を定義し、仮想ネットワークに接続することができます。 この記事では、Azure Monitor Private Link スコープを使用するタイミングと、その設定方法について説明します。

## <a name="advantages"></a>長所

Private Link を使用すると、次のことができます。

- パブリック ネットワーク アクセスを開かずに Azure Monitor にプライベートに接続する
- 承認されたプライベート ネットワーク経由でのみ監視データにアクセスできるようにする
- プライベート エンドポイント経由で接続する特定の Azure Monitor リソースを定義して、プライベート ネットワーク経由のデータ流出を防ぐ
- ExpressRoute と Private Link を使用して、オンプレミスのプライベート ネットワークを Azure Monitor に安全に接続する
- すべてのトラフィックを Microsoft Azure のバックボーン ネットワーク内に収める

詳細については、[Private Link の主な利点](../../private-link/private-link-overview.md#key-benefits)に関するページを参照してください。

## <a name="how-it-works"></a>しくみ

Azure Monitor Private Link スコープ (AMPLS) によって、プライベート エンドポイント (およびそれらが含まれている VNet) が 1 つ以上の Azure Monitor リソース (Log Analytics ワークスペースと Application Insights コンポーネント) に接続されます。

![基本的なリソース トポロジの図](./media/private-link-security/private-link-basic-topology.png)

* VNet のプライベート エンドポイントを使用すると、これらのエンドポイントのパブリック IP を使用するのではなく、ネットワークのプールのプライベート IP を介して Azure Monitor エンドポイントに接続することができます。 これにより、不要な送信トラフィックへの VNet を開かなくても、Azure Monitor リソースを使用し続けることができます。 
* プライベート エンドポイントから Azure Monitor リソースへのトラフィックは、パブリック ネットワークにルーティングされるのではなく、Microsoft Azure のバックボーンを経由します。 
* パブリック ネットワークからの取り込みとクエリを許可または拒否するように、各ワークスペースまたはコンポーネントを構成できます。 これによりリソース レベルの保護が提供されるため、特定のリソースへのトラフィックを制御できます。

> [!NOTE]
> 1 つの Azure Monitor リソースは複数の AMPLS に属することはできますが、1 つの VNet を複数の AMPLS に接続することはできません。 

## <a name="planning-your-private-link-setup"></a>Private Link のセットアップを計画する

Azure Monitor Private Link のセットアップを設定する前に、ネットワーク トポロジ、特に DNS ルーティング トポロジを検討してください。 

### <a name="the-issue-of-dns-overrides"></a>DNS オーバーライドの問題
一部の Azure Monitor サービスでは、グローバル エンドポイントが使用されます。つまり、ワークスペースまたはコンポーネントを対象とする要求が処理されます。 いくつかの例として、Application Insights のインジェスト エンドポイント、および Application Insights と Log Analytics の両方のクエリ エンドポイントがあります。

Private Link 接続を設定すると、DNS が更新され、VNet の IP 範囲からプライベート IP アドレスに Azure Monitor エンドポイントがマッピングされます。 この変更により、これらのエンドポイントの以前のマッピングがオーバーライドされます。これは、次のような重要な影響を与える可能性があります。 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link はすべての Azure Monitor リソースに適用されます (全か無か)
一部の Azure Monitor エンドポイントはグローバルであるため、特定のコンポーネントまたはワークスペースの Private Link 接続を作成することはできません。 代わりに、1 つの Application Insights コンポーネントへの Private Link を設定すると、**すべての** Application Insights コンポーネントに対して DNS レコードが更新されます。 コンポーネントの取り込みまたはクエリを実行しようとすると、Private Link 経由で実行され、失敗する可能性があります。 同様に、1 つのワークスペースへの Private Link を設定すると、すべての Log Analytics クエリが Private Link クエリ エンドポイントを経由します (ただし、ワークスペース固有のエンドポイントを持つインジェスト要求を除きます)。

![1 つの VNet での DNS オーバーライドの図](./media/private-link-security/dns-overrides-single-vnet.png)

これは、特定の VNet についてだけではなく、同じ DNS サーバーを共有するすべての Vnet に当てはまります (「[DNS オーバーライドの問題](#the-issue-of-dns-overrides)」を参照してください)。 そのため、たとえば Application Insights コンポーネントにログを取り込む要求は、常に Private Link ルートを経由して送信されます。 AMPLS にリンクされていないコンポーネントは、Private Link の検証に失敗し、通過しません。

> [!NOTE]
> 結論:1 つのリソースへの Private Link 接続を設定すると、ネットワーク内のすべての Azure Monitor リソースに適用されます。すべてに適用されるか、どれにも適用されないかです。 実質的には、ネットワーク内のすべての Azure Monitor リソースを AMPLS に追加するか、そのいずれも追加しないようにする必要があります。

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link はネットワーク全体に適用されます
ネットワークによっては、複数の VNet で構成されるものもあります。 VNet で同じ DNS サーバーが使用されている場合は、互いの DNS マッピングがオーバーライドされ、場合によっては Azure Monitor との相互の通信が切断される可能性があります (「[DNS オーバーライドの問題](#the-issue-of-dns-overrides)」を参照してください)。 最終的には、最後の VNet のみが Azure Monitor と通信できるようになります。これは、DNS で Azure Monitor エンドポイントがこの VNet の範囲からプライベート IP にマッピングされるためです (他の VNet からはアクセスできない可能性があります)。

![複数の VNet での DNS オーバーライドの図](./media/private-link-security/dns-overrides-multiple-vnets.png)

上の図では、VNet 10.0.1.x が最初に AMPLS1 に接続され、Azure Monitor のグローバル エンドポイントがその範囲の IP にマッピングされます。 その後、VNet 10.0.2.x が AMPLS2 に接続され、"*同じグローバル エンドポイント*" の DNS マッピングがその範囲の IP でオーバーライドされます。 これらの VNet はピアリングされていないため、最初の VNet はこれらのエンドポイントに接続できません。

> [!NOTE]
> 結論:AMPLS のセットアップは、同じ DNS ゾーンを共有するすべてのネットワークに影響します。 互いの DNS エンドポイント マッピングがオーバーライドされないようにするには、ピアリングされたネットワーク (ハブ VNet など) に単一のプライベート エンドポイントをセットアップするか、DNS レベルでネットワークを分離することをお勧めします (たとえば、DNS フォワーダーを使用するか、DNS サーバーを完全に分離します)。

### <a name="hub-spoke-networks"></a>ハブスポーク ネットワーク
ハブスポーク トポロジでは、各 VNet の Private Link を個別に設定するのではなく、ハブ (メイン) VNet に Private Link を設定することによって、DNS オーバーライドの問題を回避できます。 このセットアップは特に、スポーク VNet で使用される Azure Monitor リソースが共有されている場合に合理的です。 

![ハブアンドスポークのシングル PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> たとえば、各 VNet が限られた一連の監視リソースにアクセスできるようにするために、スポーク VNet 用に個別の Private Link を作成することをお勧めします。 このような場合は、VNet ごとに専用のプライベート エンドポイントと AMPLS を作成できますが、DNS がオーバーライドされないように、同じ DNS ゾーンが共有されていないことも確認する必要があります。


### <a name="consider-limits"></a>制限を考慮に入れる

「[制限事項と制約事項](#restrictions-and-limitations)」に記載されているように、AMPLS オブジェクトには、次のトポロジに示されているいくつかの制限があります。
* 各 VNet は **1** つの AMPLS オブジェクトにのみ接続します。
* AMPLS B は、使用可能なプライベート エンドポイント接続 10 個のうち 2 個を使用して、2 つの VNet (VNet2 と VNet3) のプライベート エンドポイントに接続します。
* AMPLS A は、使用可能な Azure Monitor リソース接続 50 個のうち 3 個を使用して、2 つのワークスペースと 1 つの Application Insights コンポーネントに接続します。
* ワークスペース 2 は、使用可能な AMPLS 接続 5 個のうち 2 個を使用して、AMPLS A と AMPLS B に接続します。

![AMPLS の制限に関する図](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>接続の例

まず、Azure Monitor Private Link Scope リソースを作成します。

1. Azure portal で **[リソースの作成]** に移動して、「**Azure Monitor Private Link Scope**」を検索します。

   ![Azure Monitor Private Link スコープを検索する](./media/private-link-security/ampls-find-1c.png)

2. **[作成]** を選択します。
3. サブスクリプションとリソース グループを選択します。
4. AMPLS に名前を付けます。 "AppServerProdTelem" など、意味のある明確な名前を使用することをお勧めします。
5. **[確認および作成]** を選択します。 

   ![Azure Monitor Private Link スコープを作成する](./media/private-link-security/ampls-create-1d.png)

6. 検証をパスしたら、 **[作成]** を選択します。

### <a name="connect-azure-monitor-resources"></a>Azure Monitor リソースの接続

Azure Monitor リソース (Log Analytics ワークスペースと Application Insights コンポーネント) を AMPLS に接続します。

1. Azure Monitor Private Link スコープで、左側のメニューの **[Azure Monitor リソース]** を選択します。 **[追加]** ボタンを選びます。
2. ワークスペースまたはコンポーネントを追加します。 **[追加]** ボタンを選択するとダイアログが表示され、Azure Monitor リソースを選択できます。 サブスクリプションとリソース グループを参照するか、名前を入力してフィルターを適用できます。 ワークスペースまたはコンポーネントを選択し、 **[適用]** を選択してスコープに追加します。

    ![スコープの選択 UX のスクリーンショット](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Monitor リソースを削除するには、まず接続先の AMPLS オブジェクトから切断する必要があります。 AMPLS に接続されているリソースを削除することはできません。

### <a name="connect-to-a-private-endpoint"></a>プライベート エンドポイントへの接続

これで、AMPLS にリソースを接続できたので、ネットワークを接続するためのプライベート エンドポイントを作成します。 このタスクは、この例のように、[Azure portal Private Link センター](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)、または Azure Monitor Private Link Scope 内で実行できます。

1. スコープ リソースで、左側のリソース メニューの **[プライベート エンドポイント接続]** を選択します。 **[プライベート エンドポイント]** を選択して、エンドポイント作成プロセスを開始します。 Private Link センターで開始された接続をここで承認するには、そのリンクを選択し、 **[承認]** を選択します。

    ![プライベート エンドポイント接続 UX のスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. サブスクリプション、リソース グループ、およびエンドポイントの名前と、それを有効にするリージョンを選択します。 リージョンは、接続する VNet のリージョンと同じにする必要があります。

3. **[Next:リソース]** を選択します。 

4. リソース画面で、

   a. Azure Monitor プライベート スコープ リソースを含む **[サブスクリプション]** を選択します。 

   b. **[リソースの種類]** には、**Microsoft.insights/privateLinkScopes** を選択してください。 

   c. **[リソース]** ドロップダウンから、前に作成した Private Link スコープを選択します。 

   d. **Next:構成 >** をクリックします。
      ![[プライベート エンドポイントを作成する] の選択のスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. [構成] ウィンドウで、

   a.    Azure Monitor リソースに接続する **[仮想ネットワーク]** と **[サブネット]** を選択します。 
 
   b.    **[プライベート DNS ゾーンとの統合]** で **[はい]** を選択して、新しいプライベート DNS ゾーンを自動で作成します。 実際の DNS ゾーンは、下のスクリーンショットに示されているものとは異なる場合があります。 
   > [!NOTE]
   > **[いいえ]** を選択して DNS レコードを手動で管理する場合は、最初に、このプライベート エンドポイントと AMPLS 構成を含め、プライベート リンクの設定を完了させます。 次に、「[Azure プライベート エンドポイントの DNS 構成](../../private-link/private-endpoint-dns.md)」の手順に従って、DNS を構成します。 プライベート リンクの設定の準備で、空のレコードを作成しないようにしてください。 作成する DNS レコードによって、既存の設定がオーバーライドされ、Azure Monitor との接続が影響を受ける可能性があります。
 
   c.    **[Review + create]\(レビュー + 作成\)** を選択します。
 
   d.    検証をパスします。 
 
   e.    **［作成］** を選択します 

    ![プライベート エンドポイントの詳細の選択に関するスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

これで、この AMPLS に接続された新しいプライベート エンドポイントが作成されました。

## <a name="review-and-validate-your-private-link-setup"></a>自分の Private Link セットアップを確認および検証する

### <a name="reviewing-your-endpoints-dns-settings"></a>エンドポイントの DNS 設定の確認
作成したプライベート エンドポイントには今、次の 4 つの DNS ゾーンが構成されているはずです。

[![プライベート エンドポイント DNS ゾーンのスクリーンショット。](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net

> [!NOTE]
> これらのゾーンのそれぞれで、特定の Azure Monitor エンドポイントが VNet の IP プールからのプライベート IP にマップされます。 以下の画像に示されている IP アドレスは単なる例です。 実際の構成では、代わりに、独自のネットワークからのプライベート IP が表示されるはずです。

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
このゾーンでカバーされるのは、Azure Monitor によって使用されるグローバル エンドポイントです。つまり、これらのエンドポイントによって、すべてのリソース (特定のものではなく) を考慮して要求が処理されます。 このゾーンには、次のようにマップされたエンドポイントが必要です。
* `in.ai` - Application Insights インジェスト エンドポイント。グローバルおよびリージョンのエントリが表示されます。
* `api` - Application Insights および Log Analytics API エンドポイント
* `live` - Application Insights ライブ メトリック エンドポイント
* `profiler` - Application Insights プロファイラー エンドポイント
* `snapshot` - Application Insights スナップショット エンドポイント[![プライベート DNS ゾーン monitor-azure-com のスクリーンショット。](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
このゾーンでカバーされるのは、OMS エンドポイントへのワークスペース固有のマッピングです。 このプライベート エンドポイントに接続されている AMPLS にリンクされた各ワークスペースのエントリが表示されます。
[![プライベート DNS ゾーン oms-opinsights-azure-com のスクリーンショット。](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
このゾーンでカバーされるのは、ODS エンドポイント (Log Analytics のインジェスト エンドポイント) へのワークスペース固有のマッピングです。 このプライベート エンドポイントに接続されている AMPLS にリンクされた各ワークスペースのエントリが表示されます。
[![プライベート DNS ゾーン ods-opinsights-azure-com のスクリーンショット。](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
このゾーンでカバーされるのは、エージェント サービス オートメーション エンドポイントへのワークスペース固有のマッピングです。 このプライベート エンドポイントに接続されている AMPLS にリンクされた各ワークスペースのエントリが表示されます。
[![プライベート DNS ゾーン エージェント svc-azure-automation-net。](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>プライベート リンクを介して通信が行われていることの検証
* 自分の要求がプライベート エンドポイントを介して、そしてプライベート IP にマップされたエンドポイントに送信されるようになっていることを検証するには、ツール (またはご利用のブラウザー) に対するネットワーク追跡を使用してそれらを確認できます。 たとえば、ご利用のワークスペースまたはアプリケーションに対してクエリを試みる場合は、API エンドポイントにマップされたプライベート IP に要求が送信されることを確認します。この例では、*172.17.0.9* です。

    注: ブラウザーによっては、他の DNS 設定が使用されます (「[ブラウザーの DNS 設定](#browser-dns-settings)」を参照してください)。 目的の DNS 設定が適用されていることを確認します。

* (AMPLS 経由で接続されていない) パブリック ネットワークからの要求がご利用のワークスペースまたはコンポーネントによって受信されていないことを確認するには、「[プライベート リンク スコープ外からのアクセスを管理する](#manage-access-from-outside-of-private-links-scopes)」で説明されているように、リソースのパブリック インジェストとクエリ フラグを *[いいえ]* に設定します。

* ご利用の保護されたネットワーク上のクライアントから、`nslookup` を、ご利用の DNS ゾーンに一覧表示されている任意のエンドポイントに対して使用します。 それは、既定で使用されるパブリック IP ではなく、マップされたプライベート IP に、DNS サーバーによって解決される必要があります。


## <a name="configure-log-analytics"></a>Log Analytics の構成

Azure Portal にアクセスします。 Log Analytics ワークスペース リソースのメニューの左側に、 **[ネットワークの分離]** という項目があります。 このメニューから、2 つの異なる状態を制御できます。

![LA ネットワークの分離](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>接続されている Azure Monitor プライベート リンク スコープ
ワークスペースに接続されているすべてのスコープが、この画面に表示されます。 スコープ (AMPLS) に接続すると、各 AMPLS に接続されている仮想ネットワークからのネットワーク トラフィックがこのワークスペースに到達できます。 ここから接続を確立すると、[Azure Monitor リソースの接続](#connect-azure-monitor-resources)の場合のように、スコープから接続するのと同じ効果があります。 新しい接続を追加するには、 **[追加]** を選択し、Azure Monitor Private Link スコープを選択します。 **[適用]** を選択して接続します。 「[制限事項と制約事項](#restrictions-and-limitations)」で説明しているように、ワークスペースは 5 つの AMPLS オブジェクトに接続できます。 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>プライベート リンク スコープ外からのアクセスを管理する
このページの下部にある設定では、パブリック ネットワークからのアクセスを制御します。つまり、上に示したスコープではネットワークに接続されません。 **[Allow public network access for ingestion]\(取り込みにパブリック ネットワークを許可する\)** を **[いいえ]** に設定すると、接続されているスコープ外のマシンからのログ取り込みがブロックされます。 **[Allow public network access for queries]\(クエリにパブリック ネットワークを許可する\)** を **[いいえ]** に設定すると、スコープ外のマシンからのクエリがブロックされます。 これには、ブック、ダッシュボード、API ベースのクライアント エクスペリエンス、Azure portal の分析情報などで実行されるクエリが含まれます。 Azure portal の外部で実行され、Log Analytics データにクエリを発行するエクスペリエンスも、プライベート リンク VNET 内で実行する必要があります。

### <a name="exceptions"></a>例外
これまで説明したアクセスの制限は Azure Resource Manager には適用されないため、次のような制限事項があります。
* データへのアクセス - パブリック ネットワークからのクエリのブロック/許可は、ほとんどの Log Analytics エクスペリエンスに適用されますが、一部のエクスペリエンスでは、Azure Resource Manager を介してデータのクエリが実行されるため、プライベート リンクの設定が Resource Manager にも適用される (機能は近日公開予定) 場合を除き、データのクエリを実行することはできません。 たとえば、Azure Monitor ソリューション、ブックと分析情報、LogicApp コネクタなどです。
* ワークスペースの管理 - ワークスペースの設定と構成の変更 (これらのアクセス設定をオンまたはオフにするなど) は、Azure Resource Manager によって管理されます。 適切なロール、アクセス許可、ネットワーク制御、および監査を使用して、ワークスペースの管理へのアクセスを制限します。 詳細については、[Azure Monitor のロール、アクセス許可、およびセキュリティ](../roles-permissions-security.md)に関するページを参照してください。

> [!NOTE]
> [[診断設定]](../essentials/diagnostic-settings.md) を使用してワークスペースにアップロードされたログとメトリックは、セキュリティで保護されたプライベート Microsoft チャネルを経由し、これらの設定によって制御されることはありません。

### <a name="log-analytics-solution-packs-download"></a>Log Analytics ソリューション パックのダウンロード

Log Analytics エージェントでソリューション パックをダウンロードできるようにするには、適切な完全修飾ドメイン名をファイアウォールの許可リストに追加します。 


| クラウド環境 | エージェントのリソース | Port | Direction |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | 送信
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  送信
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 送信

## <a name="configure-application-insights"></a>Application Insights の構成

Azure Portal にアクセスします。 Azure Monitor Application Insights コンポーネント リソースの左側に、 **[ネットワークの分離]** というメニュー項目があります。 このメニューから、2 つの異なる状態を制御できます。

![AI ネットワークの分離](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

まず、この Application Insights リソースを、アクセス権を持つ任意の Azure Monitor Private Link Scope に接続できます。 **[追加]** を選択し、 **[Azure Monitor Private Link Scope]** を選択します。 [適用] を選択して接続します。 接続されているすべてのスコープがこの画面に表示されます。 この接続を確立すると、接続された仮想ネットワーク内のネットワーク トラフィックがこのコンポーネントに接続できるようになります。また、[Azure Monitor リソースを接続](#connect-azure-monitor-resources)した場合と同じように、スコープからの接続と同じ効果があります。 

次に、このリソースに対して、前述の Pivate Link スコープ (AMPLS) の外部からアクセスする方法を制御できます。 **[Allow public network access for ingestion]\(取り込みにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、接続されているスコープ外のマシンまたは SDK は、このコンポーネントにデータをアップロードできません。 **[Allow public network access for queries]\(クエリにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、スコープ外のマシンは、この Application Insights リソースのデータにアクセスできません。 このデータには、APM ログ、メトリック、ライブ メトリック ストリームへのアクセスと、ブック、ダッシュボード、クエリ API ベースのクライアント エクスペリエンス、Azure portal の分析情報など、その上に構築されたエクスペリエンスが含まれます。 

> [!NOTE]
> ポータル以外の消費エクスペリエンスも、監視対象のワークロードを含むプライベート リンク VNET 内で実行する必要があります。

監視対象のワークロードをホストしているリソースをプライベート リンクに追加する必要があります。 例については、「[Azure Web アプリでプライベート エンドポイントを使用する](../../app-service/networking/private-endpoint.md)」を参照してください。

この方法でのアクセス制限は、Application Insights リソース内のデータにのみ適用されます。 ただし、これらのアクセス設定をオンまたはオフにするなどの構成の変更は、Azure Resource Manager で管理されます。 そのため、適切なロール、アクセス許可、ネットワーク制御、および監査を使用して、リソース マネージャーへのアクセスを制限する必要があります。 詳細については、[Azure Monitor のロール、アクセス許可、およびセキュリティ](../roles-permissions-security.md)に関するページを参照してください。

> [!NOTE]
> ワークスペース ベースの Application Insights を完全に保護するには、基になる Log Analytics ワークスペースと、Application Insights リソースへの両方のアクセスをロックダウンする必要があります。
>
> コードレベルの診断 (プロファイラーまたはデバッガー) を行うには、プライベート リンクをサポートする[ご自身のストレージ アカウントを指定する](../app/profiler-bring-your-own-storage.md)必要があります。

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Private Link の全か無かの性質の対処
「[Private Link のセットアップを計画する](#planning-your-private-link-setup)」で説明したように、1 つのリソースに対して Private Link を設定すると、そのネットワークと、同じ DNS を共有する他のネットワーク内のすべての Azure Monitor リソースに影響します。 この動作により、オンボード プロセスが困難になる可能性があります。 次のオプションを検討してください。

* すべて - 最も単純かつ安全な方法は、すべての Application Insights コンポーネントを AMPLS に追加することです。 他のネットワークからも引き続きアクセスする必要があるコンポーネントの場合は、[取り込み/クエリにパブリック インターネット アクセスを許可する] フラグを [はい] (既定) のままにします。
* ネットワークを分離する - スポーク VNet を使用している (または連携できる) 場合は、「[Azure のハブスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)」に記載されているガイダンスに従ってください。 次に、関連するスポーク VNet で、Private Link の設定を個別にセットアップします。 DNS ゾーンを他のスポーク ネットワークと共有すると [DNS がオーバーライドされる](#the-issue-of-dns-overrides)ため、DNS ゾーンも分離するようにしてください。
* 特定のアプリにカスタム DNS ゾーンを使用する - このソリューションでは、Private Link を介して選択された Application Insights コンポーネントにアクセスし、他のすべてのトラフィックはパブリック ルート経由にすることができます。
    - [カスタム プライベート DNS ゾーン](../../private-link/private-endpoint-dns.md)を設定し、internal.monitor.azure.com などの一意の名前を付けます。
    - AMPLS とプライベート エンドポイントを作成し、プライベート DNS と自動統合 **しない** ように選択します。
    - [プライベート エンドポイント]、[DNS 構成] の順にアクセスし、FQDN の推奨マッピングを確認します。
    - [構成の追加] を選択して、先ほど作成した internal.monitor.azure.com ゾーンを選択します。
    - 上記のレコードを追加します。![構成された DNS ゾーンのスクリーンショット](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Application Insights コンポーネントにアクセスし、[接続文字列](../app/sdk-connection-string.md)をコピーします。
    - Private Link を介してこのコンポーネントを呼び出すアプリまたはスクリプトでは、EndpointSuffix=internal.monitor.azure.com の接続文字列を使用する必要があります。
* DNS ではなく hosts ファイルを介してエンドポイントをマップする - ネットワーク内の特定のマシンまたは VM からのみ Private Link にアクセスできるようにします。
    - AMPLS とプライベート エンドポイントを設定し、プライベート DNS と自動統合 **しない** ように選択します。 
    - hosts ファイルでアプリを実行するコンピューターの上記の A レコードを構成します。


## <a name="use-apis-and-command-line"></a>API とコマンド ラインの使用

前に説明したプロセスは、Azure Resource Manager テンプレート、REST、コマンド ライン インターフェイスを使用して自動化できます。

プライベート リンク スコープを作成して管理するには、[REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) または [Azure CLI (az monitor private-link-scope)](/cli/azure/monitor/private-link-scope) を使用します。

ネットワーク アクセスを管理するには、[Log Analytics ワークスペース](/cli/azure/monitor/log-analytics/workspace)または [Application Insights コンポーネント](/cli/azure/ext/application-insights/monitor/app-insights/component)でフラグ `[--ingestion-access {Disabled, Enabled}]` と `[--query-access {Disabled, Enabled}]` を使用します。

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Private Link 経由でカスタム ログと IIS ログを収集する

ストレージ アカウントは、カスタム ログの取り込みプロセスで使用されます。 既定では、サービスで管理されるストレージ アカウントが使用されます。 ただし、プライベート リンクにカスタム ログを取り込むには、独自のストレージ アカウントを使用して、それらを Log Analytics ワークスペースに関連付ける必要があります。 [コマンド ライン](/cli/azure/monitor/log-analytics/workspace/linked-storage)を使用してこのようなアカウントをセットアップする方法の詳細について参照してください。

独自のストレージ アカウントを使用する方法の詳細については、「[ログ取り込み用の顧客所有のストレージ アカウント](private-storage.md)」を参照してください。

## <a name="restrictions-and-limitations"></a>制限事項と制約事項

### <a name="ampls"></a>AMPLS
AMPLS オブジェクトには、Private Link の構成を計画するときに考慮に入れる必要のある制限がいくつかあります。

* 1 つの VNet は、1 つの AMPLS オブジェクトにのみ接続できます。 これは、その AMPLS オブジェクトで、VNet でアクセスできる必要のあるすべての Azure Monitor リソースへのアクセスを提供する必要があることを意味します。
* 1 つの Azure Monitor リソース (ワークスペースまたは Application Insights コンポーネント) は、最大 5 つの AMPLS に接続できます。
* 1 つの AMPLS オブジェクトは、最大 50 個の Azure Monitor リソースに接続できます。
* 1 つの AMPLS オブジェクトは、最大 10 個のプライベート エンドポイントに接続できます。

これらの制限の詳細については、「[制限を考慮に入れる](#consider-limits)」を参照してください。

### <a name="agents"></a>エージェント

Log Analytics ワークスペースへのセキュリティで保護された取り込みをサポートするには、最新バージョンの Windows および Linux エージェントを使用する必要があります。 以前のバージョンでは、プライベート ネットワーク経由で監視データをアップロードすることはできません。

**Log Analytics Windows エージェント**

Log Analytics エージェント バージョン 10.20.18038.0 以降を使用します。

**Log Analytics Linux エージェント**

エージェント バージョン 1.12.25 以降を使用します。 できない場合は、VM で次のコマンドを実行します。

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure portal

Application Insights や Log Analytics などの Azure Monitor ポータル エクスペリエンスを使用するには、プライベート ネットワークで Azure portal および Azure Monitor の拡張機能にアクセスできるようにする必要があります。 **AzureActiveDirectory**、**AzureResourceManager**、**AzureFrontDoor.FirstParty**、および **AzureFrontdoor.Frontend** [サービス タグ](../../firewall/service-tags.md)をネットワーク セキュリティ グループに追加します。

### <a name="querying-data"></a>データのクエリを実行する
[`externaldata` 演算子](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor)は、ストレージ アカウントからデータを読み取りますが、ストレージにプライベートでアクセスすることは保証されないため、Private Link 経由ではサポートされていません。

### <a name="programmatic-access"></a>プログラムによるアクセス

REST API、[CLI](/cli/azure/monitor) または PowerShell をプライベート ネットワーク上の Azure Monitor で使用するには、[サービス タグ](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** と **AzureResourceManager** をファイアウォールに追加します。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>コンテンツ配信ネットワークから Application Insights SDK をダウンロードする

ブラウザーが CDN からコードをダウンロードしないように、スクリプトに JavaScript コードをバンドルします。 例については [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) を参照してください。

### <a name="browser-dns-settings"></a>ブラウザーの DNS 設定

プライベート リンクを介して Azure Monitor リソースに接続している場合、これらのリソースへのトラフィックは、お使いのネットワーク上で構成されているプライベート エンドポイントを経由する必要があります。 プライベート エンドポイントを有効にするには、「[プライベート エンドポイントへの接続](#connect-to-a-private-endpoint)」の説明に従って、DNS 設定を更新します。 一部のブラウザーでは、ユーザーが設定したものではなく、ブラウザー自身の DNS 設定が使用されています。 ブラウザーが Azure Monitor パブリック エンドポイントに接続を試みる際に、プライベート リンクを完全にバイパスする場合があります。 ブラウザーの設定によって、DNS 設定がオーバーライドされていないこと、また、古い DNS 設定がキャッシュされていないことを確認してください。 

## <a name="next-steps"></a>次のステップ

- [プライベート ストレージ](private-storage.md)について確認します