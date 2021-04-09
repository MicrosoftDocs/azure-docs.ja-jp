---
title: Kubernetes および Helm と共に Speech サービス コンテナーを使用する
titleSuffix: Azure Cognitive Services
description: Kubernetes と Helm を使って音声テキスト変換とテキスト読み上げのコンテナー イメージを定義し、Kubernetes パッケージを作成します。 このパッケージは、オンプレミスの Kubernetes クラスターに展開されます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: aahi
ms.openlocfilehash: 78ac9ae4aa8611f50caa94c84d3e6c95e58fc91c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200735"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Kubernetes および Helm と共に Speech サービス コンテナーを使用する

オンプレミスの音声コンテナーを管理するための 1 つの方法は、Kubernetes と Helm を使用することです。 Kubernetes と Helm を使って音声テキスト変換とテキスト読み上げのコンテナー イメージを定義し、Kubernetes パッケージを作成します。 このパッケージは、オンプレミスの Kubernetes クラスターに展開されます。 最後に、展開されたサービスをテストする方法と、さまざまな構成オプションについて調べます。 Kubernetes オーケストレーションを使用せずに、Docker コンテナーを実行する方法の詳細については、「[Speech サービス コンテナーをインストールして実行する](speech-container-howto.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

オンプレミスの Speech コンテナーを使用する前の前提条件は次のとおりです。

| 必須 | 目的 |
|----------|---------|
| Azure アカウント | Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][free-azure-account] を作成してください。 |
| コンテナー レジストリのアクセス | Kubernetes でクラスターに Docker イメージをプルするには、コンテナー レジストリにアクセスする必要があります。 |
| Kubernetes CLI | コンテナー レジストリからの共有資格情報を管理するには、[Kubernetes CLI][kubernetes-cli] が必要です。 また、Kubernetes は、Kubernetes のパッケージ マネージャーである Helm の前に必要です。 |
| Helm CLI | Helm Chart (コンテナー パッケージ定義) のインストールに使用される [Helm CLI][helm-install] をインストールします。 |
|Speech リソース |これらのコンテナーを使用するためには、以下が必要です。<br><br>関連付けられている課金キーと課金エンドポイント URI を取得するための _Speech_ Azure リソース。 これらの値は、どちらも Azure portal の **Speech** の [概要] ページと [キー] ページで入手でき、コンテナーを起動するために必要です。<br><br>**{API_KEY}** : リソース キー<br><br>**{ENDPOINT_URI}** : エンドポイント URI の例: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>推奨されるホスト コンピューターの構成

詳しくは、[Speech サービスのコンテナー ホスト コンピューター][speech-container-host-computer]に関する記事をご覧ください。 この "*Helm チャート*" では、ユーザーが指定しているデコードの数 (同時要求数) に基づいて、CPU とメモリの要件が自動的に計算されます。 さらに、オーディオ/テキスト入力の最適化が `enabled` として構成されているかどうかに基づいて調整されます。 Helm チャートの既定値では、同時要求の数は 2、最適化は無効です。

| サービス | CPU/コンテナー | メモリ/コンテナー |
|--|--|--|
| **音声テキスト変換** | 1 つのデコーダーで、1,150 ミリコア以上が必要です。 `optimizedForAudioFile` が有効になっている場合は、1,950 ミリコアが必要です。 (既定値: 2 つのデコーダー) | 必須: 2 GB<br>上限: 4 GB |
| **音声合成** | 1 つの同時要求で、500 ミリコア以上が必要です。 `optimizeForTurboMode` が有効になっている場合は、1,000 ミリコアが必要です。 (既定値: 2 つの同時要求) | 必須: 1 GB<br> 上限: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes クラスターに接続する

ホスト コンピューターには使用可能な Kubernetes クラスターがあることが想定されます。 ホスト コンピューターへの Kubernetes クラスターの展開方法の概念を理解するには、[Kubernetes クラスターの展開](../../aks/tutorial-kubernetes-deploy-cluster.md)に関するこのチュートリアルをご覧ください。

