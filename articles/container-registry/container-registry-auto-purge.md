---
title: タグとマニフェストを消去する
description: 消去コマンドを使用すると、経過時間とタグ フィルターに基づいて Azure コンテナー レジストリから複数のタグとマニフェストを削除できるほか、必要に応じて消去操作をスケジュールすることができます。
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 2dedfdd6eba73b7573743eba60294ac2231ffc56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722230"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Azure コンテナー レジストリからイメージを自動的に消去する

開発ワークフローの一部として Azure コンテナー レジストリを使用すると、レジストリは短時間で不要なイメージやその他の成果物によりすぐにいっぱいになる可能性があります。 特定の期間よりも前のタグをすべて削除することも、指定した名前フィルターに一致したタグをすべて削除することもできます。 複数の成果物をすばやく削除できるように、この記事では、オンデマンドまたは[スケジュールされた](container-registry-tasks-scheduled.md) ACR タスクとして実行できる `acr purge` コマンドを紹介します。 

`acr purge` コマンドは現在、GitHub の [acr-cli](https://github.com/Azure/acr-cli) リポジトリのソース コードから作成された、パブリック コンテナー イメージ (`mcr.microsoft.com/acr/acr-cli:0.4`) で配布されています。

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、この記事の ACR タスクの例を実行できます。 これをローカルで使用したい場合は、バージョン 2.0.76 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。 

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

> [!WARNING]
> `acr purge` コマンドを使用する際は注意してください。削除されたイメージ データは回復することができません。 (イメージ名ではなく) マニフェスト ダイジェストによってイメージをプルするシステムを使用している場合は、タグなしのイメージを消去しないでください。 このようなシステムでは、タグの付いていないイメージを削除すると、レジストリからイメージをプルできなくなります。 マニフェストでプルするのではなく、*一意のタグ付け* スキームの [推奨されるベスト プラクティス](container-registry-image-tag-version.md)を採用することを検討してください。

Azure CLI コマンドを使用して 1 つのイメージ タグまたはマニフェストを削除する場合は、「[Azure Container Registry のコンテナー イメージを削除する](container-registry-delete.md)」を参照してください。

## <a name="use-the-purge-command"></a>消去コマンドを使用する

`acr purge` コンテナー コマンドでは、名前フィルターに一致するイメージおよび指定した期間よりも古いイメージがリポジトリ内のタグによって削除されます。 既定では、タグ参照のみが削除され、基になる[マニフェスト](container-registry-concepts.md#manifest)およびレイヤー データは削除されません。 このコマンドには、マニフェストも削除するオプションがあります。 

> [!NOTE]
> `acr purge` では、`write-enabled` 属性が `false` に設定されているイメージ タグまたはリポジトリは削除されません。 詳細については、「[Azure コンテナー レジストリでのコンテナー イメージをロックする](container-registry-image-lock.md)」を参照してください。

`acr purge` は、[ACR タスク](container-registry-tasks-overview.md)でコンテナー コマンドとして実行するように設計されているため、そのタスクを実行するレジストリで自動的に認証され、そこでアクションを実行します。 この記事のタスク例では、完全修飾コンテナー イメージ コマンドの代わりに、`acr purge` コマンドの[エイリアス](container-registry-tasks-reference-yaml.md#aliases)を使用します。

`acr purge` を実行するときは、少なくとも以下を指定します。

* `--filter` - リポジトリと、リポジトリ内のタグをフィルター処理する *正規表現*。 例: `--filter "hello-world:.*"` は `hello-world` リポジトリ内のすべてのタグと一致し、`--filter "hello-world:^1.*"` は `1` で始まるタグと一致します。 複数のリポジトリを消去するには、複数の `--filter` パラメーターを渡します。
* `--ago` - イメージが削除されるまでの期間を示す Go スタイルの[期間文字列](https://golang.org/pkg/time/)。 この期間は、1 つまたは複数の一連の 10 進数で構成され、それぞれに単位サフィックスが付きます。 有効な時間単位には、日を表す "d"、時間を表す "h"、分を表す "m" などがあります。 たとえば、`--ago 2d3h6m` の場合は、最後に変更されたのが 2 日と 3 時間 6 分以上前であるフィルター処理されたイメージがすべて選択され、`--ago 1.5h` の場合は、最後に変更されたのが 1.5 時間以上前であるイメージが選択されます。

`acr purge` では、いくつかのオプション パラメーターがサポートされます。 この記事の例では、次の 2 つを使用します。

* `--untagged` - タグが関連付けられていないマニフェスト (*タグなしマニフェスト*) を削除することを指定します。
* `--dry-run` - データは削除しないが、出力をこのフラグなしでコマンドを実行した場合と同じにすることを指定します。 このパラメーターは、削除コマンドをテストして、保存しようとしているデータが誤って削除されることがないことを確認する場合に役立ちます。
* `--keep` - 削除するタグの最新の x 個を保持するように指定します。
* `--concurrency` - 同時に処理する消去タスクの数を指定します。 このパラメーターが指定されていない場合は、既定値が使用されます。

その他のパラメーターについては、`acr purge --help` を実行してください。 

`acr purge` では、[Run 変数](container-registry-tasks-reference-yaml.md#run-variables)や、ストリーミングされ、後で取得できるように保存される[タスク実行ログ](container-registry-tasks-logs.md)など、ACR タスク コマンドの他の機能もサポートしています。

### <a name="run-in-an-on-demand-task"></a>オンデマンド タスクで実行する

次の例では、[az acr run][az-acr-run] コマンドを使用して、`acr purge` コマンドをオンデマンドで実行します。 この例では、1 日以上前に変更された *myregistry* 内の `hello-world` リポジトリにあるすべてのイメージ タグとマニフェストを削除します。 コンテナー コマンドは、環境変数を使用して渡されます。 タスクは、ソース コードのコンテキストなしで実行されます。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>スケジュールされたタスクで実行する

次の例では、[az acr task create][az-acr-task-create] コマンドを使用して、毎日[スケジュールされた ACR タスク](container-registry-tasks-scheduled.md)を作成します。 このタスクでは、`hello-world` リポジトリ内で 7 日以上前に変更されたタグが消去されます。 コンテナー コマンドは、環境変数を使用して渡されます。 タスクは、ソース コードのコンテキストなしで実行されます。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

タイマー トリガーが構成されていることを確認するには、[az acr task show][az-acr-task-show] コマンドを実行します。

### <a name="purge-large-numbers-of-tags-and-manifests"></a>多数のタグとマニフェストを消去する

多数のタグとマニフェストを削除する場合は、数分またはそれ以上かかることがあります。 何千ものタグとマニフェストを削除する場合は、コマンドの実行時間が、オンデマンド タスクの既定のタイムアウト時間 (600 秒)、またはスケジュールされたタスクの既定のタイムアウト時間 (3600 秒) より長くなることがあります。 タイムアウト時間が経過すると、タグとマニフェストのサブセットのみが削除されます。 大規模な消去を確実に完了するには、`--timeout` パラメーターを渡して値を増やします。 

たとえば、次のオンデマンド タスクでは、タイムアウト時間を 3600 秒 (1 時間) に設定されます。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>例:レジストリ内にある複数のリポジトリのスケジュールされた消去

この例では、`acr purge` を使用して、レジストリ内の複数のリポジトリを定期的にクリーン アップします。 たとえば、`samples/devimage1` リポジトリと `samples/devimage2` リポジトリにイメージをプッシュする開発パイプラインがあるとします。 デプロイ用の運用リポジトリに開発イメージを定期的にインポートするため、開発イメージは不要になりました。 毎週、`samples/devimage1` リポジトリと `samples/devimage2` リポジトリは、次の週の作業に備えて消去します。

### <a name="preview-the-purge"></a>消去のプレビュー

データを削除する前に、`--dry-run` パラメーターを使用して、オンデマンドの消去タスクを実行することをお勧めします。 このオプションを使用すると、データを削除することなく、コマンドによって消去されるタグとマニフェストを表示できます。 

次の例では、各リポジトリのフィルターによってすべてのタグが選択されます。 `--ago 0d` パラメーターは、フィルターと一致するリポジトリにあるすべての経過時間のイメージに一致します。 必要に応じて、ご自身のシナリオの選択条件を変更します。 `--untagged` パラメーターでは、タグに加えてマニフェストを削除することが指定されます。 コンテナー コマンドは、環境変数を使用して [az acr run][az-acr-run] コマンドに渡されます。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

コマンドの出力を確認すると、選択パラメーターに一致するタグとマニフェストがわかります。 コマンドは `--dry-run` を使用して実行されるため、データは削除されません。

サンプル出力:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>消去のスケジュールを設定する

ドライ ランの確認が完了したら、スケジュールされたタスクを作成して消去を自動化します。 次の例では、毎週のタスクを日曜日の 1:00 UTC にスケジュールして、前の消去コマンドを実行します。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

タイマー トリガーが構成されていることを確認するには、[az acr task show][az-acr-task-show] コマンドを実行します。

## <a name="next-steps"></a>次のステップ

Azure Container Registry で[イメージ データを削除](container-registry-delete.md)するその他のオプションについて説明します。

イメージの保存の詳細については、「[Azure Container Registry へのコンテナー イメージの保存](container-registry-storage.md)」を参照してください。

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

