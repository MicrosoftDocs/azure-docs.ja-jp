---
title: Custom Speech のモデルとエンドポイントのライフサイクル - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Speech では、適応用として基本モデルが提供され、自分のデータからカスタム モデルを作成できます。 この記事では、モデルと、それらのモデルに使用されるエンドポイントのタイムラインについて説明します。
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103547955"
---
# <a name="model-and-endpoint-lifecycle"></a>モデルとエンドポイントのライフサイクル

Custom Speech には、*基本モデル* と *カスタム モデル* の両方が使用されます。 言語ごとに、1 つ以上の基本モデルがあります。 一般に、新しい音声モデルが通常の音声サービスにリリースされると、新しい基本モデルとして Custom Speech サービスにもインポートされます。 6 から 12 か月ごとに更新されます。 普通は最新のモデルの方が正確性が高いため、古いモデルは時間の経過と共にあまり役に立たなくなります。

対照的に、カスタム モデルは、選択した基本モデルを、特定の顧客シナリオからのデータに合わせて適応させることで作成されます。 ニーズに合った特定のカスタム モデルを確保した後は、それを長期間使用し続けることができます。 ただし、定期的に最新の基本モデルに更新し、それを時間の経過と共に追加データを使用して再トレーニングすることをお勧めします。 

モデルのライフサイクルに関しては、他に次のような用語が重要です。

* **適応**: 基本モデルを選択し、テキスト データやオーディオ データを使用して、ドメインやシナリオに合わせてカスタマイズすること
* **デコード**: モデルを使用して音声認識を実行すること (音声をテキストにデコードする)
* **エンドポイント**: 特定のユーザー "*のみ*" がアクセスできる、基本モデルまたはカスタム モデルのユーザー固有のデプロイ。

### <a name="expiration-timeline"></a>有効期限のタイムライン

新しいモデルや新機能が利用可能になると、古くて正確さが低下したモデルは廃止されます。モデルとエンドポイントの有効期限については、次のタイムラインを参照してください。

**基本モデル** 

* 適応: 1 年間使用できます。 モデルがインポートされたら、カスタム モデルを作成するために 1 年間使用できます。 1 年後には、より新しいバージョンの基本モデルから新しいカスタム モデルを作成する必要があります。  
* デコード: インポートの後で 2 年間使用できます。 したがって、このモデルで 2 年間はエンドポイントを作成し、バッチ トランスクリプトを使用することができます。 
* エンドポイント:デコードと同じタイムラインで使用できます。

**カスタム モデル**

* デコード: モデルが作成されてから 2 年間使用できます。 したがって、カスタム モデルを作成してから 2 年間 (バッチ、リアルタイム、テスト) 使用することができます。 2 年後には、通常、基本モデルが適合に非推奨になっているため、"*モデルを再トレーニングする必要があります*"。  
* エンドポイント:デコードと同じタイムラインで使用できます。

基本モデルまたはカスタム モデルいずれかの有効期限が切れると、常に *最新バージョンの基本モデル* にフォールバックします。 このため、実装が中断されることはありませんが、カスタム モデルの有効期限が切れると、"*特定のデータ*" に対して精度が低下する可能性があります。 モデルの有効期限は、Speech Studio の Custom Speech エリアの次の場所で確認できます。

* モデルのトレーニングの概要
* モデルのトレーニングの詳細
* デプロイの概要
* デプロイの詳細

モデルのトレーニングの概要からの例を示します。

![モデルのトレーニングの概要](media/custom-speech/custom-speech-model-training-with-expiry.png) モデルのトレーニングの詳細ページからの例も示します。

![モデルのトレーニングの詳細](media/custom-speech/custom-speech-model-details-with-expiry.png)

また、[`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) または [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) Custom Speech API を使用し、JSON 応答の `deprecationDates` プロパティで、有効期限を確認することもできます。

GetModel API 呼び出しの有効期限データの例を次に示します。 "DEPRECATIONDATES" に次が表示されます。 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Speech Studio のデプロイ セクションで、または Custom Speech API を使用して、エンドポイントで使用されているモデルを変更することにより、ダウンタイムなしで Custom Speech エンドポイントのモデルをアップグレードできます。

## <a name="next-steps"></a>次のステップ

* [モデルのトレーニングとデプロイ](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>その他のリソース

* [データを準備してテストする](./how-to-custom-speech-test-and-train.md)
* [データを検査する](how-to-custom-speech-inspect-data.md)