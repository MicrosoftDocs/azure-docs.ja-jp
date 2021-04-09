---
title: さまざまなネットワーク トポロジで Azure Dev Spaces 用のネットワークを構成する
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Azure Kubernetes Services で Azure Dev Spaces を実行するためのネットワーク要件について説明します。
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、コンテナー、CNI、kubenet、SDN、ネットワーク
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91970340"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>さまざまなネットワーク トポロジで Azure Dev Spaces 用のネットワークを構成する

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces は、既定のネットワーク構成を使用して Azure Kubernetes Service (AKS) クラスター上で実行されます。 AKS クラスターのネットワーク構成を変更する (クラスターをファイアウォールの背後に配置する、ネットワーク セキュリティ グループを使用する、ネットワーク ポリシーを使用するなど) 場合は、Azure Dev Spaces を実行するための追加の考慮事項を組み込む必要があります。

![Virtual Network の構成](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>仮想ネットワークまたはサブネット構成

AKS クラスターには、その AKS クラスターへのイングレスまたはエグレス トラフィックを制限するための別の仮想ネットワークまたはサブネット構成が含まれている可能性があります。 たとえば、クラスターがファイアウォール (Azure Firewall など) の背後に存在したり、ネットワーク トラフィックを制限するためのネットワーク セキュリティ グループまたはカスタム ロールを使用したりすることがあります。 ネットワーク構成の例は、[GitHub の Azure Dev Spaces のサンプル リポジトリ][sample-repo]にあります。

Azure Dev Spaces には、*イングレスおよびエグレス* ネットワーク トラフィックや、*イングレスのみ* のトラフィックに対する特定の要件があります。 AKS クラスターへのトラフィックを制限する仮想ネットワークまたはサブネット構成を含む AKS クラスター上で Azure Dev Spaces を使用している場合、Azure Dev Spaces が正しく機能するには、次のイングレスのみのトラフィックとイングレスおよびエグレス トラフィックの要件に従う必要があります。

### <a name="ingress-and-egress-network-traffic-requirements"></a>イングレスおよびエグレス ネットワーク トラフィックの要件

Azure Dev Spaces では、次の FQDN にはイングレスおよびエグレス トラフィックが必要です。

| FQDN                       | Port       | 用途      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Azure Dev Spaces の Docker イメージをプルするため |
| gcr.io                     | HTTPS: 443 | Azure Dev Spaces の Helm イメージをプルするため |
| storage.googleapis.com     | HTTPS: 443 | Azure Dev Spaces の Helm イメージをプルするため |

上のすべての FQDN と [Azure Dev Spaces インフラストラクチャ サービス][service-tags]との間のネットワーク トラフィックを許可するようにファイアウォールまたはセキュリティ構成を更新してください。 たとえば、ネットワークをセキュリティで保護するためのファイアウォールを使用している場合、上記の FQDN をファイアウォールのアプリケーション ルールに追加し、さらに Azure Dev Spaces のサービス タグも[ファイアウォールに追加][firewall-service-tags]する必要があります。 これらのドメインとの間で送受信されるトラフィックを許可するには、ファイアウォールに対するこれら両方の更新が必要です。

### <a name="ingress-only-network-traffic-requirements"></a>イングレスのみのネットワーク トラフィックの要件

Azure Dev Spaces は、Kubernetes 名前空間レベルのルーティングや、独自の FQDN を使用したサービスへのパブリック アクセスを提供します。 これらの両方の機能が動作するようにするには、クラスター上の Azure Dev Spaces イングレス コントローラーの外部 IP アドレスへのパブリック イングレスを許可するようにファイアウォールまたはネットワーク構成を更新してください。 あるいは、[内部ロード バランサー][aks-internal-lb]を作成し、ファイアウォールのパブリック IP をその内部ロード バランサーの IP に変換する NAT ルールをファイアウォールに追加することができます。 また、[traefik][traefik-ingress] または [NGINX][nginx-ingress] を使用して、カスタム イングレス コントローラーを作成することもできます。

## <a name="aks-cluster-network-requirements"></a>AKS クラスター ネットワークの要件

AKS では、[ネットワーク ポリシー][aks-network-policies]を使用して、クラスター上のポッド間のイングレスおよびエグレス トラフィックや、ポッドからのエグレス トラフィックを制御できます。 Azure Dev Spaces には、*イングレスおよびエグレス* ネットワーク トラフィックや、*イングレスのみ* のトラフィックに対する特定の要件があります。 AKS ネットワーク ポリシーを含む AKS クラスター上で Azure Dev Spaces を使用している場合、Azure Dev Spaces が正しく機能するには、次のイングレスのみのトラフィックとイングレスおよびエグレス トラフィックの要件に従う必要があります。

### <a name="ingress-and-egress-network-traffic-requirements"></a>イングレスおよびエグレス ネットワーク トラフィックの要件

Azure Dev Spaces では、デバッグのために、クラスター上の開発スペース内のポッドと直接通信できます。 この機能が動作するようにするには、Azure Dev Spaces インフラストラクチャの IP アドレス (これは[リージョンによって異なります][service-tags]) へのイングレスおよびエグレス通信を許可するネットワーク ポリシーを追加します。

### <a name="ingress-only-network-traffic-requirements"></a>イングレスのみのネットワーク トラフィックの要件

Azure Dev Spaces は、名前空間にまたがるポッド間のルーティングを提供します。 たとえば、Azure Dev Spaces が有効になっている名前空間には、親と子の名前空間にまたがるポッド間でネットワーク トラフィックをルーティングできる親/子の関係を割り当てることができます。 また、Azure Dev Spaces は、独自の FQDN を使用してサービス エンドポイントを公開します。 サービスを公開するさまざまなやり方を構成する方法と、名前空間レベルのルーティングへのその影響についてには、「[さまざまなエンドポイント オプションの使用][endpoint-options]」を参照してください。

## <a name="using-azure-cni"></a>Azure CNI の使用

既定では、AKS クラスターは、Azure Dev Spaces で動作する [kubenet][aks-kubenet] をネットワークに使用するように構成されます。 また、[Azure Container Networking Interface (CNI)][aks-cni] を使用するように AKS クラスターを構成することもできます。 AKS クラスター上で Azure Dev Spaces と Azure CNI を使用するには、仮想ネットワークとサブネットのアドレス空間に、Azure Dev Spaces によってデプロイされたポッドのための最大 10 個のプライベート IP アドレスを許可します。 プライベート IP アドレスを許可する方法に関する詳細は、[AKS Azure CNI のドキュメント][aks-cni-ip-planning]に記載されています。

## <a name="using-api-server-authorized-ip-ranges"></a>API サーバーの許可された IP 範囲の使用

AKS クラスターでは、クラスターにアクセスする IP アドレスを制限する追加のセキュリティ (カスタム仮想ネットワークの使用や、[許可された IP 範囲を使用する API サーバーへのアクセスのセキュリティ保護][aks-ip-auth-ranges]など) を構成できます。 クラスターの[作成][aks-ip-auth-range-create]中にこの追加のセキュリティを使用する場合に Azure Dev Spaces を使用するには、[リージョンに基づいて追加の範囲を許可する][service-tags]必要があります。 また、既存のクラスターを[更新][aks-ip-auth-range-update]して、これらの追加の範囲を許可することもできます。 デバッグのために AKS クラスターに接続するすべての開発用コンピューターの IP アドレスが API サーバーに接続できるようにすることも必要です。

## <a name="using-aks-private-clusters"></a>AKS プライベート クラスターの使用

現時点では、[AKS プライベート クラスター][aks-private-clusters]で Azure Dev Spaces はサポートされていません。

## <a name="using-different-endpoint-options"></a>さまざまなエンドポイント オプションの使用

Azure Dev Spaces には、AKS で実行されているサービスのエンドポイントを公開するためのオプションがあります。 クラスターで Azure Dev Spaces を有効にするときに、クラスターのエンドポイントの種類を構成するための次のオプションがあります。

* "*パブリック*" エンドポイント (既定値) では、パブリック IP アドレスを持つイングレス コントローラーがデプロイされます。 パブリック IP アドレスはクラスターの DNS に登録されるため、URL を使用してサービスにパブリック アクセスできるようになります。 この URL は `azds list-uris` を使用して表示できます。
* "*プライベート*" エンドポイントでは、プライベート IP アドレスを持つイングレス コントローラーがデプロイされます。 プライベート IP アドレスを使用すると、クラスターのロード バランサーには、そのクラスターの仮想ネットワーク内からのみアクセスできます。 ロード バランサーのプライベート IP アドレスはクラスターの DNS に登録されるため、クラスターの仮想ネットワーク内のサービスに URL を使用してアクセスできます。 この URL は `azds list-uris` を使用して表示できます。
* エンドポイント オプションに *none* を設定すると、イングレス コントローラーはデプロイされません。 イングレス コントローラーがデプロイされていない場合、[Azure Dev Spaces のルーティング機能][dev-spaces-routing]は機能しません。 必要に応じて、[traefik][traefik-ingress] または [NGINX][nginx-ingress] を使用して独自のイングレス コントローラー ソリューションを実装できます。これにより、ルーティング機能を再び機能させることができます。

エンドポイント オプションを構成するには、クラスターで Azure Dev Spaces を有効にするときに *-e* または *--endpoint* を使用します。 次に例を示します。

> [!NOTE]
> エンドポイント オプションを使用するには、Azure CLI バージョン 2.2.0 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>クライアントの要件

Azure Dev Spaces は、デバッグのための AKS クラスターとの通信にクライアント側ツール (Azure Dev Spaces CLI 拡張機能、Visual Studio Code 拡張機能、Visual Studio 拡張機能など) を使用します。 Azure Dev Spaces のクライアント側ツールを使用するには、開発用マシンから [Azure Dev Spaces インフラストラクチャ][dev-spaces-allow-infrastructure]へのトラフィックを許可します。 [API サーバーの許可された IP 範囲][auth-range-section]を使用している場合は、デバッグのために AKS クラスターに接続するすべての開発用コンピューターの IP アドレスが API サーバーに接続できるようにすることも必要です。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags