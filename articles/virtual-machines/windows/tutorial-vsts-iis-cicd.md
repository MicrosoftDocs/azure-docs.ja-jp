---
title: "Team Services を使用して Azure に CI/CD パイプラインを作成する | Microsoft Docs"
description: "Windows VM 上の IIS に Web アプリをデプロイする継続的インテグレーションと継続的配信のための Visual Studio Team Services パイプラインを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a587f58fad2ec74c7633823c4d34f900e7c01f7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Visual Studio Team Services と IIS を使用して継続的インテグレーション パイプラインを作成する
ビルド、テスト、デプロイというアプリケーション開発の各フェーズを自動化するには、継続的インテグレーション/継続的配置 (CI/CD) パイプラインを使用できます。 このチュートリアルでは、Visual Studio Team Services と、IIS を実行する Azure の Windows 仮想マシン (VM) を使用して CI/CD パイプラインを作成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Web アプリケーションを Team Services プロジェクトに発行する
> * コードのコミットによってトリガーされるビルド定義を作成する
> * Azure の仮想マシンに IIS をインストールして構成する
> * Team Services の配置グループに IIS インスタンスを追加する
> * 新しい Web デプロイ パッケージを IIS に発行するためのリリース定義を作成する
> * CI/CD パイプラインのテスト

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。


