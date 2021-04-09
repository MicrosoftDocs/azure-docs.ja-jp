---
title: Windows コンテナーを操作する
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Windows コンテナーを持つ既存のクラスターでの Azure Dev Spaces の実行方法について説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Windows コンテナー
ms.openlocfilehash: 682af37b79083fb360a17f87624f69239c03549d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198185"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Azure Dev Spaces を使用した Windows コンテナーの操作

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces は、新規と既存の両方の Kubernetes 名前空間で有効にすることができます。 Azure Dev Spaces は、Linux コンテナーで実行されるサービスを実行し、インストルメント化します。 それらのサービスは、同じ名前空間で Windows コンテナーで実行されるアプリケーションを操作することもできます。 この記事では、既存の Windows コンテナーを持つ名前空間で Dev Spaces を使用してサービスを実行する方法について説明します。 現時点では、Azure Dev Spaces を使用して、Windows コンテナーのデバッグやアタッチを行うことはできません。

## <a name="set-up-your-cluster"></a>クラスターのセットアップ

この記事では、Linux と Windows の両方のノード プールを持つクラスターが既にあることを前提としています。 Linux と Windows のノード プールを持つクラスターを作成する必要がある場合は、[こちら][windows-container-cli]の手順に従ってください。

Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使ってクラスターに接続します。 Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get][kubectl-get] コマンドを使用します。

```azurecli-interactive
kubectl get nodes
```

次の出力例は、Windows と Linux の両方のノードを持つクラスターを示しています。 次に進む前に、各ノードの状態が *Ready* であることを確認してください。

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Windows ノードに [taint][using-taints] を適用します。 Windows ノードに taint があると、Dev Spaces がその Windows ノードで Linux コンテナーの実行をスケジューリングしなくなります。 次のコマンド例では、前の例の Windows ノード *aksnpwin987654* に taint を適用しています。

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> ノードに taint を適用する場合、そのノードでサービスを実行するには、サービスのデプロイ テンプレートで適合する toleration を構成する必要があります。 このサンプル アプリケーションは、前のコマンドで構成した taint と[適合する toleration][sample-application-toleration-example] を使用して既に構成されています。

## <a name="run-your-windows-service"></a>Windows サービスの実行

AKS クラスターで Windows サービスを実行し、状態が *Running* であることを確認します。 この記事では、[サンプル アプリケーション][sample-application]を使用して、クラスターで実行される Windows サービスと Linux サービスのデモンストレーションを行います。

このサンプル アプリケーションを GitHub から複製し、`dev-spaces/samples/existingWindowsBackend/mywebapi-windows` ディレクトリに移動します。

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

サンプル アプリケーションでは、クラスターで Windows サービスを実行するために [Helm 3][helm-installed] が使用されます。 `charts` ディレクトリに移動し、Helm を使用して Windows サービスを実行します。

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

上のコマンドでは、Helm を使用して *dev* 名前空間で Windows サービスを実行しています。 *dev* という名前空間がない場合は作成されます。

`kubectl get pods` コマンドを使用して、Windows サービスがクラスターで実行されていることを確認します。 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Azure Dev Spaces の有効化

Windows サービスの実行に使用した名前空間と同じ名前空間で Dev Spaces を有効にします。 次のコマンドを実行すると、*dev* 名前空間で Dev Spaces が有効になります。

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Dev Spaces 向けに Windows サービスを更新

コンテナーが既に実行されている既存の名前空間で Dev Spaces を有効にすると、既定では Dev Spaces がその名前空間で実行される新しいコンテナーをすべてインストルメント化しようとします。 また Dev Spaces は、名前空間で既に実行されているサービス用に作成された新しいコンテナーもすべてインストルメント化しようとします。 名前空間で実行されているコンテナーが Dev Spaces によってインストルメント化されないようにするには、`deployment.yaml` に *no-proxy* ヘッダーを追加します。

`existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` ファイルに `azds.io/no-proxy: "true"` を追加します。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

`helm list` を使用して、Windows サービスのデプロイを一覧表示します。

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

上の例では、デプロイの名前が *windows-service* となっています。 `helm upgrade` を使用して Windows サービスを新しい構成で更新します。

```cmd
helm upgrade windows-service . --namespace dev
```

`deployment.yaml` を更新したため、Dev Spaces がサービスをインストルメント化しようとすることはありません。

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Azure Dev Spaces を使用した Linux アプリケーションの実行

`webfrontend` ディレクトリに移動し、コマンド `azds prep` と `azds up` を使用してクラスターで Linux アプリケーションを実行します。

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

`azds prep --enable-ingress` コマンドでは、アプリケーションの Helm チャートと Dockerfile が生成されます。

> [!TIP]
> プロジェクトの [Dockerfile と Helm チャート](../how-dev-spaces-works-prep.md#prepare-your-code)は、対象のコードをビルドして実行するために Azure Dev Spaces によって使用されますが、プロジェクトのビルドおよび実行方法を変更する場合は、これらのファイルを変更することができます。

`azds up` コマンドでは、名前空間でサービスが実行されます。

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

azds up コマンドの出力に示されているパブリック URL を開くと、サービスが実行されていることを確認できます。 この例のパブリック URL は `http://dev.webfrontend.abcdef0123.eus.azds.io/` です。 ブラウザーでサービスに移動し、上部の *[バージョン情報]* をクリックします。 コンテナーが使用している Windows のバージョンが記載されたメッセージが、*mywebapi* サービスから表示されることを確認します。

![mywebapi から Windows のバージョンを通知するサンプル アプリ](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
