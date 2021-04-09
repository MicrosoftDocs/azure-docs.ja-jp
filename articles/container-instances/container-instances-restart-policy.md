---
title: 一度だけ実行されるタスクの再起動ポリシー
description: Azure Container Instances を使用して、ビルド、テスト、イメージ レンダリングのジョブなど、完了まで実行するタスクを実行する方法を説明します。
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 49280549fa834b82574f81494f1cf44817d8be5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203829"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>再起動ポリシーによるコンテナー化タスクの実行

Azure Container Instances ではコンテナー デプロイを簡単にすばやく行えるため、コンテナー インスタンスでのビルド、テスト、イメージ レンダリングなどの一度のみ実行されるタスクの実行に優れたプラットフォームを提供します。

構成可能な再起動ポリシーを使用して、プロセスが完了したらコンテナーが停止するように指定できます。 コンテナーのインスタンスは秒単位で課金されるため、タスクを実行するコンテナーの実行中に使用されるコンピューティング リソースのみが課金されます。

この記事にある例では、Azure CLI を使用します。 Azure CLI バージョン 2.0.21 以上が[ローカルにインストールされている][azure-cli-install]か、[Azure Cloud Shell](../cloud-shell/overview.md) で CLI を使用する必要があります。

## <a name="container-restart-policy"></a>コンテナー再起動ポリシー

Azure Container Instances で[コンテナー グループ](container-instances-container-groups.md)を作成する場合、3 つの再起動ポリシー設定のいずれかを指定できます。

| 再起動ポリシー   | 説明 |
| ---------------- | :---------- |
| `Always` | コンテナー グループ内のコンテナーを常に再起動する。 これは **既定** の設定で、コンテナー作成時に再起動ポリシーが指定されていない場合に適用されます。 |
| `Never` | コンテナー グループ内のコンテナーを再起動しない。 コンテナーは最大で 1 回実行されます。 |
| `OnFailure` | コンテナーで実行されたプロセスが失敗 (0 以外の終了コードで終了) した場合にのみ、コンテナー グループ内のコンテナーを再起動する。 コンテナーは少なくとも 1 回実行されます。 |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>再起動ポリシーの指定

再起動ポリシーを指定する方法は、Azure CLI や Azure PowerShell コマンドレット、または Azure Portal を使うなどのコンテナー インスタンスを作成する方法によって異なります。 Azure CLI では、[az container create][az-container-create] を呼び出すときに、`--restart-policy` パラメーターを指定します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>完了まで実行の例

再起動ポリシーが動作しているのを確認するには、Microsoft [aci-wordcount][aci-wordcount-image] イメージからコンテナー インスタンスを作成し、`OnFailure` 再起動ポリシーを指定します。 このコンテナー例では、既定でシェイクスピアの[ハムレット](http://shakespeare.mit.edu/hamlet/full.html)のテキストを解析し、最もよく使われる単語 10 個を STDOUT に書き込んで終了する Python スクリプトを実行します。

このコンテナー例を次の [az container create][az-container-create] コマンドで実行します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances はコンテナーを開始し、そのアプリケーション (ここではスクリプト) が終了すると停止します。 Azure Container Instances が再起動ポリシー `Never` または `OnFailure` のコンテナーを停止すると、そのコンテナーの状態は **終了** に設定されます。 [az container show][az-container-show] コマンドで、コンテナーの状態を確認できます。

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

出力例:

```bash
"Terminated"
```

コンテナー例の状態が *終了* と表示されたら、コンテナー ログを表示してタスクの出力を確認できます。 [az container logs][az-container-logs] コマンドを実行して、スクリプトの出力を表示します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

出力:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

この例は、スクリプトが STDOUT に送信した出力を示しています。 ただし、コンテナー化されたタスクでは、後で取得できるように、その出力を永続的ストレージに書き込む場合があります。 たとえば、[Azure ファイル共有](./container-instances-volume-azure-files.md)に書き込むなどです。

## <a name="next-steps"></a>次のステップ

いくつかのコンテナーがある大きなデータセットのバッチ処理など、タスク ベースのシナリオでは、ランタイムではカスタムの[環境変数](container-instances-environment-variables.md)または[コマンド ライン](container-instances-start-command.md)のメリットが得られます。

完了まで実行するコンテナーの出力を保存する方法の詳細については、「[Azure Container Instances での Azure ファイル共有のマウント](./container-instances-volume-azure-files.md)」をご覧ください。

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
