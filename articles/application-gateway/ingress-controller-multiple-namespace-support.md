---
title: Application Gateway イングレス コントローラーでの複数の名前空間のサポートを有効にする
description: この記事では、Application Gateway イングレス コントローラーを使用して、Kubernetes クラスター内で複数の名前空間のサポートを有効にする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874595"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Application Gateway イングレス コントローラーを使用して AKS クラスターでの複数の名前空間のサポートを有効にする

## <a name="motivation"></a>目的
Kubernetes [名前空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) を使用すると、Kubernetes クラスターをパーティション分割して、大規模なチームのサブグループに割り当てることが可能になります。 これらのサブチームでは、リソース、セキュリティ、構成などをより細かく制御し、インフラストラクチャをデプロイして管理できます。Kubernetes を使用すると、各名前空間内に 1 つまたは複数のイングレス リソースを個別に定義できます。

バージョン 0.7 以降の [Azure Application Gateway の Kubernetes イングレス コントローラー](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) では、複数の名前空間からイベントを取り込んで、それらの名前空間を監視することができます。 AKS 管理者が [App Gateway](https://azure.microsoft.com/services/application-gateway/) をイングレスとして使用することを決定した場合、すべての名前空間では Application Gateway の同じインスタンスが使用されます。 イングレス コントローラーの 1 回のインストールによって、アクセス可能な名前空間が監視され、関連付けられている Application Gateway が構成されます。

AGIC のバージョン 0.7 では、`default` 名前空間が Helm 構成内の 1 つまたは複数の異なる名前空間に明示的に変更されない限り、これが引き続き排他的に監視されます (以下のセクションを参照)。

## <a name="enable-multiple-namespace-support"></a>複数の名前空間のサポートを有効にする
複数の名前空間のサポートを有効にするには:
1. 次のいずれかの方法で、[helm-config.yaml](#sample-helm-config-file) ファイルを変更します。
   - `watchNamespace` キーを [helm-config.yaml](#sample-helm-config-file) から完全に削除する - AGIC では、すべての名前空間が監視されます
   - `watchNamespace` に空の文字列を設定する - AGIC では、すべての名前空間が監視されます
   - 複数の名前空間をコンマで区切って追加する (`watchNamespace: default,secondNamespace`) - AGIC では、これらの名前空間が排他的に監視されます
2. 以下を使用して、Helm テンプレートの変更を適用します: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

複数の名前空間を監視する機能を備えてデプロイされると、AGIC では次のことが可能になります。
  - アクセス可能なすべての名前空間からのイングレス リソースを一覧表示する
  - `kubernetes.io/ingress.class: azure/application-gateway` によって注釈が付けられたイングレス リソースにフィルター処理する
  - 組み合わせた [Application Gateway の構成](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)を作成する
  - [ARM](../azure-resource-manager/management/overview.md) 経由で、関連付けられている Application Gateway に構成を適用する

## <a name="conflicting-configurations"></a>競合する構成
複数の名前空間が設定された[イングレス リソース](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource)では、単一の Application Gateway に対して競合する構成を作成するように、AGIC に指示することができます  (たとえば、同じドメインを要求する 2 つのイングレス)。

階層の最上位では、**リスナー** (IP アドレス、ポート、およびホスト) と **ルーティング規則** (バインディング リスナー、バックエンド プール、および HTTP 設定) は、複数の名前空間/イングレスによって作成および共有することができます。

一方、パス、バックエンド プール、HTTP 設定、および TLS 証明書は、1 つの名前空間のみによって作成され、重複は削除されます。

たとえば、次の重複したイングレス リソースによって定義された `www.contoso.com` に対する名前空間 `staging` および `production` について考えます。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

`www.contoso.com` がそれぞれの Kubernetes 名前空間にルーティングされるように 2 つのイングレス リソースによってトラフィックが要求されていますが、トラフィックを処理できるバックエンドは 1 つだけです。 AGIC では、"先着順の処理" を基本に、リソースの 1 つに対して構成を作成します。 2 つのイングレス リソースが同時に作成された場合、アルファベット順で先になるものが優先されます。 上記の例からは、`production` イングレスに対する設定しか作成できません。 Application Gateway は、次のリソースによって構成されます。

  - リスナー: `fl-www.contoso.com-80`
  - ルーティング規則: `rr-www.contoso.com-80`
  - バックエンド プール: `pool-production-contoso-web-service-80-bp-80`
  - HTTP 設定: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - 正常性プローブ: `pb-production-contoso-web-service-80-websocket-ingress`

*リスナー* と *ルーティング規則* を除き、作成される Application Gateway リソースには、それらが作成された名前空間 (`production`) の名前が含まれていることに注意してください。

2 つのイングレス リソースが異なる時点で AKS クラスターに導入されている場合、AGIC では、Application Gateway が再構成され `namespace-B`から `namespace-A` にトラフィックが再ルーティングされるシナリオに終始する傾向にあります。

たとえば `staging` を最初に追加した場合、AGIC では、ステージング バックエンド プールにトラフィックをルーティングするように Application Gateway が構成されます。 以降の段階で、`production` イングレスを導入すると、AGIC では Application Gateway が再プログラミングされ、`production` バックエンド プールへのトラフィックのルーティングが開始されます。

## <a name="restrict-access-to-namespaces"></a>名前空間へのアクセスを制限する
AGIC では既定で、いずれかの名前空間内の注釈付きのイングレスに基づいて、Application Gateway が構成されます。 この動作を制限する場合、次のオプションを使用できます。
  - AGIC において [helm-config.yaml](#sample-helm-config-file) 内の `watchNamespace` YAML キー経由で監視する必要がある名前空間を明示的に定義することで、名前空間を制限する
  - [Role/RoleBinding](../aks/azure-ad-rbac.md) を使用して、AGIC を特定の名前空間に制限する

## <a name="sample-helm-config-file"></a>サンプルの Helm 構成ファイル

```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```