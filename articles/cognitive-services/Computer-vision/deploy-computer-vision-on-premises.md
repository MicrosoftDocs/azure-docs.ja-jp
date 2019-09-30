---
title: Kubernetes と Helm と共に Computer Vision コンテナーを使用する
titleSuffix: Azure Cognitive Services
description: Computer Vision コンテナーを Azure Container Instance にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: dapine
ms.openlocfilehash: 1627aea958707eaaef6ee79908a17afc2e8f7b45
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068964"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Kubernetes と Helm と共に Computer Vision コンテナーを使用する

オンプレミスの Computer Vision コンテナーを管理するための 1 つの方法は、Kubernetes と Helm を使用することです。 Kubernetes と Helm を使ってテキスト認識コンテナー イメージを定義し、Kubernetes パッケージを作成します。 このパッケージは、オンプレミスの Kubernetes クラスターに展開されます。 最後に、デプロイされたサービスをテストする方法を検討します。 Kubernetes オーケストレーションを使用せずに Docker コンテナーを実行する方法の詳細については、「[テキスト認識コンテナーをインストールして実行する](computer-vision-how-to-install-containers.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

オンプレミスの Computer Vision コンテナーを使用する前の前提条件は次のとおりです。

|必須|目的|
|--|--|
| Azure アカウント | Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][free-azure-account] を作成してください。 |
| コンテナー レジストリのアクセス | Kubernetes でクラスターに Docker イメージをプルするには、コンテナー レジストリにアクセスする必要があります。 最初に、[コンテナー レジストリへのアクセスを要求する][vision-preview-access]必要があります。 |
| Kubernetes CLI | コンテナー レジストリからの共有資格情報を管理するには、[Kubernetes CLI][kubernetes-cli] が必要です。 また、Kubernetes は、Kubernetes のパッケージ マネージャーである Helm の前に必要です。 |
| Helm CLI | [Helm CLI][helm-install] のインストールの一環として、Helm を初期化する必要もあります。これにより、[Tiller][tiller-install] がインストールされます。 |
| Computer Vision リソース |コンテナーを使用するためには、以下が必要です。<br><br>Azure **Computer Vision** リソースとその関連する API キーおよびエンドポイント URI。 どちらの値も、対象リソースの概要ページとキー ページで使用でき、コンテナーを開始するために必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページで提供されるエンドポイント|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes クラスターに接続する

ホスト コンピューターには使用可能な Kubernetes クラスターがあることが想定されます。 ホスト コンピューターへの Kubernetes クラスターの展開方法の概念を理解するには、[Kubernetes クラスターの展開](../../aks/tutorial-kubernetes-deploy-cluster.md)に関するこのチュートリアルをご覧ください。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Kubernetes クラスターと Docker の資格情報を共有する

Kubernetes クラスターで `containerpreview.azurecr.io` コンテナー レジストリの構成済みイメージに対して `docker pull` を実行できるようにするには、Docker の資格情報をクラスターに転送する必要があります。 下の [`kubectl create`][kubectl-create] コマンドを実行し、コンテナー レジストリのアクセスの前提条件から提供された資格情報に基づいて、"*docker-registry シークレット*" を作成します。

適切なコマンド ライン インターフェイスから、次のコマンドを実行します。 `<username>`、`<password>`、`<email-address>` は、コンテナー レジストリの資格情報に置き換えてください。

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> `containerpreview.azurecr.io` コンテナー レジストリへのアクセス権が既にある場合は、代わりに汎用フラグを使って Kubernetes シークレットを作成できます。 Docker 構成 JSON に対して実行する次のコマンドを検討してください。
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

シークレットが正常に作成されると、次の出力がコンソールに表示されます。

```console
secret "containerpreview" created
```

シークレットが作成されたことを確認するには、`secrets` フラグを指定して [`kubectl get`][kubectl-get] を実行します。

```console
kuberctl get secrets
```

`kubectl get secrets` を実行すると、構成されているすべてのシークレットが表示されます。

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>展開に対する Helm チャートの値を構成する

