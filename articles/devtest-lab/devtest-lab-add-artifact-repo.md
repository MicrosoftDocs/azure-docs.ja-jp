---
title: "Azure DevTest Labs 内のラボへの Git レポジトリの追加 | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 053f92a65f9ae29154d471fd22ee842620b4f273
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates"></a>カスタム アーティファクトおよび Azure Resource Manager テンプレートを格納するための Git リポジトリの追加

ラボ内の VM 用の[カスタム アーティファクトを作成する](devtest-lab-artifact-author.md)か、または [Azure Resource Manager テンプレートを使用してカスタム テスト環境を作成する](devtest-lab-create-environment-from-arm.md)場合は、チームが作成するアーティファクトまたは Azure Resource Manager テンプレートを格納するためのプライベート Git レポジトリも追加する必要があります。 リポジトリは、[GitHub](https://github.com) または [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上にホストできます。

弊社では、そのままデプロイするか、またはラボ用にカスタマイズできる[アーティファクトの Github レポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)を提供しています。 アーティファクトをカスタマイズまたは作成した場合、それらはパブリック レポジトリには格納できません。独自のプライベート レポジトリを作成する必要があります。 

VM を作成した場合は、Azure Resource Manager テンプレートを保存したり、必要に応じてカスタマイズしたり、後で使用して他の VM を容易に作成したりできます。 カスタム Azure Resource Manager テンプレートを格納するための独自のプライベート レポジトリを作成する必要があります。  

* GitHub リポジトリを作成する方法については、「 [GitHub Bootcamp (GitHub ブートキャンプ)](https://help.github.com/categories/bootcamp/)」を参照してください。
* Git リポジトリを使用して、Team Services プロジェクトを作成する方法については、「 [Connect to Visual Studio Team Services (Visual Studio Team Services への接続)](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)」を参照してください。

次のスクリーン ショットでは、GitHub でアーティファクトを含むリポジトリがどのように表示されるかの例を示します。  
![GitHub アーティファクト リポジトリのサンプル](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>リポジトリ情報および資格情報を取得する
ラボにレポジトリを追加するには、最初にそのレポジトリから特定の情報を取得する必要があります。 次のセクションでは、GitHub および Visual Studio Team Services 上にホストされたレポジトリからこれらの情報を取得する方法について説明します。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub リポジトリのクローン URL と個人用アクセス トークンの取得
GitHub リポジトリのクローン URL と個人用アクセス トークンを取得するには、次の手順に従います:

1. アーティファクトまたは Azure Resource Manager テンプレート定義を含む GitHub レポジトリのホーム ページを参照します。
2. **[複製またはダウンロード]**を選択します。
3. ボタンを選択して **HTTPS クローンの URL** をクリップボードにコピーし、後で使用するために URL を保存します。
4. GitHub の右上隅にあるプロファイルの画像を選択し、 **[設定]**を選択します。
5. **[Personal settings (個人設定)]** メニューの左側で、**[Personal access tokens (個人用アクセス トークン)]** を選択します。
6. **[新しいトークンの生成]**を選択します。
7. **[New personal access token (新しい個人用アクセス トークン)]** ページで、**トークンの説明**を入力し、**[Select scopes (範囲の選択)]** で既定の項目をそのまま使用します。次に **[(Generate Token) トークンの生成]** を選択します。
8. 後で必要となるため、生成されたトークンを保存します。
9. これで、GitHub を閉じることができます。   
10. [ラボのレポジトリへの接続](#connect-your-lab-to-the-repository)のセクションに進みます。

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Visual Studio Team Services リポジトリのクローン URL と個人用アクセス トークンの取得
Visual Studio Team Services リポジトリのクローン URL と個人用アクセス トークンを取得するには、次の手順に従います:

1. チーム コレクションのホーム ページ (たとえば、`https://contoso-web-team.visualstudio.com`) を開き、プロジェクトを選択します。
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
10. [ラボのレポジトリへの接続](#connect-your-lab-to-the-repository)のセクションに進みます。

## <a name="connect-your-lab-to-the-repository"></a>ラボのレポジトリへの接続
1. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。   
4. 左側のパネルで、**[Configuration and policies]\(構成とポリシー\)** を選択します。
5. ラボの **[Configuration and policies]\(構成とポリシー\)** 領域で、**[レポジトリ]** を選択します。
6. **[レポジトリ]** 領域で、**[+ 追加]** を選択します。

    ![レポジトリの追加ボタン](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. 2 番目の **[レポジトリ]** ページで、以下の情報を指定します。

   * **名前** - リポジトリの名前を入力します。
   * **Git クローン URL** - 先ほど GitHub または Visual Studio Team Services からコピーした Git HTTPS クローン URL を入力します。
   * **分岐** - 定義を取得する分岐を入力します。
   * **個人用アクセス トークン** - 先ほど GitHub、または Visual Studio Team Services で取得した個人用アクセス トークンを入力します。
   * **フォルダ パス** - アーティファクトまたは Azure Resource Manager テンプレート定義を含む複製 URL を基準とした少なくとも 1 つのフォルダ パスを入力します。 サブディレクトリを指定する場合は、フォルダ パスにフォワードスラッシュを含めるようにしてください。

     ![[リポジトリ] 領域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. [ **保存**] を選択します。

## <a name="next-steps"></a>次のステップ
プライベート Git レポジトリを作成したら、必要に応じて、次のどちらかまたは両方を実行できます。
* [カスタム アーティファクト](devtest-lab-artifact-author.md)を格納します。これは、後で新しい VM を作成するために使用できます。
* [Azure Resource Manager テンプレートを使用して複数 VM 環境および PaaS リソースを作成](devtest-lab-create-environment-from-arm.md)してから、それらのテンプレートをプライベート レポジトリ内に格納します。

VM を作成するときに、アーティファクトまたはテンプレートが Git レポジトリに追加されることを確認できます。 これらはアーティファクトまたはテンプレートのリストで直ちに使用可能になり、ソースを指定する列にプライベート レポジトリの名前が表示されます。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>関連するブログ記事
* [Azure DevTest Labs でアーティファクトの失敗をトラブルシューティングする方法](devtest-lab-troubleshoot-artifact-failure.md)
* [Azure DevTest Labs で Resource Manager テンプレートを使用して既存の AD ドメインに VM を参加させる](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

