---
title: 'チュートリアル: Azure Arc 対応 Kubernetes クラスターを使用して GitOps で CI/CD を実装する'
description: このチュートリアルでは、Azure Arc enabled Kubernetes クラスターで GitOps を使用して CI/CD ソリューションを設定する手順について説明します。 このワークフローの概念については、「GitOps を使用した CI/CD ワークフロー - Azure Arc 対応 Kubernetes」記事を参照してください。
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: 72caca47cde960eb7298ec2cf0c6994755cb3159
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121611"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>チュートリアル: Azure Arc 対応 Kubernetes クラスターを使用して GitOps で CI/CD を実装する


このチュートリアルでは、Azure Arc 対応 Kubernetes クラスターで GitOps を使用して CI/CD ソリューションを設定します。 サンプルの Azure Vote アプリを使用して、次のことを行います。

> [!div class="checklist"]
> * Azure Arc 対応 Kubernetes クラスターを作成する。
> * アプリケーションおよび GitOps リポジトリを Azure Repos に接続する。
> * CI/CD パイプラインをインポートする。
> * Azure Container Registry (ACR) を Azure DevOps および Kubernetes に接続する。
> * 環境変数グループを作成する。
> * `dev` および `stage` 環境をデプロイする。
> * アプリケーション環境をテストする。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure DevOps、Azure Repos および Pipelines、Azure CLI について理解していることを前提としています。

