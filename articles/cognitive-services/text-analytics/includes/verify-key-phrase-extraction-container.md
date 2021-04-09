---
title: キー フレーズ抽出コンテナー インスタンスを確認する
titleSuffix: Azure Cognitive Services
description: キー フレーズ抽出コンテナー インスタンスを確認する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5dca4828a5c1127133461ddf9fc06099fc176b68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009911"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>キー フレーズ抽出コンテナー インスタンスを確認する

1. **[概要]** タブを選択し、IP アドレスをコピーします。
1. 新しいブラウザー タブを開き、IP アドレスを入力します。 たとえば、「`http://<IP-address>:5000 (http://55.55.55.55:5000`)」と入力します。 コンテナーのホーム ページが表示され、コンテナーが実行中であることを知らせます。

    ![コンテナーのホーム ページを表示してコンテナーが実行中であることを確認します](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **[Service API Description]\(サービス API の説明\)** リンクを選択して、コンテナーの Swagger ページに移動します。

1. いずれかの **POST** APIを選択して **[試してみる]** を選択します。次の入力の例を含むパラメーターが表示されます。

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. 入力を次の JSON コンテンツに置き換えます。

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. **showStats** を `true` に設定します。

1. **[実行]** を選択してテキストの感情を判定します。

    コンテナーにパッケージ化されたモデルでは、0 から 1 のスコアが生成されます (0 は否定的、1 は肯定的)。

    返される JSON 応答には、更新されたテキスト入力の感情が含まれます。

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

これで、応答ペイロードの JSON データのドキュメント `id` を、元の要求ペイロード ドキュメント `id` に関連付けることができます。 結果的に生成されるドキュメントには `keyPhrases` 配列が与えられます。この配列には、対応する入力ドキュメントから抽出されたキーフレーズの一覧が含まれます。 また、結果的に生成される各ドキュメントに、`characterCount` や `transactionCount` など、さまざまな統計値があります。