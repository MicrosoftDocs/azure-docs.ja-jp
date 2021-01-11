---
title: '方法: 単純なコマンドを使用してアプリケーションを作成する - 音声サービス'
titleSuffix: Azure Cognitive Services
description: この記事では、単純なコマンドを使用して、ホストされたカスタム コマンド アプリケーションを作成してテストする方法について説明します。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d166257dd28773d89a4f1fd56de3cb1a22242523
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284168"
---
# <a name="create-application-with-simple-commands"></a>単純なコマンドを使用してアプリケーションを作成する

この記事では、次の方法について説明します。
 - 空のアプリケーションを作成する
 - LUIS リソースを更新する
 - いくつかの基本的なコマンドをカスタム コマンド アプリケーションに追加する

## <a name="create-empty-application"></a>空のアプリケーションを作成する
空のカスタム コマンド アプリケーションを作成します。 詳細については、[クイックスタート](quickstart-custom-commands-application.md)を参照してください。 今回は、プロジェクトをインポートする代わりに、空のプロジェクトを作成します。

1. **[名前]** ボックスに、プロジェクト名として「`Smart-Room-Lite`」 (またはご自分で選んだ名前) を入力します。
1. **[言語]** 一覧で、 **[英語 (米国)]** を選択します。
1. 任意の LUIS リソースを選択または作成します。

   > [!div class="mx-imgBorder"]
   > ![プロジェクトの作成](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>LUIS リソースを更新する (省略可能)

**[新しいプロジェクト]** ウィンドウで選択した作成リソースを更新し、予測リソースを設定できます。 予測リソースは、カスタム コマンド アプリケーションが発行されると、認識のために使用されます。 開発およびテスト フェーズでは、予測リソースは必要ありません。

## <a name="add-turnon-command"></a>TurnOn コマンドを追加する

作成した空の **Smart-Room-Lite** カスタム コマンド アプリケーションで、発話を処理する単純なコマンド `turn on the tv` を追加し、メッセージ `Ok, turning the tv on` で応答します。

1. 左ペインの上部にある **[新しいコマンド]** を選択して、新しいコマンドを作成します。 **[新しいコマンド]** ウィンドウが開きます。
1. **[名前]** フィールドの値に **TurnOn** を指定します。
1. **［作成］** を選択します

中央のペインには、コマンドのさまざまなプロパティが一覧表示されます。 コマンドの次のプロパティを構成します。 コマンドのすべての構成プロパティの説明については、[リファレンス](./custom-commands-references.md)を参照してください。

| 構成            | 説明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Example sentences\(例文\)** | 発話の例では、ユーザーはこのコマンドを言ってトリガーすることができます。                                                                 |
| **Parameters\(パラメーター\)**       | コマンドを完了するために必要な情報                                                                                |
| **Completion rules\(完了ルール\)** | コマンドを完了するために実行されるアクション。 たとえば、ユーザーへの応答や、別の Web サービスとの通信などです。 |
| **Interaction rules\(相互作用ルール\)**   | より具体的または複雑な状況に対処するための追加ルールです。                                                              |


> [!div class="mx-imgBorder"]
> ![コマンドの作成](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>例文を追加する

まず **[例文]** セクションから始めて、ユーザーが言うことができる文の例を示します。

1. 中央のペインで **[例文]** セクションを選択します。
1. 右端のペインで、例を追加します。

    ```
    turn on the tv
    ```

1.  ペインの上部にある **[保存]** を選択します。

現在、パラメーターがないため、 **[Completion rules]\(完了ルール\)** セクションに移動できます。

### <a name="add-a-completion-rule"></a>完了ルールを追加する

次に、コマンドに完了ルールを追加する必要があります。 このルールは、ユーザに対して、フルフィルメント アクションが実行されていることを示します。 ルールと完了ルールの詳細については、[リファレンス](./custom-commands-references.md)を参照してください。

1. 既定の完了ルール **Done** を選択し、次のように編集します。

    
    | 設定    | 推奨値                          | 説明                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Name\(名前\)**       | ConfirmationResponse                  | ルールの目的を説明する名前          |
    | **Conditions\(条件\)** | なし                                     | ルールを実行できるタイミングを決定する条件    |
    | **Actions\(アクション\)**    | [Send speech response]\(音声応答を送信する\) > [Simple editor]\(シンプルなエディター\) > [First variation]\(最初のバリエーション\) > `Ok, turning the tv on` | ルール条件が真のときに実行するアクション |
    



   > [!div class="mx-imgBorder"]
   > ![音声応答の作成](media/custom-commands/create-speech-response-action.png)

1. **[保存]** を選択してアクションを保存します。
1. **[完了ルール]** セクションに戻り、 **[保存]** を選択してすべての変更を保存します。 


    > [!NOTE]
    > コマンドに付属する既定の完了ルールを使用する必要はありません。 必要に応じて、既定の完了ルールを削除し、独自のルールを追加することができます。

### <a name="try-it-out"></a>試してみる

テスト チャット パネルを使用して動作をテストする
1. 右側のペインの上部にある **[トレーニング]** アイコンを選択します。
1. トレーニングが完了したら、 **[テスト]** を選択します。 音声またはテキストで次の発話を試してみてください。
    - 入力: turn on the tv (テレビをつけて)
    - 出力:Ok, turning the tv on (はい、テレビをつけます)


> [!div class="mx-imgBorder"]
> ![Web チャットを使用したテスト](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> テスト パネルの **[Turn details]\(ターンの詳細\)** を選択すると、この音声またはテキスト入力がどのように処理されたかについての情報を得ることができます。  

## <a name="add-settemperature-command"></a>SetTemperature コマンドを追加する

次に、1 つの発話 `set the temperature to 40 degrees` を受け取って、メッセージ `Ok, setting temperature to 40 degrees` で応答するコマンド **SetTemperature** をもう 1 つ追加します。

**TurnOn** コマンドに示されている手順に従って、"**set the temperature to 40 degrees**" (温度を 40 度に設定して) という例文を使用して新しいコマンドを作成します。

次に、既存の完了ルール **Done** を次のように編集します。

| 設定    | 推奨値                          |
| ---------- | ---------------------------------------- |
| Name\(名前\)  | ConfirmationResponse                  |
| Conditions\(条件\) | なし                                     |
| Actions    | [Send speech response]\(音声応答を送信する\) > [Simple editor]\(シンプルなエディター\) > [First variation]\(最初のバリエーション\) > `Ok, setting temperature to 40 degrees` |

**[保存]** を選択して、すべての変更をコマンドに保存します。

## <a name="add-setalarm-command"></a>SetAlarm コマンドを追加する
例文 "**set an alarm for 9 am tomorrow**" (明日の午前 9 時にアラームを設定して) を使用して、新しいコマンド **SetAlarm** を作成します。 次に、既存の完了ルール **Done** を次のように編集します。

| 設定    | 推奨値                          |
| ---------- | ---------------------------------------- |
| Rule Name\(規則の名前\)  | ConfirmationResponse                  |
| Conditions\(条件\) | なし                                     |
| Actions    | [Send speech response]\(音声応答を送信する\) > [Simple editor]\(シンプルなエディター\) > [First variation]\(最初のバリエーション\) > `Ok, setting an alarm for 9 am tomorrow` |

**[保存]** を選択して、すべての変更をコマンドに保存します。

## <a name="try-it-out"></a>試してみる

テスト チャット パネルを使用して動作をテストする
1. **[Train]\(トレーニング\)** を選択します。 トレーニングが成功したら、 **[テスト]** を選択して、次を試します。
    - 入力: set the temperature to 40 degrees (温度を 40 度に設定して)
    - 予期される応答:Ok, setting temperature to 40 degrees (はい、温度を 40 度に設定します)
    - 「turn on the tv」と入力します。
    - 予期される応答:Ok, turning the tv on (はい、テレビをつけます)
    - 入力: set an alarm for 9 am tomorrow (明日の午前 9 時にアラームを設定して)
    - 予期される応答:Ok, setting an alarm for 9 am tomorrow (はい、明日の午前 9 時にアラームを設定します)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: コマンドにパラメーターを追加する](./how-to-custom-commands-add-parameters-to-commands.md)
