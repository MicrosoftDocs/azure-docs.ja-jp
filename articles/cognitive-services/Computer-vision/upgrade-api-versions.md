---
title: Computer Vision API の v3.0 へのアップグレード
titleSuffix: Azure Cognitive Services
description: Computer Vision Read API を v2.0 または v2.1 から v3.0 にアップグレードする方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214190"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>Computer Vision Read API を v2.0 または v2.1 から v3.0 にアップグレードする

このガイドでは、既存の Computer Vision v2.0 または v2.1 REST API コードを v3.0 Read 操作にアップグレードする方法について説明します。 

## <a name="upgrade-batch-read-file-to-read"></a>`Batch Read File` を `Read` にアップグレードする


1. `Batch Read File` 2.x の API パスを次のように変更します。


    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    新しい省略可能な _language_ パラメーターを使用できます。 ドキュメントの言語がわからない場合 (多言語である可能性がある場合) は、これを含めないでください。 

2. 2\.x の `Get Read Results` の API パスを次のように変更します。

    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0**/read/**operations**/{operationId}     |https://{endpoint}/vision/**v3.0**/read/**analyzeResults**/{operationId}|

3. `Get Read Operation Result` の JSON の結果を確認するためのコードを変更します。 `Get Read Operation Result` の呼び出しが成功すると、JSON 本文の状態文字列フィールドが返されます。 他の Cognitive Service REST API との整合性を高めるために、v2.0 の次の値が変更されました。 
 
    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. `Get Read Operation Result` の最終的な認識結果の JSON を解釈するようにコードを変更します。 

    JSON の次の変更点に注意してください。
    
    - v2.x では、状態が `"Succeeded"` のときに `"Get Read Operation Result"` によって OCR 認識 JSON が返されます。 v3.0 では、このフィールドは `"succeeded"` です。
    - ページ配列のルートを取得するには、JSON 階層を `"recognitionResults"` から `"analyzeResult"`/`"readResults"` に変更します。 ページごとの行と単語の JSON 階層は変更されていないため、コードを変更する必要はありません。
    -   ページ角度の `"clockwiseOrientation"` は、`"angle"` に名前が変更されました。範囲は、0 から 360 度から、-180 から 180 度に変更されました。 ほとんどの数学関数ではいずれの範囲も処理できるので、コードに応じて、変更を加える必要がある場合とない場合があります。
    -   v3.0 API では、必要に応じて利用できる次の機能強化も導入されています。- 処理の期間を追跡できるように、`"createdDateTime"` と `"lastUpdatedDateTime"` が追加されました。 詳細については、ドキュメントを参照してください。 
        - `"version"` は、結果の生成に使用された API のバージョンを示します。
        - 単語ごとの `"confidence"` が追加されました。 この値は、値 0.95 が、95% の確率で認識が正しいことを意味するように調整されています。 信頼度スコアを使用すると、人によるレビューに送信するテキストを選択できます。 
    
    
        2\.X の出力形式は次のとおりです。 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    v3.0 では、次のように調整されています。
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```

## <a name="upgrade-from-recognize-text-to-read"></a>`Recognize Text` から `Read` にアップグレードする
`Recognize Text` は "*プレビュー*" 操作であり、*Computer Vision API のすべてのバージョンで非推奨*になっています。 `Recognize Text` から `Read` (v3.0) または `Batch Read File` (v2.0、v2.1) に移行する必要があります。 v3.0 の `Read` には、テキスト認識に適した新しいモデルと追加機能が含まれているため、これが推奨されます。 `Recognize Text` から `Read` にアップグレードするには、次の手順を実行します。

1. `Recognize Text` v2.x の API パスを次のように変更します。


    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    _mode_ パラメーターは、`Read` ではサポートされていません。 手書きのテキストと印刷されたテキストの両方が自動的にサポートされます。
    
    v3.0 では、新しい省略可能な _language_ パラメーターを使用できます。 ドキュメントの言語がわからない場合 (多言語である可能性がある場合) は、これを含めないでください。 

2. `Get Recognize Text Operation Result` v2.x の API パスを次のように変更します。

    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/**v3.0/read/analyzeResults**/{operationId}|

3. `Get Recognize Text Operation Result` の JSON の結果を確認するためのコードを変更します。 `Get Read Operation Result` の呼び出しが成功すると、JSON 本文の状態文字列フィールドが返されます。 
 
    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. `Get Read Operation Result` をサポートするために、`Get Recognize Text Operation Result` の最終的な認識結果の JSON を解釈するようにコードを変更します。

    JSON の次の変更点に注意してください。    

    - v2.x では、状態が `"Succeeded"` のときに `"Get Read Operation Result"` によって OCR 認識 JSON が返されます。 v3.0 では、このフィールドは `"succeeded"` です。
    - ページ配列のルートを取得するには、JSON 階層を `"recognitionResult"` から `"analyzeResult"`/`"readResults"` に変更します。 ページごとの行と単語の JSON 階層は変更されていないため、コードを変更する必要はありません。
    -   v3.0 API では、必要に応じて利用できる次の機能強化も導入されています。 詳細については、API リファレンスを参照してください: - 処理の期間を追跡できるように、`"createdDateTime"` と `"lastUpdatedDateTime"` が追加されました。 詳細については、ドキュメントを参照してください。 
        - `"version"` は、結果の生成に使用された API のバージョンを示します。
        - 単語ごとの `"confidence"` が追加されました。 この値は、値 0.95 が、95% の確率で認識が正しいことを意味するように調整されています。 信頼度スコアを使用すると、人によるレビューに送信するテキストを選択できます。 
        - `"angle"` は、-180 から 180 度の角度で測定された、テキストの時計回りの一般的な向きです。
        -  `"width"` と `"height"` により、ドキュメントのサイズが示されます。また、`"unit"` では、それらのサイズの単位 (ドキュメントの種類に応じてピクセルまたはインチ) が示されます。
        - `"page"` では、複数ページのドキュメントがサポートされています。
        - `"language"` は、ドキュメントの入力言語です (省略可能な _language_ パラメーターで指定)。


    2\.X の出力形式は次のとおりです。 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    v3.0 では、次のように調整されています。
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```
    
## <a name="all-other-operations"></a>他のすべての操作

Computer Vision API の v2.X と v3.0 の間に、他の破壊的変更はありません。 API パスを変更して、`v2.0` を `v3.0` に置き換えるだけです。
