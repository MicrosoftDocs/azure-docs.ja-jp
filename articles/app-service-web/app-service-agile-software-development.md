---
title: "Azure App Service を使用したアジャイル ソフトウェア開発"
description: "アジャイル ソフトウェア開発をサポートする方法で Azure App Service を使用して拡張性の高い複雑なアプリケーションを開発する方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: c0fdb676-36a6-4738-925f-65b4835d187f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5ea7af4744876f1085e14de800d599e001c3f3dd
ms.lasthandoff: 03/27/2017


---
# <a name="agile-software-development-with-azure-app-service"></a>Azure App Service を使用したアジャイル ソフトウェア開発
このチュートリアルでは、[アジャイル ソフトウェア開発](https://en.wikipedia.org/wiki/Agile_software_development)をサポートする方法で [Azure App Service](/azure/app-service/) を使用して拡張性の高い複雑なアプリケーションを開発する方法について説明します。 ここでは、 [Azure で複雑なアプリケーションを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)方法を理解していることを前提とします。

技術的な処理の限界によって、アジャイル手法を用いた実装の成功が妨げられることはよくあることです。 Azure App Service は[継続的パブリッシング](app-service-continuous-deployment.md)、[ステージング環境](web-sites-staged-publishing.md) (スロット)、[監視](web-sites-monitor.md)などの機能を持っており、これらの機能を [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) のデプロイと連携させて賢く統合管理すると、アジャイル ソフトウェア開発に携わる開発者にとって非常に優れたソリューションの一部になることができます。

次の表に、アジャイル開発に関連するいくつかの要件と、Azure サービスが各要件にどのように対応できるかを示します。

| 要件 | Azure でできること |
| --- | --- |
| - コミットするたびにビルドする<br>- 自動的かつ高速にビルドする |継続的デプロイを行うように構成すると、Azure App Service は、開発ブランチに基づくライブ実行ビルドとして機能できます。 コードはブランチにプッシュされるたびに自動的にビルドされ、Azure でライブで実行されます。 |
| - ビルドにセルフ テストを実行させる |Azure リソース マネージャー テンプレートを使用して、負荷テスト、Web テストなどをデプロイできます。 |
| - 運用環境のクローン内でテストを実行する |Azure リソース マネージャー テンプレートを使用して、テストを迅速かつ予想どおりに実行するための (アプリの設定、接続文字列テンプレート、スケーリングなどを含む) Azure 運用環境のクローンを作成できます。 |
| - 最新のビルドの結果を容易に表示する |リポジトリから Azure への継続的デプロイは、新しいコードを、変更をコミットしたすぐ後でライブ アプリケーションでテストできることを意味します。 |
| - メイン ブランチに毎日コミットする<br>- デプロイを自動化する |実稼働アプリケーションとリポジトリのメイン ブランチとの継続的インテグレーションによって、すべてのコミット/マージを運用環境のメイン ブランチに自動的にデプロイします。 |

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>学習内容
新しい変更をアプリケーションに発行するための手順について、開発-テスト-ステージング-実稼働という一般的なワークフローに沿って説明を進めていきます。ここで使用するサンプル アプリケーションは [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) という名前であり、2 つの [Web アプリ](/services/app-service/web/) (フロントエンド (FE) と Web API バックエンド (BE)) と [SQL Database](/services/sql-database/) で構成されます。 次に示すデプロイ アーキテクチャで作業します。

![](./media/app-service-agile-software-development/what-1-architecture.png)

図の説明は次のとおりです。

* デプロイ アーキテクチャは 3 つの個別の環境 (Azure では[リソース グループ](../azure-resource-manager/resource-group-overview.md)と言います) に分割され、それぞれに独自の [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)、[スケーリング設定](web-sites-scale.md)、および SQL Database があります。 
* 各環境は別々に管理できます。 異なるサブスクリプションで存在することもできます。
* ステージングと実稼働は、同じ App Service アプリの 2 つのスロットとして実装されます。 マスター ブランチは、ステージング スロットとの継続的インテグレーションを行うようにセットアップされます。
* マスター ブランチへのコミットが (実稼働データを使用して) ステージング スロットで検証されると、検証されたステージング アプリは、 [ダウンタイムなしで](web-sites-staged-publishing.md)実稼働スロットにスワップされます。

ステージング/運用環境は、[*&lt;repository_root>*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json) テンプレートによって定義されます。

開発環境とテスト環境は、[*&lt;repository_root>*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json) テンプレートによって定義されます。

一般的なブランチ戦略を使用して、コードを開発ブランチからテスト ブランチに移動した後、マスター ブランチに移動させることもできます (いわば品質の上方移動)。

![](./media/app-service-agile-software-development/what-2-branches.png) 

## <a name="what-you-will-need"></a>前提条件
* Azure アカウント
* [GitHub](https://github.com/) アカウント
* Git Shell ( [GitHub for Windows](https://windows.github.com/)とともにインストールされます) - これにより、同じセッション内で Git コマンドと PowerShell コマンドの両方を実行できます。 
* 最新の [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi) ビット
* 以下の事柄の基礎知識:
  * [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) テンプレートのデプロイ(「[Azure で複雑なアプリケーションを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)」も参照してください)
  * [Git](http://git-scm.com/documentation)
  * [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。
> 
> * 無料で [Azure アカウントを開く](https://azure.microsoft.com/pricing/free-trial/)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Web Apps など無料の Azure サービスをご利用になれます。
> * [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) こともできます - Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。
> 
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="set-up-your-production-environment"></a>運用環境をセットアップする
> [!NOTE]
> このチュートリアルで使用するスクリプトは、GitHub リポジトリからの継続的パブリッシングを自動的に構成します。 これを行うには、GitHub 資格情報が既に Azure に保存されている必要があります。保存されていない場合、スクリプト化されたデプロイは、Web アプリに対するソース管理設定を構成しようとした時点で失敗します。 
> 
> GitHub 資格情報を Azure に保存するには、Web アプリを [Azure Portal](https://portal.azure.com/) で作成し、[GitHub のデプロイを構成](app-service-continuous-deployment.md)します。 この操作を行うのは 1 回だけです。 
> 
> 

一般的な DevOps シナリオでは、Azure でライブ実行されているアプリケーションがあり、継続的パブリッシングを通してそれを変更します。 このシナリオには、開発、テスト、および運用環境にデプロイするために使用するテンプレートがあります。 このセクションで、それを設定します。

1. [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) リポジトリの自分専用のフォークを作成します。 フォークの作成の詳細については、「 [リポジトリをフォークする](https://help.github.com/articles/fork-a-repo/)」を参照してください。 フォークが作成されたら、ブラウザーでそれを確認できます。
   
   ![](./media/app-service-agile-software-development/production-1-private-repo.png)
2. Git Shell セッションを開きます。 Git Shell をまだ持っていない場合は、この時点で [GitHub for Windows](https://windows.github.com/) をインストールします。
3. 次のコマンドを実行して、フォークのローカル クローンを作成します。
   
     git clone https://github.com/<フォーク>/ToDoApp.git 
4. ローカル クローンを作成したら、*&lt;repository_root>*\ARMTemplates に移動し、次のように deploy.ps1 スクリプトを実行します。
   
     .\deploy.ps1 –RepoUrl https://github.com/<フォーク>/todoapp.git
5. メッセージが表示されたら、データベースにアクセスするためのユーザー名とパスワードを入力します。
   
   Azure のさまざまなリソースのプロビジョニングの進行状況が表示されます。 デプロイが完了すると、スクリプトによってアプリケーションがブラウザー内に起動し、わかりやすいビープ音が鳴ります。
   
   ![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
   
   > [!TIP]
   > *&lt;repository_root>*\ARMTemplates\Deploy.ps1 を見て、どのような方法でリソースが一意の ID で生成されるかを確認してください。 同じ方法で、同じデプロイのクローンを、リソース名の競合を心配せずに作成できます。
   > 
   > 
6. Git Shell セッションに戻り、次を実行します。
   
     .\swap –Name ToDoApp<一意の文字列>master
   
   ![](./media/app-service-agile-software-development/production-4-swap.png)
7. スクリプトが終了したら、フロントエンドのアドレス (http://ToDoApp*&lt;unique_string>*master.azurewebsites.net/) を参照して、アプリケーションが運用環境で実行されていることを確認します。
8. [Azure ポータル](https://portal.azure.com/) にログインして、何が作成されたかを調べます。
   
   同じリソース グループ内に 2 つの Web アプリがあり、1 つは名前に `Api` サフィックスが付いていることを確認できます。 リソース グループ ビューを表示している場合は、SQL Database とサーバー、App Service プラン、および Web アプリのステージング スロットも表示されます。 さまざまなリソースを参照し、それらを *&lt;repository_root>*\ARMTemplates\ProdAndStage.json と比較して、テンプレート内にどのように構成されているかを確認します。
   
   ![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

これで、運用環境がセットアップされました。 次に、アプリケーションの新しい更新を開始します。

## <a name="create-dev-and-test-branches"></a>開発ブランチとテスト ブランチを作成する
Azure の運用環境で実行されている複雑なアプリケーションがあり、アジャイル手法に従ってアプリケーションを更新します。 このセクションでは、更新を行うために必要な開発ブランチとテスト ブランチを作成します。

1. 最初に、テスト環境を作成します。 Git Shell セッションで次のコマンドを実行して、 **NewUpdate**という名前の新しいブランチ用の環境を作成します。 
   
     git checkout -b NewUpdate   git push origin NewUpdate   .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<フォーク>/ToDoApp.git -Branch NewUpdate
2. メッセージが表示されたら、データベースにアクセスするためのユーザー名とパスワードを入力します。 
   
   デプロイが完了すると、スクリプトによってアプリケーションがブラウザー内に起動し、わかりやすいビープ音が鳴ります。 これで、新しいブランチと、専用のテスト環境が用意されました。 このテスト環境について、いくつかの事柄を再確認しておきます。
   
   * 任意の Azure サブスクリプションで作成できます。 これは、運用環境をテスト環境から切り離して管理できることを意味します。
   * テスト環境は Azure でライブ実行されます。
   * テスト環境は運用環境と同じですが、ステージング スロットとスケーリング設定は異なります。 ProdandStage.json と Dev.json の違いはこれだけであるため、どちらの環境であるかをこれによって判断できます。
   * テスト環境は、価格レベルが異なる独自の App Service プラン ( **無料**など) で管理できます。
   * このテスト環境の削除は、リソース グループの削除と同じように容易に実行できます。 これを行う方法については、 [後で](#delete)説明します。
3. 次のコマンドを実行して、開発ブランチの作成に進みます。
   
     git checkout -b Dev   git push origin Dev   .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<フォーク>/ToDoApp.git -Branch Dev
4. メッセージが表示されたら、データベースにアクセスするためのユーザー名とパスワードを入力します。 
   
   この開発環境について、いくつかの事柄を再確認しておきます。 
   
   * 開発環境はテスト環境と同じテンプレートを使用してデプロイされるため、その構成はテスト環境と同じです。
   * 開発環境は開発者自身の Azure サブスクリプションで作成でき、テスト環境とは別に管理されます。
   * 開発環境は、Azure でライブ実行されます。
   * 開発環境の削除は、リソース グループの削除と同じように容易に実行できます。 これを行う方法については、 [後で](#delete)説明します。

> [!NOTE]
> 複数の開発者が新しい更新のために作業する場合、各開発者は、次の手順を実行することで、ブランチと専用の開発環境を簡単に作成できます。
> 
> 1. GitHub でリポジトリの自分専用のフォークを作成します (「 [リポジトリをフォークする](https://help.github.com/articles/fork-a-repo/)」を参照してください)。
> 2. ローカル コンピューターにフォークのクローンを作成します。
> 3. 自分専用の開発ブランチと環境を作成するためのコマンドを実行します。
> 
> 

操作が完了すると、GitHub フォークには 3 つのブランチが存在します。

![](./media/app-service-agile-software-development/test-1-github-view.png)

さらに、3 つの別々のリソース グループに 6 つの Web アプリケーション (2 つで 1 セットが 3 セット) が存在します。

![](./media/app-service-agile-software-development/test-2-all-webapps.png)

> [!NOTE]
> ProdandStage.json では、運用環境を**標準**価格レベルで使用することを指定しています。これは実稼働アプリケーションのスケーラビリティに適しています。
> 
> 

## <a name="build-and-test-every-commit"></a>すべてのコミットをビルドしてテストする
ProdAndStage.json テンプレート ファイルと Dev.json テンプレート ファイルには既にソース管理パラメーターが指定されており、既定で Web アプリの継続的パブリッシングが設定されています。 したがって、GitHub ブランチに対するすべてのコミットは、そのブランチから Azure への自動デプロイをトリガーします。 ここで、このセットアップがどのようには機能するかを見ていきましょう。

1. ローカル リポジトリの開発ブランチにいることを確認します。 これを行うには、Git Shell で次のコマンドを実行します。
   
     git checkout Dev
2. [ブートストラップ](http://getbootstrap.com/components/) リストを使用するようにコードを変更することで、アプリの UI レイヤーに簡単な変更を加えます。 *&lt;repository_root>*\src\MultiChannelToDo.Web\index.cshtml を開き、次の図で強調表示されているように変更します。
   
   ![](./media/app-service-agile-software-development/commit-1-changes.png)
   
   > [!NOTE]
   > 上の図が読み取れない場合: 
   > 
   > * 行 18 で、`check-list` を `list-group` に変更します。
   > * 行 19 で、`class="check-list-item"` を `class="list-group-item"` に変更します。
   > 
   > 
3. 変更を保存します。 Git Shell に戻り、次のコマンドを実行します。
   
     cd <リポジトリ ルート> git add .
     git commit -m "Bootstrap スタイルに変更" git push origin Dev
   
   これらの Git コマンドは、TFS などの別のソース管理システムでの "コードのチェックイン" に似ています。 `git push`を実行すると、新しいコミットによって Azure への自動コード プッシュがトリガーされ、開発環境で変更を反映するようにアプリケーションがリビルドされます。
4. 開発環境に対するこのコード プッシュが発生したことを確認するには、開発環境の Wb アプリ ブレードに移動し、 **[デプロイ]** 部分を調べます。 最新のコミット メッセージを確認できます。
   
   ![](./media/app-service-agile-software-development/commit-2-deployed.png)
5. そこから、 **[参照]** をクリックして、Azure で実行されているライブ アプリケーションの新しい変更を確認します。
   
   ![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)
   
   これは、アプリケーションに対する非常に小さな変更です。 ただし、複雑な Web アプリケーションに対する新しい変更によって、意図的でない、望ましくない副作用が発生することがあります。 ライブ ビルドでのすべてのコミットを簡単にテストできることで、問題を顧客が目にする前に捕まえることができます。

ここまでの説明で、 **NewUpdate** プロジェクトの開発者として、自分用の開発環境の作成、すべてのコミットのビルド、およびすべてのコミットのテストを容易に実行できることを十分に理解できたはずです。

## <a name="merge-code-into-test-environment"></a>テスト環境にコードをマージする
コードを開発ブランチから NewUpdate ブランチにプッシュする準備ができたら、次の標準的な Git プロセスを実行します。

1. NewUpdate に対するすべての新しいコミット (他の開発者によって作成されたコミットなど) を、GitHub の開発ブランチにマージします。 GitHub での新しいコミットによってコード プッシュがトリガーされ、開発環境でビルドされます。 その後、開発ブランチのコードが NewUpdate ブランチからの最新のビットで引き続き動作することを確認できます。
2. 開発ブランチからのすべての新しいコミットを GitHub の NewUpdate ブランチにマージします。 この操作によって、コード プッシュとテスト環境でのビルドがトリガーされます。 

ここでも、これらの Git ブランチには継続的デプロイが既に設定されているため、インテグレーション ビルドの実行と同じように、それ以外の操作を行う必要はないことに注意してください。 実行する必要があるのは、Git を使用した標準的なソース管理プラクティスだけであり、すべてのビルド プロセスは Azure が代わりに実行します。

次に、コードを **NewUpdate** ブランチにプッシュします。 Git Shell で、次のコマンドを実行します。

    git checkout NewUpdate
    git pull origin NewUpdate
    git merge Dev
    git push origin NewUpdate

これで終了です。 

テスト環境の Web アプリ ブレードに移動し、(NewUpdate ブランチにマージされた) 新しいコミットがテスト環境にプッシュされたことを確認します。 次に、 **[参照]** をクリックし、スタイルの変更が Azure でライブ実行されていることを確認します。

## <a name="deploy-update-to-production"></a>運用環境に更新をデプロイする
ステージング/運用環境にコードをプッシュする操作は、テスト環境にコードをプッシュするときに実行した操作と違いはありません。 それは非常に単純です。 

Git Shell で、次のコマンドを実行します。

    git checkout master
    git pull origin master
    git merge NewUpdate
    git push origin master

新しいコードは、ProdandStage.json 内のステージング/運用環境のセットアップ方法に基づいて、 **ステージング** スロットにプッシュされ、そこで実行されます。 したがって、ステージング スロットの URL に移動すると、新しいコードがそこで実行されていることがわかります。 これを行うには、Git Shell で `Show-AzureWebsite` コマンドレットを実行します。

    Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging

ステージング スロットで更新を確認した後、残っている唯一の操作は、それを運用環境にスワップすることです。 Git Shellで、次のコマンドを実行します。

    cd <repository_root>\ARMTemplates
    .\swap.ps1 -Name ToDoApp<unique_string>master

ご利用ありがとうございます。 新しい更新が実稼働している Web アプリケーションに正常にパブリッシュされました。 これは、開発環境とテスト環境の作成、およびすべてのコミットのビルドとテストを容易に実行した結果です。 これらは、アジャイル ソフトウェア開発の重要な構成要素です。

<a name="delete"></a>

## <a name="delete-dev-and-test-enviroments"></a>開発環境とテスト環境を削除する
開発環境とテスト環境は意図的に自己完結型リソース グループとして構築したため、それらは非常に簡単に削除できます。 このチュートリアルで作成した、GitHub のブランチと Azure アーチファクトの両方を削除するには、Git Shell で次のコマンドを実行するだけですみます。

    git branch -d Dev
    git push origin :Dev
    git branch -d NewUpdate
    git push origin :NewUpdate
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## <a name="summary"></a>概要
アジャイル ソフトウェア開発は、アプリケーション プラットフォームとして Azure を採用することを希望する多くの企業になくてはならないものです。 このチュートリアルでは、運用環境の完全なまたは完全に近いレプリカの作成と削除を容易に実行する方法について説明しました。これは複雑なアプリケーションでも同じです。 すべてのコミットを Azure でビルドして構築できる開発プロセス作成機能を活用する方法についても説明しました。 このチュートリアルが、Azure App Service と Azure リソース マネージャーを最大限に利用して、アジャイル手法に対応する DevOps ソリューションを作成する方法の十分な説明になっていれば幸いです。 次に、このシナリオを基にしてビルドするには、[運用環境でのテスト](app-service-web-test-in-production-get-start.md)などの高度な DevOps テクニックを実行します。 一般的な運用環境でのテスト シナリオについては、「[Azure App Service でのフライト デプロイメント (ベータ テスト)](app-service-web-test-in-production-controlled-test-flight.md)」を参照してください。

## <a name="more-resources"></a>その他のリソース
* [Azure で複雑なアプリケーションを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)
* [Agile Development in Practice: Tips and Tricks for Modernized Development Cycle (アジャイル開発の実践: 最新の開発サイクルのためのヒント)](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
* [Advanced deployment strategies for Azure Web Apps using Resource Manager templates (Resource Manager テンプレートを使用した Azure Web Apps 向けの高度なデプロイ戦略)](http://channel9.msdn.com/Events/Build/2015/2-620)
* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)
* [JSONLint - JSON Validator に関するページ](http://jsonlint.com/)
* [ARMClient – サイトへの GitHub のパブリッシュの設定に関するページ](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
* [Git のブランチ機能 - 基本的なブランチとマージに関するページ](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* [David Ebbo のブログ](http://blog.davidebbo.com/)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Azure クロスプラットフォーム コマンド ライン ツール](../cli-install-nodejs.md)
* [Azure AD でのユーザーの作成または編集](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
* [Project Kudu Wiki](https://github.com/projectkudu/kudu/wiki)


