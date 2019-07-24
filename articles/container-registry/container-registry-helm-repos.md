---
title: Azure Container Registry の Helm リポジトリを使用する
description: Azure Container Registry で Helm リポジトリを使用してアプリケーションのグラフを保存する方法
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551995"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>アプリケーションのグラフに Helm リポジトリとして Azure Container Registry を使用する

アプリケーションを Kubernetes 用に簡単に管理しデプロイするには、[Helm パッケージ マネージャーのオープン ソース][helm]を使用します。 Helm を使用すると、アプリケーションは Helm グラフ リポジトリに保存される*グラフ*として定義されます。 これらのグラフでは、構成と依存関係が定義され、アプリケーションのライフサイクル全体でバージョン管理できます。 Azure Container Registry は、Helm グラフ リポジトリ用のホストとして使用できます。

Azure Container Registry にはプライベートのセキュリティで保護された Helm グラフ リポジトリがあり、ビルド パイプラインや他の Azure サービスと統合できます。 Azure Container Registry の Helm グラフ リポジトリには、冗長性のため、デプロイの近くにグラフを保持する geo レプリケーション機能が含まれます。 グラフで使用されるストレージに対してのみ課金が発生し、すべての Azure Container Registry の価格レベルで使用できます。

この記事では、Azure Container Registry に格納されている Helm グラフ リポジトリを使用する方法について説明します。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次の前提条件を満たす必要があります。

- **Azure Container Registry** - コンテナー レジストリを Azure サブスクリプションで作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。
- **Helm クライアント バージョン 2.11.0 (RC バージョンではない) 以降** - 現行バージョンを調べるには、`helm version` を実行してください。 また、Kubernetes クラスター内で開始される Helm サーバー (Tiller) も必要です。 必要に応じて、[Azure Kubernetes Service クラスターを作成][aks-quickstart]できます。 Helm のインストール方法とアップグレード方法について詳しくは、「[Helm のインストール][helm-install]」をご覧ください。
- **Azure CLI バージョン 2.0.46 以降** - バージョンを見つけるには `az --version` を実行してください。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="add-a-repository-to-helm-client"></a>Helm クライアントにリポジトリを追加する

Helm リポジトリは、Helm グラフを保存できる HTTP サーバーです。 Azure Container Registry では、Helm グラフ用の記憶域を提供でき、リポジトリでのグラフの追加や削除に応じてインデックスの定義を管理できます。

Helm グラフ リポジトリとして Azure Container Registry を追加するには、Azure CLI を使用します。 このアプローチでは、Helm クライアントは、Azure Container Registry によってバックアップされるリポジトリの URI と資格情報で更新されます。 このリポジトリ情報を手動で指定する必要はなく、資格情報がコマンドの履歴等で公開されることはありません。

必要に応じて、Azure CLI にログインし、プロンプトに従ってください。

```azurecli
az login
```

[az configure][az-configure] コマンドを使用して Azure Container Registry の名前で Azure CLI の既定を構成します。 次の例では、`<acrName>` がレジストリの名前と置き換えられます。

```azurecli
az configure --defaults acr=<acrName>
```

ここで、[az acr helm repo add][az-acr-helm-repo-add]コマンドを使用して Helm クライアントに Azure Container Registry Helm グラフのリポジトリを追加します。 このコマンドは、Helm クライアントによって使用される Azure コンテナー レジストリの認証トークンを取得します。 認証トークンは 1 時間有効です。 `docker login` と同様に、このコマンドを将来の CLI セッションで実行し、Azure Container Registry Helm のグラフレジストリで Helm クライアントを認証することができます。

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>グラフをリポジトリに追加する

この記事では、パブリックの Helm *安定した*リポジトリから既存の Helm グラフを取得してみましょう。 *安定した*リポジトリとは、一般的なアプリケーション グラフを含む、キュレーションされた公開リポジトリです。 パッケージ管理者は、Docker Hub が共通のコンテナ イメージ用にパブリック レジストリを提供するのと同じ方法で、*安定した* リポジトリにグラフを提出できます。 パブリックの*安定した*リポジトリからダウンロードされたグラフは、プライベートの Azure Container Registry リポジトリにプッシュできます。 ほとんどのシナリオで、開発したアプリケーション用の独自のグラフをビルドし、アップロードすることになります。 独自の Helm グラフを作成する方法の詳細については、[Helm グラフの開発][develop-helm-charts]をご覧ください。

最初に、ディレクトリを *~/acr-helm* に作成し、既存の*stable/wordpress* グラフ をダウンロードします。

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

ダウンロードしたグラフを一覧表示し、ファイル名に含まれている Wordpress バージョンを確認します。 `helm fetch stable/wordpress` コマンドでは特定のバージョンを指定しなかったため、*最新*バージョンがフェッチされました。 すべての Helm グラフには、[SemVer 2][semver2]標準に従ったファイル名のバージョン番号が含まれます。 次の出力例では、Wordpress グラフのバージョンは *2.1.10* です。

