---
title: Azure Pipelines を使用した継続的インテグレーション
description: Azure Resource Manager テンプレート (ARM テンプレート) を継続的にビルド、テスト、およびデプロイする方法について説明します。
ms.date: 03/02/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3ff98c1c033c6da4b6bdf40c3b8ecb3347601741
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722820"
---
# <a name="tutorial-continuous-integration-of-arm-templates-with-azure-pipelines"></a>チュートリアル:Azure Pipelines を使用した ARM テンプレートの継続的インテグレーション

[前のチュートリアル](./deployment-tutorial-linked-template.md)では、リンク済みテンプレートをデプロイしました。  このチュートリアルでは、Azure Pipelines を使用して Azure Resource Manager テンプレート (ARM テンプレート) プロジェクトを継続的にビルドおよびデプロイする方法について説明します。

Azure DevOps は、チームによる作業の計画、コード開発での共同作業、アプリケーションのビルドおよびデプロイをサポートするために、開発者サービスを提供します。 開発者は、Azure DevOps Services を使用してクラウドで作業できます。 Azure DevOps は、Web ブラウザーまたは IDE クライアントを介してアクセス可能な機能の統合セットを提供します。 Azure パイプラインは、この機能の 1 つです。 Azure Pipelines は、完全な機能を備えた継続的インテグレーション (CI) および継続的デリバリー (CD) サービスです。 任意の Git プロバイダーと連携し、ほとんどの主要クラウド サービスにデプロイできます。 その後、Microsoft Azure、Google Cloud Platform、アマゾン ウェブ サービスのコードのビルド、テスト、およびデプロイを自動化できます。

