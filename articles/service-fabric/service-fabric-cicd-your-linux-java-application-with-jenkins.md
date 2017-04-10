---
title: "Jenkins を使用した Azure Service Fabric Linux Java アプリケーションの継続的なビルドと統合 | Microsoft Docs"
description: "Jenkins を使用した Linux Java アプリケーションの継続的なビルドと統合"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 71e3d130f22515d22dc7f486f3dede936b874049
ms.lasthandoff: 03/25/2017


---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>Jenkins を使用した Linux Java アプリケーションのビルドとデプロイ
Jenkins は、アプリの継続的な統合とデプロイを行うための一般的なツールです。 この記事では、Jenkins を使用して Azure Service Fabric アプリケーションをビルドし、デプロイする方法について説明します。

## <a name="general-prerequisites"></a>一般的な前提条件
- Git をローカルにインストールしておきます。 [Git のダウンロード ページ](https://git-scm.com/downloads)から、お使いのオペレーティング システムに応じて適切な Git バージョンをインストールできます。 Git を使用するのが初めての場合、[Git に関するドキュメント](https://git-scm.com/docs)で詳細を確認してください。
- Service Fabric Jenkins プラグインを用意します。 これは、[Service Fabric のダウンロード](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)で入手できます。

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Service Fabric クラスター内での Jenkins のセットアップ

Jenkins は、Service Fabric クラスター内外でセットアップできます。 次のセクションでは、クラスター内でセットアップする方法について説明します。

### <a name="prerequisites"></a>前提条件
1. Service Fabric Linux クラスターを用意します。 Azure Portal で作成された Service Fabric クラスターには、Docker が既にインストールされています。 クラスターをローカルで実行している場合、コマンド ``docker info`` を使用して、Docker がインストールされているかどうかを確認してください。 Docker がインストールされていない場合、次のコマンドを使用して適宜インストールします。

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. 次の手順を使用して、Service Fabric コンテナー アプリケーションをクラスターにデプロイします。

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
これでクラスターに Jenkins コンテナーがインストールされます。このコンテナーは Service Fabric Explorer を使用して監視できます。

### <a name="steps"></a>手順
1. ブラウザーで ``http://PublicIPorFQDN:8081`` に移動します。 サインインに必要な最初の管理者パスワードのパスが提供されます。 引き続き、管理者ユーザーとして Jenkins を使用できます。 また、最初の管理者アカウントを使用してサインインした後、ユーザーを作成して変更することもできます。

   > [!NOTE]
   > クラスターの作成時にアプリケーションのエンドポイント ポートとしてポート 8081 が指定されていることを確認してください。
   >

2. ``docker ps -a`` を使用してコンテナーのインスタンス ID を取得します。
3. Secure Shell (SSH) でコンテナーにサインインして、Jenkins ポータルに表示されたパスを貼り付けます。 たとえば、ポータルに表示されたパスが `PATH_TO_INITIAL_ADMIN_PASSWORD` の場合、次のコマンドを実行します。

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. 「[Generating a new SSH key and adding it to the SSH agent (新しい SSH キーの生成と SSH エージェントへの追加)](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)」で説明されている手順を使用して、Jenkins を利用できるように GitHub をセットアップします。
    * GitHub によって提供されている手順を使用して、SSH キーを生成し、リポジトリをホストする GitHub アカウントにその SSH キーを追加します。
    * (ホストではなく) Jenkins Docker シェルで、前のリンク先に記載されているコマンドを実行します。
    * ホストから Jenkins シェルにサインインするには、次のコマンドを使用します。

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Service Fabric クラスター外での Jenkins のセットアップ

Jenkins は、Service Fabric クラスターの内部か外部でセットアップできます。 次のセクションでは、クラスターの外部でセットアップする方法について説明します。

### <a name="prerequisites"></a>前提条件
Docker がインストールされている必要があります。 ターミナルからの Docker のインストールには次のコマンドを使用できます。

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

ターミナルで ``docker info`` を実行すると、Docker サービスが実行されていることが出力に表示されます。

### <a name="steps"></a>手順
  1. Service Fabric Jenkins コンテナー イメージを取得します。``docker pull raunakpandya/jenkins:v1``
  2. コンテナー イメージを実行します。``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. コンテナー イメージ インスタンスの ID を取得します。 ``docker ps –a`` コマンドを使用して、すべての Docker コンテナーの一覧を取得できます。
  4. 次の手順を使用して Jenkins ポータルにサインインします。

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    コンテナー ID が 2d24a73b5964 の場合、2d24 を使用します。
    * このパスワードは、ポータルから Jenkins ダッシュボードにサインインする際に必要になります (``http://<HOST-IP>:8080``)。
    * 初めてサインインすると、独自のユーザー アカウントを作成して今後使用するか、管理者アカウントを使い続けることができます。 ユーザーを作成したら、それを使い続ける必要があります。
  5. 「[Generating a new SSH key and adding it to the SSH agent (新しい SSH キーの生成と SSH エージェントへの追加)](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)」で説明されている手順を使用して、Jenkins を利用できるように GitHub をセットアップします。
        * GitHub によって提供されている手順を使用して、SSH キーを生成し、リポジトリをホストする GitHub アカウントにその SSH キーを追加します。
        * (ホストではなく) Jenkins Docker シェルで、前のリンク先に記載されているコマンドを実行します。
        * ホストから Jenkins シェルにサインインするには、次のコマンドを使用します。

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Jenkins コンテナー イメージがホストされているクラスターまたはマシンに公開 IP があることを確認します。 この公開 IP を使用することで、Jenkins インスタンスは GitHub から通知を受け取ることができます。

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>ポータルからの Service Fabric Jenkins プラグインのインストール

1. ``http://PublicIPorFQDN:8081`` に移動します
2. Jenkins ダッシュボードで、**[Manage Jenkins (Jenkins の管理)]** > **[Manage Plugins (プラグインの管理)]** > **[Advanced (詳細設定)]** の順に選択します。
ここでは、プラグインをアップロードできます。 **[Choose file (ファイルの選択)]** を選択し、前提条件に従ってダウンロードした **serviceFabric.hpi** ファイルを選択します。 **[Upload (アップロード)]** を選択すると、Jenkins によってプラグインが自動的にインストールされます。 要求された場合は再起動を許可します。

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins ジョブの作成と構成

1. ダッシュボードで**新しい項目**を作成します。
2. 項目の名前を入力します (例: **MyJob**)。 **フリースタイル プロジェクト**を選択し、**[OK]** をクリックします。
3. ジョブ ページに移動し、**[Configure (構成)]** をクリックします。

   a. 全般セクションの **[GitHub project (GitHub プロジェクト)]** で、GitHub プロジェクトの URL を指定します。 この URL では、Jenkins の継続的インテグレーション/継続的デプロイ (CI/CD) フローと統合する Service Fabric Java アプリケーションがホストされます (例: ``https://github.com/sayantancs/SFJenkins``)。

   b. **[Source Code Management (ソース コードの管理)]** セクションで **[Git]** を選択します。 Jenkins CI/CD フローと統合する Service Fabric Java アプリケーションをホストするリポジトリの URL を指定します (例: ``https://github.com/sayantancs/SFJenkins.git``)。 ここで、ビルドする分岐を指定することもできます (例: **/master**)。
4. Jenkins と対話できるように (リポジトリをホストする) *GitHub* を構成します。 次の手順に従います。

   a. GitHub リポジトリ ページに移動します。 次に、**[Settings (設定)]** > **[Integrations and Services (統合とサービス)]** の順に移動します。

   b. **[Add Service (サービスの追加)]** を選択して「**Jenkins**」と入力し、**[Jenkins-GitHub plugin (Jenkins-GitHub プラグイン)]** を選択します。

   c. Jenkins webhook の URL を入力します (既定では、``http://<PublicIPorFQDN>:8081/github-webhook/`` です)。 **[Add service (サービスの追加)] または [Update service (サービスの更新)]** をクリックします。

   d. テスト イベントが Jenkins インスタンスに送信されます。 GitHub で webhook の側に緑色のチェック マークが表示され、プロジェクトがビルドされます。

   e. **[Build Triggers (ビルド トリガー)]** セクションで、目的のビルド オプションを選択します。 この例では、リポジトリにプッシュが行われるたびにビルドがトリガーされるようにします。 そのため、**[GitHub hook trigger for GITScm polling (GITScm ポーリングの GitHub フック トリガー)]** を選択します  (以前、このオプションの名前は **[Build when a change is pushed to GitHub (変更が GitHub にプッシュされたときにビルド)]** でした)。

   f. **[Build (ビルド)]** セクションで、**[Add build step (ビルド手順の追加)]** ボックスの一覧の **[Invoke Gradle Script (Gradle スクリプトの呼び出し)]** オプションを選択します。 表示されたウィジェットで、**[Root build script (ルート ビルド スクリプト)]** にアプリケーションのパスを指定します。 ウィジェットは、指定されたパスから build.gradle を取得し、適切に動作します。 (Eclipse プラグインまたは Yeoman ジェネレーターを使用して) ``MyActor`` という名前のプロジェクトを作成した場合は、ルート ビルド スクリプトに ``${WORKSPACE}/MyActor`` を含める必要があります。 次のスクリーンショットで、この手順の例を確認してください。

    ![Service Fabric Jenkins のビルド アクション][build-step]

   g. **[Post-Build Actions (ビルド後のアクション)]** ボックスの一覧の **[Deploy Service Fabric Project (Service Fabric プロジェクトのデプロイ)]** を選択します。 ここでは、Jenkins でコンパイルされた Service Fabric アプリケーションをデプロイするクラスターの詳細を指定する必要があります。 アプリケーションのデプロイに使用される追加のアプリケーションの詳細を指定することもできます。 次のスクリーンショットで、この手順の例を確認してください。

    ![Service Fabric Jenkins のビルド アクション][post-build-step]

   > [!NOTE]
   > このクラスターは、Service Fabric を使用して Jenkins コンテナー イメージをデプロイする場合に、Jenkins コンテナー アプリケーションをホストするクラスターと同じにすることもできます。
   >

## <a name="next-steps"></a>次のステップ
GitHub と Jenkins の構成が完了しました。 リポジトリ (https://github.com/sayantancs/SFJenkins など) の ``MyActor`` プロジェクトにサンプルの変更を加えてみましょう。 リモートの ``master`` 分岐 (または動作するように構成したいずれかの分岐) に変更をプッシュすると、 構成した Jenkins ジョブ ``MyJob`` がトリガーされます。 GitHub から変更内容がフェッチされてビルドが行われ、ビルド後のアクションで指定したクラスター エンドポイントにアプリケーションがデプロイされます。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

