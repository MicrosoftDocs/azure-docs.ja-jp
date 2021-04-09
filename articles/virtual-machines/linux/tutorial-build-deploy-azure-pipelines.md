---
title: チュートリアル - Azure Pipelines を使用した Azure VM への CI/CD
description: このチュートリアルでは、YAML ベースの Azure パイプラインを使用して、Azure VM に対する Node.js アプリの継続的インテグレーション (CI) と継続的配置 (CD) を設定する方法について説明します。
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552492"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>チュートリアル:Azure DevOps Services と Azure Pipelines を使用して Azure の Linux 仮想マシンにアプリをデプロイする

継続的インテグレーション (CI) と継続的デプロイ (CD) では、すべてのコードのコミットの後でコードのビルド、リリース、デプロイに使用できるパイプラインが形成されます。 このドキュメントには、Azure Pipelines を使用して複数マシンのデプロイを行うための CI/CD パイプラインの設定に関連する手順が含まれています。

Azure Pipelines では、オンプレミスまたは任意のクラウド上の仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。

このチュートリアルで設定する YAML ベースの CI/CD パイプラインでは、それぞれがアプリを実行する Web サーバーとして機能する Linux 仮想マシンがリソースとして含まれる Azure Pipelines [環境](/azure/devops/pipelines/process/environments)に、アプリをデプロイします。

学習内容は次のとおりです。

> [!div class="checklist"]
> * サンプル アプリを取得する。
> * サンプル アプリをビルドするための、YAML ベースの Azure Pipelines の CI パイプラインを作成する。
> * Azure 仮想マシン用の Azure Pipelines 環境を作成する
> * Azure Pipelines の CD パイプラインを作成する。
> * 手動デプロイおよび CI によってトリガーされるデプロイを実行する。

## <a name="before-you-begin"></a>開始する前に

