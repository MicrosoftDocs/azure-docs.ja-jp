---
title: Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する
description: Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: ''
ms.openlocfilehash: 6045fa475b3bb112afee9ceacd8d6b136087feab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077170"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する

> [!IMPORTANT]
> 現時点では、この機能を使用するには**アクセス権をリクエスト**する必要があります。 [サインアップ フォーム](https://aka.ms/AzMonPrivateLinkSignup)を使用してアクセスに応募できます。


[Azure Private Link](../../private-link/private-link-overview.md) を使用すると、プライベート エンドポイントを使用して Azure PaaS サービスを仮想ネットワークに安全に接続できます。 多くのサービスでは、リソースごとにエンドポイントを設定するだけです。 ただし、Azure Monitor は、相互に連携してワークロードを監視する、相互接続されたさまざまなサービスの集まりです。 そのため、監視ネットワークの境界を定義し、仮想ネットワークに接続できるようにする Azure Monitor Private Link Scope (AMPLS) というリソースを作成しました。 この記事では、Azure Monitor Private Link スコープを使用するタイミングと、その設定方法について説明します。

## <a name="advantages"></a>長所

Private Link を使用すると、次のことができます。

- パブリック ネットワーク アクセスを開かずに Azure Monitor にプライベートに接続する
- 承認されたプライベート ネットワーク経由でのみ監視データにアクセスできるようにする
- プライベート エンドポイント経由で接続する特定の Azure Monitor リソースを定義して、プライベート ネットワーク経由のデータ流出を防ぐ
- ExpressRoute と Private Link を使用して、オンプレミスのプライベート ネットワークを Azure Monitor に安全に接続する
- すべてのトラフィックを Microsoft Azure のバックボーン ネットワーク内に収める

詳細については、[Private Link の主な利点](../../private-link/private-link-overview.md#key-benefits)に関するページを参照してください。

## <a name="how-it-works"></a>しくみ

Azure Monitor Private Link Scope は、1 つまたは複数のプライベート エンドポイント (つまり、それを格納する仮想ネットワークも同様) を 1 つまたは複数の Azure Monitor リソースに接続するためのグループ化リソースです。 リソースには、Log Analytics ワークスペースと Application Insights コンポーネントが含まれます。

![リソース トポロジの図](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> 1 つの Azure Monitor リソースは複数の AMPLS に属することはできますが、1 つの VNet を複数の AMPLS に接続することはできません。 

## <a name="planning-based-on-your-network"></a>ネットワークに基づく計画

AMPLS リソースを設定する前に、ネットワークの分離要件を検討してください。 仮想ネットワークのパブリック インターネットへのアクセスと、各 Azure Monitor リソース (Application Insights コンポーネントと Log Analytics ワークスペース) のアクセス制限を評価します。

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>プライベート リンクに接続する仮想ネットワークを評価する

まず、インターネットへのアクセスが制限されている仮想ネットワーク (VNet) を評価します。 無料のインターネットを使用している VNet の場合、Azure Monitor リソースにアクセスするために Private Link を必要としない場合があります。 VNet が接続する監視リソースによっては、受信トラフィックが制限され、Private Link 接続が要求される場合があります (ログ取り込みまたはクエリのいずれかのため)。 このような場合は、パブリック インターネットにアクセスできる VNet であっても、Private Link を使用し、かつ AMPLS を介してこれらのリソースに接続する必要があります。

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Private Link を使用する Azure Monitor リソースを評価する

各 Azure Monitor リソースを確認します。

- リソースで、特定の VNet にあるリソースからのログ取り込みのみを許可すべきか。
- 特定の VNet に配置されているクライアントのみにリソースのクエリを許可すべきかどうか。

これらの質問に対する回答が「はい」の場合は、[Log Analytics ワークスペースの構成](#configure-log-analytics)、および [Application Insights コンポーネントの構成](#configure-application-insights)に関するの説明に従って制限を設定し、これらのリソースを 1 つまたは複数の AMPLS に関連付けます。 これらの監視リソースにアクセスする必要がある仮想ネットワークには、関連する AMPLS に接続するためのプライベート エンドポイントが必要です。
同じワークスペースまたはアプリケーションを複数の AMPLS に接続して、異なるネットワークからアクセス可能にできることを忘れないでください。

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>ネットワーク アクセシビリティごとに監視リソースをまとめてグループ化する

各 VNet は 1 つの AMPLS リソースにしか接続できないため、同じネットワークにアクセスできるようにする必要がある監視リソースはまとめてグループ化する必要があります。 このグループを管理する最も簡単な方法は、VNet ごとに 1 つの AMPLS を作成し、そのネットワークに接続するリソースを選択することです。 ただし、リソースを減らし、管理を簡単にするには、ネットワーク経由で AMPLS を再利用することが必要になる場合があります。

たとえば、内部仮想ネットワーク VNet1 と VNet2 をワークスペース Workspace1 と ワークスペース Workspace2、および Application Insights コンポーネントの Application Insights 3 に接続する必要がある場合は、3 つすべてのリソースを同じ AMPLS に関連付けます。 VNet3 が Workspace1 にのみアクセスできるようにするには、次の図に示すように、別の AMPLS リソースを作成し、Workspace1 をそれに関連付け、VNet3 を接続します。

![AMPLS A トポロジの図](./media/private-link-security/ampls-topology-a-1.png)

![AMPLS B トポロジの図](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>制限を考慮に入れる

Private Link の構成を計画するときには、考慮に入れる必要のある制限がいくつかあります。

* 1 つの VNet は、1 つの AMPLS オブジェクトにのみ接続できます。 これは、その AMPLS オブジェクトで、VNet でアクセスできる必要のあるすべての Azure Monitor リソースへのアクセスを提供する必要があることを意味します。
* 1 つの Azure Monitor リソース (ワークスペースまたは Application Insights コンポーネント) は、最大 5 つの AMPLS に接続できます。
* 1 つの AMPLS オブジェクトは、最大 20 個の Azure Monitor リソースに接続できます。
* 1 つの AMPLS オブジェクトは、最大 10 個のプライベート エンドポイントに接続できます。

下記のトポロジの場合:
* 各 VNet は 1 つの AMPLS オブジェクトに接続するので、他の AMPLS には接続できません。
* AMPLS B は 2 つの Vnet に接続しています。使用可能なプライベート エンドポイント接続 10 のうち 2 つを使用しています。
* AMPLS A は 2 つのワークスペースと 1 つの Application Insights コンポーネントに接続しています。使用可能な Azure Monitor リソース 20 個のうち 3 個を使用しています。
* ワークスペース 2 は AMPLS A と AMPLS B に接続しています。使用可能な AMPLS 接続 5 つのうち 2 つを使用しています。

![AMPLS の制限に関する図](./media/private-link-security/ampls-limits.png)

## <a name="example-connection"></a>接続の例

まず、Azure Monitor Private Link Scope リソースを作成します。

1. Azure portal で **[リソースの作成]** に移動して、「**Azure Monitor Private Link Scope**」を検索します。

   ![Azure Monitor Private Link スコープを検索する](./media/private-link-security/ampls-find-1c.png)

2. **[Create]\(作成\)** をクリックします。
3. サブスクリプションとリソース グループを選択します。
4. AMPLS に名前を付けます。 スコープの使用目的とセキュリティ境界を明確に示す名前を使用することをお勧めします。これは、だれかが誤ってネットワーク セキュリティ境界を破ることがないようにするためです。 たとえば、"AppServerProdTelem" などです。
5. **[Review + Create]\(レビュー + 作成\)** をクリックします。 

   ![Azure Monitor Private Link スコープを作成する](./media/private-link-security/ampls-create-1d.png)

6. 検証をパスしたら、 **[作成]** をクリックします。

## <a name="connect-azure-monitor-resources"></a>Azure Monitor リソースの接続

最初に AMPLS をプライベート エンドポイントに接続してから Azure Monitor リソースに接続することも、その逆を行うこともできますが、Azure Monitor リソースから始めることで接続プロセスが高速になります。 Azure Monitor Log Analytics ワークスペースと Application Insights コンポーネントを AMPLS に接続する方法を次に示します。

1. Azure Monitor Private Link Scope で、左側のメニューの **[Azure Monitor リソース]** をクリックします。 **[追加]** をクリックします。
2. ワークスペースまたはコンポーネントを追加します。 **[追加]** ボタンをクリックするとダイアログが表示され、Azure Monitor リソースを選択できます。 サブスクリプションとリソース グループを参照するか、名前を入力してフィルターを適用できます。 ワークスペースまたはコンポーネントを選択し、 **[適用]** をクリックしてスコープに追加します。

    ![スコープの選択 UX のスクリーンショット](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>プライベート エンドポイントへの接続

これで、AMPLS にリソースを接続できたので、ネットワークを接続するためのプライベート エンドポイントを作成します。 このタスクは、この例のように、[Azure portal Private Link センター](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)、または Azure Monitor Private Link Scope 内で実行できます。

1. スコープ リソースで、左側のリソース メニューの **[プライベート エンドポイント接続]** をクリックします。 **[プライベート エンドポイント]** をクリックして、エンドポイント作成プロセスを開始します。 Private Link センターで開始された接続をここで承認するには、そのリンクを選択し、 **[承認]** をクリックします。

    ![プライベート エンドポイント接続 UX のスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. サブスクリプション、リソース グループ、およびエンドポイントの名前と、それを有効にするリージョンを選択します。 リージョンは、接続先の仮想ネットワークと同じリージョンである必要があります。

3. **[次へ: リソース]** を選択します。 

4. リソース画面で、

   a. Azure Monitor プライベート スコープ リソースを含む **[サブスクリプション]** を選択します。 

   b. **[リソースの種類]** には、**Microsoft.insights/privateLinkScopes** を選択してください。 

   c. **[リソース]** ドロップダウンから、前に作成した Private Link スコープを選択します。 

   d. **次へ: 構成 >** をクリックします。
      ![[プライベート エンドポイントを作成する] の選択のスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. [構成] ウィンドウで、

   a.    Azure Monitor リソースに接続する **[仮想ネットワーク]** と **[サブネット]** を選択します。 
 
   b.    **[プライベート DNS ゾーンとの統合]** で **[はい]** を選択して、新しいプライベート DNS ゾーンを自動で作成します。 実際の DNS ゾーンは、下のスクリーンショットに示されているものとは異なる場合があります。 
 
   c.    **[Review + create]\(レビュー + 作成\)** をクリックします。
 
   d.    検証をパスします。 
 
   e.    **Create** をクリックしてください。 

    ![[プライベート エンドポイント 2 を作成する] の選択のスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

これで、この Azure Monitor Private Link Scope に接続された新しいプライベート エンドポイントが作成されました。

## <a name="configure-log-analytics"></a>Log Analytics の構成

Azure Portal にアクセスします。 Log Analytics ワークスペース リソースの左側に、 **[ネットワークの分離]** というメニュー項目があります。 このメニューから、2 つの異なる状態を制御できます。 

![LA ネットワークの分離](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

まず、この Log Analytics リソースを、アクセス権を持つ任意の Azure Monitor Private Link Scope に接続できます。 **[追加]** をクリックし、[Azure Monitor Private Link Scope] を選択します。  **[適用]** をクリックして接続します。 接続されているすべてのスコープがこの画面に表示されます。 この接続を確立すると、接続された仮想ネットワーク内のネットワーク トラフィックがこのワークスペースに接続できるようになります。 接続の確立は、[Azure Monitor リソースの接続](#connect-azure-monitor-resources)の場合と同じように、スコープから接続するのと同じ効果があります。  

次に、このリソースに対して、上記の Pivate Link スコープの外部からアクセスする方法を制御できます。 **[Allow public network access for ingestion]\(取り込みにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、接続されているスコープ外のマシンは、このワークスペースにデータをアップロードできません。 **[Allow public network access for queries]\(クエリにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、スコープ外のマシンは、このワークスペースのデータにアクセスできません。 このデータには、ブック、ダッシュボード、クエリ API ベースのクライアント エクスペリエンス、Azure portal の分析情報などへのアクセスが含まれます。 Azure portal の外部で実行され、Log Analytics データにクエリを発行するエクスペリエンスも、プライベート リンク VNET 内で実行する必要があります。

この方法でのアクセス制限は、ワークスペース内のデータにのみ適用されます。 これらのアクセス設定をオンまたはオフにするなどの構成の変更は、Azure Resource Manager で管理されます。 適切なロール、アクセス許可、ネットワーク制御、および監査を使用して、リソース マネージャーへのアクセスを制限します。 詳細については、[Azure Monitor のロール、アクセス許可、およびセキュリティ](roles-permissions-security.md)に関するページを参照してください。

> [!NOTE]
> [[診断設定]](diagnostic-settings.md) を使用してワークスペースにアップロードされたログとメトリックは、セキュリティで保護されたプライベート Microsoft チャネルを経由し、これらの設定によって制御されることはありません。

## <a name="configure-application-insights"></a>Application Insights の構成

Azure Portal にアクセスします。 Azure Monitor Application Insights コンポーネント リソースの左側に、 **[ネットワークの分離]** というメニュー項目があります。 このメニューから、2 つの異なる状態を制御できます。

![AI ネットワークの分離](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

まず、この Application Insights リソースを、アクセス権を持つ任意の Azure Monitor Private Link Scope に接続できます。 **[追加]** をクリックし、 **[Azure Monitor Private Link Scope]** を選択します。 [適用] をクリックして接続します。 接続されているすべてのスコープがこの画面に表示されます。 この接続を確立すると、接続された仮想ネットワーク内のネットワーク トラフィックがこのコンポーネントに接続できるようになります。 接続の確立は、[Azure Monitor リソースの接続](#connect-azure-monitor-resources)の場合と同じように、スコープから接続するのと同じ効果があります。 

次に、このリソースに対して、前述の Pivate Link スコープの外部からアクセスする方法を制御できます。 **[Allow public network access for ingestion]\(取り込みにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、接続されているスコープ外のマシンまたは SDK は、このコンポーネントにデータをアップロードできません。 **[Allow public network access for queries]\(クエリにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、スコープ外のマシンは、この Application Insights リソースのデータにアクセスできません。 このデータには、APM ログ、メトリック、ライブ メトリック ストリームへのアクセスと、ブック、ダッシュボード、クエリ API ベースのクライアント エクスペリエンス、Azure portal の分析情報など、その上に構築されたエクスペリエンスが含まれます。 

ポータル以外の消費エクスペリエンスも、監視対象のワークロードを含むプライベート リンク VNET 内で実行されている必要があることに注意してください。 

監視対象のワークロードをホストしているリソースをプライベート リンクに追加する必要があります。 App Services でこれを行う方法については、[ドキュメント](../../app-service/networking/private-endpoint.md)を参照してください。

この方法でのアクセス制限は、Application Insights リソース内のデータにのみ適用されます。 これらのアクセス設定をオンまたはオフにするなどの構成の変更は、Azure Resource Manager で管理されます。 代わりに、適切なロール、アクセス許可、ネットワーク制御、および監査を使用して、リソース マネージャーへのアクセスを制限します。 詳細については、[Azure Monitor のロール、アクセス許可、およびセキュリティ](roles-permissions-security.md)に関するページを参照してください。

> [!NOTE]
> ワークスペース ベースの Application Insights を完全に保護するには、基になる Log Analytics ワークスペースと、Application Insights リソースへの両方のアクセスをロックダウンする必要があります。
>
> コードレベルの診断 (プロファイラーまたはデバッガー) を行うには、プライベート リンクをサポートするご自身のストレージ アカウントを指定する必要があります。 これを行う方法については、[こちらのドキュメント](../app/profiler-bring-your-own-storage.md)を参照してください。

## <a name="use-apis-and-command-line"></a>API とコマンド ラインの使用

前に説明したプロセスは、Azure Resource Manager テンプレートとコマンド ライン インターフェイスを使用して自動化できます。

Pivate Link スコープを作成して管理するには、[az monitor private-link-scope](/cli/azure/monitor/private-link-scope?view=azure-cli-latest) を使用します。 このコマンドを使用すると、スコープの作成、Log Analytics ワークスペースと Application Insights コンポーネントの関連付け、プライベート エンドポイントの追加、削除、承認を行うことができます。

ネットワーク アクセスを管理するには、[Log Analytics ワークスペース](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest)または [Application Insights コンポーネント](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest)でフラグ `[--ingestion-access {Disabled, Enabled}]` と `[--query-access {Disabled, Enabled}]` を使用します。

## <a name="collect-custom-logs-over-private-link"></a>Private Link 経由でカスタム ログを収集する

ストレージ アカウントは、カスタム ログの取り込みプロセスで使用されます。 既定では、サービスで管理されるストレージ アカウントが使用されます。 ただし、プライベート リンクにカスタム ログを取り込むには、独自のストレージ アカウントを使用して、それらを Log Analytics ワークスペースに関連付ける必要があります。 [コマンド ライン](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest)を使用してこのようなアカウントをセットアップする方法の詳細について参照してください。

独自のストレージ アカウントを使用する方法の詳細については、「[ログ取り込み用の顧客所有のストレージ アカウント](private-storage.md)」を参照してください。

## <a name="restrictions-and-limitations"></a>制限事項と制約事項

### <a name="agents"></a>エージェント

Log Analytics ワークスペースへのセキュリティで保護された取り込みを有効にするには、最新バージョンの Windows および Linux エージェントをプライベート ネットワーク上で使用する必要があります。 以前のバージョンでは、プライベート ネットワークに監視データをアップロードすることはできません。

**Log Analytics Windows エージェント**

Log Analytics エージェント バージョン 10.20.18038.0 以降を使用します。

**Log Analytics Linux エージェント**

エージェント バージョン 1.12.25 以降を使用します。 できない場合は、VM で次のコマンドを実行します。

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure portal

Application Insights や Log Analytics などの Azure Monitor ポータル エクスペリエンスを使用するには、プライベート ネットワークで Azure portal および Azure Monitor の拡張機能にアクセスできるようにする必要があります。 **AzureActiveDirectory**、**AzureResourceManager**、**AzureFrontDoor.FirstParty**、および **AzureFrontdoor.Frontend** [サービス タグ](../../firewall/service-tags.md)をファイアウォールに追加します。

### <a name="programmatic-access"></a>プログラムによるアクセス

REST API、[CLI](/cli/azure/monitor?view=azure-cli-latest) または PowerShell をプライベート ネットワーク上の Azure Monitor で使用するには、[サービスタグ](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** と **AzureResourceManager** をファイアウォールに追加します。

これらのタグを追加すると、ログ データのクエリ、Log Analytics ワークスペースや AI コンポーネントの作成と管理などの操作を実行できます。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>コンテンツ配信ネットワークから Application Insights SDK をダウンロードする

ブラウザーが CDN からコードをダウンロードしないように、スクリプトに JavaScript コードをバンドルします。 例については [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) を参照してください。

### <a name="log-analytics-solution-download"></a>Log Analytics ソリューションのダウンロード

Log Analytics エージェントでソリューション パックをダウンロードできるようにするには、適切な完全修飾ドメイン名をファイアウォールの許可リストに追加します。 


| クラウド環境 | エージェントのリソース | Port | Direction |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | 送信
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  送信
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 送信

### <a name="browser-dns-settings"></a>ブラウザーの DNS 設定

プライベート リンクを介して Azure Monitor リソースに接続している場合、これらのリソースへのトラフィックは、お使いのネットワーク上で構成されているプライベート エンドポイントを経由する必要があります。 プライベート エンドポイントを有効にするには、「[プライベート エンドポイントへの接続](#connect-to-a-private-endpoint)」の説明に従って、DNS 設定を更新します。 一部のブラウザーでは、ユーザーが設定したものではなく、ブラウザー自身の DNS 設定が使用されています。 ブラウザーが Azure Monitor パブリック エンドポイントに接続を試みる際に、プライベート リンクを完全にバイパスする場合があります。 ブラウザーの設定によって、DNS 設定がオーバーライドされていないこと、また、古い DNS 設定がキャッシュされていないことを確認してください。 

## <a name="next-steps"></a>次のステップ

- [プライベート ストレージ](private-storage.md)について確認します
