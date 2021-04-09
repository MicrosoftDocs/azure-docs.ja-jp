---
title: Azure Dev Spaces についてよく寄せられる質問
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Azure Dev Spaces についての一般的ないくつかの質問にお答えします
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548836"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces についてよく寄せられる質問

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces についてよく寄せられる質問に回答します。

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Azure Dev Spaces では、どのバージョンの Kubernetes がサポートされていますか。

Azure Dev Spaces では、[AKS で現在サポートされている Kubernetes の、1.18 までの一般提供 (GA) バージョン][aks-supported-k8s]がサポーされます。 AKS の Kubernetes 1.19 以降では、コンテナー ランタイムとして ContainerD が使用されていますが、これは Azure Dev Spaces では機能しません。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>現在はどの Azure リージョンで Azure Dev Spaces が提供されていますか。

利用可能なリージョンの完全な一覧については、[サポートされているリージョン][supported-regions]を参照してください。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Azure Dev Spaces が含まれる AKS クラスターを別のリージョンに移行できますか。

はい。Azure Dev Spaces が含まれる AKS クラスターを、別の[サポートされているリージョン][supported-regions]に移動する場合は、他のリージョンに新しいクラスターを作成してから、Azure Dev Spaces をインストールして構成し、リソースとアプリケーションを新しいクラスターにデプロイすることをお勧めします。 AKS の移行について詳しくは、「[Azure Kubernetes Service (AKS) に移行する][aks-migration]」をご覧ください。

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>既存の Dockerfiles または Helm Ｃhart で Azure Dev Spaces を使用できますか。

はい。プロジェクトに既に Dockerfile または Helm Ｃhart が存在する場合は、これらのファイルを Azure Dev Spaces で使用できます。 `azds prep` を実行する場合は、`--chart` パラメーターを使用してチャートの場所を指定します。 Azure Dev Spaces でも *azds.yaml* ファイルと *Dockerfile.develop* ファイルが生成されますが、既存の Dockerfile や Helm Ｃｈａｒｔ が置換または変更されることはありません。 `azds up` の実行時に既存のアプリケーションですべてを正常に機能させるために、*azds.yaml* ファイルと *Dockerfile.develop* ファイルの変更が必要になる場合があります。

独自の Dockerfile または Helm Ｃhart を使用する場合、次の制限があります。
* Dockerfile を 1 つだけ使用する場合は、ランタイムだけでなく言語 SDK など、開発シナリオを有効にするために必要なすべてを含める必要があります。 Azure Dev Spaces 用の独立した Dockerfile (Dockerfile.develop など) を使用する場合は、開発シナリオを有効にするために必要なすべてのものを Dockerfile に含める必要があります。
* Helm Ｃhart では、*values.yaml* からの値として、イメージ タグの一部または全体を渡すことがサポートされている必要があります。
* イングレスで何かを変更する場合は、Azure Dev Spaces によって提供されるイングレス ソリューションを使用するように Helm Ｃhart を更新することもできます。
* [Azure Dev Spaces によって提供されるルーティング機能][dev-spaces-routing]を使用する場合は、個々のプロジェクトのすべてのサービスが単一の Kubernetes 名前空間に収まる必要があり、単純な名前 (たとえば *service-a*) でデプロイする必要があります。 標準の Helm Ｃhart では、*fullnameOverride* プロパティの値を指定することでこの名前を更新できます。

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Azure Dev Spaces によって生成されたファイルを変更できますか。

はい。[プロジェクトを準備するときに Azure Dev Spaces によって生成される][dev-spaces-prep] *azds.yaml* ファイル、Dockerfile、および Helm Ｃhart を変更できます。 これらのファイルを変更すると、プロジェクトのビルド方法と実行方法が変更されます。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>パブリック IP アドレスなしで Azure Dev Spaces を使用できますか。

いいえ。パブリック IP を持たない AKS クラスター上に Azure Dev Spaces をプロビジョニングすることはできません。 パブリック IP は、[ルーティングのために Azure Dev Spaces に必要][dev-spaces-routing]です。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces で独自のイングレスを使用できますか。

はい。Azure Dev Spaces によって作成されるイングレスと共に、独自のイングレスを構成できます。 たとえば、[traefik][ingress-traefik] または [NGINX][ingress-nginx] を使用できます。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces で HTTPS を使用できますか。

はい。[traefik][ingress-https-traefik] または [NGINX][ingress-https-nginx] を使用して、HTTPS で独自のイングレスを構成できます。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>kubenet ではなく CNI を使用するクラスター上で Azure Dev Spaces を使用できますか。 

