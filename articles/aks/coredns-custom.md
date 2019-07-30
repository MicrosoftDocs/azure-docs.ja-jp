---
title: Azure Kubernetes Service (AKS) 用に CoreDNS をカスタマイズする
description: Azure Kubernetes Service (AKS) を使用して、サブドメインを追加したりカスタム DNS エンドポイントを拡張したりするために CoreDNS をカスタマイズする方法について説明します
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 247665f58dd064565f0e9aebc9859e97ce0ab0c0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836972"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Azure Kubernetes Service で CoreDNS をカスタマイズする

Azure Kubernetes Service (AKS) は、すべての *1.12.x* 以降のクラスターでクラスター DNS の管理と解決に [CoreDNS][coredns] プロジェクトを使用します。 以前は、kube-dns プロジェクトが使用されました。 この kube-dns プロジェクトは非推奨となっています。 CoreDNS のカスタマイズおよび Kubernetes について詳しくは、[公式のアップ ストリーム ドキュメント][corednsk8s]を参照してください。

AKS はマネージド サービスであるため、CoreDNS のメイン構成 (*CoreFile*) を変更することはできません。 代わりに、既定の設定をオーバーライドするには、Kubernetes *ConfigMap* を使用してください。 既定の AKS CoreDNS ConfigMaps を表示するには、`kubectl get configmaps coredns -o yaml` コマンドを使用してください。

この記事では、AKS の CoreDNS の基本的なカスタマイズ オプションに ConfigMaps を使用する方法を説明します。

> [!NOTE]
> `kube-dns` では、Kubernetes 構成マップを介してさまざまな[カスタマイズ オプション][kubednsblog]が提供されていました。 CoreDNS には kube-dns との下位互換性は**ありません**。 以前に使用したカスタマイズはすべて、CoreDNS で使用するために更新する必要があります。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、AKS のクイックスタートの、[Azure CLI を使用][aks-quickstart-cli]に関するページと、Azure portal に関するページor [using the Azure portal][aks-quickstart-portal]を参照してください。

## <a name="what-is-supportedunsupported"></a>サポート対象/サポート対象外

組み込みの CoreDNS プラグインはすべてサポート対象です。 アドオン/サード パーティ製のプラグインはサポート対象外です。 

## <a name="rewrite-dns"></a>DNS を書き換える

シナリオの 1 つとして、その場で DNS 名の書き換えを実行する場合があります。 次の例では、`<domain to be written>` を独自の完全修飾ドメイン名に置き換えます。 `corednsms.yaml` という名前のファイルを作成し、次の構成例を貼り付けます。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

[kubectl apply configmap][kubectl-apply] コマンドを使用して ConfigMap を作成し、YAML マニフェストの名前を指定します。

```console
kubectl apply -f corednsms.yaml
```

カスタマイズが適用されたことを確認するには、[kubectl get configmaps][kubectl-get] を使用し、*coredns-custom* ConfigMap を指定します。

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

ここで、ConfigMap の再読み込みを CoreDNS に強制します。 [kubectl delete pod][kubectl delete] コマンドは破壊的なコマンドではなく、停止時間も発生しません。 `kube-dns` ポッドが削除され、Kubernetes スケジューラによってそれらのポッドが再作成されます。 それらの新しいポッドには TTL 値の変更が含まれています。

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 上記のコマンドは正しいです。 `coredns` の変更中は、デプロイは **kube-dns** 名によって実行されます。

## <a name="custom-proxy-server"></a>カスタム プロキシ サーバー

ネットワーク トラフィックにプロキシ サーバーを指定する必要がある場合は、DNS をカスタマイズするための ConfigMap を作成できます。 次の例では、`proxy` の名前とアドレスを自分の環境の値で更新します。 `corednsms.yaml` という名前のファイルを作成し、次の構成例を貼り付けます。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        proxy foo.com 1.1.1.1
    }
```

前の例と同様に、[kubectl apply configmap][kubectl-apply] コマンドを使用して ConfigMap を作成し、ご自分の YAML マニフェストの名前を指定します。次に、CoreDNS で kubectl delete pod を使用して ConfigMap を作成し、Kubernetes スケジューラでそれらを再作成できるようにします。command and specify the name of your YAML manifest. Then, force CoreDNS to reload the ConfigMap using the [kubectl delete pod][kubectl delete]

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>カスタム ドメインを使用する

内部的にしか解決できないカスタム ドメインを構成する必要が生じる場合があります。 たとえば、有効な最上位レベルのドメインではないカスタム ドメイン *puglife.local* を解決したい場合があります。 カスタム ドメインの ConfigMap がないと、AKS クラスターはアドレスを解決できません。

次の例では、自分の環境の値で、トラフィックの送信先となるカスタム ドメインと IP アドレスを更新します。 `corednsms.yaml` という名前のファイルを作成し、次の構成例を貼り付けます。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

前の例と同様に、[kubectl apply configmap][kubectl-apply] コマンドを使用して ConfigMap を作成し、ご自分の YAML マニフェストの名前を指定します。次に、CoreDNS で kubectl delete pod を使用して ConfigMap を作成し、Kubernetes スケジューラでそれらを再作成できるようにします。command and specify the name of your YAML manifest. Then, force CoreDNS to reload the ConfigMap using the [kubectl delete pod][kubectl delete]

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>スタブ ドメイン

CoreDNS を使用してスタブ ドメインを構成することもできます。 次の例では、自分の環境の値でカスタム ドメイン と IP アドレスを更新します。 `corednsms.yaml` という名前のファイルを作成し、次の構成例を貼り付けます。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

前の例と同様に、[kubectl apply configmap][kubectl-apply] コマンドを使用して ConfigMap を作成し、ご自分の YAML マニフェストの名前を指定します。次に、CoreDNS で kubectl delete pod を使用して ConfigMap を作成し、Kubernetes スケジューラでそれらを再作成できるようにします。command and specify the name of your YAML manifest. Then, force CoreDNS to reload the ConfigMap using the [kubectl delete pod][kubectl delete]

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>ホストのプラグイン

組み込みのプラグインがすべてサポートされるということは、CoreDNS [Hosts][coredns hosts] プラグインもカスタマイズして使用できることを意味します。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="next-steps"></a>次の手順

この記事では、CoreDNS カスタマイズのシナリオ例をいくつか紹介しました。 CoreDNS プロジェクトについては、[CoreDNS アップストリーム プロジェクト ページ][coredns]を参照してください。

コア ネットワークの概念の詳細については、「[AKS でのアプリケーションに対するネットワークの概念][concepts-network]」を参照してください。

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
