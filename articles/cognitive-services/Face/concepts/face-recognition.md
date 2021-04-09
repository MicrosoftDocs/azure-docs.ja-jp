---
title: 顔認識の概念
titleSuffix: Azure Cognitive Services
description: この記事では、顔認識操作の確認、類似検索、グループ化、識別の概念と基になるデータ構造について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92518808"
---
# <a name="face-recognition-concepts"></a>顔認識の概念

この記事では、顔認識操作の確認、類似検索、グループ化、識別の概念と基になるデータ構造について説明します。 認識は広く、顔が同様であるか、同じ人に属しているかを判断するために、2 つの異なる顔を比較する作業のことを表します。

## <a name="recognition-related-data-structures"></a>認識に関連するデータ構造

認識操作では、主に次のデータ構造を使用します。 これらのオブジェクトはクラウドに格納され、その ID 文字列によって表すことができます。 ID 文字列は、サブスクリプション内で常に一意です。 名前フィールドは重複している可能性があります。

|Name|説明|
|:--|:--|
|DetectedFace| この 1 つの顔の表現は、[顔検出](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)操作によって取得されます。 その ID は作成後 24 時間で期限が切れます。|
|PersistedFace| DetectedFace オブジェクトがグループ (FaceList または Person) に追加されると、これらは PersistedFace オブジェクトになります。 これらはいつでも[取得](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)することができ、期限が切れることはありません。|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) または [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| このデータ構造は、PersistedFace オブジェクトの類別された一覧です。 FaceList には一意の ID、名前の文字列が含まれ、任意でユーザー データの文字列が含まれます。|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| このデータ構造は、同じ人に属する PersistedFace オブジェクトの一覧です。 これには一意の ID、名前の文字列が含まれ、任意でユーザー データの文字列が含まれます。|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) または [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| このデータ構造は、Person オブジェクトの類別された一覧です。 これには一意の ID、名前の文字列が含まれ、任意でユーザー データの文字列が含まれます。 PersonGroup は、認識操作で使用するには、その前に[トレーニングする](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)必要があります。|

## <a name="recognition-operations"></a>認識操作

このセクションでは、4 つの認識操作で前に示されたデータ構造をどのように使用するかについて説明します。 それぞれの認識操作の広範な説明については、[概要](../Overview.md)に関するページを参照してください。

### <a name="verify"></a>Verify (英語の可能性あり)

[確認](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)操作では、DetectedFace または PersistedFace からの顔 ID と、別の顔 ID または Person オブジェクトのいずれかを取得し、同じ人に属しているかどうかを判断します。 Person オブジェクトを渡す場合、パフォーマンスを向上させるために、任意で Person が属している PersonGroup を渡すことができます。

### <a name="find-similar"></a>類似検索

[Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 操作では、DetectedFace または PersistedFace から顔 ID と、FaceList または他の顔 ID の配列のいずれかを取得します。 FaceList を使用すると、指定した顔と同様の顔のより小さい FaceList が返されます。 顔 ID の配列を使用すると、同様により小さい配列が返されます。

### <a name="group"></a>Group

[グループ化](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)操作では、DetectedFace または PersistedFace から類別された顔 ID の配列を取得し、いくつかのより小さい配列にグループ化された同じ ID が返されます。 各 "groups" 配列には、類似している顔 ID が含まれます。 1 つの "messyGroup" 配列には、類似点が見つからなかった顔 ID が含まれます。

### <a name="identify"></a>識別

[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)操作では、DetectedFace または PersistedFace および PersonGroup から 1 つまたは複数の顔 ID を取得し、それぞれの顔が属している可能性がある Person オブジェクトの一覧を返します。 返される Person オブジェクトは、予測信頼度値を含む Candidate オブジェクトとしてまとめられます。

## <a name="input-data"></a>入力データ

次のヒントを使用して、確実に入力画像から最も正確な認識結果が得られるようにします。

* サポートされている入力画像形式は、JPEG、PNG、GIF (最初のフレーム)、BMP です。
* 画像ファイル サイズは 6 MB 以内であることが必要です。
* Person オブジェクトを作成するときに、さまざまな種類の角度と照明を備えた写真を使用します。
* 次のような技術的な課題のために、一部の顔を認識できない場合があります。
  * 強すぎる照明を含む画像 (強烈な逆光照明など)
  * 一方または両方の目を遮っている障害物
  * 髪質や顔の毛の違い
  * 年齢による顔立ちの変化
  * 極端な表情

## <a name="next-steps"></a>次のステップ

顔認識の概念を理解したので、トレーニング済みの PersonGroup に対して顔を識別するスクリプトを作成します。

* [Face クライアント ライブラリのクイックスタート](../Quickstarts/client-libraries.md)