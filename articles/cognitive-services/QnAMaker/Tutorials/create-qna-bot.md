---
title: チュートリアル:QnA ボット - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: 既存のナレッジ ベース用の発行ページから、QnA チャット ボットを作成します。 このボットでは、Bot Framework SDK v4 が使用されます。 ボットを構築するためにコードを記述する必要はありません。すべてのコードが自動的に提供されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ea6e0d266c181d930f3d18171b09d222e53da7ab
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390901"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>チュートリアル:Azure Bot Service v4 を使用して QnA ボットを作成する

既存のナレッジ ベース用の**発行**ページから QnA チャット ボットを作成します。 このボットでは、Bot Framework SDK v4 が使用されます。 ボットを構築するためにコードを記述する必要はありません。すべてのコードが自動的に提供されます。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 既存のナレッジ ベースから Azure ボット サービスを作成する
> * コードが動作していることを確認するためにボットとチャットする 

## <a name="prerequisites"></a>前提条件

このチュートリアル用に公開済みのナレッジ ベースが必要です。 それがない場合は、[KB の作成と KB から質問に回答](create-publish-query-in-portal.md)に関するチュートリアルの手順に従って、質問と回答が含まれる QnA Maker ナレッジ ベースを作成します。

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>QnA ボットの作成

ナレッジ ベースのクライアント アプリケーションとしてボットを作成します。 

1. QnA Maker ポータルで、**発行**ページに進み、ご自分のナレッジ ベースを発行します。 **[ボットを作成する]** を選択します。 

    ![QnA Maker ポータルで発行ページに進み、ご自分のナレッジ ベースを発行します。 [ボットを作成する] を選択します。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Azure portal が開き、ボットの作成構成が表示されます。

1.  ボットを作成するための設定を入力します。

    |Setting|値|目的|
    |--|--|--|
    |ボット名|`my-tutorial-kb-bot`|これは、ボット用の Azure リソース名です。|
    |Subscription|目的を参照。|QnA Maker リソースを作成するために使用したのと同じサブスクリプションを選択します。|
    |Resource group|`my-tutorial-rg`|ボット関連の Azure リソースすべてに使用されるリソース グループ。|
    |Location|`west us`|ボットの Azure リソースの場所。|
    |Pricing tier|`F0`|Azure ボット サービス用の Free レベル。|
    |アプリの名前|`my-tutorial-kb-bot-app`|これは、ご利用のボットのみをサポートする Web アプリです。 これは、ご利用の QnA Maker サービスで既に使用されている名前と同じにしないでください。 QnA Maker の Web アプリを他のリソースと共有することはサポートされていません。|
    |SDK 言語|C#|これは、ボット フレームワーク SDK によって使用される基になるプログラミング言語です。 使用する選択肢は、[C#](https://github.com/Microsoft/botbuilder-dotnet) または [Node.js](https://github.com/Microsoft/botbuilder-js) です。|
    |QnA 認証キー|**変更しない**|この値は自動的に入力されます。|
    |App Service プラン/場所|**変更しない**|このチュートリアルでは、場所は重要ではありません。|
    |Azure Storage|**変更しない**|会話データは Azure Storage テーブルに格納されます。|
    |Application Insights|**変更しない**|ログ記録は、Application Insights に送信されます。|
    |Microsoft アプリ ID|**変更しない**|Active Directory ユーザーとパスワードが必須です。|

    ![これらの設定を使用して、ナレッジ ベース ボットを作成します。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    ボット作成プロセスの通知によって成功が報告されるまで、数分をお待ちください。

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>ボットとチャットする

1. Azure portal で、通知から新しいボット リソースを開きます。 

    ![Azure portal で、通知から新しいボット リソースを開きます。](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. **[Bot management]\(ボット管理\)** で、 **[Test in Web Chat]\(Web チャットでのテスト\)** を選択し、「`How large can my KB be?`」と入力します。 ボットの応答内容は次のとおりです。 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![新しいナレッジ ベースをテストします。](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Azure ボットの詳細については、「[QnA Maker を使用して質問に回答する](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルのボットを完了したら、Azure portal でボットを削除します。 

ボットのリソースとして新しいリソース グループを作成している場合は、そのリソース グループを削除します。 

新しいリソース グループを作成していない場合は、ボットに関連付けられているリソースを検索する必要があります。 ボットとボット アプリから成る名前で検索するのが最も簡単です。 ボット リソースには次のものがあります。

* App Service プラン
* Search Service
* Cognitive Service
* App Service
* 必要に応じて、Application Insights サービスと Application Insights データ用のストレージも含まれることがあります


## <a name="related-to-qna-maker-bots"></a>QnA Maker ボット関連

* [ボットのサンプル](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support)として、QnA Maker ポータルで使用されている QnA Maker ヘルプ ボットを利用できます。
    ![QnA Maker ヘルプ ボットのアイコンは赤色のロボット](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Healthcare ボット](https://docs.microsoft.com/HealthBot/qna_model_howto)は、その[言語モデル](https://docs.microsoft.com/HealthBot/qna_model_howto)の 1 つとして QnA Maker を使用します。


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [概念: ナレッジ ベース](../concepts/knowledge-base.md)

## <a name="see-also"></a>関連項目

- [ナレッジ ベースを管理する](https://qnamaker.ai)
- [さまざまなチャネルでボットを有効にする](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
