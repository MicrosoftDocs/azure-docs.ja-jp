---
title: GitHub Actions を使用して AKS ノードのアップグレードを処理する
titleSuffix: Azure Kubernetes Service
description: GitHub Actions を使用して AKS ノードを更新する方法について説明します
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217959"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>GitHub Actions を使用して Azure Kubernetes Service (AKS) ノードにセキュリティ更新プログラムを自動的に適用する

セキュリティ更新プログラムは、AKS クラスターのセキュリティと、基になる OS の最新の修正プログラムへの準拠を維持するための重要な部分です。 これらの更新プログラムには、OS のセキュリティ修正プログラムやカーネルの更新プログラムが含まれています。 一部の更新プログラムでは、プロセスを完了するためにノードの再起動が必要になります。

`az aks upgrade` を実行すると、ダウンタイムをゼロで更新プログラムを適用できます。 このコマンドによって、すべてのクラスターのノードへの最新の更新プログラムの適用、ノードに対するトラフィックの切断とドレイン、ノードの再起動、および更新されたノードへのトラフィックの許可が処理されます。 別の方法を使用してノードを更新した場合、AKS はノードを自動的に再起動しません。

> [!NOTE]
> `az aks upgrade` を `--node-image-only` フラグと一緒に使用した場合の主な違いは、それを使用した場合にノード イメージだけがアップグレードされることです。 それを省略すると、ノード イメージと Kubernetes コントロール プレーン バージョンの両方がアップグレードされます。 詳細については、[ノードでの管理アップグレードに関するドキュメント][managed-node-upgrades-article]と[クラスターのアップグレードに関するドキュメント][cluster-upgrades-article]を参照してください。

Kubernetes のすべてのノードは、標準の Azure 仮想マシン (VM) で実行されます。 これらの VM は、Windows ベースまたは Linux ベースのどちらでもかまいません。 Linux ベースの VM では、更新プログラムの有無を毎晩自動的にチェックするように OS が構成されている Ubuntu イメージが使用されます。

`az aks upgrade` コマンドを使用すると、Azure CLI によって、最新のセキュリティとカーネルの更新プログラムを使用して新しいノードが大量に作成されます。これらのノードは、更新が完了するまで、アプリがそれらに対してスケジュールされないようにするために、最初に切断されます。 完了後、Azure は、古いノードの切断 (新しいワークロードのスケジュールのためにノードを使用不可にする) と、ドレイン (既存のワークロードを他のノードに移動する) を行い、新しいものの切断を解除します。これにより、スケジュールされたすべてのアプリケーションが効率的に新しいノードに転送されます。

このプロセスは、Linux ベースのカーネルを手動で更新するよりも優れています。Linux では、新しいカーネル更新プログラムをインストールするときに再起動が必要になるためです。 OS を手動で更新する場合は、VM を再起動し、すべてのアプリを手動で切断してドレインする必要もあります。

この記事では、AKS ノードの更新プロセスを自動化する方法について説明します。 GitHub Actions と Azure CLI を使用して、自動的に実行される `cron` に基づいた更新タスクを作成します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

また、この記事では、アクションを作成するための [GitHub][github] アカウントがあることを前提としています。

## <a name="create-a-timed-github-action"></a>時間指定の GitHub アクションを作成する

`cron` は、一連のコマンド (ジョブ) を自動スケジュールに基づいて実行できるユーティリティです。 自動スケジュールに基づいて AKS ノードを更新するジョブを作成するには、アクションをホストするリポジトリが必要になります。 通常、GitHub アクションはお使いのアプリケーションと同じリポジトリで構成されますが、任意のリポジトリを使用できます。 この記事では、[プロファイル リポジトリ][profile-repository]を使用します。 これがない場合は、GitHub ユーザー名と同じ名前を持つ新しいリポジトリを作成します。

1. GitHub の自分のリポジトリに移動します。
1. ページの上部にある **[アクション]** タブをクリックします。
1. このリポジトリに既にワークフローを設定している場合は、完了した実行の一覧が表示されます。この場合、 **[New Workflow]\(新しいワークフロー\)** ボタンをクリックします。 これがリポジトリ内の最初のワークフローである場合、GitHub でいくつかのプロジェクト テンプレートが表示されます。説明テキストの下にある **[Set up a workflow yourself]\(ワークフローを自分で設定する\)** リンクをクリックします。
1. ワークフローの `name` と `on` タグを下のように変更します。 GitHub Actions では、Linux ベースのシステムと同じ [POSIX cron 構文][cron-syntax]が使用されます。 このスケジュールでは、15 日間隔で午前 3 時にワークフローが実行されるように設定します。

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. 以下を使用して、新しいジョブを作成します。 このジョブは `upgrade-node` という名前で、Ubuntu エージェントで実行され、Azure CLI アカウントに接続して、ノードをアップグレードするために必要な手順を実行します。

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>ワークフローで Azure CLI を設定する

