---
title: "Team Services を使用して Jenkins から Azure VM への CI/CD を設定する | Microsoft Docs"
description: "Visual Studio Team Services (VSTS) または Microsoft Team Foundation Server (TFS) の Release Management から Jenkins を使用して、Azure VM に対する Node.js アプリの継続的インテグレーション (CI) と継続的配置 (CD) を設定します"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a40e26a8681df31fad664e4d1df4c1513311900d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>Jenkins と Team Services を使用してアプリを Linux VM に配置する

継続的インテグレーション (CI) と継続的配置 (CD) は、コードの構築、リリース、配置に使用できるパイプラインです。 Team Services には、Azure への配置に使用できる機能が一式そろった CI/CD 自動化ツールが用意されています。 Jenkins は、よく使われているサードパーティの CI/CD サーバーベースのツールであり、CI/CD 自動化機能も備えています。 CI/CD の両方を使用して、クラウド アプリまたはサービスの提供方法をカスタマイズすることができます。

このチュートリアルでは、Jenkins を使用して **Node.js Web アプリ**を構築し、Visual Studio Team Services を使用して Linux 仮想マシンを含む[配置グループ](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)に配置します。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * Jenkins でアプリを構築する
> * Team Services との統合のために Jenkins を構成する
> * Azure 仮想マシンの配置グループを作成する
> * VM の構成とアプリの配置に使用するリリース定義を作成する

## <a name="before-you-begin"></a>開始する前に

