---
title: "Azure Data Lake Analytics で U-SQL Cognitive 機能の使用 |Microsoft Docs"
description: "U-SQL の Cognitive 機能のインテリジェンスを使用する方法"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
translationtype: Human Translation
ms.sourcegitcommit: 21b4d574705d589406f50cac106a47ada71d24cd
ms.openlocfilehash: 596459e25f8ad072a55ad45a2f444c71b27fd60c


---

# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>チュートリアル: U-SQL の Cognitive 機能を使い始める

U-SQL の Cognitive 機能により、開発者はビッグ データ プログラムにインテリジェンスを配置できます。 全体的なプロセスは簡単です。

* REFERENCE ASSEMBLY ステートメントを使用して U-SQL スクリプトの認識機能を有効にします
* PROCESS 操作を使用して Cognitive 機能を使用します 

## <a name="imaging-scenarios"></a>シナリオのイメージング

### <a name="a-simple-example-image-tagging"></a>簡単な例: イメージのタグ付け

次の例では、イメージ内のオブジェクトの検出を目的とした、エンド ツー エンドでのイメージング機能の使用が示されます。

    REFERENCE ASSEMBLY ImageCommon;
    REFERENCE ASSEMBLY FaceSdk;
    REFERENCE ASSEMBLY ImageEmotion;
    REFERENCE ASSEMBLY ImageTagging;
    REFERENCE ASSEMBLY ImageOcr;

    @imgs =
        EXTRACT FileName string, ImgData byte[]
        FROM @"/images/{FileName:*}.jpg"
        USING new Cognition.Vision.ImageExtractor();

    // Extract the number of objects on each image and tag them 
    @objects =
        PROCESS @imgs 
        PRODUCE FileName,
                NumObjects int,
                Tags string
        READONLY FileName
        USING new Cognition.Vision.ImageTagger();


### <a name="extract-emotions-from-human-faces"></a>人の顔から感情を抽出 

    @emotions =
        PROCESS @imgs
        PRODUCE FileName string,
                NumFaces int,
                Emotion string
        READONLY FileName
        USING new Cognition.Vision.EmotionAnalyzer();

### <a name="estimate-age-and-gender-for-human-faces"></a>人の顔の年齢や性別を推定

    @faces = 
            PROCESS @imgs
            PRODUCE FileName,
                    NumFaces int,
                    FaceAge string,
                    FaceGender string
            READONLY FileName
            USING new Cognition.Vision.FaceDetector();

### <a name="detect-text-in-images-ocr"></a>イメージ内のテキストを検出 (OCR)

    @ocrs =
            PROCESS @imgs
            PRODUCE FileName,
                    Text string
            READONLY FileName
            USING new Cognition.Vision.OcrExtractor();

## <a name="text-scenarios"></a>テキスト シナリオ

### <a name="input-data"></a>入力データ

Leo Tolstoy の「戦争と平和」で構成される入力があると仮定します。

    REFERENCE ASSEMBLY [TextCommon];
    REFERENCE ASSEMBLY [TextSentiment];
    REFERENCE ASSEMBLY [TextKeyPhrase];

    @WarAndPeace =
        EXTRACT No int,
                Year string,
                Book string,
                Chapter string,
                Text string
        FROM @"/usqlext/samples/cognition/war_and_peace.csv"
        USING Extractors.Csv();

### <a name="extract-key-phrases-for-each-paragraph"></a>各段落のキー フレーズを抽出します。

    @keyphrase =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                KeyPhrase string
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.KeyPhraseExtractor();

    // Tokenize the key phrases.
    @kpsplits =
        SELECT No,
            Year,
            Book,
            Chapter,
            Text,
            T.KeyPhrase
        FROM @keyphrase
            CROSS APPLY
                new Cognition.Text.Splitter("KeyPhrase") AS T(KeyPhrase);
    
### <a name="perform-sentiment-analysis-on-each-paragraph"></a>各段落の感情分析を実行

    @sentiment =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                Sentiment string,
                Conf double
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.SentimentAnalyzer(true);




<!--HONumber=Dec16_HO3-->


