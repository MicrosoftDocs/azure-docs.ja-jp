---
title: Web エンドポイントからコマンドを更新する
titleSuffix: Azure Cognitive Services
description: Web エンドポイントの呼び出しを使用して、コマンドの状態を更新する方法について説明します。
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560272"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Web エンドポイントからコマンドを更新する

クライアント アプリケーションから、音声入力を使用しないで、実行中のコマンドの状態を更新する必要がある場合は、Web エンドポイントの呼び出しを使用してコマンドを更新できます。

この記事では、Web エンドポイントから実行中のコマンドを更新する方法について説明します。

## <a name="prerequisites"></a>前提条件
> [!div class = "checklist"]
> * 以前に[作成したカスタム コマンド アプリ](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure 関数の作成 

この例では、次の入力 (またはこの入力のサブセット) をサポートする、HTTP によってトリガーされる [Azure 関数](../../azure-functions/index.yml)が必要です。

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

この入力のキー属性を確認します。

| 属性 | 説明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 会話の一意の識別子。 この ID はクライアント アプリから生成できることに注意してください。 |
| **currentCommand** | 会話で現在アクティブになっているコマンド。 |
| **name** | コマンドの名前。 `parameters` 属性は、パラメーターの現在の値を含むマップです。 |
| **currentGlobalParameters** | `parameters` のようなマップですが、グローバル パラメーターに使用されます。 |

Azure 関数の出力で、次の形式がサポートされている必要があります。

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

この形式は、[クライアントからコマンドを更新する](./how-to-custom-commands-update-command-from-client.md)ときに使用したものと同じなので、見覚えがあるかもしれません。 

次に、Node.js に基づいて Azure 関数を作成します。 次のコードをコピーして貼り付けます。

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

カスタム コマンドからこの Azure 関数を呼び出すと、会話の現在の値が送信されます。 更新するパラメーターを返すか、または現在のコマンドをキャンセルします。

## <a name="update-the-existing-custom-commands-app"></a>既存の Custom Commands アプリを更新する

Azure 関数と既存の Custom Commands アプリを接続します。

1. `IncrementCounter` という名前の新しいコマンドを追加します。
1. 値 `increment` を含むサンプル文を 1 つだけ追加します。
1. `Counter` という名前 (Azure 関数で指定したものと同じ名前) で `Number` 型の新しいパラメーターを追加します。既定値は `0` です。
1. Azure 関数の URL を使用し、 **[Remote updates]/(リモート更新/)** を **[有効]** に設定して、`IncrementEndpoint` という名前の新しい Web エンドポイントを追加します。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="リモート更新を使用した Web エンドポイントの設定を示すスクリーンショット。":::
1. **IncrementRule** という名前の新しい相互作用ルールを作成し、**Call web endpoint** アクションを追加します。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="相互作用ルールの作成を示すスクリーンショット。":::
1. アクション構成で、`IncrementEndpoint` を選択します。 `Counter` の値を使用して **[On success]/(成功時/)** を **[Send speech response]** に構成し、 **[On failure]\(失敗時\)** をエラー メッセージと共に構成します。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Web エンドポイントを呼び出すためのインクリメント カウンターの設定を示すスクリーンショット。":::
1. ルールの実行後の状態を **ユーザー入力待機** に設定します。

## <a name="test-it"></a>テストする

1. アプリを保存してトレーニングします。
1. **[Test]** を選択します。
1. 数回 `increment` を送信します (`IncrementCounter` コマンドの例の文)。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="インクリメント カウンターの例を示すスクリーンショット。":::

Azure 関数によってターンごとに `Counter` パラメーターの値がどのようにインクリメントされるかを確認してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム コマンド アプリケーションの CI/CD プロセスを有効にする](./how-to-custom-commands-deploy-cicd.md)