---
title: Azure Pipelines を使用した継続的インテグレーション | Microsoft Docs
description: Azure Resource Manager テンプレートを継続的にビルド、テスト、およびデプロイする方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064736"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>チュートリアル:Azure Pipelines を使用した Azure Resource Manager テンプレートの継続的インテグレーション

Azure Pipelines を使用して Azure Resource Manager テンプレート プロジェクトを継続的にビルドおよびデプロイする方法について説明します。

Azure DevOps は、チームによる作業の計画、コード開発での共同作業、アプリケーションのビルドおよびデプロイをサポートするために、開発者サービスを提供します。 開発者は、Azure DevOps Services を使用してクラウドで作業できます。 Azure DevOps は、Web ブラウザーまたは IDE クライアントを介してアクセス可能な機能の統合セットを提供します。 Azure パイプラインは、この機能の 1 つです。 Azure Pipelines は、完全な機能を備えた継続的インテグレーション (CI) および継続的デリバリー (CD) サービスです。 任意の Git プロバイダーと連携し、ほとんどの主要クラウド サービスにデプロイできます。 その後、Microsoft Azure、Google Cloud Platform、アマゾン ウェブ サービスのコードのビルド、テスト、およびデプロイを自動化できます。

このチュートリアルは、Azure DevOps Services および Azure Pipelines を初めて使う Azure Resource Manager テンプレート開発者に適しています。 GitHub と DevOps を使い慣れている方は、「[パイプラインを作成する](#create-a-pipeline)」に進んでください。

> [!NOTE]
> プロジェクト名を選択します。 チュートリアルを実行する場合は、それぞれの **AzureRmPipeline** をプロジェクト名で置き換えます。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * GitHub レポジトリを準備する
> * Azure DevOps プロジェクトを作成する
> * Azure パイプラインを作成する
> * パイプラインのデプロイを確認する
> * テンプレートを更新して再デプロイする
> * リソースのクリーンアップ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* テンプレートのリポジトリの作成に使用する **GitHub アカウント**。 持っていない場合は、[無料で作成](https://github.com)できます。 GitHub リポジトリの使用に関する詳細については、[GitHub リポジトリの構築](/azure/devops/pipelines/repos/github)に関するページを参照してください。
* **Git をインストールします**。 このチュートリアルの手順では、*Git Bash* または *Git Shell* を使用します。 手順については、[Git のインストールに関するページ]( https://www.atlassian.com/git/tutorials/install-git)を参照してください。
* **Azure DevOps 組織**。 ない場合は、無料で作成できます。 [組織またはプロジェクト コレクションの作成]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)に関するページを参照してください。
* **Resource Manager ツール拡張機能を持つ [Visual Studio Code](https://code.visualstudio.com/)** 。 [拡張機能のインストールに関する記事](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)を参照してください。

## <a name="prepare-a-github-repository"></a>GitHub レポジトリを準備する

GitHub を使用して、Resource Manager テンプレートを含むプロジェクトのソース コードを保存します。 その他のサポートされているリポジトリを使用する場合は、[Azure DevOps でサポートされているリポジトリ](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types)に関するページを参照してください。

### <a name="create-a-github-repository"></a>GitHub リポジトリを作成する

GitHub アカウントをお持ちでない場合は、[前提条件](#prerequisites)を参照してください。

1. [GitHub](https://github.com) にサインインします。
2. 右上隅に表示されるご自身のアカウント イメージを選択し、**自分のリポジトリ**を選択します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines GitHub リポジトリの作成](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. 緑色の **[New]** ボタンを選択します。
1. **[Repository name]** にリポジトリ名を入力します。  たとえば、**AzureRmPipeline-repo** とします。 忘れずにそれぞれの **AzureRmPipeline** をプロジェクト名で置き換えてください。 このチュートリアルを **[Public]** または **[Private]** のどちらにするかを選択できます。 続いて、 **[Create repository]** を選択します。
1. URL を書き留めます。 リポジトリの URL は、次の形式です。

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

このリポジトリは、*リモート リポジトリ*と呼ばれます。 同じプロジェクトの開発者は、自分の*ローカル リポジトリ*を複製して変更をリモート リポジトリにマージできます。

### <a name="clone-the-remote-repository"></a>リモート リポジトリを複製する

1. Git Shell または Git Bash を開きます。  「[前提条件](#prerequisites)」を参照してください。
1. 現在のフォルダーが **github** であることを確認します。
1. 次のコマンドを実行します。

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    **[YourAccountName]** は GitHub のアカウント名で、 **[YourGitHubRepositoryName]** は前の手順で作成したリポジトリ名で置換します。

    次のスクリーンショットは、例を示しています。

    ![Azure Resource Manager Azure DevOps Azure Pipelines GitHub bash の作成](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

**CreateAzureStorage** フォルダーは、テンプレートが格納されているフォルダーです。 **pwd** コマンドを実行すると、フォルダー パスが表示されます。 次の手順に従い、このパスにテンプレートを保存します。

### <a name="download-a-quickstart-template"></a>クイックスタート テンプレートをダウンロードする

テンプレートを作成する代わりに、[クイックスタート テンプレート]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)をダウンロードすることができます。 このテンプレートによって Azure Storage アカウントが作成されます。

1. Visual Studio Code を開きます。 「[前提条件](#prerequisites)」を参照してください。
2. 次の URL を使用して、テンプレートを開きます。

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. このファイルを **azuredeploy.json** として **CreateAzureStorage** フォルダーに保存します。 パイプラインでは、フォルダー名とファイル名の両方を使用します。  これらの名前を変更する場合は、パイプラインで使用する名前を更新する必要があります。

### <a name="push-the-template-to-the-remote-repository"></a>テンプレートをリモート リポジトリにプッシュする

ローカル リポジトリに azuredeploy.json が追加されます。 次に、テンプレートをリモート リポジトリにアップロードします。

1. *Git Shell* または *Git Bash* を開いていない場合は開きます。
1. ローカル リポジトリで CreateAzureStorage フォルダーにディレクトリを変更します。
1. フォルダー内に **azuredeploy.json** ファイルがあることを確認します。
1. 次のコマンドを実行します。

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    LF に関する警告が表示されることがあります。 この警告は無視できます。 **master** は master ブランチです。  通常は、更新ごとにブランチを作成します。 簡略化のため、このチュートリアルでは直接 master ブランチを使用します。
1. ブラウザーから GitHub リポジトリに移動します。  URL は、 **https://github.com/ [YourAccountName]/[YourGitHubRepository]** です。 フォルダー内に **CreateAzureStorage** フォルダーと **azuredeploy.json** があるはずです。

ここまでで、GitHub リポジトリを作成して、リポジトリにテンプレートをアップロードできました。

## <a name="create-a-devops-project"></a>DevOps プロジェクトを作成する

次の手順に進むには、DevOps 組織が必要です。  ない場合は、[前提条件](#prerequisites)を参照してください。

1. [Azure DevOps](https://dev.azure.com) にサインインします。
1. 左から DevOps 組織を選択します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines Azure DevOps プロジェクトの作成](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. **[プロジェクトの作成]** を選択します。 プロジェクトがない場合は、プロジェクトの作成ページが自動的に開かれます。
1. 次の値を入力します。

    * **[プロジェクト名]** : プロジェクトの名前を入力します。 チュートリアルの最初に選択したプロジェクト名を使用することができます。
    * **[バージョン コントロール]** : **[Git]** を選択します。 **[バージョン コントロール]** を表示するには、 **[詳細設定]** の展開が必要になることがあります。

    他のプロパティには既定値をそのまま使用します。
1. **[プロジェクトの作成]** を選択します。

プロジェクトを Azure にデプロイするために使用するサービス接続を作成します。

1. 左側のメニューの下部にある **[Project settings] (プロジェクトの設定)** を選択します。
1. **[パイプライン]** の下にある **[サービス接続]** を選択します。
1. **[New Service connection] (新しいサービス接続)** を選択し、 **[AzureResourceManager]** を選択します。
1. 次の値を入力します。

    * **[接続名]** : 接続名を入力します。 たとえば、**AzureRmPipeline-conn** とします。 パイプラインを作成するときに必要になるので、この名前を書き留めておきます。
    * **[スコープのレベル]** : **[サブスクリプション]** を選択します。
    * **[サブスクリプション]** : ご使用のサブスクリプションを選択します。
    * **リソース グループ**:空白のままにします。
    * **[この接続の使用をすべてのパイプラインに許可します]** : (オン)
1. **[OK]** を選択します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。

ここまでで、以下のタスクが完了しました。  GitHub と DevOps を使い慣れており、前のセクションをスキップした場合は、続行する前に以下のタスクを完了する必要があります。

- GitHub リポジトリを作成し、リポジトリの **CreateAzureStorage** フォルダーに[このテンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)を保存する。
- DevOps プロジェクトを作成し、Azure Resource Manager サービス接続を作成する。

テンプレートをデプロイする手順を含むパイプラインを作成するには、以下の手順を実行します。

1. 左側のメニューから **[パイプライン]** を選択します。
1. **[新しいパイプライン]** を選択します。
1. **[接続]** タブで **[GitHub]** を選択します。 尋ねられた場合は GitHub の資格情報を入力し、指示に従います。 次の画面が表示されたら、 **[Only select repositories] (リポジトリのみ選択)** を選択し、一覧にリポジトリがあることを確認してから **[Approve & Install] (承認してインストール)** を選択します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines リポジトリのみ選択](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. **[選択]** タブで、リポジトリを選択します。  既定の名前は、 **[YourAccountName]/[YourGitHubRepositoryName]** です。
1. **[構成]** タブで **[Starter pipeline] (スターター パイプライン)** を選択します。 2 つのスクリプト手順が記述された **azure-pipelines.yml** パイプライン ファイルが表示されます。
1. **steps** セクションを次の YAML に置き換えます。

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    次のようになります。

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    次の変更を行います。

    * **azureSubscription**: 前の手順で作成したサービス接続で値を更新します。
    * **action**: **Create Or Update Resource Group** アクションは、2 つのアクションを行います。1. 新しいリソース グループ名が提供されている場合は、リソース グループを作成します。2. 指定されたテンプレートをデプロイします。
    * **resourceGroupName**: 新しいリソース グループの名前を指定します。 たとえば、**AzureRmPipeline-rg** です。
    * **location**: リソース グループの場所を指定します。
    * **templateLocation**: **[Linked artifact] (リンクされた成果物)** が指定されている場合、タスクは接続されているリポジトリから直接テンプレート ファイルを探します。
    * **csmFile** は、テンプレート ファイルのパスです。 テンプレートで定義されているすべてのパラメーターに既定値があるので、テンプレート パラメーター ファイルを指定する必要はありません。

    このタスクの詳細については、[Azure リソース グループの配置タスクについての記事](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)を参照してください。
1. **[保存および実行]** を選択します。
1. もう一度 **[保存および実行]** を選択します。 YAML ファイルのコピーが接続されているリポジトリに保存されます。 リポジトリを参照すると、YAML ファイルを確認できます。
1. パイプラインが正常に実行されることを確認します。

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>デプロイを検証する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. リソース グループを開きます。 この名前は、パイプラインの YAML ファイルで指定したものです。  作成した 1 つのストレージ アカウントが表示されます。  ストレージ アカウント名は、**store** で始まります。
1. ストレージ アカウント名を選択して開きます。
1. **[プロパティ]** を選択します。 **[SKU]** が **Standard_LRS** になっている点に注意してください。

    ![Azure Resource Manager Azure DevOps Azure Pipelines ポータル確認](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>更新して再デプロイする

テンプレートを更新してリモート リポジトリに変更をプッシュすると、パイプラインは自動的にリソースを更新します。今回の場合は、ストレージ アカウントが更新されます。

1. Visual Studio Code で、ローカル リポジトリから **azuredeploy.json** を開きます。
1. **storageAccountType** の **defaultValue** を **Standard_GRS** に更新します。 次のスクリーンショットをご覧ください。

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml 更新](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. 変更を保存します。
1. Git Bash/Shell から次のコマンドを実行して、リモート リポジトリに変更をプッシュします。

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    最初のコマンドは、ローカル リポジトリをリモート リポジトリと同期します。 リモート リポジトリにパイプラインの YAML ファイルが追加されたことに注意してください。

    リモート リポジトリの master ブランチが更新されたので、パイプラインが再度実行されます。

変更内容を確認するには、ストレージ アカウントの SKU を確認します。  「[デプロイを検証する](#verify-the-deployment)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

GitHub リポジトリと Azure DevOps プロジェクトを削除しても構いません。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure DevOps パイプラインを作成して Azure Resource Manager テンプレートをデプロイしました。 複数のリージョンにわたって Azure リソースをデプロイする方法のほか、安全なデプロイの実践については、以下を参照してください

> [!div class="nextstepaction"]
> [Azure Deployment Manager の使用](./resource-manager-tutorial-deploy-vm-extensions.md)
