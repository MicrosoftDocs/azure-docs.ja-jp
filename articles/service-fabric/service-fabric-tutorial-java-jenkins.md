---
title: Azure で Service Fabric 上の Java アプリ用に Jenkins を構成する
description: このチュートリアルでは、Jenkins を使用して Java Service Fabric アプリケーションをデプロイするための継続的インテグレーションをセットアップする方法について説明します。
ms.topic: tutorial
ms.date: 08/27/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4e29104f0586a8e18afb7a640903c57ce988b692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531974"
---
# <a name="tutorial-configure-a-jenkins-environment-to-enable-cicd-for-a-java-application-on-service-fabric"></a>チュートリアル:Service Fabric 上の Java アプリケーションに対して CI/CD を有効にするように Jenkins 環境を構成する

このチュートリアルは、シリーズの第 5 部です。 Jenkins を使用してアプリケーションにアップグレードをデプロイする方法を示します。 このチュートリアルでは、Service Fabric Jenkins プラグインと、投票アプリケーションをホストする GitHub リポジトリを組み合わせて使用して、アプリケーションをクラスターにデプロイします。

シリーズの第 5 部で学習する内容は次のとおりです。
> [!div class="checklist"]
> * Service Fabric Jenkins コンテナーをマシンにデプロイする
> * Service Fabric へのデプロイ用に Jenkins 環境をセットアップする
> * アプリケーションをアップグレードする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Java Service Fabric Reliable Services アプリケーションをビルドする](service-fabric-tutorial-create-java-app.md)
> * [ローカル クラスターでアプリケーションをデプロイおよびデバッグする](service-fabric-tutorial-debug-log-local-cluster.md)
> * [アプリケーションを Azure クラスターにデプロイする](service-fabric-tutorial-java-deploy-azure.md)
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-java-elk.md)
> * CI/CD をセットアップする

## <a name="prerequisites"></a>前提条件

