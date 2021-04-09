---
title: Application Gateway イングレス コントローラーのトラブルシューティング
description: この記事では、Application Gateway イングレス コントローラーに関する一般的な質問や問題のトラブルシューティングを行う方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: f2b9f79f0914e645c736f8a577c46baa42587332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874612"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>イングレス コントローラーに関する一般的な質問または問題のトラブルシューティング

AKS および AGIC のインストールに関する問題をトラブルシューティングするための最も便利な方法は、[Azure Cloud Shell](https://shell.azure.com/) です。 [shell.azure.com](https://shell.azure.com/) から、またはリンクをクリックして、シェルを起動します。

[![埋め込みの起動](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>簡単な Kubernetes アプリでテストする

以下の手順では、次のことが想定されています。
  - 高度なネットワークが有効になっている AKS クラスターがある
  - AKS クラスターに AGIC がインストールされている
  - お使いの AKS クラスターと共有されている VNET 上に、Application Gateway が既にある

Application Gateway と AKS と AGIC のインストールが正しくセットアップされていることを確認するには、考えられる最も簡単なアプリをデプロイします。

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
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
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

上記のスクリプトのすべての行を一度にコピーして、[Azure Cloud Shell](https://shell.azure.com/) に貼り付けます。 `cat` から最後の `EOF` まで、コマンド全体をコピーしたことを確認してください。

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

AKS クラスターにアプリが正常にデプロイされると、新しいポッド、サービス、およびイングレスが作成されます。

[Cloud Shell](https://shell.azure.com/)`kubectl get pods -o wide` を使用して、ポッドの一覧を取得します。
"test-agic-app-pod" という名前のポッドが作成されているはずです。 それには IP アドレスが割り当てられます。 このアドレスは、AKS で使用される Application Gateway の VNET 内にある必要があります。

![一覧に test-agic-app-pod が含まれるポッドの一覧を示す Azure Cloud Shell の Bash ウィンドウのスクリーンショット。](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

サービスの一覧を取得します: `kubectl get services -o wide`。 "test-agic-app-service" という名前のサービスが表示されるはずです。

![一覧に test-agic-app-pod が含まれるサービスの一覧を示す Azure Cloud Shell の Bash ウィンドウのスクリーンショット。](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

イングレスの一覧を取得します: `kubectl get ingress`。 "test-agic-app-ingress" という名前のイングレス リソースが作成されているはずです。 リソースのホスト名は "test.agic.contoso.com" になります。

![一覧に test-agic-app-ingress が含まれるイングレスの一覧を示す Azure Cloud Shell の Bash ウィンドウのスクリーンショット。](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

ポッドの 1 つは AGIC です。 `kubectl get pods` を実行するとポッドの一覧が表示され、その 1 つは "ingress-azure" で始まっています。 `kubectl logs <name-of-ingress-controller-pod>` を使用してそのポッドのすべてのログを取得し、デプロイが成功したことを確認します。 デプロイが成功すると、ログに次の行が追加されます。
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

または、[Cloud Shell](https://shell.azure.com/) で `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` を使用して、Application Gateway の構成が成功したことを示す行のみを取得できます。`<ingress-azure....>` は、AGIC ポッドの正確な名前です。

Application Gateway には、次の構成が適用されます。

- リスナー: ![listener](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- ルーティング規則: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- バックエンド プール:
  - バックエンド アドレス プールには 1 つの IP アドレスがあり、それは前に `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png) で見たポッドの IP アドレスと一致します


最後に、[Cloud Shell](https://shell.azure.com/) 内から `cURL` コマンドを使用して、新しくデプロイされたアプリへの HTTP 接続を確立できます。

1. `kubectl get ingress` を使用して、Application Gateway のパブリック IP アドレスを取得します
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` を使用します

![cURL コマンドによってテスト アプリへの HTTP 接続が正常に確立されたことを示す Azure Cloud Shell の Bash ウィンドウのスクリーンショット。](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

`HTTP/1.1 200 OK` の結果では、Application Gateway と AKS と AGIC のシステムが想定どおりに動作していることが示されます。


## <a name="inspect-kubernetes-installation"></a>Kubernetes のインストールを調べる

### <a name="pods-services-ingress"></a>ポッド、サービス、イングレス
Application Gateway イングレス コントローラー (AGIC) では、次の Kubernetes リソースが継続的に監視されます。[デプロイ](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment)または[ポッド](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod)、[サービス](https://kubernetes.io/docs/concepts/services-networking/service/)、[イングレス](https://kubernetes.io/docs/concepts/services-networking/ingress/)


AGIC が想定どおりに機能するには、次のものが必要です。
  1. AKS には、1 つ以上の正常な **ポッド** が必要です。
     このことを、[Cloud Shell](https://shell.azure.com/) の `kubectl get pods -o wide --show-labels` で確認します。`apsnetapp` が含まれるポッドがある場合、出力は次のようになります。
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 1 つ以上の **サービス**。一致する `selector` ラベルで上のポッドを参照します。
     これを、[Cloud Shell](https://shell.azure.com/) の `kubectl get services -o wide` で確認します
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. `kubernetes.io/ingress.class: azure/application-gateway` で注釈が付けられた **イングレス**。上のサービスを参照します。[Cloud Shell](https://shell.azure.com/) の `kubectl get ingress -o wide --show-labels` でこれを確認します
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 上のイングレスの注釈を表示します: `kubectl get ingress aspnetapp -o yaml` (`aspnetapp` をお使いのイングレスの名前に置き換えます)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     イングレス リソースには、`kubernetes.io/ingress.class: azure/application-gateway` で注釈が付けられている必要があります。
 

### <a name="verify-observed-namespace"></a>監視対象の名前空間を確認する

* Kubernetes クラスターで既存の名前空間を取得します。 アプリはどのような名前空間で実行されていますか。 その名前空間は AGIC で監視されていますか。 監視対象の名前空間を適切に構成する方法については、[複数の名前空間のサポート](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support)に関するドキュメントを参照してください。

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC ポッドは、`default` 名前空間に存在する必要があります (`NAMESPACE` 列を参照)。 正常なポッドでは、`STATUS` 列に `Running` と表示されます。 少なくとも 1 つの AGIC ポッドが必要です。

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* AGIC ポッドが正常でない場合 (上記のコマンドの `STATUS` 列が `Running` ではない):
  - ログを取得して理由を調べます: `kubectl logs <pod-name>`
  - ポッドの前のインスタンスの場合: `kubectl logs <pod-name> --previous`
  - ポッドを説明して詳細なコンテキストを取得します: `kubectl describe pod <pod-name>`


* Kubernetes の[サービス](https://kubernetes.io/docs/concepts/services-networking/service/) リソースと[イングレス](https://kubernetes.io/docs/concepts/services-networking/ingress/) リソースがありますか。
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* [イングレス](https://kubernetes.io/docs/concepts/services-networking/ingress/)には `kubernetes.io/ingress.class: azure/application-gateway` で注釈が付けられていますか。 AGIC では、この注釈を持つ Kubernetes イングレス リソースのみが監視されます。
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC では、特定の重大なエラーに対して Kubernetes イベントが出力されます。 それらを表示できます。
  - ターミナルでは `kubectl get events --sort-by=.metadata.creationTimestamp` を使用します
  - ブラウザーでは [Kubernetes Web UI (ダッシュボード)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) を使用します


## <a name="logging-levels"></a>ログ記録のレベル

AGIC には、3 つのログ記録レベルがあります。 レベル 1 は既定で、ログ行数は最小限になります。
一方、レベル 5 では、ARM に適用された構成のサニタイズ済みコンテンツを含むすべてのログが表示されます。

Kubernetes コミュニティでは、[kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) ツール用に 9 レベルのログ記録が確立されています。 このリポジトリでは、セマンティクスが似た次の 3 つを使用しています。


| 詳細度 | 説明 |
|-----------|-------------|
|  1        | 既定のログ レベル。スタートアップの詳細、警告、エラーが表示されます |
|  3        | イベントと変更に関する拡張情報、作成されたオブジェクトの一覧 |
|  5        | マーシャリングされたオブジェクトをログに記録します。ARM に適用されたサニタイズ済み JSON 構成が表示されます |


詳細レベルは、[helm-config.yaml](#sample-helm-config-file) ファイルの `verbosityLevel` 変数を使用して調整できます。 詳細レベルを `5` に上げると、[ARM](../azure-resource-manager/management/overview.md) にディスパッチされた JSON 構成が取得されます。
  - [helm-config.yaml](#sample-helm-config-file) 内の 1 行に `verbosityLevel: 5` を追加し、再インストールします
  - `kubectl logs <pod-name>` でログを取得します

### <a name="sample-helm-config-file"></a>サンプルの Helm 構成ファイル
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