> [!NOTE]
> プロジェクト名を選択します。 チュートリアルを実行する場合は、それぞれの **AzureRmPipeline** をプロジェクト名で置き換えます。
> このプロジェクト名は、リソース名を生成するために使用されます。  リソースの 1 つはストレージ アカウントです。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。 名前は一意である必要があります。 このテンプレートでは、プロジェクト名に **store** を追加したものがストレージ アカウント名になります。プロジェクト名は 3 文字を超え、11 文字未満であることが必要です。 そのためプロジェクト名は、ストレージ アカウント名の要件を満たしていること、また 11 文字未満であることが必要となります。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * GitHub レポジトリを準備する
> * Azure DevOps プロジェクトを作成する
> * Azure パイプラインを作成する
> * パイプラインのデプロイを確認する
> * テンプレートを更新して再デプロイする
> * リソースをクリーンアップする

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* テンプレートのリポジトリの作成に使用する **GitHub アカウント**。 持っていない場合は、[無料で作成](https://github.com)できます。 GitHub リポジトリの使用に関する詳細については、[GitHub リポジトリの構築](/azure/devops/pipelines/repos/github)に関するページを参照してください。
* **Git をインストールします**。 このチュートリアルの手順では、*Git Bash* または *Git Shell* を使用します。 手順については、[Git のインストールに関するページ](https://www.atlassian.com/git/tutorials/install-git)を参照してください。
* **Azure DevOps 組織**。 ない場合は、無料で作成できます。 [組織またはプロジェクト コレクションの作成](/azure/devops/organizations/accounts/create-organization)に関するページを参照してください。
* (省略可能) **Visual Studio Code と Resource Manager ツール拡張機能**。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

## <a name="prepare-a-github-repository"></a>GitHub レポジトリを準備する

GitHub を使用して、Resource Manager テンプレートを含むプロジェクトのソース コードを保存します。 その他のサポートされているリポジトリを使用する場合は、[Azure DevOps でサポートされているリポジトリ](/azure/devops/pipelines/repos/)に関するページを参照してください。

### <a name="create-a-github-repository"></a>GitHub リポジトリを作成する

GitHub アカウントをお持ちでない場合は、[前提条件](#prerequisites)を参照してください。

1. [GitHub](https://github.com) にサインインします。
1. 右上隅に表示されるご自身のアカウント イメージを選択し、**自分のリポジトリ** を選択します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines GitHub リポジトリの作成](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. 緑色の **[New]** ボタンを選択します。
1. **[Repository name]** にリポジトリ名を入力します。  たとえば、**AzureRmPipeline-repo** とします。 忘れずにそれぞれの **AzureRmPipeline** をプロジェクト名で置き換えてください。 このチュートリアルを **[Public]** または **[Private]** のどちらにするかを選択できます。 続いて、 **[Create repository]** を選択します。
1. URL を書き留めます。 リポジトリの URL は、 `https://github.com/[YourAccountName]/[YourRepositoryName]` 形式です。

このリポジトリは、*リモート リポジトリ* と呼ばれます。 同じプロジェクトの開発者は、各自の "*ローカル リポジトリ*" を複製し、変更をリモート リポジトリにマージできます。

### <a name="clone-the-remote-repository"></a>リモート リポジトリを複製する

1. Git Shell または Git Bash を開きます。 「[前提条件](#prerequisites)」を参照してください。
1. 現在のフォルダーが **GitHub** であることを確認します。
1. 次のコマンドを実行します。

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    `[YourAccountName]` は GitHub のアカウント名で、 `[YourGitHubRepositoryName]` は前の手順で作成したリポジトリ名で置換します。

_CreateWebApp_ フォルダーは、テンプレートが格納されているフォルダーです。 `pwd` コマンドを実行すると、フォルダー パスが表示されます。 次の手順に従い、このパスにテンプレートを保存します。

### <a name="download-a-quickstart-template"></a>クイックスタート テンプレートをダウンロードする

テンプレートを作成する代わりに、テンプレートをダウンロードして _CreateWebApp_ フォルダーに保存してもかまいません。

* メイン テンプレート: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* リンク済みテンプレート: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

パイプラインでは、フォルダー名とファイル名の両方をそのまま使用します。 これらの名前を変更する場合は、パイプラインで使用する名前を更新する必要があります。

### <a name="push-the-template-to-the-remote-repository"></a>テンプレートをリモート リポジトリにプッシュする

ローカル リポジトリに _azuredeploy.json_ が追加されました。 次に、テンプレートをリモート リポジトリにアップロードします。

1. *Git Shell* または *Git Bash* を開いていない場合は開きます。
1. ローカル リポジトリでディレクトリを _CreateWebApp_ フォルダーに変更します。
1. フォルダー内に _azuredeploy.json_ ファイルがあることを確認します。
1. 次のコマンドを実行します。

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin main
    ```

    LF に関する警告が表示されることがあります。 この警告は無視できます。 **main** はメイン ブランチです。  通常は、更新ごとにブランチを作成します。 チュートリアルを簡単にするために、メイン ブランチを直接使用します。

1. ブラウザーから GitHub リポジトリに移動します。 URL は `https://github.com/[YourAccountName]/[YourGitHubRepository]` です。 _CreateWebApp_ フォルダーがあって、その中に 2 つのファイルが格納されています。
1. _azuredeploy.json_ を選択してテンプレートを開きます。
1. **[Raw]\(未加工\)** ボタンを選択します。 URL は `https://raw.githubusercontent.com` で始まります。
1. URL のコピーを作成します。 後でこのチュートリアルの中でパイプラインを構成する際に、この値を指定する必要があります。

ここまでで、GitHub リポジトリを作成して、リポジトリにテンプレートをアップロードできました。

## <a name="create-a-devops-project"></a>DevOps プロジェクトを作成する

次の手順に進むには、DevOps 組織が必要です。 ない場合は、[前提条件](#prerequisites)を参照してください。

1. [Azure DevOps](https://dev.azure.com) にサインインします。
1. 左から DevOps 組織を選択します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines Azure DevOps プロジェクトの作成](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. **[新しいプロジェクト]** を選択します。 プロジェクトがない場合は、プロジェクトの作成ページが自動的に開かれます。
1. 次の値を入力します。

    * **[プロジェクト名]** : プロジェクトの名前を入力します。 チュートリアルの最初に選択したプロジェクト名を使用することができます。
    * **[バージョン コントロール]** : **[Git]** を選択します。 **[バージョン コントロール]** を表示するには、 **[詳細設定]** の展開が必要になることがあります。

    他のプロパティには既定値をそのまま使用します。
1. **［作成］** を選択します

プロジェクトを Azure にデプロイするために使用するサービス接続を作成します。

1. 左側のメニューの下部にある **[Project settings] (プロジェクトの設定)** を選択します。
1. **[パイプライン]** の下にある **[サービス接続]** を選択します。
1. **[サービス接続の作成]** を選択し、 **[Azure Resource Manager]** を選択して、 **[次へ]** を選択します。
1. **[サービス プリンシパル]** を選択し、 **[次へ]** を選択します。
1. 次の値を入力します。

    * **[スコープのレベル]** : **[サブスクリプション]** を選択します。
    * **[サブスクリプション]** : ご使用のサブスクリプションを選択します。
    * **リソース グループ**:空白のままにします。
    * **[接続名]** : 接続名を入力します。 たとえば、**AzureRmPipeline-conn** とします。 パイプラインを作成するときに必要になるので、この名前を書き留めておきます。
    * **[すべてのパイプラインへのアクセス許可を与える]** : (オン)
1. **[保存]** を選択します。

## <a name="create-a-pipeline"></a>パイプラインを作成する

ここまでで、以下のタスクが完了しました。  GitHub と DevOps を使い慣れており、前のセクションをスキップした場合は、続行する前に以下のタスクを完了する必要があります。

* GitHub リポジトリを作成し、リポジトリの _CreateWebApp_ フォルダーにこのテンプレートを保存する。
* DevOps プロジェクトを作成し、Azure Resource Manager サービス接続を作成する。

テンプレートをデプロイする手順を含むパイプラインを作成するには、以下の手順を実行します。

1. 左側のメニューから **[パイプライン]** を選択します。
1. **[パイプラインを作成]** を選択します。
1. **[接続]** タブで **[GitHub]** を選択します。 尋ねられた場合は GitHub の資格情報を入力し、指示に従います。 次の画面が表示されたら、 **[Only select repositories] (リポジトリのみ選択)** を選択し、一覧にリポジトリがあることを確認してから **[Approve & Install] (承認してインストール)** を選択します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines リポジトリのみ選択](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. **[選択]** タブで、リポジトリを選択します。 既定の名前は `[YourAccountName]/[YourGitHubRepositoryName]` です。
1. **[構成]** タブで **[Starter pipeline] (スターター パイプライン)** を選択します。 2 つのスクリプト手順が記述された _azure-pipelines.yml_ パイプライン ファイルが表示されます。
1. _yml_ ファイルから 2 つのスクリプト ステップを削除します。
1. **steps:** の後の行にカーソルを移動します。
1. 画面の右側にある **[アシスタントを表示する]** を選択して **[タスク]** ペインを開きます。
1. **[ARM template deployment]\(ARM テンプレートのデプロイ\)** を選択します。
1. 次の値を入力します。

    * **deploymentScope**: **[リソース グループ]** を選択します。 スコープの詳細については、「[デプロイのスコープ](deploy-rest.md#deployment-scope)」を参照してください。
    * **Azure Resource Manager 接続**: 前に作成したサービス接続名を選択します。
    * **サブスクリプション**:ターゲット サブスクリプション ID を指定します。
    * **アクション**:**Create Or Update Resource Group** アクションは、2 つのアクションを行います。1. 新しいリソース グループ名が提供されている場合は、リソース グループを作成します。2. 指定されたテンプレートをデプロイします。
    * **[リソース グループ]** :新しいリソース グループの名前を入力します。 たとえば、**AzureRmPipeline-rg** です。
    * **[場所]** :リソース グループの場所を選択します (**米国中部** など)。
    * **テンプレートの場所**: **ファイルの URL** を選択します。この URL を使用してテンプレート ファイルが検索されます。 メイン テンプレートで _relativePath_ が使用され、_relativePath_ は URI ベースのデプロイでのみサポートされるため、ここでは URL を使用する必要があります。
    * **テンプレート リンク**: 「[GitHub リポジトリを準備する](#prepare-a-github-repository)」セクションの最後にある URL を入力します。 これは `https://raw.githubusercontent.com` で始まります。
    * **テンプレート パラメーターのリンク**: このフィールドは空のままにします。 **[テンプレート パラメーターのオーバーライド]** にパラメーターの値を指定します。
    * **[テンプレート パラメーターのオーバーライド]** : 「 `-projectName [EnterAProjectName]` 」と入力します。
    * **デプロイ モード**: **[増分]** を選択します。
    * **デプロイ名**: 「**DeployPipelineTemplate**」と入力します。 **[デプロイ名]** は、 **[Advanced]\(詳細\)** を選択すると表示されます。

    ![スクリーンショットは、必要な値が入力された [ARM template deployment]\(ARM テンプレートのデプロイ\) ページを示しています。](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. **[追加]** を選択します。

    このタスクの詳細については、「[Azure リソース グループの配置タスク](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)」と [Azure Resource Manager テンプレートのデプロイ タスク](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)に関するページを参照してください。

    _yml_ ファイルは次のようになります。

    !["Review your pipeline YAML" (パイプライン YAML のレビュー) というタイトルの新しいパイプラインが表示された [レビュー] ページを示すスクリーンショット。](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. **[保存および実行]** を選択します。
1. **[保存および実行]** ペインから、もう一度 **[保存および実行]** を選択します。 YAML ファイルのコピーが接続されているリポジトリに保存されます。 リポジトリを参照すると、YAML ファイルを確認できます。
1. パイプラインが正常に実行されることを確認します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>デプロイを検証する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. リソース グループを開きます。 この名前は、パイプラインの YAML ファイルで指定したものです。 作成した 1 つのストレージ アカウントが表示されます。 ストレージ アカウント名は、**store** で始まります。
1. ストレージ アカウント名を選択して開きます。
1. **[プロパティ]** を選択します。 **[レプリケーション]** が **[ローカル冗長ストレージ (LRS)]** になっていることに注意してください。

## <a name="update-and-redeploy"></a>更新して再デプロイする

テンプレートを更新してリモート リポジトリに変更をプッシュすると、パイプラインは自動的にリソースを更新します。今回の場合は、ストレージ アカウントが更新されます。

1. Visual Studio Code または任意のテキスト エディターで、ローカル リポジトリの _linkedStorageAccount.json_ を開きます。
1. **storageAccountType** の **defaultValue** を **Standard_GRS** に更新します。 次のスクリーンショットをご覧ください。

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml 更新](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. 変更を保存します。
1. Git Bash/Shell から次のコマンドを実行して、リモート リポジトリに変更をプッシュします。

    ```bash
    git pull origin main
    git add .
    git commit -m "Update the storage account type."
    git push origin main
    ```

    最初のコマンド (`pull`) は、ローカル リポジトリをリモート リポジトリと同期します。 リモート リポジトリに追加されたのは、パイプラインの YAML ファイルのみです。 `pull` コマンドを実行すると、YAML ファイルのコピーがローカル ブランチにダウンロードされます。

    4 つ目のコマンド (`push`) によって、修正後の _linkedStorageAccount.json_ ファイルがリモート リポジトリにアップロードされます。 リモート リポジトリのメイン ブランチが更新されたので、パイプラインが再度実行されます。

変更内容を確認するには、ストレージ アカウントのレプリケーション プロパティを確認できます。 「[デプロイを検証する](#verify-the-deployment)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

GitHub リポジトリと Azure DevOps プロジェクトを削除しても構いません。

## <a name="next-steps"></a>次のステップ

お疲れさまでした。Resource Manager テンプレートをデプロイするチュートリアルは以上です。 ご意見やご提案がありましたら、フィードバック セクションからお寄せください。 よろしくお願いいたします。
テンプレートに関するより高度な概念を学ぶ準備ができました。 次のチュートリアルでは、デプロイするリソースを効率よく定義できるテンプレート リファレンス ドキュメントの利用について詳しく取り上げます。

> [!div class="nextstepaction"]
> [テンプレート リファレンスの利用](./template-tutorial-use-template-reference.md)
