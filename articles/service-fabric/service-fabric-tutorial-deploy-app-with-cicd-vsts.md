---
title: Azure で継続的インテグレーションと Azure Pipelines を使用して Service Fabric アプリをデプロイする | Microsoft Docs
description: このチュートリアルでは、Azure Pipelines を使用して、Service Fabric アプリケーションの継続的インテグレーションと継続的デプロイを設定する方法について説明します。
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
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 184c57c0d9160cedef4be417f16c52c8635a1eb4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68385295"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>チュートリアル: CI/CD を使用して Service Fabric クラスターへアプリケーションをデプロイする

このチュートリアルはシリーズの第 4 部です。Azure Pipelines を使用して、Azure Service Fabric アプリケーションの継続的インテグレーションと継続的デプロイを設定する方法について説明します。  既存の Service Fabric アプリケーションが必要で、[.NET アプリケーション ビルド](service-fabric-tutorial-create-dotnet-app.md)で作成されたアプリケーションを例として使用します。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * Azure Pipelines にビルド パイプラインを作成する
> * Azure Pipelines にリリース パイプラインを作成する
> * アプリケーションを自動的にデプロイおよびアップグレードする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)
> * [アプリケーションをリモート クラスターにデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core フロントエンド サービスに HTTPS エンドポイントを追加する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Azure Pipelines を使用して CI/CD を構成する
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Visual Studio 2019 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
* [Service Fabric SDK をインストール](service-fabric-get-started.md)します。
* [このチュートリアルに従って](service-fabric-tutorial-create-vnet-and-windows-cluster.md)、たとえば、Azure 上に Windows Service Fabric クラスターを作成します。
* [Azure DevOps 組織](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)を作成します。 これにより、Azure DevOps でプロジェクトを作成し、Azure Pipelines を使用することができます。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>発行プロファイルの準備

ここまでで、[アプリケーションを作成し](service-fabric-tutorial-create-dotnet-app.md)、[アプリケーションを Azure にデプロイ](service-fabric-tutorial-deploy-app-to-party-cluster.md)しました。これで、継続的インテグレーションをセットアップする準備ができました。  まず、Azure Pipelines 内で実行されるデプロイ プロセスで使用する発行プロファイルを、アプリケーション内に準備します。  発行プロファイルは、あらかじめ作成したクラスターを対象とするように構成する必要があります。  Visual Studio を起動し、既存の Service Fabric アプリケーション プロジェクトを開きます。  **ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[発行]** を選択します。

継続的インテグレーション ワークフローに使用するアプリケーション プロジェクト内で目標一覧表 (たとえば Cloud) を選択します。  クラスター接続エンドポイントを指定します。  Azure DevOps 内の各デプロイ用にアプリケーションがアップグレードされるよう、 **[アプリケーションのアップグレード]** チェックボックスをオンにします。  **[保存]** ハイパーリンクをクリックして設定を発行プロファイルに保存し、 **[キャンセル]** をクリックしてダイアログ ボックスを閉じます。

![プロファイルのプッシュ][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>新しい Azure DevOps の Git リポジトリで Visual Studio ソリューションを共有する

ビルドを生成できるように、Azure DevOps のプロジェクトで、アプリケーションのソース ファイルを共有します。

Visual Studio の右下隅のステータス バーにある **[ソース管理** ->  **Git に追加]** を選択して、プロジェクトの新しいローカル Git リポジトリを作成します。

**チーム エクスプローラー**の **[プッシュ]** ビューで、 **[Push to Azure DevOps]\(Azure DevOps へのプッシュ\)** の下にある **[Git リポジトリの発行]** ボタンを選択します。

![Git リポジトリのプッシュ][push-git-repo]

電子メールを確認し、 **[Azure DevOps ドメイン]** ドロップダウンからご自身のアカウントを選択します。 リポジトリ名を入力し、 **[リポジトリの発行]** を選択します。

![Git リポジトリのプッシュ][publish-code]

リポジトリを公開すると、ローカル リポジトリと同じ名前の新しいプロジェクトがご自身のアカウントに作成されます。 既存のプロジェクトでリポジトリを作成するには、 **[リポジトリ名]** の横にある **[詳細]** をクリックして、プロジェクトを選択します。 **[See it on the web]\(Web を参照\)** を選択すると、Web でコードを表示できます。

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Azure Pipelines を使用して継続的デリバリーを構成する

Azure Pipelines のビルド パイプラインでは、順次実行される一連のビルド ステップで構成されたワークフローを記述します。 Service Fabric クラスターをデプロイするため、Service Fabric アプリケーション パッケージおよび他のアーティファクトを生成するビルド パイプラインを作成します。 Azure Pipelines のビルド パイプラインの詳細については、[こちら](https://www.visualstudio.com/docs/build/define/create)をご覧ください。 

Azure Pipelines のリリース パイプラインでは、クラスターにアプリケーション パッケージをデプロイするワークフローを記述します。 ビルド パイプラインとリリース パイプラインを併用すると、ソース ファイルから始まり、クラスターでのアプリケーションの実行で終わるワークフロー全体を実行できます。 Azure Pipelines のリリース パイプラインの詳細については、[こちら](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)をご覧ください。

### <a name="create-a-build-pipeline"></a>ビルド パイプラインを作成する

Web ブラウザーを開き、新しいプロジェクト ([https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting)) に移動します。

**[パイプライン]** タブ、 **[ビルド]** 、 **[新しいパイプライン]** の順に選択します。

![新しいパイプライン][new-pipeline]

ソースとして **[Azure Repos Git]** を選択し、 **[Voting]** チーム プロジェクト、 **[Voting]** リポジトリ、 **[マスター]** 既定ブランチまたは手動のビルドおよびスケジュールされたビルドを選択します。  **[Continue]\(続行\)** をクリックします。

![リポジトリの選択][select-repo]

**[テンプレートの選択]** で **[Azure Service Fabric アプリケーション]** テンプレートを選択し、 **[適用]** をクリックします。

![ビルド テンプレートを選択する][select-build-template]

**[タスク]** の **[エージェント プール]** に「Hosted VS2017」と入力します。

![タスクを選択する][save-and-queue]

**[トリガー]** で **[継続的インテグレーションを有効にする]** をオンにして、継続的インテグレーションを有効にします。 **[ブランチ フィルター]** 内で、 **[ブランチ仕様]** が既定の **[マスター]** になります。 **[保存してキューに登録]** を選択して、ビルドを手動で開始します。

![トリガーを選択する][save-and-queue2]

プッシュまたはチェックイン時にもビルドがトリガーされます。 ビルドの進行状況を確認するには、 **[ビルド]** タブに切り替えます。ビルドが正常に実行されることを確認したら、クラスターにアプリケーションをデプロイするリリース パイプラインを定義します。

### <a name="create-a-release-pipeline"></a>リリース パイプラインを作成する

**[パイプライン]** タブ、 **[リリース]** 、 **[+ 新しいパイプライン]** の順に選択します。  **[テンプレートの選択]** で一覧から **[Azure Service Fabric の配置]** テンプレートを選択し、 **[適用]** を選択します。

![リリース テンプレートを選択する][select-release-template]

**[タスク]** -> **[環境 1]** の順に選択し、 **[+ 新規]** を選択して、新しいクラスター接続を追加します。

![クラスターの接続を追加する][add-cluster-connection]

**[Add new Service Fabric Connection]\(新しい Service Fabric 接続\)** ビューで、 **[証明書ベース]** または **[Azure Active Directory]** 認証を選択します。  接続名 "mysftestcluster" とクラスター エンドポイント "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (またはデプロイ先のクラスターのエンドポイント) を指定します。

証明書ベースの認証の場合は、クラスターの作成に使用したサーバー証明書の**サーバー証明書拇印**を追加します。  **[クライアント証明書]** で、クライアント証明書ファイルの Base 64 エンコーディングを追加します。 証明書の Base 64 でエンコードされた表現を取得する方法については、そのフィールドのヘルプ ポップアップをご覧ください。 また、証明書の**パスワード**も追加します。  別のクライアント証明書がない場合は、クラスター証明書またはサーバー証明書を使用できます。

Azure Active Directory の資格情報の場合は、クラスターの作成に使用したサーバー証明書の**サーバー証明書拇印**を追加し、 **[ユーザー名]** フィールドと **[パスワード]** フィールドにクラスターへの接続で使用する資格情報を指定します。

**[追加]** をクリックして、クラスター接続を保存します。

次に、ビルド成果物をパイプラインに追加して、リリース パイプラインでビルドの出力を見つけられるようにします。 **[パイプライン]** を選択し、 **[成果物]** -> **[+ 追加]** の順に選択します。  **[ソース (ビルド定義)]** で、前に作成したビルド パイプラインを選択します。  **[追加]** をクリックして、ビルド成果物を保存します。

![成果物を追加する][add-artifact]

ビルドの完了時にリリースが自動的に作成されるように、継続的配置トリガーを有効にします。 成果物の稲妻のアイコンをクリックし、トリガーを有効にします。次に、 **[保存]** をクリックして、リリース パイプラインを保存します。

![トリガーを有効にする][enable-trigger]

**[+ リリース]**  ->  **[リリースの作成]**  ->  **[作成]** の順に選択して、リリースを手動で作成します。 **[リリース]** タブで、リリースの進行状況を監視できます。

デプロイが成功し、アプリケーションがクラスターで実行されていることを確認します。  Web ブラウザーを開いて、[http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/) にアクセスします。  この例では、アプリケーション バージョンは「1.0.0.20170616.3」となっていることに注意してください。

## <a name="commit-and-push-changes-trigger-a-release"></a>変更のコミットとプッシュ、リリースのトリガー

Azure DevOps へのコード変更をチェックインして、継続的インテグレーション パイプラインが機能していることを確認します。

コードを記述すると、変更内容は Visual Studio によって自動的に追跡されます。 右下のステータス バーから [保留中の変更] アイコン (![Pending][pending]) を選択して、ローカル Git リポジトリへ変更をコミットします。

チーム エクスプローラーの **[変更]** ビューで、更新内容を説明するメッセージを追加し、変更をコミットします。

![すべてコミットする][changes]

発行されていない変更のステータス バー アイコンを選択 (![発行されていない変更][unpublished-changes]) するか、またはチーム エクスプローラーで [同期] ビューを選択します。 **[プッシュ]** を選択して、Azure Pipelines のコードを更新します。

![変更をプッシュする][push]

Azure Pipelines へ変更をプッシュすると、ビルドが自動的にトリガーされます。  ビルド パイプラインが正常に完了すると、リリースは自動的に作成され、クラスター上のアプリケーションのアップグレードが開始されます。

ビルドの進行状況を確認するには、Visual Studio の**チーム エクスプローラー**で **[ビルド]** タブに切り替えます。  ビルドが正常に実行されることを確認したら、クラスターにアプリケーションをデプロイするリリース パイプラインを定義します。

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
> * ビルド パイプラインを作成する
> * リリース パイプラインを作成する
> * アプリケーションを自動的にデプロイおよびアップグレードする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
