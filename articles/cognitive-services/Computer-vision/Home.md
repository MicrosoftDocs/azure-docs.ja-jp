---
title: Computer Vision とは - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision サービスを使用すると、開発者は、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 17dd428f79f66bc70da47527314ad43a27955c82
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772511"
---
# <a name="what-is-computer-vision"></a>Computer Vision とは

Azure の Computer Vision サービスを使用すると、開発者は、関心のあるビジュアル機能に応じて、画像を処理して情報を返す高度なアルゴリズムにアクセスできます。 たとえば、Computer Vision では、成人向けコンテンツが画像に含まれているかどうかを判断したり、画像内の人物の顔をすべて検出したりすることができます。

Computer Vision は、ネイティブ SDK を通じてアプリケーション内で、または REST API を直接呼び出すことで使用できます。 このページでは、Computer Vision でできることを大まかに取り上げます。

## <a name="computer-vision-for-digital-asset-management"></a>デジタル資産管理用の Computer Vision

Computer Vision は、多くのデジタル資産管理 (DAM) シナリオに活用できます。 DAM は、リッチ メディア アセットの整理、保管、取得、およびデジタルの権利とアクセス許可の管理を行うビジネス プロセスです。 たとえば、会社では、表示されるロゴ、顔、オブジェクト、色などに基づいて、画像をグループ化し、識別することができます。 または、自動的に[画像のキャプションを生成](./Tutorials/storage-lab-tutorial.md)し、キーワードを添付して検索できるようにすることもできます。 Cognitive Services、Azure Cognitive Search、およびインテリジェント レポートを使用するオールインワンの DAM ソリューションについては、GitHub 上の「[ナレッジ マイニング ソリューション アクセラレータ ガイド](https://github.com/Azure-Samples/azure-search-knowledge-mining)」を参照してください。 その他の DAM の例については、[Computer Vision ソリューション テンプレート](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates)のリポジトリを参照してください。

## <a name="analyze-images-for-insight"></a>画像を分析して分析情報を得る

画像を分析し、その視覚的特徴や性質に関する分析情報を検出して提示できます。 次の表に示したすべての機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API に備わっています。

| アクション | 説明 |
| ------ | ----------- |
|**[視覚的特徴のタグ付け](concept-tagging-images.md)**|数千個の認識可能なオブジェクト、生物、風景、および動作のセットから、画像内の視覚的な特徴を識別してタグ付けします。 タグが不明確な場合や、常識的でない場合は、API 応答により、タグのコンテキストを明確にするためのヒントが示されます。 タグ付けの対象は、前景の人物などの被写体に限らず、背景 (屋内または屋外)、家具、道具、植物、動物、アクセサリ、ガジェットなども含まれます。|
|**[オブジェクトの検出](concept-object-detection.md)**| オブジェクトの検出はタグ付けに似ていますが、API で返されるのは、各タグが適用された境界ボックスの座標です。 たとえば犬や猫、人物が画像に含まれている場合、検出操作によって、それらのオブジェクトが、画像における対応する座標と共に一覧表示されます。 この機能を使用して、画像内のオブジェクト間のリレーションシップをさらに処理できます。 画像内に同じタグの複数のインスタンスが存在する場合はそれも知ることができます。|
|**[ブランドの検出](concept-brand-detection.md)**|数千点ものグローバル ロゴのデータベースから、画像または動画に含まれる商業ブランドを識別します。 この機能は、たとえば、ソーシャル メディアで最も人気のあるブランドや、メディアのプロダクト プレイスメントの中で最も普及しているブランドを検出する目的で使用できます。|
|**[イメージの分類](concept-categorizing-images.md)**|親/子で引き継がれる階層を備えた[カテゴリの分類](Category-Taxonomy.md)を使用して、イメージ全体を識別してタグ付けします。 カテゴリは単独で、または新しいタグ付けモデルと共に使用できます。<br/>現時点では、イメージのタグ付けと分類でサポートされている言語は、英語のみです。|
|**[イメージの説明](concept-describing-images.md)**|完全な文を使用して、人間が判読できる言語でイメージ全体の説明を生成します。 Computer Vision のアルゴリズムでは、イメージ内の識別されるオブジェクトに基づいて、さまざまな説明が生成されます。 説明はそれぞれ評価され、信頼度スコアが生成されます。 その後、信頼度スコアが最も高いものから最も低いものの順に並べられたリストが返されます。|
|**[顔の検出](concept-detecting-faces.md)** |イメージ内の人物の顔を検出して、検出されたそれぞれの顔に関する情報を提示します。 Computer Vision は検出された各顔の座標、四角い枠、性別、および年齢を返します。<br/>Computer Vision では、[Face](/azure/cognitive-services/face/) サービス機能のサブセットが提供されます。 Face サービスは、顔識別や姿勢検出など、より詳細な分析に使用できます。|
|**[イメージの種類の検出](concept-detecting-image-types.md)**|イメージが線による描画かクリップ アートのようになっているかなど、イメージの性質を検出します。|
|**[ドメイン固有のコンテンツの検出](concept-detecting-domain-content.md)**|ドメイン モデルを使用して、有名人やランドマークなど、イメージ内のドメイン固有のコンテンツを検出して識別します。 たとえば、画像に人物が含まれている場合、Computer Vision では、有名人用のドメイン モデルを使用して、画像内で検出された人物が既知の有名人と一致するかどうかを判断できます。|
|**[配色の検出](concept-detecting-color-schemes.md)**|イメージ内にある色の使用状況を分析します。 Computer Vision では、イメージが白黒かカラーかを特定すると共に、カラーのイメージの場合、主要な色やアクセントになる色を識別できます。|
|**[サムネイルの生成](concept-generating-thumbnails.md)**|イメージのコンテンツを分析して、そのイメージの適切なサムネイルを生成します。 Computer Vision では、最初に高品質のサムネイルを生成した後、画像内のオブジェクトを分析して "*関心領域*" を特定します。 Computer Vision では、その後、関心領域の要件に合わせて、画像がトリミングされます。 ユーザーのニーズに応じて、元のイメージの縦横比とは異なる縦横比を使用して、生成されたサムネイルを表示することができます。|
|**[関心領域を取得する](concept-generating-thumbnails.md#area-of-interest)**|画像の内容が分析され、"*関心領域*" の座標が返されます。 画像をクロップしたり、サムネイルを生成したりする代わりに、Computer Vision では領域の境界ボックスの座標が返されるため、呼び出し元のアプリケーションで必要に応じて元の画像を変更できます。|

## <a name="extract-text-from-images"></a>画像からテキストを抽出する

Computer Vision の [Read](concept-recognizing-text.md#read-api) API を使用して、画像から印刷されたテキストと手書きのテキストをマシンで読み取り可能な文字ストリームに抽出できます。 読み取り API では、最新モデルを使用し、レシート、ポスター、名刺、手紙、ホワイトボードなど、さまざまなサーフェスや背景でテキストを使用します。 現在、サポートされている言語は英語だけです。

また、[光学文字認識 (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) API を使用して、複数の言語で印刷されたテキストを抽出することもできます。 必要に応じて、OCR では、認識されたテキストの回転を修正し、各語句のフレーム座標を提供します。 OCR は 25 の言語をサポートし、認識されたテキストの言語を自動的に検出します。

## <a name="moderate-content-in-images"></a>画像内のコンテンツを調整する

Computer Vision を使用すると、画像内の[成人向けコンテンツを検出](concept-detecting-adult-content.md)し、さまざまな分類の信頼度スコアを返すことができます。 コンテンツをフラグ設定するためのしきい値は、自分の都合に合わせて、スライディング スケールで設定することができます。

## <a name="use-containers"></a>コンテナーの使用

印刷されたテキストと手書きのテキストをローカルに認識するには、標準化された Docker コンテナーをデータの近くにインストールして、[Computer Vision コンテナーを使用](computer-vision-how-to-install-containers.md)します。

## <a name="image-requirements"></a>イメージの要件

Computer Vision では、次の要件に合ったイメージを分析できます。

- イメージが、JPEG、PNG、GIF、または BMP で提示されている
- イメージのファイル サイズが 4 メガバイト (MB) 未満である
- イメージのディメンションが 50 x 50 ピクセルよりも大きい値である
  - Read API の場合、画像の寸法は、50 x 50 から 10,000 x 10,000 ピクセルの間である必要があります。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Computer Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

以下のクイック スタート ガイドに従って、実際に Computer Vision を使ってみましょう。

- [クイック スタート: Computer Vision .NET SDK](quickstarts-sdk/csharp-sdk.md)
- [クイック スタート: Computer Vision Python SDK](quickstarts-sdk/python-sdk.md)
- [クイック スタート: Computer Vision Java SDK](quickstarts-sdk/java-sdk.md)
