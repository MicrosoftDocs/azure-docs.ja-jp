---
title: '方法: Custom Commands アプリケーションの開発 - Speech Service'
titleSuffix: Azure Cognitive Services
description: Custom Commands アプリケーションを開発およびカスタマイズする方法について説明します。 これらの音声コマンド アプリは、タスクの完了やコマンドとコントロールのシナリオに最適です。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935820"
---
# <a name="develop-custom-commands-applications"></a>Custom Commands アプリケーションを開発する

このハウツー記事では、Custom Commands アプリケーションを開発および構成する方法について説明します。 Custom Commands 機能を使用すると、音声優先の対話操作を行うために最適化された、リッチな音声コマンド処理アプリを構築できます。 この機能は、タスクの完了やコマンドとコントロールのシナリオに最も適しています。 特に、モノのインターネット (IoT) デバイスと、アンビエントおよびヘッドレス デバイスに適しています。

この記事では、テレビのオンとオフを切り替えたり、温度を設定したり、アラームを設定したりできるアプリケーションを作成します。 これらの基本的なコマンドを作成することで、コマンドをカスタマイズするための次のオプションについて学習できます。

* コマンドへのパラメーターの追加
* コマンド パラメーターへの構成の追加
* 相互作用ルールの作成
* 音声応答の言語生成テンプレートの作成
* Custom Voice ツールの使用

## <a name="create-an-application-by-using-simple-commands"></a>単純なコマンドを使用してアプリケーションを作成する

空の Custom Commands アプリケーションを作成することから始めます。 詳細については、[クイックスタート](quickstart-custom-commands-application.md)を参照してください。 このアプリケーションでは、プロジェクトをインポートする代わりに、空のプロジェクトを作成します。

