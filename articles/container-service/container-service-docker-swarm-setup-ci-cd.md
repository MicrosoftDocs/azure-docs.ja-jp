---
title: "Azure Container Service と Swarm を使用した CI/CD | Microsoft Docs"
description: "Azure Container Service、Docker Swarm、Azure Container Registry、および Visual Studio Team Services を使用して、複数コンテナー .NET Core アプリケーションを継続的に配信します"
services: container-service
documentationcenter: " "
author: jcorioland
manager: pierlag
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロ サービス, Swarm, Azure, Visual Studio Team Services, DevOps"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2016
ms.author: jucoriol
translationtype: Human Translation
ms.sourcegitcommit: c5e05a8693bd000010013922ed6f2a127e998efe
ms.openlocfilehash: db58703f573c09ba45abed87547c5e80ef58638b


---

# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Docker Swarm と Visual Studio Team Services を使用して、Azure Container Service に複数コンテナー アプリケーションをデプロイする完全な CI/CD パイプライン

クラウド向けの最新のアプリケーションを開発する際の最も大きな課題の&1; つは、これらのアプリケーションを継続的に配信できるようにすることです。 この記事では、Azure Container Service、Docker Swarm、Azure Container Registry、および Visual Studio Team Services のビルドとリリース管理を使用して、完全な CI/CD (継続的な統合とデプロイ) パイプラインを実装する方法について説明します。

この記事は、[GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) から入手できる、ASP.NET Core で開発された単純なアプリケーションに基づいています。 このアプリケーションは、4 つの異なるサービス、3 つの Web API、および&1; つの Web フロント エンドで構成されています。

