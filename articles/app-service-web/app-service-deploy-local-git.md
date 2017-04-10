---
title: "Azure App Service へのローカル Git デプロイ"
description: "Azure App Service へのローカル Git デプロイを有効にする方法を説明します。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 657554ee3929572632dc007d1a6500e59e2a6b97
ms.lasthandoff: 03/22/2017


---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service へのローカル Git デプロイ
このチュートリアルでは、ローカル コンピューター上の Git リポジトリから [Azure App Service] にアプリをデプロイする方法を説明します。 この方法は、App Service により、 **Azure ポータル** の [ローカル Git]デプロイ オプションでサポートされています。  
この記事に記載されている Git コマンドの多くは、[ここ](app-service-web-get-started.md)に説明されている [Azure コマンド ライン インターフェイス]を使用して App Service アプリを作成する際に自動的に実行されます。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次のものが必要です。

* Git インストール バイナリを[こちら](http://www.git-scm.com/downloads)でダウンロードできます。  
* Git の基本的な知識。
* Microsoft Azure アカウント。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)にしてください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。  
> 
> 

## <a name="Step1"></a>手順 1: ローカル リポジトリを作成する
次のタスクを実行して、新しい Git リポジトリを作成します。

1. **GitBash** (Windows) や **Bash** (Unix シェル) などのコマンド ライン ツールを起動します。 OS X システムでは、 **ターミナル** アプリケーションを使用してコマンド ラインにアクセスできます。
2. デプロイするコンテンツが配置されるディレクトリに移動します。
3. 次のコマンドを使用して、新しい Git リポジトリを初期化します。
   
        git init

## <a name="Step2"></a>手順 2: コンテンツをコミットする
App Service では、さまざまなプログラミング言語で作成されたアプリケーションをサポートしています。 

1. リポジトリに既にコンテンツが含まれている場合は、このポイントをスキップし、以下のポイント 2 に進みます。 リポジトリにまだコンテンツが含まれていない場合は、静的 .html ファイルにより値を次のように設定します。 
   
   * テキスト エディターを使用して、Git リポジトリのルートに **index.html** という名前の新しいファイルを作成します。
   * index.html ファイルの内容として次のテキストを追加し、ファイルを保存します: *Hello Git!*
2. コマンド ラインで、Git リポジトリのルートにいることを確認します。 次のコマンドを使用して、リポジトリにファイルを追加します。
   
        git add -A 
3. 次に、次のコマンドを使用して、リポジトリへの変更をコミットします。
   
        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>手順 3: App Service アプリのリポジトリを有効にする
次に示している手順を実行して、App Service アプリに対して Git リポジトリを有効にします。

1. [ローカル Git]にログインします。
2. App Service アプリのブレードで、**[設定]、[展開元]** の順にクリックします。 **[ソースの選択]** をクリックし、**[ローカル Git リポジトリ]** をクリックして **[OK]** をクリックします。  
   
    ![ローカルの Git リポジトリ](./media/app-service-deploy-local-git/local_git_selection.png)
3. Azure で初めてリポジトリを設定した場合、そのログイン資格情報を作成する必要があります。 それらを使用して、Azure リポジトリにログインし、ローカル Git リポジトリから変更をプッシュします。 アプリのブレードで **[設定]、[デプロイ資格情報]** の順にクリックし、デプロイ用のユーザー名とパスワードを構成します。 終了したら **[保存]**をクリックします。
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>手順 4: プロジェクトをデプロイする
次に示している手順に従って、ローカル Git を使用してアプリを App Service に発行します。

