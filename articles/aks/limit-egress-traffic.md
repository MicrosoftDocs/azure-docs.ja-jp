---
title: Azure Kubernetes Service (AKS) でエグレス トラフィックを制限する
description: Azure Kubernetes Service (AKS) でエグレス トラフィックを制御するために必要なポートとアドレスについて説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 12922496bc97ad51d1cc96f7ffe8df05c1fd66ea
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614958"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) でクラスター ノードのエグレス トラフィックを制限し、必要なポートとサービスへのアクセスを制御する

既定で、AKS クラスターは、送信 (エグレス) インターネット アクセスが無制限です。 このレベルのネットワーク アクセスでは、実行しているノードやサービスから必要に応じて外部リソースにアクセスできます。 エグレス トラフィックを制限する場合は、正常なクラスター メンテナンス タスクを維持するために、アクセスできるポートとアドレスの数を制限する必要があります。 次に、外部パブリック リポジトリではなく、Microsoft Container Registry (MCR) または Azure Container Registry (ACR) のベース システム コンテナー イメージのみを使用するようにクラスターを構成します。 適切なファイアウォール規則とセキュリティ規則を構成し、これらの必要なポートとアドレスを許可する必要があります。

この記事では、AKS クラスターでエグレス トラフィックを制限する場合に必要なネットワーク ポートと完全修飾ドメイン名 (FQDN) について説明します。  現在、この機能はプレビュー段階にあります。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらの機能はプレビュー段階であり、運用環境での使用を意図していません。 パブリック プレビュー段階の機能は、"ベスト エフォート" のサポートに該当します。 AKS テクニカル サポート チームによるサポートは、太平洋タイム ゾーン (PST) での営業時間内のみで利用できます。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.66 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

エグレス トラフィックを制限できる AKS クラスターを作成するには、まずサブスクリプションの機能フラグを有効にします。 この機能の登録によって、作成する AKS クラスターは、MCR または ACR のベース システム コンテナー イメージを使用するように構成されます。 *AKSLockingDownEgressPreview* 機能フラグを登録するには、次の例に示すように [az feature register][az-feature-register] コマンドを使用します。

> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能の登録解除を行うことができません。 一部のプレビュー機能を有効にした後、すべての AKS クラスターに対して既定値が使用され、サブスクリプション内に作成されます。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

状態が *[登録済み]* と表示されるまでに数分かかります。 [az feature list][az-feature-list] コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>エグレス トラフィックの概要

管理と運用上の目的から、AKS クラスター内のノードは特定のポートと完全修飾ドメイン名 (FQDN) にアクセスする必要があります。 このようなアクションには、API サーバーと通信することや、コア Kubernetes クラスター コンポーネントとノード セキュリティの更新プログラムをダウンロードしてからインストールすることがあります。 既定では、エグレス (送信) インターネット トラフィックは AKS クラスター内のノードに制限されていません。 クラスターでは、外部リポジトリからベース システム コンテナー イメージをプルすることがあります。

AKS クラスターのセキュリティを強化するために、エグレス トラフィックを制限する場合があります。 クラスターは、MCR または ACR からベース システム コンテナー イメージをプルするように構成されています。 この方法でエグレス トラフィックをロック ダウンする場合は、AKS ノードが必要な外部サービスと正しく通信できるように、特定のポートと FQDN を定義する必要があります。 このような承認済みのポートと FQDN がない場合、AKS ノードは API サーバーと通信できず、コア コンポーネントをインストールできません。

[Azure Firewall][azure-firewall] またはサードパーティ製ファイアウォール アプライアンスを使用して、エグレス トラフィックをセキュリティで保護し、これらの必要なポートとアドレスを定義することができます。 AKS では、これらの規則は自動的に作成されません。 次のポートとアドレスは参照用であり、お使いのネットワーク ファイアウォールで適切な規則を作成する必要があります。

AKS には、2 組のポートとアドレスがあります。

