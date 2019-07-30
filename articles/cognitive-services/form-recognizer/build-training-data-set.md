---
title: カスタム モデルのトレーニング データ セットを作成する方法 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: お使いのトレーニング データ セットが Form Recognizer モデルのトレーニングに最適化されていることを確認する方法を学習します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 643f0d6dd3ee073bd19f8697346689523032ad9f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592639"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>カスタム モデルのトレーニング データ セットを作成する

Form Recognizer のカスタム モデルを使用する場合は、モデルを業界固有のフォームに合わせてトレーニングできるように、独自のトレーニング データを提供します。 モデルは、5 つの入力フォームか、空のフォーム (ファイル名に "empty" という単語を含める必要があります) と 2 つの入力フォームを使用して、トレーニングできます。 トレーニングに使用する十分な数の入力フォームがある場合でも、トレーニング データ セットに空のフォームを追加することで、モデルの精度を高めることができます。

## <a name="training-data-tips"></a>トレーニング データのヒント

トレーニング用に最適化されたデータ セットを使用することが重要です。 以下のヒントを使用して、[モデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)操作で最良の結果が得られるようにしてください。

* 可能であれば、画像ベースのドキュメントではなく、テキストベースの PDF ドキュメントを使用します。 スキャンした PDF は画像として処理されます。
* 1 つの空のフォームと 2 つの入力フォームを用意できる場合はそれらを使用します。
* 入力フォームの場合は、すべてのフィールドに入力されている例を使用します。
* 各フィールドに異なる値が含まれたフォームを使用します。
* フォームの画像の品質が低い場合は、より大きなデータ セット (たとえば 10 から 15 の画像) を使用します。

## <a name="general-input-requirements"></a>一般的な入力の要件

トレーニング データ セットが、すべての Form Recognizer コンテンツの入力の要件にも従っていることを確認します。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>トレーニング データをアップロードする

トレーニングに使用するフォーム ドキュメントのセットをまとめたら、それを Azure Blob Storage コンテナーにアップロードする必要があります。 コンテナーを含む Azure Storage アカウントを作成する方法がわからない場合は、[Azure portal の Azure Storage に関するクイックスタート](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)に従ってください。

### <a name="organize-your-data-in-subfolders-optional"></a>データをサブフォルダーに整理する (オプション)

[モデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API の既定では、ストレージ コンテナーのルートにあるフォーム ドキュメントのみが使用されます。 ただし、API 呼び出しで指定した場合は、サブフォルダー内のデータを使用してトレーニングすることができます。 通常、[モデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)呼び出しの本文は次の形式になります。ここで、`<SAS URL>` は、コンテナーの共有アクセス署名 URL です。

```json
{
  "source":"<SAS URL>"
}
```

次の内容を要求本文に追加すると、API によってサブフォルダーにあるドキュメントがトレーニングされます。 `"prefix"` フィールドはオプションであり、トレーニング データ セットを、指定された文字列で始まるパスのファイルに制限します。 そのため、たとえば、値 `"Test"` の場合、API は、"Test" という単語で始まるファイルまたはフォルダーのみを調べます。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>次の手順

トレーニング データ セットの作成方法を習得したので、クイックスタートに従って、カスタム Form Recognizer モデルをトレーニングし、お使いのフォームでの使用を開始してください。

* [クイック スタート:cURL を使用してモデルをトレーニングし、フォーム データを抽出する](./quickstarts/curl-train-extract.md)
* [クイック スタート:Python で REST API を使用してモデルをトレーニングし、フォーム データを抽出する](./quickstarts/python-train-extract.md)

