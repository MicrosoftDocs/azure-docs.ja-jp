---
title: Azure Kubernetes Service (AKS) でエグレス トラフィックを制限する
description: Azure Kubernetes Service (AKS) でエグレス トラフィックを制御するために必要なポートとアドレスについて説明します。
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 9e65e2736578ce04dfa79d5a7827e190d47fb312
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573831"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でクラスター ノードに対するエグレス トラフィックを制御する

この記事では、Azure Kubernetes Service (AKS) からの送信トラフィックをセキュリティで保護するために必要な詳細情報について説明します。 基になる AKS のデプロイのクラスター要件と、オプションのアドオンと機能に対する追加要件が含まれています。 [Azure Firewall でこれらの要件を構成する方法の終わりには、例を示してあります](#restrict-egress-traffic-using-azure-firewall)。 ただし、この情報は任意の送信制限の方法またはアプライアンスに適用できます。

## <a name="background"></a>バックグラウンド

AKS クラスターは仮想ネットワークにデプロイされます。 このネットワークは、(AKS によって作成された) マネージドでも、(ユーザーによって事前に構成された) カスタムでもかまいません。 どちらの場合も、クラスターには、その仮想ネットワークの外部にあるサービスに対する **送信** 依存関係があります (サービスには受信依存関係はありません)。

管理と運用上の目的から、AKS クラスター内のノードは特定のポートと完全修飾ドメイン名 (FQDN) にアクセスする必要があります。 これらのエンドポイントは、ノードが API サーバーと通信するため、またはコア Kubernetes クラスター コンポーネントとノードのセキュリティ更新プログラムをダウンロードしてからインストールするために必要です。 たとえば、クラスターでは Microsoft Container Registry (MCR) から基本システムのコンテナー イメージをプルする必要があります。

AKS の送信依存関係は、ほぼすべて、背後に静的アドレスがない FQDN を使用して定義されています。 静的アドレスがないということは、ネットワーク セキュリティ グループを使用して AKS クラスターからの送信トラフィックをロック ダウンできないことを意味します。 

既定で、AKS クラスターは、送信 (エグレス) インターネット アクセスが無制限です。 このレベルのネットワーク アクセスでは、実行しているノードやサービスから必要に応じて外部リソースにアクセスできます。 エグレス トラフィックを制限する場合は、正常なクラスター メンテナンス タスクを維持するために、アクセスできるポートとアドレスの数を制限する必要があります。 送信アドレスをセキュリティで保護する最も簡単なソリューションは、ドメイン名に基づいて送信トラフィックを制御できるファイアウォール デバイスを使用することです。 たとえば、Azure Firewall では、送信先の FQDN に基づいて HTTP と HTTPS の送信トラフィックを制限できます。 また、適切なファイアウォール規則とセキュリティ規則を構成し、これらの必要なポートとアドレスを許可することができます。

> [!IMPORTANT]
> このドキュメントでは、AKS サブネットから出て行くトラフィックをロックダウンする方法についてのみ説明します。 既定では、AKS にはイングレス要件はありません。  ネットワーク セキュリティ グループ (NSG) とファイアウォールを使用して **内部サブネット トラフィック** をブロックすることは、サポートされていません。 クラスター内のトラフィックを制御およびブロックするには、 "[**_ネットワーク ポリシー_**][network-policy]" を使用します。

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>AKS クラスターに必要な送信ネットワーク規則と FQDN

AKS クラスターには、次のネットワーク規則と FQDN およびアプリケーションの規則が必要です。Azure Firewall 以外のソリューションを構成する場合は、これらの規則を使用できます。

* IP アドレスの依存関係が HTTP/S 以外のトラフィック (TCP トラフィックと UDP トラフィックの両方) に対応しています
* FQDN HTTP/HTTPS エンドポイントは、ファイアウォール デバイスに配置することができます。
* HTTP と HTTPS のワイルドカード エンドポイントは、いくつかの修飾子に基づき、AKS クラスターによって異なる場合がある依存関係です。
* AKS では、アドミッション コントローラーを使用して、kube-system と gatekeeper-system の下にあるすべてのデプロイに対し、FQDN が環境変数として挿入されます。これにより、ノードと API サーバー間のすべてのシステム通信において、API サーバーの IP ではなく、API サーバーの FQDN が使用されるようになります。 
* API サーバーと通信する必要があるアプリまたはソリューションがある場合は、ネットワーク規則を **追加** して、"*API サーバーの IP のポート 443 への TCP 通信* を許可する必要があります。
* まれに、メンテナンスが行われると、API サーバーの IP が変わる可能性があります。 API サーバーの IP が変わる可能性がある計画メンテナンス操作は、常に事前に通知されます。


### <a name="azure-global-required-network-rules"></a>Azure Global に必要なネットワーク規則

必要なネットワーク規則と IP アドレスの依存関係は次のとおりです。

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 これは、[プライベート クラスター](private-clusters.md)では必要ありません|
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 これは、[プライベート クラスター](private-clusters.md)では必要ありません |
| **`*:123`** または **`ntp.ubuntu.com:123`** (Azure Firewall ネットワーク規則を使用している場合)  | UDP      | 123     | Linux ノードでのネットワーク タイム プロトコル (NTP) の時刻同期に必要です。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | カスタム DNS サーバーを使用している場合は、クラスター ノードからそれらにアクセスできることを確認する必要があります。 |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API サーバーにアクセスするポッドとデプロイを実行する場合に必要です。それらのポッドとデプロイでは API IP が使用されます。 これは、[プライベート クラスター](private-clusters.md)では必要ありません  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure Global に必要な FQDN とアプリケーションの規則 

次の FQDN/アプリケーション規則が必要です。

| 送信先 FQDN                 | Port            | 用途      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | ノードと API サーバーの間の通信に必要です。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。 |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Microsoft Container Registry (MCR) のイメージにアクセスするために必要です。 このレジストリには、ファーストパーティのイメージとグラフ (coreDNS など) が含まれます。 これらのイメージは、スケーリング操作やアップグレード操作など、クラスターの適切な作成と機能のために必要です。  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Azure Content Delivery Network (CDN) によってサポートされる MCR ストレージに必要です。 |
| **`management.azure.com`**       | **`HTTPS:443`** | Azure API に対する Kubernetes の操作に必要です。 |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Azure Active Directory の認証に必要です。 |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | このアドレスは、キャッシュされた *apt-get* 操作に使用される Microsoft パッケージ リポジトリです。  パッケージの例としては、Moby、PowerShell、Azure CLI などがあります。 |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | このアドレスは、kubernet や Azure CNI などの必要なバイナリをダウンロードしてインストールするために必要なリポジトリ用です。 |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure China 21Vianet で必要なネットワーク規則

必要なネットワーク規則と IP アドレスの依存関係は次のとおりです。

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.Region:1194`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 |
| **`*:22`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:22`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:22`** <br/> *Or* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 |
| **`*:123`** または **`ntp.ubuntu.com:123`** (Azure Firewall ネットワーク規則を使用している場合)  | UDP      | 123     | Linux ノードでのネットワーク タイム プロトコル (NTP) の時刻同期に必要です。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | カスタム DNS サーバーを使用している場合は、クラスター ノードからそれらにアクセスできることを確認する必要があります。 |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API サーバーにアクセスするポッドとデプロイを実行する場合に必要です。それらのポッドとデプロイでは API IP が使用されます。  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet に必要な FQDN とアプリケーションの規則

次の FQDN/アプリケーション規則が必要です。

| 送信先 FQDN                               | Port            | 用途      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | ノードと API サーバーの間の通信に必要です。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。 |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | ノードと API サーバーの間の通信に必要です。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。 |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Microsoft Container Registry (MCR) のイメージにアクセスするために必要です。 このレジストリには、ファーストパーティのイメージとグラフ (coreDNS など) が含まれます。 これらのイメージは、スケーリング操作やアップグレード操作など、クラスターの適切な作成と機能のために必要です。 |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Azure Content Delivery Network (CDN) によってサポートされる MCR ストレージに必要です。 |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Azure API に対する Kubernetes の操作に必要です。 |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Azure Active Directory の認証に必要です。 |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | このアドレスは、キャッシュされた *apt-get* 操作に使用される Microsoft パッケージ リポジトリです。  パッケージの例としては、Moby、PowerShell、Azure CLI などがあります。 |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | このアドレスは、kubernet や Azure CNI などの必要なバイナリをダウンロードしてインストールするために必要なリポジトリ用です。 |

### <a name="azure-us-government-required-network-rules"></a>Azure US Government に必要なネットワーク規則

必要なネットワーク規則と IP アドレスの依存関係は次のとおりです。

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 |
| **`*:123`** または **`ntp.ubuntu.com:123`** (Azure Firewall ネットワーク規則を使用している場合)  | UDP      | 123     | Linux ノードでのネットワーク タイム プロトコル (NTP) の時刻同期に必要です。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | カスタム DNS サーバーを使用している場合は、クラスター ノードからそれらにアクセスできることを確認する必要があります。 |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API サーバーにアクセスするポッドとデプロイを実行する場合に必要です。それらのポッドとデプロイでは API IP が使用されます。  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure US Government に必要な FQDN とアプリケーションの規則 

次の FQDN/アプリケーション規則が必要です。

| 送信先 FQDN                                        | Port            | 用途      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | ノードと API サーバーの間の通信に必要です。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Microsoft Container Registry (MCR) のイメージにアクセスするために必要です。 このレジストリには、ファーストパーティのイメージとグラフ (coreDNS など) が含まれます。 これらのイメージは、スケーリング操作やアップグレード操作など、クラスターの適切な作成と機能のために必要です。 |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Azure Content Delivery Network (CDN) によってサポートされる MCR ストレージに必要です。 |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Azure API に対する Kubernetes の操作に必要です。 |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Azure Active Directory の認証に必要です。 |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | このアドレスは、キャッシュされた *apt-get* 操作に使用される Microsoft パッケージ リポジトリです。  パッケージの例としては、Moby、PowerShell、Azure CLI などがあります。 |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | このアドレスは、kubernet や Azure CNI などの必要なバイナリをインストールするために必要なリポジトリ用です。 |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>AKS クラスターに対して推奨される省略可能な FQDN とアプリケーションの規則

以下の FQDN とアプリケーションの規則は省略可能ですが、AKS クラスターに対しては推奨されます。

| 送信先 FQDN                                                               | Port          | 用途      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | このアドレスを使用すると、Linux クラスター ノードから必要なセキュリティ パッチと更新プログラムをダウンロードできます。 |

これらの FQDN をブロックして許可しない場合、[ノード イメージのアップグレード](node-image-upgrade.md)または[クラスターのアップグレード](upgrade-cluster.md)を行うと、ノードは OS の更新プログラムのみを受け取ります。

## <a name="gpu-enabled-aks-clusters"></a>GPU が有効な AKS クラスター

### <a name="required-fqdn--application-rules"></a>必要な FQDN とアプリケーションの規則

GPU が有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| 送信先 FQDN                        | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |

## <a name="windows-server-based-node-pools"></a>Windows Server ベースのノード プール 

### <a name="required-fqdn--application-rules"></a>必要な FQDN とアプリケーションの規則

次の FQDN またはアプリケーション規則が、Windows Server ベースのノード プールを使用する場合に必要です。

| 送信先 FQDN                                                           | Port      | 用途      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Windows 関連のバイナリをインストールするため |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Windows 関連のバイナリをインストールするため |

## <a name="aks-addons-and-integrations"></a>AKS のアドオンと統合

### <a name="azure-monitor-for-containers"></a>コンテナーに対する Azure Monitor

Azure Monitor for containers へのアクセスを提供するには 2 つのオプションがあります。Azure Monitor の [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) を許可するか、**または**、必要な FQDN とアプリケーションの規則へのアクセスを提供することができます。

#### <a name="required-network-rules"></a>必要なネットワーク規則

次の FQDN/アプリケーション規則が必要です。

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureMonitor:443`**  | TCP           | 443      | このエンドポイントは、メトリック データとログを Azure Monitor および Log Analytics に送信するために使用されます。 |

#### <a name="required-fqdn--application-rules"></a>必要な FQDN とアプリケーションの規則

Azure Monitor for containers 有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | このエンドポイントは、Azure Monitor を使用するメトリックと監視テレメトリに使用されます。 |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | このエンドポイントは、ログ分析データを取り込むために Azure Monitor によって使用されます。 |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | このエンドポイントは、ログ分析サービスの認証に使用される omsagent によって使用されます。 |
| *.monitoring.azure.com | **`HTTPS:443`** | このエンドポイントは、メトリック データを Azure Monitor に送信するために使用されます。 |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

以下のすべての FQDN と [Azure Dev Spaces インフラストラクチャ サービス][dev-spaces-service-tags]との間のネットワーク トラフィックを許可するように、ファイアウォールまたはセキュリティ構成を更新します。

#### <a name="required-network-rules"></a>必要なネットワーク規則

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureDevSpaces`**  | TCP           | 443      | このエンドポイントは、メトリック データとログを Azure Monitor および Log Analytics に送信するために使用されます。 |

#### <a name="required-fqdn--application-rules"></a>必要な FQDN とアプリケーションの規則 

Azure Dev Spaces が有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | このアドレスは、linux alpine やその他の Azure Dev Spaces イメージをプルするために使用されます。 |
| `gcr.io` | **`HTTPS:443`** | このアドレスは、helm/tiller イメージをプルするために使用されます。 |
| `storage.googleapis.com` | **`HTTPS:443`** | このアドレスは、helm/tiller イメージをプルするために使用されます。 |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>必要な FQDN とアプリケーションの規則 

Azure Policy が有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                          | Port      | 用途      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | このアドレスは、Kubernetes ポリシーをプルし、クラスターのコンプライアンス状態をポリシー サービスにレポートするために使用されます。 |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | このアドレスは、組み込みポリシーの Gatekeeper アーティファクトをプルするために使用されます。 |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | このアドレスは、Azure Policy の適切な操作のために使用されます。  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | このアドレスは、Azure Policy の正しい動作のために組み込みポリシーを GitHub から取得するために使用されます。 |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | テレメトリ データを Application Insights エンドポイントに送信するAzure Policy アドオン。 |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet に必要な FQDN とアプリケーションの規則 

Azure Policy が有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                          | Port      | 用途      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | このアドレスは、Kubernetes ポリシーをプルし、クラスターのコンプライアンス状態をポリシー サービスにレポートするために使用されます。 |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | このアドレスは、組み込みポリシーの Gatekeeper アーティファクトをプルするために使用されます。 |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure US Government に必要な FQDN とアプリケーションの規則

Azure Policy が有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                          | Port      | 用途      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | このアドレスは、Kubernetes ポリシーをプルし、クラスターのコンプライアンス状態をポリシー サービスにレポートするために使用されます。 |
| **`store.policy.azure.us`** | **`HTTPS:443`** | このアドレスは、組み込みポリシーの Gatekeeper アーティファクトをプルするために使用されます。 |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Azure Firewall を使用してエグレス トラフィックを制限する

Azure Firewall では、この構成を簡略化するための Azure Kubernetes Service (`AzureKubernetesService`) FQDN タグが提供されています。 

> [!NOTE]
> FQDN タグには上記の FQDN がすべて含まれており、自動的に最新の状態に維持されます。
>
> 運用環境のシナリオで SNAT ポートの消費の問題が発生しないようにするには、Azure Firewall に少なくとも 20 のフロントエンド IP を用意することをお勧めします。

デプロイのアーキテクチャの例を次に示します。

![ロック ダウンされたトポロジ](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* パブリック イングレスは、ファイアウォール フィルターを経由することが強制されます
  * AKS エージェント ノードは、専用サブネットに分離されます。
  * [Azure Firewall](../firewall/overview.md) は、独自のサブネットにデプロイされます。
  * DNAT 規則では、FW パブリック IP が LB フロントエンド IP に変換されます。
* 送信要求は、[ユーザー定義ルート](egress-outboundtype.md)を使用して、エージェント ノードから Azure Firewall 内部 IP に対して開始されます
  * AKS エージェント ノードからの要求は、AKS クラスターがデプロイされたサブネットに設定されている UDR に従います。
  * Azure Firewall は、パブリック IP フロントエンドから仮想ネットワークを出ます
  * パブリック インターネットまたはその他の Azure サービスへのアクセスは、ファイアウォール フロントエンド IP アドレスを送信先と送信元として行われます。
  * AKS コントロール プレーンへのアクセスは、必要に応じて、[API サーバーの許可された IP 範囲](./api-server-authorized-ip-ranges.md)により保護されます。これには、ファイアウォールのパブリック フロントエンド IP アドレスが含まれます。
* 内部トラフィック
  * 必要に応じて、[パブリック ロード バランサー](load-balancer-standard.md)の代わりに、またはそれに加えて、内部トラフィックに[内部ロード バランサー](internal-lb.md)を使用することもできます。これも、独自のサブネットで分離できます。


以下の手順では、Azure Firewall の `AzureKubernetesService` FQDN タグを使用して、AKS クラスターからの送信トラフィックを制限し、ファイアウォール経由のパブリック受信トラフィックを構成する方法の例を示します。


### <a name="set-configuration-via-environment-variables"></a>環境変数を使用して構成を設定する

リソースの作成に使用する一連の環境変数を定義します。

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>複数のサブネットを含んだ仮想ネットワークを作成する

クラスター用とファイアウォール用の 2 つのサブネットを持つ仮想ネットワークをプロビジョニングします。 必要に応じて、内部サービス イングレス用のものを作成することもできます。

![空のネットワーク トポロジ](media/limit-egress-traffic/empty-network.png)

すべてのリソースを保持するリソース グループを作成します。

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

AKS クラスターと Azure Firewall をホストするための 2 つのサブネットを含む仮想ネットワークを作成します。 それぞれに独自のサブネットがあります。 AKS ネットワークから始めましょう。

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>UDR を使用する Azure ファイアウォールの作成と設定

Azure Firewall の受信および送信規則を構成する必要があります。 ファイアウォールの主な目的は、組織が AKS クラスターに対してきめ細かなイングレスおよびエグレス トラフィック規則を設定できるようにすることです。

![ファイアウォールと UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> クラスターまたはアプリケーションにより、同じ送信先または送信先の小さいサブセットに対して多数の送信接続が作成される場合、フロントエンド IP あたりのポート数の上限に達するのを防ぐため、より多くのファイアウォール フロントエンド IP が必要になることがあります。
> 複数の IP を持つ Azure ファイアウォールを作成する方法の詳細については、「[**こちら**](../firewall/quick-create-multiple-ip-template.md)」を参照してください

Azure Firewall フロントエンド アドレスとして使用される Standard SKU のパブリック IP リソースを作成します。

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Azure ファイアウォールを作成するには、プレビューの cli 拡張機能を登録します。
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
これで、以前に作成した IP アドレスをファイアウォール フロントエンドに割り当てることができるようになります。

> [!NOTE]
> Azure Firewall へのパブリック IP アドレスの設定には数分かかる場合があります。
> ネットワーク規則で FQDN を利用するには、DNS プロキシを有効にする必要があります。有効にすると、ファイアウォールではポート 53 でリッスンが行われ、上で指定した DNS サーバーに DNS 要求が転送されます。 これにより、ファイアウォールでその FQDN を自動的に変換できるようになります。

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

前のコマンドが正常に完了したら、後で構成できるようにファイアウォールのフロントエンド IP アドレスを保存します。

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> [承認済み IP アドレス範囲](./api-server-authorized-ip-ranges.md)で AKS API サーバーへのセキュリティで保護されたアクセスを使用する場合は、承認された IP 範囲にファイアウォール パブリック IP を追加する必要があります。

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Azure Firewall へのホップがある UDR を作成する

Azure では、Azure のサブネット、仮想ネットワーク、およびオンプレミスのネットワーク間のトラフィックが自動的にルーティングされます。 Azure の既定のルーティングを変更する場合は、ルート テーブルを作成して変更します。

特定のサブネットに関連付ける空のルート テーブルを作成します。 ルート テーブルには、次のホップを以前の手順で作成した Azure Firewall と定義します。 各サブネットには、0 個または 1 個のルート テーブルを関連付けることができます。

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Azure の既定のシステム ルートをオーバーライドする方法、またはサブネットのルート テーブルに新しいルートを追加する方法については、[仮想ネットワークのルート テーブルに関するドキュメント](../virtual-network/virtual-networks-udr-overview.md#user-defined)を参照してください。

### <a name="adding-firewall-rules"></a>ファイアウォール規則の追加

次に示すのは、ファイアウォールでの構成に使用できる 3 つのネットワーク規則であり、デプロイに基づいてこれらの規則を調整することが必要な場合があります。 最初の規則では、TCP 経由でのポート 9000 へのアクセスが許可されます。 2 番目の規則では、UDP 経由でのポート 1194 と 123 へのアクセスが許可されます (Azure China 21Vianet にデプロイする場合は、[さらに](#azure-china-21vianet-required-network-rules)必要になる場合があります)。 どちらの規則でも、使用している Azure リージョン CIDR (この場合は米国東部) 宛てのトラフィックのみが許可されます。 最後に、UDP 経由で `ntp.ubuntu.com` FQDN に対してポート 123 を開く 3 番目のネットワーク規則を追加します (ネットワーク規則として FQDN を追加することは、Azure Firewall 固有の機能の 1 つであり、独自のオプションを使用する場合はそれを調整する必要があります)。

ネットワーク規則を設定した後、TCP ポート 443 およびポート 80 を通してアクセスできるすべての必要な FQDN を対象とするアプリケーション規則も、`AzureKubernetesService` を使用して追加します。

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Azure Firewall サービスの詳細については、[Azure Firewall のドキュメント](../firewall/overview.md)を参照してください。

### <a name="associate-the-route-table-to-aks"></a>ルート テーブルを AKS に関連付ける

クラスターをファイアウォールに関連付けるには、クラスターのサブネットの専用サブネットから、以前の手順で作成したルート テーブルを参照する必要があります。 関連付けを行うには、クラスターとファイアウォールの両方を保持する仮想ネットワークに対して、クラスターのサブネットのルート テーブルを更新するコマンドを発行します。

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>UDR の送信の種類を使用して AKS を既存のネットワークにデプロイする

これで、AKS クラスターを既存の仮想ネットワークにデプロイできるようになりました。 また、[送信の種類 `userDefinedRouting`](egress-outboundtype.md) も使用します。この機能により、すべての送信トラフィックがファイアウォールの通過を強制され、他のエグレス パスが存在しないことが保証されます (既定では、Load Balancer の送信の種類を使用できます)。

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>既存の仮想ネットワーク内にプロビジョニングできるアクセス権を持つサービス プリンシパルを作成する

クラスター ID (マネージド ID またはサービス プリンシパル) は、クラスター リソースを作成するために AKS によって使用されます。 作成時に渡されるサービス プリンシパルは、AKS によって使用されるストレージ リソース、IP、ロード バランサーなどの基になる AKS リソースを作成するために使用されます (代わりに[マネージド ID](use-managed-identity.md) を使用することもできます)。 以下の適切なアクセス許可が付与されていない場合、AKS クラスターをプロビジョニングすることはできません。

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

次に、以下の `APPID` と `PASSWORD` を、前のコマンド出力で自動生成されたサービス プリンシパル appid とサービス プリンシパル パスワードに置き換えます。 VNET リソース ID を参照してサービス プリンシパルにアクセス許可を付与し、AKS によってリソースをデプロイできるようにします。

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

必要なアクセス許可の詳細については、[こちら](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)で確認できます。

> [!NOTE]
> Kubernet ネットワーク プラグインを使用している場合、Kubernet では必要なルーティング規則を追加するためにルート テーブルが必要であるため、事前に作成されているルート テーブルへのアクセス許可を、AKS サービス プリンシパルまたはマネージド ID に付与する必要があります。 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>AKS をデプロイする

最後に、クラスター専用にした既存のサブネットに AKS クラスターをデプロイすることができます。 デプロイ先のターゲット サブネットは、環境変数 `$SUBNETID` で定義します。 前の手順では `$SUBNETID` 変数を定義しませんでした。 サブネット ID の値を設定するには、次のコマンドを使用します。

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

サブネット上に既に存在する UDR を使用するように、送信の種類を定義します。 この構成により、AKS ではロード バランサーに対するセットアップと IP のプロビジョニングをスキップできるようになります。

> [!IMPORTANT]
> 制限など、送信の種類 UDR の詳細については、[**エグレス送信の種類 UDR**](egress-outboundtype.md#limitations)に関する記事を参照してください。


> [!TIP]
> [**プライベート クラスター**](private-clusters.md)などの新しい機能をクラスターのデプロイに追加できます。 
>
> [**API サーバーの許可された IP 範囲**](api-server-authorized-ip-ranges.md)に対して AKS 機能を追加し、API サーバーのアクセスをファイアウォールのパブリック エンドポイントのみに制限できます。 許可された IP 範囲の機能は、図ではオプションとして示されています。 許可された IP 範囲機能を有効にして API サーバーへのアクセスを制限する場合、開発者ツールでファイアウォールの仮想ネットワークからのジャンプボックスを使用するか、すべての開発者エンドポイントを許可された IP 範囲に追加する必要があります。

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>開発者の API サーバーへのアクセスを有効にする

前のステップでクラスターに対して許可された IP 範囲を使用した場合、それらから API サーバーにアクセスするには、許可された IP 範囲の AKS クラスター一覧に開発者ツールの IP アドレスを追加する必要があります。 もう 1 つの方法は、ファイアウォールの仮想ネットワーク内の別のサブネット内に必要なツールを使用してジャンプボックスを構成することです。

次のコマンドを使用して、許可された範囲にもう 1 つの IP アドレスを追加します

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 新しく作成された Kubernetes クラスターに接続するように、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して `kubectl` を構成します。 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>パブリック サービスをデプロイする
サービスの公開と、このクラスターへのアプリケーションのデプロイを始めることができます。 この例では、パブリック サービスを公開しますが、[内部ロード バランサー](internal-lb.md)を介して内部サービスを公開することもできます。

![パブリック サービスの DNAT](media/limit-egress-traffic/aks-create-svc.png)

以下の yaml を `example.yaml` という名前のファイルにコピーして、Azure 投票アプリケーションをデプロイします。

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

次を実行してサービスをデプロイします。

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Azure Firewall に DNAT 規則を追加する

> [!IMPORTANT]
> Azure Firewall を使用してエグレス トラフィックを制限し、すべてのエグレス トラフィックを強制するユーザー定義ルート (UDR) を作成するときは、イグレス トラフィックを正しく許可するために、ファイアウォールで適切な DNAT 規則を作成する必要があります。 UDR で Azure Firewall を使用すると、非対称ルーティングによってイングレス設定が機能しなくなります (AKS サブネットにファイアウォールのプライベート IP アドレスに送信される既定のルートがあるのに、種類が LoadBalancer であるイングレスまたは Kubernetes サービスのパブリック ロード バランサーを使用している場合、この問題が発生します)。 この場合、ロード バランサーの受信トラフィックはパブリック IP アドレス経由で受信されますが、復路のパスはファイアウォールのプライベート IP アドレスを通過します。 ファイアウォールはステートフルであり、確立済みのセッションを認識しないため、返されるパケットは破棄されます。 Azure Firewall をイングレスまたはサービスのロード バランサーと統合する方法については、「[Azure Firewall と Azure Standard Load Balancer を統合する](../firewall/integrate-lb.md)」を参照してください。


受信接続を構成するには、DNAT 規則を Azure ファイアウォールに書き込む必要があります。 クラスターへの接続をテストするために、内部サービスによって公開されている内部 IP にルーティングされるように、ファイアウォール フロントエンド パブリック IP アドレスの規則を定義します。

送信先アドレスは、アクセス先のファイアウォールのポートなので、カスタマイズできます。 変換されたアドレスは、内部ロード バランサーの IP アドレスである必要があります。 変換されたポートは、Kubernetes サービスの公開ポートである必要があります。

Kubernetes サービスによって作成されたロード バランサーに割り当てられた内部 IP アドレスを指定する必要があります。 次を実行してアドレスを取得します。

```bash
kubectl get services
```

必要な IP アドレスは、次のように、EXTERNAL-IP 列に表示されます。

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

以下を実行して、サービス IP を取得します。
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

以下を実行して、NAT 規則を追加します。
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>接続の検証

ブラウザーで Azure Firewall フロントエンド IP アドレスに移動して、接続を検証します。

AKS 投票アプリが表示されます。 この例では、ファイアウォールのパブリック IP は `52.253.228.132` でした。


![[Cats]、[Dogs]、[Reset] のボタンと合計値が表示されている A K S 投票アプリを示すスクリーンショット。](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースをクリーンアップするには、AKS リソース グループを削除します。

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>次のステップ

この記事では、クラスターのエグレス トラフィックを制限する場合に許可するポートとアドレスについて学習しました。 また、Azure Firewall を使用して送信トラフィックをセキュリティで保護する方法についても説明しました。 

必要な場合は、[送信の種類 `userDefinedRoute` のドキュメント](egress-outboundtype.md)に従い、上記の手順を一般化して、好みのエグレス ソリューションにトラフィックを転送できます。

ポッド間の通信方法の制限、およびクラスター内の East-West トラフィックの制限については、「[Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護][network-policy]」を参照してください。

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