* Jenkins アカウントにアクセスする必要があります。 まだ Jenkins サーバーを作成していない場合は、[Jenkins のドキュメント](https://jenkins.io/doc/)を参照してください。 

* Team Services アカウント (`https://{youraccount}.visualstudio.com`) にサインインします。 
  [無料の Team Services アカウント](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)を取得することができます。

  > [!NOTE]
  > 詳細については、「[Connect to Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)」(Team Services への接続) を参照してください。

* まだ Team Services アカウントで個人用アクセス トークン (PAT) を作成していない場合は作成します。 Jenkins から Team Services アカウントにアクセスするには、この情報が必要です。
  生成方法については、[Team Services と TFS の個人用アクセス トークンを作成する方法に関する記事](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)を参照してください。

## <a name="get-the-sample-app"></a>サンプル アプリを入手する

配置するアプリを Git リポジトリに格納する必要があります。
このチュートリアルでは、[GitHub で入手できるこのサンプル アプリ](https://github.com/azooinmyluggage/fabrikam-node)を使用することをお勧めします。

1. このアプリのフォークを作成し、このチュートリアルの後の手順で使用できるように場所 (URL) のメモを取ります。

1. 後で GitHub へ接続する処理を簡単にするために、このフォークを**公開**します。

> [!NOTE]
> 詳細については、「[Fork a repo](https://help.github.com/articles/fork-a-repo/)」(リポジトリをフォークする) と「[Making a private repository public](https://help.github.com/articles/making-a-private-repository-public/)」(プライベート リポジトリを公開する) を参照してください。

> [!NOTE]
> このアプリは [Yeoman](http://yeoman.io/learning/index.html) を使用して構築されています。また、**Express**、**bower**、**grunt** が使用され、依存関係としていくつかの **npm** パッケージがあります。
> このサンプル アプリには、Azure への配置用に仮想マシンを動的に作成するときに使用できる一連の [Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)が含まれています。 これらのテンプレートは、[Team Services リリース定義](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions)のタスクに使用されます。
> メイン テンプレートで、ネットワーク セキュリティ グループ、仮想マシン、仮想ネットワークを作成します。 また、パブリック IP アドレスを割り当て、受信ポート 80 を開きます。 配置を受け取る仮想マシンを配置グループが選択するときに使用されるタグも追加します。
>
> このサンプルには、Nginx を設定し、アプリを配置するスクリプトも含まれています。 このスクリプトは各仮想マシンで実行します。 このスクリプトを実行すると、具体的には Node、Nginx、PM2 がインストールされ、Nginx と PM2 が構成され、Node アプリが開始されます。

## <a name="configure-jenkins-plugins"></a>Jenkins プラグインを構成する

まず **NodeJS** 用と **Integration with Team Services** 用という 2 つの Jenkins プラグインを構成する必要があります。

1. Jenkins アカウントを開き、**[Manage Jenkins]\(Jenkins の管理\)** を選択します。

1. **[Manage Jenkins]\(Jenkins の管理\)** ページで **[Manage Plugins]\(プラグインの管理\)** をクリックします。

1. 一覧を絞り込んで **NodeJS** プラグインを探し、インストールします (再起動は不要です)。

   ![NodeJS プラグインを Jenkins に追加する](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. 一覧を絞り込んで **Team Foundation Server** プラグインを探し、インストールします (このプラグインは Team Services と Team Foundation Server の両方に使用できます)。Jenkins の再起動は不要です。

## <a name="configure-jenkins-build-for-nodejs"></a>Node.js 用に Jenkins ビルドを構成する

Jenkins で新しいビルド プロジェクトを作成し、次のように構成します。

1. **[General]\(全般\)** タブにビルド プロジェクトの名前を入力します。

1. **[Source Code Management]\(ソース コード管理\)** タブで **[Git]** を選択し、アプリ コードを保存するリポジトリとブランチの詳細情報を入力します。

   ![ビルドにリポジトリを追加する](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > 公開するリポジトリではない場合、**[Add]\(追加\)** を選択し、接続に使用する資格情報を指定します。

1. **[Build Triggers]\(ビルド トリガー\)** タブで **[Poll SCM]\(SCM のポーリング\)** を選択し、スケジュール「`H/03 * * * *`」を入力して Git リポジトリの変更を 3 分間隔でポーリングします。 

1. **[Build Environment]\(ビルド環境\)** タブで **[Provide Node &amp; npm bin/ folder PATH]\(Node と npm bin\ フォルダーのパスを入力)** を選択し、[Node JS Installation]\(Node JS のインストール\) の値に「`NodeJS`」と入力します。 **[npmrc file]\(npmrc ファイル\)** は [use system default]\(システムの既定値を使用する\) のままにします。

1. **[Build]\(ビルド\)** タブでコマンド「`npm install`」を入力し、すべての依存関係が更新されるようにします。

## <a name="configure-jenkins-for-team-services-integration"></a>Team Services との統合のために Jenkins を構成する

1. **[Post-build Actions]\(ビルド後のアクション\)** タブの **[Files to archive]\(アーカイブするファイル\)** に「`**/*`」と入力してすべてのファイルが含まれるようにします。

1. **[Trigger release in TFS/Team Services]\(TFS/Team Services のリリースをトリガー\)** に、アカウントの完全な URL (`https://your-account-name.visualstudio.com` など)、プロジェクト名、リリース定義の名前 (後の手順で作成します)、アカウントへの接続に使用する資格情報を入力します。
   作成済みのユーザー名と PAT が必要です。 

   ![Jenkins のビルド後アクションを構成する](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. ビルド プロジェクトを保存します。

## <a name="create-a-jenkins-service-endpoint"></a>Jenkins サービス エンドポイントを作成する

サービス エンドポイントを使用して Team Services から Jenkins に接続することができます。

1. Team Services の **[サービス]** ページを開き、**[新しいサービス エンドポイント]** リストを開き、**[Jenkins]** を選択します。

   ![Jenkins エンドポイントを追加する](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. この接続の参照に使用する名前を入力します。

1. Jenkins サーバーの URL を入力し、**[信頼されていない SSL 証明書を受け入れる]** オプションをオンにします。

1. Jenkins アカウントのユーザー名とパスワードを入力します。

1. **[接続の確認]** を選択して情報が正しいことを確認します。

1. **[OK]** を選択してサービス エンドポイントを作成します。

## <a name="create-a-deployment-group"></a>配置グループを作成する

仮想マシンを追加する[配置グループ](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)が必要です。

1. **[ビルドとリリース]** ハブの **[リリース]** タブを開き、**[配置グループ]** タブを開き、**[+ 新規]** を選択します。

1. 配置グループの名前と、説明 (省略可能) を入力します。
   **[作成]** を選択します。

Azure Resource Manager テンプレートを使用して [Azure リソース グループの配置] タスクを実行すると、VM が作成され、登録されます。
仮想マシンの作成と登録を手動で実行する必要はありません。

## <a name="create-a-release-definition"></a>リリース定義の作成

リリース定義には、Team Services でアプリを配置するときに実行するプロセスを指定します。
Team Services でリリース定義を作成するには:

1. **[ビルドとリリース]** ハブの **[リリース]** タブを開き、リリース定義のリストの **+** ドロップダウンを開き、**[リリース定義の作成]** を選択します。 

1. **[空]** テンプレートを選択し、**[次へ]** を選択します。

1. **[アーティファクト]** セクションの **[成果物ソースのリンク]** をクリックし、**[Jenkins]** を選択します。 Jenkins サービス エンドポイントの接続を選択します。 Jenkins ソース ジョブを選択し、**[作成]** を選択します。 

1. 新しいリリース定義で **[+ タスクの追加]** を選択し、**[Azure リソース グループの配置]** タスクを既定の環境に追加します。

1. **[+ タスクの追加]** リンクの横にあるドロップダウン矢印を選択し、配置グループ フェーズを定義に追加します。

   ![配置グループ フェーズを追加する](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. [タスク] カタログの **[ユーティリティ]** セクションを開き、**[シェル スクリプト]** タスクのインスタンスを追加します。

1. [Azure リソース グループの配置] タスクに使用されているパラメーター テンプレートで、VM の接続に使用される管理者パスワードが設定されます。
   変数 **$(adminpassword)** を使用してこのパスワードを指定します。
   
   - **[変数]** タブを開き、**[変数]** セクションに `adminpassword` という名前を入力します。

   - 管理者パスワードを入力します。

   - 値のテキストボックスの横にある "南京錠" アイコンを選択してパスワードを保護します。 

## <a name="configure-the-azure-resource-group-deployment-task"></a>[Azure リソース グループの配置] タスクを構成する

**[Azure リソース グループの配置]** タスクは、配置グループの作成に使用されます。 SAP コネクタを次のように構成します。

* **[Azure サブスクリプション]**: **[利用可能な Azure サービス接続]** のリストから接続を選択します。 
  接続が表示されない場合は **[管理]**、**[新しいサービス エンドポイント]**、**[Azure Resource Manager]** の順に選択して指示に従います。
  リリース定義に戻り、**[AzureRM サブスクリプション]** リストを更新し、作成した接続を選択します。

* **[リソース グループ]**: 前の手順で作成したリソース グループの名前を入力します。

* **[場所]**: 配置の地域を選択します。

  ![新しいリソース グループを作成する](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **[テンプレートの場所]**: `URL of the file`

* **[テンプレートのリンク]**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **[テンプレート パラメーターのリンク]**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **[テンプレート パラメーターの無視]**: オーバーライド値の一覧。次に例を示します。`-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`<br />{プレースホルダー} には実際の値を挿入してください。 

* **[前提条件を有効にする]**: `Configure with Deployment Group agent`

* **[TFS/VSTS エンドポイント]**: **[追加]** を選択し、[新しい Team Foundation Server/Team Services Connection の追加] ダイアログで **[トークン ベースの認証]** を選択します。 チーム プロジェクトの接続名と URL を入力します。 チーム プロジェクトへの接続を認証するために使用する [[個人用アクセス トークン]]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) \(PAT\) を生成して入力します。

  ![個人用アクセス トークンを作成する](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **[チーム プロジェクト]**: 現在のプロジェクトを選択します。

* **[配置グループ]**: **[リソース グループ]** パラメーターに使用した名前と同じ配置グループ名を入力します。

[Azure リソース グループの配置] タスクの既定の設定では、リソースが作成または更新されます。作成と更新は段階的に行われます。 このタスクを初めて実行すると VM が作成され、2 回目の実行からは単に更新されます。

## <a name="configure-the-shell-script-task"></a>[シェル スクリプト] タスクを構成する

**[シェル スクリプト]** タスクは、Node.js をインストールし、アプリを起動するために各サーバーで実行されるスクリプトの構成を指定するために使用されます。 SAP コネクタを次のように構成します。

* **[スクリプト パス]**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **[作業ディレクトリを指定する]**: `Checked`

* **[作業ディレクトリ]**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>リリース定義の名前を変更して保存する

1. リリース定義の名前を、Jenkins のビルドの **[Post-build Actions]\(ビルド後のアクション\)** タブで指定した名前に変更します。 ソース アーティファクトが更新されたときに、Jenkins から新しいリリースをトリガーするには、この名前を指定する必要があります。

1. 必要に応じて環境の名前を変更するには、名前をクリックします。 

1. **[保存]** を選択し、**[OK]** を選択します。

## <a name="start-a-manual-deployment"></a>手動の配置を開始する

1. **[+ リリース]** を選択し、**[リリースの作成]** を選択します。

1. 強調表示されているドロップダウン リストから完了したビルドを選択し、**[作成]** を選択します。

1. ポップアップ メッセージでリリース リンクを選択します。 たとえば、"リリース **Release-1** が作成されました" と表示されます。

1. **[ログ]** タブを開いて、リリース コンソールの出力を確認します。

1. ブラウザーで、配置グループに追加したサーバーのいずれかについて URL を開きます。 たとえば、「`http://{your-server-ip-address}`」と入力します。

## <a name="start-a-cicd-deployment"></a>CI/CD の配置を開始する

1. リリース定義を開き、[Azure リソース グループの配置] タスクの設定の **[管理オプション]** セクションで **[有効]** チェックボックスをオフにします。
   今後、既存の配置グループに配置する場合、このタスクを再実行する必要はありません。

1. ソース Git リポジトリを開き、ファイル [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade) の **h1** 見出しの内容を変更します。

1. 変更を確定します。

1. 数分経つと、Team Services または Team Foundation Server の **[リリース]** ページに、作成された新しいリリースが表示されます。 リリースを開き、実行された配置を確認します。 ご利用ありがとうございます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Jenkins ビルドと Team Services を使用して Azure にアプリをリリース用に配置する処理を自動化しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Jenkins でアプリを構築する
> * Team Services との統合のために Jenkins を構成する
> * Azure 仮想マシンの配置グループを作成する
> * VM の構成とアプリの配置に使用するリリース定義を作成する

LAMP を展開する方法の詳細 (Linux、Apache、MySQL、および PHP) については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [LAMP スタックのデプロイ](tutorial-lamp-stack.md)
