---
title: チュートリアル - Azure DevOps Services を使用して Jenkins から Azure VM への CI/CD を設定する | Microsoft Docs
description: このチュートリアルでは、Visual Studio Team Services または Microsoft Team Foundation Server の Release Management から Jenkins を使用して、Azure VM に対する Node.js アプリの継続的インテグレーション (CI) と継続的配置 (CD) を設定する方法を説明します
author: tomarchermsft
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: jenkins
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 7cd7b8f7b49915db9fcf17602429e47c1b9da95d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901425"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>チュートリアル:Jenkins と Azure DevOps Services を使用して Azure の Linux 仮想マシンにアプリをデプロイする

継続的インテグレーション (CI) と継続的デプロイ (CD) は、コードのビルド、リリース、デプロイに使用できるパイプラインを生成します。 Azure DevOps Services には、Azure へのデプロイに使用できる機能が一式そろった CI/CD 自動化ツールが用意されています。 Jenkins は、よく使われているサードパーティの CI/CD サーバーベースのツールであり、CI/CD 自動化機能も備えています。 Azure DevOps Services と Jenkins を一緒に使用して、クラウド アプリまたはサービスの提供方法をカスタマイズできます。

このチュートリアルでは、Jenkins を使用して Node.js Web アプリをビルドします。 次に、Azure DevOps を使用して、

Linux 仮想マシン (VM) を含む[配置グループ](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts)にアプリをデプロイします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * サンプル アプリを入手する。
> * Jenkins プラグインを構成する。
> * Node.js の Jenkins フリースタイル プロジェクトを構成する。
> * Azure DevOps Services との統合のために Jenkins を構成する。
> * Jenkins サービス エンドポイントを作成する。
> * Azure 仮想マシンのデプロイ グループを作成する。
> * Azure Pipelines のリリース パイプラインを作成する。
> * 手動デプロイおよび CI によってトリガーされるデプロイを実行する。

## <a name="before-you-begin"></a>開始する前に

* Jenkins サーバーにアクセスする必要があります。 Jenkins サーバーをまだ作成していない場合は、[Azure 仮想マシンへの Jenkins マスターの作成](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)に関するページをご覧ください。 