## <a name="configure-helm-chart-values-for-deployment"></a>展開に対する Helm チャートの値を構成する

Microsoft によって提供されているすべてのパブリックに使用可能な Helm チャートについては、[Microsoft Helm Hub][ms-helm-hub] をご覧ください。 Microsoft Helm Hub で、**Cognitive Services Speech On-Premises Chart** を探します。 **Cognitive Services Speech On-Premises** チャートをインストールしますが、最初に明示的な構成で `config-values.yaml` ファイルを作成する必要があります。 最初に、Helm インスタンスに Microsoft リポジトリを追加しましょう。

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

次に、Helm チャートの値を構成します。 次の YAML をコピーし、`config-values.yaml` という名前のファイルに貼り付けます。 **Cognitive Services Speech On-Premises Helm Chart** のカスタマイズについて詳しくは、「[Helm チャートをカスタマイズする](#customize-helm-charts)」をご覧ください。 `# {ENDPOINT_URI}` と `# {API_KEY}` のコメントを独自の値に置き換えます。

```yaml
# These settings are deployment specific and users can provide customizations
# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` および `apikey` の値を指定しないと、サービスの有効期限は 15 分後に切れます。 同様に、サービスを利用できないため、検証が失敗します。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes パッケージ (Helm チャート)

"*Helm チャート*" には、`mcr.microsoft.com` コンテナー レジストリからプルする Docker イメージの構成が含まれます。

> [Helm チャート][helm-charts] は、関連する Kubernetes リソースのセットが記述されているファイルのコレクションです。 1 つのチャートを使って、memcached ポッドのような単純なものや、HTTP サーバー、データベース、キャッシュなどを含む完全な Web アプリ スタックのような複雑なものを、展開できます。

提供されている 「*Helm チャート*」では、テキスト読み上げサービスと音声テキスト変換サービス両方の Speech サービスの Docker イメージが、`mcr.microsoft.com` コンテナー レジストリからプルされます。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes クラスターに Helm チャートをインストールする

"*Helm チャート*" をインストールするには、[`helm install`][helm-install-cmd] コマンドを実行する必要があります。`<config-values.yaml>` は、適切なパスとファイル名の引数に置き換えます。 以下で参照されている `microsoft/cognitive-services-speech-onpremise` Helm チャートは、[こちらの Microsoft Helm Hub][ms-helm-hub-speech-chart] で入手できます。

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

インストールが正常に実行されると表示される出力の例を次に示します。

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes の展開が完了するには数分かかります。 ポッドとサービスの両方が適切に展開されて使用可能なことを確認するには、次のコマンドを実行します。

```console
kubectl get all
```

次のような出力結果が表示されます。

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Helm テストで Helm の展開を検証する

インストールされた Helm チャートでは、検証を行うのに便利な "*Helm テスト*" が定義されています。 これらのテストでは、サービスの準備状態が検証されます。 **音声テキスト変換** サービスと **テキスト読み上げ** サービスの両方を検証するため、[helm test][helm-test] コマンドを実行します。

```console
helm test onprem-speech
```

> [!IMPORTANT]
> ポッドの状態が `Running` でない場合、または展開が `AVAILABLE` 列に表示されない場合、これらのテストは失敗します。 これが完了するには 10 分以上かかるのでお待ちください。

これらのテストでは、さまざまな状態の結果が出力されます。

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

"*Helm テスト*" を実行する代わりに、`kubectl get all` コマンドから "*外部 IP*" アドレスおよび対応するポートを収集することもできます。 IP とポートを使用して、Web ブラウザーを開き、`http://<external-ip>:<port>:/swagger/index.html` に移動して、API Swagger ページを表示します。

## <a name="customize-helm-charts"></a>Helm チャートをカスタマイズする

Helm チャートは階層的です。 階層的であるためチャートを継承できます。また、特異性の概念に対応するので、継承された規則はいっそう固有の設定によってオーバーライドされます。

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>次のステップ

Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストールについて詳しくは、[こちらをご覧ください][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [Cognitive Services コンテナー][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md