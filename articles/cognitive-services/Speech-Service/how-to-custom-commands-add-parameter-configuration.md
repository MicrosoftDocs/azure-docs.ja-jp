---
title: 方法:パラメーターを外部エンティティのエンティティとして構成する
titleSuffix: Azure Cognitive Services
description: この記事では、Web エンドポイントを介して公開されているカタログを参照するように文字列パラメーターを構成する方法について説明します。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284185"
---
# <a name="add-configurations-to-commands-parameters"></a>コマンドのパラメーターに構成を追加する

この記事では、次のような高度なパラメーターの構成について詳しく説明します。

 - カスタム コマンド アプリケーションの外部で定義されたセットにパラメーター値を含める方法
 - パラメーターの値に対する検証句を追加する方法

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。

> [!div class="checklist"]
> * [方法: 単純なコマンドを使用してアプリケーションを作成する](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [方法: コマンドにパラメーターを追加する](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>パラメーターを外部カタログ エンティティとして構成する

このセクションでは、Web エンドポイントでホストされている外部カタログを参照するように文字列型のパラメーターを構成します。 これにより、カスタム コマンド アプリケーションを編集することなく、外部カタログを個別に更新することができます。 この方法は、カタログ エントリの数が多い場合に便利です。

**TurnOnOff** コマンドの **SubjectDevice** パラメーターを再利用します。 このパラメーターの現在の構成では、**内部カタログから定義済みの入力を受け入れます**。 これは、パラメーターの構成で定義されているデバイスの静的なリストを参照します。 このコンテンツを、独立して更新できる外部データ ソースに移動します。

これを行うには、最初に新しい Web エンドポイントを追加します。 左側のペインの **[Web endpoint]\(Web エンドポイント\)** セクションに移動し、次の構成で新しい Web エンドポイントを追加します。

| 設定 | 推奨値 |
|----|----|
| Name | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Method | GET |


指定されている URL の値が自分の環境で機能しない場合は、制御可能なデバイスのリストで構成される JSON を返す簡単な Web エンドポイントを構成してホストする必要があります。 Web エンドポイントでは、次のように書式設定された JSON を返す必要があります。
    
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


次に、**SubjectDevice** パラメーターの設定ページに移動し、プロパティを次のように変更します。

| 設定 | 推奨値 |
| ----| ---- |
| Configuration (構成) | 外部カタログから定義済みの入力を受け取る |                               
| Catalog endpoint (カタログ エンドポイント) | getDevices |
| Method | GET |

次に、 **[Save]\(保存\)** を選択します。

> [!IMPORTANT]
> 左側のペインの **[Web endpoint]\(Web エンドポイント\)** セクションで Web エンドポイントを設定しない限り、外部カタログからの入力を受け入れるパラメーターを構成するオプションは表示されません。

### <a name="try-it-out"></a>試してみる

**[Train]\(トレーニング\)** を選択し、トレーニングの完了を待ちます。 トレーニングが完了したら、 **[Test]\(テスト\)** を選択して、いくつか会話を試してみます。

* 入力: turn on (オンにする)
* 出力:Which device do you want to control? (どのデバイスを制御しますか?)
* 入力: lights (ライト)
* 出力:Ok, turning the lights on (はい、ライトをオンにします)

> [!NOTE]
> Web エンドポイントでホストされているすべてのデバイスを制御できることに注意してください。 その場合でも、新しい変更をテストする場合は、アプリケーションをトレーニングし、アプリケーションを再発行する必要があります。

## <a name="add-validation-to-parameters"></a>パラメーターに検証を追加する

**Validations\(検証\)** は、特定のパラメーターの型に適用できるコンストラクトであり、パラメーターの値に対する制約を構成し、値が制約内に収まらない場合は修正を求めることができます。 検証コンストラクトを拡張するパラメーター型の完全な一覧については、[リファレンス](./custom-commands-references.md)のページを参照してください。

**SetTemperature** コマンドを使用して検証をテストします。 次の手順を使用して、**Temperature** パラメーターの検証を追加します。

1. 左側のペインで **SetTemperature** コマンドを選択します。
1. 中央のペインで **Temperature** を選択します。
1. 右のペインで **[Add a validation]\(検証を追加する\)** を選択します。
1. **[New validation]\(新しい検証\)** ウィンドウで、次のように検証を構成して、 **[Create]\(作成\)** を選択します。


    | パラメーターの構成 | 推奨値 | 説明 |
    | ---- | ---- | ---- |
    | Min Value (最小値) | `60` | Number パラメーターの場合、このパラメーターで想定できる最小値 |
    | Max Value (最大値) | `80` | 数値パラメーターの場合、このパラメーターで想定できる最大値 |
    | Failure response (失敗応答) |  [Simple editor]\(シンプル エディター\) > [First Variation]\(最初のバリエーション\) > 「`Sorry, I can only set temperature between 60 and 80 degrees`」 | 検証が失敗した場合に新しい値の入力を求めるプロンプトを表示します |

    > [!div class="mx-imgBorder"]
    > ![範囲検証を追加する](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>試してみる

1. 右側のペインの上部にある **[Train]\(トレーニング\)** アイコンを選択します。

1. トレーニングが完了したら、 **[Test]\(テスト\)** を選択して、いくつか会話を試してみます。

    - 次の内容を入力します。Set the temperature to 72 degrees (温度を 72 度に設定して)
    - 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)
    - 次の内容を入力します。Set the temperature to 72 degrees (温度を 45 度に設定して)
    - 出力:Sorry, I can only set temperature between 60 and 80 degrees (申し訳ありません、設定できる温度は 60 度から 80 度までです)
    - 入力: make it 72 degrees instead (それでは、72 度にして)
    - 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: 相互作用ルールを追加する](./how-to-custom-commands-add-interaction-rules.md)
