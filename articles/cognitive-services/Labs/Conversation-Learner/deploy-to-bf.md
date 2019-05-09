---
title: Conversation Learner ボットをデプロイする方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner ボットをデプロイする方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ae984cc2e0f43b81b8aa2f08b3944886733c9054
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994636"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Conversation Learner ボットをデプロイする方法

このドキュメントでは、ローカルまたは Azure のどちらかに、Conversation Learner ボットをデプロイする方法について説明します。

## <a name="prerequisite-determine-the-model-id"></a>前提条件: モデル ID を決定する 

Conversation Learner UI の外部でボットを実行するには、ボットが使用する Conversation Learner モデル ID、つまり、Conversation Learner クラウド内の機械学習モデルの ID を設定する必要があります。  (これに対して、Conversation Learner UI 経由でボットを実行している場合は、UI がモデル ID を選択します)。  

モデル ID を取得する方法を次に示します。

1. ボットと Conversation Learner UI を起動します。  詳細な手順については、クイック スタート ガイドを参照してください。ここでは、手順の概要を示します。

    1 つのコマンド ウィンドウで、次を実行します。

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    もう 1 つのコマンド ウィンドウで、次を実行します。

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. ブラウザーで `http://localhost:5050` を開きます。 

3. ID を取得する Conversation Learner モデルをクリックします。

4. 左側のナビゲーション バーにある [設定] をクリックします。

5. ページの上部付近に "モデル ID" の GUID が表示されます。

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>オプション 1:ローカルで実行する Conversation Learner ボットをデプロイする

このオプションでは、ローカル コンピューターにボットをデプロイし、Bot Framework エミュレーターを使用してこのボットにアクセスできる方法を示します。

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Conversation Learner UI 外部でのアクセス用にボットを構成する

ボットをローカルで実行する場合、アプリケーション ID をボットの `.env` ファイルに追加します。

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

次に、ボットを起動します。

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

これで、ボットがローカルで実行されるようになりました。  Bot Framework エミュレーターを使って、このボットにアクセスできます。

### <a name="download-and-install-the-emulator"></a>エミュレーターをダウンロードしてインストールする

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>エミュレーターをボットに接続する

1. エミュレーターの左上にある [Enter your endpoint URL]\(エンドポイント URL を入力してください\) ボックスに、「`http://127.0.0.1:3978/api/messages`」と入力します。  他のフィールドを空白のままにして、[接続] をクリックします。

2. これで、ボットと会話できるようになりました。

## <a name="option-2-deploy-to-azure"></a>オプション 2:Deploy to Azure (Azure へのデプロイ)

他のボットを公開する場合とほぼ同じ方法で、Conversation Learner ボットを公開します。 大まかな手順としては、ホストされている Web サイトにコードをアップロードし、適切な設定値を設定して、さまざまなチャネルでボットを登録します。 詳細な手順は、Azure Bot Service を使用してボットを公開する方法を示した動画に示されています。

ボットがデプロイされ実行されたら、Azure Bot Channel Registration を使用して Facebook、Teams、Skype などの さまざまなチャネルにボットを接続できます。 このプロセスの詳細については、 https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration を参照してください。

Conversation Learner Bot を Azure にデプロイするための手順を以下に示します。  これらの手順では、Azure DevOps Services、GitHub、BitBucket、OneDrive などのクラウド ベースのソースからご自分のボット ソースを使用できることを前提とし、継続的デプロイ用にボットを構成します。

1.  https://portal.azure.com で、Azure Portal にログインします。

2. 次の手順を実行して、新しい "Web App Bot" リソースを作成します。 

    1. Bot に名前を付けます
    2. [Bot Template]\(ボットのテンプレート\) をクリックし、[Node.js] を選択し、[Basic] を選択してから、[選択] ボタンをクリックします
    3. [作成] をクリックして、Web App Bot を作成します
    4. Web App Bot リソースが作成されるまで待機します

3. Azure Portal で、先ほど作成した Web App Bot リソースを編集します。

   1. 左にある [アプリケーション設定] ナビゲーション項目をクリックします
   1. [アプリ設定] セクションまで下へスクロールします
   2. 以下の設定を追加します

       環境変数 | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | アプリケーション ID の GUID。モデルの [設定] の下にある Conversation Learner UI から取得される>
       LUIS_AUTHORING_KEY               | このモデルの LUIS オーサリング キー
       LUIS_SUBSCRIPTION_KEY            | 必須ではありませんが、公開されたボットで作成クォータの使用を回避するために推奨されています。
    
   4. ページの上部付近ある [保存] をクリックします
   5. 左にある [ビルド] ナビゲーション項目を開きます
   6. [Configure continuous deployment]\(継続的なデプロイを構成する\) をクリックします 
   7. デプロイにある [設定] アイコンをクリックします
   8. [必須設定] をクリックします
   9. ボット コードが使用可能なソースを選択し、そのソースを構成します
