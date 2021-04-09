---
title: ISE を使用して Azure 仮想ネットワークに接続する
description: Azure Logic Apps から Azure 仮想ネットワーク (VNET) にアクセスできる統合サービス環境 (ISE) を作成します。
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 315de18539bf083515658b40fa70f3c214d7c909
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739741"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>統合サービス環境 (ISE) を使用して Azure Logic Apps から Azure Virtual Network に接続する

ロジック アプリと統合アカウントが [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)にアクセスする必要があるシナリオでは、"[*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成します。 ISE は、専用のストレージと、"グローバル" なマルチテナント Logic Apps サービスとは別に確保されている他のリソースを使用する専用環境です。 この分離で、他の Azure テナントがご利用のアプリのパフォーマンスに与える可能性がある影響も軽減されます。 ISE には、独自の静的 IP アドレスも用意されています。 これらの IP アドレスは、パブリックのマルチテナント サービスのロジック アプリによって共有される静的 IP アドレスとは別のものです。

ISE を作成すると、Azure によってその ISE が Azure 仮想ネットワークに "*挿入*" され、その仮想ネットワークに Logic Apps サービスがデプロイされます。 ロジック アプリまたは統合アカウントを作成するときに、お使いの ISE を場所として選択します。 ロジック アプリまたは統合アカウントは、仮想ネットワーク内の仮想マシン (VM)、サーバー、システム、サービスなどのリソースに直接アクセスできます。

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> ISE でロジック アプリと統合アカウントを連携させるには、両方とも "*同じ ISE*" を場所として使用する必要があります。

ISE では、実行継続時間、ストレージのリテンション期間、スループット、HTTP の要求と応答のタイムアウト、メッセージのサイズ、およびカスタム コネクタの要求の上限が引き上げられました。 詳細については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページを参照してください。 ISE の詳細については、[Azure Logic Apps から Azure Virtual Network リソースへのアクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)に関する記事を参照してください。

この記事では、Azure Portal を使ってこれらのタスクを完了する方法について説明します。

* ISE のアクセスを有効にします。
* ISE を作成します。
* ISE に容量を追加します。

カスタマー マネージド キーの設定も含め、ISE は、[サンプル Azure Resource Manager クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)を使用するか、Logic Apps REST API を使用して作成することもできます。

