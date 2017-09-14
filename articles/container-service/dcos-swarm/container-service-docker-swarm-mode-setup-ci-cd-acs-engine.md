---
title: "Azure Container Service エンジンと Swarm Mode による CI/CD | Microsoft Docs "
description: "Azure Container Service エンジン、Docker Swarm Mode、Azure Container Registry、および Visual Studio Team Services を使用して、複数コンテナーの .NET Core アプリケーションを継続的に配信します"
services: container-service
documentationcenter: " "
author: diegomrtnzg
manager: esterdnb
tags: acs, azure-container-service, acs-engine
keywords: "Docker, Containers, マイクロ サービス, Swarm, Azure, Visual Studio Team Services, DevOps, ACS エンジン"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e68f06925fd7c72d72610ca68fab58a6fa2f4561
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---

# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Visual Studio Team Services で ACS エンジンと Docker Swarm Mode を使用して Azure Container Service に複数コンテナー アプリケーションをデプロイする完全な CI/CD パイプライン

*この記事は、[Visual Studio Team Services で Docker Swarm を使用して Azure Container Service に複数コンテナー アプリケーションをデプロイする完全な CI/CD パイプライン](container-service-docker-swarm-setup-ci-cd.md)に関するドキュメントに基づいています。*

今日、クラウド向けの最新のアプリケーションを開発する際の最も大きな課題の 1 つは、これらのアプリケーションを継続的に配信できるようにすることです。 この記事では、完全な継続的インテグレーションおよびデプロイ (CI/CD) パイプラインを、以下を使用して実装する方法を説明します。 
* Azure Container Service エンジンと Docker Swarm Mode
* Azure Container Registry
* Visual Studio Team Services

この記事は、[GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux) から入手できる、ASP.NET Core で開発された単純なアプリケーションに基づいています。 このアプリケーションは、4 つの異なるサービス、3 つの Web API、および 1 つの Web フロント エンドで構成されています。

