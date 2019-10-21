---
title: クイック スタート:ナレッジ ベースの作成、トレーニング、発行 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベース (KB) は、よくあるご質問や製品マニュアルなど、独自のコンテンツから作成できます。 この例では、BitLocker キー回復に関する質問に答えるよくあるご質問の単純な Web ページから QnA Maker ナレッジ ベースを作成します。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 5d807f185a8cdb181093d37dac35ee2e467fdba9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328091"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>クイック スタート:QnA Maker ナレッジ ベースの作成、トレーニング、発行

QnA Maker のナレッジ ベース (KB) は、よくあるご質問や製品マニュアルなど、独自のコンテンツから作成できます。 この記事には、単純な FAQ Web ページから QnA Maker ナレッジ ベースを作成して、BitLocker キーの回復に関する質問に答える例が含まれています。

おしゃべりのパーソナリティを含めて、ユーザーを引き付けるナレッジにします。

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-new-qna-maker-knowledge-base"></a>新しい QnA Maker ナレッジ ベースを作成する

1. ご自分の Azure の資格情報を使用して [QnAMaker.ai](https://QnAMaker.ai) ポータルにサインインします。

1. QnA Maker ポータルで、 **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択します。

1. **[Create]\(作成\)** ページで **[Create a QnA service]\(QnA サービスの作成)** を選択します。 サブスクリプションで QnA Maker サービスを設定するため、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) にリダイレクトされます。 

1. QnA Maker ポータルで、ドロップダウン リストから QnA Maker サービスを選択します。 新しい QnA Maker サービスを作成した場合は、必ずページを更新してください。

   ![QnA Maker サービス ナレッジ ベースの選択のスクリーン ショット](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. ナレッジ ベースに **My Sample QnA KB** という名前を付けます。

1. 次の URL でサンプルの Word 文書を追加します。 

    `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`

1. [`+ Add URL`] を選択します。

1. " **_プロ_" のおしゃべり**を KB に追加します。 

1. **[Create your KB]\(KB の作成\)** を選択します。

    抽出プロセスでは、ドキュメントを読み取って質問と回答を識別します。これには数分かかります。

    QnA Maker がナレッジ ベースを正常に作成すると、 **[Knowledge base]\(ナレッジ ベース\)** ページが開きます。 このページで、ナレッジ ベースの内容を編集することができます。

## <a name="add-a-new-question-and-answer-set"></a>新しい質問と回答のセットを追加する

1. QnA Maker ポータルの **[Edit]\(編集\)** ページで、 **[Add QnA pair]\(Add QnA ペアの追加\)** を選択します。
1. 次の質問を追加します。 

    `How many Azure services are used by a knowledge base?`

1. "_マークダウン_" で書式設定された回答を追加します。

    ` * Azure QnA Maker service\n* Azure Search\n* Azure web app\n* Azure app plan`

    ![ 質問をテキストとして追加し、回答をマークダウンで書式設定して追加します。](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    マークダウン記号 `*` は、箇条書きに使用されます。 `\n` は、改行に使用されます。  

    **[Edit]\(編集\)** ページには、マークダウンが表示されます。 後で **[Test]\(テスト\)** パネルを使用すると、マークダウンが正しく表示されることを確認できます。 

## <a name="save-and-train"></a>保存してトレーニング

右上の **[Save and train]\(保存してトレーニング\)** を選択し、編集内容を保存して QnA Maker のモデルをトレーニングします。 保存しないと、編集した内容は保持されません。

## <a name="test-the-knowledge-base"></a>ナレッジ ベースをテストする

1. QnA Maker ポータルの右上にある **[テスト]** を選択して、行った変更が反映されたかどうかをテストします。 
1. テキスト ボックスにユーザー クエリの例を入力します。 

    `How many Azure services are used by a knowledge base?`  

    ![ テキスト ボックスにユーザー クエリの例を入力します。 ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. **[検査]** を選択し、詳細に応答を確認します。 テスト ウィンドウは、ナレッジ ベースへの変更を、発行前にテストする際に使用します。

1. **[Test]\(テスト\)** パネルを閉じるには、もう一度 **[Test]\(テスト\)** を選択します。

## <a name="publish-the-knowledge-base"></a>ナレッジ ベースの公開

ナレッジ ベースを発行すると、ナレッジ ベースのコンテンツが、Azure Search の `test` インデックスから `prod` インデックスに移動されます。

![ナレッジ ベースのコンテンツ移動のスクリーン ショット](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. QnA Maker ポータルで、 **[発行]** を選択します。 確認のため、ページの **[発行]** を選択します。

    QnA Maker サービスが正常に発行されました。 お使いのアプリケーションまたはボット コードで、エンドポイントを使用できます。

    ![正常な発行のスクリーンショット](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>ボットの作成

発行後は、 **[発行]** ページからボットを作成できます。 

* 複数のボットを迅速に作成し、個々のボットはリージョンや価格プランが異なっていてもすべて同じナレッジ ベースを指すことができます。 
* ナレッジ ベースのボットを 1 つだけにする場合は、 **[View all your bots on the Azure portal]\(Azure portal 上で自分のボットをすべて表示する\)** リンクを使用して、ご自分の現在のボットの一覧を表示します。 

ナレッジ ベースに変更を加えて再発行しても、ボットに対して追加のアクションを実行する必要はありません。 ナレッジ ベースと連携するように既に構成されており、ナレッジ ベースに対する今後のすべての変更に対応します。 ナレッジ ベースを発行するたびに、それに接続されているすべてのボットが自動的に更新されます。

1. QnA Maker ポータルの **[発行]** ページで、 **[Create bot]\(ボットの作成\)** を選択します。 このボタンは、ナレッジ ベースの発行後にのみ表示されます。

    ![ボットの作成のスクリーンショット](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Azure portal の新しいブラウザー タブが開き、Azure Bot Service の作成ページが表示されます。 Azure Bot Service を構成します。 
    
    * ボットを作成するときに、Azure portal 上で次の設定を変更しないでください。 これらはご自分の既存のナレッジ ベースのために事前に設定されています。 
        * QnA 認証キー
        * App Service プランと場所
    * ボットと QnA Maker は、Web App Service プランを共有できますが、Web アプリを共有することはできません。 そのため、ボットの**アプリ名**は、QnA Maker サービスのアプリ名とは異なる必要があります。 

1. ボットが作成された後、 **[Bot service]\(ボット サービス\)** リソースを開きます。 
1. **[Bot Management]\(ボット管理\)** で、 **[Test in Web Chat]\(Web チャットでのテスト\)** を選択します。
1. チャット プロンプトの **[Type your message]\(メッセージを入力\)** で、以下のように入力します。

    `Azure services?`

    チャット ボットが、ナレッジ ベースからの回答で応答します。 

    ![テスト Web チャットにユーザー クエリを入力します。](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure portal で、QnA Maker および Bot Framework のリソースをクリーンアップします。 

## <a name="next-steps"></a>次の手順

詳細:

* [回答でのマークダウン形式](../concepts/data-sources-supported.md)
* [Markdown のテスト](../concepts/data-sources-supported.md#testing-your-markdown)
* QnA Maker の[データ ソース](../Concepts/data-sources-supported.md)。 
* [ボット リソースの構成設定](../tutorials/create-qna-bot.md)。

> [!div class="nextstepaction"]
> [メタデータによる質問の追加](add-question-metadata-portal.md)