* 「[AKS クラスターの必要なポートとアドレス](#required-ports-and-addresses-for-aks-clusters)」では、承認されるエグレス トラフィックの最小要件について詳しく説明しています。
* 「[AKS クラスターの省略可能な推奨されるアドレスとポート](#optional-recommended-addresses-and-ports-for-aks-clusters)」は、すべてのシナリオに必要ではなく、Azure Monitor など、他のサービスとの統合は正しく機能しません。 この省略可能なポートと FQDN の一覧を確認し、AKS クラスターに使用されるサービスとコンポーネントをすべて承認します。

> [!NOTE]
> エグレス トラフィックの制限は、機能フラグ登録を有効にした後に作成された新しい AKS クラスターにのみ機能します。 既存のクラスターの場合は、エグレス トラフィックを制限する前に、`az aks upgrade` コマンドを使用して[クラスターのアップグレード操作を実行][aks-upgrade]します。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS クラスターの必要なポートとアドレス

AKS クラスターには、次の送信ポート/ネットワーク規則が必要です。

* TCP ポート *443*
* トンネル フロント ポッドが API サーバー上のトンネルの終端と通信するための TCP ポート *9000* と TCP ポート *22*。
    * より具体的な情報を得るには、次の表の * *.hcp.\<location\>.azmk8s.io* アドレスと * *.tun.\<location\>.azmk8s.io* アドレスを参照してください。

次の FQDN/アプリケーション規則が必要です。

| FQDN                       | Port      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS: 443、TCP: 22、TCP: 9000 | このアドレスは API サーバー エンドポイントです。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。 |
| *.tun.\<location\>.azmk8s.io | HTTPS: 443、TCP: 22、TCP: 9000 | このアドレスは API サーバー エンドポイントです。 *\<location\>* は、AKS クラスターがデプロイされているリージョンに置き換えてください。 |
| aksrepos.azurecr.io        | HTTPS: 443 | このアドレスは、Azure Container Registry (ACR) 内のイメージにアクセスするために必要です。 |
| *.blob.core.windows.net    | HTTPS: 443 | このアドレスは ACR に保存されているイメージのバックエンド ストアです。 |
| mcr.microsoft.com          | HTTPS: 443 | このアドレスは、Microsoft Container Registry (MCR) のイメージにアクセスするために必要です。 |
| *.cdn.mscr.io              | HTTPS: 443 | このアドレスは、Azure Content Delivery Network (CDN) によってサポートされる MCR ストレージに必要です。 |
| management.azure.com       | HTTPS: 443 | このアドレスは、Kubernetes の GET/PUT 操作に必要です。 |
| login.microsoftonline.com  | HTTPS: 443 | このアドレスは Azure Active Directory 認証に必要です。 |
| api.snapcraft.io           | HTTPS: 443、HTTP: 80 | このアドレスは、Linux ノードにスナップ パッケージをインストールするために必要です。 |
| ntp.ubuntu.com             | UDP: 123   | このアドレスは、Linux ノード上での NTP 時刻同期に必要です。 |
| *.docker.io                | HTTPS: 443 | このアドレスは、トンネル フロントに必要なコンテナー イメージをプルするために必要です。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS クラスターの省略可能な推奨されるアドレスとポート

* DNS 用の UDP ポート *53*

AKS クラスターが正常に機能するためには、以下の FQDN/アプリケーション規則が推奨されます。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP: 80   | このアドレスを使用すると、Linux クラスター ノードから必要なセキュリティ パッチと更新プログラムをダウンロードできます。 |
| packages.microsoft.com                  | HTTPS: 443 | このアドレスは、キャッシュされた *apt-get* 操作に使用される Microsoft パッケージ リポジトリです。 |
| dc.services.visualstudio.com            | HTTPS: 443 | Azure Monitor を使用した適切なメトリックと監視のために推奨されます。 |
| *.opinsights.azure.com                  | HTTPS: 443 | Azure Monitor を使用した適切なメトリックと監視のために推奨されます。 |
| *.monitoring.azure.com                  | HTTPS: 443 | Azure Monitor を使用した適切なメトリックと監視のために推奨されます。 |
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | このアドレスは、Azure Policy (現在 AKS でプレビュー段階) の適切な操作のために使用されます。 |
| apt.dockerproject.org                   | HTTPS: 443 | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |
| nvidia.github.io                        | HTTPS: 443 | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |

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
