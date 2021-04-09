---
title: Face サービスの新機能
titleSuffix: Azure Cognitive Services
description: Face サービスのリリース ノートには、さまざまなバージョンのリリース変更履歴が含まれています。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: pafarley
ms.openlocfilehash: 9293d47d8aade11f4cc3ee55bb32574f125dfafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428288"
---
# <a name="whats-new-in-face-service"></a>Face サービスの新機能

Azure Face サービスは、継続的に更新されます。 この記事で、機能の強化、修正、ドキュメントの更新についての最新情報を入手してください。

## <a name="february-2021"></a>2021 年 2 月

* 新しい Face API 検出モデル: 新しい検出 03 モデルは、現在使用できる最も正確な検出モデルです。 新しいお客様の場合は、このモデルを使用することをお勧めします。 検出 03 では、画像内で見つかった小さい顔のリコールと精度が向上しています (64x64 ピクセル)。 さらに改善された点としては、偽陽性が全体的に削減されたことと、回転した顔の向きの検出が向上したことがあります。 検出 03 と新しい認識 04 を組み合わせることにより、認識の精度も向上します。 詳細については、「[顔検出モデルを指定する](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model)」を参照してください。
* フェイス マスク属性: フェイス マスク属性は、最新の検出 03 モデルで使用できます。また、フェイス マスクによって、意図されたとおりに鼻と口の両方がカバーされるように装着されているかどうかを検出する追加の `"noseAndMouthCovered"` 属性を使用できます。 最新のマスク検出機能を使用するには、ユーザーが API 要求で検出モデルを指定する必要があります。_detectionModel_ パラメーターに `detection_03` を設定してモデルのバージョンを割り当てます。 詳細については、「[顔検出モデルを指定する](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model)」を参照してください。
* 新しい Face API 認識モデル: 新しい認識 04 モデルは、現在使用できる最も正確な認識モデルです。 新しいお客様の場合は、検証と識別にこのモデルを使用することをお勧めします。 これにより、認識 03 の精度が向上し、フェイス マスク (サージカル マスク、N95 マスク、布マスク) を装着した登録されているユーザーの認識が向上します。 最新の検出 03 モデルを使用して、登録されているユーザーがフェイス カバーを装着しているかどうかを検出し、最新の認識 04 モデルを使用してそれが誰であるかを認識する、安全でシームレスなユーザー エクスペリエンスを構築できるようになりました。 詳細については、「[顔認識モデルを指定する](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-recognition-model)」を参照してください。


## <a name="january-2021"></a>2021 年 1 月
* Face API を使用するときの待機時間を軽減する: Face チームは、サービス使用時の待機時間の潜在的な原因と、考えられる軽減戦略について詳しく説明されている、新しい記事を公開しました。 「[Face サービスを使用するときの待機時間を軽減する](./face-api-how-to-topics/how-to-mitigate-latency.md)」を参照してください。

## <a name="december-2020"></a>2020 年 12 月
* Face ID ストレージのお客様の構成: Face サービスにはお客様の画像は格納されませんが、抽出された顔の特徴がサーバーに格納されます。 Face ID は顔の特徴の識別子であり、[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)、[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) で使用されます。 格納されている顔の特徴は、元の検出呼び出しから 24 時間後に有効期限が切れ、削除されます。 これらの Face ID がキャッシュされる時間の長さを、お客様が決定できるようになりました。 最大値は最大 24 時間で変わりませんが、最小値として 60 秒に設定できるようになりました。 Face ID がキャッシュされている新しい時間範囲は、60 秒から 24 時間までの任意の値です。 詳細については、[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API リファレンス (*faceIdTimeToLive* パラメーター) を参照してください。

## <a name="november-2020"></a>2020 年 11 月
* サンプルの顔登録アプリを公開して、高品質の登録を通して意味のある同意を確立し、高精度の顔認識システムを作成するためのベストプラクティスを示しました。 オープンソースのサンプルを[登録アプリのビルド](build-enrollment-app.md)に関するガイドと [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample) に追加しました。開発者はすぐにデプロイまたはカスタマイズすることができます。 

## <a name="august-2020"></a>2020 年 8 月
* 保存データのカスタマー マネージド暗号化: Face サービスでは、クラウドに永続化されるときにデータが自動的に暗号化されます。 Face サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 カスタマー マネージド キー (CMK) と呼ばれるユーザー独自のキーを使用してサブスクリプションを管理する新しいオプションもあります。 詳細については、[カスタマー マネージド キー](./encrypt-data-at-rest.md)に関するページを参照してください。

## <a name="april-2020"></a>2020 年 4 月
* 新しい Face API 認識モデル: 新しい認識 03 モデルは、現在使用できる最も正確なモデルです。 新しいお客様の場合は、このモデルを使用することをお勧めします。 認識 03 を使用すると、類似性比較と人物照合比較の両方の精度が向上します。 詳細については、「[顔認識モデルを指定する](./face-api-how-to-topics/specify-recognition-model.md)」を参照してください。

## <a name="june-2019"></a>2019 年 6 月

* 小さい顔、横顔、隠れた顔、ぼやけた顔の精度を向上させた新しい顔検出モデルを追加しました。 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)、[LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) で、`detectionModel` パラメーターに新しい顔検出モデル名 `detection_02` を指定して使用してください。 詳細については、[検出モデルの指定方法](Face-API-How-to-Topics/specify-detection-model.md)に関するページを参照してください。