![MyShop サンプル アプリケーション](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

目的は、Visual Studio Team Services を使用して、Docker Swarm Mode クラスターにこのアプリケーションを継続的に配信することです。 次の図は、この継続的な配信パイプラインの詳細を示しています。

![MyShop サンプル アプリケーション](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

手順の簡単な説明を次に示します。

1. コードの変更は、ソース コード リポジトリ (ここでは、GitHub) にコミットされます。 
2. GitHub により、Visual Studio Team Services でビルドがトリガーされます。 
3. Visual Studio Team Services がソースの最新バージョンを取得し、アプリケーションを構成するすべてのイメージをビルドします。 
4. Visual Studio Team Services が Azure Container Registry サービスを使用して作成された Docker レジストリに各イメージをプッシュします。 
5. Visual Studio Team Services が新しいリリースをトリガーします。 
6. リリースにより、Azure Container Service クラスターのマスター ノードで SSH を使用して何らかのコマンドが実行されます。 
7. クラスター上の Docker Swarm Mode がイメージの最新バージョンを取得します。 
8. Docker Stack を使用して、アプリケーションの新しいバージョンがデプロイされます。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次のタスクを実行する必要があります。

- [ACS エンジンを使用して Azure Container Service 内に Swarm クラスターを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Azure Container Service 内の Swarm クラスターと接続する](../container-service-connect.md)
- [Azure コンテナー レジストリを作成する](../../container-registry/container-registry-get-started-portal.md)
- [Visual Studio Team Services アカウントとチーム プロジェクトを作成する](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [GitHub アカウントに GitHub リポジトリをフォークする](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Azure Container Service の Docker Swarm オーケストレーターは、従来のスタンドアロンの Swarm を使用します。 現時点では、(Docker 1.12 以降の) 統合された [Swarm モード](https://docs.docker.com/engine/swarm/)は、Azure Container Service でサポートされているオーケストレーターではありません。 このため、コミュニティに投稿されている[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)である [ACS エンジン](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md)または [Azure Marketplace](https://azuremarketplace.microsoft.com) の Docker ソリューションを使用します。
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>手順 1: Visual Studio Team Services アカウントの構成 

このセクションでは、Visual Studio Team Services アカウントを構成します。 VSTS サービス エンドポイントを構成するには、Visual Studio Team Services プロジェクトで、ツールバーの **[設定]** アイコンをクリックし、**[サービス]** を選択します。

![サービス エンドポイントを開く](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Visual Studio Team Services と Azure アカウントの接続

VSTS プロジェクトと Azure アカウント間の接続を設定します。

1. 左側で、**[新しいサービス エンドポイント]** > **[Azure Resource Manager]** をクリックします。
2. VSTS が Azure アカウントで動作することを承認するには、**[サブスクリプション]** を選択し、**[OK]** をクリックします。

    ![Visual Studio Team Services - Azure を承認する](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Visual Studio Team Services と GitHub の接続

VSTS プロジェクトと GitHub アカウント間の接続を設定します。

1. 左側で、**[新しいサービス エンドポイント]** > **[GitHub]** の順にクリックします。
2. VSTS が GitHub アカウントを使用することを承認するには、**[認証]** をクリックし、開いたウィンドウの手順に従います。

    ![Visual Studio Team Services - GitHub の承認](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>VSTS の Azure Container Service クラスターへの接続

CI/CD パイプラインに進む前の最後の手順は、Azure 内の Docker Swarm クラスターへの外部接続を構成することです。 

1. Docker Swarm クラスターの場合は、**SSH** タイプのエンドポイントを追加します。 その後、Swarm クラスター (マスター ノード) の SSH 接続情報を入力します。

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

これで、すべての構成は完了です。 次の手順では、アプリケーションをビルドし、Docker Swarm クラスターにデプロイする CI/CD パイプラインを作成します。 

## <a name="step-2-create-the-build-definition"></a>手順 2: ビルド定義の作成

この手順では、VSTS プロジェクトのビルド定義を設定し、コンテナー イメージのビルド ワークフローを定義します。

### <a name="initial-definition-setup"></a>定義の初期設定

1. ビルド定義を作成するには、Visual Studio Team Services プロジェクトに接続し、**[Build & Release (ビルドとリリース)]** をクリックします。 **[ビルド定義]** セクションで、**[+ 新規]** をクリックします。 

    ![Visual Studio Team Services - 新しいビルド定義](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. **[Empty process]\(空のプロセス\)** を選択します。

    ![Visual Studio Team Services - 新しい空のビルド定義](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. 次に、**[変数]** タブをクリックし、2 つの新しい変数 (**RegistryURL**  と **AgentURL**) を作成します。 レジストリとクラスター エージェントの DNS の値を貼り付けます。

    ![Visual Studio Team Services - ビルド変数の構成](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. **[ビルド定義]** ページで、**[トリガー]** タブを開き、前提条件で作成した MyShop プロジェクトのフォークを使用するように、継続的インテグレーションで使用するビルドを構成します。 次に、**[バッチ変更]** を選択します。 **[ブランチ仕様]** として "*docker-linux*" が選択されていることを確認します。

    ![Visual Studio Team Services - ビルドのリポジトリの構成](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. 最後に、**[オプション]** タブをクリックし、既定のエージェント キューを **[Hosted Linux Preview]\(ホストされている Linux (プレビュー)\)** に構成します。

    ![Visual Studio Team Services - ホスト エージェントの構成](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>ビルド ワークフローの定義
次の手順では、ビルド ワークフローを定義します。 最初に、コードのソースを構成する必要があります。 これを行うには、**[GitHub]**、およびお使いの**リポジトリ**と**ブランチ** (docker-linux) を選択します。

![Visual Studio Team Services - コード ソースの構成](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

*MyShop* アプリケーションの場合、ビルドするコンテナー イメージが 5 つあります。 各イメージは、プロジェクト フォルダーにある Dockerfile を使用してビルドされます。

* ProductsApi
* プロキシ
* RatingsApi
* RecommandationsApi
* ShopFront

イメージごとに 2 つの Docker ステップ (イメージをビルドするステップと Azure コンテナー レジストリにイメージをプッシュするステップ) が必要です。 

1. ビルド ワークフローにステップを追加するには、**[+ ビルド ステップの追加]** をクリックし、**[Docker]** を選択します。

    ![Visual Studio Team Services - ビルド ステップの追加](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. 各イメージについて、`docker build` コマンドを使用する 1 つ目のステップを構成します。

    ![Visual Studio Team Services - Docker のビルド](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    ビルド操作については、Azure Container Registry、**[Build an image]\(イメージのビルド\)** アクション、および各イメージを定義する Dockerfile を選択します。 **[作業ディレクトリ]** を Dockerfile のルート ディレクトリとして定義し、**[イメージ名]** を定義し、**[最新のタグを含める]** を選択します。
    
    イメージ名は、```$(RegistryURL)/[NAME]:$(Build.BuildId)``` の形式にする必要があります。 **[NAME]** をイメージ名に置き換えます。
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. 各イメージについて、`docker push` コマンドを使用する 2 つ目のステップを構成します。

    ![Visual Studio Team Services - Docker のプッシュ](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    プッシュ操作については、Azure コンテナー レジストリ、**[Push an image]\(イメージのプッシュ\)** アクションを選択し、前のステップでビルドした**イメージ名**を入力し、**[最新のタグを含める]** を選択します。

4. 5 つの各イメージのビルド ステップとプッシュ ステップを構成したら、ビルド ワークフローにステップをもう 3 つ追加します。

   ![Visual Studio Team Services - コマンド ライン タスクの追加](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. bash スクリプトを使用して、docker-compose.yml ファイル内に出現する *RegistryURL* を RegistryURL 変数に置き換えるコマンド ライン タスク。 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - Registry URL を使用した compose ファイルの更新](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. bash スクリプトを使用して、docker-compose.yml ファイル内に出現する *AgentURL* を AgentURL 変数に置き換えるコマンド ライン タスク。
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. 更新された compose ファイルをビルド成果物としてドロップし、リリースで使用できるようにするタスク。 詳細については、次の画面を参照してください。

         ![Visual Studio Team Services - 成果物の発行](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services - compose ファイルの発行](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. **[保存してキューに登録]** をクリックして、ビルド定義をテストします。

   ![Visual Studio Team Services - 保存してキューに登録](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services - 新しいキュー](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. **ビルド**が適切であれば、次の画面が表示されます。

  ![Visual Studio Team Services - ビルド成功](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>手順 3: リリース定義の作成

Visual Studio Team Services を使用すると、[複数の環境のリリースを管理](https://www.visualstudio.com/team-services/release-management/)できます。 継続的なデプロイを有効にして、さまざまな環境 (開発、テスト、運用前、運用など) にアプリケーションをスムーズにデプロイすることができます。 Azure Container Service の Docker Swarm Mode クラスターを表す環境を作成できます。

![Visual Studio Team Services - ACS へのリリース](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>リリースの初期設定

1. リリース定義を作成するために、**[リリース]** > **[+ リリース]** の順にクリックします。

2. 成果物ソースを構成するために、**[成果物]** > **[成果物ソースのリンク]** の順にクリックします。 ここでは、この新しいリリース定義を、前の手順で定義したビルドにリンクします。 その後、docker-compose.yml ファイルをリリース プロセスで使用できるようになります。

    ![Visual Studio Team Services - リリースの成果物](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. リリース トリガーを構成するために、**[トリガー]** をクリックし、**[継続的配置]** を選択します。 同じ成果物ソースにトリガーを設定します。 この設定により、ビルドが正常に完了したときに新しいリリースが開始されることが保証されます。

    ![Visual Studio Team Services - リリースのトリガー](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. リリース変数を設定するには、**[変数]** をクリックします。**[+変数]** を選択し、3 つの新しい変数 (**docker.username****docker.password**、および **docker.registry**) をレジストリの情報を使用して作成します。 レジストリとクラスター エージェントの DNS の値を貼り付けます。

    ![Visual Studio Team Services - ビルドのリポジトリの構成](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > 前の画面に示すように、docker.password の **[ロック]** チェック ボックスをクリックします。 この設定は、パスワードを制限するために重要です。
    >

### <a name="define-the-release-workflow"></a>リリース ワークフローの定義

リリース ワークフローは、追加する 2 つのタスクで構成されます。

1. 前に構成した SSH 接続を使用して、Docker Swarm マスター ノード上の *deploy* フォルダーに compose ファイルを安全にコピーするタスクを構成します。 詳細については、次の画面を参照してください。
    
    ソース フォルダー: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - リリースの SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. bash コマンドを実行して、マスター ノードで `docker` コマンドと `docker stack deploy` コマンドを実行する 2 つ目のタスクを構成します。 詳細については、次の画面を参照してください。

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services - リリースの bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    マスターで実行されるコマンドでは、Docker CLI と Docker-Compose CLI を使用して、次のタスクを実行します。

    - Azure コンテナー レジストリにログインします (**[変数]** タブに定義されている 3 つのビルド変数を使用します)。
    - Swarm エンドポイント (:2375) を使用するよう **DOCKER_HOST** 変数を定義します。
    - 前の安全なコピー タスクによって作成された、docker-compose.yml ファイルを含む *deploy* フォルダーに移動します。 
    - 新しいイメージの取得とコンテナーの作成を行う `docker stack deploy` コマンドを実行します。

    >[!IMPORTANT]
    > 前の画面に示すように、**[STDERR でのエラー]** チェック ボックスはオフのままにします。 この設定により、`docker-compose` が (コンテナーの停止や削除などの) 診断メッセージを標準エラー出力に出力することで、リリース プロセスを完了できます。 このチェック ボックスをオンにすると、すべてうまくいった場合でも、Visual Studio Team Services からリリース中にエラーが発生したと報告されます。
    >
3. この新しいリリース定義を保存します。

## <a name="step-4-test-the-cicd-pipeline"></a>手順 4: CI/CD パイプラインのテスト

構成が完了したので、この新しい CI/CD パイプラインをテストしてみましょう。 テストする最も簡単な方法は、ソース コードを更新し、変更を GitHub リポジトリにコミットすることです。 コードをプッシュしてから数秒経つと、Visual Studio Team Services に実行中の新しいビルドが表示されます。 正常に完了すると、新しいリリースがトリガーされ、アプリケーションの新しいバージョンが Azure Container Service クラスターにデプロイされます。

## <a name="next-steps"></a>次のステップ

* Visual Studio Team Services を使用した CI/CD の詳細については、[VSTS ビルドの概要](https://www.visualstudio.com/docs/build/overview)に関するページを参照してください。
* ACS エンジンの詳細については、[ACS エンジンの GitHub リポジトリ](https://github.com/Azure/acs-engine)に関するページを参照してください。
* Docker Swarm Mode の詳細については、[Docker Swarm Mode の概要](https://docs.docker.com/engine/swarm/)に関するページを参照してください。

