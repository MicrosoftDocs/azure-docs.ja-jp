---
title: Computer Vision API とは - Computer Vision
titlesuffix: Azure Cognitive Services
description: Computer Vision サービスを使用すると、開発者は、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f92d8ce8e95962558b3bdb7b4a4d8fe70c725f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604494"
---
# <a name="what-is-computer-vision"></a>Computer Vision とは

Azure の Computer Vision サービスを使用すると、開発者は、画像を処理して情報を返す高度なアルゴリズムにアクセスできます。 イメージを分析するには、イメージをアップロードするか、またはイメージの URL を指定します。 この画像処理アルゴリズムでは、目的の視覚的特徴に応じて、さまざまな方法でコンテンツを分析できます。 たとえば、Computer Vision では、成人向けまたはきわどいコンテンツが画像に含まれているかどうかを判断したり、画像内の人の顔をすべて検索したりすることができます。

Computer Vision は、アプリケーションからネイティブ SDK を使用するか、REST API を直接呼び出すことで使用できます。 このページでは、Computer Vision でできることを大まかに取り上げます。

## <a name="analyze-images-for-insight"></a>画像を分析して分析情報を得る

画像を分析し、その視覚的特徴や性質に関する分析情報を検出して提示できます。 次の表に示したすべての機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API に備わっています。