* [Logic Apps REST API を使用して統合サービス環境 (ISE) を作成する](../logic-apps/create-integration-service-environment-rest-api.md)
* [ISE の保存データを暗号化するためにカスタマー マネージド キーを設定する](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

  > [!IMPORTANT]
  > ISE 内で実行されるロジック アプリ、組み込みトリガー、組み込みアクション、およびコネクターでは、使用量ベースの価格プランとは異なる価格プランが使用されます。 ISE の価格と課金のしくみについては、「[固定価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)」を参照してください。 価格については、[Logic Apps の価格](../logic-apps/logic-apps-pricing.md)に関する記事を参照してください。

* 4 つの "*空の*" サブネットがある [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)。これらは、ISE 内にリソースを作成してデプロイするために必要であり、これらの内部または非表示のコンポーネントで使用されます。

  * Logic Apps コンピューティング
  * 内部 App Service Environment (コネクタ)
  * 内部 API Management (コネクタ)
  * キャッシュとパフォーマンスのための内部 Redis
  
  サブネットは、事前に作成することも、ISE を作成するときに同時に作成することもできます。 ただし、サブネットを作成する前に、[サブネットの要件](#create-subnet)を必ず確認してください。

  * ISE が正常に動作し、アクセス可能な状態を維持できるように、仮想ネットワークで [ISE アクセスが有効になっている](#enable-access)ことを確認します。

  * [ExpressRoute](../expressroute/expressroute-introduction.md) を[強制トンネリング](../firewall/forced-tunneling.md)と共に使用する場合は、次の特定のルートを使用して[ルート テーブルを作成](../virtual-network/manage-route-table.md)し、ISE で使用される各サブネットにそのルート テーブルをリンクする必要があります。

    **名前**: <*route-name*><br>
    **アドレス プレフィックス**:0.0.0.0/0<br>
    **次ホップ**:インターネット
    
    この特定のルート テーブルは、Logic Apps コンポーネントが、Azure Storage や Azure SQL DB などの他に依存している Azure サービスと通信するために必要です。 このルートの詳細については、「[アドレス プレフィックス 0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route)」を参照してください。 ExpressRoute と共に強制トンネリングを使用しない場合、この特定のルート テーブルは必要ありません。
    
    ExpressRoute を使用すると、オンプレミスのネットワークを Microsoft クラウドに拡張し、接続プロバイダーが提供するプライベート接続を介して Microsoft クラウド サービスに接続できます。 具体的には、ExpressRoute は、パブリック インターネットを通じてではなくプライベート ネットワーク経由でトラフィックをルーティングする仮想プライベート ネットワークです。 ロジック アプリを ExpressRoute または仮想プライベート ネットワーク経由で接続するときに、同じ仮想ネットワーク内にあるオンプレミスのリソースに接続できます。
   
  * [ネットワーク仮想アプライアンス (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined) を使用する場合は、TLS/SSL 終端を有効にすることも、TLS/SSL の送信トラフィックを変更することもしないでください。 また、ISE のサブネットから発信されたトラフィックの検査を有効にしないようにしてください。 詳細については、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。

  * Azure 仮想ネットワークでカスタム DNS サーバーを使用する場合は、ISE を仮想ネットワークにデプロイする前に、[次の手順に従ってそのようなサーバーを設定します](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 DNS サーバー設定の管理方法に関する詳細については、「[仮想ネットワークの作成、変更、削除](../virtual-network/manage-virtual-network.md#change-dns-servers)」を参照してください。

    > [!NOTE]
    > DNS サーバーまたはその設定に変更を加えた場合は、それらの変更を ISE が取得できるよう、ISE を再起動する必要があります。 詳細については、「[ISE を再起動する](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)」を参照してください。

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE のアクセスを有効にする

Azure 仮想ネットワークで ISE を使用した場合、一般的な設定の問題はブロックされたポートが 1 つ以上あることです。 ISE と宛先システムとの間の接続を作成するために使用するコネクタにも、独自のポート要件がある可能性があります。 たとえば、FTP コネクタを使用して FTP システムと通信する場合、FTP システム上で使用するポート (コマンド送信用のポート 21 など) が使用可能である必要があります。

ISE にアクセスできること、および ISE 内のロジック アプリが仮想ネットワーク内のサブネット間で通信できることを確認するには、[各サブネットについてこの表に記載されたポートを開きます](#network-ports-for-ise)。 いずれかの必要なポートが使用できなった場合、ISE は正常に機能しません。

* IP 制限がある他のエンドポイントへのアクセスを必要とする ISE インスタンスが複数ある場合は、[Azure Firewall](../firewall/overview.md) または[ネットワーク仮想アプライアンス](../virtual-network/virtual-networks-overview.md#filter-network-traffic)を仮想ネットワークにデプロイし、そのファイアウォールまたはネットワーク仮想アプライアンスを経由して送信トラフィックをルーティングします。 その後、送信先システムとの通信のために仮想ネットワーク内のすべての ISE インスタンスで使用可能な、[静的かつ予測可能な 1 つの送信パブリック IP アドレスを設定](connect-virtual-network-vnet-set-up-single-ip-address.md)できます。 このようにすると、それらの送信先システムで ISE ごとに追加のファイアウォールを設定する必要はありません。

   > [!NOTE]
   > アクセスする必要がある IP アドレスの数をシナリオで制限する必要がある場合は、1 つの ISE に対してこの方法を使用できます。 ファイアウォールまたは仮想ネットワーク アプライアンスの追加コストが、シナリオにとって妥当かどうかを検討してください。 [Azure Firewall の価格](https://azure.microsoft.com/pricing/details/azure-firewall/)の詳細を確認してください。

* 新しい Azure 仮想ネットワークとサブネットを制約なしで作成した場合、サブネット間のトラフィックを制御する目的で仮想ネットワーク内に[ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) を設定する必要はありません。

* 既存の仮想ネットワークの場合は、*必要に応じて*[ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) を設定して、[サブネット間のネットワーク トラフィックをフィルター処理](../virtual-network/tutorial-filter-network-traffic.md)することができます。 このルートを使用する場合、または既に NSG を使用している場合は、NSG の[この表に記載されているポート](#network-ports-for-ise)を開いてください。

  [NSG セキュリティ規則](../virtual-network/network-security-groups-overview.md#security-rules)を設定する場合は、**TCP** プロトコルと **UDP** プロトコルの *両方* を使用するか、代わりに **[任意]** を選択する必要があります。そうしないと、プロトコルごとに個別のルールを作成する必要があります。 NSG セキュリティ規則では、これらのポートへのアクセスが必要な IP アドレスに対して開く必要があるポートが記述されています。 すべてのファイアウォール、ルーター、またはこれらのエンドポイント間に存在するその他の項目で、これらのポートがこれらの IP アドレスにアクセスできる状態になっていることも確認してください。

* ファイアウォールを経由してインターネットへのトラフィックをリダイレクトする強制トンネリングを設定した場合は、[強制トンネリングの追加要件](#forced-tunneling)を確認してください。

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE で使用されるネットワーク ポート

この表は、ISE でのアクセスが要求されるポートとそれらのポートの目的を示しています。 セキュリティ規則をもっと簡単に設定できるように、テーブルの[サービス タグ](../virtual-network/service-tags-overview.md)は、特定の Azure サービスの IP アドレス プレフィックス グループを表わしています。 明記されている場合、*内部 ISE* および *外部 ISE* は、[ISE の作成中に選択されたアクセス エンドポイント](connect-virtual-network-vnet-isolated-environment.md#create-environment)を意味します。 詳細については、「[エンドポイント アクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)」を参照してください。

> [!IMPORTANT]
> ソース ポートは一時的なものであるため、すべての規則において、ソース ポートが `*` に設定されていることを確認してください。

#### <a name="inbound-security-rules"></a>受信セキュリティ規則

| 目的 | ソース サービス タグまたは IP アドレス | ソース ポート | 宛先サービス タグまたは IP アドレス | 宛先ポート | Notes |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 仮想ネットワーク内のサブネット間通信 | 仮想ネットワークと ISE サブネットのアドレス空間 | * | 仮想ネットワークと ISE サブネットのアドレス空間 | * | トラフィックが仮想ネットワーク内のサブネットの "*間*" を通過するために必要です。 <p><p>**重要**:トラフィックが各サブネット内の "*コンポーネント*" 間を通過するには、各サブネット内のすべてのポートを開いていることを確認します。 |
| 両方: <p>ロジック アプリへの通信 <p><p>ロジック アプリの実行履歴| 内部 ISE: <br>**VirtualNetwork** <p><p>外部 ISE:**インターネット** (または **メモ** を参照) | * | **VirtualNetwork** | 443 | **Internet** サービス タグを使用する代わりに、これらの項目に発信元 IP アドレスを指定できます。 <p><p>- ロジック アプリ内の任意の要求トリガーまたは Webhook を呼び出すコンピューターまたはサービス <p>- ロジック アプリの実行履歴へのアクセス元となるコンピューターまたはサービス <p><p>**重要**:このポートを閉じるかブロックすると、要求トリガーまたは Webhook を持つロジック アプリへの呼び出しができなくなります。 また、実行履歴の各ステップの入力と出力にアクセスすることもできなくなります。 ただし、ロジック アプリの実行履歴にアクセスすることはできます。|
| Logic Apps デザイナー - 動的プロパティ | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | 要求は、そのリージョンの Logic Apps アクセス エンドポイントの[受信 IP アドレス](../logic-apps/logic-apps-limits-and-config.md#inbound)から取得されます。 |
| コネクタのデプロイ | **AzureConnectors** | * | **VirtualNetwork** | 454 | コネクタをデプロイおよび更新するために必要です。 このポートを閉じたりブロックしたりすると、ISE のデプロイが失敗し、コネクタの更新や修正ができなくなります。このポートを閉じたりブロックしたりすると、ISE のデプロイが失敗し、コネクタの更新や修正ができなくなります。 |
| ネットワーク正常性チェック | **LogicApps** | * | **VirtualNetwork** | 454 | 要求は、そのリージョンの[受信 IP アドレス](../logic-apps/logic-apps-limits-and-config.md#inbound)と[送信 IP アドレス](../logic-apps/logic-apps-limits-and-config.md#outbound)に対して Logic Apps アクセス エンドポイントから送信されます。 |
| App Service の管理の依存関係 | **AppServiceManagement** | * | **VirtualNetwork** | 454、455 ||
| Azure Traffic Manager からの通信 | **AzureTrafficManager** | * | **VirtualNetwork** | 内部 ISE:454 <p><p>外部 ISE:443 ||
| 両方: <p>コネクタ ポリシーのデプロイ <p>API Management - 管理エンドポイント | **APIManagement** | * | **VirtualNetwork** | 3443 | コネクタ ポリシーのデプロイについては、コネクタをデプロイおよび更新するためにポート アクセスが必要です。 このポートを閉じたりブロックしたりすると、ISE のデプロイが失敗し、コネクタの更新や修正ができなくなります。このポートを閉じたりブロックしたりすると、ISE のデプロイが失敗し、コネクタの更新や修正ができなくなります。 |
| ロール インスタンス間での Azure Cache for Redis インスタンスへのアクセス | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383、(さらに、**メモ** を参照)| Azure Cache for Redis で動作する ISE の場合は、[Azure Cache for Redis の FAQ で説明されている送信ポートと受信ポート](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)を開く必要があります。 |
|||||||

#### <a name="outbound-security-rules"></a>送信セキュリティ規則

| 目的 | ソース サービス タグまたは IP アドレス | ソース ポート | 宛先サービス タグまたは IP アドレス | 宛先ポート | Notes |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 仮想ネットワーク内のサブネット間通信 | 仮想ネットワークと ISE サブネットのアドレス空間 | * | 仮想ネットワークと ISE サブネットのアドレス空間 | * | トラフィックが仮想ネットワーク内のサブネットの "*間*" を通過するために必要です。 <p><p>**重要**:トラフィックが各サブネット内の "*コンポーネント*" 間を通過するには、各サブネット内のすべてのポートを開いていることを確認します。 |
| ロジック アプリからの通信 | **VirtualNetwork** | * | 宛先によって異なる | 80、443 | 宛先は、ロジック アプリが通信する必要がある外部サービスのエンドポイントによって異なります。 |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80、443 ||
| Azure Storage の依存関係 | **VirtualNetwork** | * | **Storage** | 80、443、445 ||
| 接続管理 | **VirtualNetwork** | * | **AppService** | 443 ||
| 診断ログとメトリックの発行 | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL 依存関係 | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | 正常性の状態を Resource Health に公開するために必要。 |
| ログから Event Hub ポリシーおよび監視エージェントへの依存関係 | **VirtualNetwork** | * | **EventHub** | 5672 ||
| ロール インスタンス間での Azure Cache for Redis インスタンスへのアクセス | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383、(さらに、**メモ** を参照)| Azure Cache for Redis で動作する ISE の場合は、[Azure Cache for Redis の FAQ で説明されている送信ポートと受信ポート](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)を開く必要があります。 |
| DNS 名前解決 | **VirtualNetwork** | * | 仮想ネットワーク上のカスタム ドメイン ネーム システム (DNS) サーバーの IP アドレス | 53 | 仮想ネットワークでカスタム DNS サーバーを使用する場合にのみ必要です |
|||||||

また、[App Service Environment (ASE)](../app-service/environment/intro.md) のアウトバウンド規則を追加する必要があります。

* Azure Firewall を使用する場合は、App Service Environment (ASE) の[完全修飾ドメイン名 (FQDN) タグ](../firewall/fqdn-tags.md#current-fqdn-tags)を使用してファイアウォールを設定する必要があります。これにより、ASE プラットフォームのトラフィックへの発信アクセスが許可されます。

* Azure Firewall 以外のファイアウォール アプライアンスを使用する場合は、App Service Environment に必要とされる、[ファイアウォール統合の依存関係](../app-service/environment/firewall-integration.md#dependencies)にリストされている "*すべて*" の規則を使用してファイアウォールを設定する必要があります。

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>強制トンネリングの要件

ファイアウォール経由の[強制トンネリング](../firewall/forced-tunneling.md)を設定または使用する場合は、ISE の追加の外部依存関係を許可する必要があります。 強制トンネリングを使用すると、インターネットへのトラフィックを、インターネットではなく、仮想プライベート ネットワーク (VPN) や仮想アプライアンスなどの特定のホップにリダイレクトできます。これにより、送信ネットワーク トラフィックを検査および監査することができます。

これらの依存関係へのアクセスが許可されていない場合、ISE のデプロイは失敗し、デプロイされた ISE は動作を停止します。

* ユーザー定義のルート

  非対称ルーティングを回避するには、以下に一覧表示されているすべての IP アドレスそれぞれに対して **インターネット** で次ホップとしてルートを定義する必要があります。
  
  * [App Service Environment の管理アドレス](../app-service/environment/management-addresses.md)
  * [ISE リージョンのコネクタの Azure IP アドレス。このダウンロード ファイルで入手できます](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager の管理アドレス](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [ISE 領域における Logic Apps の受信および送信アドレス](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [ISE リージョンのコネクタの Azure IP アドレス。このダウンロード ファイルにあります](https://www.microsoft.com/download/details.aspx?id=56519)

* サービス エンドポイント

  Azure SQL、Storage、Service Bus、KeyVault、および Event Hubs のサービス エンドポイントを有効にする必要があります。これは、ファイアウォールを経由してこれらのサービスにトラフィックを送信することはできないためです。

*  その他の受信および送信の依存関係

   ファイアウォールで、次の受信および送信の依存関係を許可する "*必要*" があります。
   
   * [Azure App Service の依存関係](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Azure キャッシュ サービスの依存関係](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Azure API Management の依存関係](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE を作成する

1. [Azure portal](https://portal.azure.com) で、Azure のメイン検索ボックスに、フィルターとして「`integration service environments`」と入力し、 **[統合サービス環境]** を選択します。

   ![[統合サービス環境] を探して選択](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. **[統合サービス環境]** ペインで、 **[追加]** を選択します。

   ![統合サービス環境を作成するには、[追加] を選択します](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 次の例のように、環境について次の詳細を指定してから **[確認と作成]** を選択します。

   ![環境の詳細を指定する](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | 環境に使用する Azure サブスクリプション |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | 環境を作成する新しいまたは既存の Azure リソース グループ |
   | **統合サービス環境の名前** | はい | <*environment-name*> | ISE 名。英字、数字、ハイフン (`-`)、アンダースコア (`_`)、ピリオド (`.`) のみを含めることができます。 |
   | **場所** | はい | <*Azure-datacenter-region*> | 環境をデプロイする Azure データセンター リージョン |
   | **SKU** | はい | **Premium** または **Developer (SLA なし)** | 作成および使用する ISE SKU。 これらの SKU の違いについては、[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に関する記事を参照してください。 <p><p>**重要**:このオプションは、ISE 作成時にのみ使用できます。後で変更することはできません。 |
   | **追加容量** | Premium: <br>はい <p><p>Developer: <br>適用なし | Premium: <br>0 から 10 <p><p>Developer: <br>適用なし | この ISE リソースに使用する追加の処理ユニット数。 作成後に容量を追加する場合は、「[ISE の容量を追加する](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)」を参照してください。 |
   | **アクセス エンドポイント** | はい | **内部** または **外部** | ISE に使用するアクセス エンドポイントの種類。 これらのエンドポイントにより、ISE 内のロジック アプリ上で要求または Webhook トリガーが仮想ネットワークの外からの呼び出しを受信できるかどうかが決まります。 <p><p>たとえば、次の Webhook ベース トリガーを使用する場合、必ず **[外部]** を選択してください。 <p><p>- Azure DevOps <br>- Azure Event Grid <br>- Common Data Service <br>- Office 365 <br>- SAP (ISE バージョン) <p><p>選択した内容は、ロジック アプリの実行履歴の入力と出力の表示やアクセスの方法にも影響します。 詳細については、「[ISE エンドポイント アクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)」を参照してください。 <p><p>**重要**:アクセス エンドポイントは、ISE 作成中にのみ選択できます。後でこのオプションを変更することはできません。 |
   | **Virtual Network** | はい | <*Azure-virtual-network-name*> | 環境内のロジック アプリが仮想ネットワークにアクセスできるように、その環境を挿入する Azure 仮想ネットワーク。 ネットワークがない場合は、[まず Azure 仮想ネットワークを作成](../virtual-network/quick-create-portal.md)します。 <p><p>**重要**:ISE を作成するときに "*のみ*"、この挿入を実行することができます。 |
   | **サブネット** | はい | <*subnet-resource-list*> | ISE には、*空の* サブネットが 4 つ必要です。これらのサブネットは、ISE 内にリソースを作成してデプロイするために必要であり、内部の Logic Apps コンポーネント (コネクタやパフォーマンス用のキャッシュなど) で使用されます。 <p>**重要**:[サブネットを作成する手順](#create-subnet)に進む前に、必ずサブネットの要件を確認してください。 |
   |||||

   <a name="create-subnet"></a>

   **サブネットを作成する**

   ISE には、"*空*" のサブネットが 4 つ必要です。このサブネットは、ISE 内にリソースを作成してデプロイするために必要であり、内部の Logic Apps コンポーネント (コネクタやパフォーマンス用のキャッシュなど) で使用されます。 環境の作成後に、これらのサブネット アドレスを変更することは *できません*。 Azure portal で ISE を作成してデプロイする場合は、これらのサブネットをどの Azure サービスにも委任しないようにしてください。 ただし、REST API、Azure PowerShell、または Azure Resource Manager テンプレートを使用して ISE をデプロイする場合は、1 つの空のサブネットを `Microsoft.integrationServiceEnvironment` に[委任する](../virtual-network/manage-subnet-delegation.md)必要があります。 詳細については、[サブネットの委任の追加](../virtual-network/manage-subnet-delegation.md)に関するページを参照してください。

   各サブネットは、次の要件を満たしている必要があります。

   * 名前の先頭がアルファベット文字かアンダースコア (数字ではない) で、名前に `<`、`>`、`%`、`&`、`\\`、`?`、`/` の文字が使用されていない。

   * [Classless Inter-Domain Routing (CIDR) 形式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)を使用する。
   
     > [!IMPORTANT]
     >
     > 次の IP アドレス空間は Azure Logic Apps によって解決されないため、仮想ネットワークやサブネットに使用しないでください。<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * 各サブネットには 32 個のアドレスが必要なため、アドレス空間で `/27` を使用する。 たとえば、2<sup>(32-27)</sup> は 2<sup>5</sup> (つまり 32) なので、`10.0.0.0/27` には 32 個のアドレスがあります。 アドレスが増加しても追加の利点はありません。 アドレス計算の詳細については、「[IPv4 CIDR blocks (IPv4 CIDR ブロック)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)」を参照してください。

   * [ExpressRoute](../expressroute/expressroute-introduction.md) を使用する場合、次のルートを持つ[ルート テーブルを作成](../virtual-network/manage-route-table.md)し、ISE によって使用される各サブネットにそのテーブルをリンクする必要があります。

     **名前**: <*route-name*><br>
     **アドレス プレフィックス**:0.0.0.0/0<br>
     **次ホップ**:インターネット

   1. **[サブネット]** 一覧で **[サブネット構成の管理]** を選択します。

      ![サブネット構成の管理](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. **[サブネット]** ペインで **[サブネット]** を選択します。

      ![空のサブネットを 4 つ追加](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. **[サブネットの追加]** ウィンドウで次の情報を指定します。

      * **Name**:サブネットの名前
      * **アドレス範囲 (CIDR ブロック)** :仮想ネットワークのサブネットの範囲 (CIDR 形式)

      ![サブネットの詳細を追加する](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 終了したら、 **[OK]** を選択します。

   1. さらに 3 つのサブネットについてこれらの手順を繰り返します。

      > [!NOTE]
      > 作成しようとしているサブネットが有効でない場合、Azure portal にメッセージが表示されますが、作業の進行は中断されません。

   サブネットの作成の詳細については、[仮想ネットワーク サブネットの追加](../virtual-network/virtual-network-manage-subnet.md)に関する記事を参照してください。

1. Azure で ISE 情報の検証が正常に完了したら、次の例のように **[作成]** を選択します。

   ![検証が完了したら、[作成] を選択](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure によって、環境のデプロイが開始されます。通常は、2 時間以内に完了します。 状況によっては、最大でデプロイに 4 時間かかる場合があります。 デプロイの状態を確認するには、Azure ツールバーで通知アイコンを選択します。これで通知ペインが開きます。

   ![展開の状態を確認する](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   デプロイが正常に完了すると、Azure に次の通知が表示されます。

   ![デプロイメント成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   表示されない場合は、デプロイのトラブルシューティングに関する Azure portal の手順に従います。

   > [!NOTE]
   > デプロイが失敗するか、ISE を削除する場合、サブネットがリリースされるまでに最長 1 時間 (まれにそれ以上) かかる可能性があります。 そのため、このようなサブネットを他の ISE で再利用できるようになるまで待たなければならない場合があります。
   >
   > 仮想ネットワークを削除すると、通常、サブネットがリリースされるまでに最長 2 時間かかる可能性があり、この操作にさらに時間がかかる場合があります。 
   > 仮想ネットワークを削除する場合は、まだ接続されているリソースがないことを確認してください。 
   > 「[仮想ネットワークの削除](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)」を参照してください。

1. デプロイの完了後に Azure で環境が自動的に表示されない場合は、環境を表示するために **[リソースに移動]** を選択します。

1. エンドポイント アクセスが "*外部*" の ISE については、ネットワーク セキュリティ グループをまだ用意していない場合、それを作成し、受信セキュリティ規則を追加し、管理されているコネクタ送信 IP アドレスからのトラフィックを許可する必要があります。 このルールを設定するには、次の手順に従います。

   1. ISE メニューの **[設定]** で、 **[プロパティ]** を選択します。

   1. **[コネクタ発信 IP アドレス]** でパブリック IP アドレス範囲をコピーします。これは制限と構成情報に関する記事の「[送信 IP アドレス](../logic-apps/logic-apps-limits-and-config.md#outbound)」にもあります。

   1. ネットワーク セキュリティ グループがまだ存在しない場合、それを作成します。
   
   1. 次の情報に基づいて、コピーしたパブリック送信 IP アドレスの受信セキュリティ規則を追加します。 詳細については、[Azure portal を使用してネットワーク セキュリティ グループでネットワーク トラフィックをフィルター処理する](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group)」を参照してください。

      | 目的 | ソース サービス タグまたは IP アドレス | ソース ポート | 宛先サービス タグまたは IP アドレス | 宛先ポート | Notes |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | コネクタ送信 IP アドレスからのトラフィックを許可する | <*connector-public-outbound-IP-addresses*> | * | 仮想ネットワークと ISE サブネットのアドレス空間 | * | |
      |||||||

1. ISE のネットワーク正常性を確認するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)に関する記事を参照してください。

   > [!CAUTION]
   > ISE のネットワークが異常になった場合、ISE によって使用されている内部 App Service Environment (ASE) も異常になることがあります。 ASE が異常な状態になったまま 7 日が過ぎると、ASE は停止されます。 この状態を解決するには、仮想ネットワークのセットアップを確認します。 見つかった問題を解決してから、ISE を再起動します。 そうしないと、90 日後に、停止されている ASE が削除され、ISE を使用できなくなります。 そのため、必要なトラフィックが許可されるよう ISE を正常な状態に保ってください。
   > 
   > 詳細については、以下のトピックを参照してください。
   >
   > * [Azure App Service の診断の概要](../app-service/overview-diagnostics.md)
   > * [Azure App Service Environment のメッセージ ログ](../app-service/environment/using-an-ase.md#logging)

1. ISE 内でロジック アプリやその他の成果物の作成を開始するには、「[統合サービス環境にリソースを追加する](../logic-apps/add-artifacts-integration-service-environment-ise.md)」を参照してください。

   > [!IMPORTANT]
   > ISE の作成後、マネージド ISE コネクタを使用できるようになりますが、それらはロジック アプリ デザイナーのコネクタ ピッカーに自動的に表示されません。 これらの ISE コネクタを使用するには、手動で[これらのコネクタを追加して ISE にデプロイ](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)し、ロジック アプリ デザイナーに表示されるようにする必要があります。

## <a name="next-steps"></a>次のステップ

* [統合サービス環境にリソースを追加する](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [統合サービス環境を管理する](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