* Azure DevOps Services 組織 (**https://{yourorganization}.visualstudio.com**) にサインインします。 
  [無料の Azure DevOps Services 組織](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)を取得できます。

  > [!NOTE]
  > 詳細については、[Azure DevOps Services への接続](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts)に関する記事をご覧ください。

*  デプロイ ターゲットの Linux 仮想マシンが必要です。  詳細については、「[Azure CLI を使用した Linux VM の作成と管理](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)」をご覧ください。

*  仮想マシンの受信ポート 80 を開きます。 詳細については、「[Azure Portal を使用したネットワーク セキュリティ グループの作成](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)」をご覧ください。

## <a name="get-the-sample-app"></a>サンプル アプリを入手する

デプロイするアプリを Git リポジトリに格納する必要があります。
このチュートリアルでは、[GitHub で入手できるこのサンプル アプリ](https://github.com/azooinmyluggage/fabrikam-node)を使用することをお勧めします。 このチュートリアルには、Node.js のインストールに使用するサンプル スクリプトとアプリケーションが含まれています。 独自のリポジトリで作業する場合は、同様のサンプルを構成する必要があります。

このアプリのフォークを作成し、このチュートリアルの後の手順で使用できるように場所 (URL) のメモを取ります。 詳細については、「[Fork a repo (リポジトリのフォーク)](https://help.github.com/articles/fork-a-repo/)」を参照してください。    

> [!NOTE]
> アプリは [Yeoman](https://yeoman.io/learning/index.html) でビルドされました。 Express、bower、および Grunt を使用します。 また、依存関係として複数の npm パッケージを保持しています。
> このサンプルには、Nginx を設定し、アプリを配置するスクリプトも含まれています。 これは仮想マシンで実行します。 スクリプトによって、具体的には次の処理が行われます。
> 1. Node、Nginx、および PM2 をインストールします。
> 2. Nginx と PM2 を構成します。
> 3. Node アプリを開始します。

## <a name="configure-jenkins-plug-ins"></a>Jenkins プラグインを構成する

最初に、2 つの Jenkins プラグインを構成する必要があります。**NodeJS** と **VS Team Services Continuous Deployment** です。

1. Jenkins アカウントを開き、**[Jenkins の管理]** を選択します。
2. **[Jenkins の管理]** ページで、**[プラグインの管理]** を選択します。
3. 一覧を絞り込んで **[NodeJS]** プラグインを探し、**[Install without restart] \(再起動せずにインストール)** オプションを選択します。
    ![NodeJS プラグインを Jenkins に追加する](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. 一覧を絞り込んで **VS Team Services Continuous Deployment** プラグインを探し、**Install without restart \(再起動せずにインストール)** オプションを選択します。
5. Jenkins ダッシュボードに戻り、**[Jenkins の管理]** を選択します。
6. **[Global Tool Configuration]\(ツールのグローバル構成)** を選択します。 **[NodeJS]** を検索して **[NodeJS installations] \(NodeJS のインストール)** を選択します。
7. **[Install automatically] \(自動的にインストールする)** オプションを選択して、**[名前]** の値を入力します。
8. **[保存]** を選択します。

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Node.js の Jenkins フリースタイル プロジェクトを構成する

1. **[New Item (新しい項目)]** を選択します。 項目名を入力します。
2. **[Freestyle project] \(フリースタイル プロジェクト)** を選択します。 **[OK]** を選択します。
3. **[Source Code Management] \(ソース コードの管理)** タブで **[Git]** を選択し、アプリ コードを保存するリポジトリとブランチの詳細情報を入力します。    
    ![ビルドにリポジトリを追加する](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. **[Build Triggers] \(ビルド トリガー)** タブで **[Poll SCM] \(SCM のポーリング)** を選択し、スケジュール「`H/03 * * * *`」を入力して Git リポジトリの変更を 3 分間隔でポーリングします。 
5. **[Build Environment] \(ビルド環境)** タブで **[Provide Node &amp; npm bin/ folder PATH] \(Node と npm bin/ フォルダーのパスを入力)** を選択し、**[NodeJS Installation] \(Node JS のインストール)** の値を選択します。 **[npmrc file] \(npmrc ファイル)** は **[use system default] \(システムの既定値を使用する)** のままにします。
6. **[ビルド]** タブで **[Execute shell] \(シェルの実行)** を選択して `npm install` コマンドを入力し、すべての依存関係が更新されるようにします。


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Azure DevOps Services との統合のために Jenkins を構成する

> [!NOTE]
> 次の手順で使用する個人用アクセス トークン (PAT) に Azure DevOps Services の*リリース* (読み取り、書き込み、実行、管理) アクセス許可が含まれるようにします。
 
1.  Azure DevOps Services 組織で PAT を作成します (まだない場合)。 Jenkins から Azure DevOps Services 組織にアクセスするには、この情報が必要です。 必ず、このセクションの以降の手順で必要なトークンの情報を保存します。
  
    トークンの生成方法については、[Azure DevOps Services の個人用アクセス トークンを作成する方法](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts)に関する記事をご覧ください。
2. **[Post-build Actions]\(ビルド後のアクション)** タブで **[Add post-build action]\(ビルド後のアクションを追加する)** を選択します。 **[Archive the artifacts] \(成果物のアーカイブ)** を選択します。
3. **[Files to archive]\(アーカイブするファイル\)** に `**/*` と入力してすべてのファイルが含まれるようにします。
4. 別のアクションを作成するには **[Add post-build action] \(ビルド後のアクションを追加する)** をクリックします。
5. **[Trigger release in TFS/Team Services]\(TFS/Team Services でリリースをトリガーする)** を選択します。 Azure DevOps Services 組織の URI を入力します (例: **https://{your-organization-name}.visualstudio.com**)。
6. **プロジェクト**の名前を入力します。
7. リリース パイプラインの名前を選択します  (このリリース パイプラインは、後で Azure DevOps Services で作成します)。
8. Azure DevOps Services 環境または Team Foundation Server 環境に接続するための資格情報を選択します。
   - Azure DevOps Services を使用している場合は、**[ユーザー名]** を空白のままにしておきます。 
   - オンプレミス版の Team Foundation Server を使用している場合は、ユーザー名とパスワードを入力します。    
   ![Jenkins のビルド後のアクションを構成する](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Jenkins プロジェクトを保存します。


## <a name="create-a-jenkins-service-endpoint"></a>Jenkins サービス エンドポイントを作成する

サービス エンドポイントを使用して、Azure DevOps Services から Jenkins に接続できます。

1. Azure DevOps Services の **[サービス]** ページを開き、**[新しいサービス エンドポイント]** 一覧を開いて、**[Jenkins]** を選択します。
   ![Jenkins エンドポイントを追加する](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. 接続名を入力します。
3. Jenkins サーバーの URL を入力し、**[信頼されていない SSL 証明書を受け入れる]** オプションを選択します。 URL は、たとえば **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com** です。
4. Jenkins アカウントのユーザー名とパスワードを入力します。
5. **[接続の確認]** を選択して情報が正しいことを確認します。
6. **[OK]** を選択してサービス エンドポイントを作成します。

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Azure 仮想マシンのデプロイ グループを作成する

リリース パイプラインを仮想マシンにデプロイできるように、Azure DevOps Services エージェントを登録する[配置グループ](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)が必要です。 デプロイ グループを使用すると、デプロイ用にターゲット マシンの論理グループを定義し、各コンピューターに必要なエージェントをインストールするのが簡単になります。

   > [!NOTE]
   > 次の手順では前提条件となるものをインストールしますが、*スクリプトは sudo 権限で実行しない*ようにします。

1. **[ビルドと&amp;リリース]** ハブの **[リリース]** タブを開き、**[Deployment groups] \(デプロイ グループ)** を開いて **[+ 新規]** を選択します。
2. 配置グループの名前と、説明 (省略可能) を入力します。 **[作成]** を選択します。
3. デプロイ ターゲットの仮想マシンのオペレーティング システムを選択します。 たとえば、**[Ubuntu 16.04+]** を選択します。
4. **[認証用にスクリプト内の個人用アクセス トークンを使用する]** を選択します。
5. **[システムの前提条件]** を選択します。 お使いのオペレーティング システムの前提条件となるものをインストールします。
6. **[スクリプトをクリップボードにコピー]** を選択してスクリプトをコピーします。
7. デプロイ ターゲットの仮想マシンにログインして、スクリプトを実行します。 スクリプトは sudo 権限で実行しないでください。
8. インストール後、デプロイ グループのタグを求められます。 既定値を受け入れます。
9. Azure DevOps Services の **[配置グループ]** の **[ターゲット]** で、新たに登録された仮想マシンを確認します。

## <a name="create-an-azure-pipelines-release-pipeline"></a>Azure Pipelines のリリース パイプラインを作成する

リリース パイプラインでは、Azure Pipelines でアプリをデプロイするときに使用するプロセスを指定します。 この例ではシェル スクリプトを実行します。

Azure Pipelines でリリース パイプラインを作成するには、次の手順に従います。

1. **[ビルドとリリース]** ハブの **[リリース]** タブを開き、**[リリース パイプラインの作成]** を選択します。 
2. **[空のプロセス]** で開始することを選択して **[空]** のテンプレートを選択します。
3. **[成果物]** セクションで **[+ 成果物の追加]** を選択し、**[ソースの種類]** に **[Jenkins]** を選択します。 Jenkins サービス エンドポイントの接続を選択します。 Jenkins ソース ジョブを選択し、**[追加]** を選択します。
4. **[環境 1]** の横にある省略記号を選択します。 **[Add deployment group phase]\(デプロイ グループ フェーズを追加)** をクリックします。
5. ご利用のデプロイ グループを選択します。
5. **[+]** を選択して **[Deployment group phase]\(デプロイ グループ フェーズ)** にタスクを追加します。
6. **[シェル スクリプト]** タスクを選択して **[追加]** を選択します。 **[シェル スクリプト]** タスクは、Node.js をインストールし、アプリを起動するために各サーバーで実行されるスクリプトの構成を指定します。
8. **[スクリプト パス]** については、**$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh** を入力します。
9. **[詳細]** を選択して、**[作業ディレクトリを指定する]** を有効にします。
10. **[作業ディレクトリ]** については、**$(System.DefaultWorkingDirectory)/Fabrikam-Node** を入力します。
11. リリース パイプラインの名前を、Jenkins のビルドの **[Post-build Actions] \(ビルド後のアクション)** タブで指定した名前に変更します。 ソース アーティファクトが更新されたときに、Jenkins から新しいリリースをトリガーするには、この名前を指定する必要があります。
12. **[保存]** を選択し、**[OK]** をクリックしてリリース パイプラインを保存します。

## <a name="execute-manual-and-ci-triggered-deployments"></a>手動デプロイおよび CI によってトリガーされるデプロイを実行する

1. **[+ リリース]** を選択し、**[リリースの作成]** を選択します。
2. 強調表示されているドロップダウン リストから完了したビルドを選択し、**[キュー]** を選択します。
3. ポップアップ メッセージでリリース リンクを選択します。 例: "リリース **Release-1** が作成されました。"
4. **[ログ]** タブを開いて、リリース コンソールの出力を確認します。
5. ブラウザーで、デプロイ グループに追加したサーバーのいずれかについて URL を開きます。 たとえば、**http://{your-server-ip-address}** と入力します。
6. ソース Git リポジトリに移動し、app/views/index.jade ファイル内の **[h1]** 見出しの内容を、変更を行ったテキストで変更します。
7. 変更を確定します。
8. 数分後、Azure DevOps の **[リリース]** ページに、作成された新しいリリースが表示されます。 リリースを開き、実行された配置を確認します。 お疲れさまでした。

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins プラグインのトラブルシューティング

Jenkins プラグインでバグが発生した場合は、[Jenkins JIRA](https://issues.jenkins-ci.org/) で特定のコンポーネントについて問題を報告してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ビルドに Jenkins を、リリースに Azure DevOps Services を使用して、Azure へのアプリのデプロイを自動化しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Jenkins でアプリをビルドする。
> * Azure DevOps Services との統合のために Jenkins を構成する。
> * Azure 仮想マシンのデプロイ グループを作成する。
> * VM を構成し、アプリをデプロイするリリース パイプラインを作成する。

LAMP (Linux、Apache、MySQL、および PHP) スタックをデプロイする方法の詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [LAMP スタックのデプロイ](tutorial-lamp-stack.md)
