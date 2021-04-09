---
title: タスク実行ログの表示 - タスク
description: ACR タスクによって生成された実行ログを表示および管理する方法。
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027178"
---
# <a name="view-and-manage-task-run-logs"></a>タスク実行ログを表示および管理する

[Azure Container Registry タスク](container-registry-tasks-overview.md)のそれぞれのタスク実行で、タスク ステップが正常に実行されたかどうかを判別するために検査できるログ出力が生成されます。 

この記事では、タスク実行ログの表示および管理方法について説明します。

## <a name="view-streamed-logs"></a>ストリーム配信されるログを表示する

タスクを手動でトリガーすると、ログ出力が直接コンソールにストリーム配信されます。 たとえば、[az acr build](/cli/azure/acr#az-acr-build)、[az acr run](/cli/azure/acr#az-acr-run)、または [az acr task run](/cli/azure/acr/task#az-acr-task-run) コマンドを使用して手動でタスクをトリガーする場合、コンソールにストリーム配信されたログ出力が表示されます。 

次の [az acr run](/cli/azure/acr#az-acr-run) サンプル コマンドは、同じレジストリからプルされたコンテナーを実行するタスクを手動でトリガーします。

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

ストリーム配信されたログ:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>保存されたログを表示する 

Azure Container Registry では、すべてのタスクの実行ログが保存されます。 保存されている実行ログは、Azure portal で表示できます。 または [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) コマンドを使用して、選択したログを表示します。 既定では、ログは 30 日間保持されます。

たとえばソースコードの更新でタスクが自動的にトリガーされる場合、保存されているログへのアクセスが、実行ログを表示する *唯一* の方法になります。 自動タスク トリガーには、ソース コードのコミットまたは pull request、基本イメージの更新、およびタイマー トリガーが含まれます。

ポータル内の実行ログを表示するには:

1. お使いのコンテナー レジストリに移動します。
1. **[サービス]** で、 **[タスク]**  >  **[実行]** を選択します。
1. 実行状態と実行ログを表示するには、 **[実行 ID]** を選択します。 ログには、ストリーム配信されたログが生成された場合は、これと同じ情報が含まれます。

![タスク実行ログイン ポータルを表示する](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Azure CLI を使用してログを表示するには、[az acr task logs](/cli/azure/acr/task#az-acr-task-logs) を実行して、実行 ID、タスク名、またはビルド タスクで作成される特定のイメージを指定します。 タスク名が指定されている場合、コマンドは最後に作成された実行のログを表示します。

次の例では、ID *cf4* の実行のログを出力します。

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>代替ログ ストレージ

タスク実行ログをローカル ファイル システムに保存することも、Azure Storage などの代替アーカイブ ソリューションを使用することもできます。

たとえば、ローカルの *tasklogs* ディレクトリを作成し、[az acr task logs](/cli/azure/acr/task#az-acr-task-logs) の出力をローカル ファイルにリダイレクトします。

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

ローカル ログ ファイルを Azure Storage に保存することもできます。 たとえば、[Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md)、[Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md)、またはその他の方法を使用して、ファイルをストレージ アカウントにアップロードします。

## <a name="next-steps"></a>次のステップ

* [Azure Container Registry タスク](container-registry-tasks-overview.md)の詳細を確認する


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
