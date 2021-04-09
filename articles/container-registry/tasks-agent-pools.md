---
title: 専用プールを使用してタスクを実行する - タスク
description: Azure Container Registry タスクを実行するための専用コンピューティング プールをレジストリに設定します。
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: eeb9a71854f52da5c1a9f4befae93c377ad67b05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920309"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>専用エージェント プールで ACR タスクを実行する

専用のコンピューティング環境で [Azure Container Registry タスク][acr-tasks]を実行できるよう、Azure 管理 VM プール ("*エージェント プール*") を設定します。 レジストリで 1 つまたは複数のプールを構成したら、サービスの既定のコンピューティング環境の代わりにタスクを実行するプールを選択できます。

エージェント プールからは次のものが与えられます。

- **仮想ネットワーク サポート** - エージェント プールを Azure VNet に割り当てます。コンテナー レジストリ、キー コンテナー、またはストレージなど、VNet のリソースへのアクセスを提供します。
- **必要に応じた拡大縮小** - 大量の計算処理能力を要求するタスクのためにエージェント プールでインスタンスの数を増やしたり、ゼロに縮小したりします。 課金はプール割り当てに基づきます。 詳細については、[価格](https://azure.microsoft.com/pricing/details/container-registry/)のページを参照してください。
- **柔軟なオプション** - タスク ワークロードのニーズに合わせ、さまざまな [プール レベル](#pool-tiers)やスケール オプションから選択します。
- **Azure 管理** - タスク プールには Azure によってパッチが適用され、保守管理されます。割当が予約され、個々の VM を保守管理する必要がありません。

この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 レジストリ サービスのレベルと制限については、「[Azure Container Registry SKU][acr-tiers]」をご覧ください。

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>

## <a name="preview-limitations"></a>プレビューの制限事項

- タスク エージェント プールでは現在、Linux ノードがサポートされています。 Windows ノードは現在、サポートされていません。
- 次のリージョンでは、タスク エージェント プールをプレビューでご利用いただけます。米国西部 2、米国中南部、米国東部 2、米国東部、米国中部、USGov アリゾナ、USGov テキサス、および USGov バージニア。
- 各レジストリについて、既定の合計 vCPU (コア) クォータは、Standard エージェント プールの場合はすべて 16 で、分離エージェント プールの場合は 0 です。 追加で割り当てる場合、[サポート リクエスト][open-support-ticket]を開始します。
- 現在、エージェント プールで実行されるタスクを取り消すことはできません。

## <a name="prerequisites"></a>前提条件

* この記事の Azure CLI 手順を使用するには、Azure CLI バージョン 2.3.1 以降が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。 または、[Azure Cloud Shell](../cloud-shell/quickstart.md) で実行します。
* コンテナー レジストリがまだない場合は、プレビュー リージョンで [1 つ作成します][create-reg-cli] (Premium レベルが必要)。

## <a name="pool-tiers"></a>プール レベル

エージェント プール レベルからは、プール内のインスタンスごとに次のリソースが提供されます。

|レベル    | 型  |  CPU  |メモリ (GB)  |
|---------|---------|---------|---------|
|S1     |  standard    | 2       |    3     |
|S2     |  standard    | 4       |    8     |
|S3     |  standard    | 8       |   16     |
|I6     |  分離    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>タスク エージェント プールの作成と管理

### <a name="set-default-registry-optional"></a>既定のレジストリを設定する (省略可能)

続く Azure CLI コマンドを簡略化するには、[az configure][az-configure] コマンドを実行して既定のレジストリを設定します。

```azurecli
az configure --defaults acr=<registryName>
```

次の例では、既定のレジストリを設定していることを前提としています。 設定していない場合、各 `az acr` コマンドで `--registry <registryName>` パラメーターを渡します。

### <a name="create-agent-pool"></a>エージェント プールの作成

[az acr agentpool create][az-acr-agentpool-create] コマンドを使用し、エージェント プールを作成します。 次の例では、レベル S2 プール (インスタンスあたり 4 CPU) が作成されます。 既定では、プールにはインスタンスが 1 つ含まれます。

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> エージェント プールとその他のプール管理操作の作成完了には数分かかります。

### <a name="scale-pool"></a>プールのスケーリング

[az acr agentpool update][az-acr-agentpool-update] コマンドでプール サイズを拡大縮小します。 次の例では、プールが 2 インスタンスに拡大されます。 0 インスタンスに縮小できます。

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>仮想ネットワーク内にプールを作成する

### <a name="add-firewall-rules"></a>ファイアウォール規則を追加する

タスク エージェント プールでは、次の Azure サービスへのアクセスが要求されます。 次のファイアウォール規則を既存のネットワーク セキュリティ グループまたはユーザー定義の経路に追加する必要があります。

| Direction | Protocol | source         | 発信元ポート | 宛先          | 宛先ポート | 使用    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| 送信  | TCP      | VirtualNetwork | Any         | AzureKeyVault        | 443       | 既定 |
| 送信  | TCP      | VirtualNetwork | Any         | 記憶域              | 443       | 既定 |
| 送信  | TCP      | VirtualNetwork | Any         | EventHub             | 443       | 既定 |
| 送信  | TCP      | VirtualNetwork | Any         | AzureActiveDirectory | 443       | 既定 |
| 送信  | TCP      | VirtualNetwork | Any         | AzureMonitor         | 443       | 既定 |

> [!NOTE]
> 公共のインターネットからの追加リソースがタスクで必要になる場合、それに該当する規則を追加します。 たとえば、Docker Hub から基本イメージをプルするか、NuGet パッケージを復元する Docker ビルド タスクを実行する場合、こうした規則が必要になります。

### <a name="create-pool-in-vnet"></a>VNet でプールを作成する

次の例では、ネットワーク *myvnet* の *mysubnet* サブネットでエージェント プールが作成されます。

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>エージェント プールでタスクを実行する

次の例からは、タスクを待ち行列に入れるときのエージェント プールの指定方法がわかります。

> [!NOTE]
> ACR タスクでエージェント プールを使用するには、プールにインスタンスが少なくとも 1 つは確実に含まれるようにします。
>

### <a name="quick-task"></a>クイック タスク

[az acr build][az-acr-build] コマンドを使用してエージェント プールでクイック タスクを待ち行列に入れ、`--agent-pool` パラメーターを渡します。

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>自動的にトリガーされたタスク

たとえば、[az acr task create][az-acr-task-create] を使用してエージェント プールでタスクの日程を作成し、`--agent-pool` パラメーターを渡します。

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

タスク セットアップを確認するには、[az acr task run][az-acr-task-run] を実行します。

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>プールの状態を照会する

エージェント プールで現在スケジュールされている実行数を確認するには、[az acr agentpool show][az-acr-agentpool-show] を実行します。

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>次のステップ

クラウドのコンテナー イメージのビルドや保守管理について例がもっと必要であれば、[ACR タスクのチュートリアル シリーズ](container-registry-tutorial-quick-task.md)をご覧ください。



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