## <a name="april-2019"></a>2019 年 4 月

* `age` と `headPose` 属性の全体的な精度の向上。 `headPose` 属性も、`pitch` 値で更新されます。 これらの属性を、[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`パラメーターの `returnFaceAttributes` パラメーターに指定して使用します。 

* [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)、および [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) の速度が向上しました。

## <a name="march-2019"></a>2019 年 3 月

* 正確性が向上した新しい顔認識モデルを追加しました。 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) で、`recognitionModel` パラメーターに新しい顔認識モデル名 `recognition_02` を指定して使用してください。 詳細については、[認識モデルの指定方法](Face-API-How-to-Topics/specify-recognition-model.md)に関するページを参照してください。

## <a name="january-2019"></a>2019 年 1 月

* サブスクリプション間のデータ移行をサポートするためのスナップショット機能が追加されました。[スナップショット](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get)。 詳細については、[顔データを別の Face サブスクリプションに移行する方法](Face-API-How-to-Topics/how-to-migrate-face-data.md)に関するページを参照してください。

## <a name="october-2018"></a>2018 年 10 月

* 「[PersonGroup - Get Training Status (PersonGroup - トレーニングの状態の取得)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)」、「[LargePersonGroup - Get Training Status (LargePersonGroup - トレーニングの状態の取得)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)」、および「[LargeFaceList - Get Training Status (LargeFaceList - トレーニング状態の取得)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)」で、`status`、`createdDateTime`、`lastActionDateTime`、および `lastSuccessfulTrainingDateTime` の説明を改善しました。

## <a name="may-2018"></a>2018 年 5 月

* `gender` 属性が大幅に改善され、`age`、`glasses`、`facialHair`、`hair`、`makeup` 属性も改善されました。 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` パラメーターを通じてそれらを使用します。 

* [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)、[LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) で入力画像ファイルのサイズ上限が 4 MB から 6 MB に増えました。

## <a name="march-2018"></a>2018 年 3 月

* 100 万規模のコンテナー、[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) と [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) を追加しました。 詳細は「[How to use the large-scale feature](Face-API-How-to-Topics/how-to-use-large-scale.md)」 (大規模な機能を使用する方法) にあります。

* [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` パラメーターを [1, 5] から [1, 100] に増やしました。既定値は 10 です。

## <a name="may-2017"></a>2017 年 5 月

* `hair`、`makeup`、`accessory`、`occlusion`、`blur`、`exposure`、`noise` 属性を [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` パラメーターに追加しました。

* PersonGroup と [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) で 1 万人に対応しました。

* [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) がオプション パラメーターの `start` と `top` で改ページに対応しました。

* さまざまな FaceLists と PersonGroup のさまざまな人に対する顔の追加/削除でコンカレンシーに対応しました。

## <a name="march-2017"></a>2017 年 3 月
* [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` パラメーターに `emotion` 属性を追加しました。

* [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) と [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) で `targetFace` として [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) から返された顔を長方形で再検出できない問題を解消しました。

* 厳密に 36 x 36 から 4096 x 4096 ピクセルになるように、検出可能な顔サイズを修正しました。

## <a name="november-2016"></a>2016 年 11 月
* 同一人物確認や同様の照合に [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) または [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) を使用するとき、保存する顔の数を増やすために Face ストレージ Standard サブスクリプションを追加しました。 保存した画像には、顔 1000 個あたり 0.5 ドルの料金がかかります。この料金は日割りで課金されます。 Free レベルのサブスクリプションは引き続き合計 1,000 人に制限されます。

## <a name="october-2016"></a>2016 年 10 月
* [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) と [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) で、targetFace の複数の顔に対するエラー メッセージが 'There are more than one face in the image' から 'There is more than one face in the image' に変更されました。

## <a name="july-2016"></a>2016 年 7 月
* [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) が Face to Person のオブジェクト認証に対応しました。

* `mode` というオプション パラメーターが追加され、[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) で `matchPerson` と `matchFace` という 2 つの作業モードを選択できるようになりました。指定しなければ、`matchPerson` です。

* `confidenceThreshold` というオプション パラメーターを追加しました。[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) で、ある顔がある Person オブジェクトに属するかどうかを示すしきい値をこのパラメーターで設定できます。

* [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) にオプション パラメーターの `start` と `top` を追加しました。一覧表示する開始点と合計 PersonGroups 数を指定できます。

## <a name="v10-changes-from-v0"></a>V1.0 の V0 からの変更点

* サービス ルート エンドポイントが ```https://westus.api.cognitive.microsoft.com/face/v0/``` から ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` に更新されました。 変更は [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) に適用されます。

* 顔の検出可能な最小サイズが 36 x 36 ピクセルに変更されました。 36 x 36 ピクセルより小さな顔は検出されません。

* Face V0 では、PersonGroup データと Person データが非推奨になりました。 それらのデータには Face V1.0 サービスでアクセスできません。

* 2016 年 6 月 30 日をもって Face API の V0 エンドポイントが非推奨になりました。