1. Azure Portal の対象アプリのブレードで、**[設定]、[プロパティ]** の順にクリックし、**[Git URL (Git の URL)]** を確認します。
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    **Git URL** は、ローカル リポジトリからデプロイする際のリモート参照です。 この URL は次の手順で使用します。
2. コマンド ラインを使用して、現在の位置がローカル Git リポジトリのルートであることを確認します。
3. `git remote` を使用して、手順 1 の **[Git の URL]** にリストされたリモート参照を追加します。 コマンドは次のようになります。
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > この **remote** コマンドは、名前付きの参照をリモート リポジトリに追加します。 この例では、Web アプリのリポジトリに「azure」という名前の参照を作成しています。
   > 
   > 
4. 作成した新しい **azure** リモートを使用して、コンテンツを App Service にプッシュします。
   
        git push azure master
   
    Azure ポータルでデプロイメント資格情報をリセットしたときに作成したパスワードの入力を求められます。 パスワードを入力します (パスワードを入力しても Gitbash によってコンソールにアスタリスクはエコーされません)。 
5. Azure ポータル内のアプリに戻ります。 最近使用したプッシュのログ エントリが **[デプロイ]** ブレードに表示されます。 
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. アプリのブレード上部の **[参照]** ボタンをクリックして、コンテンツがデプロイされていることを確認します。 

## <a name="Step5"></a>トラブルシューティング
Git を使用して Azure の App Service に発行する場合に発生する一般的なエラーまたは問題を以下に示します。

- - -
**症状**: '[siteURL]' にアクセスできません: [scmAddress] に接続できませんでした

**原因**: このエラーはアプリが実行されていない場合に発生する可能性があります。

**解決策**: Azure ポータルでアプリを起動します。 Git デプロイはアプリが実行されていない限り機能しません。 

- - -
**症状**: ホスト 『hostname』 を解決できませんでした

**原因**: このエラーは、"azure" リモートを作成するときに入力したアドレス情報が間違っている場合に発生します。

**解決策**: `git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。 "azure" リモートの URL が正しいことを確認します。 必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。

- - -
**症状**: 参照が共通していないか、指定されていません。未対応です。 『master』 などの分岐を指定する必要があります。

**原因**: このエラーは、git push 操作を実行するときに分岐を指定せず、Git で使用される push.default 値を設定していない場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。 次に例を示します。

    git push azure master

- - -
**症状**: src refspec [branchname] does not match any.

**原因**: このエラーは、"azure" リモートの master 以外の分岐にプッシュしようとした場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。 For example:

    git push azure master

- - -
**症状**: RPC failed; result=22, HTTP code = 502

**原因**: このエラーは、HTTPS 経由で大規模な Git リポジトリをプッシュしようとした場合に発生します。

**解決策**: ローカル コンピューター上の Git 構成を変更して postBuffer を増やします。

    git config --global http.postBuffer 524288000

- - -
**症状**: エラー - 変更がリモート リポジトリにコミットされましたが、Web アプリは更新されていません。

**原因**: このエラーは、必要な追加モジュールを指定する package.json ファイルを含む Node.js アプリをデプロイする場合に発生します。

**解決策**: "npm ERR!" を含む追加のメッセージが ログに記録されます。このメッセージによって、このエラーに関する追加のコンテキストが提供される場合があります。 このエラーの既知の原因と、対応する "npm ERR!" メッセージを以下に示します。 メッセージ:

* **形式が正しくない package.json ファイル**: npm ERR! Couldn't read dependencies.
* **Windows 用のバイナリ配布がないネイティブ モジュール**:
  
  * npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1
    
      または
  * npm ERR! [modulename@version] preinstall: \`make || gmake\`

## <a name="additional-resources"></a>その他のリソース
* [Git に関するドキュメント](http://git-scm.com/documentation)
* [Project Kudu に関するドキュメント](https://github.com/projectkudu/kudu/wiki)
* [Azure App Service への継続的なデプロイ](app-service-continuous-deployment.md)
* [How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)](/powershell/azureps-cmdlets-docs)
* [Azure コマンド ライン インターフェイスの使用方法](../cli-install-nodejs.md)

[Azure App Service]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[ローカル Git]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure コマンド ライン インターフェイス]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart

