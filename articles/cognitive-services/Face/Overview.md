---
title: Face API とは
titleSuffix: Azure Cognitive Services
description: Face サービスを使用して、画像内の顔を検出し、分析する方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: dcfb50c58f1205a5ab31c3fc6b9b22fdb503e4ec
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588960"
---
# <a name="what-is-the-azure-face-api"></a>Azure Face API とは

Azure Face API は、画像に含まれている人の顔を検出、認識、分析するためのアルゴリズムを備えたコグニティブ サービスです。 人の顔の情報を処理する機能は、セキュリティ、自然なユーザー インターフェイス、画像コンテンツの分析と管理、モバイル アプリ、ロボティクスなど、多種多様なソフトウェア シナリオで重要です。

Face API にはいくつかの異なる機能があり、そのそれぞれについて以降のセクションで説明します。 それぞれの詳細については、後の説明を参照してください。

## <a name="face-detection"></a>顔検出

Face API では、画像に含まれている人の顔を検出し、その位置の四角形の座標を返すことができます。 顔検出では、オプションとして、ポーズ、頭部姿勢、性別、年齢、ひげ、眼鏡などの顔関連の属性を抽出できます。

> [!NOTE] 
> 顔検出機能は [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) を通じて利用することもできますが、さらなる操作を顔データに対して実行したい場合は、Face API (本サービス) を使用することをお勧めします。

![女性と男性の画像 (それぞれの顔の周囲に四角形が描かれ、年齢と性別が表示されている)](./Images/Face.detection.jpg)

顔検出の詳細については、[顔検出のハウツー ガイド](face-api-how-to-topics/howtodetectfacesinimage.md)または [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) リファレンス ドキュメントを参照してください。

## <a name="face-verification"></a>顔検証

Verify API は、検出された 2 つの顔に対する認証、または検出された 1 つの顔から 1 つの人物オブジェクトに対する認証を実行します。 実質的には、2 つの顔が同一人物のものであるかどうかを評価します。 これは、セキュリティのシナリオで役立つ可能性があります。 詳細については、[Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) に関するページを参照してください。

## <a name="find-similar-faces"></a>似た顔の検索

Find Similar API では、ターゲットの顔と候補となる一連の顔が受け取られ、ターゲットの顔によく似ている一連の顔が検索されます。 **matchPerson** と **matchFace** の 2 つの動作モードがサポートされています。 **matchPerson** モードでは、([Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) を使用して) 同一人物についてフィルター処理が行われた後、似た顔が返されます。 **matchFace** モードでは、同一人物フィルターは無視され、同一人物のものであるかどうかに関係なく、似ている顔の候補のリストが返されます。

以下の例では、ターゲットの顔は次のとおりです。

![笑顔の女性](./Images/FaceFindSimilar.QueryFace.jpg)

そして候補となる顔は次のとおりです。

![笑っている人々の 5 つの画像。 画像 a) と画像 b) は同一人物](./Images/FaceFindSimilar.Candidates.jpg)

4 つの似た顔を検索する場合、**matchPerson** モードではターゲットの顔と同じ人を表す (a) と (b) が返されます。 **matchFace** モードでは、ターゲットと同一人物でなくても、また類似性が低くても厳密に 4 つの候補が返されるので、(a)、(b)、(c)、(d) が返されます。 詳細については、[Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) に関するページを参照してください。

## <a name="face-grouping"></a>顔のグループ化

Group API では、未知の顔の集合が、類似性に基づいて複数のグループに分けられます。 それぞれのグループは、元の顔の集合から得られる、互いに素な真部分集合です。 グループに含まれるすべての顔は同一人物のものである可能性が高いものの、1 人の人物について、いくつかの異なるグループが存在する可能性があります (表情など、別の要因によって区別される)。 詳細については、[Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) に関するページを参照してください。

## <a name="person-identification"></a>人物の特定

Identify API を使用すると、人のデータベースと照らして、検出された顔を識別することができます。 これは、写真管理ソフトウェアにおける画像の自動タグ付けに役立つ可能性があります。 データベースは事前に作成しておき、後で編集できます。

次の画像は、"myfriends" という名前のデータベースの例を示しています。 各グループは、最大で 1,000,000 個の異なる人物オブジェクトを含むことができ、各人物オブジェクトには最大で 248 個の顔を登録できます。

![それぞれ異なる人物に対応する 3 つの列とそれぞれ 3 行の顔の画像から成るグリッド](./Images/person.group.clare.jpg)

データベースを作成してトレーニングした後、新しく検出された顔を含んだグループに対して識別を実行できます。 顔がグループ内の person として識別された場合、その person オブジェクトが返されます。

人の識別について詳しくは、[Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) に関するページを参照してください。

## <a name="use-containers"></a>コンテナーの使用

標準化された Docker コンテナーをデータの近くにインストールすることによって、[Face コンテナーを使用](face-how-to-install-containers.md)して顔を検出、認識、識別します。

## <a name="sample-apps"></a>サンプル アプリ

以下のサンプル アプリケーションでは、Face API の使い道をいくつか紹介しています。

- [Microsoft Face API: Windows クライアント ライブラリとサンプル](https://github.com/Microsoft/Cognitive-Face-Windows) - 顔の検出、分析、識別についていくつかのシナリオを実証する WPF アプリ。
- [FamilyNotes UWP アプリ](https://github.com/Microsoft/Windows-appsample-familynotes) - 家族でノートを共有するシナリオにおいて、音声、Cortana、インク、カメラと共に顔の識別が使用されるユニバーサル Windows プラットフォーム (UWP) アプリです。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Face サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

クイック スタートに従って、簡単な顔検出のシナリオをコードで実装します。
- [クイック スタート:.NET SDK と C# を使って画像の中にある顔を検出する](quickstarts/csharp.md) (その他の言語版もあり)
