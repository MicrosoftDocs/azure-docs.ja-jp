---
title: 方法:カスタム コマンドに確認を追加する
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンドでコマンドに対する確認を実装する方法を説明します。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 7d6c0928196c9e8e1abf6aa7f724a58753ce3d2a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289047"
---
# <a name="add-interaction-rules"></a>相互作用ルールを追加する

この記事では、**相互作用ルール**について説明します。 これらは、より具体的または複雑な状況に対処するための追加ルールです。 独自のカスタム相互作用ルールを自由に作成できますが、この記事では、次の対象となるシナリオの相互作用ルールを使用します。

* コマンドの確認
* コマンドへの 1 ステップの修正の追加

相互作用ルールの詳細については、[リファレンス](./custom-commands-references.md) セクションを参照してください。

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。
> [!div class="checklist"]
> * [方法: 単純なコマンドを使用してアプリケーションを作成する](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [方法: コマンドにパラメーターを追加する](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>コマンドに確認を追加する

確認を追加するには、**SetTemperature** コマンドを使用します。 確認を行うには、次の手順のようにして相互作用ルールを作成します。

1. 左側のペインで **SetTemperature** コマンドを選択します。
1. 中央のペインで **[追加]** を選択して、相互作用ルールを追加ます。 次に、 **[Interaction rules]\(相互作用ルール\)**  >  **[Confirm command]\(確認コマンド\)** を選択します。

    この操作により、3 つの相互作用ルールが追加されます。このルールは、ユーザーにアラームの日付と時刻の確認を要求し、次のターンで確認 (yes/no) を期待します。

    1. 次の構成に従って、**確認コマンド**相互作用ルールを変更します。
        1. **[Name]\(名前\)** を「**Confirm temperature**」 (温度確認) に変更します。
        1. **[Required parameters]\(必須パラメーター\)**  > **Temperature** で新しい条件を追加します。
        1. 新しいアクションとして、 **[Type]\(種類\)**  >  **[Send speech response]\(音声応答を送信する\)**  > 「**Are you sure you want to set the temperature as {Temperature} degrees?** 」 (温度を {Temperature} 度に設定しますか?) を追加します。
        1. **[Expectations]\(予測\)** セクションは既定値 **[Expecting confirmation from user]\(ユーザーからの確認が必要\)** のままにします。
      
         > [!div class="mx-imgBorder"]
         > ![必須パラメーターの応答を作成する](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 成功した確認 (ユーザーが "はい" と言った) を処理するように、 **[Confirmation succeeded]\(確認成功\)** 相互作用ルールを変更します
      
          1. **[Name]\(名前\)** を「**Confirmation temperature succeeded**」 (温度確認成功) に変更します。
          1. 既存の **[Confirmation was successful]\(確認が成功した\)** 条件をそのままにします。
          1. **[Type]\(種類\)**  >  **[Required parameters]\(必須パラメーター\)**  > **Temperature** で新しい条件を追加します。
          1. **[Post-execution state]\(実行後の状態\)** は既定値の **[Execute completion rules]\(完了ルールの実行\)** のままにします。

    1. 確認が拒否された (ユーザーが "いいえ" と言った) 場合のシナリオを処理するように、 **[Confirmation denied]\(確認拒否\)** 相互作用ルールを変更します。

          1. **[Name]\(名前\)** を「**Confirmation temperature denied**」 (温度確認拒否) に変更します。
          1. 既存の **[Confirmation was denied]\(確認が拒否された\)** 条件をそのままにします。
          1. **[Type]\(種類\)**  >  **[Required parameters]\(必須パラメーター\)**  > **Temperature** で新しい条件を追加します。
          1. 新しいアクションとして、 **[Type]\(種類\)**  >  **[Send speech response]\(音声応答を送信する\)**  > 「**No problem. what temperature then?** 」 (ご安心ください。それでは何度にしますか) を追加します。
          1. **[Post-execution state]\(実行後の状態\)** は既定値の **[Wait for user's input]\(ユーザーの入力を待つ\)** のままにします。

> [!IMPORTANT]
> この記事では、組み込みの確認機能を使用しました。 相互作用ルールを 1 つずつ手動で追加することもできます。
   

### <a name="try-out-the-changes"></a>変更を試す

**[トレーニング]** を選択し、トレーニングが完了するのを待って、 **[テスト]** を選択します。

- **入力**:Set temperature to 80 degrees (温度を 80 度に設定する)
- **出力**: are you sure you want to set the temperature as 80 degrees? (温度を 80 度に設定しますか?)
- **入力**:いいえ
- **出力**:ご安心ください。 What temperature then? (それでは何度にしますか)
- **入力**:72 degrees (72 度)
- **出力**: are you sure you want to set the temperature as 72 degrees? (温度を 72 度に設定しますか?)
- **入力**:はい
- **出力**:OK, setting temperature to 83 degrees (OK、温度を 83 度に設定します)


## <a name="implement-corrections-in-a-command"></a>コマンドで修正を実装する

このセクションでは、フルフィルメント アクションが既に実行された後で使用されるワンステップの修正を構成します。 コマンドがまだ満たされていない場合に、既定で修正が有効になる例も示します。 コマンドが完了していないときに修正を追加するには、新しいパラメーター **AlarmTone** を追加します。

左側のペインで **SetAlarm** コマンドを選択し、新しいパラメーター **AlarmTone** を追加します。
        
- **[Name]\(名前\)**  > **AlarmTone**
- **[Type]\(型\)**  > **String**
- **[Default Value]\(既定値\)**  >  **[Chimes]\(チャイム\)**
- **[Configuration]\(構成\)**  > 「**Accept predefined input values from the internal catalog**」 (内部カタログから定義済み入力値を受け取る)
- **[Predefined input values]\(定義済みの入力値\)**  > 個別の定義済み入力として **[Chimes]\(チャイム\)** 、 **[Jingle]\(チリンチリン\)** 、 **[Echo]\(エコー\)**


次に、**DateTime** パラメーターに対する応答を、「**Ready to set alarm with tone as {AlarmTone}. For what time?** 」 (トーンをチャイムとして設定する準備ができました。何時に設定しますか?) に更新します。 次に、完了ルールを次のように変更します。

1. 既存の完了ルール **ConfirmationResponse** を選択します。
1. 右側のペインで、既存のアクションをポイントし、 **[編集]** を選択します。
1. 音声応答を、「**OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}.** 」 (OK、アラームを {DateTime} に設定しました。アラーム音は {AlarmTone} です。) に更新します。

### <a name="try-out-the-changes"></a>変更を試す

**[トレーニング]** を選択し、トレーニングが完了するのを待って、 **[テスト]** を選択します。
次の発話を試します。

- **入力**:Set an alarm. (アラームを設定する)
- **出力**:Ready to set alarm with tone as Chimes. (トーンをチャイムとして設定する準備ができました。) For what time? (何時に設定しますか?)
- **入力**:Set an alarm with the tone as Jingle for 9 am tomorrow. (トーンをチリンチリンにして明日午前 9 時にアラームを設定する)
- **出力**:OK, alarm set for 2020-05-30 09:00:00. (OK、アラームを 2020-05-30 09:00:00 に設定しました。) The alarm tone is Jingle. (アラーム音はチリンチリンです。)

> [!IMPORTANT]
> 実行中のコマンドで明示的に構成しなくても、アラームのトーンを変更できます (たとえば、コマンドがまだ完了していない場合)。 "*コマンドがまだ処理されていない場合、ターン番号に関係なく、すべてのコマンド パラメーターに対して修正が既定で有効になります。* "

### <a name="correction-when-command-is-completed"></a>コマンドが完了したときの修正

カスタム コマンド プラットフォームでは、コマンドが完了している場合でも、ワンステップ修正機能が提供されます。 この機能は、既定で有効になっています。 明示的に構成する必要があります。 以下の手順のようにしてワンステップ修正を構成します。

1. **SetAlarm** コマンドで、種類が **[Update previous command]\(前のコマンドを更新する\)** の相互作用ルールを追加して、以前に設定したアラームを更新します。 相互作用ルールの既定の **[名前]** を、「**前のアラームを更新する**」に変更します。
1. 既定の条件 **[Previous command needs to be updated]\(前のコマンドを更新する必要がある\)** はそのままにします。
1. **[Type]\(種類\)**  >  **[Required Parameter]\(必須パラメーター\)**  > **DateTime** で新しい条件を追加します
1. 新しいアクションとして、 **[Type]\(種類\)**  >  **[Send speech response]\(音声応答を送信する\)**  >  **[Simple editor]\(簡易エディター\)**  > 「**Updating previous alarm time to {DateTime}.** 」 (前のアラーム時刻を {DateTime} に更新します) を追加します。
1. **[Post-execution state]\(実行後の状態\)** は既定値の **[Command completed]\(コマンド完了\)** のままにします。

### <a name="try-out-the-changes"></a>変更を試す

**[トレーニング]** を選択し、トレーニングが完了するのを待って、 **[テスト]** を選択します。

- **入力**:Set an alarm. (アラームを設定する)
- **出力**:Ready to set alarm with tone as Chimes. (トーンをチャイムとして設定する準備ができました。) For what time? (何時に設定しますか?)
- **入力**:Set an alarm with the tone as Jingle for 9 am tomorrow. (トーンをチリンチリンにして明日午前 9 時にアラームを設定する)
- **出力**:OK, alarm set for 2020-05-21 09:00:00. (OK、アラームを 2020-05-21 09:00:00 に設定しました。) The alarm tone is Jingle. (アラーム音はチリンチリンです。)
- **入力**:No, 8 am. (違う、午前 8 時)
- **出力**:Updating previous alarm time to 2020-05-29 08:00. (前のアラーム時刻を 2020-05-29 08:00 に更新します。)

> [!NOTE]
> 実際のアプリケーションでは、この修正ルールの **[Actions]\(アクション\)** セクションで、クライアントにアクティビティを返信するか、または HTTP エンドポイントを呼び出し、システムのアラーム時刻を更新する必要もあります。 このアクションでは、アラーム時刻を更新するだけにして、コマンドの他の属性は更新しないようにする必要があります。 この場合、それはアラーム音です。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: 音声応答の言語生成テンプレートを追加する](./how-to-custom-commands-add-language-generation-templates.md)