* [Git のダウンロード ページ](https://git-scm.com/downloads)からローカル コンピューターに Git をインストールしている。 Git の詳細については、[Git のドキュメント](https://git-scm.com/docs)を参照してください。
* [Jenkins](https://jenkins.io/) に関する実践的な知識がある。
* [GitHub](https://github.com/) アカウントを作成済みで、GitHub の使い方を理解している。
* [Docker](https://www.docker.com/community-edition) をコンピューターにインストールしている。

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Service Fabric Jenkins コンテナー イメージをプルしてデプロイする

Jenkins は、Service Fabric クラスター内外でセットアップできます。 以下の手順では、提供されている Docker イメージを使用して、クラスターの外部でセットアップする方法について説明します。 ただし、事前に構成された Jenkins ビルド環境を使用することもできます。 次のコンテナー イメージは、Service Fabric プラグインと共にインストールされるため、すぐに Service Fabric で使用できます。

1. Service Fabric Jenkins コンテナー イメージを取得します``docker pull rapatchi/jenkins:v10``。 このイメージは、プリインストールされた Service Fabric Jenkins プラグインから取得されます。

1. ローカル マシン上の証明書が格納されている場所で、コンテナー イメージを実行します。

    ```bash
    docker run -itd -p 8080:8080 -v /service-fabric-java-quickstart/AzureCluster rapatchi/jenkins:v10
    ```

1. コンテナー イメージ インスタンスの ID を取得します。 ``docker ps –a`` コマンドを使用して、すべての Docker コンテナーの一覧を取得できます。

1. 次のコマンドを実行して、Jenkins インスタンスのパスワードを取得します。

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    コンテナー ID が 2d24a73b5964 の場合、2d24 を使用します。
    * このパスワードは、ポータルから Jenkins ダッシュボードにサインインする際に必要になります (``http://<HOST-IP>:8080``)。
    * 初めてサインインした後は、自分のユーザー アカウントを作成することも、管理者アカウントを使用することもできます。

1. 「[Generating a new SSH key and adding it to the SSH agent (新しい SSH キーの生成と SSH エージェントへの追加)](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)」で説明されている手順を使用して、Jenkins を利用できるように GitHub をセットアップします。 コマンドは Docker コンテナーから実行されるため、Linux 環境向けの手順に従ってください。
   * GitHub の手順に従って SSH キーを生成します。 次に、リポジトリをホストしている GitHub アカウントに SSH キーを追加します。
   * (ホストではなく) Jenkins Docker シェルで、前のリンク先に記載されているコマンドを実行します。
   * ホストから Jenkins シェルにサインインするには、次のコマンドを使用します。

     ```sh
     docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
     ```

     Jenkins コンテナー イメージがホストされているクラスターまたはマシンに公開 IP があることを確認します。 この公開 IP を使用することで、Jenkins インスタンスは GitHub から通知を受け取ることができます。

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins ジョブの作成と構成

1. GitHub で投票プロジェクトをホストするために使用できるリポジトリがない場合は、最初にリポジトリを作成します。 このチュートリアルでは、これ以降、このリポジトリを **dev_test** と呼びます。

1. Jenkins ダッシュボード (``http://<HOST-IP>:8080``) で **新しい項目** を作成します。

1. 項目の名前を入力します (例: **MyJob**)。 **フリースタイル プロジェクト** を選択し、 **[OK]** をクリックします。

1. ジョブ ページに移動し、 **[Configure (構成)]** をクリックします。

   a. 全般セクションで **[GitHub project]\(GitHub プロジェクト\)** を選択し、GitHub プロジェクトの URL を指定します。 この URL では、Jenkins の継続的インテグレーション/継続的デプロイ (CI/CD) フローと統合する Service Fabric Java アプリケーションがホストされます (例: ``https://github.com/testaccount/dev_test``)。

   b. **[Source Code Management (ソース コードの管理)]** セクションで **[Git]** を選択します。 Jenkins CI/CD フローと統合する Service Fabric Java アプリケーションをホストするリポジトリの URL を指定します (例: *`https://github.com/testaccount/dev_test.git`* )。 ここで、ビルドするブランチを指定することもできます (例: **/master**)。

1. Jenkins と対話できるように (リポジトリをホストする) *GitHub* を構成します。 次の手順に従います。

   a. GitHub リポジトリ ページに移動します。 次に、 **[Settings (設定)]**  >  **[Integrations and Services (統合とサービス)]** の順に移動します。

   b. **[Add Service (サービスの追加)]** を選択して「**Jenkins**」と入力し、 **[Jenkins-GitHub plugin (Jenkins-GitHub プラグイン)]** を選択します。

   c. Jenkins webhook の URL を入力します (既定では、``http://<PublicIPorFQDN>:8081/github-webhook/`` です)。 **[Add service (サービスの追加)] または [Update service (サービスの更新)]** をクリックします。

   d. テスト イベントが Jenkins インスタンスに送信されます。 GitHub で webhook の側に緑色のチェック マークが表示され、プロジェクトがビルドされます。

   ![Service Fabric Jenkins の構成](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

1. **[Build Triggers (ビルド トリガー)]** セクションで、目的のビルド オプションを選択します。 この例では、リポジトリにプッシュが行われるたびにビルドがトリガーされるようにします。 そのため、 **[GitHub hook trigger for GITScm polling (GITScm ポーリングの GitHub フック トリガー)]** を選択します

1. **[Build (ビルド)]** セクションで、 **[Add build step (ビルド手順の追加)]** ボックスの一覧の **[Invoke Gradle Script (Gradle スクリプトの呼び出し)]** オプションを選択します。 表示されたウィジェットで詳細メニューを開き、 **[Root build script]\(ルート ビルド スクリプト\)** にアプリケーションのパスを指定します。 ウィジェットは、指定されたパスから build.gradle を取得し、適切に動作します。

    ![Service Fabric Jenkins のビルド アクション](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)

1. **[Post-Build Actions (ビルド後のアクション)]** ボックスの一覧の **[Deploy Service Fabric Project (Service Fabric プロジェクトのデプロイ)]** を選択します。 ここでは、Jenkins でコンパイルされた Service Fabric アプリケーションをデプロイするクラスターの詳細を指定する必要があります。 証明書のパスは、ボリュームがマウントされた場所です (/tmp/myCerts)。

    アプリケーションのデプロイに使用される追加の詳細を指定することもできます。 次のスクリーンショットで、アプリケーションの詳細の例を確認してください。

    ![その他の Service Fabric プロジェクト デプロイ オプションを含んだ [Post-build Actions (ビルド後のアクション)] ダイアログ](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > このクラスターは、Service Fabric を使用して Jenkins コンテナー イメージをデプロイする場合に、Jenkins コンテナー アプリケーションをホストするクラスターと同じにすることもできます。
    >

1. **[保存]** をクリックします。

## <a name="update-your-existing-application"></a>既存のアプリケーションを更新する

1. *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* ファイル内の HTML のタイトルを **Service Fabric Voting Sample V2** で更新します。

    ```html
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

1. *Voting/VotingApplication/ApplicationManifest.xml* ファイル内で、**ApplicationTypeVersion** と **ServiceManifestVersion** のバージョンを **2.0.0** に更新します。

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>
    </ApplicationManifest>
    ```

1. *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* ファイル内で、**ServiceManifest** の **Version** フィールドと **CodePackage** タグの **Version** フィールドを **2.0.0** に更新します。

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

1. アプリケーションのアップグレードを実行する Jenkins ジョブを初期化するには、新しい変更を GitHub リポジトリにプッシュします。

1. Service Fabric Explorer で、 **[アプリケーション]** ドロップダウンをクリックします。 アップグレードの状態を確認するには、 **[アップグレードの進行中]** タブをクリックします。

    ![アップグレードの進行中](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

1. **http://\<Host-IP>:8080** にアクセスすると、フル機能を備えた投票アプリケーションが実行中になっています。

    ![ローカルの投票アプリ](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Service Fabric Jenkins コンテナーをマシンにデプロイする
> * Service Fabric へのデプロイ用に Jenkins 環境をセットアップする
> * アプリケーションをアップグレードする

* その他の[Java サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)を確認する
