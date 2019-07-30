---
title: Face API とは
titleSuffix: Azure Cognitive Services
description: Face サービスを使用して、画像内の顔を検出し、分析する方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 4be33f781dec93fd9fe1b1846322672266cd7350
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606911"
---
# <a name="what-is-the-azure-face-api"></a>Azure Face API とは

Azure Cognitive Services Face API は、画像に含まれている人の顔の検出、認識、分析に使用されるアルゴリズムを備えています。 人の顔の情報を処理する機能は、多種多様なソフトウェア シナリオで重要です。 シナリオの例として、セキュリティ、自然なユーザー インターフェイス、画像コンテンツの分析と管理、モバイル アプリ、ロボティクスなどがあります。

Face API にはいくつかの異なる機能があります。 それぞれの機能について以降のセクションで説明します。 これらの詳細については、後の説明を参照してください。

## <a name="face-detection"></a>顔検出

Face API では、画像に含まれている人の顔を検出し、その位置の四角形の座標を返します。 顔検出ではオプションとして、顔関連の属性を抽出できます。 たとえば、頭部姿勢、性別、年齢、感情、顔ひげ、眼鏡です。

> [!NOTE]
> 顔検出機能は [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) でも利用できます。 顔のデータを使ってさらに操作を行いたい場合は、Face API (この記事で説明するサービス) を使用してください。

![女性と男性の画像 (それぞれの顔の周囲に四角形が描かれ、年齢と性別が表示されている)](./Images/Face.detection.jpg)

顔検出の詳細については、[顔検出](concepts/face-detection.md)の概念に関するページを参照してください。 また、[Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) リファレンス ドキュメントも参照してください。

## <a name="face-verification"></a>顔検証

Verify API は、検出された 2 つの顔に対する認証、または検出された 1 つの顔から 1 つの人物オブジェクトに対する認証を実行します。 実質的には、2 つの顔が同一人物のものであるかどうかを評価します。 この機能は、セキュリティのシナリオで役立つ可能性があります。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) リファレンス ドキュメントを参照してください。

## <a name="find-similar-faces"></a>似た顔の検索

Find Similar API では、ターゲットの顔と候補となる一連の顔を比較して、ターゲットの顔によく似ている一連の顔を見つけます。 matchPerson と matchFace の 2 つの動作モードがサポートされています。 matchPerson モードでは、[Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) を使用して同一人物についてフィルター処理が行われた後、似た顔が返されます。 matchFace モードでは、同一人物フィルターは無視されます。 同一人物のものであるかどうかに関係なく、似ている顔の候補のリストが返されます。

ターゲットの顔の例を次に示します。

![笑顔の女性](./Images/FaceFindSimilar.QueryFace.jpg)

そして候補となる顔は次のとおりです。

![笑っている人々の 5 つの画像。 画像 a と画像 b は同一人物を示しています。](./Images/FaceFindSimilar.Candidates.jpg)

4 つの似た顔を検索する場合、matchPerson モードではターゲットの顔と同じ人を表す a と b が返されます。 matchFace モードでは、ターゲットと同一人物でなくても、また類似性が低くても厳密に 4 つの候補が返されるので、a、b、c、d が返されます。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) リファレンス ドキュメントを参照してください。

## <a name="face-grouping"></a>顔のグループ化

Group API では、未知の顔の集合が、類似性に基づいて複数のグループに分けられます。 それぞれのグループは、元の顔の集合から得られる、互いに素な真部分集合です。 グループ内のすべての顔は、同じ人物に属する可能性があります。 1 人の人物について、いくつかの異なるグループが存在する可能性があります。 グループは、たとえば表情などの別の要因によって区別されます。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) リファレンス ドキュメントを参照してください。

## <a name="person-identification"></a>人物の特定

Identify API を使用して、人のデータベースと照らして、検出された顔を識別します。 この機能は、写真管理ソフトウェアにおける画像の自動タグ付けに役立つ可能性があります。 データベースは事前に作成しておき、後で編集できます。

次の画像は、`"myfriends"` という名前のデータベースの例を示しています。 各グループは、最大で 100 万個の異なる person オブジェクトを含むことができます。 各 person オブジェクトには最大で 248 個の顔を登録できます。

![それぞれ異なる人物に対応する 3 つの列とそれぞれ 3 行の顔の画像から成るグリッド](./Images/person.group.clare.jpg)

データベースを作成してトレーニングした後、新しく検出された顔を含むグループに対して識別を実行できます。 顔がグループ内の person として識別された場合、その person オブジェクトが返されます。

人物の特定の詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) リファレンス ドキュメントを参照してください。

## <a name="use-containers"></a>コンテナーの使用

標準化された Docker コンテナーをデータの近くにインストールすることによって、[Face コンテナーを使用](face-how-to-install-containers.md)して顔を検出、認識、識別します。

## <a name="sample-apps"></a>サンプル アプリ

以下のサンプル アプリケーションは、Face API のいくつかの使用方法を示しています。

- [Microsoft Face API: Windows クライアント ライブラリとサンプル](https://github.com/Microsoft/Cognitive-Face-Windows) は、顔の検出、分析、識別についていくつかのシナリオを実証する WPF アプリです。
- [FamilyNotes UWP アプリ](https://github.com/Microsoft/Windows-appsample-familynotes)は、家族でノートを共有するシナリオにおいて、音声、Cortana、インク、カメラと共に顔の識別が使用されるユニバーサル Windows プラットフォーム (UWP) アプリです。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services リソース全般に言えることですが、Face サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

クイック スタートに従って、顔検出のシナリオをコードで実装します。

- [クイック スタート:.NET SDK と C# を使用して画像内の顔を検出する](quickstarts/csharp.md)。 その他の言語が使用可能です。