## <a name="create-project-in-team-services"></a>Team Services でのプロジェクトの作成
Visual Studio Team Services を使用すると、オンプレミスのコード管理ソリューションを保持することなく、コラボレーションと開発を容易に行うことができます。 Team Services を通じて、クラウド コードのテスト、ビルド、アプリケーションの洞察が可能となります。 実際のコード開発に最も合った IDE とバージョン管理リポジトリを選択できます。 このチュートリアルでは、無料アカウントを使用して、基本的な ASP.NET Web アプリと CI/CD パイプラインを作成します。 Team Services アカウントがまだない場合は、[作成](http://go.microsoft.com/fwlink/?LinkId=307137)してください。

コード コミット プロセス、ビルド定義、リリース定義を管理するには、次のように、Team Services でプロジェクトを作成します。

1. Web ブラウザーで Team Services ダッシュボードを開き、**[新しいプロジェクト]** を選択します。
2. **[プロジェクト名]** に「*myWebApp*」と入力します。 その他はすべて既定値のままにし、*Git* バージョン管理と "*アジャイル*" 作業項目プロセスを使用します。
3. "*チーム メンバー*" と**共有**するオプションを選択し、**[作成]** を選択します。
5. プロジェクトが作成されたら、**[Readme や gitignore で初期化する]** オプションを選択し、**[初期化]** を選択します。
6. 新しいプロジェクトで上部の **[ダッシュボード]** を選択し、**[Visual Studio で開く]** を選択します。


## <a name="create-aspnet-web-application"></a>ASP.NET Web アプリケーションの作成
前の手順では、プロジェクトを Team Services で作成しました。 最後の手順で新しいプロジェクトが Visual Studio で開かれます。 コード コミットは、**[チーム エクスプローラー]** ウィンドウで管理します。 新しいプロジェクトのローカル コピーを作成したうえで、次のように ASP.NET Web アプリケーションをテンプレートから作成します。

1. **[複製]** を選択して、Team Services プロジェクトのローカル Git リポジトリを作成します。
    
    ![Team Services プロジェクトからリポジトリを複製する](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. **[ソリューション]** の **[新規作成]** を選択します。

    ![Web アプリケーション ソリューションを作成する](media/tutorial-vsts-iis-cicd/new_solution.png)

3. **[Web]** テンプレートを選択し、**[ASP.NET Web アプリケーション]** テンプレートを選択します。
    1. アプリケーションの名前 (例: *myWebApp*) を入力し、**[ソリューションのディレクトリを作成]** チェック ボックスをオフにします。
    2. **[Application Insights をプロジェクトに追加する]** チェック ボックスをオフにします (このオプションが選択可能である場合)。 Application Insights を使用するには、Web アプリケーションを Azure Application Insights で承認する必要があります。 このチュートリアルでは簡潔にするために、このプロセスをスキップします。
    3. **[OK]**を選択します。
4. テンプレート一覧から **[MVC]** を選択します。
    1. **[認証の変更]** を選択し、**[認証なし]** を選択して、**[OK]** を選択します。
    2. **[OK]** を選択してソリューションを作成します。
5. **[チーム エクスプローラー]** ウィンドウで **[変更]** を選択します。

    ![ローカルでの変更を Team Services の Git リポジトリにコミットする](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. コミット テキスト ボックスに、メッセージを入力します (例: *Initial commit*)。 ドロップダウン メニューから **[すべてをコミットして同期]** を選択します。


## <a name="create-build-definition"></a>ビルド定義の作成
Team Services では、ビルド定義を使用してアプリケーションのビルド方法を指定します。 このチュートリアルでは、ソース コードを取得し、ソリューションをビルドして、IIS サーバーで Web アプリを実行するために使用できる Web デプロイ パッケージを作成する基本的な定義を作成します。

1. Team Services プロジェクトで、上部にある **[ビルドとリリース]** を選択し、**[ビルド]** を選択します。
3. **[+ 新しい定義]** を選択します。
4. **[ASP.NET (プレビュー)]** テンプレートを選択し、**[適用]** を選択します。
5. タスクの既定値すべてそのままにします。 **[ソースの取得]** で、*myWebApp* リポジトリと *master* ブランチが選択されていることを確認します。

    ![Team Services プロジェクトでビルド定義を作成する](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. **[トリガー]** タブの **[Enable this trigger]\(このトリガーを有効にする\)** のスライダーを *[有効]* に移動します。
7. **[保存してキューに登録]** を選択し、もう一度 **[保存してキューに登録]** を選択して、ビルド定義を保存し、新しいビルドをキューに登録します。 既定の設定はそのままにして、**[キュー]** を選択します。

ホステッド エージェントでビルドがスケジュールされ、その後ビルドが開始されるようすを確認します。 出力は次の例のようになります。

![Team Services プロジェクトのビルドが成功](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>仮想マシンの作成
ASP.NET Web アプリを実行するプラットフォームを提供するには、IIS を実行する Windows 仮想マシンが必要です。 Team Services は、コードがコミットされてビルドがトリガーされるときの IIS インスタンスとのやり取りを、エージェントを使って行います。

[このスクリプト サンプル](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)を使用して Windows Server 2016 VM を作成してください。 このスクリプトを実行してから VM が作成されるまでに数分かかります。 VM が作成されたら、次のように [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して、Web トラフィック用にポート 80 を開きます。

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

VM に接続するには、次のように [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用してパブリック IP アドレスを取得します。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

VM へのリモート デスクトップ セッションを作成します。

```cmd
mstsc /v:<publicIpAddress>
```

VM で、**管理者権限の PowerShell** コマンド プロンプトを開きます。 次のように、IIS と必要な .NET 機能をインストールします。

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>配置グループの作成
Web デプロイ パッケージを IIS サーバーにプッシュするには、Team Services で配置グループを定義します。 このグループを使用すると、Team Services にコードをコミットしてビルドが実行されるときに、どのサーバーが新しいビルドのターゲットになるかを指定できます。

1. Team Services で **[ビルドとリリース]** を選択し、**[配置グループ]** を選択します。
2. **[配置グループの追加]** を選択します。
3. グループの名前 (*myIIS* など) を入力し、**[作成]** を選択します。
4. **[マシンの登録]** セクションで *[Windows]* が選択されていることを確認し、**[Use a personal access token in the script for authentication]\(スクリプトでの認証に個人用アクセス トークンを使用する\)** チェック ボックスをオンにします。
5. **[Copy script to clipboard]\(スクリプトをクリップボードにコピー\)** を選択します。


### <a name="add-iis-vm-to-the-deployment-group"></a>配置グループへの IIS VM の追加
配置グループが作成されたら、そのグループに各 IIS インスタンスを追加します。 新しい Web デプロイ パッケージを受け取る VM にエージェントをダウンロードして構成し、それを IIS に適用するスクリプトが、Team Services によって生成されます。

1. VM の**管理者権限の PowerShell** セッションに戻り、Team Services からコピーしたスクリプトを貼り付けて実行します。
2. エージェントのタグを構成するように求められたら、*Y* を選択し、「*web*」と入力します。
3. ユーザー アカウントの入力を求められたら、*Enter* キーを押して既定値を受け入れます。
4. スクリプトの完了を待ちます。完了すると、"*Service vstsagent.account.computername started successfully (サービス vstsagent.account.computername が正常に開始されました)*" というメッセージが表示されます。
5. **[ビルドとリリース]** メニューの **[配置グループ]** ページで、*myIIS* 配置グループを開きます。 **[Machines]\(マシン\)** タブで、目的の VM が表示されていることを確認します。

    ![Team Services の配置グループに VM が正常に追加されました](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>リリース定義の作成
ビルドを発行するには、Team Services でリリース定義を作成します。 この定義は、アプリケーションが正常にビルドされたことをきっかけとして自動的にトリガーされます。 Web デプロイ パッケージのプッシュ先の配置グループを選択し、適切な IIS 設定を定義します。

1. **[ビルドとリリース]** を選択し、**[ビルド]** を選択します。 前の手順で作成したビルド定義を選択します。
2. **[最近完了したもの]** で最新のビルドを選択し、**[リリース]** を選択します。
3. **[はい]** を選択してリリース定義を作成します。
4. **[空]** のテンプレートを選択し、**[次へ]** を選択します。
5. プロジェクトとソース ビルド定義に、対象のプロジェクトが入力されていることを確認します。
6. **[継続的配置]** チェック ボックスをオンにし、**[作成]** を選択します。
7. **[+ タスクの追加]** の横のドロップダウン ボックスを選択し、*[配置グループのフェーズを追加する]* を選択します。
    
    ![Team Services でリリース定義にタスクを追加する](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. **[IIS Web App Deploy (Preview)]\(IIS Web アプリのデプロイ (プレビュー)\)** の横の **[追加]** を選択し、**[閉じる]** を選択します。
9. **[配置グループで実行]** 親タスクを選択します。
    1. **[配置グループ]** で、先ほど作成した配置グループを選択します (*myIIS* など)。
    2. **[コンピューター タグ]** ボックスで **[追加]** を選択し、*[web]* タグを選択します。
    
    ![IIS のリリース定義の配置グループ タスク](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. **[Deploy: IIS Web App Deploy]\(デプロイ: IIS Web アプリのデプロイ\)** タスクを選択して、IIS インスタンスの設定を次のように構成します。
    1. **[Web サイト名]** に「*Default Web Site*」と入力します。
    2. その他の既定の設定はすべてそのままにします。
12. **[保存]** を選択し、**[OK]** を 2 回選択します。


## <a name="create-a-release-and-publish"></a>リリースの作成と発行
Web デプロイ パッケージを新しいリリースとしてプッシュできるようになりました。 この手順では、配置グループに属している各インスタンスのエージェントとやり取りして、Web デプロイ パッケージをプッシュし、最新の Web アプリケーションを実行するように IIS を構成します。

1. リリース定義で **[+ リリース]** を選択し、**[リリースの作成]** を選択します。
2. ドロップダウン リストで最新のビルドが、**[自動配置: リリース作成後]** と共に選択されていることを確認します。 **[作成]**を選択します。
3. リリース定義の上部に、"*Release 'Release-1' has been created (リリース 'Release-1' が作成されました)*" のように小さなバナーが表示されます。 該当するリリースのリンクを選択します。
4. **[ログ]** タブを開いて、リリースの進行状況を監視します。
    
    ![Team Services のリリースと Web デプロイ パッケージのプッシュに成功](media/tutorial-vsts-iis-cicd/successful_release.png)

5. リリースが完了したら、Web ブラウザーを開いて VM のパブリック IP アドレスを入力します。 ASP.NET Web アプリケーションが実行されています。

    ![IIS VM 上で実行されている ASP.NET Web アプリ](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>CI/CD パイプライン全体のテスト
IIS で Web アプリケーションが実行されている状態で、今度は CI/CD パイプライン全体を試します。 Visual Studio でコードに変更を加えてコミットすると、ビルドがトリガーされ、さらにそれをきっかけとして、更新された Web デプロイ パッケージが IIS にリリースされます。

1. Visual Studio で **[ソリューション エクスプローラー]** ウィンドウを開きます。
2. *[myWebApp]、[Views]、[Home]、[Index.cshtml]* の順に移動して開きます。
3. 6 行目を次のように編集します。

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. ファイルを保存します。
5. **[チーム エクスプローラー]** ウィンドウで *[myWebApp]* プロジェクトを選択し、**[変更]** を選択します。
6. コミット メッセージ (例: *Testing CI/CD pipeline*) を入力し、ドロップダウン メニューから **[すべてをコミットして同期]** を選択します。
7. Team Services ワークスペースで、コードのコミットから新しいビルドがトリガーされます。 
    - **[ビルドとリリース]** を選択し、**[ビルド]** を選択します。 
    - ビルド定義を選択し、**[キューに入っているものと実行中のもの]** ビルドを選択して、ビルドの進行状況を確認します。
9. ビルドが成功すると、新しいリリースがトリガーされます。
    - **[ビルドとリリース]**、**[リリース]** の順に選択して、IIS VM にプッシュされた Web デプロイ パッケージを確認します。 
    - **[最新の情報に更新]** アイコンを選択して状態を更新します。 *[環境]* 列に緑色のチェック マークが表示されている場合は、リリースが IIS に正常にデプロイされています。
11. 適用された変更を確認するために、ブラウザーで IIS Web サイトを最新の情報に更新します。

    ![IIS VM で CI/CD パイプラインから実行されている ASP.NET Web アプリ](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Team Services で ASP.NET Web アプリケーションを作成し、コードがコミットされるたびに新しい Web デプロイ パッケージが IIS にデプロイされるようにビルド定義とリリース定義を構成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * ASP.NET Web アプリケーションを Team Services プロジェクトに発行する
> * コードのコミットによってトリガーされるビルド定義を作成する
> * Azure の仮想マシンに IIS をインストールして構成する
> * Team Services の配置グループに IIS インスタンスを追加する
> * 新しい Web デプロイ パッケージを IIS に発行するためのリリース定義を作成する
> * CI/CD パイプラインのテスト

次のチュートリアルに進み、SSL を使用して Web サーバーをセキュリティで保護する方法を学習してください。

> [!div class="nextstepaction"]
> [SSL による Web サーバーのセキュリティ保護](tutorial-secure-web-server.md)