1. **[名前]** ボックスに、プロジェクト名として「*Smart-Room-Lite*」(または他の任意の名前) を入力します。
1. **[言語]** 一覧で、 **[英語 (米国)]** を選択します。
1. LUIS リソースを選択または作成します。

   > [!div class="mx-imgBorder"]
   > ![[新しいプロジェクト] ウィンドウを示すスクリーンショット。](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>LUIS リソースを更新する (省略可能)

**[新しいプロジェクト]** ウィンドウで選択した作成リソースを更新できます。 予測リソースを設定することもできます。 

予測リソースは、Custom Commands アプリケーションが発行されると、認識のために使用されます。 開発およびテスト フェーズでは、予測リソースは必要ありません。

### <a name="add-a-turnon-command"></a>TurnOn コマンドを追加する

作成した空の Smart-Room-Lite Custom Commands アプリケーションに、コマンドを追加します。 このコマンドを使用すると、発話 `Turn on the tv` が処理されます。 これにより、`Ok, turning the tv on` というメッセージが応答されます。

1. 左ペインの上部にある **[新しいコマンド]** を選択して、新しいコマンドを作成します。 **[新しいコマンド]** ウィンドウが開きます。
1. **[名前]** フィールドで、値 `TurnOn` を指定します。
1. **［作成］** を選択します

中央のペインには、このコマンドのプロパティが表示されます。 

コマンドの構成プロパティを次の表に示します。 詳細については、「[カスタム コマンドの概念と定義](./custom-commands-references.md)」を参照してください。

| 構成            | 説明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 例文 | ユーザーがコマンドをトリガーするために利用できる発話の例。                                                                 |
| パラメーター       | コマンドを完了するために必要な情報。                                                                                |
| 完了ルール | コマンドを完了するために実行されるアクション。 例: ユーザーへの応答や Web サービスとの通信など。 |
| 相互作用ルール   | より具体的または複雑な状況に対処するための他のルール。                                                              |


> [!div class="mx-imgBorder"]
> ![コマンドを作成する場所を示すスクリーンショット。](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>例文を追加する

**[例文]** セクションでは、ユーザーが発話できる内容の例を指定します。

1. 中央のペインで **[例文]** を選択します。
1. 右側のペインに、次の例を追加します。

    ```
    Turn on the tv
    ```

1.  ウィンドウの上部で **[保存]** を選択します。

パラメーターはまだないため、 **[完了ルール]** セクションに移動します。

#### <a name="add-a-completion-rule"></a>完了ルールを追加する

次に、コマンドに完了ルールを追加する必要があります。 このルールは、ユーザに対して、フルフィルメント アクションが実行されていることを示します。 

ルールと完了ルールの詳細については、「[カスタム コマンドの概念と定義](./custom-commands-references.md)」を参照してください。

1. 既定の完了ルール **[Done]** を選択します。 その後、次のように編集します。

    
    | 設定    | 推奨値                          | 説明                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **名前**       | `ConfirmationResponse`                  | ルールの目的を説明する名前          |
    | **条件** | なし                                     | ルールを実行できるタイミングを決定する条件    |
    | **アクション**    | **[Send speech response]\(音声応答の送信\)**  >  **[Simple editor]\(シンプルなエディター\)**  >  **[First variation]\(最初のバリエーション\)**  > `Ok, turning the tv on` | ルール条件が真のときに実行するアクション |

   > [!div class="mx-imgBorder"]
   > ![音声応答を作成する場所を示すスクリーンショット。](media/custom-commands/create-speech-response-action.png)

1. **[保存]** を選択してアクションを保存します。
1. **[完了ルール]** セクションに戻り、 **[保存]** を選択してすべての変更を保存します。 

    > [!NOTE]
    > コマンドに付属する既定の完了ルールを使用する必要はありません。 既定の完了ルールを削除して、独自のルールを追加することができます。

### <a name="add-a-settemperature-command"></a>SetTemperature コマンドを追加する

ここで、もう 1 つのコマンド `SetTemperature` を追加します。 このコマンドを使用すると、1 つの発話 `Set the temperature to 40 degrees` を受け取って、`Ok, setting temperature to 40 degrees` というメッセージが応答されます。

新しいコマンドを作成するには、例文に `Set the temperature to 40 degrees` 使用して、`TurnOn` コマンドに使用した手順に従います。

次に、既存の完了ルール **Done** を次のように編集します。

| 設定    | 推奨値                          |
| ---------- | ---------------------------------------- |
| **名前**  | `ConfirmationResponse`                  |
| **条件** | なし                                     |
| **アクション**    | **[Send speech response]\(音声応答の送信\)**  >  **[Simple editor]\(シンプルなエディター\)**  >  **[First variation]\(最初のバリエーション\)**  > `Ok, setting temperature to 40 degrees` |

**[保存]** を選択して、すべての変更をコマンドに保存します。

### <a name="add-a-setalarm-command"></a>SetAlarm コマンドを追加する

新しいコマンド `SetAlarm` を作成します。 例文 `Set an alarm for 9 am tomorrow` を使用します。 次に、既存の完了ルール **Done** を次のように編集します。

| 設定    | 推奨値                          |
| ---------- | ---------------------------------------- |
| **名前**  | `ConfirmationResponse`                  |
| **条件** | なし                                     |
| **アクション**    | **[Send speech response]\(音声応答の送信\)**  >  **[Simple editor]\(シンプルなエディター\)**  >  **[First variation]\(最初のバリエーション\)**  > `Ok, setting an alarm for 9 am tomorrow` |

**[保存]** を選択して、すべての変更をコマンドに保存します。

### <a name="try-it-out"></a>試してみる

テスト ペインを使用して、次のようにアプリケーションの動作をテストします。 

1. ペインの右上隅にある **[トレーニング]** アイコンを選択します。 
1. トレーニングが完了したら、 **[テスト]** を選択します。 

音声またはテキストを使用して、次の発話の例を試してみます。

- 入力: *set the temperature to 40 degrees* (温度を 40 度に設定して)
- 予期される応答:Ok, setting temperature to 40 degrees (はい、温度を 40 度に設定します)
- 入力: *turn on the tv* (テレビをつけて)
- 予期される応答:Ok, turning the tv on (はい、テレビをつけます)
- 入力: *set an alarm for 9 am tomorrow* (明日の午前 9 時にアラームを設定して)
- 予期される応答:Ok, setting an alarm for 9 am tomorrow (はい、明日の午前 9 時にアラームを設定します)

> [!div class="mx-imgBorder"]
> ![Web チャットのインターフェイスに表示されているテストのスクリーンショット。](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> テスト ペインの **[Turn details]\(ターンの詳細\)** を選択すると、この音声入力またはテキスト入力がどのように処理されたかについての情報を得ることができます。

## <a name="add-parameters-to-commands"></a>コマンドにパラメーターを追加する

このセクションでは、コマンドにパラメーターを追加する方法について説明します。 コマンドには、タスクを完了するためのパラメーターが必要です。 複雑なシナリオでは、パラメーターを使用して、カスタム アクションをトリガーする条件を定義することができます。

### <a name="configure-parameters-for-a-turnon-command"></a>TurnOn コマンドのパラメーターを構成する

まず、複数のデバイスのオンとオフを切り替えるように既存の `TurnOn` コマンドを編集しましょう。

1. このコマンドによってオンとオフの両方のシナリオが処理されるようになったため、コマンドの名前を *TurnOnOff* に変更します。
   1. 左側のペインで、 **[TurnOn]** コマンドを選択します。 次に、ペインの上部にある **[新しいコマンド]** の横にある省略記号 ( **...** ) ボタンを選択します。
   
   1. **[Rename]\(名前の変更\)** を選択します。 **[Rename command]\(コマンドの名前変更\)** ウィンドウで、名前を「*TurnOnOff*」に変更します。

1. コマンドに新しいパラメーターを追加します。 このパラメーターは、ユーザーがデバイスをオンにするかオフにするかを表します。
   1. 中央のペインの上部にある **[追加]** を選択します。 ドロップダウン メニューから **[パラメーター]** を選択します。
   1. 右側のペインの **[パラメーター]** セクションで、 **[名前]** ボックスに `OnOff` を追加します。
   1. **[必須]** を選択します。 **[Add response for a required parameter]\(必須パラメーターの応答追加\)** ウィンドウで **[Simple editor]\(シンプルなエディター\)** を選択します。 **[First variation]\(最初のバリエーション\)** フィールドで "*On or Off?* " を追加します。
   1. **[Update]\(更新\)** を選択します。

       > [!div class="mx-imgBorder"]
       > ![[Simple editor]\(シンプルなエディター\) タブが選択されている [Add response for a required parameter]\(必須パラメーターの応答追加\) セクションを示すスクリーンショット。](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 次の表を使用して、パラメーターのプロパティを構成します。 コマンドのすべての構成プロパティの詳細については、「[カスタム コマンドの概念と定義](./custom-commands-references.md)」を参照してください。
      

       | 構成      | 推奨値     | 説明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **名前**               | `OnOff`           | パラメーターのわかりやすい名前                                                                           |
       | **Is Global (グローバル)**          | 未選択       | このパラメーターの値がアプリケーションのすべてのコマンドにグローバルで適用されるかどうかを示すチェックボックス。|
       | **必須**           | Selected         | コマンドの完了にこのパラメーターの値が必須かどうかを示すチェック ボックス。 |
       | **Response for required parameter (必須パラメーターの応答)**      |**[Simple editor]\(シンプルなエディター\)**  > `On or Off?`      | 認識されていない場合にパラメーターの値を求めるプロンプト。 |
       | **Type**               | **String**          | Number、String、Date Time、Geography などのパラメーターの型。   |
       | **構成**      | **[Accept predefined input values from an internal catalog]\(内部カタログから定義済み入力値を受け取る\)** | 文字列の場合、この設定により、入力が一連の指定可能な値に制限されます。 |
       | **Predefined input values (定義済み入力値)**     | `on`, `off`           | 指定できる一連の値とその別名。         |
       
        
   1. 定義済みの入力値を追加するには、 **[Add a predefined input]\(定義済みの入力を追加\)** を選択します。 **[新しい項目]** ウィンドウで、前の表に示されているように *[名前]* を入力します。 今回は別名を使用していないため、このフィールドは空にしておくことができます。
   
      > [!div class="mx-imgBorder"]
      > ![パラメーターの作成方法を示すスクリーンショット。](media/custom-commands/create-on-off-parameter.png)

   1. パラメーターのすべての構成を保存するには、 **[保存]** を選択します。
 
#### <a name="add-a-subjectdevice-parameter"></a>SubjectDevice パラメーターを追加する

1. このコマンドを使用して制御できるデバイスの名前を表す 2 番目のパラメーターを追加するには、 **[追加]** を選択します。 次の構成を使用します。


    | 設定            | 推奨値       |
    | ------------------ | --------------------- |
    | **名前**               | `SubjectDevice`         |
    | **Is Global (グローバル)**          | 未選択             |
    | **必須**           | Selected               |
    | **Response for required parameter (必須パラメーターの応答)**     | **[Simple editor]\(シンプルなエディター\)**  > `Which device do you want to control?`    | 
    | **Type**               | **String**                |          |
    | **構成**      | **[Accept predefined input values from an internal catalog]\(内部カタログから定義済み入力値を受け取る\)** | 
    | **Predefined input values (定義済み入力値)** | `tv`, `fan`               |
    | **Aliases (別名)** (`tv`)      | `television`, `telly`     |

1. **[保存]** を選択します。

#### <a name="modify-example-sentences"></a>例文を変更する

パラメーターを使用するコマンドの場合、考えられる組み合わせすべてを網羅する例文を追加すると役立ちます。 次に例を示します。

* 完全なパラメーター情報: `turn {OnOff} the {SubjectDevice}`
* 部分的なパラメーター情報: `turn it {OnOff}`
* パラメーター情報なし: `turn something`

情報の程度が異なる例文を利用すると、Custom Commands アプリケーションでは、部分的な情報を使用して 1 回限りの解決とマルチターンの解決の両方を解決できます。

この情報を念頭に置き、以下の推奨されるパラメーターを使用するよう例文を編集します。

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

**[保存]** を選択します。

> [!TIP]
> 例文エディターでは、中かっこを使用してパラメーターを参照します。 たとえば、「 `turn {OnOff} the {SubjectDevice}` 」のように入力します。
> 以前に作成したパラメーターでサポートされているオート コンプリートには、タブを使用します。

#### <a name="modify-completion-rules-to-include-parameters"></a>パラメーターを含めるように完了ルールを変更する

既存の完了ルール `ConfirmationResponse` を変更します。

1. **[条件]** セクションで、 **[条件の追加]** を選択します。
1. **[新しい条件]** ウィンドウの **[種類]** リストで、 **[必要なパラメーター]** を選択します。 次の一覧で **OnOff** と **SubjectDevice** の両方を選択します。
1. **IsGlobal** はオフのままにします。
1. **［作成］** を選択します
1. **[アクション]** セクションで、 **[Send speech response]\(音声応答の送信\)** アクションにカーソルを移動して編集ボタンを選択し、それを編集します。 今回は、新しく作成した `OnOff` と `SubjectDevice` パラメーターを使用します。

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. **[保存]** を選択します。

変更内容を試すには、右側のペインの上部にある **[トレーニング]** アイコンを選択します。 

トレーニングが完了したら、 **[テスト]** を選択します。 **[Test your application]\(アプリケーションのテスト\)** ウィンドウが表示されます。 次の対話を試してみます。

- 入力: *turn off the tv* (テレビを消して)
- 出力:Ok, turning off the tv (了解です。テレビを消します)
- 入力: *turn off the television* (テレビを消して)
- 出力:Ok, turning off the tv (了解です。テレビを消します)
- 入力: *turn it off* (それを消して)
- 出力:Which device do you want to control? (どのデバイスを制御しますか?)
- 入力: *the tv* (テレビ)
- 出力:Ok, turning off the tv (了解です。テレビを消します)

### <a name="configure-parameters-for-a-settemperature-command"></a>SetTemperature コマンドのパラメーターを構成する

`SetTemperature` コマンドを変更して、ユーザーの指示に従って温度を設定できるようにします。

`Temperature` パラメーターを追加します。 次の構成を使用します。

| 構成      | 推奨値     |
| ------------------ | ----------------|
| **名前**               | `Temperature`           |
| **必須**           | Selected         |
| **Response for required parameter (必須パラメーターの応答)**      | **[Simple editor]\(シンプルなエディター\)**  > `What temperature would you like?`
| **Type**               | `Number`          |


次の値を使用するよう発話の例を編集します。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

既存の完了ルールを編集します。 次の構成を使用します。

| 構成      | 推奨値     |
| ------------------ | ----------------|
| **条件**         | **[Required parameter]\(必須パラメーター\)**  >  **[Temperature]\(温度\)**           |
| **アクション**           | **[Send speech response]\(音声応答の送信\)**  > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>SetAlarm コマンドのパラメーターを構成する

`DateTime` という名前のパラメーターを追加します。 次の構成を使用します。

   | 設定                           | 推奨値                     | 
   | --------------------------------- | ----------------------------------------|
   | **名前**                              | `DateTime`                               |
   | **必須**                          | Selected                                 |
   | **Response for required parameter (必須パラメーターの応答)**   | **[Simple editor]\(シンプルなエディター\)**  > `For what time?`            | 
   | **Type**                              | **DateTime**                                |
   | **日付の既定値**                     | 日付が指定されていない場合、今日に設定します。            |
   | **時間の既定値**                     | 時間が指定されていない場合、一日の始まりに設定します。     |


> [!NOTE]
> この記事では、ほとんどの場合、String、Number、および DateTime パラメーターの型を使用します。 サポートされているすべてのパラメーターの型とそのプロパティの一覧については、「[カスタム コマンドの概念と定義](./custom-commands-references.md)」を参照してください。


発話の例を編集します。 次の値を使用してください。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

既存の完了ルールを編集します。 次の構成を使用します。

   | 設定    | 推奨値                               |
   | ---------- | ------------------------------------------------------- |
   | **アクション**    | **[Send speech response]\(音声応答の送信\)**  > `Ok, alarm set for {DateTime}`  |

異なるコマンドに関連する発話を使用して、3 つのコマンドをまとめてテストします。 (異なるコマンド間で切り替えることができます。)

- 次の内容を入力します。*Set an alarm* (アラームを設定して)
- 出力:For what time? (何時に設定しますか?)
- 次の内容を入力します。*Turn on the tv* (テレビをつけて)
- 出力:Ok, turning the tv on (はい、テレビをつけます)
- 次の内容を入力します。*Set an alarm* (アラームを設定して)
- 出力:For what time? (何時に設定しますか?)
- 次の内容を入力します。*5 pm* (午後 5 時)
- 出力:Ok, alarm set for 2020-05-01 17:00:00 (はい、アラームを 2020-05-01 17:00:00 に設定しました)

## <a name="add-configurations-to-command-parameters"></a>コマンド パラメーターに構成を追加する

このセクションでは、次のような高度なパラメーターの構成について詳しく説明します。

 - Custom Commands アプリケーションの外部で定義されたセットにパラメーター値を含める方法。
 - パラメーターの値に対する検証句を追加する方法。

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>外部カタログ エンティティとしてパラメーターを構成する

Custom Commands 機能を使用すると、Web エンドポイントでホストされている外部カタログを参照するように文字列型のパラメーターを構成できます。 そのため、Custom Commands アプリケーションを編集することなく、外部カタログを個別に更新できます。 この方法は、カタログ エントリが多数ある場合に便利です。

`TurnOnOff` コマンドの `SubjectDevice` パラメーターを再利用します。 このパラメーターの現在の構成では、**内部カタログから定義済みの入力を受け入れます**。 この構成は、パラメーターの構成内のデバイスの静的なリストを参照します。 このコンテンツを、独立して更新できる外部データ ソースに移動します。

コンテンツを移動するには、まず新しい Web エンドポイントを追加します。 左側のペインから **[Web エンドポイント]** セクションに移動します。 そこで、新しい Web エンドポイントを追加します。 次の構成を使用します。

| 設定 | 推奨値 |
|----|----|
| **名前** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **方法** | **GET** |


指定されている URL の値が自分の環境で機能しない場合は、制御可能なデバイスのリストで構成される JSON ファイルを返す Web エンドポイントを構成してホストする必要があります。 Web エンドポイントから、次のように書式設定された JSON ファイルが返されます。
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

次に、**SubjectDevice** パラメーターの設定ページにアクセスします。 次のプロパティを設定します。

| 設定 | 推奨値 |
| ----| ---- |
| **構成** | **外部カタログから定義済みの入力を受け取る** |                               
| **Catalog endpoint (カタログ エンドポイント)** | `getDevices` |
| **方法** | **GET** |

次に、 **[保存]** を選択します。

> [!IMPORTANT]
> 左側のペインの **[Web エンドポイント]** セクションで Web エンドポイントを設定しない限り、外部カタログからの入力を受け入れるパラメーターを構成するオプションは表示されません。

**[トレーニング]** を選択して試します。 トレーニングが完了したら、 **[テスト]** を選択していくつかの対話を試してみます。

* 入力: *turn on* (オンにする)
* 出力:Which device do you want to control? (どのデバイスを制御しますか?)
* 入力: *lights* (ライト)
* 出力:Ok, turning the lights on (はい、ライトをオンにします)

> [!NOTE]
> これで、Web エンドポイントでホストされているすべてのデバイスを制御できるようになりました。 ただし、新しい変更をテストする場合は、アプリケーションをトレーニングし、アプリケーションを再発行する必要があります。

### <a name="add-validation-to-parameters"></a>パラメーターに検証を追加する

"*検証*" は、特定のパラメーターの型に適用できるコンストラクトであり、パラメーターの値に対して制約を構成する場合に使用できます。 値が制約内に収まっていない場合、修正するよう求めるプロンプトが表示されます。 検証コンストラクトを拡張するパラメーターの型の一覧については、「[カスタム コマンドの概念と定義](./custom-commands-references.md)」を参照してください。

`SetTemperature` コマンドを使用して検証をテストします。 次の手順を使用して、`Temperature` パラメーターの検証を追加します。

1. 左側のペインで **SetTemperature** コマンドを選択します。
1. 中央のペインで **Temperature** を選択します。
1. 右のペインで **[Add a validation]\(検証を追加する\)** を選択します。
1. **[New validation]\(新しい検証\)** ウィンドウで、次の表のように検証を構成します。 **[作成]** を選択します。


    | パラメーターの構成 | 推奨値 | 説明 |
    | ---- | ---- | ---- |
    | **最小値** | `60` | Number パラメーターの場合、このパラメーターで想定できる最小値 |
    | **最大値** | `80` | 数値パラメーターの場合、このパラメーターで想定できる最大値 |
    | **失敗応答** |  **[Simple editor]\(シンプルなエディター\)**  >  **[First variation]\(最初のバリエーション\)**  > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 検証が失敗した場合に新しい値の入力を求めるプロンプト |

    > [!div class="mx-imgBorder"]
    > ![範囲の検証を追加する方法を示すスクリーンショット。](media/custom-commands/add-validations-temperature.png)

右側のペインの上部にある **[トレーニング]** アイコンを選択して試してみます。 トレーニングが完了したら、 **[テスト]** を選択します。 以下のようにいくつかの対話を試してみます。

- 次の内容を入力します。*Set the temperature to 72 degrees* (温度を 72 度に設定して)
- 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)
- 次の内容を入力します。*Set the temperature to 45 degrees* (温度を 45 度に設定して)
- 出力:Sorry, I can only set temperature between 60 and 80 degrees (申し訳ありません、設定できる温度は 60 度から 80 度までです)
- 入力: *make it 72 degrees instead* (それでは、72 度にして)
- 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)

## <a name="add-interaction-rules"></a>相互作用ルールを追加する

相互作用ルールは、具体的または複雑な状況に対処するための *追加* ルールです。 独自の相互作用ルールを自由に作成できますが、この例では、次のシナリオの相互作用ルールを使用します。

* コマンドの確認
* コマンドへの 1 ステップの修正の追加

相互作用ルールの詳細については、「[カスタム コマンドの概念と定義](./custom-commands-references.md)」を参照してください。

### <a name="add-confirmations-to-a-command"></a>コマンドに確認を追加する

確認を追加するには、`SetTemperature` コマンドを使用します。 確認を行うには、次の手順に従って相互作用ルールを作成します。

1. 左側のペインで **SetTemperature** コマンドを選択します。
1. 中央のペインで **[追加]** を選択して、相互作用ルールを追加します。 次に、 **[Interaction rules]\(相互作用ルール\)**  >  **[Confirm command]\(確認コマンド\)** を選択します。

    これにより、3 つの相互作用ルールが追加されます。 これらのルールは、ユーザーにアラームの日付と時刻の確認を要求します。 次のターンで期待される回答は、確認 (yes または no) です。

    1. 次の構成を使用して、 **[Confirm command]\(確認コマンド\)** 相互作用ルールを変更します。
        1. 名前を「**Confirm temperature**」(温度の確認) に変更します。
        1. 新しい条件を追加します: **[Required parameters]\(必須パラメーター\)**  >  **[Temperature]\(温度\)** 。
        1. 新しいアクションを追加します: **[Type]\(種類\)**  >  **[Send speech response]\(音声応答を送信する\)**  > 「**Are you sure you want to set the temperature as {Temperature} degrees?** 」(温度を {Temperature} 度に設定しますか?)
        1. **[Expectations]\(期待される回答\)** セクションは既定値 **[Expecting confirmation from user]\(ユーザーからの確認が必要\)** のままにします。
      
         > [!div class="mx-imgBorder"]
         > ![必須パラメーターの応答の作成方法を示すスクリーンショット。](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 成功した確認 (ユーザーが "yes" と言った) を処理するように、 **[Confirmation succeeded]\(確認成功\)** 相互作用ルールを変更します。
      
          1. 名前を「**Confirmation temperature succeeded**」(温度確認成功) に変更します。
          1. 既存の **[Confirmation was successful]\(確認が成功した\)** 条件はそのままにします。
          1. 新しい条件を追加します: **[Type]\(種類\)**  >  **[Required parameters]\(必須パラメーター\)**  >  **[Temperature]\(温度\)** 。
          1. **[Post-execution state]\(実行後の状態\)** は既定値の **[Execute completion rules]\(完了ルールの実行\)** のままにします。

    1. 確認が拒否された (ユーザーが "no" と言った) 場合のシナリオを処理するように、 **[Confirmation denied]\(確認拒否\)** 相互作用ルールを変更します。

          1. 名前を「**Confirmation temperature denied**」 (温度確認拒否) に変更します。
          1. 既存の **[Confirmation was denied]\(確認が拒否された\)** 条件はそのままにします。
          1. 新しい条件を追加します: **[Type]\(種類\)**  >  **[Required parameters]\(必須パラメーター\)**  >  **[Temperature]\(温度\)** 。
          1. 新しいアクションを追加します: **[Type]\(種類\)**  >  **[Send speech response]\(音声応答を送信する\)**  > 「**No problem. What temperature then?」(わかりました。それでは何度にしますか?)** 。
          1. 規定値の **[Post-execution state]\(実行後の状態\)** を **[Wait for user's input]\(ユーザーの入力を待つ\)** に変更します。

> [!IMPORTANT]
> この記事では、組み込みの確認機能を使用します。 相互作用ルールを 1 つずつ手動で追加することもできます。
   
**[トレーニング]** を選択して、変更を試してみます。 トレーニングが完了したら、 **[テスト]** を選択します。

- **入力**:*Set temperature to 80 degrees* (温度を 80 度に設定して)
- **出力**: are you sure you want to set the temperature as 80 degrees? (温度を 80 度に設定しますか?)
- **入力**:*いいえ*
- **出力**:ご安心ください。 What temperature then? (それでは何度にしますか)
- **入力**:*72 degrees* (72 度)
- **出力**: are you sure you want to set the temperature as 72 degrees? (温度を 72 度に設定しますか?)
- **入力**:*Yes* (はい)
- **出力**:OK, setting temperature to 72 degrees (はい、温度を 72 度に設定します)

### <a name="implement-corrections-in-a-command"></a>コマンドで修正を実装する

このセクションでは、ワンステップ修正を構成します。 この修正は、フルフィルメント アクションの実行後に使用されます。 コマンドがまだ処理されていない場合に、既定で修正が有効になる例も示します。 コマンドが完了していないときに修正を追加するには、新しいパラメーター `AlarmTone` を追加します。

左側のペインで、 **[SetAlarm]** コマンドを選択します。 次に、新しいパラメーター **AlarmTone** を追加します。
        
- **名前** > `AlarmTone`
- **[Type]\(型\)**  > **String**
- **[Default Value]\(既定値\)**  >  **[Chimes]\(チャイム\)**
- **[Configuration]\(構成\)**  > 「**Accept predefined input values from the internal catalog**」 (内部カタログから定義済み入力値を受け取る)
- **[Predefined input values]\(定義済みの入力値\)**  >  **[Chimes]\(チャイム\)** 、 **[Jingle]\(チリンチリン\)** 、および **[Echo]\(エコー\)** (これらの値は個別の定義済みの入力です。)


次に、**DateTime** パラメーターに対する応答を、「**Ready to set alarm with tone as {AlarmTone}. For what time?** 」 (トーンをチャイムとして設定する準備ができました。何時に設定しますか?) に更新します。 次に、完了ルールを次のように変更します。

1. 既存の完了ルール **ConfirmationResponse** を選択します。
1. 右側のペインの既存のアクションをポイントして、 **[編集]** を選択します。
1. 音声応答を `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` に更新します。

> [!IMPORTANT]
> アラーム音は、実行中のコマンドで明示的に構成されていなくても変わる可能性があります。 たとえば、コマンドがまだ完了していない場合に変更される場合があります。 コマンドがまだ処理されていない場合、ターン番号に関係なく、すべてのコマンド パラメーターに対する修正は "*既定で*" 有効になります。

#### <a name="implement-a-correction-when-a-command-is-finished"></a>コマンドの完了時に修正を実装する

Custom Commands プラットフォームでは、コマンドが終了した場合でも、ワンステップ修正が可能です。 この機能は、既定で有効になっています。 明示的に構成する必要があります。 

以下の手順に従ってワンステップ修正を構成します。

1. **SetAlarm** コマンドで、種類が **[Update previous command]\(前のコマンドを更新する\)** の相互作用ルールを追加して、以前に設定したアラームを更新します。 相互作用ルールの名前を「**Update previous alarm**」 (前のアラームを更新する) に変更します。
1. 条件は次の規定値のままにします: **[Previous command needs to be updated]\(前のコマンドの更新が必要\)** 。
1. 新しい条件を追加します: **[Type]\(種類\)**  >  **[Required parameters]\(必須パラメーター\)**  >  **[DateTime]** 。
1. 新しいアクションを追加します: **[Type]\(種類\)**  >  **[Send speech response]\(音声応答を送信する\)**  >  **[Simple editor]\(シンプルなエディター\)**  > 「**Updating previous alarm time to {DateTime}** 」(前のアラーム時刻を {DateTime} に更新します) を追加します。
1. **[Post-execution state]\(実行後の状態\)** は既定値の **[Command completed]\(コマンド完了\)** のままにします。

**[トレーニング]** を選択して、変更を試してみます。 トレーニングが完了するのを待ってから、 **[テスト]** を選択します。

- **入力**:*Set an alarm.* (アラームを設定して)
- **出力**:Ready to set alarm with tone as Chimes. (トーンをチャイムとして設定する準備ができました。) For what time? (何時に設定しますか?)
- **入力**:*Set an alarm with the tone as Jingle for 9 am tomorrow.* (トーンをチリンチリンにして明日午前 9 時にアラームを設定して)
- **出力**:OK, alarm set for 2020-05-21 09:00:00. (OK、アラームを 2020-05-21 09:00:00 に設定しました。) The alarm tone is Jingle. (アラーム音はチリンチリンです。)
- **入力**:*No, 8 am.* (いいえ、午前 8 時です。)
- **出力**:Updating previous alarm time to 2020-05-29 08:00. (前のアラーム時刻を 2020-05-29 08:00 に更新します。)

> [!NOTE]
> 実際のアプリケーションでは、この修正ルールの **[Actions]\(アクション\)** セクションで、クライアントにアクティビティを返信するか、または HTTP エンドポイントを呼び出し、システムのアラーム時刻を更新する必要もあります。 このアクションでは、アラーム時刻を更新するだけにします。 コマンドの他の属性は更新しないようにする必要があります。 この場合の属性は、アラーム音です。

## <a name="add-language-generation-templates-for-speech-responses"></a>音声応答の言語生成テンプレートを追加する

言語生成 (LG) テンプレートを使用すると、クライアントに送信される応答をカスタマイズできます。 これにより、応答にバリエーションをもたせることができます。 言語生成は、以下を使用することで実現できます。

* 言語生成テンプレート
* アダプティブ式

Custom Commands テンプレートは、Bot Framework の [LG テンプレート](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)に基づいています。 Custom Commands 機能を使用すると必要に応じて新しい LG テンプレートが作成されるため (パラメーターまたはアクションでの音声応答の場合)、LG テンプレートの名前を指定する必要はありません。 

そのため、次のようにテンプレートを定義する必要はありません。

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

代わりに、次のように、名前を指定せずにテンプレートの本体を定義します。

> [!div class="mx-imgBorder"]
> ![テンプレート エディターの例を示すスクリーンショット。](./media/custom-commands/template-editor-example.png)


この変更により、クライアントに送信される音声応答にバリエーションが生まれます。 発話の場合、対応する音声応答は、この準備したオプションからランダムに選択されます。

LG テンプレートを利用すると、アダプティブ式を使用してコマンドに複雑な音声応答を定義することもできます。 詳細については、[LG テンプレート形式](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)に関するセクションを参照してください。 

既定の Custom Commands 機能では、次のようなわずかな違いはありますが、すべての機能がサポートされています。

* LG テンプレートエンティティは、`${entityName}` と表されます。 Custom Commands 機能では、エンティティは使用されません。 ただし、`${parameterName}` 表現または `{parameterName}` 表現を使用して、パラメーターを変数として使用することができます。
* Custom Commands 機能では、テンプレートの構成と展開はサポートされていません。これは、 *.lg* ファイルを直接編集することがないためです。 編集するのは、自動的に作成されたテンプレートの応答のみです。
* Custom Commands 機能では、LG によって挿入されたカスタム関数はサポートされていません。 定義済みの関数はサポートされています。
* Custom Commands 機能では、`strict`、`replaceNull`、`lineBreakStyle` などのオプションはサポートされていません。

### <a name="add-template-responses-to-a-turnonoff-command"></a>TurnOnOff コマンドにテンプレート応答を追加する

`TurnOnOff` コマンドを変更して、新しいパラメーターを追加します。 次の構成を使用します。

| 設定            | 推奨値       | 
| ------------------ | --------------------- | 
| **名前**               | `SubjectContext`         | 
| **Is Global (グローバル)**          | 未選択             | 
| **必須**           | 未選択               | 
| **Type**               | **String**                |
| **既定値**      | `all` |
| **Configuration**      | **Accept predefined input values from internal catalog (内部カタログから定義済み入力値を受け取る)** | 
| **Predefined input values (定義済み入力値)** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>完了ルールを変更する

既存の完了ルール **ConfirmationResponse** の **[アクション]** セクションを編集します。 **[アクションの編集]** ウィンドウで、 **[テンプレート エディター]** に切り替えます。 次に、テキストを次の例に置き換えます。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

次の入力と出力を使用して、アプリケーションのトレーニングとテストを行います。 応答にバリエーションがあることに注目してください。 このバリエーションは、テンプレート値の複数の代替を用意し、さらにアダプティブ式を使用することで生まれています。

* 入力: *turn on the tv* (テレビをつけて)
* 出力:Ok, turning the tv on (はい、テレビをつけます)
* 入力: *turn on the tv* (テレビをつけて)
* 出力:Done, turned on the tv (テレビをつけました)
* 入力: *turn off the lights* (ライトを消して)
* 出力:Ok, turning all the lights off (はい、すべてのライトをオフにします)
* 入力: *turn off room lights* (部屋のライトを消して)
* 出力:Ok, turning off the room lights (はい、部屋のライトをオフにします)

## <a name="use-a-custom-voice"></a>カスタム音声を使用する

Custom Commands の応答をカスタマイズするもう 1 つの方法は、出力音声を選択することです。 既定の音声をカスタム音声に切り替えるには、次の手順に従います。

1. Custom Commands アプリケーションの左側のペインで、 **[設定]** を選択します。
1. 中央のペインで **[Custom Voice]** を選択します。
1. 表から、カスタム音声またはパブリック音声を選択します。
1. **[保存]** を選択します。

> [!div class="mx-imgBorder"]
> ![例文とパラメーターを示すスクリーンショット。](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> パブリック音声の場合、ニューラル型は特定のリージョンでのみ使用できます。 詳細については、[Speech Service がサポートされているリージョン](./regions.md#standard-and-neural-voices)に関するページを参照してください。
>
> カスタム音声は、**Custom Voice** プロジェクト ページから作成できます。 詳細については、「[Custom Voice の概要](./how-to-custom-voice.md)」を参照してください。

これで、アプリケーションは既定の音声ではなく、選択した音声で応答します。

## <a name="next-steps"></a>次のステップ

* Speech SDK を使用して、クライアント アプリに [Custom Commands アプリケーションを統合する](how-to-custom-commands-setup-speech-sdk.md)方法について説明します。
* Azure DevOps を使用して Custom Commands アプリケーションの[継続的配置を設定する](how-to-custom-commands-deploy-cicd.md)。 
                      