![MyShop サンプル アプリケーション](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

目的は、Visual Studio Team Services を使用して、Docker Swarm クラスターでこのアプリケーションを継続的に配信することです。 次の図は、この継続的な配信パイプラインの詳細を示しています。

![MyShop サンプル アプリケーション](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

手順の簡単な説明を次に示します。

1. コードの変更は、ソース コード リポジトリ (ここでは、GitHub) にコミットされます。 
2. GitHub により、Visual Studio Team Services でビルドがトリガーされます。 
3. Visual Studio Team Services がソースの最新バージョンを取得し、アプリケーションを構成するすべてのイメージをビルドします。 
4. Visual Studio Team Services が Azure Container Registry サービスを使用して作成された Docker レジストリに各イメージをプッシュします。 
5. Visual Studio Team Services が新しいリリースをトリガーします。 
6. リリースにより、Azure Container Service クラスターのマスター ノードで SSH を使用して何らかのコマンドが実行されます。 
7. クラスター上の Docker Swarm がイメージの最新バージョンを取得します。 
8. Docker Compose を使用して、アプリケーションの新しいバージョンがデプロイされます。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次のタスクを実行する必要があります。

- [Azure コンテナー サービスに Swarm クラスターを作成する](container-service-deployment.md)
- [Azure コンテナー サービスの Swarm クラスターと接続する](container-service-connect.md)
- [Azure コンテナー レジストリを作成する](../container-registry/container-registry-get-started-portal.md)
- [Visual Studio Team Services アカウントとチーム プロジェクトを作成する](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [GitHub アカウントに GitHub リポジトリをフォークする](https://github.com/jcorioland/MyShop/)

Docker がインストールされた Ubuntu (14.04 または 16.04) マシンも必要です。 このマシンは、Visual Studio Team Services がビルドおよびリリース プロセス中に使用します。 このマシンを作成する方法の&1; つに、[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/) から入手できるイメージを使用する方法があります。 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>手順 1: Visual Studio Team Services アカウントの構成 

このセクションでは、Visual Studio Team Services アカウントを構成します。

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Visual Studio Team Services の Linux ビルド エージェントの構成

Docker イメージを作成し、Visual Studio Team Services ビルドから Azure コンテナー レジストリにこれらのイメージをプッシュするには、Linux エージェントを登録する必要があります。 次のインストール オプションがあります。

* [エージェントを Linux にデプロイする](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Docker を使用して、VSTS エージェントを実行する](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Docker 統合 VSTS 拡張機能のインストール

Microsoft では、ビルドとリリース プロセスで Docker を使用できる VSTS 拡張機能を提供しています。 この拡張機能は、[VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker) から入手できます。 **[Install (インストール)]** をクリックして、VSTS アカウントにこの拡張機能を追加します。

![Docker 統合のインストール](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

資格情報を使って、VSTS アカウントに接続するよう求められます。 

### <a name="connect-visual-studio-team-services-and-github"></a>Visual Studio Team Services と GitHub の接続

VSTS プロジェクトと GitHub アカウント間の接続を設定します。

1. Visual Studio Team Services プロジェクトで、ツールバーの **[設定]** アイコンをクリックし、**[サービス]** を選択します。

    ![Visual Studio Team Services - 外部接続](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. 左側で、**[新しいサービス エンドポイント]** > **[GitHub]** の順にクリックします。

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. VSTS が GitHub アカウントを使用することを承認するには、**[認証]** をクリックし、開いたウィンドウの手順に従います。

    ![Visual Studio Team Services - GitHub の承認](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Azure コンテナー レジストリと Azure Container Service クラスターへの VSTS の接続

CI/CD パイプラインに進む前の最後の手順として、Azure でコンテナー レジストリと Docker Swarm クラスターへの外部接続を構成します。 

1. Visual Studio Team Services プロジェクトの **[サービス]** 設定で、**Docker Registry** タイプのサービス エンドポイントを追加します。 

2. 表示されたポップアップで、URL と Azure コンテナー レジストリの資格情報を入力します。

    ![Visual Studio Team Services - Docker レジストリ](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Docker Swarm クラスターの場合は、**SSH** タイプのエンドポイントを追加します。 その後、Swarm クラスターの SSH 接続情報を入力します。

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

これで、すべての構成は完了です。 次の手順では、アプリケーションをビルドし、Docker Swarm クラスターにデプロイする CI/CD パイプラインを作成します。 

## <a name="step-2-create-the-build-definition"></a>手順 2: ビルド定義の作成

この手順では、VSTS プロジェクトのビルド定義を設定し、コンテナー イメージのビルド ワークフローを定義します。

### <a name="initial-definition-setup"></a>定義の初期設定

1. ビルド定義を作成するには、Visual Studio Team Services プロジェクトに接続し、**[Build & Release (ビルドとリリース)]** をクリックします。 

2. **[ビルド定義]** セクションで、**[+ 新規]** をクリックします。 **[空]** テンプレートを選択します。

    ![Visual Studio Team Services - 新しいビルド定義](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. GitHub リポジトリ ソースを使用して新しいビルドを構成し、**[継続的インテグレーション]** チェック ボックスをオンにして、Linux エージェントを登録したエージェント キューを選択します。 **[作成]** をクリックして、ビルド定義を作成します。

    ![Visual Studio Team Services - ビルド定義の作成](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. **[ビルド定義]** ページで、最初に **[リポジトリ]** タブを開き、前提条件で作成した MyShop プロジェクトのフォークを使用するようビルドを構成します。 **[既定のブランチ]** で *[acs-docs]* を選択していることを確認します。

    ![Visual Studio Team Services - ビルドのリポジトリの構成](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. **[トリガー]** タブで、各コミット後にトリガーされるようにビルドを構成します。 **[継続的インテグレーション]** と **[バッチ変更]** チェック ボックスをオンにします。

    ![Visual Studio Team Services - ビルドのトリガーの構成](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>ビルド ワークフローの定義
次の手順では、ビルド ワークフローを定義します。 *MyShop* アプリケーションの場合、ビルドするコンテナー イメージが&5; つあります。 各イメージは、プロジェクト フォルダーにある Dockerfile を使用してビルドされます。

* ProductsApi
* プロキシ
* RatingsApi
* RecommandationsApi
* ShopFront

イメージごとに&2; つの Docker ステップ (イメージをビルドするステップと Azure コンテナー レジストリにイメージをプッシュするステップ) を追加する必要があります。 

1. ビルド ワークフローにステップを追加するには、**[+ ビルド ステップの追加]** をクリックし、**[Docker]** を選択します。

    ![Visual Studio Team Services - ビルド ステップの追加](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. 各イメージについて、`docker build` コマンドを使用する&1; つ目のステップを構成します。

    ![Visual Studio Team Services - Docker のビルド](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    ビルド操作については、Azure コンテナー レジストリ、**[Build an image (イメージのビルド)]** アクション、および各イメージを定義する Dockerfile を選択します。 **[Build context (ビルド コンテキスト)]** に Dockerfile のルート ディレクトリを設定し、**イメージ名**を定義します。 
    
    前の画面に示すように、イメージ名の先頭に Azure コンテナー レジストリの URI を付けます (ビルド変数を使用して、イメージのタグ (この例のビルド識別子など) をパラメーター化することもできます)。

3. 各イメージについて、`docker push` コマンドを使用する&2; つ目のステップを構成します。

    ![Visual Studio Team Services - Docker のプッシュ](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    プッシュ操作については、Azure コンテナー レジストリ、**[Push an image (イメージのプッシュ)]** アクションを選択し、前のステップでビルドした**イメージ名**を入力します。

4. 5 つの各イメージのビルド ステップとプッシュ ステップを構成したら、ビルド ワークフローにステップをもう&2; つ追加します。

    a. bash スクリプトを使用して、docker-compose.yml ファイル内に出現する *BuildNumber* を現在のビルド ID に置き換えるコマンド ライン タスク。 詳細については、次の画面を参照してください。

    ![Visual Studio Team Services - compose ファイルの更新](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. 更新された compose ファイルをビルド成果物としてドロップし、リリースで使用できるようにするタスク。 詳細については、次の画面を参照してください。

    ![Visual Studio Team Services - compose ファイルの発行](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. **[保存]** をクリックし、ビルド定義に名前を付けます。

## <a name="step-3-create-the-release-definition"></a>手順 3: リリース定義の作成

Visual Studio Team Services を使用すると、[複数の環境のリリースを管理](https://www.visualstudio.com/team-services/release-management/)できます。 継続的なデプロイを有効にして、さまざまな環境 (開発、テスト、運用前、運用など) にアプリケーションをスムーズにデプロイすることができます。 Azure Container Service の Docker Swarm クラスターを表す新しい環境を作成できます。

![Visual Studio Team Services - ACS へのリリース](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>リリースの初期設定

1. リリース定義を作成するために、**[リリース]** > **[+ リリース]** の順にクリックします。

2. 成果物ソースを構成するために、**[成果物]** > **[成果物ソースのリンク]** の順にクリックします。 ここでは、この新しいリリース定義を、前の手順で定義したビルドにリンクします。 これにより、docker-compose.yml ファイルをリリース プロセスで使用できるようになります。

    ![Visual Studio Team Services - リリースの成果物](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. リリース トリガーを構成するために、**[トリガー]** をクリックし、**[継続的配置]** を選択します。 同じ成果物ソースにトリガーを設定します。 この設定により、ビルドが正常に完了するとすぐに新しいリリースが開始されます。

    ![Visual Studio Team Services - リリースのトリガー](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>リリース ワークフローの定義

リリース ワークフローは、追加する&2; つのタスクで構成されます。

1. 前に構成した SSH 接続を使用して、Docker Swarm マスター ノード上の *deploy* フォルダーに compose ファイルを安全にコピーするタスクを構成します。 詳細については、次の画面を参照してください。

    ![Visual Studio Team Services - リリースの SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. bash コマンドを実行して、マスター ノードで `docker` コマンドと `docker-compose` コマンドを実行する&2; つ目のタスクを構成します。 詳細については、次の画面を参照してください。

    ![Visual Studio Team Services - リリースの bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    マスターで実行されるコマンドでは、Docker CLI と Docker-Compose CLI を使用して、次のタスクを実行します。

    - Azure コンテナー レジストリにログインします (**[変数]** タブで定義されている&3; つのビルド変数を使用します)。
    - Swarm エンドポイント (:2375) を使用するよう **DOCKER_HOST** 変数を定義します。
    - 前の安全なコピー タスクによって作成された、docker-compose.yml ファイルを含む *deploy* フォルダーに移動します。 
    - 新しいイメージの取得、サービスの停止、サービスの削除、コンテナーの作成を行う `docker-compose` コマンドを実行します。

    >[!IMPORTANT]
    > 前の画面に示すように、**[STDERR でのエラー]** チェック ボックスをオフのままにします。 `docker-compose` では標準エラー出力でいくつかの診断メッセージ (コンテナーが停止中や削除中など) が出力されるため、これは重要な設定です。 このチェック ボックスをオンにすると、すべてうまくいった場合でも、Visual Studio Team Services からリリース中にエラーが発生したと報告されます。
    >
3. この新しいリリース定義を保存します。


>[!NOTE]
>このデプロイでは、古いサービスを停止し、新しいサービスを実行するため、ある程度のダウンタイムが発生します。 Blue-Green デプロイを実行することで、この問題を回避できます。
>

## <a name="step-4-test-the-cicd-pipeline"></a>手順 4. CI/CD パイプラインのテスト

構成が完了したので、この新しい CI/CD パイプラインをテストしてみましょう。 テストする最も簡単な方法は、ソース コードを更新し、変更を GitHub リポジトリにコミットすることです。 コードをプッシュしてから数秒経つと、Visual Studio Team Services に実行中の新しいビルドが表示されます。 正常に完了すると、新しいリリースがトリガーされ、アプリケーションの新しいバージョンが Azure Container Service クラスターにデプロイされます。

## <a name="next-steps"></a>次のステップ

* Visual Studio Team Services を使用した CI/CD の詳細については、[VSTS ビルドの概要](https://www.visualstudio.com/docs/build/overview)に関するページを参照してください。


<!--HONumber=Dec16_HO2-->


