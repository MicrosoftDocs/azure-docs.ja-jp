---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー インスタンスを検証する方法を学びます。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455119"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>感情分析コンテナー インスタンスを検証する

1. **[概要]** タブを選択し、IP アドレスをコピーします。
1. 新しいブラウザー タブを開き、`http://<IP-address>:5000 (http://55.55.55.55:5000` のように IP アドレスを使用します。 コンテナーのホーム ページが表示され、コンテナーが実行中であることを知らせます。

    ![コンテナーのホーム ページを表示してコンテナーが実行中であることを確認する](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **[Service API Description]\(サービス API の説明\)** リンクを選択して、コンテナーの Swagger ページに移動します。

1. いずれかの **POST** APIを選択して **[試してみる]** を選択します。入力の例を含むパラメーターが表示されます。

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

1. 入力を次の JSON に置き換えます。

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

1. **showStats** を true に設定します。

1. **[実行]** を選択してテキストの感情を判定します。

    コンテナーにパッケージ化されたモデルでは、0 ～ 1 のスコアが生成されます(0 は否定的、1 は肯定的)。

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

これで、応答ペイロード JSON のドキュメント `id` を、元の要求ペイロード ドキュメント `id` に関連付けて、非常に肯定的な感情を示す `.98` を超えるスコアがあったことを確認できます。