まず、*text-recognizer* という名前のフォルダーを作成します。次の YAML コンテンツをコピーし、`Chart.yml` という名前の新しいファイルに貼り付けます。

```yaml
apiVersion: v1
name: text-recognizer
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-recognize-text to a Kubernetes cluster
```

Helm チャートの既定値を構成するために、次の YAML をコピーし、`values.yaml` という名前のファイルに貼り付けます。 `# {ENDPOINT_URI}` と `# {API_KEY}` のコメントを独自の値に置き換えます。

```yaml
# These settings are deployment specific and users can provide customizations

recognizeText:
  enabled: true
  image:
    name: cognitive-services-recognize-text
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-recognize-text
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` および `apikey` の値を指定しないと、サービスの有効期限は 15 分後に切れます。 同様に、サービスを利用できないため、検証が失敗します。

*text-recognizer* ディレクトリの下に *templates* フォルダーを作成します。 次の YAML をコピーし、`deployment.yaml` という名前のファイルに貼り付けます。 `deployment.yaml` ファイルは Helm テンプレートとして機能します。

> テンプレートによってマニフェスト ファイルが生成されます。マニフェスト ファイルは、Kubernetes が理解できる YAML 形式のリソース記述です。 [- Helm チャート テンプレート ガイド][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: text-recognizer
spec:
  template:
    metadata:
      labels:
        app: text-recognizer-app
    spec:
      containers:
      - name: {{.Values.recognizeText.image.name}}
        image: {{.Values.recognizeText.image.registry}}{{.Values.recognizeText.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.recognizeText.image.args.eula}}
        - name: billing
          value: {{.Values.recognizeText.image.args.billing}}
        - name: apikey
          value: {{.Values.recognizeText.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.recognizeText.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: text-recognizer
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: text-recognizer-app
```

このテンプレートでは、ロード バランサー サービスと、テキスト認識用のコンテナーまたはイメージのデプロイを指定しています。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes パッケージ (Helm チャート)

"*Helm チャート*" には、`containerpreview.azurecr.io` コンテナー レジストリからプルする Docker イメージの構成が含まれます。

> [Helm チャート][helm-charts] は、関連する Kubernetes リソースのセットが記述されているファイルのコレクションです。 1 つのチャートを使って、memcached ポッドのような単純なものや、HTTP サーバー、データベース、キャッシュなどを含む完全な Web アプリ スタックのような複雑なものを、展開できます。

提供されている "*Helm チャート*" では、Computer Vision サービスとテキスト認識サービス Docker イメージが、`containerpreview.azurecr.io` コンテナー レジストリからプルされます。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes クラスターに Helm チャートをインストールする

"*Helm チャート*" をインストールするには、[`helm install`][helm-install-cmd] コマンドを実行する必要があります。 必ず `text-recognizer` フォルダーの上のディレクトリから install コマンドを実行します。

```console
helm install text-recognizer --name text-recognizer
```

インストールが正常に実行されると表示される出力の例を次に示します。

```console
NAME:   text-recognizer
LAST DEPLOYED: Thu Aug 22 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                              READY  STATUS             RESTARTS  AGE
text-recognizer-57cb76bcf7-45sdh  0/1    ContainerCreating  0         0s

==> v1/Service
NAME             TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
text-recognizer  LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME             READY  UP-TO-DATE  AVAILABLE  AGE
text-recognizer  0/1    1           0          0s
```

Kubernetes の展開が完了するには数分かかります。 ポッドとサービスの両方が適切に展開されて使用可能なことを確認するには、次のコマンドを実行します。

```console
kubectl get all
```

次のような出力結果が表示されます。

```console
λ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/text-recognizer-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes        ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/text-recognizer   LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/text-recognizer   1/1     1            1           17s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/text-recognizer-57cb76bcf7   1         1         1       17s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>次の手順

Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストールについて詳しくは、[こちらをご覧ください][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [Cognitive Services コンテナー][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-preview-access]: computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
