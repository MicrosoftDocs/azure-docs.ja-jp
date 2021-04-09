---
title: Computer Vision の新機能
titleSuffix: Azure Cognitive Services
description: この記事には、Computer Vision に関するニュースが含まれています。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: 78746e7623f58af5ae9df829b48245295dc39f01
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487102"
---
# <a name="whats-new-in-computer-vision"></a>Computer Vision の新機能

サービス内の新機能について説明します。 以下の項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="march-2021"></a>2021 年 3 月

### <a name="computer-vision-32-public-preview-update"></a>Computer Vision 3.2 パブリック プレビューの更新

Computer Vision API v3.2 パブリック プレビューが更新されました。 プレビュー リリースには、すべての Computer Vision 機能に加えて、更新された Read および Analyze API が含まれています。

> [!div class="nextstepaction"]
> [Computer Vision v3.2 パブリック プレビュー 3 を参照する](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>2021 年 2 月

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Read API v3.2 のパブリック プレビューが 73 言語の OCR に対応
クラウド サービスおよび Docker コンテナーとして利用できるようになった Computer Vision の Read API v3.2 パブリック プレビューでは、次の更新が行われています。
* [OCR が 73 言語に対応](./language-support.md#optical-character-recognition-ocr)。簡易字中国語と繁体字中国語、日本語、韓国語、ラテン語系の言語が含まれます。
* テキスト行出力に自然な読みの順序を使用 (ラテン語系の言語のみ)
* テキスト行に対する手書きスタイルの分類と信頼度スコア (ラテン語系の言語のみ)。
* 複数ページから成るドキュメントで選択ページのみのテキストを抽出。
* オンプレミス デプロイ用の[ディストリビューションレス コンテナー](./computer-vision-how-to-install-containers.md?tabs=version-3-2)として利用可能。

Read API については、[こちら](concept-recognizing-text.md)を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.2 パブリック プレビューを使用する](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>2021 年 1 月

### <a name="spatial-analysis-container-update"></a>空間分析コンテナーの更新

新しい機能セットが含まれる[空間分析コンテナー](spatial-analysis-container.md)の新しいバージョンがリリースされています。 この Docker コンテナーを使用すると、リアルタイム ストリーミング ビデオを分析して、物理環境における人物とその動きとの間の空間的な関係を把握できます。 

* 人物がマスクなどの顔を保護するものを装着しているかどうかを検出するように[空間分析操作](spatial-analysis-operations.md)を構成できるようになりました。 
    * マスク分類子は、`ENABLE_FACE_MASK_CLASSIFIER` パラメーターを構成することで、`personcount`、`personcrossingline`、および `personcrossingpolygon` 操作に対して有効にすることができます。
    * ビデオ ストリームで検出された人物それぞれの信頼度スコアと共に、`face_mask` および `face_noMask` 属性がメタデータとして返されます
* *personcrossingpolygon* 操作が拡張され、人がゾーン内で費やした滞在時間を計算できるようになりました。 この操作のゾーンの構成で `type` パラメーターを `zonedwelltime` に設定できます。これにより、*personZoneDwellTimeEvent* 型の新しいイベントに、人がゾーン内で費やした時間 (ミリ秒単位) が設定された `durationMs` フィールドが含まれるようになります。
* **破壊的変更**:*personZoneEvent* イベントの名前が *personZoneEnterExitEvent* に変更されました。 人がゾーンに出入りするときに *personcrossingpolygon* イベントが発生し、交差したゾーンの番号付き側に方向情報を提供します。
* ビデオの URL を、すべての操作で "Private Parameter/obfuscated" として指定できます。 難読化はオプションになり、`KEY` および `IV` が環境変数として指定されている場合にのみ機能します。
* 既定では、すべての操作で較正が有効になっています。 無効にするには、`do_calibration: false` を設定します。
* `enable_recalibration` パラメーターによる自動再較正 (既定では無効) のサポートを追加しました。詳細については、「[空間分析操作](./spatial-analysis-operations.md)」参照してください
* カメラ較正のパラメーターを `DETECTOR_NODE_CONFIG` にします。 詳細については、「[空間分析操作](./spatial-analysis-operations.md)」を参照してください。


## <a name="october-2020"></a>2020 年 10 月

### <a name="computer-vision-api-v31-ga"></a>Computer Vision API v3.1 GA

一般提供中の Computer Vision API は、v3.1 にアップグレードされました。

## <a name="september-2020"></a>2020 年 9 月

### <a name="spatial-analysis-container-preview"></a>空間分析コンテナー プレビュー

[空間分析コンテナー](spatial-analysis-container.md)は現在プレビュー段階です。 Computer Vision の空間分析機能を使用すると、リアルタイム ストリーミング ビデオを分析して、物理環境における人物とその動きとの間の空間的な関係を把握できます。 空間分析は、オンプレミスで使用できる Docker コンテナーです。 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v3.1 パブリック プレビューが新たに日本語の OCR に対応
Computer Vision の Read API v3.1 パブリック プレビューで次の機能が追加されました。
* 日本語の OCR
* それぞれのテキスト行について、その体裁が手書きであるか印刷スタイルであるかが信頼度スコア (ラテン語系の言語のみ) と共に示されます。
* 複数ページから成るドキュメントから、選択されたページまたはページ範囲のテキストのみを抽出します。

* この Read API のプレビュー バージョンでは、英語、オランダ語、フランス語、ドイツ語、イタリア語、日本語、ポルトガル語、簡体字中国語、およびスペイン語がサポートされています。

詳細については、[Read API の概要](concept-recognizing-text.md)に関する記事を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.1 パブリック プレビュー 2 の詳細を確認する](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020 年 7 月

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Read API v3.1 のパブリック プレビューが簡体字中国語の OCR に対応
Computer Vision の Read API v3.1 パブリック プレビューでは、簡体字中国語のサポートが追加されています。

* この Read API のプレビュー バージョンでは、英語、オランダ語、フランス語、ドイツ語、イタリア語、ポルトガル語、簡体字中国語、およびスペイン語がサポートされています。

詳細については、[Read API の概要](concept-recognizing-text.md)に関する記事を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.1 パブリック プレビュー 1 の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020 年 5 月
Computer Vision API v3.0 が一般提供になり、[Read API](concept-recognizing-text.md) が更新されています。

* 英語、オランダ語、フランス語、ドイツ語、イタリア語、ポルトガル語、およびスペイン語のサポート
* 精度の向上
* 抽出された各単語の信頼度スコア
* 新しい出力形式

## <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

## <a name="january-2020"></a>2020 年 1 月

### <a name="read-api-30-public-preview"></a>Read API 3.0 パブリック プレビュー

Read API のバージョン3.0 では、印刷されたテキストまたは手書きテキストを画像から抽出するオプションが追加されました。 以前のバージョンと比較して、3.0 では以下のものが提供されます。
* 精度の向上
* 新しい出力形式
* 抽出された各単語の信頼度スコア
* 言語パラメーターの追加によるスペイン語および英語のサポート

3\.0 API の使用を開始するには、[テキストの抽出に関するクイックスタート](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3)に従ってください。

## <a name="cognitive-service-updates"></a>Cognitive Services の更新プログラム

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)