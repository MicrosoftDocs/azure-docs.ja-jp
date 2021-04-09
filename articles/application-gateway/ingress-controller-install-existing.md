---
title: 既存の Application Gateway を使用してイングレス コントローラーを作成する
description: この記事では、既存の Application Gateway を使用して Application Gateway イングレス コントローラーをデプロイする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2d64766c754c0ea104ae83fde799a514e9da6d68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693733"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>既存の Application Gateway を使用して Application Gateway イングレス コントローラー (AGIC) をインストールする

Application Gateway イングレス コントローラー (AGIC) は、使用する Kubernetes クラスター内のポッドです。
AGIC では、Kubernetes [イングレス](https://kubernetes.io/docs/concepts/services-networking/ingress/) リソースを監視し、Kubernetes クラスターの状態に基づいて Application Gateway 構成を作成して適用します。

## <a name="outline"></a>アウトライン:
- [前提条件](#prerequisites)
- [Azure Resource Manager (ARM) 認証](#azure-resource-manager-authentication)
    - オプション 1: [AAD ポッド ID を設定](#set-up-aad-pod-identity)して、ARM で Azure ID を作成する
    - オプション 2:[サービス プリンシパルを使用する](#using-a-service-principal)
- [Helm を使用してイングレス コントローラーをインストールする](#install-ingress-controller-as-a-helm-chart)
- [マルチクラスター/共有 Application Gateway](#multi-cluster--shared-application-gateway):1 つ以上の AKS クラスターやその他の Azure コンポーネント間で Application Gateway が共有される環境に AGIC をインストールします。

## <a name="prerequisites"></a>前提条件
このドキュメントは、次のツールとインフラストラクチャが既にインストールされていることを前提としています。
- [高度なネットワーク](../aks/configure-azure-cni.md)が構成されている [AKS](https://azure.microsoft.com/services/kubernetes-service/)
- AKS と同じ仮想ネットワーク内の [Application Gateway v2](./tutorial-autoscale-ps.md)
- ご利用の AKS クラスターにインストールされている [AAD ポッド ID](https://github.com/Azure/aad-pod-identity)
- [Cloud Shell](https://shell.azure.com/) は、`az`CLI、`kubectl`、`helm` がインストールされている Azure シェル環境です。 これらのツールは、以下のコマンドに必要です。

AGIC をインストールする前に、__Application Gateway の構成をバックアップ__ してください。
  1. [Azure portal](https://portal.azure.com/) を使用してご利用の `Application Gateway` インスタンスに移動する
  2. `Export template` から `Download` をクリックする

ダウンロードした zip ファイルには、必要になったときに Application Gateway を復元するために使用できる JSON テンプレート、bash、PowerShell スクリプトが含まれます

## <a name="install-helm"></a>Helm のインストール
[Helm](../aks/kubernetes-helm.md) は、Kubernetes 用のパッケージ マネージャーです。 これを利用して `application-gateway-kubernetes-ingress` パッケージをインストールします。
[Cloud Shell](https://shell.azure.com/) を使用して、Helm をインストールします。

1. [Helm](../aks/kubernetes-helm.md) をインストールし、以下を実行して `application-gateway-kubernetes-ingress` Helm パッケージを追加します。

    - "*Kubernetes RBAC が有効*" の AKS クラスター

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - "*Kubernetes RBAC が無効*" の AKS クラスター

    ```bash
    helm init
    ```

1. AGIC Helm リポジトリを追加します。
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager 認証

AGIC では、Kubernetes API サーバーと Azure Resource Manager と通信します。 これらの API にアクセスするには ID が必要です。

## <a name="set-up-aad-pod-identity"></a>AAD ポッド ID の設定

[AAD ポッド ID](https://github.com/Azure/aad-pod-identity) は、AGIC に似たコントローラーであり、使用する AKS でも実行されます。 Azure Active Directory ID は、ご利用の Kubernetes ポッドにバインドされます。 ID は、Kubernetes ポッド内のアプリケーションが他の Azure コンポーネントと通信できるようにするために必要です。 特定のケースではここで、AGIC ポッドで [ARM](../azure-resource-manager/management/overview.md) への HTTP 要求を行うための承認が必要です。

[AAD ポッド ID のインストール手順](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra)に従って、使用する AKS にこのコンポーネントを追加します。

次に、Azure ID を作成して、そのアクセス許可を ARM に付与する必要があります。
[Cloud Shell](https://shell.azure.com/) を使用して、次のコマンドをすべて実行し、ID を作成します。

1. **AKS ノードと同じリソース グループ内に** Azure ID を作成します。 正しいリソース グループを選択することが重要です。 次のコマンドで必要なリソース グループは、AKS ポータル ウィンドウで参照できるリソース グループでは "*ありません*"。 これは `aks-agentpool` 仮想マシンのリソース グループです。 通常、リソース グループは `MC_` で始まり、使用する AKS の名前が含まれています。 例: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 次のロールの割り当てコマンドでは、新しく作成された ID の `principalId` を取得する必要があります。

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Application Gateway への `Contributor` アクセスを ID に付与します。 この場合、Application Gateway の ID が必要で、次のようになります: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    次を使用して、ご利用のサブスクリプション内の Application Gateway ID の一覧を取得します: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Application Gateway リソース グループへの `Reader` アクセスを ID に付与します。 リソース グループ ID は次のようになります: `/subscriptions/A/resourceGroups/B`。 すべてのリソース グループを取得するには、次を使用します: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>サービス プリンシパルを使用する
Kubernetes シークレットを使用して ARM への AGIC アクセスを提供することもできます。

1. Active Directory のサービス プリンシパルを作成し、base64 でエンコードします。 JSON BLOB を Kubernetes に保存するには、base64 エンコードが必要です。

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. base64 でエンコードされた JSON BLOB を `helm-config.yaml` ファイルに追加します。 `helm-config.yaml` の詳細については、次のセクションを参照してください。
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>イングレス コントローラーを Helm Chart としてインストールする
最初の手順で、ご利用の Kubernetes クラスターに Helm の Tiller をインストールします。 [Cloud Shell](https://shell.azure.com/) を使用して、AGIC Helm パッケージをインストールします。

1. `application-gateway-kubernetes-ingress` Helm リポジトリを追加し、Helm の更新を実行します

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. AGIC の構成を行う helm-config.yaml をダウンロードします。
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    または、以下の YAML ファイルをコピーします。 
    
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. helm-config.yaml を編集し、`appgw` と `armAuth` の値を入力します。
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` と `<identity-client-id>` は、前のセクションで設定した Azure AD ID のプロパティです。 この情報を取得するには、次のコマンドを実行します: `az identity show -g <resourcegroup> -n <identity-name>`。`<resourcegroup>` は、最上位レベルの AKS クラスター オブジェクト、Application Gateway、および管理対象 ID がデプロイされているリソース グループです。

1. 前の手順の `helm-config.yaml` 構成を使用して、Helm Chart `application-gateway-kubernetes-ingress` をインストールします。

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    または、1 つの手順の中で `helm-config.yaml` と Helm コマンドを組み合わせることもできます。
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. 新しく作成されたポッドのログを確認して、正常に開始したかどうかを確認します

Azure Application Gateway を使用して、AKS サービスを HTTP または HTTPS 経由でインターネットに公開する方法については、[この攻略ガイド](ingress-controller-expose-service-over-http-https.md)を参照してください。



## <a name="multi-cluster--shared-application-gateway"></a>マルチクラスター/共有 Application Gateway
既定では、AGIC はリンク先の Application Gateway の完全な所有権を前提としています。 AGIC バージョン 0.8.0 以降では、1 つの Application Gateway を他の Azure コンポーネントと共有できます。 たとえば、AKS クラスターだけでなく、仮想マシン スケール セットでホストされているアプリケーションに対しても同じ Application Gateway を使用できます。

この設定を有効にする前に、__Application Gateway の構成をバックアップ__ してください。
  1. [Azure portal](https://portal.azure.com/) を使用してご利用の `Application Gateway` インスタンスに移動する
  2. `Export template` から `Download` をクリックする

ダウンロードした zip ファイルには、Application Gateway を復元するために使用できる JSON テンプレート、bash、PowerShell スクリプトが含まれます

### <a name="example-scenario"></a>シナリオ例
2 つの Web サイトのトラフィックを管理する架空の Application Gateway を見てみましょう。
  - `dev.contoso.com` - Application Gateway と AGIC を使用して、新しい AKS にホストされている
  - `prod.contoso.com` - [Azure 仮想マシン スケール セット](https://azure.microsoft.com/services/virtual-machine-scale-sets/)にホストされている

既定の設定では、AGIC は、指定されている Application Gateway の 100% の所有権を前提としています。 AGIC では、すべての Application Gateway の構成が上書きされます。 Kubernetes イングレス内に定義せずに、(Application Gateway 上で) `prod.contoso.com` のリスナーを手動で作成すると、`prod.contoso.com` 構成は AGIC により数秒以内に削除されます。

また、AGIC をインストールし、仮想マシン スケール セットのマシンから `prod.contoso.com` を提供するには、`dev.contoso.com` のみを構成するように AGIC を制限する必要があります。 これは、次の [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) をインスタンス化すると容易になります。

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

上記のコマンドによって、`AzureIngressProhibitedTarget` オブジェクトが作成されます。 これにより、AGIC (バージョン 0.8.0 以降) では、`prod.contoso.com` の Application Gateway 構成の存在を認識し、そのホスト名に関連付けられた構成を変更しないように明示的に指示します。


### <a name="enable-with-new-agic-installation"></a>新しい AGIC インストールで有効にする
AGIC (バージョン 0.8.0 以降) を Application Gateway 構成のサブセットに制限するには、`helm-config.yaml` テンプレートを変更します。
`appgw:` セクションで、`shared` キーを追加して `true` に設定します。

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Helm の変更を適用します:
  1. `AzureIngressProhibitedTarget` CRD がインストールされていることを確認します:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Helm を更新します:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

その結果、AKS には `prohibit-all-targets` という `AzureIngressProhibitedTarget` の新しいインスタンスが作成されます。
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

オブジェクト `prohibit-all-targets` では、名前が示すように、AGIC が "*いかなる*" ホストおよびパスの構成も変更することが禁止されます。
`appgw.shared=true` を指定して Helm をインストールすると、AGIC がデプロイされますが、Application Gateway は変更されません。


### <a name="broaden-permissions"></a>アクセス許可を拡大する
`appgw.shared=true` と既定の `prohibit-all-targets` を使用した Helm では、AGIC による構成の適用がブロックされるためです。

AGIC のアクセス許可を拡大します:
1. 特定のセットアップを使用して、新しい `AzureIngressProhibitedTarget` を作成します:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. 独自のカスタム禁止を作成した後にのみ、既定のものを削除できますが、これは過度な拡大です:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>既存の AGIC インストールに対して有効にする
AKS、Application Gateway、構成済みの AGIC が既にクラスター内で動作しているとしましょう。 `prod.contoso.com` のイングレスがあり、AKS からそれのトラフィックを正常にサービスしています。 既存の Application Gateway に `staging.contoso.com` を追加しますが、[VM](https://azure.microsoft.com/services/virtual-machines/) でホストする必要があります。 既存の Application Gateway を再利用し、`staging.contoso.com` のリスナーとバックエンド プールを手動で構成します。 しかし、Application Gateway 構成を ([ポータル](https://portal.azure.com)、[ARM API](/rest/api/resources/) または [Terraform](https://www.terraform.io/) から) 手動で調整すると、AGIC が完全な所有権を仮定した場合と競合します。 変更を適用するとすぐに、AGIC によって上書きまたは削除されます。

AGIC が構成のサブセットに変更を加えることを禁止できます。

1. `AzureIngressProhibitedTarget` オブジェクトを作成します:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. 新しく作成されたオブジェクトを表示します:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. ポータルを使用して Application Gateway 構成を変更する - リスナー、ルーティング規則、バックエンドなどを追加します。作成した新しいオブジェクト (`manually-configured-staging-environment`) では、AGIC が `staging.contoso.com` に関連付けられた Application Gateway 構成を上書きすることを禁止します。
