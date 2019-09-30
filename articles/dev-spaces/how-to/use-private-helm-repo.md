---
title: Azure Dev Spaces でプライベート Helm リポジトリを使用する方法
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Azure Dev Space でプライベート Helm リポジトリを使用します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー, Helm
manager: gwallace
ms.openlocfilehash: d0f2cb739a502d614ac329eef1fc57b2fb15cb38
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014278"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Azure Dev Spaces でプライベート Helm リポジトリを使用する

[Helm][helm] は、Kuberentes 用のパッケージ マネージャーです。 Helm では、依存関係をパッケージ化するために[チャート][helm-chart]形式が使用されます。 Helm チャートは、パブリックまたはプライベートのリポジトリに格納されます。 Azure Dev Spaces では、アプリケーションを実行するときに、パブリックリ ポジトリからのみ Helm チャートが取得されます。 Helm リポジトリがプライベートである場合、または Azure Dev Spaces でアクセスできない場合は、そのリポジトリからアプリケーションにチャートを直接追加できます。 チャートを直接追加すると、Azure Dev Spaces で、プライベート Helm リポジトリにアクセスしなくてもアプリケーションを実行することができます。

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>プライベート Helm リポジトリをローカル コンピューターに追加する

[helm repo add][helm-repo-add] と [helm repo update][helm-repo-update] を使用して、ローカル コンピューターからプライベート Helm リポジトリにアクセスします。

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>アプリケーションにチャートを追加する

プロジェクトのディレクトリに移動して、`azds prep` を実行します。

```cmd
azds prep --public
```

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

## <a name="next-steps"></a>次の手順

[Helm とそのしくみ][helm]についてさらに学習します。

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/developing_charts/
[helm-dependency-update]: https://helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/developing_charts/#chart-dependencies