---
title: Azure IoT Edge の継続的インテグレーションと継続的配置 | Microsoft Docs
description: Azure IoT Edge の継続的インテグレーションと継続的配置の概要
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16dac996f871241b8c9b5e4c1b797d07d79aeb79
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632564"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge に対する継続的インテグレーションと継続的配置

Azure Pipelines 内の組み込み Azure IoT Edge タスクまたは Jenkins サーバー上の [Jenkins 用の Azure IoT Edge プラグイン](https://plugins.jenkins.io/azure-iot-edge)を使用して、Azure IoT Edge アプリケーションで DevOps を簡単に導入できます。 この記事では、Azure Pipelines と Azure DevOps Server の継続的インテグレーションと継続的配置の機能を使用して、アプリケーションを迅速かつ効率的に Azure IoT Edge にビルド、テスト、配置する方法について説明します。 

この記事では、次のことについて説明します。
* サンプルの IoT Edge ソリューションを作成してチェックインします。
* 継続的インテグレーション (CI) を構成して、ソリューションをビルドします。
* 継続的配置 (CD) を構成して、ソリューションを配置し、応答を表示します。

この記事の手順を完了するには、20 分かかります。

![CI および CD](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Visual Studio Code を使用して Azure IoT Edge ソリューションのサンプルを作成する

このセクションでは、ビルド プロセスの一部として実行できる単体テストを含む IoT Edge ソリューションのサンプルを作成します。 このセクションのガイダンスに従う前に、「[Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する](tutorial-multiple-modules-in-vscode.md)」の手順を完了してください。

1. VS Code コマンド パレットで、コマンド **Azure IoT Edge: New IoT Edge solution** を入力して実行します。 次に、ご自身のワークスペース フォルダーを選択し、ソリューション名 (既定の名前は **EdgeSolution**) を指定して、C# モジュール (**FilterModule**) をこのソリューションの最初のユーザー モジュールとして作成します。 また、この最初のモジュールの Docker イメージ リポジトリを指定する必要もあります。 既定のイメージ リポジトリは、ローカル Docker レジストリに基づきます (`localhost:5000/filtermodule`)。 継続的インテグレーションを強化するには、これを Azure Container Registry (`<your container registry address>/filtermodule`) または Docker Hub に変更します。

    ![ACR の設定](./media/how-to-ci-cd/acr.png)

2. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 必要に応じて、**Azure IoT Edge: Add IoT Edge module** を入力して実行し、さらにモジュールを追加できます。 ルート フォルダーには、`modules` フォルダー、`.vscode` フォルダー、および配置マニフェスト テンプレート ファイルがあります。 ユーザー モジュール コードはすべて、`modules` フォルダーのサブフォルダーになります。 `deployment.template.json` は、配置マニフェスト テンプレートです。 このファイルのパラメーターの中には、すべてのモジュール フォルダーにある `module.json` から解析されるものがあります。

3. これで、IoT Edge ソリューションのサンプルが準備できました。 既定の C# モジュールは、パイプ メッセージ モジュールとして機能します。 `deployment.template.json` では、このソリューションに 2 つのモジュールが含まれていることがわかります。 メッセージは `tempSensor` モジュールから生成され、`FilterModule` を介して直接パイプ処理された後、使用している IoT ハブに送信されます。

4. これらのプロジェクトを保存し、Azure Repos または Azure DevOps Server リポジトリにチェックインします。
    
> [!NOTE]
> Azure Repos の使用に関する詳細については、「[Share your code with Visual Studio and Azure Repos (自分のコードを Visual Studio と Azure Repos に共有する)](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)」を参照してください。


## <a name="configure-azure-pipelines-for-continuous-integration"></a>継続的インテグレーションのための Azure Pipelines の構成
このセクションでは、IoT Edge ソリューションのサンプルへの変更をチェックインするときに自動的に実行するように構成されたビルド パイプラインを作成します。その後、Azure Pipelines にビルド ログが表示されます。

1. ご自身の Azure DevOps 組織 (**https://dev.azure.com/{your organization}/**) にサインインして、サンプル アプリをチェックインしたプロジェクトを開きます。

    ![チェックイン コード](./media/how-to-ci-cd/init-project.png)

1. Azure Pipelines 内で、**[ビルド]** タブを開き、**[+ 新しいパイプライン]** を選択します。 または、すでにビルド パイプラインがある場合は、**[+ 新規]** ボタンを選択します。 **[新しいビルド パイプライン]** を選択します。

    ![新しいパイプライン](./media/how-to-ci-cd/add-new-build.png)

1. プロンプトが表示されたら、ソースの種類として **Azure DevOps Git** を選択します。 次に、コードがあるプロジェクト、リポジトリ、ブランチを選択します。 **[続行]** を選択します。

    ![git の選択](./media/how-to-ci-cd/select-vsts-git.png)

    **[テンプレートの選択]** ウィンドウで、**[start with an Empty process]\(空のプロセスを開始する\)** を選択します。

    ![テンプレートの選択](./media/how-to-ci-cd/start-with-empty.png)

1. パイプライン エディターで、エージェント プールを選択します。 
    
    * Linux コンテナー用にプラットフォーム amd64 でモジュールをビルドする場合、**[Hosted Ubuntu 1604]** を選択します。
    * Windows コンテナー用にプラットフォーム amd64 でモジュールをビルドする場合、**[Hosted VS2017]** を選択します。 
    * Linux コンテナー用にプラットフォーム arm32v7 でモジュールをビルドする場合は、**[管理]** ボタンをクリックして自分のビルド エージェントを設定する必要があります。
    
    ![ビルド エージェントの構成](./media/how-to-ci-cd/configure-env.png)

1. エージェント ジョブで [+] をクリックして、ビルド パイプラインに 3 つのタスクを追加します。 最初の 2 つは、**[Azure IoT Edge]** からのタスクです。 3 番目は、**[ビルド成果物の発行]** からのタスクです
    
    ![タスクの追加](./media/how-to-ci-cd/add-tasks.png)

1. 最初の **[Azure IoT Edge]** タスクで **[表示名]** を "**Azure IoT Edge - Build module images**" に更新し、**[アクション]** ドロップダウン リストで "**Build module images**" を選択します。 **[.template.json file]\(.template.json ファイル\)** コントロールで、IoT Edge ソリューションについて説明している **deployment.template.json** ファイルを選択します。 次に、**[既定のプラットフォーム]** を選択し、IoT Edge デバイスと同じプラットフォームを選択します。 このタスクでは、指定したターゲット プラットフォームを使用してソリューション内のすべてのモジュールをビルドします。 **deployment.json** ファイルも生成され、出力変数内にファイル パスが見つかります。 この変数の別名を `edge` に設定します。
    
    ![ビルドとプッシュ](./media/how-to-ci-cd/build-and-push.png)

1. 2 番目の **[Azure IoT Edge]** タスクで **[表示名]** を "**Azure IoT Edge - Push module images**" に更新し、**[アクション]** ドロップダウン リストで "**Push module images**" を選択します。 [コンテナー レジストリの種類] を選択して、コード内に同じレジストリを構成して選択します (module.json)。 **[.template.json file]\(.template.json ファイル\)** コントロールで、IoT Edge ソリューションについて説明している **deployment.template.json** ファイルを選択します。 次に、**[既定のプラットフォーム]** を選択し、ビルドされたモジュール イメージと同じプラットフォームを選択します。 このタスクでは、すべてのモジュール イメージが選択したコンテナー レジストリにプッシュされます。 **deployment.json** ファイル内にコンテナー レジストリ資格情報も追加され、出力変数内にファイル パスが見つかります。 この変数の別名を `edge` に設定します。 モジュール イメージをホストするコンテナー レジストリが複数ある場合は、このタスクを複製し、別のコンテナー レジストリを選択し、詳細設定の **[Bypass module(s)]\(モジュールをバイパス\)** を使用して、この特定のレジストリ用ではないイメージをバイパスする必要があります。

    ![プッシュ](./media/how-to-ci-cd/push.png)

1. **[ビルド成果物の発行]** タスクでは、ビルド タスクによって生成された配置ファイルを指定します。 **[Path to publish]\(発行するパス\)** を `$(edge.DEPLOYMENT_FILE_PATH)` に設定します。

    ![成果物の発行](./media/how-to-ci-cd/publish-build-artifacts.png)

1. **[トリガ]** タブを開き、**[継続的インテグレーション]** トリガーを有効にします。 ご自身のコードを含むブランチを含まれていることを確認します。

    ![トリガーの構成](./media/how-to-ci-cd/configure-trigger.png)

    新しいビルド パイプラインを保存します。 **[保存]** ボタンをクリックします。


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>継続的配置のための Azure Pipelines の構成
このセクションでは、ビルド パイプラインで成果物を削除するときに自動的に実行するように構成されたリリース パイプラインを作成します。その後、Azure Pipelines に配置ログが表示されます。

1. **[リリース]** タブで、**[+ 新しいパイプライン]** を選択します。 または、既にリリース パイプラインがある場合は、**[+ 新規]** ボタンを選択します。  

    ![リリース パイプラインの追加](./media/how-to-ci-cd/add-release-pipeline.png)

    **[テンプレートの選択]** ウィンドウで、**[start with an Empty job]\(空のジョブを開始する\)** を選択します。

    ![空のジョブの開始](./media/how-to-ci-cd/start-with-empty-job.png)

2. 次に、**[ステージ 1]** という 1 つのステージを持つリリース パイプラインが初期化されます。 **[ステージ 1]** の名前を **[QA]** に変更し、それをテスト環境として扱います。 一般的な継続的配置パイプラインでは、複数のステージがあるため、ご使用の DevOps 手法に基づいてさらにステージを作成できます。

    ![ステージの作成](./media/how-to-ci-cd/QA-env.png)

3. リリースをビルド成果物にリンクします。 成果物領域で **[追加]** をクリックします。

    ![成果物の追加](./media/how-to-ci-cd/add-artifacts.png)  
    
    **[Add an artifact page]\(成果物ページの追加\)** で、ソースの種類として **[ビルド]** を選択します。 次に、作成したプロジェクトとビルド パイプラインを選択します。 **[追加]** をクリックします。

    ![アーティファクトの追加](./media/how-to-ci-cd/add-an-artifact.png)

    継続的配置トリガーを開き、新しいビルドが利用可能になるたびに新しいリリースが作成されるようにします。

    ![トリガーの構成](./media/how-to-ci-cd/add-a-trigger.png)

4. **[QA stage]\(QA ステージ\)** に移動し、このステージのタスクを構成します。

    ![QA タスクの構成](./media/how-to-ci-cd/view-stage-tasks.png)

   配置タスクはプラットフォームに左右されません。つまり、**[エージェント プール]** で、**[Hosted VS2017]** と **[Hosted Ubuntu 1604]** のいずれか (または自分で管理している他のエージェント) を選択できます。 [+] をクリックして、タスクを 1 つ追加します。

    ![QA 用タスクの追加](./media/how-to-ci-cd/add-task-qa.png)

5. [Azure IoT Edge] タスクで、**[アクション]** ドロップダウン リストに移動し、**[Deploy to IoT Edge device]\(IoT Edge デバイスに配置\)** を選択します。 **[Azure サブスクリプション]** を選択し、使用している **IoT ハブ名**を入力します。 1 つまたは複数のデバイスへの配置を選択できます。 **複数のデバイス**に配置する場合は、デバイスの**ターゲット条件**を指定する必要があります。 ターゲット条件とは、IoT ハブの一連の Edge デバイスと突き合わせるフィルターです。 デバイス タグを条件として使用する場合は、対応するデバイス タグを IoT Hub デバイス ツインに合わせて更新する必要があります。 詳細設定で、**[IoT Edge deployment ID]\(IoT Edge 配置 ID\)** を "deploy-qa" に更新します。 'qa' というタグが付いた IoT Edge デバイスがいくつかあると想定します。その場合、タスク構成は次のスクリーンショットのようになります。 

    ![QA への配置](./media/how-to-ci-cd/deploy-to-qa.png)

    新しいリリース パイプラインを保存します。 **[保存]** ボタンをクリックします。 次に、**[パイプライン]** をクリックしてパイプラインに戻ります。

6. 2 番目のステージは、運用環境用です。 新しいステージ "PROD" を追加するには、ステージ "QA" を複製します。その後、複製したステージを **PROD** という名前に変更します。

    ![ステージの複製](./media/how-to-ci-cd/clone-stage.png)

7. 運用環境のタスクを構成します。 タスク構成に 'prod' というタグが付いた IoT Edge デバイスがいくつかあると想定します。ターゲット条件を "prod" に更新し、詳細設定で配置 ID を "deploy-prod" に設定します。 **[保存]** ボタンをクリックします。 次に、**[パイプライン]** をクリックしてパイプラインに戻ります。
    
    ![運用環境への配置](./media/how-to-ci-cd/deploy-to-prod.png)

7. 現時点では、ビルド成果物は **QA** ステージ、次いで **PROD** ステージで継続的にトリガーされます。 ただしほとんどの場合、QA デバイスでいくつかのテスト ケースを統合し、ビットを手動で承認する必要があります。 ビットは後で PROD 環境に配置されます。 PROD ステージでの承認を以下のスクリーンショットのように設定します。

    1. **[配置前条件]** 設定パネルを開きます。

        ![配置前条件を開く](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. **[配置前承認]** を **[有効]** に設定します。 **[承認者]** も入力します。 その後、 **[保存]** をクリックします。
    
        ![条件の設定](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. この段階でリリース パイプラインが次のスクリーンショットのように設定されています。

    ![リリース パイプライン](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>ビルド パイプラインとリリース パイプラインの IoT Edge CI/CD を検証する

このセクションでは、ビルドをトリガーして、CI/CD パイプラインを動作させます。 次に配置が成功したことを検証します。

1. ビルド ジョブをトリガーするには、コミットをソース コード リポジトリにプッシュするか、手動でトリガーできます。 次のスクリーンショットに示されているように、**[キュー]** ボタンをクリックすると、ビルド パイプラインのビルド ジョブをトリガーできます。

    ![手動トリガー](./media/how-to-ci-cd/manual-trigger.png)

2. ビルド パイプラインが正常に完了すると、リリースが **QA** ステージにトリガーされます。 ビルド パイプライン ログに移動すると、次のスクリーンショットのように表示されます。

    ![ビルド ログ](./media/how-to-ci-cd/build-logs.png)

3. **QA** ステージに正常に配置されると、承認者に通知がトリガーされます。 リリース パイプラインに移動すると、次のスクリーンショットのように表示されます。 

    ![承認保留中](./media/how-to-ci-cd/pending-approval.png)


4. 承認者がこの変更を承認すると、**PROD** に配置できます。

    ![Prod への配置](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>次の手順

* 「[1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて](module-deployment-monitoring.md)」で IoT Edge デプロイについて理解します。
* 「[Deploy and monitor IoT Edge modules at scale (IoT Edge モジュールを大規模にデプロイして監視する)](how-to-deploy-monitor.md)」で、デプロイを作成、更新、または削除するための手順を学習してください。
