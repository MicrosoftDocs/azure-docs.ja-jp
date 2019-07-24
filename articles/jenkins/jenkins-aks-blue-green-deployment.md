---
title: Jenkins とブルー/グリーン デプロイ パターンを使用した Azure Kubernetes Service (AKS) へのデプロイ
description: Jenkins とブルー/グリーン デプロイ パターンを使用した Azure Kubernetes Service (AKS) へのデプロイについて説明します。
ms.service: jenkins
keywords: Jenkins, Azure, 開発, Kubernetes, k8s, AKS, ブルー/グリーン デプロイ, 継続的デリバリー, CD
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/11/2018
ms.openlocfilehash: 93f2ac284931ba664e0965e537e515c824e6f7a6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092295"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Jenkins とブルー/グリーン デプロイ パターンを使用した Azure Kubernetes Service (AKS) へのデプロイ

Azure Kubernetes Service (AKS) を使用すると、ホストされている Kubernetes 環境を管理できます。これによって、コンテナー化されたアプリケーションを迅速かつ簡単にデプロイおよび管理できるようになります。 コンテナーのオーケストレーションに関する知識は必要ありません。 また、AKS では、要求に応じてリソースをプロビジョニング、アップグレード、スケーリングすることにより、実行中の操作およびメンテナンスの負担もなくなります。 アプリケーションをオフラインにする必要はありません。 AKS の詳細については、[AKS のドキュメント](/azure/aks/)を参照してください。

ブルー/グリーン デプロイとは、新しい (グリーン) バージョンのデプロイ中に、既存 (ブルー) のバージョンを実行状態のまま保持する、Azure DevOps の継続的デリバリーのパターンです。 通常、このパターンでは、増加するトラフィックをグリーンのデプロイにリダイレクトする負荷分散を採用します。 監視でインシデントが検出された場合、トラフィックはまだ実行中のブルーのデプロイに再ルーティングされる場合があります。 継続的デリバリーの詳細については、[継続的デリバリーの説明](/azure/devops/what-is-continuous-delivery)に関する記事を参照してください。

このチュートリアルでは、以下のタスクの実行方法について説明します。

> [!div class="checklist"]
> * ブルー/グリーン デプロイ パターンの理解
> * マネージド Kubernetes クラスターの作成
> * Kubernetes クラスターを構成するサンプル スクリプトの実行
> * Kubernetes クラスターの手動での構成
> * Jenkins ジョブの作成と実行

