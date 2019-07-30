---
title: CI/CD を使用して Azure Service Fabric クラスターへコンテナー アプリケーションをデプロイする
description: このチュートリアルでは、Visual Studio Azure DevOps を使用して、Azure Service Fabric コンテナー アプリケーションの継続的インテグレーションと継続的配置をセットアップする方法について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b686ceace3679d1541e8f1a74bca7e99b81ba932
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598895"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>チュートリアル:CI/CD を使用して Service Fabric クラスターへコンテナー アプリケーションをデプロイする

このチュートリアルはシリーズの第 2 部です。Visual Studio と Azure DevOps を使用して、Azure Service Fabric コンテナー アプリケーションの継続的インテグレーションと継続的配置を設定する方法について説明します。  既存の Service Fabric アプリケーションが必要で、「[Windows コンテナー内の .NET アプリケーションを Azure Service Fabric にデプロイする](service-fabric-host-app-in-a-container.md)」で作成されたアプリケーションを例として使用します。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * Visual Studio Team Explorer でビルド定義を作成する
> * Visual Studio Team Explorer でリリース定義を作成する
> * アプリケーションを自動的にデプロイおよびアップグレードする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure にクラスターを用意する、または[このチュートリアルを参照して作成する](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [コンテナー化されたアプリケーションをクラスターにデプロイする](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>発行プロファイルの準備

[コンテナー アプリケーションをデプロイ](service-fabric-host-app-in-a-container.md)できたので、継続的インテグレーションを設定する準備が整いました。  まず、Azure DevOps 内で実行されるデプロイ プロセスで使用する発行プロファイルを、アプリケーション内に準備します。  発行プロファイルは、あらかじめ作成したクラスターを対象とするように構成する必要があります。  Visual Studio を起動し、既存の Service Fabric アプリケーション プロジェクトを開きます。  **ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[発行]** を選択します。

継続的インテグレーション ワークフローに使用するアプリケーション プロジェクト内で目標一覧表 (たとえば Cloud) を選択します。  クラスター接続エンドポイントを指定します。  Azure DevOps 内の各デプロイ用にアプリケーションがアップグレードされるよう、 **[アプリケーションのアップグレード]** チェックボックスをオンにします。  **[保存]** ハイパーリンクをクリックして設定を発行プロファイルに保存し、 **[キャンセル]** をクリックしてダイアログ ボックスを閉じます。

![プロファイルのプッシュ][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>新しい Azure DevOps の Git リポジトリで Visual Studio ソリューションを共有する

ビルドを生成できるように、Azure DevOps のチーム プロジェクトで、アプリケーションのソース ファイルを共有します。

Visual Studio の右下隅のステータス バーにある **[ソース管理** ->  **Git に追加]** を選択して、プロジェクトの新しいローカル Git リポジトリを作成します。

**チーム エクスプローラー**の **[プッシュ]** ビューで、 **[Push to Azure DevOps]\(Azure DevOps へのプッシュ\)** の下にある **[Git リポジトリの発行]** ボタンを選択します。

![Git リポジトリのプッシュ][push-git-repo]

電子メールを確認し、 **[アカウント]** ドロップダウンからご自身の組織を選択します。 まだ組織が存在しない場合は、組織を設定する必要があります。 リポジトリ名を入力し、 **[リポジトリの発行]** を選択します。

![Git リポジトリのプッシュ][publish-code]

リポジトリを公開すると、ローカル リポジトリと同じ名前の新しいチーム プロジェクトが自分のアカウントに作成されます。 既存のチーム プロジェクトでリポジトリを作成するには、 **[リポジトリ名]** の横にある **[詳細]** をクリックして、チーム プロジェクトを選択します。 **[See it on the web]\(Web を参照\)** を選択すると、Web でコードを表示できます。

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Azure Pipelines を使用して継続的デリバリーを構成する

Azure DevOps のビルド定義では、順次実行される一連のビルド ステップで構成されたワークフローを記述します。 Service Fabric クラスターをデプロイするため、Service Fabric アプリケーション パッケージおよび他のアーティファクトを生成するビルド定義を作成します。 Azure DevOps の[ビルド定義](https://www.visualstudio.com/docs/build/define/create)について詳細を確認してください。 

Azure DevOps のリリース定義では、クラスターにアプリケーション パッケージをデプロイするワークフローを記述します。 ビルド定義とリリース定義を併用すると、ソース ファイルから始まり、クラスターでのアプリケーションの実行で終わるワークフロー全体を実行できます。 Azure DevOps の[リリース定義](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)について詳細を確認してください。

### <a name="create-a-build-definition"></a>ビルド定義の作成

Web ブラウザーで https://dev.azure.com に移動し、該当する組織と新しいプロジェクトを順に選択して、新しいチーム プロジェクトを開きます。 

左側のパネルの **[パイプライン]** オプションを選択し、 **[新しいパイプライン]** をクリックします。

>[!NOTE]
>ビルド定義テンプレートが表示されない場合は、 **[YAML パイプライン作成の新しいエクスペリエンス]** 機能がオフになっていることを確認します。 この機能は、DevOps アカウントの **[プレビュー機能]** セクションで構成します。

![新しいパイプライン][new-pipeline]

ソースとして **[Azure Repos Git]** を選択し、該当するチーム プロジェクト名、プロジェクト リポジトリ、 **[マスター]** 既定ブランチまたは手動のビルドおよびスケジュールされたビルドを選択します。  **[Continue]\(続行\)** をクリックします。

**[テンプレートの選択]** で **[Docker がサポートされている Azure Service Fabric アプリケーション]** テンプレートを選択し、 **[適用]** をクリックします。

![ビルド テンプレートを選択する][select-build-template]

**[タスク]** で、 **[エージェント プール]** として **[Hosted VS2017]** を選択します。

![タスクを選択する][task-agent-pool]

**[Tag images]\(イメージにタグ付け\)** をクリックします。

**[コンテナー レジストリの種類]** で、 **[Azure Container Registry]** を選択します。 **Azure サブスクリプション**を選択し、 **[承認]** をクリックします。 **Azure コンテナー レジストリ**を選択します。

![Docker で [Tag images]\(イメージにタグ付け\) を選択する][select-tag-images]

**[Push images]\(イメージをプッシュ\)** をクリックします。

**[コンテナー レジストリの種類]** で、 **[Azure Container Registry]** を選択します。 **Azure サブスクリプション**を選択し、 **[承認]** をクリックします。 **Azure コンテナー レジストリ**を選択します。

![Docker で [Push images]\(イメージをプッシュ\) を選択する][select-push-images]

**[トリガー]** タブで **[継続的インテグレーションを有効にする]** をオンにして、継続的インテグレーションを有効にします。 **[ブランチ フィルター]** で **[+ 追加]** をクリックすると、 **[ブランチ仕様]** が既定の **[マスター]** になります。

**[ビルド パイプラインとキューの保存]** ダイアログで、 **[保存してキューに登録]** をクリックして手動でビルドを開始します。

![トリガーを選択する][save-and-queue]

プッシュまたはチェックイン時にもビルドがトリガーされます。 ビルドの進行状況を確認するには、 **[ビルド]** タブに切り替えます。ビルドが正常に実行されることを確認したら、クラスターにアプリケーションをデプロイするリリース定義を定義します。

### <a name="create-a-release-definition"></a>リリース定義の作成

左側のパネルの **[パイプライン]** オプションを選択し、 **[リリース]** を選択して、 **[+ 新しいパイプライン]** をクリックします。  **[テンプレートの選択]** で一覧から **[Azure Service Fabric の配置]** テンプレートを選択し、 **[適用]** を選択します。

![リリース テンプレートを選択する][select-release-template]

**[タスク]** 、 **[環境 1]** 、 **[+ 新規]** の順に選択し、新しいクラスター接続を追加します。

![クラスターの接続を追加する][add-cluster-connection]

**[Add new Service Fabric Connection]\(新しい Service Fabric 接続\)** ビューで、 **[証明書ベース]** または **[Azure Active Directory]** 認証を選択します。  接続名 "mysftestcluster" とクラスター エンドポイント "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (またはデプロイ先のクラスターのエンドポイント) を指定します。

証明書ベースの認証の場合は、クラスターの作成に使用したサーバー証明書の**サーバー証明書拇印**を追加します。  **[クライアント証明書]** で、クライアント証明書ファイルの Base 64 エンコーディングを追加します。 証明書の Base 64 でエンコードされた表現を取得する方法については、そのフィールドのヘルプ ポップアップをご覧ください。 また、証明書の**パスワード**も追加します。  別のクライアント証明書がない場合は、クラスター証明書またはサーバー証明書を使用できます。

Azure Active Directory の資格情報の場合は、クラスターの作成に使用したサーバー証明書の**サーバー証明書拇印**を追加し、 **[ユーザー名]** フィールドと **[パスワード]** フィールドにクラスターへの接続で使用する資格情報を指定します。

**[追加]** をクリックして、クラスター接続を保存します。



[エージェント フェーズ] で、 **[Service Fabric アプリケーションの展開]** をクリックします。
**[Docker 設定]** 、 **[Docker 設定の構成]** の順にクリックします。 **[レジストリ資格情報のソース]** で、 **[Azure Resource Manager Service Connection]\(Azure Resource Manager サービス接続\)** を選択します。 次に **Azure サブスクリプション**を選択します。

![リリース パイプライン エージェント][release-pipeline-agent]

次に、ビルド成果物をパイプラインに追加して、リリース定義でビルドの出力を見つけられるようにします。 **[パイプライン]** を選択し、 **[成果物]** -> **[+ 追加]** の順に選択します。  **[ソース (ビルド定義)]** で、前に作成したビルド定義を選択します。  **[追加]** をクリックして、ビルド成果物を保存します。

![成果物を追加する][add-artifact]

ビルドの完了時にリリースが自動的に作成されるように、継続的配置トリガーを有効にします。 成果物の稲妻のアイコンをクリックし、トリガーを有効にします。次に、 **[保存]** をクリックして、リリース定義を保存します。

![トリガーを有効にする][enable-trigger]

**[+ リリース]**  ->  **[リリースの作成]**  ->  **[作成]** の順に選択して、リリースを手動で作成します。 **[リリース]** タブで、リリースの進行状況を監視できます。

デプロイが成功し、アプリケーションがクラスターで実行されていることを確認します。  Web ブラウザーを開いて、[http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/) にアクセスします。  この例では、アプリケーション バージョンは「1.0.0.20170616.3」となっていることに注意してください。

## <a name="commit-and-push-changes-trigger-a-release"></a>変更のコミットとプッシュ、リリースのトリガー

Azure DevOps へのコード変更をチェックインして、継続的インテグレーション パイプラインが機能していることを確認します。

コードを記述すると、変更内容は Visual Studio によって自動的に追跡されます。 右下のステータス バーから [保留中の変更] アイコン (![Pending][pending]) を選択して、ローカル Git リポジトリへ変更をコミットします。

チーム エクスプローラーの **[変更]** ビューで、更新内容を説明するメッセージを追加し、変更をコミットします。

![すべてコミットする][changes]

発行されていない変更のステータス バー アイコンを選択 (![発行されていない変更][unpublished-changes]) するか、またはチーム エクスプローラーで [同期] ビューを選択します。 **[プッシュ]** を選択して、Azure DevOps のコードを更新します。

![変更をプッシュする][push]

Azure DevOps へ変更をプッシュすると、ビルドが自動的にトリガーされます。  ビルド定義が正常に完了すると、リリースは自動的に作成され、クラスター上のアプリケーションのアップグレードが開始されます。

ビルドの進行状況を確認するには、Visual Studio の**チーム エクスプローラー**で **[ビルド]** タブに切り替えます。  ビルドが正常に実行されることを確認したら、クラスターにアプリケーションをデプロイするリリース定義を定義します。

デプロイが成功し、アプリケーションがクラスターで実行されていることを確認します。  Web ブラウザーを開いて、[http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/) にアクセスします。  この例では、アプリケーション バージョンが "1.0.0.20170815.3" である点に注意してください。

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>アプリケーションの更新

アプリケーションで、コードを変更します。  前の手順に従って、変更を保存し、コミットします。

アプリケーションのアップグレードが開始されると、Service Fabric Explorer でアップグレードの進行状況を見ることができます。

![Service Fabric Explorer][sfx2]

アプリケーションのアップグレードには数分かかる場合があります。 アップグレードが完了すると、アプリケーションは次のバージョンで実行されます。  この例では "1.0.0.20170815.4" です。

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * ビルド定義の作成
> * リリース定義の作成
> * アプリケーションを自動的にデプロイおよびアップグレードする

チュートリアルの次の部分で、[コンテナーの監視](service-fabric-tutorial-monitoring-wincontainers.md)をセットアップする方法を学習します。

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
