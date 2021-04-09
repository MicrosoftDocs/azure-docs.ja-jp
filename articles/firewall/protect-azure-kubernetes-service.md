---
title: Azure Firewall を使用して Azure Kubernetes Service (AKS) のデプロイを保護する
description: Azure Firewall を使用して Azure Kubernetes Service (AKS) のデプロイを保護する方法について説明します
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: 51af9ff4972f5edef02426a6e81e8582123c9a7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98107856"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Azure Firewall を使用して Azure Kubernetes Service (AKS) のデプロイを保護する

Azure Kubernetes Service (AKS) では、Azure 上にマネージド Kubernetes クラスターが提供されます。 その責任の多くを Azure にオフロードすることで、Kubernetes の管理の複雑さと運用上のオーバーヘッドが軽減されます。 AKS では、正常性の監視やメンテナンスなどの重要なタスクが自動的に処理され、管理の促進によって、セキュリティ保護されたエンタープライズ グレードのクラスターが提供されます。

Kubernetes では、仮想マシンのクラスターが調整され、使用可能なコンピューティング リソースと各コンテナーのリソース要件に基づいて、これらの仮想マシンで実行されるようにコンテナーがスケジュールされます。 コンテナーは、Kubernetes の基本的な運用単位であるポッドにグループ化され、これらのポッドが目的の状態にスケーリングされます。

管理と運用上の目的から、AKS クラスター内のノードは特定のポートと完全修飾ドメイン名 (FQDN) にアクセスする必要があります。 このようなアクションには、API サーバーと通信することや、コア Kubernetes クラスター コンポーネントとノード セキュリティの更新プログラムをダウンロードしてからインストールすることがあります。 Azure Firewall は、環境をロックダウンし、送信トラフィックをフィルター処理するのに役立ちます。

この記事のガイドラインに従って、Azure Firewall を使用して、Azure Kubernetes クラスターの保護を強化します。

## <a name="prerequisites"></a>前提条件

- アプリケーションを実行中のデプロイ済みの Azure Kubernetes クラスター。

   詳細については、「[チュートリアル: Azure Kubernetes Service (AKS) クラスターのデプロイ](../aks/tutorial-kubernetes-deploy-cluster.md)」と「[チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションの実行](../aks/tutorial-kubernetes-deploy-application.md)」を参照してください。


## <a name="securing-aks"></a>AKS の保護

Azure Firewall では、構成を簡略化するための AKS FQDN タグが提供されています。 AKS プラットフォームの送信トラフィックを許可するには、次の手順に従います。

- Azure Firewall を使用して送信トラフィックを制限し、すべての送信トラフィックをリダイレクトするユーザー定義ルート (UDR) を作成するときは、受信トラフィックを正しく許可するために、ファイアウォールに適切な DNAT 規則を作成する必要があります。 

   Azure Firewall と UDR を使用すると、非対称ルーティングによって受信設定が機能しなくなります。 AKS サブネットにファイアウォールのプライベート IP アドレスに送信される既定のルートがある場合に、パブリック ロード バランサーを使用すると、この問題が発生します。 たとえば、*LoadBalancer* という種類の受信または Kubernetes サービスです。

   この場合、ロード バランサーの受信トラフィックはパブリック IP アドレス経由で受信されますが、復路のパスはファイアウォールのプライベート IP アドレスを通過します。 ファイアウォールはステートフルであり、確立済みのセッションを認識しないため、返されるパケットは破棄されます。 Azure Firewall をイングレスまたはサービスのロード バランサーと統合する方法については、「[Azure Firewall と Azure Standard Load Balancer を統合する](integrate-lb.md)」を参照してください。
- アプリケーション ルール コレクションを作成し、*AzureKubernetesService* FQDN タグを有効にするルールを追加します。 送信元 IP アドレスの範囲はホスト プールの仮想ネットワークで、プロトコルは https、宛先は AzureKubernetesService です。
- AKS クラスターには、次の送信ポート/ネットワーク規則が必要です。

   - TCP ポート 443
   - API サーバーと通信する必要があるアプリがある場合は、TCP [*IPAddrOfYourAPIServer*]:443 が必要です。 この変更は、クラスターの作成後に設定できます。
   - トンネル フロント ポッドが API サーバー上のトンネルの終端と通信するための TCP ポート 9000 と UDP ポート 1194。

      具体的には、次の表のアドレスを参照してください。

   | 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 |
   | **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [リージョンの CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | ノードとコントロール プレーンの間のセキュリティで保護されたトンネル通信の場合。 |

   - ネットワーク タイム プロトコル (NTP) の時刻同期 (Linux ノード) 用の UDP ポート 123。
   - API サーバーに直接アクセスするポッドがある場合は、DNS 用の UDP ポート 53 も必要です。

   詳細については、「[Azure Kubernetes Service (AKS) でクラスター ノードに対するエグレス トラフィックを制御する](../aks/limit-egress-traffic.md)」を参照してください。
- AzureMonitor タグと Storage Service タグを構成します。 Azure Monitor で Log Analytics データを受信します。

   お使いのワークスペース URL (`<worksapceguid>.ods.opinsights.azure.com` と `<worksapceguid>.oms.opinsights.azure.com`) を個別に許可することもできます。 これは、次の方法のいずれかの方法で対処できます。

    - ホスト プール サブネットから `*. ods.opinsights.azure.com` と `*.oms. opinsights.azure.com` への https アクセスを許可します。 これらのワイルドカード FQDN によって必要なアクセスが有効になりますが、制限は緩くなります。
    - 次の Log Analytics クエリを使用して、必要な FQDN を正確に一覧表示してから、ファイアウォール アプリケーション ルールでそれらを明示的に許可します。
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>次のステップ

- Azure Kubernetes Service の詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念](../aks/concepts-clusters-workloads.md)」を参照してください。
