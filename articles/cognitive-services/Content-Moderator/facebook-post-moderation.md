---
title: チュートリアル:Facebook のコンテンツをモデレートする - Content Moderator
titlesuffix: Azure Cognitive Services
description: このチュートリアルでは、機械学習ベースの Content Moderator を使用して Facebook の投稿とコメントをモデレートする方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: dd06330e82850cc44bc0f4d36ba7caf596ace939
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603517"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>チュートリアル:Azure Content Moderator で Facebook の投稿とコマンドをモデレートする

このチュートリアルでは、Azure Content Moderator を使用して Facebook ページの投稿とコメントをモデレートする方法について説明します。 訪問者が投稿したコンテンツは Facebook から Content Moderator サービスに送信されるようになります。 その後、コンテンツのスコアとしきい値に応じて、Content Moderator のワークフローは、コンテンツを公開するか、レビュー ツール内にレビューを作成します。 このシナリオの例が動作しているところは、[Build 2017 のデモ ビデオ](https://channel9.msdn.com/Events/Build/2017/T6033)でご覧いただけます。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Content Moderator チームを作成します。
> * Content Moderator と Facebook からの HTTP イベントをリッスンする Azure Functions を作成します。
> * Facebook アプリケーションを使用して Facebook のページを Content Moderator にリンクします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

この図では、このシナリオの各コンポーネントが示されています。

!["FBListener" を介して Facebook から情報を受信し、"CMListener" を介して情報を送信する、Content Moderator の図](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> 2018 年に、Facebook は Facebook アプリのより厳密な審査を実装しました。 お客様のアプリが Facebook のレビュー チームによってレビューおよび承認されていない場合は、このチュートリアルの手順を完了できません。

## <a name="prerequisites"></a>前提条件

- Content Moderator のサブスクリプション キー。 [Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Content Moderator サービスをサブスクライブし、お客様のキーを取得してください。
- [Facebook アカウント](https://www.facebook.com/)。

## <a name="create-a-review-team"></a>レビュー チームを作成する

[Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)にサインアップしてレビュー チームを作成する方法の手順については、「[Web 上で Content Moderator を試す](quick-start.md)」クイック スタートを参照してください。 **[資格情報]** ページの **[チーム ID]** を書き留めておきます。

## <a name="configure-image-moderation-workflow"></a>画像モデレーション ワークフローを構成する

「[ワークフローの定義、テスト、使用](review-tool-user-guide/workflows.md)」ガイドを参考にして、カスタム画像ワークフローを作成します。 Content Moderator では、このワークフローを使用して自動的に Facebook 上の画像を確認し、一部をレビュー ツールに送信します。 ワークフローの**名前**を記録しておきます。

## <a name="configure-text-moderation-workflow"></a>テキスト モデレーション ワークフローを構成する

もう一度、[ワークフローの定義、テスト、使用](review-tool-user-guide/workflows.md)に関するガイドを参照して、今度はカスタム テキスト ワークフローを作成します。 Content Moderator では、このワークフローを使用して、テキスト コンテンツを自動的に確認します。 ワークフローの**名前**を記録しておきます。

![テキスト ワークフローを構成する](images/text-workflow-configure.PNG)

**[Execute Workflow]\(ワークフローの実行\)** ボタンを使用してワークフローをテストします。

![テキスト ワークフローのテスト](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions を作成する

[Azure portal](https://portal.azure.com/) にサインインして、次の手順を実行します。

1. [[Azure Functions]](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) ページの表示に従って Azure Function アプリを作成します。
1. 新しく作成した Function アプリに移動します。
1. アプリ内で **[プラットフォーム機能]** タブに移動し、 **[構成]** を選択します。 次のページの **[アプリケーション設定]** セクションで、 **[新しいアプリケーション設定]** を選択し、次のキー/値ペアを追加します。
    
    | アプリ設定の名前 | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | Content Moderator のチーム ID  | 
    | cm:SubscriptionKey | Content Moderator のサブスクリプション キー - [資格情報](review-tool-user-guide/credentials.md)に関するページを参照してください |
    | cm:Region | Content Moderator のリージョン名 (スペースなし)。 |
    | cm:ImageWorkflow | 画像に対して実行するワークフローの名前 |
    | cm:TextWorkflow | テキストに対して実行するワークフローの名前 |
    | cm:CallbackEndpoint | このガイドで後で作成する CMListener 関数アプリの URL |
    | fb:VerificationToken | お客様が作成するシークレット トークン。Facebook のフィード イベントにサブスクライブするために使用されます |
    | fb:PageAccessToken | Facebook のグラフ API アクセス トークンは無期限なので、代理で投稿の非表示/削除関数を実行させることができます。 このトークンは、後の手順で取得します。 |

    ページ上部にある **[保存]** ボタンをクリックします。

1. **[プラットフォーム機能]** タブに戻ります。左側のウィンドウの **[+]** ボタンを使用して、 **[新しい関数]** ウィンドウを表示します。 作成する関数は、Facebook からイベントを受け取ることになります。

    ![[関数の追加] ボタンが強調表示されている Azure Functions ウィンドウ。](images/new-function.png)

    1. **[Http trigger]\(Http トリガー\)** というタイルをクリックします。
    1. 「**FBListener**」という名前を入力します。 **[Authorization Level]\(承認レベル\)** フィールドは **[Function]\(関数\)** に設定する必要があります。
    1. **Create** をクリックしてください。
    1. **run.csx** の内容を **FbListener/run.csx** の内容で置き換えます

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. **CMListener** という名前の新しい **Http トリガー**関数を作成します。 この関数は Content Moderator からイベントを受け取ります。 **run.csx** の内容を **CMListener/run.csx** の内容で置き換えます

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Facebook ページとアプリを構成する

1. Facebook アプリを作成します。

    ![Facebook 開発者ページ](images/facebook-developer-app.png)

    1. [Facebook 開発者向けサイト](https://developers.facebook.com/)に移動します。
    1. **[My Apps]\(マイ アプリ\)** をクリックします。
    1. 新しいアプリを追加します。
    1. 適当な名前を付けます
    1. **[Webhooks] > [設定]** を選択します
    1. ドロップダウン メニューで **[ページ]** を選択し、 **[Subscribe to this object]\(このオブジェクトをサブスクライブする\)** を選択します
    1. [Callback URL]\(コールバック URL\) に **FBListener Url** を入力し、 **[Function App Settings]\(Function アプリの設定\)** で構成した**トークンを確認**します。
    1. サブスクライブが完了したら、フィードまで下にスクロールして、 **[Subscribe]\(サブスクライブ\)** を選択します。
    1. **フィード**行の **[テスト]** ボタンをクリックして、テスト メッセージを FBListener Azure 関数に送信してから、 **[Send to My Server]\(マイ サーバーに送信\)** ボタンをクリックします。 FBListener で受信される要求が表示されます。

1. Facebook ページを作成します。

    > [!IMPORTANT]
    > 2018 年に、Facebook は Facebook アプリのより厳密な審査を実装しました。 お客様のアプリが Facebook のレビュー チームによってレビューおよび承認されていない場合は、セクション 2、3、4 を実行できません。

    1. [Facebook](https://www.facebook.com/bookmarks/pages) に移動し、**新しい Facebook ページ**を作成します。
    1. Facebook アプリがこのページにアクセスできるようにするには、次の手順を実行します。
        1. [Graph API Explorer](https://developers.facebook.com/tools/explorer/) に移動します。
        1. **[Application]\(アプリケーション\)** を選択します。
        1. **[Page Access Token]\(ページ アクセス トークン\)** を選択し、**Get** 要求を送信します。
        1. 応答の **[Page ID]\(ページ ID\)** をクリックします。
        1. URL に **/subscribed_apps** を付加し、**Get** (空の応答) 要求を送信します。
        1. **Post** 要求を送信します。 **success: true** という応答を受け取ります。

3. 無期限の Graph API アクセス トークンを作成します。

    1. [Graph API Explorer](https://developers.facebook.com/tools/explorer/) に移動します。
    2. **[Application]\(アプリケーション\)** オプションを選択します。
    3. **[Get User Access Token]\(ユーザー アクセス トークンの取得\)** オプションを選択します。
    4. **[Select Permissions]\(アクセス許可の選択\)** で **manage_pages** および **publish_pages** オプションを選択します。
    5. この**アクセス トークン** (短命トークン) は、次の手順で使用します。

4. 次のいくつかの手順では Postman を使用します。

    1. **Postman** を開きます (または[こちら](https://www.getpostman.com/)で入手します)。
    2. 以下の 2 つのファイルをインポートします。
        1. [Postman コレクション](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman 環境](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. 以下の環境変数を更新します。
    
        | キー | 値   | 
        | -------------------- |-------------|
        | appId   | Facebook アプリ識別子をここに挿入します  | 
        | appSecret | Facebook アプリのシークレットをここに挿入します | 
        | short_lived_token | 前の手順で生成した短命のユーザー アクセス トークンを挿入します |
    4. コレクションに含まれている 3 つの API を実行します。 
        1. **[Generate Long-Lived Access Token]\(長命のアクセス トークンの生成\)** を選択し、 **[Send]\(送信\)** をクリックします。
        2. **[Get User ID]\(ユーザー ID の取得\)** を選択し、 **[Send]\(送信\)** をクリックします。
        3. **[Get Permanent Page Access Token]\(永続的なページ アクセス トークンの取得\)** を選択し、 **[Send]\(送信\)** をクリックします。
    5. 応答から **access_token** の値をコピーし、それをアプリ設定 **fb: PageAccessToken** に割り当てます。

このソリューションは Facebook ページに投稿されたすべての画像とテキストを Content Moderator に送信します。 すると、前に構成したワークフローが呼び出されます。 ワークフローに定義されている条件を満たさないコンテンツは、レビュー ツール内のレビューに渡されます。 その他のコンテンツは自動的に公開されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、製品の種類別にタグを付けてレビュー チームがコンテンツ モデレーションに関して情報に基づいた決定を行えるようにすることを目的として、製品画像を分析するプログラムを設定しました。 次は、画像のモデレーションの詳細について学習してください。

> [!div class="nextstepaction"]
> [Image moderation](./image-moderation-api.md)