```
$ ls

wordpress-2.1.10.tgz
```

ここで、Azure CLI [az acr helm push][az-acr-helm-push] コマンドを使用して Azure Container Registry の Helm グラフ リポジトリにグラフをプッシュします。 前の手順でダウンロードした Helm グラフの名前を *wordpress-2.1.10.tgz* などに指定します。

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

しばらくすると、次の出力例に示すように Azure CLI は、グラフが保存されていることを報告します。

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>リポジトリのグラフ一覧

Helm クライアントは、リモート リポジトリの内容のローカル キャッシュされたコピーを保持します。 リモート リポジトリを変更しても、Helm クライアントがローカルに認識している使用可能なグラフの一覧が自動的に更新されることはありません。 リポジトリ全体でグラフを検索すると、Helm がローカルにキャッシュされたインデックスを使用します。 前の手順でアップロードしたグラフを使用するには、ローカルの Helm リポジトリのインデックスを更新する必要があります。 Helm クライアントでリポジトリのインデックスを更新したり、Azure CLI を使用してリポジトリのインデックスを更新することができます。 グラフを独自のリポジトリに追加するたびに、次の手順を完了する必要があります。

```azurecli
az acr helm repo add
```

リポジトリに保存されているグラフと、ローカルに使用できる更新されたインデックスを使用して、通常の Helm クライアント コマンドを使用して検索またはインストールを実行できます。 リポジトリ内のすべてのグラフを表示するには、`helm search <acrName>` を使用します。 次のように独自の Azure Container Registry 名を提供します。

```console
helm search <acrName>
```

次の出力例に示すように、前の手順でプッシュした Wordpress グラフが一覧されます。

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Azure CLI の [az acr helm list][az-acr-helm-list] を使用してグラフを一覧することもできます。

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Helm グラフの情報を表示する

リポジトリ内の特定のグラフの情報を表示する場合も通常の Helm クライアントを使用できます。 *wordpress* という名前のグラフの情報を見るには、`helm inspect` を使用します。

```console
helm inspect <acrName>/wordpress
```

バージョン番号を指定しない場合、*最新*バージョンが使用されます。 次の縮約された出力例が示すように、グラフに関する詳細情報が Helm によって返されます。

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Azure CLI [az acr helm show][az-acr-helm-show] コマンドを使用してグラフの情報を表示することもできます。 ここでも*最新*バージョンのグラフが既定で返されます。 `--version` をアペンドすれば、*2.1.10* など、特定バージョンのグラフを一覧することができます。

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Helm グラフをリポジトリからインストールする

リポジトリ内の Helm グラフは、リポジトリ名を指定して、グラフ名を指定することでインストールされます。 Wordpress のグラフをインストールするには、次のように Helm クライアントを使用します。

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Azure Container Registry の Helm グラフ リポジトリにプッシュして、後で新しい CLI セッションに戻る場合、ローカルの Helm クライアントは更新された認証トークンを必要とします。 新しい認証トークンを取得するには、[az acr helm repo add][az-acr-helm-repo-add] コマンドを使用します。

インストール プロセス中には、次の手順が完了します。

- Helm クライアントは、ローカル リポジトリのインデックスを検索します。
- 対応するグラフが Azure Container Registry リポジトリからダウンロードされます。
- グラフは、Tiller を使用して Kubernetes クラスターにデプロイされます。

次の縮約された出力例は、Helm グラフを使用してデプロイされた Kubernetes リソースを示したものです。

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Helm グラフをリポジトリから削除する

リポジトリからグラフを削除するには、[az acr helm delete][az-acr-helm-delete] コマンドを使用します。 グラフの名前 (*wordpress* など) および削除するバージョン (*2.1.10* など) を指定します。

```azurecli
az acr helm delete wordpress --version 2.1.10
```

名前付きのグラフのすべてのバージョンを削除する場合は、`--version` パラメーターを除外します。

グラフが `helm search <acrName>` に返されていきます。 ここでも、Helm クライアントは、リポジトリで使用可能なグラフの一覧を自動的に更新することはありません。 Helm クライアントのリポジトリ インデックスを更新するには、同じく [az acr helm repo add][az-acr-helm-repo-add] コマンドを使用します。

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>次の手順

この記事では、パブリックの*安定した*リポジトリから既存の Helm グラフを使用しました。 Helm グラフを作成してデプロイする方法の詳細については、[Helm グラフの開発][develop-helm-charts]をご覧ください。

Helm グラフは、コンテナーのビルド プロセスの一部として使用できます。 詳細については、[Azure Container Registry タスクを使用する][acr-tasks]を参照してください。

Azure Container Registry を使用して管理する方法の詳細については、[ベスト プラクティス][acr-bestpractices]を参照してください。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