## <a name="prerequisites"></a>前提条件
- [GitHub アカウント](https://github.com):サンプル リポジトリを複製するために GitHub アカウントが必要です。
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest):Kubernetes クラスターを作成するために Azure CLI 2.0 を使用します。
- [Chocolatey](https://chocolatey.org):kubectl をインストールするために使用するパッケージ マネージャーです。
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/):Kubernetes クラスターに対してコマンドを実行するために使用するコマンド ライン インターフェイスです。
- [jq](https://stedolan.github.io/jq/download/):簡便なコマンド ライン JSON プロセッサです。

## <a name="clone-the-sample-app-from-github"></a>GitHub からのサンプル アプリの複製

GitHub の Microsoft リポジトリに、Jenkins とブルー/グリーン パターンを使用して AKS にデプロイする方法を示すサンプル アプリがあります。 このセクションでは、GitHub にそのリポジトリのフォークを作成し、アプリをローカル システムに複製します。

1. GitHub リポジトリで [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git) サンプル アプリを参照します。

    ![Microsoft GitHub リポジトリにあるサンプル アプリのスクリーンショット。](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. リポジトリをフォークします。具体的には、ページの右上で **[Fork]\(フォーク\)** を選択し、手順に従って、お使いの GitHub アカウントにリポジトリをフォークします。

    ![フォークする GitHub オプションのスクリーンショット](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. リポジトリをフォークした後、アカウント名が自分のアカウント名に変更されたことがわかります。また、メモにリポジトリのフォーク元が示されます (Microsoft)。

    ![GitHub アカウント名と注釈のスクリーンショット](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. **[複製またはダウンロード]** を選択します。

    ![リポジトリを複製またはダウンロードする GitHub オプションのスクリーンショット](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. **[Clone with HTTPS]\(HTTPS で複製する\)** ウィンドウで、**コピー** アイコンを選択します。

    ![複製 URL をクリップボードにコピーする GitHub オプションのスクリーンショット](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. ターミナル ウィンドウまたは Git Bash ウィンドウを開きます。

1. リポジトリのローカル コピー (複製) を格納したい場所にディレクトリを変更します。

1. `git clone` コマンドを使用して、先ほどコピーした URL を複製します。

    ![Git Bash の git clone コマンドのスクリーンショット](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Enter キーを押して複製プロセスを開始します。

    ![Git Bash の git clone コマンド結果のスクリーンショット](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. ディレクトリを、アプリのソースの複製が含まれる、新しく作成されたディレクトリに変更します。

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>マネージド Kubernetes クラスターの作成と構成

このセクションでは、次の手順を実行します。

- Azure CLI 2.0 を使用してマネージド Kubernetes クラスターを作成する。
- セットアップ スクリプトを使用するか、手動で、クラスターを設定する方法を学ぶ。
- Azure Container Registry サービスのインスタンスを作成する。

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Azure CLI 2.0 を使用したマネージド Kubernetes クラスターの作成
[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用してマネージド Kubernetes クラスターを作成する場合は、Azure CLI バージョン 2.0.25 以降を使用していることを確認してください。

1. Azure アカウントにサインインします。 次のコマンドを入力した後に、サインインを完了する方法を説明する手順が示されます。 
    
    ```bash
    az login
    ```

1. 前の手順で `az login` コマンドを実行すると、すべての Azure サブスクリプションの一覧が表示されます (サブスクリプション ID も含む)。 この手順では、既定の Azure サブスクリプションを設定します。 &lt;your-subscription-id> プレースホルダーを目的の Azure サブスクリプション ID に置き換えます。 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. リソース グループを作成します。 &lt;your-resource-group-name> プレースホルダーを新しいリソース グループの名前に置き換え、&lt;your-location> プレースホルダーを場所に置き換えます。 `az account list-locations` コマンドにより、すべての Azure の場所が表示されます。 AKS のプレビュー期間中は、すべての場所を使用できるわけではありません。 このとき、無効な場所を入力すると、エラー メッセージに利用可能な場所が一覧表示されます。

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Kubernetes クラスターを作成します。 &lt;your-resource-group-name> を前の手順で作成したリソース グループの名前に置き換え、&lt;you-kubernetes-cluster-name> を新しいクラスターの名前に置き換えます。 (このプロセスは、完了までに数分かかる場合があります)。

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Kubernetes クラスターの設定

AKS でのブルー/グリーン デプロイの設定は、手動で行うことも、前に複製したサンプルで提供されたセットアップ スクリプトを使って実施することもできます このセクションでは、両方の方法について説明します。

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>サンプルのセットアップ スクリプトを使用した Kubernetes クラスターの設定
1. **deploy/aks/setup/setup.sh** ファイルを編集して、次のプレースホルダーを、自分の環境に合った値に置き換えます。 

   - **&lt;your-resource-group-name>**
   - **&lt;your-kubernetes-cluster-name>**
   - **&lt;your-location>**
   - **&lt;your-dns-name-suffix>**

     ![複数のプレースホルダーが強調表示されている、bash での setup.sh スクリプトのスクリーンショット](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. セットアップ スクリプトを実行します。

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>手動での Kubernetes クラスターの設定 
1. Kubernetes 構成をプロファイル フォルダーにダウンロードします。

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. ディレクトリを **deploy/aks/setup** ディレクトリに変更します。 

1. 次の **kubectl** コマンドを実行して、パブリック エンドポイントと 2 つのテスト エンドポイントのサービスを設定します。

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. パブリック エンドポイントとテスト エンドポイントの DNS 名を更新します。 Kubernetes クラスターの作成時に、**MC_&lt;your-resource-group-name>_&lt;your-kubernetes-cluster-name>_&lt;your-location>** という名前付けパターンで、[追加のリソース グループ](https://github.com/Azure/AKS/issues/3)も作成されます。

    リソース グループ内のパブリック IP を特定します。

    ![リソース グループ内のパブリック IP のスクリーンショット](./media/jenkins-aks-blue-green-deployment/publicip.png)

    各サービスで次のコマンドを実行して、外部 IP アドレスを特定します。
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    次のコマンドで、対応する IP アドレスの DNS 名を更新します。

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    `todoapp-test-blue` と `todoapp-test-green` の呼び出しを繰り返します。

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    この DNS 名は、サブスクリプション内で一意である必要があります。 一意性を確保するために、`<your-dns-name-suffix>` を使用できます。

### <a name="create-an-instance-of-container-registry"></a>Container Registry のインスタンスを作成する

1. `az acr create` コマンドを実行して、Container Registry のインスタンスを作成します。 次のセクションでは、`login server` を Docker レジストリの URL として使用できます。

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. `az acr credential` コマンドを実行して、Container Registry の資格情報を表示します。 次のセクションで必要になるため、Docker レジストリのユーザー名とパスワードを書き留めます。

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Jenkins サーバーの準備

このセクションでは、ビルドを実行するために、テストに最適な Jenkins サーバーを準備する方法を説明します。 ただし、Azure でエージェントを活用してお使いのビルドを実行するために、[Azure VM エージェント](https://plugins.jenkins.io/azure-vm-agents)または [Azure Container エージェント](https://plugins.jenkins.io/azure-container-agents)を使用する必要があります。 詳細については、Jenkins の「[security implications of building on master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master)」 (マスターでの構築におけるセキュリティへの影響) の記事を参照してください。

1. [Azure に Jenkins マスターを](https://aka.ms/jenkins-on-azure)デプロイします。

1. SSH を使用してサーバーに接続し、ビルドを実行するサーバーにビルド ツールをインストールします。
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Docker をインストールします](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)。 ユーザー `jenkins` に `docker` コマンドを実行する権限があることを確認します。

1. [kubectl をインストールします](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

1. [jq をダウンロードします](https://stedolan.github.io/jq/download/)。

1. 次のコマンドを使用して jq をインストールします。

   ```bash
   sudo apt-get install jq
   ```
   
1. Jenkins ダッシュボードで次の手順を実行し、Jenkins にプラグインをインストールします。

    1. **[Manage Jenkins]\(Jenkins の管理\)、[Manage Plugins]\(プラグインの管理\)、[Available]\(使用可能\)** の順に選択します。
    1. Azure Container Service プラグインを検索してインストールします。

1. Azure でリソースを管理するための資格情報を追加します。 まだプラグインを持っていない場合は、**Azure 資格情報**プラグインをインストールします。

1. Azure サービス プリンシパルの資格情報を、タイプ **Microsoft Azure サービス プリンシパル**として追加します。

1. タイプ **ユーザー名とパスワード**として、(「Container Registry のインスタンスを作成する」セクションで取得したとおりに)、Azure Docker Registry のユーザー名とパスワードを追加します。

## <a name="edit-the-jenkinsfile"></a>Jenkinsfile の編集

1. お使いのリポジトリで `/deploy/aks/` に移動し、`Jenkinsfile` を開きます。

2. ファイルを次のように更新します。

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Container Registry の資格情報 ID を次のように更新します。
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>ジョブを作成する
1. タイプ **パイプライン**で新しいジョブを追加します。

1. **[Pipeline]\(パイプライン\)** > **[Definition]\(定義\)** > **[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** の順に選択します。

1. &lt;your-forked-repo> で、SCM リポジトリの URL を入力します。

1. `deploy/aks/Jenkinsfile` というスクリプト パスを入力します。

## <a name="run-the-job"></a>ジョブを実行する

1. ローカル環境でプロジェクトを正常に実行できることを確認します。 その方法は次のとおりです。[ローカル コンピューターでプロジェクトを実行します](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)。

1. Jenkins ジョブを実行します。 ジョブを初めて実行するときは、Jenkins によって、既定の非アクティブな環境であるブルーの環境に To Do アプリがデプロイされます。 

1. ジョブが実行されたことを確認するには、次の URL を参照します。
    - パブリック エンドポイント: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - 青のエンドポイント - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - グリーンのエンドポイント - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

パブリックおよびブルーのテスト エンドポイントでは同じ更新が行われますが、グリーンのエンドポイントでは既定の tomcat イメージが表示されます。  

ビルドを複数回実行する場合は、ブルーとグリーンのデプロイが順に繰り返されます。 つまり、現在の環境がブルーの場合、ジョブはグリーンの環境にデプロイしてテストを行います。 その後、テストが順調な場合、ジョブはアプリケーションのパブリック エンドポイントを更新して、グリーンの環境にトラフィックをルーティングします。

## <a name="additional-information"></a>追加情報

ダウンタイムなしのデプロイの詳細については、この[クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment)を参照してください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースは、不要になったら削除できます。

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>トラブルシューティング

Jenkins プラグインでバグが発生した場合は、[Jenkins JIRA](https://issues.jenkins-ci.org/) で特定のコンポーネントについて問題を報告してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Jenkins とブルー/グリーンデプロイ パターンを使用した AKS へのデプロイ方法を説明しました。 Azure Jenkins プロバイダーの詳細については、Azure 上の Jenkins に関するサイトを参照してください。

> [!div class="nextstepaction"]
> [Azure 上の Jenkins](/azure/jenkins/)