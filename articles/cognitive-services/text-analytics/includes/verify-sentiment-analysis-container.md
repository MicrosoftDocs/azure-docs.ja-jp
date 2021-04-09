---
title: 感情分析コンテナー インスタンスを検証する
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー インスタンスを検証する方法を学びます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d0a5e09f1d697c2f5f842edfb06be4b13229d095
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009886"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>感情分析コンテナー インスタンスを検証する

1. **[概要]** タブを選択し、IP アドレスをコピーします。
1. 新しいブラウザー タブを開き、IP アドレスを入力します。 たとえば、「`http://<IP-address>:5000 (http://55.55.55.55:5000`)」と入力します。 コンテナーのホーム ページが表示され、コンテナーが実行中であることを知らせます。

    ![コンテナーのホーム ページを表示してコンテナーが実行中であることを確認します](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **[Service API Description]\(サービス API の説明\)** リンクを選択して、コンテナーの Swagger ページに移動します。

1. いずれかの **POST** APIを選択して **[試してみる]** を選択します。次の入力の例を含むパラメーターが表示されます。

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
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

    コンテナーにパッケージ化されたモデルでは、0 から 1 のスコアが生成されます (0 は否定的センチメント、1 は肯定的センチメント)。

    返される JSON 応答には、更新されたテキスト入力の感情が含まれます。

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
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

これで、応答ペイロードの JSON データのドキュメント `id` を、元の要求ペイロード ドキュメント `id` に関連付けることができます。 `0.98` を超えるスコアは、非常に肯定的な感情を示しています。