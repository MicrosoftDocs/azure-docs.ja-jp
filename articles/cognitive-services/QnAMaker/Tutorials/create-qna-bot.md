---
title: QnA ボット - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Azure portal で Azure Bot Service v3 を使用して QnA ボットを構築する手順について説明します。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.openlocfilehash: 218103f2c75ec1016a997c259767ccd011191fab
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879612"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>チュートリアル: Azure Bot Service v3 による QnA ボットの作成

このチュートリアルでは、[Azure portal](https://portal.azure.com) でコードを書くことなく Azure Bot Service v3 を使用して QnA ボットを構築する手順について説明します。 公開されたナレッジ ベース (KB) のボットへの接続は、ボット アプリケーションの設定を変更するだけです。 

> [!Note] 
> このトピックは、ボット SDK のバージョン 3 を対象にしています。 バージョン 4 については、[こちら](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)をご覧ください。 

**このチュートリアルでは、以下の内容を学習します。**

<!-- green checkmark -->
> [!div class="checklist"]
> * QnA Maker テンプレートを使用して Azure Bot Service を作成する
> * コードが動作していることを確認するためにボットとチャットする 
> * 公開された KB をボットに接続する
> * 問題のボットをテストする

この記事では、無料の QnA Maker [サービス](../how-to/set-up-qnamaker-service-azure.md)を使用することができます。

## <a name="prerequisites"></a>前提条件

このチュートリアル用に公開済みのナレッジ ベースが必要です。 ない場合は、「[ナレッジ ベースの作成](../How-To/create-knowledge-base.md)」の手順に従って、質問と回答が含まれる QnA Maker サービスを作成します。

## <a name="create-a-qna-bot"></a>QnA ボットの作成

1. Azure Portal で、**[リソースの作成]** を選択します。

    ![ボット サービスの作成](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 検索ボックスで、「**Web アプリ ボット**」を検索します。

    ![ボット サービスの選択](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. **[ボット サービス]** で、必要な情報を指定します。

    - **[アプリ名]** にボットの名前を設定します。 この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (mynotesbot.azurewebsites.net など)。
    - [サブスクリプション]、[リソース グループ]、[App Service プラン]、[場所] を選択します。

4. v3 テンプレートを使用するには、SDK バージョンは **SDK v3** を、SDK 言語は **C#** または **Node.js** を選択します。

    ![ボット SDK 設定](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. ボット テンプレートのフィールドで **[Question and Answer]\(質問と回答\)** テンプレートを選択し、**[選択]** を選択してテンプレート設定を保存します。

    ![ボットのサービス テンプレートの選択を保存する](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. 設定を確認して **[作成]** を選択します。 これによって、ボット サービスが作成され、Azure にデプロイされます。

    ![ボットを作成する](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. ボット サービスがデプロイされたことを確認します。

    - **[通知]** (Azure portal の上端にあるベル アイコン) を選択します。 通知は、**[デプロイが開始されました]** から **[デプロイメントに成功しました]** に変わります。
    - 通知が **[デプロイメントに成功しました]** に変わったら、その通知で **[リソースに移動]** を選択します。

## <a name="chat-with-the-bot"></a>ボットとチャットする

**[リソースに移動]** を選択すると、ボットのリソースに移動します。

**[Test in Web Chat]\(Web チャットでのテスト\)** を選択して、Web チャット ウィンドウを開きます。 Web チャットに "hi" と入力します。

![QnA ボットの Web チャット](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

ボットは、"Please set QnAKnowledgebaseId and QnASubscriptionKey in App Settings.  この応答は、QnA ボットがメッセージを受信したが、QnA Maker のナレッジ ベースはまだボットに関連付けられていないことを裏付けています。 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>QnA Maker ナレッジ ベースをボットに接続する

1. **[アプリケーション設定]** を開き、**QnAKnowledgebaseId**、**QnAAuthKey**、および **QnAEndpointHostName** の各フィールドを編集して、QnA Maker ナレッジ ベースの値を格納します。

    ![アプリケーション設定](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. QnA Maker ポータルで、ナレッジ ベースの [設定] タブから、ナレッジ ベース ID、ホスト URL、およびエンドポイント キーを取得します。

   - [QnA Maker](https://qnamaker.ai) にサインインする
   - ナレッジ ベースに移動する
   - **[設定]** タブを選択します
   - まだの場合は、ナレッジ ベースを**発行**します。

     ![QnA Maker の値](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

## <a name="test-the-bot"></a>ボットのテスト

Azure portal で、**[Test in Web Chat]\(Web チャットでのテスト\)** を選択してボットをテストします。 

![QnA Maker ボット](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA ボットがナレッジ ベースから回答します。

## <a name="related-to-qna-maker-bots"></a>QnA Maker ボット関連

* [ボットのサンプル](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot)として、QnA Maker ポータルで使用されている QnA Maker ヘルプ ボットを利用できます。
    ![QnA Maker ヘルプ ボットのアイコンは赤色のロボット](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Healthcare ボット](https://docs.microsoft.com/HealthBot/qna_model_howto)は、その[言語モデル](https://docs.microsoft.com/HealthBot/qna_model_howto)の 1 つとして QnA Maker を使用します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルのボットを完了したら、Azure portal でボットを削除します。 ボット サービスには次のようなサービスがあります。

* App Service プラン
* Search Service
* Cognitive Service
* App Service
* 必要に応じて、Application Insights サービスと Application Insights データ用のストレージも含まれることがあります

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [概念: ナレッジ ベース](../concepts/knowledge-base.md)

## <a name="see-also"></a>関連項目

- [ナレッジ ベースを管理する](https://qnamaker.ai)
- [さまざまなチャネルでボットを有効にする](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
