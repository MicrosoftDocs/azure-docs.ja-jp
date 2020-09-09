---
title: 方法:単純なコマンドをカスタム コマンド アプリケーションに追加する - 音声サービス
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンドにパラメーターを追加する方法について説明します
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0ed237debc2395ed307658b2d57a541574f9478a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284151"
---
# <a name="add-parameters-to-commands"></a>コマンドにパラメーターを追加する

この記事では、カスタム コマンドにパラメーターを追加する方法について説明します。 パラメーターは、コマンドがタスクを完了するために必要な情報です。 複雑なシナリオでは、パラメーターを使用して、カスタム アクションをトリガーする条件を定義することもできます。

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * [方法: 単純なコマンドを使用してアプリケーションを作成する](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>TurnOn コマンドのパラメーターを構成する

既存の **TurnOn** コマンドを編集して、複数のデバイスのオンとオフを切り替えます。

1. このコマンドはオンとオフの両方のシナリオを処理するようになるため、コマンドの名前を **TurnOnOff** に変更します。
   1. 左ペインで **TurnOn** コマンドを選択し、ペインの上部で **[新しいコマンド]** の横にある省略記号 (...) ボタンを選択します。
   
   1. **[Rename]\(名前の変更\)** を選択します。 **[Rename command]\(コマンドの名前変更\)** ウィンドウで、 **[名前]** を「**TurnOnOff**」に変更します。

1. 次に、ユーザーがデバイスをオンにしたいのか、オフにしたいのかを表わす新しいパラメーターをこのコマンドに追加します。
   1. 中央のペインの上部にある **[追加]** を選択します。 ドロップダウンから **[パラメーター]** を選択します。
   1. 右ペインの **[パラメーター]** セクションで、 **[名前]** ボックスに値として **OnOff** を追加します。
   1. **[必須]** を選択します。 **[Add response for a required parameter]\(必須パラメーターの応答追加\)** ウィンドウで **[Simple editor]\(シンプルなエディター\)** を選択します。 **[First variation]\(最初のバリエーション\)** に次を追加します。
        ```
        On or Off?
        ```
   1. **[Update]\(更新\)** を選択します。

       > [!div class="mx-imgBorder"]
       > ![必須パラメーターの応答を作成する](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 次に、パラメーターのプロパティを構成します。 コマンドのすべての構成プロパティの説明については、[リファレンス](./custom-commands-references.md)を参照してください。 パラメーターのプロパティを次のように構成します。
      

       | 構成      | 推奨値     | 説明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名前               | `OnOff`           | パラメーターのわかりやすい名前                                                                           |
       | Is Global          | チェック解除       | このパラメーターの値がアプリケーションのすべてのコマンドにグローバルで適用されるかどうかを示すチェックボックス|
       | 必須           | チェック         | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス |
       | 必須パラメーターの応答      |Simple editor\(シンプルなエディター\) > `On or Off?`      | 理解されていないとき、このパラメーターの値を問うプロンプト |
       | Type               | String          | Number、String、Date Time、Geography などのパラメーターの型   |
       | 構成      | 内部カタログから事前定義済み入力値を受け取る | Strings の場合、これにより入力が一連の指定可能値に限定されます。 |
       | 定義済み入力値     | `on`, `off`           | 一連の入力可能値とその別名         |
       
        
   1. 定義済み入力値を追加するには、 **[Add a predefined input]\(定義済みの入力を追加\)** を選択し、 **[新しい項目]** ウィンドウで、上の表に示されているように **[名前]** を入力します。 今回の場合は別名を使用しないため、空白のままにします。
   
      > [!div class="mx-imgBorder"]
      > ![パラメーターの作成](media/custom-commands/create-on-off-parameter.png)

   1. パラメーターのすべての構成を保存するには、 **[保存]** を選択します。
 
 ### <a name="add-subjectdevice-parameter"></a>SubjectDevice パラメーターを追加する 

   1. 次に、 **[追加]** をもう一度選択し、このコマンドを使用して制御できるデバイスの名前を表す 2 番目のパラメーターを追加します。 次の構成を使用します。
   

       | 設定            | 推奨値       |
       | ------------------ | --------------------- |
       | 名前               | `SubjectDevice`         |
       | Is Global          | チェック解除             |
       | 必須           | チェック               |
       | 必須パラメーターの応答     | Simple editor\(シンプルなエディター\) > `Which device do you want to control?`    | 
       | Type               | String                |          |
       | 構成      | 内部カタログから事前定義済み入力値を受け取る | 
       | 定義済み入力値 | `tv`, `fan`               |
       | 別名 (`tv`)      | `television`, `telly`     |

   1. **[保存]** を選びます。

### <a name="modify-example-sentences"></a>例文を変更する

パラメーターを使用したコマンドの場合、可能な組み合わせをすべて網羅する例文を追加すると役立ちます。 次に例を示します。

* 完全なパラメーター情報 - `turn {OnOff} the {SubjectDevice}`
* 部分パラメーター情報 - `turn it {OnOff}`
* パラメーターなし情報 - `turn something`

情報の度合いの異なる例文を利用することで、カスタム コマンド アプリケーションでは、部分的な情報で 1 回限りの解決とマルチターンの解決の両方を解決できます。

そのことを念頭に置き、下の提案のようにパラメーターを使用するように例文を編集します。

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

**[保存]** を選択します。

> [!TIP]
> 例文エディターでは、中かっこを使用してパラメーターを参照します。 - `turn {OnOff} the {SubjectDevice}` 前に作成したパラメーターでバックアップされるオートコンプリートには Tab キーを使用します。

### <a name="modify-completion-rules-to-include-parameters"></a>パラメーターを含めるように完了ルールを変更する

既存の完了ルール **ConfirmationResponse** を変更します。

1. **[条件]** セクションで、 **[条件の追加]** を選択します。
1. **[新しい条件]** ウィンドウの **[種類]** リストで、 **[必要なパラメーター]** を選択します。 下のチェックリストで、**OnOff** と **SubjectDevice** の両方を選択します。
1. **[IsGlobal]** をオフのままにします。
1. **［作成］** を選択します
1. **[アクション]** セクションで、既存の **[Send speech response]\(音声応答の送信\)** アクションにカーソルを移動して編集ボタンを選択することで、そのアクションを編集します。 今回は、新しく作成した **OnOff** と **SubjectDevice** のパラメーターを使用します。

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. **[保存]** を選択します。

### <a name="try-it-out"></a>試してみる
1. 右側のペインの上部にある **[トレーニング]** アイコンを選択します。

1. トレーニングが完了したら、 **[テスト]** を選択します。 **[Test your application]\(アプリケーションのテスト\)** ウィンドウが表示されます。
 いくつかやりとりしてみてください。

    - 入力: turn off the tv (テレビを消して)
    - 出力:Ok, turning off the tv (了解です。テレビを消します)
    - 入力: turn off the television (テレビを消して)
    - 出力:Ok, turning off the tv (了解です。テレビを消します)
    - 入力: turn it off (それを消して)
    - 出力:Which device do you want to control? (どのデバイスを制御しますか?)
    - 入力: the tv (テレビ)
    - 出力:Ok, turning off the tv (了解です。テレビを消します)

## <a name="configure-parameters-for-settemperature-command"></a>SetTemperature コマンドのパラメーターを構成する

**SetTemperature** コマンドを変更して、ユーザーの指示に従って温度を設定できるようにします。

次の構成を使用して新しいパラメーター **Temperature** を追加します。

| 構成      | 推奨値     |
| ------------------ | ----------------|
| 名前               | `Temperature`           |
| 必須           | チェック         |
| 必須パラメーターの応答      | Simple editor\(シンプルなエディター\) > `What temperature would you like?`
| Type               | Number          |


発話の例を編集して次の値にします。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

次の構成に従って、既存の完了ルールを編集します。

| 構成      | 推奨値     |
| ------------------ | ----------------|
| 条件         | 必須のパラメーター > Temperature           |
| Actions           | Send speech response\(音声応答の送信\) > `Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>試してみる

いくつかの相互作用で変更の**トレーニング**と**テスト**を行います。

- 次の内容を入力します。Set Temperature (温度を設定して)
- 出力:What temperature would you like? (何度にしますか?)
- 次の内容を入力します。50 degrees (50 度)
- 出力:Ok, setting temperature to 50 degrees (はい、温度を 50 度に設定します)

## <a name="configure-parameters-to-the-setalarm-command"></a>SetAlarm コマンドにパラメーターを構成する

以下の構成で **DateTime** というパラメーターを追加します。

   | 設定                           | 推奨値                     | 
   | --------------------------------- | ----------------------------------------|
   | 名前                              | `DateTime`                               |
   | 必須                          | チェック                                 |
   | 必須パラメーターの応答   | Simple editor\(シンプルなエディター\) > `For what time?`            | 
   | Type                              | DateTime                                |
   | 日付の既定値                     | 日付が指定されていない場合、今日に設定します            |
   | 時間の既定値                     | 時間が指定されていない場合、一日の始まりに設定します     |


> [!NOTE]
> この記事では、主に文字列型、数値型、DateTime 型のパラメーターを使用しました。 サポートされているすべてのパラメーターの型とそのプロパティの一覧については、[リファレンス](./custom-commands-references.md)を参照してください。


発話の例を編集して次の値にします。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

次の構成に従って、既存の完了ルールを編集します。

   | 設定    | 推奨値                               |
   | ---------- | ------------------------------------------------------- |
   | Actions    | 音声応答の送信 - `Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>試してみる

変更を**トレーニング**して**テスト**します。
- 次の内容を入力します。Set alarm for tomorrow at noon
- 出力:Ok, alarm set for 2020-05-02 12:00:00 (はい、アラームを 2020-05-02 12:00:00 に設定しました)
- 次の内容を入力します。アラームを設定する
- 出力:What time? (いつに設定しますか?)
- 次の内容を入力します。5 時
- 出力:Ok, alarm set for 2020-05-01 17:00:00 (はい、アラームを 2020-05-01 17:00:00 に設定しました)


## <a name="try-out-all-the-commands"></a>すべてのコマンドを試す

異なるコマンドに関連する発話を使用して、3 つのコマンドをすべてまとめてテストします。 異なるコマンドを切り替えられることにご注意ください。

- 次の内容を入力します。アラームを設定する
- 出力:For what time? (何時に設定しますか?)
- 次の内容を入力します。Turn on the tv (テレビをつけて)
- 出力:Ok, turning the tv on (はい、テレビをつけます)
- 次の内容を入力します。アラームを設定する
- 出力:For what time? (何時に設定しますか?)
- 次の内容を入力します。5 時
- 出力:Ok, alarm set for 2020-05-01 17:00:00 (はい、アラームを 2020-05-01 17:00:00 に設定しました)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: コマンドのパラメーターに構成を追加する](./how-to-custom-commands-add-parameter-configuration.md)
