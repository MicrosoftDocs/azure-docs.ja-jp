---
title: コンテナー インスタンスのエントリポイントをオーバーライドする
description: Azure コンテナー インスタンスのデプロイ時にコンテナー イメージのエントリポイントをオーバーライドするためのコマンド ラインを設定する
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169631"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>コンテナー インスタンスにコマンド ラインを設定して既定のコマンド ライン操作をオーバーライドする

コンテナー インスタンスを作成するときに、必要に応じて、コンテナー イメージに組み込まれた既定のコマンド ライン命令をオーバーライドするコマンドを指定します。 この動作は、`docker run` に対する `--entrypoint` コマンドライン引数に似ています。

コンテナー インスタンスの[環境変数](container-instances-environment-variables.md)の設定と同様に、タスク固有の構成を使用して各コンテナーを動的に準備する必要のあるバッチ ジョブにおいて、開始コマンド ラインの指定は役立ちます。

## <a name="command-line-guidelines"></a>コマンドラインのガイドライン

* 既定では、このコマンド ラインでは、コンテナーで *シェルを使用せずに開始する単一のプロセス* を指定します。 たとえば、コマンド ラインにより Python スクリプトまたは実行ファイルを実行できます。 このプロセスでは、追加のパラメーターまたは引数を指定できます。

* 複数のコマンドを実行するには、コンテナーのオペレーティング システムでサポートされるシェル環境を設定することで、コマンド ラインを開始します。 例 :

  |オペレーティング システム  |既定のシェル  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  シェルの表記規則に従って、複数のコマンドを順番に実行します。

* コンテナーの構成によっては、コマンドライン実行可能ファイルへの完全パスまたは引数を設定する必要があります。

* 長時間実行されるタスクまたは一度だけ実行されるタスクのどちらがコマンド ラインで指定されているかに応じて、コンテナー インスタンスに適切な[再起動ポリシー](container-instances-restart-policy.md)を設定します。 たとえば、一度だけ実行されるタスクには、再起動ポリシーの `Never` または `OnFailure` をお勧めします。 

* コンテナー イメージに設定された既定のエントリポイントに関する情報が必要な場合は、[docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/) コマンドを使用してください。

## <a name="command-line-syntax"></a>コマンド ラインの構文

コマンド ラインの構文は、インスタンスの作成に使用される Azure API やツールに応じて異なります。 シェル環境を指定する場合、シェルのコマンド構文表記規則も確認してください。

* [az container create][az-container-create] コマンド:`--command-line` パラメーターを使用して文字列を渡します。 例: `--command-line "python myscript.py arg1 arg2"`。

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell コマンドレット:`-Command` パラメーターを使用して文字列を渡します。 例: `-Command "echo hello"`.

* Azure portal:コンテナー構成の **[Command override]\(コマンドのオーバーライド\)** プロパティーで、文字列のコンマ区切りのリストを引用符なしで指定します。 例: `python, myscript.py, arg1, arg2`。 

* Resource Manager テンプレート、YAML ファイル、またはいずれかの Azure SDK:コマンド ライン プロパティーを文字列の配列として指定します。 例: Resource Manager テンプレートの JSON 配列 `["python", "myscript.py", "arg1", "arg2"]`。 

  [Dockerfile](https://docs.docker.com/engine/reference/builder/) 構文に慣れている場合、この形式は、CMD 命令の *exec* フォームに似ています。

### <a name="examples"></a>例

|    |  Azure CLI   | ポータル | Template | 
| ---- | ---- | --- | --- |
| **単一のコマンド** | `--command-line "python myscript.py arg1 arg2"` | **[Command override]\(コマンドのオーバーライド\)** : `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **複数のコマンド** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**[Command override]\(コマンドのオーバーライド\)** : `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI の例

例として、[microsoft/aci-wordcount][aci-wordcount] コンテナー イメージの動作を変更します。このコンテナーは、シェイクスピアの「*ハムレット*」の中のテキストを分析し、最も頻繁に使用される単語を検索します。 「*ハムレット*」を分析する代わりに、異なるテキスト ソースをポイントするコマンド ラインを設定することもできます。

[microsoft/aci-wordcount][aci-wordcount] コンテナーが既定のテキストを分析するときにその出力を表示するには、次の [az container create][az-container-create] コマンドを使用して実行します。 既定のコンテナー コマンドが実行されるように、開始コマンド ラインは指定されていません。 分かりやすくするため、この例では、5 文字以上の上位 3 の単語を検索するための[環境変数](container-instances-environment-variables.md)が設定されています。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

コンテナーの状態が *終了* と表示されたら ([az container show][az-container-show] を使用して状態を確認)、[az container logs][az-container-logs] を使用してログを表示し、出力を確認します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

出力:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

次に、2 つ目の例のコンテナーを設定し、別のコマンド ラインを指定して異なるテキストを分析します。 コンテナーで実行される Python スクリプト *wordcount.py* は、引数として URL を受け取り、既定のページではなくそのページのコンテンツを処理します。

たとえば、「*ロミオとジュリエット*」で 5 文字以上の上位 3 の単語を特定するには、次のようにします。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

再び、コンテナーが *終了* になったら、コンテナー ログを表示することにより出力を表示します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

出力:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>次のステップ

複数のコンテナーがある大きなデータセットのバッチ処理など、タスク ベースのシナリオでは、実行時にカスタムのコマンド ラインを使用するとメリットがあります。 タスク ベースのコンテナーの実行に関する詳細については、「[再起動ポリシーによるコンテナー化タスクの実行](container-instances-restart-policy.md)」を参照してください。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
