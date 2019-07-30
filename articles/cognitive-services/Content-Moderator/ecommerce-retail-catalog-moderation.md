---
title: チュートリアル:eコマース製品画像をモデレートする - Content Moderator
titlesuffix: Azure Cognitive Services
description: アプリケーションを設定して、指定されたラベルで製品画像を分析および分類します (Azure Computer Vision と Custom Vision を使用)。 さらに確認されるように好ましくない画像にタグを付けます (Azure Content Moderator を使用)。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: ec17f9f0206ef639bd47d694880c064a012ea1cf
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604187"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>チュートリアル:Azure Content Moderator を使用して eコマース製品画像をモデレートする

このチュートリアルでは、Content Moderator などの Azure Cognitive Services を使用し、eコマース シナリオ用の製品画像を分類してモデレートする方法について学習します。 Computer Vision と Custom Vision を使用してタグ (ラベル) を画像に適用したうえで、チーム レビューを作成します。これにより、Content Moderator の機械学習に基づくテクノロジが人間のレビュー チームと組み合わされ、インテリジェントなモデレーション システムが実現します。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Content Moderator にサインアップして、レビュー チームを作成します。
> * Content Moderator の Image API を使用して、成人向けとわいせつ向けの可能性があるコンテンツをスキャンします。
> * Computer Vision サービスを使用して、著名人のコンテンツ (または、Computer Vision で検出可能なその他のタグ) をスキャンします。
> * Custom Vision サービスを使用して、旗、おもちゃ、ペン (またはその他のカスタム タグ) の存在をスキャンします。
> * 人間によるレビューと最終的な意思決定のために、組み合わされたスキャン結果を表示します。