| Action | 説明 |
| ------ | ----------- |
|**[視覚的特徴のタグ付け](concept-tagging-images.md)**|数千個の認識可能なオブジェクト、生物、風景、および動作のセットから、画像内の視覚的な特徴を識別してタグ付けします。 タグが不明確な場合や、常識的でない場合は、API 応答により、タグのコンテキストを明確にするためのヒントが示されます。 タグ付けの対象は、前景の人物などの被写体に限らず、背景 (屋内または屋外)、家具、道具、植物、動物、アクセサリ、ガジェットなども含まれます。|
|**[オブジェクトの検出](concept-object-detection.md)**| オブジェクトの検出はタグ付けに似ていますが、API で返されるのは、各タグが適用された境界ボックスの座標です。 たとえば、犬、猫、および人物が画像に含まれている場合、検出操作では、これらのオブジェクトと画像内での各オブジェクトの座標の一覧が表示されます。 この機能を使用して、画像内のオブジェクト間のリレーションシップをさらに処理できます。 画像内に同じタグの複数のインスタンスが存在する場合はそれも知ることができます。|
|**[ブランドの検出](concept-brand-detection.md)**|数千点ものグローバル ロゴのデータベースから、画像または動画に含まれる商業ブランドを識別します。 この機能は、たとえばソーシャル メディアで最も人気のあるブランドや、メディアのプロダクト プレイスメントの中で最も普及しているブランドを検出する目的で使用できます。|
|**[イメージの分類](concept-categorizing-images.md)**|親/子で引き継がれる階層を備えた[カテゴリの分類](Category-Taxonomy.md)を使用して、イメージ全体を識別してタグ付けします。 カテゴリは単独で、または新しいタグ付けモデルと共に使用できます。<br/>現時点では、イメージのタグ付けと分類でサポートされている言語は、英語のみです。|
|**[イメージの説明](concept-describing-images.md)**|完全な文を使用して、人間が判読できる言語でイメージ全体の説明を生成します。 Computer Vision のアルゴリズムでは、イメージ内の識別されるオブジェクトに基づいて、さまざまな説明が生成されます。 説明はそれぞれ評価され、信頼度スコアが生成されます。 その後、信頼度スコアが最も高いものから最も低いものの順に並べられたリストが返されます。|
|**[顔の検出](concept-detecting-faces.md)** |イメージ内の人物の顔を検出して、検出されたそれぞれの顔に関する情報を提示します。 Computer Vision は検出された各顔の座標、四角い枠、性別、および年齢を返します。<br/>Computer Vision では、[Face](/azure/cognitive-services/face/) サービス機能のサブセットが提供されます。 Face サービスは、顔識別や姿勢検出など、より詳細な分析に使用できます。|
|**[イメージの種類の検出](concept-detecting-image-types.md)**|イメージが線による描画かクリップ アートのようになっているかなど、イメージの性質を検出します。|
|**[ドメイン固有のコンテンツの検出](concept-detecting-domain-content.md)**|ドメイン モデルを使用して、有名人やランドマークなど、イメージ内のドメイン固有のコンテンツを検出して識別します。 たとえば、画像に人物が含まれている場合、Computer Vision では、有名人用のドメイン モデルを使用して、画像内で検出された人物が既知の有名人と一致するかどうかを判断できます。|
|**[配色の検出](concept-detecting-color-schemes.md)**|イメージ内にある色の使用状況を分析します。 Computer Vision では、イメージが白黒かカラーかを特定すると共に、カラーのイメージの場合、主要な色やアクセントになる色を識別できます。|
|**[サムネイルの生成](concept-generating-thumbnails.md)**|イメージのコンテンツを分析して、そのイメージの適切なサムネイルを生成します。 Computer Vision では、最初に高品質のサムネイルを生成した後、画像内のオブジェクトを分析して "*関心領域*" を特定します。 Computer Vision では、その後、関心領域の要件に合わせて、画像がトリミングされます。 ユーザーのニーズに応じて、元のイメージの縦横比とは異なる縦横比を使用して、生成されたサムネイルを表示することができます。|
|**[関心領域を取得する](concept-generating-thumbnails.md#area-of-interest)**|画像の内容が分析され、"*関心領域*" の座標が返されます。 画像をクロップしたり、サムネイルを生成したりする代わりに、Computer Vision では領域の境界ボックスの座標が返されるため、呼び出し元のアプリケーションで必要に応じて元の画像を変更できます。|

## <a name="extract-text-from-images"></a>画像からテキストを抽出する

Computer Vision の [Read API](concept-recognizing-text.md#read-api) を使用して、画像から印刷されたテキストと手書きのテキストをコンピューターで読み取り可能な文字ストリームに抽出できます。 読み取り API では、最新モデルを使用し、レシート、ポスター、名刺、手紙、ホワイトボードなど、さまざまなサーフェスや背景でテキストを使用します。 現在、サポートされている言語は英語だけです。

また、[光学文字認識 (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) API を使用して、複数の言語で印刷されたテキストを抽出することもできます。 必要に応じて、OCR では、認識されたテキストの回転を修正し、各語句のフレーム座標を提供します。 OCR は 25 の言語をサポートし、認識されたテキストの言語を自動的に検出します。



## <a name="moderate-content-in-images"></a>画像内のコンテンツを調整する

Computer Vision を使用して、イメージ内の[成人向けやきわどいコンテンツを検出](concept-detecting-adult-content.md)し、その両方の信頼度スコアを返します。 スライディング スケールで成人向けのきわどいコンテンツを検出するためのフィルターを設定し、自分の設定を反映させることができます。

## <a name="use-containers"></a>コンテナーの使用

印刷されたテキストと手書きのテキストをローカルに認識するには、標準化された Docker コンテナーをデータの近くにインストールして、[Computer Vision コンテナーを使用](computer-vision-how-to-install-containers.md)します。

## <a name="image-requirements"></a>イメージの要件

Computer Vision では、次の要件に合ったイメージを分析できます。

- イメージが、JPEG、PNG、GIF、または BMP で提示されている
- イメージのファイル サイズが 4 メガバイト (MB) 未満である
- イメージのディメンションが 50 x 50 ピクセルよりも大きい値である
  - OCR 用に、画像の寸法が 50 x 50 から 4,200 x 4,200 ピクセルの間である

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Computer Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

以下のクイック スタート ガイドに従って、実際に Computer Vision を使ってみましょう。

- [クイック スタート:画像を分析する](quickstarts-sdk/csharp-analyze-sdk.md)
- [クイック スタート:手書きのテキストを抽出する](quickstarts-sdk/csharp-hand-text-sdk.md)
- [クイック スタート:サムネイルを生成する](quickstarts-sdk/csharp-thumb-sdk.md)
