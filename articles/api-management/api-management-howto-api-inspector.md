---
title: チュートリアル - 要求トレースを使用して Azure API Management で API をデバッグする
description: このチュートリアルの手順に従って、Azure API Management でトレースを有効にし、要求プロセスのステップを検査します。
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542316"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>チュートリアル:要求トレースを使用して API をデバッグする

このチュートリアルでは、API のデバッグとトラブルシューティングに役立つように、Azure API Management で要求処理を検査 (トレース) する方法について説明します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 呼び出しの例をトレースする
> * 要求プロセスのステップを確認する

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API インスペクター":::

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ 次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

## <a name="verify-allow-tracing-setting"></a>[トレースを許可] 設定を確認する 

API に使用するサブスクリプションの **[トレースを許可]** 設定を有効にする必要があります。 すべてのアクセス許可が組み込まれたサブスクリプションを使用している場合は、既定で有効になっています。 ポータルで確認するには、API Management インスタンスに移動し、 **[サブスクリプション]** を選択します。

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="サブスクリプションの [トレースを許可]":::

## <a name="trace-a-call"></a>呼び出しのトレース

1. [Azure portal](https://portal.azure.com) にサインインし、API Management インスタンスに移動します。
1. **[API]** を選択します。
1. API の一覧で **[Demo Conference API]\(デモ会議 API\)** を選択します。
1. **[テスト]** タブを選びます。
1. **[GetSpeakers]** 操作を選択します。
1. HTTP 要求ヘッダーに、**Ocp-Apim-Trace: True** と **Ocp-Apim-Subscription-Key** の有効な値が含まれていることを確認します。 そうでない場合は、 **[+ ヘッダーの追加]** を選択してヘッダーを追加します。
1. **[送信]** を選択して、API 呼び出しを行います。

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="API トレースを構成する":::

> [!TIP]
> **Ocp-Apim-Subscription-Key** が HTTP 要求に自動的に入力されない場合は、ポータルで取得できます。 **[サブスクリプション]** を選択し、サブスクリプションのコンテキスト メニュー ( **...** ) を開きます。 **[キーの表示/非表示]** を選択ます。 必要に応じて、キーを再生成することもできます。 次に、キーをヘッダーに追加します。

## <a name="review-trace-information"></a>トレース情報を確認する

1. 呼び出しが完了したら、 **[HTTP 応答]** の **[トレース]** タブに移動します。
1. 詳細なトレース情報に移動するには、次のいずれかのリンクを選択します: **[受信]** 、 **[バックエンド]** 、 **[送信]** 。

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="応答トレースを確認する":::

    * **[受信]** - API Management が呼び出し元から受信した元の要求と、その要求に適用されているポリシーが表示されます。 たとえば、「[チュートリアル: API を変換および保護する](transform-api.md)」のポリシーを追加した場合、ここに表示されます。

    * **[バックエンド]** - API Management が API バックエンドに送信した要求と、受信した応答が表示されます。

    * **[送信]** - 呼び出し元に送り返される前に応答に適用されるポリシーが表示されます。

    > [!TIP]
    > 各ステップには、API Management が要求を受信してからの経過時間も表示されます。

1. **[メッセージ]** タブの **ocp-apim-trace-location** ヘッダーには、Azure Blob Storage に格納されているトレース データの場所が表示されます。 必要に応じて、この場所に移動してトレースを取得します。

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Azure Storage 内のトレースの場所":::
## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 呼び出しの例をトレースする
> * 要求プロセスのステップを確認する

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [リビジョンの使用](api-management-get-started-revise-api.md)