完全なサンプル コードは、GitHub 上の [samples-eCommerceCatalogModeration](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) リポジトリで入手できます。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- Content Moderator のサブスクリプション キー。 [Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Content Moderator サービスをサブスクライブし、お客様のキーを取得してください。
- Computer Vision のサブスクリプション キー (上記と同じ手順)。
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
- Custom Vision 分類器によって使用される各ラベル用の画像のセット (ここでは、おもちゃ、ペン、米国旗)。

## <a name="create-a-review-team"></a>レビュー チームを作成する

[Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)にサインアップしてレビュー チームを作成する方法の手順については、「[Web 上で Content Moderator を試す](quick-start.md)」クイック スタートを参照してください。 **[資格情報]** ページの **[チーム ID]** を書き留めておきます。

## <a name="create-custom-moderation-tags"></a>カスタム モデレーション タグの作成

次に、レビュー ツールのカスタム タグを作成します (このプロセスでヘルプが必要な場合は、[タグ](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags)に関する記事を参照してください)。 ここでは、**著名人**、**米国**、**旗**、**おもちゃ**、**ペン**の各タグを追加します。 すべてのタグが Computer Vision で検出可能なカテゴリである必要はありません (例: **著名人**)。後で検出するために Custom Vision 分類器をトレーニングするのであれば、お客様独自のカスタム タグを追加することができます。

![カスタム タグを構成する](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio プロジェクトの作成

1. Visual Studio で、[新しいプロジェクト] ダイアログを開きます。 **[インストール済み]** 、 **[Visual C#]** の順に展開し、 **[コンソール アプリ (.NET Framework)]** を選択します。
1. アプリケーションに「**EcommerceModeration**」という名前を付けて、 **[OK]** をクリックします。
1. このプロジェクトを既存のソリューションに追加する場合、このプロジェクトをシングル スタートアップ プロジェクトとして選択します。

このチュートリアルでは、プロジェクトの中心となるコードが強調されていますが、コードのすべての行が含まれているわけではありません。 サンプル プロジェクト ([samples-eCommerceCatalogModeration](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) から _Program.cs_ のすべての内容をお客様の新しいプロジェクトの _Program.cs_ ファイルにコピーします。 次に、以下のセクションの手順に従って、プロジェクトのしくみとそれを自分で使用する方法について学習してください。

## <a name="define-api-keys-and-endpoints"></a>API キーとエンドポイントの定義

このチュートリアルでは 3 つのコグニティブ サービスを使用します。これには、対応する 3 つのキーと API エンドポイントが必要です。 **Program** クラスの次のフィールドを見てください。

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

`___Key` フィールドは、お客様のサブスクリプション キーの値で更新する必要があります (後で `CustomVisionKey` を取得します)。また、`___Uri` フィールドは、適切なリージョン識別子を含むように変更することが必要な場合があります。 `ReviewUri` フィールドの `YOURTEAMID` 部分には、お客様が先ほど作成したレビュー チームの ID を入力してください。 `CustomVisionUri` フィールドの最後の部分は後で入力します。

## <a name="primary-method-calls"></a>主要なメソッドの呼び出し

**Main** メソッドの次のコードを見てください。これにより、画像 URL の一覧がループ処理されます。 3 つの異なるサービスによって各画像が分析され、適用されたタグが **ReviewTags** 配列に記録されてから、Content Moderator レビュー ツールに画像が送信されることで、人間のモデレーター用のレビューが作成されます。 以下のセクションでは、これらのメソッドについて詳しく見ていきます。 必要であれば、どのタグが適用されたかを確認するための条件付きステートメントで **ReviewTags** 配列を使用して、レビュー用に送信される画像を制御することができます。

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy メソッド

**Program** クラスの **EvaluateAdultRacy** メソッドを見てください。 このメソッドでは、画像 URL、およびキーと値のペアの配列がパラメーターとして受け取られます。 これによって Content Moderator の Image API が呼び出され (REST を使用)、画像の Adult と Racy のスコアが取得されます。 いずれかのスコアが 0.4 を超える場合 (範囲は 0 から 1 の間)、**ReviewTags** 配列の対応する値が **True** に設定されます。

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags メソッド

次のメソッドでは、画像 URL とお客様の Computer Vision サブスクリプション情報が受け取られ、画像に著名人が存在するかどうかが分析されます。 1 人または複数の著名人が見つかった場合、**ReviewTags** 配列の対応する値が **True** に設定されます。

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags メソッド

次に、**EvaluateCustomVisionTags** メソッドを見てください。これにより、実際の製品 (ここでは、旗、おもちゃ、ペン) が分類されます。 独自のカスタムの画像分類器を構築し、旗、おもちゃ、ペン (またはお客様が自分のカスタム タグとして選択した任意のもの) を検出するには、[分類器の構築方法](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)に関するガイドの手順に従います。 この例のいくつかのカテゴリをすばやくトレーニングするために、[GitHub リポジトリ](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)の **sample-images** フォルダーにある画像を使用できます。

![ペン、おもちゃ、旗のトレーニング画像が含まれた Custom Vision の Web ページ](images/tutorial-ecommerce-custom-vision.PNG)

ご自分の分類器をトレーニングしたら、予測キーと予測エンドポイント URL を取得し (これらの取得にヘルプが必要な場合は、「[URL と予測キーを取得する](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key)」を参照してください)、それらの値をお客様の `CustomVisionKey` と `CustomVisionUri` フィールドにそれぞれ割り当てます。 メソッドによってこれらの値が使用され、分類器に対してクエリが実行されます。 分類器によって画像内にカスタム タグが 1 つまたは複数見つかった場合、このメソッドによって、**ReviewTags** 配列の対応する値が **True** に設定されます。

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>レビュー ツール用のレビューの作成

前のセクションでは、アプリでどのように受信画像の成人向けできわどいコンテンツ (Content Moderator)、著名人 (Computer Vision)、さまざまな他のオブジェクト (Custom Vision) をスキャンするかについて調べました。 次は、**CreateReview** メソッドを確認してください。これによって、適用されたすべてのタグ (_Metadata_ として渡されます) と共に画像が Content Moderator レビュー ツールにアップロードされます。

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

[Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)の [レビュー] タブに画像が表示されます。

![複数の画像と強調表示されたそれらのタグが含まれた Content Moderator レビュー ツールのスクリーンショット](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>テスト画像の一覧の送信

**Main** メソッドを見るとわかるように、このプログラムでは、画像 URL の一覧が記載されている _Urls.txt_ ファイルが含まれた "C:Test" ディレクトリが検索されます。 このファイルとディレクトリを作成するか、ご利用のテキスト ファイルを指すパスに変更します。 次に、テストする画像の URL をこのファイルに設定します。

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>プログラムの実行

上記の手順をすべて完了したら、プログラムによって (関連するタグについて 3 つのサービスすべてに対してクエリが実行され) 各画像が処理され、タグ情報と共に画像が Content Moderator レビュー ツールにアップロードされます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、製品画像を分析し、製品の種類別にタグを付け、レビュー チームがコンテンツ モデレーションに関して情報に基づいた決定を行えるようにするプログラムを設定しました。 次は、画像のモデレーションの詳細について学習してください。

> [!div class="nextstepaction"]
> [モデレートされた画像のレビュー](./review-tool-user-guide/review-moderated-images.md)
