---
title: Azure Dev Spaces でプライベート Helm リポジトリを使用する方法
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Azure Dev Space でプライベート Helm リポジトリを使用します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー, Helm
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960339"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Azure Dev Spaces でプライベート Helm リポジトリを使用する

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

[Helm][helm] は、Kubernetes 用のパッケージ マネージャーです。 Helm では、依存関係をパッケージ化するために[チャート][helm-chart]形式が使用されます。 Helm チャートは、パブリックまたはプライベートのリポジトリに格納されます。 Azure Dev Spaces では、アプリケーションを実行するときに、パブリックリ ポジトリからのみ Helm チャートが取得されます。 Helm リポジトリがプライベートである場合、または Azure Dev Spaces でアクセスできない場合は、そのリポジトリからアプリケーションにチャートを直接追加できます。 チャートを直接追加すると、Azure Dev Spaces で、プライベート Helm リポジトリにアクセスしなくてもアプリケーションを実行することができます。

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>プライベート Helm リポジトリをローカル コンピューターに追加する

[helm repo add][helm-repo-add] と [helm repo update][helm-repo-update] を使用して、ローカル コンピューターからプライベート Helm リポジトリにアクセスします。

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>アプリケーションにチャートを追加する

プロジェクトのディレクトリに移動して、`azds prep` を実行します。

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep` コマンドを実行すると、[対象のプロジェクトの Dockerfile と Helm チャート](../how-dev-spaces-works-prep.md#prepare-your-code)の生成が試行されます。 これらのファイルは、対象のコードをビルドして実行するために Azure Dev Spaces によって使用されますが、プロジェクトのビルドおよび実行方法を変更する場合は、これらのファイルを変更することができます。

アプリケーションのチャート ディレクトリにチャートを含む [requirements.yaml][helm-requirements] ファイルを作成します。 たとえば、アプリケーションの名前が *app1* の場合は、*charts/app1/requirements.yaml* を作成します。

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

アプリケーションのチャート ディレクトリに移動し、[helm dependency update][helm-dependency-update] を使用して、アプリケーションの Helm 依存関係を更新し、プライベート リポジトリからチャートをダウンロードします。

```cmd
helm dependency update
```

*tgz* ファイルを含む *charts* サブディレクトリが、アプリケーションのチャート ディレクトリに追加されていることを確認します。 たとえば、*charts/app1/charts/mychart-0.1.0.tgz* のようになります。

チャートをプライベート Helm リポジトリからダウンロードして、プロジェクトに追加しました。 Dev Spaces でこの依存関係の更新が試行されないように、*requirements.yaml* ファイルを削除します。

## <a name="run-your-application"></a>アプリケーションを実行する

プロジェクトのルート ディレクトリに移動し、`azds up` を実行して、アプリケーションが開発スペースで正常に実行されていることを確認します。

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>次のステップ

[Helm とそのしくみ][helm]についてさらに学習します。

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
