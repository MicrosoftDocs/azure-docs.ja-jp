---
title: クイック スタート:カスタム コマンドを作成する (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、ホストされたカスタム コマンド アプリケーションを作成してテストします。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155589"
---
# <a name="quickstart-create-a-custom-command-preview"></a>クイック スタート:カスタム コマンドを作成する (プレビュー)

この記事では、ホストされたカスタム コマンド アプリケーションを作成してテストする方法について説明します。
このアプリケーションでは、"turn on the tv" (テレビをつけて) のような発話を認識し、簡単なメッセージ "Ok, turning on the tv".(了解です。テレビをつけます) で応答します。

## <a name="prerequisites"></a>前提条件

- Speech サブスクリプション

Speech サブスクリプションがない場合は、[Speech Studio](https://speech.microsoft.com/) に移動し、 **[Speech リソースを作成する]** を選択して作成できます。

  > [!div class="mx-imgBorder"]
  > [ ![プロジェクトを作成する](media/custom-speech-commands/create-new-subscription.png) ](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > プレビュー期間中は、westus2 リージョンのみがサポートされます。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>カスタム コマンドを使用するため Speech Studio にアクセスする

1. Web ブラウザーを開き、[Speech Studio](https://speech.microsoft.com/) に移動します。
1. 資格情報を入力してポータルにサインインします。

   - 既定のビューは、音声サブスクリプションの一覧です。
     > [!NOTE]
     > サブスクリプションの選択ページが表示されない場合は、上部のバーの [設定] メニューで [Speech resources]\(音声リソース\) を選択すると表示されます。

1. 音声サブスクリプションを選択し、 **[Go to Studio]\(Studio にアクセス\)** を選択します。
1. **[カスタム コマンド (プレビュー)]** を選択します。

既定のビューは、作成したカスタム コマンド アプリケーションの一覧です。

## <a name="create-a-custom-commands-project"></a>カスタム コマンド プロジェクトを作成する

1. **[新しいプロジェクト]** を選択して新しいプロジェクトを作成します。

   > [!div class="mx-imgBorder"]
   > ![プロジェクトの作成](media/custom-speech-commands/create-new-project.png)

1. プロジェクトの名前と言語を入力します。
1. 作成リソースを選択します。 有効な作成リソースがない場合は、 **[新しいリソースの作成]** を選択して作成します。

   > [!div class="mx-imgBorder"]
   > ![リソースの作成](media/custom-speech-commands/create-new-resource.png)

   1. リソース名、グループ、場所、価格レベルを入力します。

         > [!NOTE]
         > リソース グループを作成するには、[リソース グループ] フィールドに目的のリソース グループ名を入力します。 **[作成]** を選択すると、リソース グループが作成されます。

1. **[作成]** を選択してプロジェクトを作成します。
1. 作成したら、プロジェクトを選択します。

これで、カスタム コマンド アプリケーションの概要が表示されます。

## <a name="update-luis-resources-optional"></a>LUIS リソースを更新する (省略可能)

[新しいプロジェクト] ウィンドウにセットされた作成リソースを更新し、実行時に入力を認識するために使用される予測リソースをセットできます。

> [!NOTE]
> アプリケーションが、オーサリング リソースが提供する 1,000 件の要求を超えた予測を要求する前に、予測リソースをセットする必要があります。

> [!div class="mx-imgBorder"]
> ![LUIS のリソースをセットする](media/custom-speech-commands/set-luis-resources.png)

1. 左側のペインで **[設定]** を選択し、中央のペインから **[LUIS リソース]** を選択して、[LUIS リソース] ペインに移動します。
1. 予測リソースを選択するか、 **[新しいリソースの作成]** を選択して作成します
1. **[保存]** を選びます。

## <a name="create-a-new-command"></a>新しいコマンドを作成する

これで、コマンドを作成できるようになりました。 1つの発話 `turn on the tv` を取得して、メッセージ `Ok, turning on the TV`で応答する例を使用してみましょう。

1. コマンドの横にある `+` アイコンを選択して `TurnOn` という名前を指定し、新しいコマンドを作成します。
1. **[保存]** を選びます。

> [!div class="mx-imgBorder"]
> ![コマンドの作成](media/custom-speech-commands/create-add-command.png)

コマンドは、次のセットです。

| Group            | 説明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 例文 | 発話の例では、ユーザーはこのコマンドを言ってトリガーすることができます。                                                                 |
| パラメーター       | コマンドを完了するために必要な情報                                                                                |
| 完了ルール | コマンドを完了するために実行されるアクション。 たとえば、ユーザーへの応答や、別の web サービスとの通信などです。 |
| 高度なルール   | より具体的または複雑な状況に対処するための追加ルールです。                                                              |

### <a name="add-a-sample-sentence"></a>例文の追加

ここでは、最初に例文を示し、ユーザーが言うことができる文の例を示します。

```
turn on the tv
```

現時点では、パラメーターはないので、完了ルールに移行できます。

### <a name="add-a-completion-rule"></a>完了ルールの追加

次に、完了ルールを追加し、アクションが実行されていることを示す応答をユーザーに返します。

1. [完了ルール] の横にある `+` アイコンを選択して、新しい完了ルールを作成します
1. ルール名を入力します
1. アクションを追加する
   1. [アクション] の横にある `+` アイコンを選択して新しい音声応答アクションを作成し、`SpeechResponse` を選択します
   1. 応答を入力します

   > [!NOTE]
   > 標準テキストはダッシュで始める必要があります。 詳細については、[こちら](https://aka.ms/sc-lg-format)を参照してください

   > [!div class="mx-imgBorder"]
   > ![音声応答の作成](media/custom-speech-commands/create-speech-response-action.png)

1. **[保存]** をクリックしてルールを保存します

> [!div class="mx-imgBorder"]
> ![完了ルールの作成](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 設定    | 推奨値                          | 説明                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 規則の名前  | "ConfirmationResponse"                   | ルールの目的を説明する名前          |
| 条件 | なし                                     | ルールを実行できるタイミングを決定する条件    |
| アクション    | SpeechResponse "- Ok, turning on the TV" (了解です。テレビをつけます) | ルール条件が真のときに実行するアクション |

## <a name="try-it-out"></a>試してみる

テスト チャット パネルを使用して動作をテストします。

> [!div class="mx-imgBorder"]
> ![Web チャットを使用したテスト](media/custom-speech-commands/create-basic-test-chat.png)

- 「turn on the tv」と入力します。
- 予期される応答:Ok, turning on the tv (了解です。テレビをつけます)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)