* Azure DevOps Services 組織 ( **https://dev.azure.com/** ) にサインインします。 
  [無料の Azure DevOps Services 組織](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)を取得できます。

  > [!NOTE]
  > 詳細については、[Azure DevOps Services への接続](/azure/devops/organizations/projects/connect-to-projects)に関する記事をご覧ください。

*  デプロイ ターゲットの Linux 仮想マシンが必要です。  詳細については、「[Azure CLI を使用した Linux VM の作成と管理](./tutorial-manage-vm.md)」をご覧ください。

*  仮想マシンの受信ポート 80 を開きます。 詳細については、「[Azure Portal を使用したネットワーク セキュリティ グループの作成](../../virtual-network/tutorial-filter-network-traffic.md)」をご覧ください。

## <a name="get-your-sample-app-code"></a>サンプル アプリのコードを入手する

デプロイするアプリが GitHub に既にある場合は、そのコード用のパイプラインを作成してみてかまいません。

ただし、新しいユーザーの場合は、サンプル コードを使用すると、より適切に開始できます。 その場合は、GitHub の次のリポジトリをフォークします。

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic は、[Maven](https://spring.io/guides/gs/maven/) を使用してビルドされた [Java Spring Boot](https://spring.io/guides/gs/spring-boot) アプリケーションです。

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> この Node.js アプリは、[Yeoman](https://yeoman.io/learning/index.html) でビルドされました。 Express、bower、および Grunt を使用します。 また、依存関係として複数の npm パッケージを保持しています。
> このサンプルには、Nginx を設定し、アプリを配置するスクリプトも含まれています。 これは仮想マシンで実行します。 スクリプトによって、具体的には次の処理が行われます。
> 1. Node、Nginx、および PM2 をインストールします。
> 2. Nginx と PM2 を構成します。
> 3. Node アプリを開始します。

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Linux VM の前提条件

前述したサンプル アプリは、Ubuntu 16.04 でテストされています。このクイックスタートでは、同じバージョンの Linux VM を使用することをお勧めします。
アプリに使用されるランタイム スタックに基づいて、以下で説明する追加手順のようにします。

#### <a name="java"></a>[Java](#tab/java)

- Java Spring Boot および Spring Cloud に基づくアプリをデプロイする場合は、[こちら](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004)のテンプレートを使用して、Azure に Linux VM を作成します。このテンプレートでは、完全にサポートされている OpenJDK ベースのランタイムが提供されます。
- Tomcat サーバーに Java サーブレットをデプロイする場合は、[こちら](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004)の Azure テンプレートを使用して Java 8 で Linux VM を作成し、[サービスとして Tomcat 9.x を構成](https://tomcat.apache.org/tomcat-9.0-doc/setup.html)します。
- Java EE ベースのアプリをデプロイする場合は、Azure テンプレートを使用して、[Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90)、[Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin)、または [Linux VM + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 を作成します 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

JavaScript アプリまたは Node.js アプリをインストールするには、アプリをデプロイするための Nginx Web サーバーを含む Linux VM が必要です。
Nginx を含む Linux VM がまだない場合は、[こちらの例](./quick-create-cli.md)の手順を使用してここで Azure に作成します。

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Azure 仮想マシンで Azure Pipelines 環境を作成する

仮想マシンを[環境](/azure/devops/pipelines/process/environments)内のリソースとして追加し、複数マシンのデプロイのターゲットにすることができます。 環境内のデプロイ履歴ビューを使うと、VM からパイプラインに、そこからさらにコミットまで、追跡できます。

**[パイプライン]** セクション内の **[環境]** ハブで環境を作成できます。
1.  Azure DevOps 組織にサインインし、プロジェクトに移動します。
2.  プロジェクト内で、 **[パイプライン]** ページに移動します。 次に **[環境]** を選択し、 **[環境の作成]** をクリックします。 環境の **[名前]** (必須) と、 **[説明]** を指定します。
3.  環境に追加する **[リソース]** として **[仮想マシン]** を選択し、 **[次へ]** をクリックします。
4.  オペレーティング システム (Windows/Linux) を選択し、**PS 登録スクリプトをコピー** します。 
5.  次に、この環境に登録する各ターゲット VM で、管理者の PowerShell コマンド プロンプトから、コピーしたスクリプトを実行します。
    > [!NOTE]
    > - ログインしているユーザーの個人用アクセス トークンが、スクリプトに事前に挿入されています。このトークンは、その日に有効期限が切れ、コピーしたスクリプトを使用できなくなります。
    > - お使いの VM でエージェントが既に実行されている場合は、環境に登録するため、"エージェント" に一意の名前を指定します。
6.  VM の登録が済むと、環境の [リソース] タブに環境リソースとして表示されるようになります。

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  さらに VM を追加する場合は、[リソースの追加] をクリックし、リソースとして [仮想マシン] を選択することで、スクリプトを再び表示してコピーできます。 このスクリプトは、この環境に追加するすべての VM に対して同じままです。 
8.  各マシンは Azure Pipelines と対話して、アプリのデプロイを調整します。

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. 対話型の PowerShell 登録スクリプトの一部として、VM にタグを追加できます。または、リソース ビューの各 VM リソースの最後にある 3 つのドットをクリックして、リソース ビューからタグを追加または削除することもできます。

   割り当てたタグを使用すると、環境がデプロイ ジョブで使用されるときに、特定の仮想マシンにデプロイを制限できます。 各タグは 256 文字以下に制限されていますが、使用できるタグの数に制限はありません。

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>CI ビルド パイプラインを定義する

Web アプリケーションを発行する継続的インテグレーション (CI) ビルド パイプラインと、Ubuntu サーバーでローカルに実行できるデプロイ スクリプトが必要です。 使用するランタイムに基づいて、CI ビルド パイプラインを設定します。 

1. Azure DevOps 組織にサインインし、プロジェクトに移動します。

1. プロジェクト内で、 **[パイプライン]** ページに移動します。 次に、新しいパイプラインを作成するためのアクションを選択します。

1. 最初に、ソース コードの場所として **GitHub** を選択し、ウィザードの手順を実行します。

1. サインインするために GitHub にリダイレクトされる場合があります。 その場合は、GitHub の資格情報を入力します。

1. リポジトリの一覧が表示されたら、目的のサンプル アプリのリポジトリを選択します。

1. Azure Pipelines によってリポジトリが分析され、適切なパイプライン テンプレートが推奨されます。

#### <a name="java"></a>[Java](#tab/java)

**starter** テンプレートを選択し、Java プロジェクトをビルドして Apache Maven でテストを実行する次の YAML スニペットをコピーします。

```YAML
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

詳しくは、[Maven での Java アプリのビルド](/azure/devops/pipelines/ecosystems/java)に関する記事に記載されている手順に従ってください。

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

**starter** テンプレートを選択し、npm で一般的な Node.js プロジェクトをビルドする次の YAML スニペットをコピーします。

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

詳しくは、[gulp での Node.js アプリのビルド](/azure/devops/pipelines/ecosystems/javascript)に関する記事の手順に従ってください。

- パイプラインを調べて、何が行われているかを確認します。 すべての既定の入力が自分のコードに適していることを確認します。

- **[保存および実行]** を選択し、 **[Commit directly to the main branch]\(メイン ブランチに直接コミットする\)** を選択した後、 **[保存および実行]** を再度選択します。

- 新しい実行が開始されます。 実行が終了するまで待ちます。

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Linux VM にデプロイするための CD 手順を定義する

1. 上記のパイプラインの YAML ファイルを編集し、次の YAML 構文を使用して、前の環境と VM リソースを参照することにより、[デプロイ ジョブ](/azure/devops/pipelines/process/deployment-jobs)を組み込みます。

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. 環境内の各仮想マシンに対して定義した **タグ** を指定することにより、デプロイを受け取る特定の仮想マシンのセットを環境から選択できます。
デプロイ ジョブの完全な YAML スキーマについては、[こちら](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job)をご覧ください。

3. デプロイ方法として、`runOnce` または `rolling` を指定できます。 

   `runOnce` は最も簡単なデプロイ方法であり、すべてのライフ サイクル フック (つまり、`preDeploy` `deploy`、`routeTraffic`、`postRouteTraffic`) が 1 回実行されます。 その後、`on:` `success` または `on:` `failure` が実行されます。

   `runOnce` の YAML スニペットの例を次に示します。
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. 次に示す YAML スニペットの例を使用すると、各反復で最大 5 つのターゲット仮想マシンを更新するローリング戦略を定義できます。 `maxParallel` によって、同時にデプロイできるターゲットの数が決まります。 選択するときは、デプロイされているターゲットを除いて、常に使用可能な状態にしておく必要があるターゲットの絶対数または割合を考慮します。 それはまた、デプロイの間に成功と失敗の条件を判断するためにも使用されます。

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   このジョブを実行するたびに、VM を作成して登録した `<environment name>` 環境に対してデプロイ履歴が記録されます。

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>パイプラインを実行し、環境の追跡可能性ビューを取得する
環境のデプロイ ビューでは、コミットと作業項目の完全な追跡、および環境とリソースごとのクロスパイプライン デプロイ履歴が提供されます。

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>次のステップ
- 作成した[パイプラインのカスタマイズ](/azure/devops/pipelines/customize-pipeline)に進むことができます。
- YAML パイプラインで他にできることについては、[YAML スキーマのリファレンス](/azure/devops/pipelines/yaml-schema)を参照してください。
- LAMP (Linux、Apache、MySQL、および PHP) スタックをデプロイする方法の詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [LAMP スタックのデプロイ](tutorial-lamp-stack.md)
