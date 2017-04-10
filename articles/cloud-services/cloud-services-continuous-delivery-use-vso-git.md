---
title: "Visual Studio Team Services と Git を使用した Azure での継続的な配信 | Microsoft Docs"
description: "Visual Studio Team Services チーム プロジェクトを Git を使用して自動的にビルドして Azure App Service の Web アプリ機能またはクラウド サービスにデプロイするための構成方法について説明します。"
services: cloud-services
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: 4b3297ef-0de6-4d5f-925c-fcdacc3085ac
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/06/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 91e061820e8edc81bca395dc78d3e1e840b21438


---
# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Visual Studio Team Services と Git を使用した Azure への継続的な配信
Visual Studio Team Services チーム プロジェクトを使用してソース コードの Git リポジトリをホストし、コミットをリポジトリにプッシュするたびに自動的にビルドして Azure の Web アプリまたはクラウド サービスにデプロイすることができます。

Visual Studio 2013 および Azure SDK をインストールする必要があります。 Visual Studio 2013 がない場合は、 **www.visualstudio.com** で [無料利用の開始](http://www.visualstudio.com)リンクをクリックしてダウンロードします。 Azure SDK を [ここ](http://go.microsoft.com/fwlink/?LinkId=239540)からインストールしてください。

> [!NOTE]
> このチュートリアルを完了するには、Visual Studio Team Services アカウントが必要です。[Visual Studio Team Services アカウントは無料で作成する](http://go.microsoft.com/fwlink/p/?LinkId=512979)ことができます。
> 
> 

Visual Studio Team Services を使用してクラウド サービスを自動的にビルドして Azure にデプロイするようにセットアップするには、次の手順に従います。

## <a name="1-create-a-git-repository"></a>1: Git リポジトリを作成する
1. Visual Studio Team Services アカウントをまだ持っていない場合は、[ここ](http://go.microsoft.com/fwlink/?LinkId=397665)で取得できます。 チーム プロジェクトを作成するときに、ソース管理システムとして Git を選択します。 指示に従い、Visual Studio をチーム プロジェクトに接続します。
2. **チーム エクスプローラー**で、**[このリポジトリを複製]** を選択します。
   
    ![][3]
3. ローカル コピーの場所を指定し、 **[複製]** をクリックします。

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2: プロジェクトを作成してリポジトリにコミットする
1. **チーム エクスプローラー**の **[ソリューション]** セクションで、**[新規作成]** リンクを選択して新しいプロジェクトをローカル リポジトリに作成します。
   
    ![][4]
2. このチュートリアルの手順に従って、Web アプリまたはクラウド サービス (Azure アプリケーション) をデプロイできます。 新しい Azure クラウド サービス プロジェクト、または新しい ASP.NET MVC プロジェクトを作成します。 プロジェクトが .NET Framework 4 以降を対象にしていることを確認します。 クラウド サービス プロジェクトを作成する場合は、ASP.NET MVC Web ロールと worker ロールを追加します。
   Web アプリを作成する場合は、**[ASP.NET Web アプリケーション プロジェクト]** テンプレートを選択し、次に **[MVC]** を選択します。 詳細については、「 [Azure App Service での ASP.NET Web アプリの作成](../app-service-web/web-sites-dotnet-get-started.md) 」を参照してください。
3. ソリューションのショートカット メニューを開き、 **[コミット]**を選択します。
   
    ![][7]
4. Visual Studio Team Services で初めて Git を使用する場合は、Git で自分自身が識別されるように情報を提供する必要があります。 **チーム エクスプローラー**の **[保留中の変更]** 領域で、ユーザー名と電子メール アドレスを入力します。 コミットのコメントを入力し、 **[コミット]** をクリックします。
   
    ![][8]
5. チェックインをするときは、特定の変更を含むまたは除外するためのオプションに注意してください。 必要な変更が除外されている場合は、 **[すべて含む]**を選択します。
6. これで、リポジトリのローカル コピーに変更をコミットできました。 次に、 **[同期]** リンクを選択してこれらの変更をサーバーと同期させます。

## <a name="3-connect-the-project-to-azure"></a>3: プロジェクトを Azure に接続する
1. ソース コードを含む Git リポジトリが Visual Studio Team Services に保持され、Git リポジトリを Azure に接続する準備が整いました。  [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)で、クラウド サービスまたは Web アプリを選択するか、新たに作成します。作成するには、左下にある [+] アイコンを選択し、**[クラウド サービス]** または **[Web アプリ]** を選択して、**[簡易作成]** を選択します。
   
    ![][9]
2. クラウド サービスの場合は、 **[Visual Studio Team Services 発行の設定]** リンクを選択します。 Web アプリの場合は、 **[ソース管理からのデプロイの設定]** リンクを選択します。
   
    ![][10]
3. ウィザードで、テキスト ボックスに Visual Studio Team Services アカウントの名前を入力し、 **[今すぐ承認]** を選択します。 サインインを求められることがあります。
   
    ![][11]
4. **[接続要求]** ポップアップ ダイアログで、**[承諾]** を選択し、Azure が Visual Studio Team Services のチーム プロジェクトを構成することを許可します。
   
    ![][12]
5. 承認が成功すると、Visual Studio Team Services チーム プロジェクトが含まれるドロップダウン リストが表示されます。  前のステップで作成したチーム プロジェクトの名前を選択し、ウィザードのチェック マーク ボタンを選択します。
   
    ![][13]
   
    次にコミットをリポジトリにプッシュするときに、Visual Studio Team Services はプロジェクトをビルドして Azure にデプロイします。

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: リビルドをトリガーし、プロジェクトを再デプロイする
1. Visual Studio で、ファイルを開き、変更します。 たとえば、MVC Web ロールの Views\\Shared フォルダーにある `_Layout.cshtml` ファイルを変更します。
   
    ![][17]
2. サイトのフッター テキストを編集し、そのファイルを保存します。
   
    ![][18]
3. **ソリューション エクスプローラー**で、ソリューション ノード、プロジェクト ノード、または変更したファイルのショートカット メニューを開き、**[コミット]** を選択します。
4. コメントを入力し、 **[コミット]**を選択します。
   
    ![][20]
5. **[同期]** リンクを選択します。
   
    ![][38]
6. **[プッシュ]** リンクを選択してコミットを Visual Studio Team Services のリポジトリにプッシュします。 (また、 **[同期]** を使用してコミットをリポジトリにコピーすることもできます。 その違いは、 **[同期]** ではリポジトリから最新の変更が取得される点です)。
   
    ![][39]
7. **[ホーム]** を選択して、**チーム エクスプローラー**のホーム ページに戻ります。
   
    ![][21]
8. **[ビルド]** を選択して処理中のビルドを表示します。
   
    ![][22]
   
    **チーム エクスプローラー** に、チェックインのためにビルドが開始されたことが示されます。
   
    ![][23]
9. ビルドの処理に応じて詳細なログを表示するには、処理中のビルドの名前をダブルクリックします。
10. ビルドの処理中に、Azure にリンクするためのウィザードを使用したときに作成されたビルド定義を調べます。  ビルド定義のショートカット メニューを開き、 **[ビルド定義の編集]**を選択します。
    
    ![][25]
11. **[トリガー]** タブを見ると、既定ではチェックインごとにビルドを行うようにビルド定義が設定されていることがわかります。 クラウド サービスの場合は、Visual Studio Team Services により master 分岐がビルドされてステージング環境に自動的にデプロイされます。 その場合も、ライブ サイトにデプロイするための手動による手順を実行する必要があります。 ステージング環境がない Web アプリの場合は、master 分岐が直接ライブ サイトにデプロイされます。
    
    ![][26]
12. **[プロセス]** タブを見ると、デプロイメント環境がクラウド サービスまたは Web アプリの名前に設定されていることがわかります。
    
     ![][27]
13. 既定値と異なる値を使用する場合は、プロパティに対して希望の値を指定します。 Azure の発行のプロパティは **[デプロイ]** セクションにあり、MSBuild パラメーターの設定が必要な場合もあります。 たとえば、クラウド サービス プロジェクトで "クラウド" 以外のサービス構成を指定する場合は、MSbuild パラメーターに `/p:TargetProfile=[YourProfile]` を設定します。ここで *[YourProfile]* は ServiceConfiguration.*YourProfile*.cscfg のような名前のサービス構成ファイルに一致します。
    
     次の表は、 **[デプロイメント]** セクションで使用可能なプロパティを示しています。
    
    | プロパティ | 既定値 |
    | --- | --- |
    | 信頼されていない証明書を許可 |false の場合、SSL 証明書はルート証明機関によって署名される必要があります。 |
    | アップグレードの許可 |新規作成の代わりに、既存のデプロイメントを更新するデプロイメントを許可します。 IP アドレスを保持します。 |
    | 削除しない |true の場合、既存の関連のないデプロイメントを上書きしません (アップグレードは許可)。 |
    | デプロイメント設定へのパス |リポジトリのルート フォルダーを基準とした Web アプリの .pubxml ファイルへのパス。 クラウド サービスでは無視されます。 |
    | Sharepoint デプロイメント環境 |サービス名と同じ。 |
    | Azure デプロイメント環境 |Web アプリ名またはクラウド サービス名。 |
14. このころまでには、ビルドが正常に完了しています。
    
     ![][28]
15. ビルド名をダブルクリックすると、関連付けられた単体テスト プロジェクトのテスト結果を含む **[ビルドの概要]**が表示されます。
    
     ![][29]
16. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)では、ステージング環境が選択されると、関連付けられたデプロイが **[デプロイ]** タブに表示されます。
    
     ![][30]
17. 目的のサイトの URL に移動します。 Web アプリの場合は、ポータルの **[参照]** をクリックします。 クラウド サービスの場合は、ステージング環境が表示される **[ダッシュボード]** ページの **[概要]** セクションで URL を選択します。
    
    クラウド サービス向けの継続的な統合からのデプロイメントは、既定ではステージング環境に発行されます。 **[代替クラウド サービス環境]** プロパティを **[運用]** に設定することで、これを変更できます。 ここでは、クラウド サービスのダッシュボード ページにサイト URL が表示されます。
    
    ![][31]
    
    新しいブラウザー タブが開いて、実行中のサイトが表示されます。
    
    ![][32]
18. プロジェクトにその他の変更を加えると、さらにビルドが実行され、複数のデプロイが累積されます。 最新のデプロイは [アクティブ] とマークされます。
    
    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: 以前のビルドを再デプロイする
この手順は省略可能です。 Azure クラシック ポータルで以前のデプロイを選択し、 **[再デプロイ]** をクリックしてサイトを以前のチェックインに戻します。 これによって、TFS で新しいビルドが開始され、デプロイ履歴に新しいエントリが作成されます。

![][34]

## <a name="6-change-the-production-deployment"></a>6: 運用デプロイを変更する
準備が整ったら、Azure クラシック ポータルで **[スワップ]** を選択してステージング環境を運用環境へ昇格できます。 新たにデプロイされたステージング環境は運用に昇格され、以前の運用環境がある場合、運用環境はステージング環境になります。 運用環境とステージング環境でアクティブなデプロイメントは異なることはありますが、最近のビルドのデプロイメント履歴は環境にかかわらず同じです。

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7: working 分岐からデプロイする
Git を使用する場合は、通常、working 分岐で変更を行い、開発が完了状態に到達したときに master 分岐に統合します。 プロジェクトの開発フェーズの間に、この working 分岐をビルドして Azure にデプロイします。

1. **チーム エクスプローラー**で、**[ホーム]** をクリックし、**[分岐]** を選択します。
   
    ![][40]
2. **[新しい分岐]** リンクを選択します。
   
    ![][41]
3. "working" など分岐の名前を入力し、 **[分岐の作成]**をクリックします。 これにより、新しいローカル分岐が作成されます。
   
    ![][42]
4. 分岐を発行します。 **[発行されていない分岐]** で分岐名を選択し、**[発行]** を選択します。
   
    ![][44]
5. 既定では、master 分岐が変更された場合にのみ、継続的なビルドがトリガーされます。 working 分岐に対して継続的なビルドを設定するには、**チーム エクスプローラー**の **[ビルド]** ページで **[ビルド定義の編集]** をクリックします。
6. **[ソースの設定]** タブをクリックします。 **[Monitored branches for continuous integration and build (継続的インテグレーションとビルドのために監視する分岐)]** で、**[新しい行を追加するにはここをクリックしてください]** をクリックします。
   
    ![][47]
7. refs/heads/working など作成した分岐を指定します。
   
    ![][48]
8. コードに変更を加え、変更したファイルのショートカット メニューを開き、 **[コミット]**を選択します。
   
    ![][43]
9. **[同期されていないコミット]** を選択し、**[同期]** を選択するか、**[プッシュ]** を選択して、Visual Studio Team Services の working 分岐のコピーに変更をコピーします。
   
   ![][45]
10. **[ビルド]** ビューに移動して、working 分岐に対してトリガーされたビルドを見つけます。

## <a name="next-steps"></a>次のステップ
Visual Studio Team Services で Git を使用する場合のその他のヒントについては、[Visual Studio を使用した Git でのコードの開発と共有](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)に関するページを参照してください。また、Visual Studio Team Services で管理されていない Git リポジトリを使用した Azure への発行の詳細については、「[Azure App Service への継続的なデプロイ](../app-service-web/app-service-continuous-deployment.md)」を参照してください。 Visual Studio Team Services の詳細については、[Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861) に関するページを参照してください。

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG



<!--HONumber=Dec16_HO2-->


