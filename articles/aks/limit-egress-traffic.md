---
title: Azure Kubernetes Service (AKS) でエグレス トラフィックを制限する
description: Azure Kubernetes Service (AKS) でエグレス トラフィックを制御するために必要なポートとアドレスについて説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/29/2019
ms.author: mlearned
ms.openlocfilehash: 3010973c7d0af784938e9295bb80fc22b7f718f3
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018639"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でクラスター ノードに対するエグレス トラフィックを制御する

既定で、AKS クラスターは、送信 (エグレス) インターネット アクセスが無制限です。 このレベルのネットワーク アクセスでは、実行しているノードやサービスから必要に応じて外部リソースにアクセスできます。 エグレス トラフィックを制限する場合は、正常なクラスター メンテナンス タスクを維持するために、アクセスできるポートとアドレスの数を制限する必要があります。 既定では、クラスターは、Microsoft Container Registry (MCR) または Azure Container Registry (ACR) のベース システム コンテナー イメージのみを使用するように構成されます。 これらの必要なポートとアドレスを許可するように、適切なファイアウォール規則とセキュリティ規則を構成します。

この記事では、AKS クラスターでエグレス トラフィックを制限する場合に必要なネットワーク ポートと完全修飾ドメイン名 (FQDN) について説明します。

> [!IMPORTANT]
> このドキュメントでは、AKS サブネットから出て行くトラフィックをロックダウンする方法についてのみ説明します。 AKS にはイングレス要件はありません。  ネットワーク セキュリティ グループ (NSGs) とファイアウォールを使用して内部サブネット トラフィックをブロックすることは、サポートされていません。 クラスター内のトラフィックを制御およびブロックするには、[ネットワーク ポリシー][network-policy]を使用します。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.66 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="egress-traffic-overview"></a>エグレス トラフィックの概要

管理と運用上の目的から、AKS クラスター内のノードは特定のポートと完全修飾ドメイン名 (FQDN) にアクセスする必要があります。 このようなアクションには、API サーバーと通信することや、コア Kubernetes クラスター コンポーネントとノード セキュリティの更新プログラムをダウンロードしてからインストールすることがあります。 既定では、エグレス (送信) インターネット トラフィックは AKS クラスター内のノードに制限されていません。 クラスターでは、外部リポジトリからベース システム コンテナー イメージをプルすることがあります。

AKS クラスターのセキュリティを強化するために、エグレス トラフィックを制限する場合があります。 クラスターは、MCR または ACR からベース システム コンテナー イメージをプルするように構成されています。 この方法でエグレス トラフィックをロック ダウンする場合は、AKS ノードが必要な外部サービスと正しく通信できるように、特定のポートと FQDN を定義します。 このような承認済みのポートと FQDN がない場合、AKS ノードは API サーバーと通信できず、コア コンポーネントをインストールできません。

[Azure Firewall][azure-firewall] またはサードパーティ製ファイアウォール アプライアンスを使用して、エグレス トラフィックをセキュリティで保護し、これらの必要なポートとアドレスを定義することができます。 AKS では、これらの規則は自動的に作成されません。 次のポートとアドレスは参照用であり、お使いのネットワーク ファイアウォールで適切な規則を作成する必要があります。

