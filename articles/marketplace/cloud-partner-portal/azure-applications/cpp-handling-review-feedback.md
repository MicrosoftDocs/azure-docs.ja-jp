---
title: Azure アプリケーションに対するレビュー フィードバックを処理する | Azure Marketplace
description: Azure DevOps を使用して、Azure Marketplace の Azure アプリケーション オファーに対するレビュー フィードバックを処理する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: 1a45af2cb5eed8daa4b50bb6f0b504f9653c827a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068955"
---
# <a name="handling-review-feedback"></a>レビュー フィードバックの処理

この記事では、Microsoft Azure Marketplace レビュー チームが使用する Azure DevOps 環境にアクセスする方法について説明します。  **Microsoft のレビュー** ステップで Azure アプリケーション オファーに重大な問題が見つかった場合は、このシステムにサインインして、それらの問題に関する詳細な情報 (レビュー フィードバック) を表示できます。  これらの問題をすべて修正した後、Azure Marketplace に引き続きオファーを発行するには、オファーを再送信する必要があります。  次の図は、このフィードバック プロセスと発行プロセスの関係を示しています。

![発行手順と Azure DevOps フィードバック](./media/pub-flow-vsts-access.png)

通常、レビューの問題はプルリクエスト (PR) と呼ばれます。  各 PR は、問題に関する詳細を含むオンラインの [Azure DevOps](https://azure.microsoft.com/services/devops/) (旧称 Visual Studio Team Services (VSTS)) 項目にリンクされています。  次の図に、レビュー PR の参照の例を示します。  複雑な状況では、レビューおよびサポート チームからお客様にメールが送信されることもあります。 

![レビューのフィードバックが表示される [状態] タブ](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Azure DevOps アクセス

レビュー フィードバックで参照されている PR 項目を表示するには、最初にパブリッシャーに適切な承認が付与されている必要があります。  そうしないと、新しいパブリッシャーは、PR を表示しようとしたときに `401 - Not Authorized` 応答ページを受け取ります。  この Azure DevOps リポジトリへのアクセスを要求するには、次の手順を実行します。

1. 次の情報を収集します。
    - パブリッシャー名または ID
    - オファーの種類 (Azure アプリ)、オファー名、SKU ID
    - プルリクエストリンク。次に例を示します。`https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  この URL は、通知メッセージまたは 401 応答ページのアドレスから取得できます。
    - アクセスを付与する発行元組織の個人のメール アドレス。  この一覧には、Cloud パートナー ポータルでパブリッシャーとして登録するときに指定した所有者アドレスを含める必要があります。
2. サポート インシデントを作成する  Cloud パートナー ポータルのタイトル バーで **[ヘルプ]** ボタンを選択し、メニューから **[サポート]** を選択します。  既定の Web ブラウザーが起動し、Microsoft の新しいサポート インシデント ページに移動します  (場合によっては、最初にサインインする必要があります)。
3. **[問題の種類]** に **[Marketplace Onboarding]\(マーケットプレースのオンボード\)** 、 **[カテゴリ]** に **[Access problem]\(アクセスの問題\)** を指定し、 **[Start request]\(要求の開始\)** を選択します。

    ![サポート チケットのカテゴリ](./media/support-incident1.png)

4. **[手順 1/2]** ページに連絡先情報を入力し、 **[続行]** を選択します。
5. **[手順 2/2]** ページにインシデントのタイトル (`Request Azure DevOps access` など) を指定し、最初の手順 (前述) で収集した情報を入力します。  条項を読み、同意してから、 **[送信]** を選択します。

インシデントの作成に成功すると、確認ページが表示されます。  後で参照できるように、このページ上の確認情報を保存します。  Microsoft サポート チームは、数営業日以内にお客様のアクセス要求に返信します。


## <a name="reviewing-the-pull-request"></a>プルリクエストの確認 

プルリクエストに記述されている問題を確認するには、次の手順を使用します。

1. **[Publishing steps]\(発行手順\)** フォームの **[Microsoft review]\(Microsoft のレビュー\)** セクションで、PR リンクをクリックしてブラウザーを起動し、この PR の **[概要]** (ホーム) ページに移動します。  次の図は、Contoso のサンプル アプリ オファーの重大な問題のホーム ページの例を示しています。  このページには、Azure アプリで検出されたレビューの問題に関する有用な概要情報が含まれています。  

    [![pull request のホーム ページ](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *拡大するには画像をクリックしてください。*
    
2. (省略可能) ウィンドウの右側にある **[ポリシー]** セクションで、問題のメッセージをクリックして (この例では、**Policy Validation failed\(ポリシーの検証に失敗しました\)** )、問題の低レベルの詳細 (関連付けられているログ ファイルを含む) を調査します。  通常、エラーはログ ファイルの下部に表示されます。

3. ホーム ページの左側にあるメニューで、**ファイル**を選択して、このオファーの技術資産を構成するリスト ファイルを表示します。  検出された重大な問題について説明するコメントが Microsoft のレビュー担当者によって追加されているはずです。  次の例では、2 つの問題が検出されました。 

    [![pull request のホーム ページ](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *拡大するには画像をクリックしてください。*

4. 左側のツリーで各コメント ノードをクリックして、周囲のコードのコンテキスト内のコメントに移動します。  チームのプロジェクト内のソース コードを修正し、コメントで説明されている問題を修正します。

> [!Note]
> レビュー チームの Azure DevOps 環境内でオファーの技術資産を編集することはできません。  パブリッシャーの場合、これは含まれているソース コードに対して読み取り専用の環境です。  ただし、Microsoft のレビュー チームのために、コメントに対する返信を残しておくことができます。

   次の例では、パブリッシャーが最初の問題をレビューし、修正して、返信しました。

   ![最初の修正およびコメントの返信](./media/first-comment-reply.png)


## <a name="next-steps"></a>次の手順

レビュー PR に記述されている重大な問題を修正したら、[Azure アプリ オファーを再発行する](./cpp-publish-offer.md)必要があります。
