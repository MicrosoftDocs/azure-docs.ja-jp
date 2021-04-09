---
title: プライベート コンテナー レジストリでパブリック コンテンツを管理する
description: Docker Hub やその他のパブリック コンテンツのパブリック イメージへの依存関係を管理するための Azure Container Registry の手法とワークフロー
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024740"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Azure Container Registry を使用してパブリック コンテンツを管理する

この記事では、[Azure コンテナー レジストリ](container-registry-intro.md)などのローカル レジストリを使用して、Docker Hub のコンテナー イメージなどのパブリック コンテンツのコピーを管理するための手法とワークフローの概要について説明します。 


## <a name="risks-with-public-content"></a>パブリック コンテンツに関するリスク

お使いの環境が、パブリック コンテナー イメージ、[Helm チャート](https://helm.sh/)、[Open Policy Agent](https://www.openpolicyagent.org/) (OPA) ポリシー、その他の成果物などのパブリック コンテンツに依存している場合があります。 たとえば、Docker Hub や別のパブリック レジストリから直接イメージをプルして、サービスのルーティングや `docker build FROM alpine` のために [nginx](https://hub.docker.com/_/nginx) を実行する場合があります。 

適切な制御なしでパブリック レジストリ コンテンツに依存すると、イメージの開発とデプロイのワークフローにリスクが生じる可能性があります。 リスクを軽減するには、できるだけパブリック コンテンツのローカル コピーを保持するようにします。 詳細については、[Open Container Initiative のブログ](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/)を参照してください。 

## <a name="authenticate-with-docker-hub"></a>Docker Hub を使用して認証する

最初の手順として、現在、ビルドまたはデプロイのワークフローの一環として Docker Hub からパブリック イメージをプルしている場合、匿名のプル要求を行うのではなく、[Docker Hub アカウントを使用して認証](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests)することをお勧めします。

匿名のプル要求を頻繁に行うと、`ERROR: toomanyrequests: Too Many Requests.` や `You have reached your pull rate limit.` のような Docker エラーが発生することがあります。このようなエラーを防ぐには、Docker Hub に対して認証を行います。

> [!NOTE]
> 2020 年 11 月 2 日より、Docker の無料プラン アカウントから Docker Hub に対する匿名と認証済みの要求に[ダウンロード レート制限](https://docs.docker.com/docker-hub/download-rate-limit)が適用されるようになり、それぞれ IP アドレスと Docker ID によって実施されます。 
>
> プル要求の数を見積もる場合、クラウド プロバイダーのサービスを使用する際や企業の NAT の内側で作業する際は、複数のユーザーが IP アドレスのサブセットとして集約的に Docker Hub に示されることを考慮してください。 Docker の有料アカウント認証を Docker Hub に対して行われる要求に追加すると、レート制限の調整によってサービスが中断する可能性がなくなります。
>
> 詳細については、[Docker の価格とサブスクリプション](https://www.docker.com/pricing)に関するページおよび「[Docker のサービス使用条件](https://www.docker.com/legal/docker-terms-service)」を参照してください。

### <a name="docker-hub-access-token"></a>Docker Hub のアクセス トークン

Docker Hub では、Docker Hub に対する認証時に Docker パスワードの代替手段として[個人用アクセス トークン](https://docs.docker.com/docker-hub/access-tokens/)がサポートされます。 Docker Hub からイメージをプルする自動化されたサービスには、トークンをお勧めします。 異なるユーザーまたはサービス用に複数のトークンを生成でき、不要になったときはトークンを取り消すことができます。

トークンを使用して `docker login` によって認証するには、コマンド ラインでパスワードを省略します。 パスワードの入力を求められたら、代わりにトークンを入力します。 Docker Hub アカウントで 2 要素認証を有効にした場合は、Docker CLI からログインするときに個人用アクセス トークンを使用する必要があります。

### <a name="authenticate-from-azure-services"></a>Azure サービスから認証する

App Service や Azure Container Instances を含むいくつかの Azure サービスでは、コンテナーをデプロイするために Docker Hub などのパブリック レジストリからイメージをプルすることがサポートされています。 Docker Hub からのイメージをデプロイする必要がある場合は、Docker Hub アカウントを使用して認証を行うように設定を構成することをお勧めします。 次に例を示します。

**App Service**

* **イメージのソース**:Docker Hub
* **リポジトリ アクセス**:プライベート
* **ログイン**: \<Docker Hub username>
* **パスワード**: \<Docker Hub token>

詳細については、「[App Service での Docker Hub の認証されたプル](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html)」を参照してください。

**Azure Container Instances**

* **イメージのソース**:Docker Hub またはその他のレジストリ
* **イメージの種類**:プライベート
* **イメージ レジストリのログイン サーバー**: docker.io
* **イメージ レジストリのユーザー名**: \<Docker Hub username>
* **イメージ レジストリのパスワード**: \<Docker Hub token>
* **イメージ**: docker.io/\<repo name\>:\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Azure コンテナー レジストリへのイメージのインポート
 
パブリック イメージのコピーの管理を始めるために、Azure コンテナー レジストリを作成できます (まだない場合)。 レジストリの作成には、[Azure CLI](container-registry-get-started-azure-cli.md)、[Azure portal](container-registry-get-started-portal.md)、[Azure PowerShell](container-registry-get-started-powershell.md)、またはその他のツールを使用します。 

推奨されている 1 回限りの手順として、基本イメージとその他のパブリック コンテンツを Azure コンテナー レジストリに[インポート](container-registry-import-images.md)してください。 Azure CLI の [az acr import](/cli/azure/acr#az_acr_import) コマンドは、Docker Hub や Microsoft Container Registry などのパブリック レジストリから、および他のプライベート コンテナー レジストリからのイメージのインポートに対応しています。 

`az acr import` では、Docker のローカル インストールは不要です。 Azure CLI のローカル インストールを使用するか、Azure Cloud Shell 内で直接実行できます。 任意の種類の OS のイメージ、マルチアーキテクチャ イメージ、または Helm チャートなどの OCI 成果物がサポートされます。

例:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

組織のニーズに応じて、専用レジストリか、または共有レジストリ内のリポジトリにインポートできます。

## <a name="automate-application-image-updates"></a>アプリケーション イメージの更新を自動化する

アプリケーション イメージの開発者は、そのコードが、自分の制御下にあるローカル コンテンツを参照していることを確認する必要があります。 たとえば、Dockerfile の `Docker FROM` ステートメントは、パブリック レジストリではなく、プライベート基本イメージ レジストリ内のイメージを参照する必要があります。 

イメージのインポートを拡張して、基本イメージの更新時にアプリケーション イメージのビルドが自動的に行われるように [Azure Container Registry タスク](container-registry-tasks-overview.md)を設定します。 自動化されたビルド タスクでは、[基本イメージの更新](container-registry-tasks-base-images.md)と[ソース コードの更新](container-registry-tasks-overview.md#trigger-task-on-source-code-update)の両方を追跡できます。

詳しい例については、「[Azure Container Registry タスクを使用してパブリック コンテンツを使用および保守する方法](tasks-consume-public-content.md)」を参照してください。 

> [!NOTE]
> タスクを 1 つ事前に構成しておくと、依存側の基本イメージを参照するすべてのアプリケーション イメージを自動的にリビルドできます。 
 
## <a name="next-steps"></a>次のステップ
 
* Azure でコンテナー イメージをビルド、実行、プッシュ、修正する [ACR タスク](container-registry-tasks-overview.md)の詳細について学習します。
* 環境に合わせて基本イメージを更新するための自動化されたゲート ワークフローについては、「[Azure Container Registry タスクを使用してパブリック コンテンツを使用および保守する方法](tasks-consume-public-content.md)」を参照してください。 
* イメージのビルドと更新を自動化するその他の例については、[ACR タスクのチュートリアル](container-registry-tutorial-quick-task.md)を参照してください。
