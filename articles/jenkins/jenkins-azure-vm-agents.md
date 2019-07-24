---
title: Azure VM エージェントを使用した Jenkins デプロイのスケーリング。
description: Azure 仮想マシンと Jenkins Azure VM エージェント プラグインを使用して、Jenkins パイプラインの容量をさらに追加します。
ms.service: jenkins
keywords: Jenkins, Azure, 開発,仮想マシン, エージェント
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 5cfece551f99a0925099b6ef936703e72f078985
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074667"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>要求を満たすために、Azure VM エージェントを使用して Jenkins デプロイをスケーリングします。

このチュートリアルでは、Jenkins の [Azure VM エージェント プラグイン](https://plugins.jenkins.io/azure-vm-agents)を使用して、Azure 上で実行している Linux 仮想マシンにオンデマンドで容量を追加します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure VM エージェント プラグインをインストールする
> * プラグインを構成して Azure サブスクリプションにリソースを作成する
> * 使用可能な計算リソースを各エージェントに設定する
> * オペレーティング システムおよび各エージェントにインストールされているツールを設定する
> * Jenkins の新しいフリースタイル ジョブを作成する
> * Azure VM エージェントでジョブを実行する

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション
* Jenkins マスター サーバー お持ちでない場合は、[[クイック スタート]](install-jenkins-solution-template.md) を表示して Azure 上で設定します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Azure VM エージェント プラグインをインストールする

> [!TIP]
> [ソリューション テンプレート](install-jenkins-solution-template.md)を使用して Jenkins を Azure にデプロイした場合、Azure VM エージェント プラグインは既にインストールされています。

1. Jenkins ダッシュボードで、**[Manage Jenkins]\(Jenkins の管理\)**、**[Manage Plugins]\(プラグインの管理\)** の順に選択します。
1. **[Available]\(利用可能\)** タブを選択し、**[Azure VM Agents]\(Azure VM エージェント\)** を探します。 エントリの横にあるチェックボックスでプラグインを選択し、ダッシュボードの下部にある **[Install without restart]\(再起動せずにインストール\)** を選択します。

## <a name="configure-the-azure-vm-agents-plugin"></a>Azure VM エージェント プラグインを構成する

1. Jenkins ダッシュボードから、**[Manage Jenkins]\(Jenkins の管理\)**、**[Configure System]\(システムの構成\)** の順に選択します。
1. ページの下部までスクロールし、**[クラウド]** セクションで **[Add new cloud]\(新しいクラウドの追加\)** ボックスを見つけて、**[Microsoft Azure VM Agents]\(Microsoft Azure VM エージェント\)** を選びます。
1. **[Azure Credentials]\(Azure 資格情報\)** セクションの **[Add]\(追加\)** ボックスから、既存のサービス プリンシパルを選択します。 一覧に何も表示されない場合は、次の手順を実施して Azure アカウントの[サービス プリンシパルを作成](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager)し、Jenkins の構成に追加します。   

    a. **[追加]** の横の **[Azure Credentials]\(Azure 資格情報\)** を選択し、**[Jenkins]** を選びます。   
    b. **[Add Credentials]\(資格情報の追加\)** ダイアログで、**[Kind]\(種類\)** ボックスの一覧から **[Microsoft Azure Service Principal]\(Microsoft Azure サービス プリンシパル\)** を選択します。   
    c. Azure CLI または [Cloud Shell](/azure/cloud-shell/overview) から Active Directory サービス プリンシパルを作成します。
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. サービス プリンシパルから **[Add Credentials]\(資格情報の追加\)** ダイアログに資格情報を入力します。 Azure サブスクリプション ID がわからない場合は、CLI で次のようにすると照会できます。
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    完了したサービス プリンシパルには、**サブスクリプション ID** に `id` フィールドを、**クライアント ID** に `appId` の値を、**クライアント シークレット**に `password` を、**テナント ID** に `tenant` を、それぞれ使用する必要があります。 **[追加]** を選択してサービス プリンシパルを追加し、プラグインを構成して新しく作成された資格情報を使用します。

    ![Azure サービス プリンシパルを構成する](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

1. **[リソース グループ名]** セクションで、**[新規作成]** を選択した状態で `myJenkinsAgentGroup` を入力します。
1. **[Verify configuration]\(構成の検証\)** を選択して、プロファイル設定をテストするために Azure に接続します。
1. **[適用]** を選択してプラグインの構成を更新します。

## <a name="configure-agent-resources"></a>エージェントのリソースを構成する

Azure VM エージェントの定義に使用するテンプレートを構成します。 このテンプレートは、各エージェントが作成された時に保持する計算リソースを定義します。

1. **[Add Azure Virtual Machine Template]\(Azure 仮想マシン テンプレートの追加\)** の横にある **[追加]** を選択します。
1. **Name** に `defaulttemplate` を入力します。
1. **Label** に `ubuntu` を入力します。
1. コンボ ボックスから目的の [[Azure リージョン]](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を選びます。
1. **[Virtual Machine Size]\(仮想マシンのサイズ\)** の下にあるドロップダウン リストから [VM サイズ](/azure/virtual-machines/linux/sizes)を選択します。 汎用的な `Standard_DS1_v2` サイズであれば、このチュートリアルでは十分です。   
1. **[保持時間]** は `60` のままにしておきます。 この設定は、Jenkins がアイドル状態のエージェントの割り当てを解除するまで待機する分単位の時間を定義します。 アイドル状態のエージェントが自動的に削除されないようにする場合は、0 を指定します。

   ![全般的な VM の構成](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>オペレーティング システムとツールを構成する

プラグイン構成の **[Image Configuration]\(イメージ構成\)** セクションで、**[Ubuntu 16.04 LTS]** を選択します。 **[Install Git (Latest)]\(Git (最新版) をインストールする\)**、**[Install Maven (V3.5.0)]\(Maven (V3.5.0) をインストールする\)**、および **[Docker をインストールする]** の横にあるチェックをオンにして、新しく作成されたエージェントにこれらのツールをインストールします。

![VM の OS とツールを構成する](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

**[管理者資格情報]** の横にある **[追加]** を選択してから、**[Jenkins]** を選択します。 エージェントのサインインに使用されるユーザー名とパスワードを入力して、Azure VM の管理者アカウントに求められる[ユーザー名とパスワードのポリシー](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)を満たしていることを確認します。

**[Verify Template]\(テンプレートの検証\)** を選択し、**[保存]** を選択して変更内容を保存したら、Jenkins のダッシュ ボードに戻ります。

## <a name="create-a-job-in-jenkins"></a>Jenkins でジョブを作成する

1. Jenkins ダッシュボードで、 **[New Item]** をクリックします。 
1. 名前に `demoproject1` を入力し、**[Freestyle project]\(フリースタイル プロジェクト\)** 選択してから **[OK]** を選択します。
1. **[全般]** タブで、**[Restrict where project can be run]\(プロジェクトを実行できる場所を制限する\)** をオンにして、**[ラベル式]** に `ubuntu` と入力します。 前の手順で作成したクラウドの構成によってラベルが提供されていることを確認するメッセージが表示されます。 
   ![ジョブを設定する](./media/jenkins-azure-vm-agents/job-config.png)
1. **[ソース コード管理]** タブで **[Git]** を選択し、**[リポジトリの URL]** フィールドに `https://github.com/spring-projects/spring-petclinic.git` のURL を追加します。
1. **[ビルド]** タブで **[Add build step]\(ビルド ステップの追加\)** を選択し、**Maven の最上位のターゲットを呼び出し**ます。 **[Goals]\(目標\)** フィールドに `package` を入力します。
1. **[保存]** を選択してジョブ定義を保存します。

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Azure VM エージェントで新しいジョブを作成する

1. Jenkins ダッシュボードに戻ります。
1. 前の手順で作成したジョブを選択し、**[Build now]\(今すぐ作成\)** をクリックします。 新しいビルドがキューに追加されますが、Azure サブスクリプションにエージェント VM が作成されるまでは開始されません。
1. ビルドが完了したら、**[Console output\(コンソール出力\)]** に移動します。 ビルドが Azure エージェントでリモートに実行されたことがわかります。

![コンソール出力](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins プラグインのトラブルシューティング

Jenkins プラグインでバグが発生した場合は、[Jenkins JIRA](https://issues.jenkins-ci.org/) で特定のコンポーネントについて問題を報告してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure App Service への CI/CD](java-deploy-webapp-tutorial.md)