`steps` キーで、ワークフローによってノードをアップグレードするために実行されるすべての作業を定義します。

Azure CLI をダウンロードしてサインインします。

1. GitHub Actions 画面の右側で、"*Marketplace の検索バー*" を見つけ、「**Azure Login**」と入力します。
1. 結果として、**Azure によって** 発行された **Azure Login** という名前のアクションが表示されます。

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="2 つの行が示されている検索結果。最初のアクションは &quot;Azure Login&quot; という名前で、2 番目は &quot;Azure Container Registry Login&quot; です":::

1. **Azure Login** をクリックします。 次の画面で、コード サンプルの右上にある **コピー アイコン** をクリックします。

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Azure Login アクションの結果ペイン。下にコード サンプルがあり、コピー アイコンを囲む赤色の四角形によってクリック位置が強調表示されています":::

1. `steps` キーの下に次のものを貼り付けます。

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Azure CLI から次のコマンドを実行して、新しいユーザー名とパスワードを生成します。

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    出力は次の JSON のようになります。

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **新しいブラウザー ウィンドウ** で、GitHub リポジトリに移動し、リポジトリの **[設定]** タブを開きます。 **[シークレット]** をクリックし、次に **[New Repository Secret]\(新しいリポジトリ シークレット\)** をクリックします。
1. *[名前]* には `AZURE_CREDENTIALS` を使用します。
1.  *[値]* には、新しいユーザー名とパスワードを作成した前の手順の出力の内容全体を追加します。

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="フォームには、シークレット タイトルとして AZURE_CREDENTIALS が示されており、実行したコマンドの出力が JSON として貼り付けられています":::

1. **[シークレットの追加]** をクリックします。

アクションによって使用される CLI は、Azure アカウントに記録され、コマンドを実行する準備が整います。

Azure CLI コマンドを実行するステップを作成するには:

1. 画面の右側にある *GitHub Marketplace* の **検索ページ** に移動し、*Azure CLI Action* を検索します。 *Azure CLI Action by Azure* を選択します。

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="&quot;Azure CLI Action&quot; の検索結果。Azure によって実行されるものとして最初の結果が表示されています":::

1. "*GitHub Marketplace の結果*" でコピー ボタンをクリックし、次のように *Azure Login* 手順の下にあるアクションの内容をメイン エディターに貼り付けます。

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > 前の手順と同様に、`-g` と `-n` パラメーターをシークレットに追加することで、コマンドからそれらを切り離すことができます。 `{resourceGroupName}` と `{aksClusterName}` プレースホルダーを、対応するシークレット (たとえば `${{secrets.RESOURCE_GROUP_NAME}}` と `${{secrets.AKS_CLUSTER_NAME}}`) に置き換えます

1. ファイルの名前を `upgrade-node-images` に変更します。
1. **[Start Commit]\(コミットの開始\)** をクリックし、メッセージ タイトルを追加し、ワークフローを保存します。

コミットを作成すると、ワークフローが保存され、実行の準備が整います。

> [!NOTE]
> クラスター上のすべてのノード プールではなく、単一のノード プールをアップグレードするには、`az aks nodepool upgrade` コマンドに `--name` パラメーターを追加して、ノード プール名を指定します。 例:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>GitHub アクションを手動で実行する

`workflow_dispatch` という新しい `on` トリガーを追加することにより、スケジュールされた実行に加えて、ワークフローを手動で実行できます。 完成したファイルは、下の YAML のようになります。

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>次のステップ

- 最新のノード イメージの詳細については、[AKS のリリース ノート](https://github.com/Azure/AKS/releases)をご覧ください。
- 「[AKS クラスターのアップグレード][cluster-upgrades-article]」によって Kubernetes バージョンをアップグレードする方法を確認します。
- 複数のノード プールの詳細と、[複数のノード プールの作成と管理][use-multiple-node-pools]によってノード プールをアップグレードする方法を確認します。
- [システム ノード プール][system-pools]の詳細を確認します。
- スポット インスタンスを使用してコストを節約する方法については、[スポット ノード プールを AKS に追加する][spot-pools]方法に関するページを参照してください。

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
