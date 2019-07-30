---
title: Azure Container Registry webhook
description: レジストリ リポジトリで特定のアクションが発生したときに、webhook を使用してイベントをトリガーする方法について説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/24/2019
ms.author: danlep
ms.openlocfilehash: 59e8d4979e7be02d6097e1c3eccc44e64da87e95
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311579"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry webhook の使用

Docker Hub で公開 Docker イメージを格納するように、Azure コンテナー レジストリではプライベート Docker コンテナー イメージを格納および管理します。 また、Kubernetes にアプリケーションをデプロイするパッケージ形式である [Helm chart](container-registry-helm-repos.md) (プレビュー) のリポジトリをホストすることもできます。 レジストリ リポジトリで特定のアクションが発生した場合に、webhook を使用してイベントをトリガーできます。 webhook では、レジストリ レベルでイベントに応答したり、特定のリポジトリ タグに範囲を絞ったりできます。 [geo レプリケートされた](container-registry-geo-replication.md)レジストリを使用して、特定のリージョン レプリカ内のイベントに応答するように各 webhook を構成します。

webhook 要求の詳細については、「[Azure Container Registry webhook スキーマ リファレンス](container-registry-webhook-reference.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Container Registry - コンテナー レジストリを Azure サブスクリプションで作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。 [Azure Container Registry SKU](container-registry-skus.md) に Webhook のさまざまなクォータがあります。
* Docker CLI - ローカル コンピューターを Docker ホストとして設定し、Docker CLI コマンドにアクセスするには、[Docker エンジン](https://docs.docker.com/engine/installation/)をインストールします。

## <a name="create-webhook---azure-portal"></a>Webhook の作成 - Azure portal

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. webhook を作成するコンテナー レジストリに移動します。
1. **[サービス]** で **[Webhook]** を選択します。
1. webhook ツールバーで **[追加]** を選択します。
1. 次の情報を利用して、 *[webhook の作成]* フォームを完成させます。

| 値 | 説明 |
|---|---|
| Webhook 名 | Webhook に付与する名前。 使用できる文字は英数字のみです。文字数は 5 ～ 50 文字にする必要があります。 |
| Location | [geo レプリケートされた](container-registry-geo-replication.md)レジストリの場合は、レジストリ レプリカの Azure リージョンを指定します。 
| サービス URI | Webhook が POST 通知を送信する URI。 |
| カスタム ヘッダー | POST 要求と共に渡すヘッダー。 "キー: 値" の形式にする必要があります。 |
| トリガー アクション | Webhook をトリガーするアクション。 アクションには、イメージのプッシュ、イメージの削除、Helm chart のプッシュ、chart の削除、およびイメージの検疫などがあります。 Webhook をトリガーするアクションを 1 つ以上選択できます。 |
| Status | Webhook の作成後の状態。 既定で有効です。 |
| Scope | Webhook が動作するスコープです。 指定しない場合、スコープはレジストリ内のすべてのイベントです。 "リポジトリ:タグ" または "リポジトリ:*" (あるリポジトリ以下のすべてのタグの場合) という形式を使用して、リポジトリまたはタグに指定できます。 |

Webhook フォームの例 :

![Azure Portal の ACR webhook の作成の UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook の作成 - Azure CLI

Azure CLI を使用して Webhook を作成するには、[az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) コマンドを使用します。 次のコマンドは、レジストリ *mycontainerregistry* 内のすべてのイメージの delete イベントに対して Webhook を作成します。

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook をテストする

### <a name="azure-portal"></a>Azure ポータル

Webhook を使用する前に、 **[Ping]** ボタンを使ってテストできます。 Ping を実行すると、指定されたエンドポイントに一般的な POST 要求が送信され、応答がログに記録されます。 ping 機能を使用して、webhook が正しく構成されていることを確認できます。

1. テストする Webhook を選択します。
2. 上部のツールバーで、 **[Ping]** を選択します。
3. **[HTTP の状態]** 列でエンドポイントの応答を確認します。

![Azure Portal の ACR webhook の作成の UI](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して ACR Webhook をテストするには、[az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) コマンドを使用します。

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

結果を表示するには、[az acr webhook list-events](/cli/azure/acr/webhook) コマンドを使用します。

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook の削除

### <a name="azure-portal"></a>Azure ポータル

Azure Portal で webhook を選択してから **[削除]** ボタンをクリックすると、各 webhook を削除できます。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>次の手順

### <a name="webhook-schema-reference"></a>Webhook スキーマ リファレンス

Azure Container Registry によって出力される JSON イベント ペイロードの形式とプロパティについて詳しくは、Webhook のスキーマ リファレンスをご覧ください。

[Azure Container Registry webhook スキーマ リファレンス](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid のイベント

この記事で説明したネイティブ レジストリ Webhook イベントだけでなく、Azure Container Registry は Event Grid に対してイベントを生成できます。

[クイック スタート:Event Grid へのコンテナー レジストリ イベントの送信](container-registry-event-grid-quickstart.md)
