---
title: Azure Kubernetes Service (AKS) 用に CoreDNS をカスタマイズする
description: Azure Kubernetes Service (AKS) を使用して、サブドメインを追加したりカスタム DNS エンドポイントを拡張したりするために CoreDNS をカスタマイズする方法について説明します
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368683"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Azure Kubernetes Service で CoreDNS をカスタマイズする

Azure Kubernetes Service (AKS) は、すべての *1.12.x* 以降のクラスターでクラスター DNS の管理と解決に [CoreDNS][coredns] プロジェクトを使用します。 以前は、kube-dns プロジェクトが使用されました。 この kube-dns プロジェクトは非推奨となっています。 CoreDNS のカスタマイズおよび Kubernetes について詳しくは、[公式のアップ ストリーム ドキュメント][corednsk8s]を参照してください。

AKS はマネージド サービスであるため、CoreDNS のメイン構成 (*CoreFile*) を変更することはできません。 代わりに、既定の設定をオーバーライドするには、Kubernetes *ConfigMap* を使用してください。 既定の AKS CoreDNS ConfigMaps を表示するには、`kubectl get configmaps --namespace=kube-system coredns -o yaml` コマンドを使用してください。

この記事では、AKS の CoreDNS の基本的なカスタマイズ オプションに ConfigMaps を使用する方法を説明します。 この方法は、CoreFile の使用といった他のコンテキストでの CoreDNS の構成とは異なります。 バージョンによって構成値が変わる可能性があるため、実行している CoreDNS のバージョンを確認してください。

> [!NOTE]
> `kube-dns` では、Kubernetes 構成マップを介してさまざまな[カスタマイズ オプション][kubednsblog]が提供されていました。 CoreDNS には kube-dns との下位互換性は **ありません**。 以前に使用したカスタマイズはすべて、CoreDNS で使用するために更新する必要があります。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

次の例のような構成を作成する場合、*data* セクション内の名前は *.server* または *.override* のいずれかで終わる必要があります。 この名前付け規則は、`kubectl get configmaps --namespace=kube-system coredns -o yaml` コマンドを使用して表示できる既定の AKS CoreDNS Configmap で定義されています。

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
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> CoreDNS サービス IP などの DNS サーバーにリダイレクトする場合、その DNS サーバーは、書き換えられたドメイン名を解決できる必要があります。

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

## <a name="custom-forward-server"></a>カスタム転送サーバー

ネットワーク トラフィックの転送サーバーを指定する必要がある場合は、DNS をカスタマイズするための ConfigMap を作成できます。 次の例では、`forward` の名前とアドレスを自分の環境の値で更新します。 `corednsms.yaml` という名前のファイルを作成し、次の構成例を貼り付けます。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

前述の例のように、[kubectl apply configmap][kubectl-apply] コマンドを使用して ConfigMap を作成し、YAML マニフェストの名前を指定します。 次に、Kubernetes スケジューラで再作成するために、[kubectl delete pod][kubectl delete] を使用して ConfigMap の再読み込みを CoreDNS に強制します。

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
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
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

前述の例のように、[kubectl apply configmap][kubectl-apply] コマンドを使用して ConfigMap を作成し、YAML マニフェストの名前を指定します。 次に、Kubernetes スケジューラで再作成するために、[kubectl delete pod][kubectl delete] を使用して ConfigMap の再読み込みを CoreDNS に強制します。

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
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
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

前述の例のように、[kubectl apply configmap][kubectl-apply] コマンドを使用して ConfigMap を作成し、YAML マニフェストの名前を指定します。 次に、Kubernetes スケジューラで再作成するために、[kubectl delete pod][kubectl delete] を使用して ConfigMap の再読み込みを CoreDNS に強制します。

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
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
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>DNS クエリのデバッグ用にログ記録を有効にする 

DNS クエリのログ記録を有効にするには、次の構成を coredns-custom ConfigMap に適用します。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>次のステップ

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
