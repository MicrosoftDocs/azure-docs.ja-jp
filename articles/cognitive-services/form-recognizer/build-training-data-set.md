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
ms.openlocfilehash: da9445b12ce6f35d249fc3af1a4a0ef560ba35de
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905093"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>カスタム モデルのトレーニング データ セットを作成する

Form Recognizer のカスタム モデルを使用する場合は、モデルを業界固有のフォームに合わせてトレーニングできるように、独自のトレーニング データを提供します。 

手動ラベルなしでトレーニングする場合は、5 つの入力済みフォームを使用するか、空のフォーム (ファイル名に "empty" という単語を含める必要があります) と 2 つの入力済みフォームを使用できます。 十分な数の入力済みフォームがある場合でも、トレーニング データ セットに空のフォームを追加することで、モデルの精度を高めることができます。

手動でラベル付けされたトレーニング データを使用する場合は、同じ種類の少なくとも 5 つの入力済みフォームから開始する必要があります。 必要なデータ セットに加え、ラベル付けされていないフォームと空のフォームを引き続き使用することもできます。

## <a name="training-data-tips"></a>トレーニング データのヒント

トレーニング用に最適化されたデータ セットを使用することが重要です。 次のヒントを使用して、[カスタムモデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) 操作で最良の結果が得られるようにしてください：

* 可能であれば、画像ベースのドキュメントではなく、テキストベースの PDF ドキュメントを使用します。 スキャンした PDF は画像として処理されます。
* 入力フォームの場合は、すべてのフィールドに入力されている例を使用します。
* 各フィールドに異なる値が含まれたフォームを使用します。
* フォームの画像の品質が低い場合は、より大きなデータ セット (たとえば 10 から 15 の画像) を使用します。
* トレーニングデータセットの合計サイズは、最大500ページまでにすることができます。

## <a name="general-input-requirements"></a>一般的な入力の要件

トレーニング データ セットが、すべての Form Recognizer コンテンツの入力の要件にも従っていることを確認します。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>トレーニング データをアップロードする

トレーニングに使用するフォーム ドキュメントのセットをまとめたら、それを Azure Blob Storage コンテナーにアップロードする必要があります。 コンテナーを含む Azure Storage アカウントを作成する方法がわからない場合は、[Azure portal の Azure Storage に関するクイックスタート](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)に従ってください。 Standard パフォーマンス レベルを使用します。

手動でラベル付けされたデータを使用する場合は、トレーニング ドキュメントに対応する *.labels.json* ファイルと *.ocr.json* ファイルもアップロードする必要があります。 [サンプル ラベル付けツール](./quickstarts/label-tool.md) (または独自の UI) を使用して、これらのファイルを生成できます。

### <a name="organize-your-data-in-subfolders-optional"></a>データをサブフォルダーに整理する (オプション)

既定では、[カスタムモデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) API は、ストレージ コンテナーのルートにあるフォーム ドキュメントのみが使用されます。 ただし、API 呼び出しで指定した場合は、サブフォルダー内のデータを使用してトレーニングすることができます。 通常、[カスタム モデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)呼び出しの本文は次の形式になります。`<SAS URL>` は、コンテナーの Shared Access Signature URL です。

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
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>次のステップ

トレーニング データ セットの作成方法を習得したので、クイックスタートに従って、カスタム Form Recognizer モデルをトレーニングし、お使いのフォームでの使用を開始してください。

* [cURL を使用してモデルをトレーニングし、フォーム データを抽出する](./quickstarts/curl-train-extract.md)
* [REST API と Python を使用してモデルをトレーニングし、フォーム データを抽出する](./quickstarts/python-train-extract.md)
* [サンプル ラベル付けツールを使用したラベルによるトレーニング](./quickstarts/label-tool.md)
* [REST API と Python を使用したラベルのトレーニング](./quickstarts/python-labeled-data.md)
