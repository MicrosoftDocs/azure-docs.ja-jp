---
title: Jenkins を使用した Azure Service Fabric Linux アプリケーションの継続的なビルドと統合 | Microsoft Docs
description: Jenkins を使用した Service Fabric Linux アプリケーションの継続的なビルドと統合
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: jeconnoc
ms.openlocfilehash: b757a0a5f3ce968b396fa89d5b32c18257d620c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875078"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Jenkins を使用した Linux アプリケーションのビルドと配置
Jenkins は、アプリの継続的な統合とデプロイを行うための一般的なツールです。 この記事では、Jenkins を使用して Azure Service Fabric アプリケーションをビルドし、デプロイする方法について説明します。

## <a name="topic-overview"></a>トピックの概要
この記事では、Jenkins 環境のいくつかのセットアップ方法と、ビルド後にアプリケーションを Service Fabric クラスターにデプロイするさまざまな方法について説明します。 Jenkins を正常にセットアップし、GitHub から変更をプルし、アプリケーションをビルドしてクラスターにデプロイするには、次の一般的な手順に従います。

1. 必ず、[前提条件](#prerequisites)をインストールしてください。
1. その後、次のいずれかのセクションの手順に従って、Jenkins をセットアップします。
   * [Service Fabric クラスター内での Jenkins のセットアップ](#set-up-jenkins-inside-a-service-fabric-cluster)、 
   * [Service Fabric クラスター外での Jenkins のセットアップ](#set-up-jenkins-outside-a-service-fabric-cluster)、または
   * [既存の Jenkins 環境への Service Fabric プラグインのインストール](#install-service-fabric-plugin-in-an-existing-jenkins-environment)。
1. Jenkins を設定したら、「[Jenkins ジョブの作成と構成](#create-and-configure-a-jenkins-job)」の手順に従って、アプリケーションに変更が加えられたときに Jenkins をトリガーするように GitHub をセットアップしたり、GitHub から変更をプルしてアプリケーションをビルドするビルド ステップを使用して Jenkins ジョブ パイプラインを構成したりします。 
1. 最後に、アプリケーションを Service Fabric クラスターにデプロイするように、Jenkins ジョブのビルド後のステップを構成します。 アプリケーションをクラスターにデプロイするように Jenkins を構成する方法は 2 つあります。    
   * 開発およびテスト環境では、「[クラスター管理エンドポイントを使用してデプロイを構成する](#configure-deployment-using-cluster-management-endpoint)」に従ってください。 これはデプロイの最も簡単なセットアップ方法です。
   * 運用環境では、「[Azure 資格情報を使用してデプロイを構成する](#configure-deployment-using-azure-credentials)」に従ってください。 運用環境にはこの方法をお勧めします。Azure 資格情報を使えば、Jenkins ジョブによる Azure リソースへのアクセス権を制限できるためです。 

## <a name="prerequisites"></a>前提条件

- Git がローカルにインストールされていることを確認してください。 [Git のダウンロード ページ](https://git-scm.com/downloads)から、お使いのオペレーティング システムに応じて適切な Git バージョンをインストールできます。 Git を使用するのが初めての場合、[Git に関するドキュメント](https://git-scm.com/docs)で詳細を確認してください。
- この記事では、アプリケーションのビルドとデプロイのために GitHub にある *Service Fabric Getting Started Sample* ([https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started)) を使用します。 このリポジトリをフォークして従うことも、手順を一部変更して独自の GitHub プロジェクトを使用することもできます。


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>既存の Jenkins 環境への Service Fabric プラグインのインストール
Service Fabric プラグインを既存の Jenkins 環境に追加する場合は、以下のものが必要になります。

- [Service Fabric CLI](service-fabric-cli.md) (sfctl)。

   > [!NOTE]
   > Jenkins で CLI コマンドを実行できるよう、CLI は必ず、ユーザー レベルではなくシステム レベルでインストールしてください。 
   >

- Java アプリケーションをデプロイするには、[Gradle および Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development) の両方をインストールします。 
- .NET Core 2.0 アプリケーションをデプロイするには、[.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development) をインストールします。 

環境に必要な前提条件をインストールした後は、Jenkins Marketplace で Azure Service Fabric プラグインを検索してインストールすることができます。

プラグインをインストールした後は、「[Jenkins ジョブの作成と構成](#create-and-configure-a-jenkins-job)」に進んでください。


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Service Fabric クラスター内での Jenkins のセットアップ

Jenkins は、Service Fabric クラスター内外でセットアップできます。 次のセクションでは、Azure Storage アカウントを使用してコンテナー インスタンスの状態を保存する際にクラスター内部で Jenkins をセットアップする方法について説明します。

### <a name="prerequisites"></a>前提条件
- Service Fabric Linux クラスターに Docker をインストールしておきます。 Azure で実行する Service Fabric クラスターにあらかじめ Docker をインストールしておきます。 クラスターをローカル (OneBox 開発環境) で実行している場合は、`docker info` コマンドを使って Docker がコンピューターにインストールされているかどうかを確認します。 Docker がインストールされていない場合、次のコマンドを使用してインストールします。

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > クラスターで 8081 ポートがカスタム エンドポイントとして指定されていることを確認します。 ローカル クラスターを使用している場合は、ホスト コンピューターでポート 8081 が開いていることと、公開 IP アドレスがあることを確認します。
   >

### <a name="steps"></a>手順
1. 次のコマンドを使用して、アプリケーションを複製します。
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. ファイル共有内の Jenkins コンテナーの状態を維持します。
   1. クラスターと**同じリージョン**に Azure Storage アカウント (例: `sfjenkinsstorage1`) を作成します。
   1. そのストレージ アカウントの下に、**ファイル共有** (例: `sfjenkins`) を作成します。
   1. そのファイル共有の **[接続]** をクリックして、 **[Linux からの接続]** の下に表示される、以下のような値をメモします。

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > cifs 共有をマウントするには、クラスター ノードに cifs ユーティリティ パッケージがインストールされている必要があります。      
   >

1. `setupentrypoint.sh` スクリプトのプレースホルダーの値を、手順 2 の Azure Storage の内容に更新します。
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * `[REMOTE_FILE_SHARE_LOCATION]` を、上記の手順 2 の接続の出力にある値 `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` に置き換えます。
   * `[FILE_SHARE_CONNECT_OPTIONS_STRING]` を上記の手順 2 の値 `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` に置き換えます。

1. **セキュリティで保護されたクラスターのみ:** 
   
   Jenkins からセキュリティで保護されたクラスター上のアプリケーションのデプロイを構成するには、Jenkins コンテナー内でクラスター証明書をアクセス可能にする必要があります。 *ApplicationManifest.xml* ファイルの **ContainerHostPolicies** タグの下にこの証明書参照を追加し、拇印の値をクラスター証明書の値に更新します。

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   さらに、*ApplicationManifest.xml* ファイルの **ApplicationManifest** (ルート) タグの下に次の行を追加し、拇印の値をクラスター証明書の拇印の値で更新します。

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. クラスターに接続し、コンテナー アプリケーションをインストールします。

   **セキュリティで保護されたクラスター**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   上記のコマンドは、PEM 形式で証明書を受け取ります。 証明書が PFX 形式の場合は、次のコマンドを使用して変換できます。 PFX ファイルがパスワードで保護されていない場合は、**passin** パラメーターを `-passin pass:` に指定します。
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **セキュリティで保護されていないクラスター**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   これでクラスターに Jenkins コンテナーがインストールされます。このコンテナーは Service Fabric Explorer を使用して監視できます。

   > [!NOTE]
   > Jenkins イメージがクラスターにダウンロードされるまでに数分かかる場合があります。
   >

1. ブラウザーで `http://PublicIPorFQDN:8081` に移動します。 サインインに必要な最初の管理者パスワードのパスが提供されます。 
1. Jenkins コンテナーで実行されているノードは、Service Fabric Explorer で確認します。 このノードに Secure Shell (SSH) でサインインします。
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. `docker ps -a` を使用してコンテナーのインスタンス ID を取得します。
1. Secure Shell (SSH) でコンテナーにサインインして、Jenkins ポータルに表示されたパスを貼り付けます。 たとえば、ポータルに表示されたパスが `PATH_TO_INITIAL_ADMIN_PASSWORD` の場合、次のコマンドを実行します。

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Jenkins の最初のページで、[Select plugins to install (インストールするプラグインの選択)] オプションを選択し、 **[None (なし)]** チェックボックスを選択して、[Install (インストール)] をクリックします。
1. ユーザーを作成するか、管理者として続行します。

Jenkins をセットアップした後は、「[Jenkins ジョブの作成と構成](#create-and-configure-a-jenkins-job)」に進んでください。  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Service Fabric クラスター外での Jenkins のセットアップ

Jenkins は、Service Fabric クラスターの内部か外部でセットアップできます。 次のセクションでは、クラスターの外部でセットアップする方法について説明します。

### <a name="prerequisites"></a>前提条件
- Docker がコンピューターにインストールされていることを確認します。 ターミナルからの Docker のインストールには次のコマンドを使用できます。

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  ターミナルで `docker info` を実行すると、Docker サービスが実行されていることが出力に表示されます。

### <a name="steps"></a>手順
1. Service Fabric Jenkins コンテナー イメージを取得します`docker pull rapatchi/jenkins:latest`。 このイメージは、プリインストールされた Service Fabric Jenkins プラグインから取得されます。
1. コンテナー イメージを実行します。`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. コンテナー イメージ インスタンスの ID を取得します。 `docker ps –a` コマンドを使用して、すべての Docker コンテナーの一覧を取得できます。
1. 次の手順を使用して Jenkins ポータルにサインインします。

   1. ホストから Jenkins シェルにサインインします。 コンテナー ID の最初の 4 桁の数字を使用します。 たとえば、コンテナー ID が `2d24a73b5964` の場合は、`2d24` を使用します。

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Jenkins シェルから、コンテナー インスタンスの管理者パスワードを取得します。

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Jenkins ダッシュボードにサインインするには、Web ブラウザーで次の URL を開きます: `http://<HOST-IP>:8080`。 前の手順のパスワードを使用して、Jenkins のロックを解除します。
   1. (省略可能)初めてサインインした後は、独自のユーザー アカウントを作成して以降の手順で使用するか、管理者アカウントを使い続けることができます。 ユーザーを作成した場合は、そのユーザーを使い続ける必要があります。
1. 「[Generating a new SSH key and adding it to the SSH agent (新しい SSH キーの生成と SSH エージェントへの追加)](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)」で説明されている手順を使用して、Jenkins を利用できるように GitHub をセットアップします。
   * GitHub によって提供されている手順を使用して、SSH キーを生成し、リポジトリをホストする GitHub アカウントにその SSH キーを追加します。
   * (ホストではなく) Jenkins Docker シェルで、前のリンク先に記載されているコマンドを実行します。
   * ホストから Jenkins シェルにサインインするには、次のコマンドを使用します。

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Jenkins コンテナー イメージがホストされているクラスターまたはコンピューターに公開 IP アドレスがあることを確認します。 この公開 IP を使用することで、Jenkins インスタンスは GitHub から通知を受け取ることができます。

Jenkins をセットアップした後は、次のセクション、「[Jenkins ジョブの作成と構成](#create-and-configure-a-jenkins-job)」に進んでください。

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins ジョブの作成と構成

このセクションの手順は、GitHub リポジトリ内の変更に対応し、変更内容を取り込んでビルドするように Jenkins ジョブを構成する方法を示しています。 このセクションの最後には、開発/テスト環境または運用環境のどちらにデプロイするかに応じてアプリケーションをデプロイするようにジョブを構成する、最後の手順が指示されます。 

1. Jenkins ダッシュボードで **[New Item (新しい項目)]** をクリックします。
1. 項目の名前を入力します (例: **MyJob**)。 **フリースタイル プロジェクト**を選択し、 **[OK]** をクリックします。
1. ジョブを構成するページが開きます。 (Jenkins ダッシュボードから構成を始めるには、ジョブをクリックしてから、 **[構成]** をクリックします)。

1. **[全般]** タブで **[GitHub プロジェクト]** のチェック ボックスをオンにし、GitHub プロジェクトの URL を指定します。 この URL では、Jenkins の継続的インテグレーション/継続的デプロイ (CI/CD) フローと統合する Service Fabric Java アプリケーションがホストされます (例: `https://github.com/{your-github-account}/service-fabric-java-getting-started`)。

1. **[Source Code Management (ソース コードの管理)]** タブで **[Git]** を選択します。 Jenkins CI/CD フローと統合する Service Fabric Java アプリケーションをホストするリポジトリの URL を指定します (例: `https://github.com/{your-github-account}/service-fabric-java-getting-started`)。 ビルドする分岐を指定することもできます (例: `/master`)。
1. Jenkins と対話するように *GitHub* リポジトリを構成します。

   a. GitHub リポジトリのページで **[Settings (設定)]**  >  **[Integrations and Services (統合とサービス)]** の順に移動します。

   b. **[Add Service (サービスの追加)]** を選択して「**Jenkins**」と入力し、 **[Jenkins-GitHub plugin (Jenkins-GitHub プラグイン)]** を選択します。

   c. Jenkins webhook の URL を入力します (既定では、`http://<PublicIPorFQDN>:8081/github-webhook/` です)。 **[Add service (サービスの追加)] または [Update service (サービスの更新)]** をクリックします。

   d. テスト イベントが Jenkins インスタンスに送信されます。 GitHub で webhook の側に緑色のチェック マークが表示され、プロジェクトがビルドされます。

1. Jenkins の **[Build Triggers (ビルド トリガー)]** タブで、目的のビルド オプションを選択します。 この例では、リポジトリへのプッシュが行われるたびにビルドがトリガーされるようにします。そのため、 **[GitHub hook trigger for GITScm polling (GITScm ポーリングの GitHub フック トリガー)]** を選択します。 (以前、このオプションの名前は **[Build when a change is pushed to GitHub (変更が GitHub にプッシュされたときにビルド)]** でした)。
1. **[ビルド]** タブで、Java アプリケーションまたは .NET Core アプリケーションのどちらをビルドするかに応じて、次のいずれかを実行します。

   * **Java アプリケーションの場合:** **[ビルド ステップの追加]** ドロップダウンから **[Gradle スクリプトの呼び出し]** を選択します。 **[詳細設定]** をクリックします。 詳細メニューで、 **[Root build script (ルート ビルド スクリプト)]** にアプリケーションのパスを指定します。 ウィジェットは、指定されたパスから build.gradle を取得し、適切に動作します。 [ActorCounter アプリケーション](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)の場合、これは `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter` です。

     ![Service Fabric Jenkins のビルド アクション][build-step]

   * **.NET Core アプリケーションの場合:** **[ビルド ステップの追加]** ドロップダウンから **[シェルの実行]** を選択します。 表示されるコマンド ボックスで、ディレクトリを、build.sh ファイルが配置されているパスに最初に変更する必要があります。 ディレクトリが変更されると、build.sh スクリプトが実行され、アプリケーションがビルドされます。

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     次のスクリーンショットは、Jenkins ジョブの名前が CounterServiceApplication の、[カウンター サービス](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) サンプルを作成するときに使用されるコマンド例を示しています。

      ![Service Fabric Jenkins のビルド アクション][build-step-dotnet]

1. ビルド後のアクションでアプリを Service Fabric クラスターにデプロイするように Jenkins を構成するには、Jenkins コンテナー内のそのクラスターの証明書の位置が必要です。 Jenkins コンテナーをクラスター内またはクラスター外のどちらで実行するかに応じて、次のいずれかを選択し、クラスター証明書の位置を書き留めます。

   * **クラスター内で実行される Jenkins の場合:** 証明書へのパスは、コンテナー内から *Certificates_JenkinsOnSF_Code_MyCert_PEM* 環境変数の値をエコーすることによって確認できます。

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **クラスター外で実行される Jenkins の場合:** 次の手順に従って、クラスター証明書をコンテナーにコピーします。
     1. 証明書は、PEM 形式でなければなりません。 PEM ファイルがない場合は、証明書の PFX ファイルから作成できます。 PFX ファイルがパスワードで保護されていない場合は、ホストから次のコマンドを実行します。

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        PFX ファイルがパスワードで保護されている場合は、`-passin` パラメーターにパスワードを含めます。 例:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Jenkins コンテナーのコンテナーの ID を取得するには、ホストから `docker ps` を実行します。
     1. 次の Docker コマンドを使用して、PEM ファイルをコンテナーにコピーします。
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

あともう少しで終了です。 Jenkins ジョブは開いたままにください。 残りのタスクは、アプリケーションを Service Fabric クラスターにデプロイするようにビルド後のステップを構成することだけです。

* 開発またはテスト環境にデプロイするには、[「クラスター管理エンドポイントを使用してデプロイを構成する](#configure-deployment-using-cluster-management-endpoint)」の手順に従います。
* 運用環境にデプロイするには、[「Azure 資格情報を使用してデプロイを構成する](#configure-deployment-using-azure-credentials)」の手順に従います。

## <a name="configure-deployment-using-cluster-management-endpoint"></a>クラスター管理エンドポイントを使用してデプロイを構成する
開発およびテスト環境では、クラスター管理エンドポイントを使用してアプリケーションをデプロイすることができます。 クラスター管理エンドポイントでアプリケーションをデプロイするようにビルド後のアクションを構成するには、最小限のセットアップが必要です。 運用環境にデプロイする場合は、「[Azure 資格情報を使用してデプロイを構成する](#configure-deployment-using-azure-credentials)」に進んで、デプロイ時に使用する、Azure Active Directory サービス プリンシパルを構成します。    

1. Jenkins ジョブで **[Post-build Actions (ビルド後のアクション)]** タブをクリックします。 
1. **[Post-Build Actions (ビルド後のアクション)]** ボックスの一覧の **[Deploy Service Fabric Project (Service Fabric プロジェクトのデプロイ)]** を選択します。 
1. **Service Fabric Cluster Configuration (Service Fabric クラスター構成)** の下にある **Fill the Service Fabric Management Endpoint (Service Fabric 管理エンドポイントの設定)** ラジオ ボタンを選択します。
1. **[Management Host (管理ホスト)]** には、クラスターの接続エンドポイント (例: `{your-cluster}.eastus.cloudapp.azure.com`) を入力します。
1. **[Client Key (クライアント キー)]** と **[Client Cert (クライアント証明書)]** には、Jenkins コンテナー内 PEM ファイルの位置 (例: `/var/jenkins_home/clustercert.pem`) を入力します。 (「[Jenkins ジョブの作成と構成](#create-and-configure-a-jenkins-job)」の最後の手順でコピーした証明書の位置です。)
1. **[Application Configuration (アプリケーションの構成)]** で、 **[アプリケーション名]** 、 **[アプリケーションの種類]** 、および **[Path to Application Manifest (アプリケーション マニフェストへのパス)]** (相対) の各フィールドを設定します。

   ![Service Fabric の Jenkins ビルド後のアクションでの管理エンドポイントの構成](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. **[Verify Configuration]** をクリックします。 検証が正常に行われたら、 **[保存]** をクリックします。 これで Jenkins ジョブ パイプラインが完全に構成されました。 [次のステップ](#next-steps)に進んで、デプロイをテストしてください。

## <a name="configure-deployment-using-azure-credentials"></a>Azure 資格情報を使用してデプロイを構成する
運用環境の場合は、アプリケーションをデプロイするように Azure 資格情報を構成することを強くお勧めします。 このセクションでは、ビルド後のアクションでアプリケーションをデプロイするために使用するように、Azure Active Directory サービス プリンシパルを構成する方法を示します。 ディレクトリのロールにサービス プリンシパルを割り当てて、Jenkins ジョブの権限を制限することができます。 

開発およびテスト環境の場合は、アプリケーションをデプロイするように、Azure 資格情報またはクラスター管理エンドポイントのいずれかを構成することができます。 クラスター管理エンドポイントの構成方法の詳細については、「[クラスター管理エンドポイントを使用してデプロイを構成する](#configure-deployment-using-cluster-management-endpoint)」をご覧ください。   

1. Azure Active Directory サービス プリンシパルを作成し、Azure サブスクリプションで権限を割り当てるには、「[Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)」の手順に従ってください。 次の点に注意してください。

   * トピック内の手順を実行しているときに、必ず次の値をコピーして保存してください:*アプリケーション ID*、*アプリケーション キー*、*ディレクトリ ID (テナント ID)* 、および*サブスクリプション ID*。 それらは Jenkins で Azure 資格情報を構成するために必要です。
   * ディレクトリに[必要な権限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)がない場合は、管理者に依頼して、権限を付与するかサービス プリンシパルを作成してもらう必要があります。または、Jenkins のジョブ用に **[Post-Build Actions (ビルド後のアクション)]** でクラスターの管理エンドポイントを構成する必要があります。
   * 「[Azure Active Directory アプリケーションを作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application)」のセクションでは、 **[サインオン URL]** に適切な形式の URL を入力することができます。
   * 「[アプリケーションをロールに割り当てる](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)」のセクションでは、アプリケーションにクラスターのリソース グループの*閲覧者*ロールを割り当てることができます。

1. Jenkins ジョブに戻って、 **[Post-build Actions (ビルド後のアクション)]** タブをクリックします。
1. **[Post-Build Actions (ビルド後のアクション)]** ボックスの一覧の **[Deploy Service Fabric Project (Service Fabric プロジェクトのデプロイ)]** を選択します。 
1. **[Service Fabric Cluster Configuration (Service Fabric クラスター構成)]** の下にある **[Select the Service Fabric Cluster (Service Fabric クラスターの選択)]** ラジオ ボタンを選択します。 **[Azure Credentials (Azure 資格情報)]** の横の **[追加]** をクリックします。 **[Jenkins]** をクリックして、Jenkins 資格情報プロバイダーを選択します。
1. Jenkins 資格情報プロバイダーで、 **[種類]** ドロップダウンから **[Microsoft Azure サービス プリンシパル]** を選択します。
1. 手順 1 でサービス プリンシパルを設定するときに保存した値を使用して、次のフィールドを設定します。

   * **クライアント ID**:*アプリケーション ID*
   * **クライアント シークレット**:*アプリケーション キー*
   * **テナント ID**:*ディレクトリ ID*
   * **サブスクリプション ID**:*サブスクリプション ID*
1. Jenkins で資格情報を選択するために使用するわかりやすい **ID** と、簡単な **[説明]** を入力します。 次に、 **[Verify Service Principal (サービス プリンシパルの検証)]** をクリックします。 検証が成功したら、 **[追加]** をクリックします。

   ![Service Fabric の Jenkins での Azure 資格情報の入力](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. **[Service Fabric Cluster Configuration (Service Fabric クラスター構成)]** に戻って、 **[Azure Credentials (Azure 資格情報)]** に新しい資格情報が選択されていることを確認します。 
1. **[リソース グループ]** ドロップダウンで、アプリケーションをデプロイするクラスターのリソース グループを選択します。
1. **[Service Fabric]** ドロップダウンで、アプリケーションをデプロイするクラスターを選択します。
1. **[Client Key (クライアント キー)]** と **[Client Cert (クライアント証明書)]** には、Jenkins コンテナー内 PEM ファイルの位置を入力します。 たとえば、「 `/var/jenkins_home/clustercert.pem` 」のように指定します。 
1. **[Application Configuration (アプリケーションの構成)]** で、 **[アプリケーション名]** 、 **[アプリケーションの種類]** 、および **[Path to Application Manifest (アプリケーション マニフェストへのパス)]** (相対) の各フィールドを設定します。
    ![Service Fabric の Jenkins のビルド後のアクションでの Azure 資格情報の構成](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. **[Verify Configuration]** をクリックします。 検証が正常に行われたら、 **[保存]** をクリックします。 これで Jenkins ジョブ パイプラインが完全に構成されました。 [次のステップ](#next-steps)に進んで、デプロイをテストしてください。

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins プラグインのトラブルシューティング

Jenkins プラグインでバグが発生した場合は、[Jenkins JIRA](https://issues.jenkins-ci.org/) で特定のコンポーネントについて問題を報告してください。

## <a name="next-steps"></a>次の手順
GitHub と Jenkins の構成が完了しました。 リポジトリ (https://github.com/Azure-Samples/service-fabric-java-getting-started ) のフォークの `reliable-services-actor-sample/Actors/ActorCounter` プロジェクトにサンプルの変更を加えてみましょう。 リモートの `master` 分岐 (または動作するように構成したいずれかの分岐) に変更をプッシュすると、 構成した Jenkins ジョブ `MyJob` がトリガーされます。 GitHub から変更内容がフェッチされてビルドが行われ、ビルド後のアクションで指定したクラスターにアプリケーションがデプロイされます。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

