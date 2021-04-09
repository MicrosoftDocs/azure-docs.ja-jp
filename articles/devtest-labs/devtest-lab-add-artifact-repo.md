---
title: Azure DevTest Labs 内のラボへの Git レポジトリの追加 | Microsoft Docs
description: Azure DevTest Labs でカスタム アーティファクト ソースの GitHub または Azure DevOps Services Git リポジトリを追加する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 225aae5e0a017b711f29a47829b06f8bb7a6a8ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483144"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>カスタム アーティファクトと Resource Manager テンプレートを格納するための Git リポジトリの追加

ラボで VM の[カスタム アーティファクトを作成](devtest-lab-artifact-author.md)したり、[Azure Resource Manager テンプレートを使用して、カスタム テスト環境を作成](devtest-lab-create-environment-from-arm.md)したりできます。 チームが作成したアーティファクトや Resource Manager テンプレートを格納するプライベート Git リポジトリを追加する必要があります。 リポジトリは、[GitHub](https://github.com) または [Azure DevOps Services ](https://visualstudio.com) 上でホストできます。

Microsoft では、そのままデプロイすることも、ラボ用にカスタマイズすることもできる、[アーティファクトの GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)を提供しています。 アーティファクトをカスタマイズまたは作成した場合、そのアーティファクトをパブリック リポジトリに格納することはできません。 カスタム アーティファクトや作成したアーティファクトを格納する独自のプライベート リポジトリを作成する必要があります。 

VM を作成したときに、Resource Manager テンプレートを保存し、必要に応じてカスタマイズできます。このテンプレートを後で使用して、他の VM を作成できます。 カスタム Resource Manager テンプレートを格納する独自のプライベート リポジトリを作成する必要があります。  

* GitHub リポジトリを作成する方法については、「 [GitHub Bootcamp (GitHub ブートキャンプ)](https://help.github.com/categories/bootcamp/)」を参照してください。
* Git リポジトリを含む Azure DevOps Services プロジェクトを作成する方法については、[Azure DevOps Services への接続](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)に関する記事をご覧ください。

次の図は、アーティファクトを含むリポジトリの GitHub での表示例を示しています。  

![GitHub アーティファクト リポジトリのサンプル](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>リポジトリ情報および資格情報を取得する
ラボにリポジトリを追加するには、まず、リポジトリから主要な情報を取得します。 以下のセクションでは、GitHub または Azure DevOps Services 上でホストされているリポジトリについて必要な情報を取得する方法について説明します。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub リポジトリのクローン URL と個人用アクセス トークンの取得

1. アーティファクトまたは Resource Manager テンプレートの定義を含む GitHub リポジトリのホーム ページに移動します。
2. **[クローンまたはダウンロード]** を選択します。
3. **[HTTPS clone URL]** ボタンをクリックして、URL をクリップボードにコピーします。 後で使用するために URL を保存します。
4. GitHub の右上隅にあるプロファイル画像を選択し、 **[Settings]** を選択します。
5. **[Personal settings]\(個人設定\)** メニューの左側で、 **[Personal access tokens]\(個人用アクセス トークン\)** を選択します。
6. **[Generate Token]\(新しいトークンの生成\)** を選択します。
7. **[New personal access token]** ページで、 **[Token description]** に説明を入力します。 **[Select scopes]** の既定の項目をそのまま使用し、 **[Generate Token]** を選択します。
8. 生成されたトークンを保存します。 後でこのトークンを使用します。
9. GitHub を閉じます。   
10. [ラボのレポジトリへの接続](#connect-your-lab-to-the-repository)のセクションに進みます。

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Azure Repos のクローン URL と個人用アクセス トークンの取得

1. チーム コレクションのホーム ページ (たとえば、`https://contoso-web-team.visualstudio.com`) に移動し、プロジェクトを選択します。
2. プロジェクトのホーム ページで、 **[コード]** を選択します。
3. クローン URL を表示するには、プロジェクト **[Code (コード)]** ページで、 **[Clone (クローン)]** を選択します。
4. URL を保存します。 後でこの URL を使用します。
5. 個人用アクセス トークンを作成するには、ユーザー アカウントのドロップダウン メニューで **[マイ プロファイル]** を選択します。
6. プロファイル情報ページで、 **[セキュリティ]** を選択します。
7. **[Security (セキュリティ)]** タブで **[Add (追加)]** を選択します。
8. **[個人用アクセス トークンの作成]** ページで、次の操作を行います。
   1. トークンの **説明** を入力します。
   2. **[期限切れまでの日数]** の一覧で、 **[180 日]** を選択します。
   3. **[アカウント]** の一覧で、 **[アクセス可能なすべてのアカウント]** を選択します。
   4. **[読み取り専用]** オプションを選択します。
   5. **[トークンの作成]** を選択します。
9. **[個人用アクセス トークン]** の一覧に新しいトークンが表示されます。 **[トークンのコピー]** を選択し、後ほど必要になるため、トークンの値を保存します。
10. [ラボのレポジトリへの接続](#connect-your-lab-to-the-repository)のセクションに進みます。

## <a name="connect-your-lab-to-the-repository"></a>ラボのレポジトリへの接続
1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[その他のサービス]** を選択し、サービスの一覧で **[DevTest Labs]** を選択します。
3. ラボの一覧でラボを選択します。 
4. **[Configuration and policies]\(構成とポリシー\)**  >  **[リポジトリ]**  >  **[+ 追加]** を選択します。

    ![リポジトリの追加ボタン](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 2 番目の **[レポジトリ]** ページで、以下の情報を指定します。
   1. **名前**。 リポジトリの名前を入力します。
   2. **[Git クローン URL]** : GitHub または Azure DevOps Services から先ほどコピーした Git HTTPS クローン URL を入力します。
   3. **[ブランチ]** : 定義を取得するには、ブランチを入力します。
   4. **[個人用アクセス トークン]** : GitHub または Azure DevOps Services から先ほど取得した個人用アクセス トークンを入力します。
   5. **[フォルダー パス]** : アーティファクトまたは Resource Manager テンプレートの定義を含む、複製 URL を基準としたフォルダー パスを少なくとも 1 つ入力します。 サブディレクトリを指定するときは、フォルダー パスにスラッシュを含めてください。

      ![[リポジトリ] 領域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. **[保存]** を選択します。

### <a name="related-blog-posts"></a>関連するブログ記事
* [DevTest Labs でアーティファクトの失敗のトラブルシューティングを行う](devtest-lab-troubleshoot-artifact-failure.md)
* [DevTest Labs で Resource Manager テンプレートを使用して既存の Active Directory ドメインに VM を参加させる](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
プライベート Git レポジトリを作成したら、必要に応じて、次のどちらかまたは両方を実行できます。
* [カスタム アーティファクト](devtest-lab-artifact-author.md)を格納します。 後でこれらのアーティファクトを使用して、新しい VM を作成できます。
* [Resource Manager テンプレートを使用して、マルチ VM 環境と PaaS リソースを作成](devtest-lab-create-environment-from-arm.md)します。 その後、テンプレートをプライベート リポジトリに格納します。

VM を作成するときに、アーティファクトまたはテンプレートが Git レポジトリに追加されることを確認できます。 これらのアーティファクトやテンプレートは、一覧ですぐに使用可能になります。 ソースを指定する列に、プライベート リポジトリの名前が表示されます。 