> [!IMPORTANT]
> Azure Firewall を使用してエグレス トラフィックを制限し、すべてのエグレス トラフィックを強制するユーザー定義ルート (UDR) を作成するときは、イグレス トラフィックを正しく許可するために、ファイアウォールで適切な DNAT 規則を作成する必要があります。 UDR で Azure Firewall を使用すると、非対称ルーティングによってイングレス設定が機能しなくなります (AKS サブネットにファイアウォールのプライベート IP アドレスに送信される既定のルートがあるのに、種類が LoadBalancer であるイングレスまたは Kubernetes サービスのパブリック ロード バランサーを使用している場合、この問題が発生します)。 この場合、ロード バランサーの受信トラフィックはパブリック IP アドレス経由で受信されますが、復路のパスはファイアウォールのプライベート IP アドレスを通過します。 ファイアウォールはステートフルであり、確立済みのセッションを認識しないため、返されるパケットは破棄されます。 Azure Firewall をイングレスまたはサービスのロード バランサーと統合する方法については、「[Azure Firewall と Azure Standard Load Balancer を統合する](https://docs.microsoft.com/azure/firewall/integrate-lb)」を参照してください。
> エグレス ワーカー ノード IP と API サーバーの IP の間のネットワーク規則を使用して、TCP ポート9000 と TCP ポート 22 のトラフィックをロックダウンすることができます。

AKS には、2 組のポートとアドレスがあります。

* 「[AKS クラスターの必要なポートとアドレス](#required-ports-and-addresses-for-aks-clusters)」では、承認されるエグレス トラフィックの最小要件について詳しく説明しています。
* 「[AKS クラスターの省略可能な推奨されるアドレスとポート](#optional-recommended-addresses-and-ports-for-aks-clusters)」は、すべてのシナリオに必要ではなく、Azure Monitor など、他のサービスとの統合は正しく機能しません。 この省略可能なポートと FQDN の一覧を確認し、AKS クラスターに使用されるサービスとコンポーネントをすべて承認します。

> [!NOTE]
> エグレス トラフィックの制限は、新しい AKS クラスターでのみ機能します。 既存のクラスターの場合は、エグレス トラフィックを制限する前に、`az aks upgrade` コマンドを使用して[クラスターのアップグレード操作を実行][aks-upgrade]します。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS クラスターの必要なポートとアドレス

AKS クラスターには、次の送信ポート/ネットワーク規則が必要です。

* TCP ポート *443*
* API サーバーと通信する必要があるアプリがある場合は、TCP [IPAddrOfYourAPIServer]:443 が必要です。  この変更は、クラスターの作成後に設定できます。
* トンネル フロント ポッドが API サーバー上のトンネルの終端と通信するための TCP ポート *9000* と TCP ポート *22*。
    * より具体的な情報を得るには、次の表の * *.hcp.\<location\>.azmk8s.io* アドレスと * *.tun.\<location\>.azmk8s.io* アドレスを参照してください。
* API サーバーに直接アクセスするポッドがある場合は、DNS に対する UDP ポート *53* も必要です。

次の FQDN/アプリケーション規則が必要です。

| FQDN                       | Port      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS: 443、TCP: 22、TCP: 9000 | このアドレスは API サーバー エンドポイントです。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。 |
| *.tun.\<location\>.azmk8s.io | HTTPS: 443、TCP: 22、TCP: 9000 | このアドレスは API サーバー エンドポイントです。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。 |
| aksrepos.azurecr.io        | HTTPS: 443 | このアドレスは、Azure Container Registry (ACR) 内のイメージにアクセスするために必要です。 このレジストリには、クラスターのアップグレード時およびスケール時にクラスターの機能に必要なサードパーティのイメージ/グラフ (メトリックサーバー、コア DNS など) が含まれています。|
| *.blob.core.windows.net    | HTTPS: 443 | このアドレスは ACR に保存されているイメージのバックエンド ストアです。 |
| mcr.microsoft.com          | HTTPS: 443 | このアドレスは、Microsoft Container Registry (MCR) のイメージにアクセスするために必要です。 このレジストリには、クラスターのアップグレード時およびスケール時にクラスターの機能に必要なファーストパーティのイメージ/グラフ (moby など) が含まれています。 |
| *.cdn.mscr.io              | HTTPS: 443 | このアドレスは、Azure Content Delivery Network (CDN) によってサポートされる MCR ストレージに必要です。 |
| management.azure.com       | HTTPS: 443 | このアドレスは、Kubernetes の GET/PUT 操作に必要です。 |
| login.microsoftonline.com  | HTTPS: 443 | このアドレスは Azure Active Directory 認証に必要です。 |
| ntp.ubuntu.com             | UDP: 123   | このアドレスは、Linux ノード上での NTP 時刻同期に必要です。 |
| packages.microsoft.com     | HTTPS: 443 | このアドレスは、キャッシュされた *apt-get* 操作に使用される Microsoft パッケージ リポジトリです。  パッケージの例としては、Moby、PowerShell、Azure CLI などがあります。 |
| acs-mirror.azureedge.net   | HTTPS: 443 | このアドレスは、kubernet や Azure CNI などの必要なバイナリをインストールするために必要なリポジトリ用です。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS クラスターの省略可能な推奨されるアドレスとポート

AKS クラスターの場合、次の送信ポート/ネットワーク規則は任意です。

AKS クラスターが正常に機能するためには、以下の FQDN/アプリケーション規則が推奨されます。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com、azure.archive.ubuntu.com、changelogs.ubuntu.com | HTTP: 80   | このアドレスを使用すると、Linux クラスター ノードから必要なセキュリティ パッチと更新プログラムをダウンロードできます。 |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>GPU が有効な AKS クラスターに必要なアドレスとポート

GPU が有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS: 443 | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |
| us.download.nvidia.com | HTTPS: 443 | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |
| apt.dockerproject.org | HTTPS: 443 | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Azure Monitor for containers が有効になっている場合に必要なアドレスとポート

Azure Monitor for containers 有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS: 443  | これは、Azure Monitor を使用する適切なメトリックと監視テレメトリ用です。 |
| *.ods.opinsights.azure.com    | HTTPS: 443 | これは、ログ分析データを取り込むために Azure Monitor によって使用されます。 |
| *.oms.opinsights.azure.com | HTTPS: 443 | このアドレスは、ログ分析サービスの認証に使用される omsagent によって使用されます。 |
|*.microsoftonline.com | HTTPS: 443 | これは、Azure Monitor に対する認証とメトリックの送信に使用されます。 |
|*.monitoring.azure.com | HTTPS: 443 | これは、メトリック データを Azure Monitor に送信するために使用されます。 |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Azure Policy (パブリック プレビュー) が有効な AKS クラスターに必要なアドレスとポート

> [!CAUTION]
> 以下の一部の機能はプレビュー段階です。  この記事の推奨事項は、機能がパブリック プレビューおよび将来のリリース段階に移行するときに、変更される可能性があります。

Azure Policy が有効になっている AKS クラスターの場合、次の FQDN/アプリケーション規則が必要です。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | このアドレスは、Azure Policy の適切な操作のために使用されます。 (現在 AKS でプレビュー段階) |
| raw.githubusercontent.com | HTTPS: 443 | このアドレスは、Azure Policy の正しい動作のために組み込みポリシーを GitHub から取得するために使用されます。 (現在 AKS でプレビュー段階) |
| *.gk.<location>.azmk8s.io | HTTPS: 443 | Azure Policy アドオンは、マスター サーバーで実行されている Gatekeeper 監査エンドポイントと通信して、監査結果を取得します。 |
| dc.services.visualstudio.com | HTTPS: 443 | Azure Policy アドオンは、テレメトリ データを Application Insights エンドポイントに送信します。 |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Windows Server ベースのノード (パブリック プレビュー) が有効な場合に必要

> [!CAUTION]
> 以下の一部の機能はプレビュー段階です。  この記事の推奨事項は、機能がパブリック プレビューおよび将来のリリース段階に移行するときに、変更される可能性があります。

次の FQDN/アプリケーション規則は Windows Server ベースの AKS クラスターの場合に必要です。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net、winlayers.blob.core.windows.net、winlayers.cdn.mscr.io、go.microsoft.com | HTTPS: 443 | Windows 関連のバイナリをインストールするため |
| mp.microsoft.com、www<span></span>.msftconnecttest.com、ctldl.windowsupdate.com | HTTP: 80 | Windows 関連のバイナリをインストールするため |
| kms.core.windows.net | TCP: 1688 | Windows 関連のバイナリをインストールするため |


## <a name="next-steps"></a>次の手順

この記事では、クラスターのエグレス トラフィックを制限する場合に許可するポートとアドレスについて学習しました。 また、ポッド自体が通信する方法と、それらのクラスター内の制限を定義することもできます。 詳細については、[AKS のネットワーク ポリシーを使用したポッド間のトラフィックの保護][network-policy]に関する記事を参照してください。

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