* [Azure DevOps Services](https://dev.azure.com/) にサインインします。
* [前のチュートリアル](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster)を完了し、CI/CD 環境用に GitOps をデプロイする方法を学習します。
* この機能の[利点とアーキテクチャ](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-configurations)について理解します。
* 以下が用意されていることを確認します。
  * **arc-cicd-cluster** という名前の[接続済みの Azure Arc 対応 Kubernetes クラスター](https://docs.microsoft.com/azure/azure-arc/kubernetes/quickstart-connect-cluster#connect-an-existing-kubernetes-cluster)。
  * [AKS 統合](https://docs.microsoft.com/azure/aks/cluster-container-registry-integration)または[非 AKS クラスター認証](https://docs.microsoft.com/azure/container-registry/container-registry-auth-kubernetes)を使用して接続された Azure Container Registry (ACR)。
  * [Azure Repos](https://docs.microsoft.com/azure/devops/repos/get-started/what-is-repos) および [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-get-started) に対する "ビルド管理者" および "プロジェクト管理者" アクセス許可。
* 次の Azure Arc 対応 Kubernetes CLI 拡張機能 (バージョン 1.0.0 以上) をインストールします。

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * これらの拡張機能を最新バージョンに更新するには、次のコマンドを実行します。

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>アプリケーションおよび GitOps リポジトリを Azure Repos にインポートする

[アプリケーション リポジトリ](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#application-repo)と [GitOps リポジトリ](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#gitops-repo)を Azure Repos にインポートします。 このチュートリアルでは、次のサンプル リポジトリを使用します。

* **arc-cicd-demo-src** アプリケーション リポジトリ
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * GitOps を使用してデプロイするサンプルの Azure Vote アプリが含まれています。
* **arc-cicd-demo-gitops** GitOps リポジトリ
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Azure Vote アプリを格納するクラスター リソースのベースとして機能します。

[Git リポジトリのインポート](https://docs.microsoft.com/azure/devops/repos/git/import-git-repository)の詳細を確認してください。

>[!NOTE]
> アプリケーション リポジトリと GitOps リポジトリ用に 2 つの異なるリポジトリをインポートして使用すると、セキュリティとシンプルさを向上させることができます。 アプリケーションおよび GitOps リポジトリのアクセス許可と可視性は、個別に調整できます。
> たとえば、クラスター管理者は、クラスターの望ましい状態に関連する変更をアプリケーション コードで検出できない場合があります。 逆に、アプリケーション開発者は、各環境の固有のパラメーターを把握する必要がありません。そのパラメーターを網羅する一連のテスト値で十分な場合があります。

## <a name="connect-the-gitops-repo"></a>GitOps リポジトリを接続する

アプリを継続的にデプロイするには、GitOps を使用してアプリケーション リポジトリをクラスターに接続します。 **arc-cicd-demo-gitops** GitOps リポジトリには、**arc-cicd-cluster** クラスターでアプリを稼働させるための基本的なリソースが含まれています。

最初の GitOps リポジトリには、デプロイ環境に対応する **dev** および **stage** 名前空間を作成する[マニフェスト](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml)のみが含まれています。

作成した GitOps 接続により、自動的に以下のことが行われます。
* マニフェスト ディレクトリ内でマニフェストを同期します。
* クラスターの状態を更新します。

この CI/CD ワークフローでは、アプリをデプロイするための追加のマニフェストがマニフェスト ディレクトリに取り込まれます。


1. Azure Repos で、新しくインポートした **arc-cicd-demo-gitops** リポジトリに対する[新しい GitOps 接続を作成](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster)します。

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Flux ではベース パスとして `arc-cicd-cluster/manifests` ディレクトリ "*のみ*" が使用されることを確認します。 このパスは、次の operator パラメーターを使用して定義します。

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > HTTPS 接続文字列を使用していて、接続の問題が発生した場合は、URL でユーザー名のプレフィックスを省略してください。 たとえば、`https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` では、`alice@` を削除してください。 その代わり、`--https-user` ではユーザーを指定します (例: `--https-user alice`)。

1. Azure portal でデプロイの状態を確認します。
   * 成功した場合は、`dev` と `stage` 両方の名前空間がクラスター内に作成されます。

## <a name="import-the-cicd-pipelines"></a>CI/CD パイプラインをインポートする

GitOps 接続の同期が完了したので、マニフェストを作成する CI/CD パイプラインをインポートする必要があります。

このアプリケーション リポジトリには、PR、CI、CD に使用するパイプラインを含む `.pipeline` フォルダーが含まれています。 サンプル リポジトリで提供されている 3 つのパイプラインをインポートし、名前を変更します。

| パイプライン ファイルの名前 | 説明 |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | **arc-cicd-demo-src PR** という名前のアプリケーション PR パイプライン |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | **arc-cicd-demo-src CI** という名前のアプリケーション CI パイプライン |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | **arc-cicd-demo-src CD** という名前のアプリケーション CD パイプライン |



## <a name="connect-your-acr"></a>ACR を接続する
Docker イメージを格納および取得するために、パイプラインとクラスターの両方で ACR を利用します。

### <a name="connect-acr-to-azure-devops"></a>ACR を Azure DevOps に接続する
CI プロセスの間、アプリケーション コンテナーをレジストリにデプロイします。 まず、Azure サービス接続を作成します。

1. Azure DevOps では、プロジェクト設定ページから **[サービス接続]** ページを開きます。 TFS では、上部のメニュー バーの **設定** アイコンから **[サービス]** ページを開きます。
2. **[+ 新しいサービス接続]** を選択し、必要なサービス接続の種類を選択します。
3. そのサービス接続のパラメーターを入力します。 このチュートリアルでは、次を使用します。
   * サービス接続に **arc-demo-acr** という名前を付けます。 
   * リソース グループとして **myResourceGroup** を選択します。
4. **[すべてのパイプラインへのアクセス許可を与える]** を選択します。 
   * このオプションにより、サービス接続に対して YAML パイプライン ファイルが承認されます。 
5. **[OK]** を選択して接続を作成します。

### <a name="connect-acr-to-kubernetes"></a>ACR を Kubernetes に接続する
Kubernetes クラスターが ACR からイメージをプルできるようにします。 非公開の場合は、認証が必要になります。

#### <a name="connect-acr-to-existing-aks-clusters"></a>ACR を既存の AKS クラスターに接続する

次のコマンドを使用して、既存の ACR と既存の AKS クラスターを統合します。

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>イメージのプル シークレットを作成する

非 AKS およびローカル クラスターを ACR に接続するには、イメージのプル シークレットを作成します。 Kubernetes では、イメージのプル シークレットを使用して、レジストリの認証に必要な情報を格納します。

次の `kubectl` コマンドを使用して、イメージのプル シークレットを作成します。 `dev` と `stage` 両方の名前空間に対して繰り返します。
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> 各ポッドで imagePullSecret を設定する必要をなくすために、`dev` および `stage` 名前空間でサービス アカウントに imagePullSecret を追加することを検討してください。 詳細については、[Kubernetes のチュートリアル](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)を参照してください。

## <a name="create-environment-variable-groups"></a>環境変数グループを作成する

### <a name="app-repo-variable-group"></a>アプリ リポジトリの変数グループ
**az-vote-app-dev** という名前の[変数グループを作成](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups)します。 次の値を設定します。

| 変数 | 値 |
| -------- | ----- |
| AZ_ACR_NAME | (自分の ACR インスタンス。例: azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (自分の Azure サービス接続。このチュートリアルで既に出てきた **arc-demo-acr** になります) |
| AZURE_VOTE_IMAGE_REPO | Azure Vote アプリ リポジトリの完全なパス (例: azurearctest.azurecr.io/azvote) |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | GitOps リポジトリの Git 接続文字列 |
| PAT | ソースの読み取り/書き込みアクセス許可が付与されている[作成済みの PAT トークン](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?#create-a-pat)。 これは、後で `stage` 変数グループを作成する際に使用するために保存します。 |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> PAT をシークレットの種類としてマークします。 アプリケーションでは、[Azure KeyVault](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)のシークレットをリンクすることを検討してください。
>
### <a name="stage-environment-variable-group"></a>環境変数グループをステージする

1. **az-vote-app-dev** 変数グループを複製します。
1. その名前を **az-vote-app-stage** に変更します。
1. 対応する変数に次の値を設定します。

| 変数 | 値 |
| -------- | ----- |
| ENVIRONMENT_NAME | 段階 |
| TARGET_NAMESPACE | `stage` |

これで、`dev` および `stage` 環境にデプロイする準備ができました。

## <a name="deploy-the-dev-environment-for-the-first-time"></a>開発環境を初めてデプロイする
CI および CD パイプラインが作成されたら、CI パイプラインを実行して、初めてアプリをデプロイします。

### <a name="ci-pipeline"></a>CI パイプライン

CI パイプラインの初回実行中、サービス接続名の読み取りでリソース承認エラーが発生する場合があります。
1. アクセスされている変数が AZURE_SUBSCRIPTION であることを確認します。
1. 使用を承認します。
1. パイプラインを再実行してください。

CI パイプライン:
* アプリケーションの変更が、デプロイに対する自動品質チェックすべてに合格したことを確認します。
* PR パイプラインで完了できなかった追加の検証を実行します。
    * GitOps に固有のパイプラインでは、CD パイプラインによってデプロイされるコミットの成果物も発行されます。
* Docker イメージが変更され、新しいイメージがプッシュされたことを確認します。

### <a name="cd-pipeline"></a>CD パイプライン
CI パイプラインの実行が成功すると、デプロイ プロセスを完了するために CD パイプラインがトリガーされます。 各環境に段階的にデプロイします。

> [!TIP]
> CD パイプラインが自動的にトリガーされない場合:
> 1. その名前が [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) のブランチ トリガーと一致することを確認します。
>    * これは、`arc-cicd-demo-src CI` である必要があります。
> 1. CI パイプラインを再実行します。

GitOps リポジトリに対するテンプレートとマニフェストの変更が生成されると、CD パイプラインによってコミットが作成、プッシュされ、承認を得るために PR が作成されます。
1. `Create PR` タスクの出力に示されている PR リンクを開きます。
1. GitOps リポジトリに対する変更を確認します。 次のような結果が表示されます。
   * 高レベルの Helm テンプレートの変更。
   * 望ましい状態への基本的な変更を示す低レベルの Kubernetes マニフェスト。 Flux により、これらのマニフェストがデプロイされます。
1. すべて問題がなければ、PR を承認して完了します。

1. 数分後、Flux によって変更が取得され、デプロイが開始されます。
1. `kubectl` を使用してポートをローカルに転送し、次を使用して、アプリが正しく動作することを確認します。

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. ブラウザーで Azure Vote アプリ (`http://localhost:8080/`) を表示します。

1. お気に入りに投票し、アプリに変更を加える準備をします。

## <a name="set-up-environment-approvals"></a>環境の承認を設定する
アプリのデプロイ時には、コードまたはテンプレートを変更できるだけでなく、意図せずにクラスターを正常でない状態にする場合もあります。

開発環境でデプロイ後に中断が明らかになる場合は、環境の承認を使用して、その後の環境に進まないようにしてください。

1. Azure DevOps プロジェクトで、保護する必要のある環境に移動します。
1. リソースの **[承認とチェック]** に移動します。
1. **［作成］** を選択します
1. 承認者とオプションのメッセージを指定します。
1. もう一度 **[作成]** を選択し、手動による承認チェックの追加を完了します。

詳細については、[承認とチェックの定義](https://docs.microsoft.com/azure/devops/pipelines/process/approvals)に関するチュートリアルを参照してください。

次に CD パイプラインが実行されると、そのパイプラインは GitOps PR の作成後に一時停止します。 変更が正常に同期され、基本的な機能が渡されることを確認します。 変更が次の環境に流れるように、パイプラインからのチェックを承認します。

## <a name="make-an-application-change"></a>アプリケーションの変更を行う

このベースライン セットのテンプレートやマニフェストがクラスターの状態を表している場合、アプリに小さな変更を加えます。

1. **arc-cicd-demo-src** リポジトリにある [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) ファイルを編集します。

2. "猫と犬" では十分な票数が得られないため、票数を増やすために "タブとスペース" に変更します。

3. 新しいブランチで変更をコミットして、それをプッシュし、pull request を作成します。
   * これは、CI/CD ライフサイクルを開始する典型的な開発者フローです。

## <a name="pr-validation-pipeline"></a>PR 検証パイプライン

この PR パイプラインは、誤った変更に対する防御の最前線となります。 通常のアプリケーション コードの品質チェックには、リンティングとスタティック分析が含まれます。 また、GitOps の観点から、結果として得られるインフラストラクチャをデプロイするために同じ品質を保証する必要があります。

アプリケーションの Dockerfile と Helm チャートでは、アプリケーションと同じようにリンティングを使用できます。

リンティング時に検出されるエラーは、次のように多岐にわたります。
* 形式が正しくない YAML ファイル
* ベスト プラクティスの提案 (アプリケーションの CPU やメモリの上限の設定など)

> [!NOTE]
> 実際のアプリケーションで Helm のリンティングを最大限に利用するには、実際の環境で使用されているものとほぼ同じ値に置き換える必要があります。

パイプラインの実行中に検出されたエラーは、実行のテスト結果セクションに表示されます。 ここでは、次の操作を実行できます。
* エラーの種類に関する有用な統計情報を追跡します。
* それらが検出された最初のコミットを検出します。
* エラーの原因となったコード セクションへのトレース スタイル リンクをスタックします。

パイプライン実行が完了すると、アプリケーション コードとそれをデプロイするテンプレートの品質を保証したことになります。 これで、PR を承認して完了できるようになりました。 CD パイプラインをトリガーする前に、この CI は再度実行され、テンプレートとマニフェストが再生成されます。

> [!TIP]
> 実際の環境では、PR が品質チェックに必ず合格するように忘れずにブランチ ポリシーを設定してください。 詳細については、[ブランチ ポリシーの設定](https://docs.microsoft.com/azure/devops/repos/git/branch-policies)に関する記事を参照してください。

## <a name="cd-process-approvals"></a>CD プロセスの承認

CI パイプラインの実行が成功すると、デプロイ プロセスを完了するために CD パイプラインがトリガーされます。 CD パイプラインを初めて実行したときと同様に、各環境に段階的にデプロイします。 今回、このパイプラインでは、各デプロイ環境を承認する必要があります。

1. `dev` 環境へのデプロイを承認します。
1. GitOps リポジトリに対するテンプレートとマニフェストの変更が生成されると、CD パイプラインによってコミットが作成、プッシュされ、承認を得るために PR が作成されます。
1. タスクに示された PR リンクを開きます。
1. GitOps リポジトリに対する変更を確認します。 次のような結果が表示されます。
   * 高レベルの Helm テンプレートの変更。
   * 望ましい状態への基本的な変更を示す低レベルの Kubernetes マニフェスト。
1. すべて問題がなければ、PR を承認して完了します。
1. デプロイが完了するまで待ちます。
1. 基本のスモーク テストとして、アプリケーション ページに移動し、投票アプリにタブとスペースが表示されていることを確認します。
   * `kubectl` を使用してポートをローカルに転送し、`kubectl port-forward -n dev svc/azure-vote-front 8080:80` を使用して、アプリが正しく動作することを確認します。
   * ブラウザーで Azure Vote アプリ (http://localhost:8080/ ) を表示し、投票の選択肢がタブとスペースに変更されていることを確認します。 
1. `stage` 環境に対して手順 1 から 7 を繰り返します。

これでデプロイは完了しました。 これにより、CI/CD ワークフローが終了します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用する予定がなければ、次の手順でリソースをすべて削除します。

1. Azure Arc GitOps 構成の接続を削除します。
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. `dev` 名前空間を削除します。
   * `kubectl delete namespace dev`

3. `stage` 名前空間を削除します。
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリケーション開発からデプロイまでの DevOps を実装する完全な CI/CD ワークフローを設定しました。 アプリを変更すると、自動的に検証とデプロイがトリガーされ、手動の承認によって制御されます。

概念に関する記事に進み、Azure Arc 対応 Kubernetes での GitOps と構成の詳細を確認してください。

> [!div class="nextstepaction"]
> [GitOps を使用した CI/CD ワークフロー - Azure Arc 対応 Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd)
