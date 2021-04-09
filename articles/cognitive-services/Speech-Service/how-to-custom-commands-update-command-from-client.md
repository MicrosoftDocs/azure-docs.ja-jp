---
title: クライアント アプリからコマンドを更新する
titleSuffix: Azure Cognitive Services
description: クライアント アプリケーションからコマンドを更新する方法について説明します。
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560289"
---
# <a name="update-a-command-from-a-client-app"></a>クライアント アプリからコマンドを更新する

この記事では、クライアント アプリケーションから実行中のコマンドを更新する方法について説明します。

## <a name="prerequisites"></a>前提条件
> [!div class = "checklist"]
> * 以前に[作成したカスタム コマンド アプリ](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>コマンドの状態を更新する

クライアント アプリケーションから、音声入力を使用しないで、実行中のコマンドの状態を更新する必要がある場合は、イベントを送信してコマンドを更新できます。

このシナリオを説明するため、次のイベント アクティビティを送信して、実行中のコマンド (`TurnOnOff`) の状態を更新します。 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

このアクティビティのキー属性を確認します。

| 属性 | 説明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | アクティビティの種類は `"event"` で、イベントの名前は `"RemoteUpdate"` である必要があります。 |
| **value** | 属性 `"value"` には、現在のコマンドを更新するために必要な属性が含まれています。 |
| **updatedCommand** | 属性 `"updatedCommand"` には、コマンドの名前が含まれています。 この属性の中で `"updatedParameters"` は、パラメーターの名前と更新後の値を含むマップです。 |
| **cancel** | 実行中のコマンドを取り消す必要がある場合は、属性 `"cancel"` を `true` に設定します。 |
| **updatedGlobalParameters** | 属性 `"updatedGlobalParameters"` は `"updatedParameters"` と同じようにマップされますが、グローバル パラメーターに使用されます。 |
| **processTurn** | アクティビティの送信後にターンを処理する必要がある場合は、属性 `"processTurn"` を `true` に設定します。 |

Custom Commands ポータルで、このシナリオをテストすることができます。

1. 前に作成したカスタム コマンド アプリケーションを開きます。 
1. **[トレーニング]** 、 **[テスト]** の順に選択します。
1. `turn` を送信します。
1. サイド パネルを開き、 **[Activity editor]\(アクティビティ エディター\)** を選択します。
1. 前のセクションで指定した `RemoteCommand` イベントを入力して送信します。
    > [!div class="mx-imgBorder"]
    > ![リモート コマンドのイベントを示すスクリーンショット。](media/custom-commands/send-remote-command-activity.png)

音声やテキストではなく、クライアントからのアクティビティを使用して、パラメーター `"OnOff"` の値が `"on"` に設定されていることに注目してください。

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>コマンドのパラメーターのカタログを更新する

パラメーターの有効なオプションのリストを構成すると、パラメーターの値がアプリケーションに対してグローバルに定義されます。 

この例では、`SubjectDevice` パラメーターに、会話に関係なくサポートされる値の固定リストが設定されます。

会話ごとに新しいエントリをパラメーターのカタログに追加する場合は、次のアクティビティを送信できます。

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
このアクティビティを使用して、コマンド `"TurnOnOff"` のパラメーター `"SubjectDevice"` のカタログに `"stereo"` のエントリを追加しました。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="カタログの更新を示すスクリーンショット。":::

いくつかの点にご注意ください。
- このアクティビティを送信する必要があるのは 1 回だけです (理想的には、接続を開始した直後)。
- このアクティビティを送信した後は、イベント `ParameterCatalogsUpdated` がクライアントに返送されるのを待つ必要があります。

## <a name="add-more-context-from-the-client-application"></a>クライアント アプリケーションからさらにコンテキストを追加する

クライアント アプリケーションから会話ごとに追加のコンテキストを設定し、後で Custom Commands アプリケーションで使用することができます。 

たとえば、Custom Commands アプリケーションに接続されているデバイスの ID と名前を送信するシナリオについて考えてみます。

このシナリオをテストするために、現在のアプリケーションに新しいコマンドを作成してみましょう。
1. `GetDeviceInfo` という名前の新しいコマンドを作成します。
1. `get device info` のサンプル文を追加します。
1. 完了ルール **Done** で、属性 `clientContext` が含まれる **Send speech response** アクションを追加します。
   ![コンテキストで音声を送信するための応答を示すスクリーンショット。](media/custom-commands/send-speech-response-context.png)
1. アプリケーションを保存し、トレーニングして、テストします。
1. テスト ウィンドウで、クライアント コンテキストを更新するアクティビティを送信します。

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. テキスト `get device info` を送信します。
   ![クライアント コンテキストを送信するためのアクティビティを示すスクリーンショット。](media/custom-commands/send-client-context-activity.png)

いくつかの点にご注意ください。
- このアクティビティを送信する必要があるのは 1 回だけです (理想的には、接続を開始した直後)。
- `clientContext` には複合オブジェクトを使用できます。
- アクティビティの送信や、Web エンドポイントの呼び出しなど、音声応答で `clientContext` を使用できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web エンドポイントからコマンドを更新する](./how-to-custom-commands-update-command-from-web-endpoint.md)
