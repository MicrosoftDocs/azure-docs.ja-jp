---
title: "Azure DevTest Labs でラボに Git アーティファクト リポジトリを追加する | Microsoft Docs"
description: "Azure DevTest Labs でカスタム アーティファクト ソースに GitHub または Visual Studio Team Services Git リポジトリを追加する"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f7656382500682898cd3ed6372630afa3c3f6350
ms.openlocfilehash: 5c595bd2175f1cbc650bd1a757eeacc80cf39e23
ms.lasthandoff: 01/12/2017


---
# <a name="add-a-git-artifact-repository-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボに Git アーティファクト リポジトリを追加する
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-add-your-private-Artifacts-Repository-in-a-DevTest-Lab/player]
> 
> 

Azure DevTest Labs のアーティファクトとは、*アクション* (VM 作成時にソフトウェアをインストールするアクション、スクリプトやコマンドを実行するアクションなど) を示します。 既定では、公式の Azure DevTest Labs アーティファクト リポジトリからのアーティファクトがラボに含まれます。 ラボに Git アーティファクト リポジトリを追加することで、チームが作成したアーティファクトを含めることができます。 リポジトリは、[GitHub](https://github.com) または [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上にホストできます。

* GitHub リポジトリを作成する方法については、「 [GitHub Bootcamp (GitHub ブートキャンプ)](https://help.github.com/categories/bootcamp/)」を参照してください。
* Git リポジトリを使用して、Team Services プロジェクトを作成する方法については、「 [Connect to Visual Studio Team Services (Visual Studio Team Services への接続)](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)」を参照してください。

次のスクリーン ショットでは、GitHub でアーティファクトを含むリポジトリがどのように表示されるかの例を示します。  
![GitHub アーティファクト リポジトリのサンプル](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>リポジトリ情報および資格情報を取得する
アーティファクト リポジトリをラボに追加するには、最初にリポジトリから特定の情報を取得する必要があります。 次のセクションでは、GitHub および Visual Studio Team Services 上にホストされたアーティファクト レポジトリの情報取得について説明します。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub リポジトリのクローン URL と個人用アクセス トークンの取得
GitHub リポジトリのクローン URL と個人用アクセス トークンを取得するには、次の手順に従います:

1. チームのアーティファクトを含む GitHub リポジトリのホーム ページを参照します。
2. **[複製またはダウンロード]**を選択します。
3. ボタンを選択して **HTTPS クローンの URL** をクリップボードにコピーし、後で使用するために URL を保存します。
4. GitHub の右上隅にあるプロファイルの画像を選択し、 **[設定]**を選択します。
5. **[Personal settings (個人設定)]** メニューの左側で、**[Personal access tokens (個人用アクセス トークン)]** を選択します。
6. **[新しいトークンの生成]**を選択します。
7. **[New personal access token (新しい個人用アクセス トークン)]** ページで、**トークンの説明**を入力し、**[Select scopes (範囲の選択)]** で既定の項目をそのまま使用します。次に **[(Generate Token) トークンの生成]** を選択します。
8. 後で必要となるため、生成されたトークンを保存します。
9. これで、GitHub を閉じることができます。   
10. [アーティファクト リポジトリにラボを接続する](#connect-your-lab-to-the-artifact-repository) のセクションに続きます。

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Visual Studio Team Services リポジトリのクローン URL と個人用アクセス トークンの取得
Visual Studio Team Services リポジトリのクローン URL と個人用アクセス トークンを取得するには、次の手順に従います:

1. チーム コレクションのホーム ページ (例: `https://contoso-web-team.visualstudio.com`) を開き、アーティファクト プロジェクトを選択します。
2. プロジェクトのホーム ページで、 **[コード]**を選択します。
3. クローン URL を表示するには、プロジェクト **[Code (コード)]** ページで、**[Clone (クローン)]** を選択します。
4. このチュートリアルで後ほど必要になるため、URL を保存します。
5. 個人用アクセス トークンを作成するには、ユーザー アカウントのドロップダウン メニューから **[マイ プロファイル]** を選択します。
6. プロファイル情報ページで、 **[セキュリティ]**を選択します。
7. **[Security (セキュリティ)]** タブで **[Add (追加)]** を選択します。
8. **[個人用アクセス トークンの作成]** ページで、次を実行します。
   
   * トークンの **説明** を入力します。
   * **[Expires In (有効期限)]** の一覧から **[180 days (180 日)]** を選択します。
   * **[Accounts (アカウント)]** の一覧から **[All accessible accounts (アクセス可能なすべてのアカウント)]** を選択します。
   * **[All scopes]** オプションを選択します。
   * **[トークンの作成]**を選択します。
9. 完了すると、新しいトークンが **[個人用アクセス トークン]** の一覧に表示されます。 **[トークンのコピー]**を選択し、後ほど必要になるため、トークンの値を保存します。
10. [アーティファクト リポジトリにラボを接続する](#connect-your-lab-to-the-artifact-repository) のセクションに続きます。

## <a name="connect-your-lab-to-the-artifact-repository"></a>アーティファクト リポジトリにラボを接続する
1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。   
4. ラボのブレードで、 **[構成]**を選択します。
5. ラボの **[構成]** ブレードで、**[Artifacts Repositories (アーティファクト リポジトリ)]** を選択します。
6. **[Artifacts Repositories (アーティファクト リポジトリ)]** ブレードで、**[+ Add (+ 追加)]** を選択します。
   
    ![アーティファクト リポジトリ ボタンを追加する](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
7. **[アーティファクト リポジトリ]** ブレードで、次の値を指定します:
   
   * **名前** - リポジトリの名前を入力します。
   * **Git クローン URL** - 先ほど GitHub または Visual Studio Team Services からコピーした Git HTTPS クローン URL を入力します。 
   * **フォルダー パス** - アーティファクト定義ファイルを含むクローン URL に相対フォルダー パスを入力します。
   * **ブランチ** - アイテム定義ファイルを取得する分岐を入力します。
   * **個人用アクセス トークン** - 先ほど GitHub、または Visual Studio Team Services で取得した個人用アクセス トークンを入力します。 
     
     ![アーティファクト リポジトリのブレード](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)
8. [ **保存**] を選択します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [How to troubleshoot failing Artifacts in AzureDevTestLabs (AzureDevTestLabs でアーティファクトの失敗をトラブルシューティングする方法)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Azure Dev Test Lab で ARM テンプレートを使用して既存の AD ドメインに VM を参加させる)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)


