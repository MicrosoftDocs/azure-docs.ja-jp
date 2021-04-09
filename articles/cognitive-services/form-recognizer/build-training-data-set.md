---
title: カスタム モデルのトレーニング データ セットを作成する方法 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: お使いのトレーニング データ セットが Form Recognizer モデルのトレーニングに最適化されていることを確認する方法を学習します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: lajanuar
ms.openlocfilehash: b33ac3cb710a2d2a9d92efadf14dc829cb5da6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467495"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>カスタム モデルのトレーニング データ セットを作成する

Form Recognizer のカスタム モデルを使用する場合は、モデルを業界固有のフォームに合わせてトレーニングできるように、独自のトレーニング データを [Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) 操作に提供します。 このガイドに従い、モデルを効果的にトレーニングするためにデータを収集し、準備する方法について学習してください。

同じ種類の少なくとも 5 つの入力フォームが必要です。

手動でラベル付けされたトレーニング データを使用する場合は、同じ種類の少なくとも 5 つの入力済みフォームから開始する必要があります。 必要なデータ セットに加え、ラベル付けされていないフォームを引き続き使用することもできます。

## <a name="custom-model-input-requirements"></a>カスタム モデルの入力要件

まず、トレーニング データ セットが Form Recognizer の入力の要件に従っていることを確認します。

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>トレーニング データのヒント

以下の追加ヒントに従い、トレーニングのためにデータ セットをさらに最適化してください。

* 可能であれば、画像ベースのドキュメントではなく、テキストベースの PDF ドキュメントを使用します。 スキャンした PDF は画像として処理されます。
* 入力フォームの場合は、すべてのフィールドに入力されている例を使用します。
* 各フィールドに異なる値が含まれたフォームを使用します。
* フォームの画像の品質が低い場合は、より大きなデータ セット (たとえば 10 から 15 の画像) を使用します。

## <a name="upload-your-training-data"></a>トレーニング データをアップロードする

トレーニングに使用するフォーム ドキュメントのセットをまとめたら、それを Azure Blob Storage コンテナーにアップロードする必要があります。 コンテナーを含む Azure Storage アカウントを作成する方法がわからない場合は、[Azure portal の Azure Storage に関するクイックスタート](../../storage/blobs/storage-quickstart-blobs-portal.md)に従ってください。 Standard パフォーマンス レベルを使用します。

手動でラベル付けされたデータを使用する場合は、トレーニング ドキュメントに対応する *.labels.json* ファイルと *.ocr.json* ファイルもアップロードする必要があります。 [サンプル ラベル付けツール](./quickstarts/label-tool.md) (または独自の UI) を使用して、これらのファイルを生成できます。

### <a name="organize-your-data-in-subfolders-optional"></a>データをサブフォルダーに整理する (オプション)

既定では、[カスタムモデルのトレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) API は、ストレージ コンテナーのルートにあるフォーム ドキュメントのみが使用されます。 ただし、API 呼び出しで指定した場合は、サブフォルダー内のデータを使用してトレーニングすることができます。 通常、[カスタム モデルのトレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)呼び出しの本文は次の形式になります。`<SAS URL>` は、コンテナーの Shared Access Signature URL です。

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

* [クライアント ライブラリまたは REST API を使用し、モデルをトレーニングし、フォーム データを抽出する](./quickstarts/client-library.md)
* [サンプル ラベル付けツールを使用したラベルによるトレーニング](./quickstarts/label-tool.md)

## <a name="see-also"></a>関連項目

* [Form Recognizer とは](./overview.md)