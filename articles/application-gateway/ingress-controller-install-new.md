---
title: 新しい Application Gateway を使用してイングレス コントローラーを作成する
description: この記事では、新しい Application Gateway を使用して Application Gateway イングレス コントローラーをデプロイする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be5ac75e2da3eaeae300fd36e152a24c9777e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593741"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>新しい Application Gateway を使用して Application Gateway イングレス コントローラー (AGIC) をインストールする方法

以下の手順は、既存のコンポーネントが存在しない環境に Application Gateway イングレス コントローラー (AGIC) をインストールすることを前提としています。

## <a name="required-command-line-tools"></a>必須のコマンド ライン ツール

次のすべてのコマンドライン操作に対して [Azure Cloud Shell](https://shell.azure.com/) を使用することをお勧めします。 shell.azure.com から、または次のリンクをクリックして、シェルを起動します。

[![埋め込みの起動](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)

または、次のアイコンを使用して Azure portal から Cloud Shell を起動します。

![ポータルの起動](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure Cloud Shell](https://shell.azure.com/) には、必要なすべてのツールが既に含まれています。 別の環境を使用する場合は、次のコマンドライン ツールがインストールされていることを確認してください。

* `az` - Azure CLI: [インストール手順](/cli/azure/install-azure-cli)
* `kubectl` - Kubernetes コマンド ライン ツール: [インストール手順](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` - Kubernetes パッケージ マネージャー: [インストール手順](https://github.com/helm/helm/releases/latest)
* `jq` - コマンド ライン JSON プロセッサ: [インストール手順](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>ID を作成する

次の手順に従って、Azure Active Directory (AAD) [サービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成します。 `appId`、`password`、`objectId` の値を記録してください。これらは次の手順で使用します。

1. AD サービス プリンシパルを作成します ([Azure RBAC の詳細をご覧ください](../role-based-access-control/overview.md))。
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    JSON 出力の `appId` と `password` の値は、次の手順で使用します。


1. 前のコマンドの出力の `appId` を使用して、新しいサービス プリンシパルの `objectId` を取得します。
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    このコマンドの出力は `objectId` です。これは、以下の Azure Resource Manager テンプレートで使用されます

1. 後で Azure Resource Manager テンプレートのデプロイで使用されるパラメーター ファイルを作成します。
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    **Kubernetes RBAC** が有効のクラスターをデプロイするには、`aksEnableRBAC` フィールドを `true` に設定します

## <a name="deploy-components"></a>コンポーネントをデプロイする
この手順では、サブスクリプションに次のコンポーネントを追加します。

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- 2 つの[サブネット](../virtual-network/virtual-networks-overview.md)を含む [Virtual Network](../virtual-network/virtual-networks-overview.md)
- [パブリック IP アドレス](../virtual-network/virtual-network-public-ip-address.md)
- [AAD ポッド ID](https://github.com/Azure/aad-pod-identity/blob/master/README.md) で使用される[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md)

1. Azure Resource Manager テンプレートをダウンロードし、必要に応じてそのテンプレートを変更します。
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. `az cli` を使用して Azure Resource Manager テンプレートをデプロイします。 これには最大 5 分かかることがあります。
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. デプロイが完了したら、`deployment-outputs.json` という名前のファイルにデプロイ出力をダウンロードします。
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Azure Application Gateway イングレス コントローラーを設定する

前のセクションの手順に従って、新しい AKS クラスターと Application Gateway を作成し、構成しました。 これで、新しい Kubernetes インフラストラクチャにサンプル アプリとイングレス コントローラーをデプロイする準備ができました。

### <a name="setup-kubernetes-credentials"></a>Kubernetes 資格情報を設定する
次の手順では、新しい Kubernetes クラスターへの接続に使用する [kubectl](https://kubectl.docs.kubernetes.io/) コマンドを設定する必要があります。 [Cloud Shell](https://shell.azure.com/) には `kubectl` が既にインストールされています。 `az` CLI を使用して、Kubernetes の資格情報を取得します。

新しくデプロイされた AKS の資格情報を取得します ([詳細はこちら](../aks/kubernetes-walkthrough.md#connect-to-the-cluster))。
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>AAD ポッド ID をインストールする
  Azure Active Directory ポッド ID は、[Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) へのトークンベースのアクセスを提供します。

  [AAD ポッド ID](https://github.com/Azure/aad-pod-identity) によって、次のコンポーネントが Kubernetes クラスターに追加されます。
   * Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`、`AzureAssignedIdentity`、`AzureIdentityBinding`
   * [Managed Identity Controller (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) コンポーネント
   * [Node Managed Identity (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) コンポーネント


AAD ポッド ID をクラスターにインストールするには、次のようにします。

   - *Kubernetes RBAC が有効* の AKS クラスター

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *Kubernetes RBAC が無効* の AKS クラスター

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Helm のインストール
[Helm](../aks/kubernetes-helm.md) は、Kubernetes 用のパッケージ マネージャーです。 これを利用して `application-gateway-kubernetes-ingress` パッケージをインストールします。

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

### <a name="install-ingress-controller-helm-chart"></a>イングレス コントローラーの Helm Chart をインストールする

1. 上記で作成した `deployment-outputs.json` ファイルを使用し、次の変数を作成します。
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. 新しくダウンロードした helm-config.yaml を編集し、`appgw` と `armAuth` のセクションに入力します。
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   値:
     - `verbosityLevel`:AGIC ログ インフラストラクチャの詳細レベルを設定します。 使用できる値については、「[ログ レベル](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)」を参照してください。
     - `appgw.subscriptionId`:Application Gateway が存在する Azure サブスクリプション ID。 例: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`:Application Gateway が作成された Azure リソース グループの名前。 例: `app-gw-resource-group`
     - `appgw.name`:Application Gateway の名前。 例: `applicationgatewayd0f0`
     - `appgw.shared`:このブール型のフラグは、既定で `false` に設定する必要があります。 [共有 Application Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway) が必要な場合は、`true` に設定します。
     - `kubernetes.watchNamespace`:AGIC で監視する名前空間を指定します。 これには、単一の文字列値、または名前空間のコンマ区切り一覧を指定できます。
    - `armAuth.type`: `aadPodIdentity` または `servicePrincipal` を指定できます
    - `armAuth.identityResourceID`:Azure マネージド ID のリソース ID
    - `armAuth.identityClientId`:ID のクライアント ID。 ID の詳細については、以下を参照してください
    - `armAuth.secretJSON`:サービス プリンシパル シークレットの種類を選択した場合 (`armAuth.type` が `servicePrincipal` に設定されている場合) にのみ必要です 


   > [!NOTE]
   > `identityResourceID` と `identityClientID` は、「[コンポーネントのデプロイ](ingress-controller-install-new.md#deploy-components)」手順で作成された値であり、次のコマンドを使用すると再び取得できます。
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > 上記のコマンドの `<resource-group>` は、Application Gateway のリソース グループです。 `<identity-name>` は、作成された ID の名前です。 特定のサブスクリプションのすべての ID は、`az identity list` を使用して一覧表示できます。


1. Application Gateway イングレス コントローラー パッケージをインストールします。

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>サンプル アプリをインストールする
Application Gateway、AKS、AGIC がインストールされたので、[Azure Cloud Shell](https://shell.azure.com/) を使用してサンプル アプリをインストールできます。

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

または、次を実行できます。

* 上記の YAML ファイルをダウンロードします。

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* YAML ファイルを適用します。

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>その他の例
この[攻略ガイド](ingress-controller-expose-service-over-http-https.md)には、Application Gateway を使用して、HTTP または HTTPS 経由で AKS サービスをインターネットに公開する方法の例が含まれています。