はい。ネットワークに CNI を使用する AKS クラスター上で、Azure Dev Spaces を使用できます。 たとえば、ネットワークに CNI を使用する[既存の Windows コンテナー][windows-containers]がある AKS クラスター上で、Azure Dev Spaces を使用できます。 Azure Dev Spaces を使用したネットワークでの CNI の使用の詳細については、[こちら](configure-networking.md#using-azure-cni)を参照してください。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Azure Dev Spaces で Windows コンテナーを使用できますか。

現時点では、Azure Dev Spaces は Linux ポッドおよびノード上でのみ実行することが想定されていますが、[既存の Windows コンテナー][windows-containers]を持つ AKS クラスター上で Azure Dev Spaces を実行できます。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API サーバーの承認済み IP アドレス範囲が有効になっている AKS クラスターで Azure Dev Spaces を使用できますか。

はい。[API サーバーの承認済み IP アドレス範囲][aks-auth-range]が有効になっている AKS クラスターで Azure Dev Spaces を使用できます。 Azure Dev Spaces で有効な API サーバー承認済み IP アドレスの範囲の AKS クラスターを使用する方法の詳細については、[こちら](configure-networking.md#using-api-server-authorized-ip-ranges)を参照してください。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>クラスター ノードのエグレス トラフィックが制限されている AKS クラスターで Azure Dev Spaces を使用できますか?

はい。正しい FQDN が許可されていると、[クラスター ノードに対する制限されたエグレス トラフィック][aks-restrict-egress-traffic]が有効になっている AKS クラスターで Azure Dev Spaces を使用できます。 Azure Dev Spaces での「クラスターノードに対する制限されたエグレストラフィックが有効な AKS クラスター」の使用の詳細については、[こちら](configure-networking.md#ingress-and-egress-network-traffic-requirements)を参照してください。

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>Kubernetes RBAC が有効化された AKS クラスターで Azure Dev Spaces を使用することはできますか。

はい。Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) が有効になっているかどうかに関係なく、AKS クラスターで Azure Dev Spaces を使用できます。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Visual Studio でプロジェクトのイングレスを有効にすると、どうなりますか。

Visual Studio を使用してプロジェクトを準備する場合は、サービスのイングレスを有効にすることを選択できます。 イングレスを有効にすると、AKS クラスター上で実行されるときにサービスにアクセスするためのパブリック エンドポイントが作成されます。これは、オプションです。 イングレスを有効にしなかった場合は、AKS クラスター内からのみサービスにアクセスできます。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Azure Dev Spaces でポッドのマネージド ID を使用できますか。

はい。Azure Dev Spaces が有効になっている AKS クラスター上で[ポッド マネージド ID][aks-pod-managed-id] を使用できますが、ポッド マネージド ID を使用して自分のクラスター上で Azure Dev Spaces を有効にした後に、[追加の構成手順][dev-spaces-pod-managed-id-steps]があります。 ポッドのマネージド ID がインストールされていて、それをアンインストールしたい場合は、[アンインストールに関する注意事項][aks-pod-managed-id-uninstall]のページで詳細を確認できます。

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>アプリケーション内の複数のマイクロサービスと共に Azure Dev Spaces を使用できますか。

はい。複数のマイクロサービスを含むアプリケーション内で Azure Dev Spaces を使用できますが、個々のマイクロサービスをそのルートで準備して実行する必要があります。 Azure Dev Spaces CLI、Azure Dev Spaces VS Code 拡張機能、および Visual Studio Azure 開発ワークロードでは、実行とデバッグを行うために、*azds.yaml* ファイルがマイクロサービスのルートにあることが想定されています。 1 つのアプリケーション内の複数のマイクロサービスの例については、[自転車シェアリングのサンプル アプリケーション][bike-sharing]を参照してください。

Visual Studio Code では、[1 つのワーク スペース内で個別のプロジェクトを開いて][vs-code-multi-root-workspaces]、Azure Dev Spaces を通じて個別にデバッグすることができます。 各プロジェクトは、自己完結型かつ Azure Dev Spaces 用に準備されている必要があります。

Visual Studio では、Azure Dev Spaces を使用してデバッグするために .NET Core ソリューションを構成することができます。

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Azure Dev Spaces はサービス メッシュと共に使用できますか。

現時点では、[Istio][istio] や [Linkerd][linkerd] などのサービス メッシュと共に Azure Dev Spaces を使用することはできません。 Azure Dev Spaces とサービス メッシュを同じ AKS クラスター上で実行することはできますが、同じ名前空間内で Azure Dev Spaces とサービス メッシュの両方を有効にすることはできません。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md